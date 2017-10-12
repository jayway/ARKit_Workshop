#ARKit Workshop @VRConf 6 november 2017

###ARKit "Hello World" with Apple SceneKit Framework in Swift for Xcode 9


**Documentation**

Apple ARKit Documentation:  [https://developer.apple.com/documentation/arkit](https://developer.apple.com/documentation/arkit)

Apple ARKit Resources:
[https://developer.apple.com/arkit/](https://developer.apple.com/arkit/)

1. **Create a new Xcode project**

    * Use the Augmented Reality template.

    * Choose swift as language and SceneKit as Content Technology.

2. **Run the app on your device.**

   * Check that you can see a spaceship floating around in the room through your device and that you see the AR-effect when you move yourself and the device around.

   * You have now everything set up properly including using a compatible device.

3. **Add a 3D-model**

   * Look for a model that is of the format .dae and consists of less than 10k polys (millions of polys will make the processor work harder than it can handle).

   * Link to free models filtered according to above criteria (sign up for a free account):
   https://www.turbosquid.com/Search/3D-Models?keyword=&search_type=free&media_typeid=2&file_type=194&min_poly=0k&max_poly=10k

   * Add your downloaded .dae-file in to the Xcode-folder art.scnassets. Notice the coordinate system and how it's oriented.

   * Preview your model by clicking on the file inside Xcode to make sure it's not corrupted.

   * Load your model into the scene by replacing the spaceship-filename with your model-filename where the scene is created in the ViewController.

4. **Move start position of the model**

   * Default position of both model and camera is (0,0,0). So you might not see anything at start until you move the camera/device out from inside the model. Or you could move the model to be 1 m in front of the camera at start.

   * "shipMesh" is the identity of the spaceship model shown in the Node inspector, put your own name for your model.

   ``` swift
   let treeNode = scene.rootNode.childNode(withName: "shipMesh", recursively: true)
   treeNode?.position = SCNVector3Make(0, 0, -1)
   ```

5. **Find the right scale for the model**

  * Iterative: Run the app and tweak the scale of your model to fit it into the real world around you seen through your device. Tips! The bounding box will tell you the size of your model in meters.

6. **Keep reference to treeNode**

  * Make the treeNode into a class member thus accessible outside any function and change the referencing accordingly.

  ``` swift
    class ViewController: UIViewController, ARSCNViewDelegate {

      var treeNode: SCNNode?
      ...
    }
  ```

7. **Detect and visualise horizontal planes in the real world**

  * Turn on debug options to be able to see what Feature Points ARKit finds. Also add the World Origin to the debug options to see the axis (x, y, z) of the coordinate system in the augmented world.

  ``` swift
    sceneView.debugOptions = [ARSCNDebugOptions.showFeaturePoints, ARSCNDebugOptions.showWorldOrigin]
  ```

   * Make sure your scene is delegate to protocol ARSCNViewDelegate for ViewController

   ``` swift
   class ViewController: UIViewController, ARSCNViewDelegate {
     override func viewDidLoad() {
        self.sceneView.delegate = self
   ```

   * Set the ARKit plane detection to horizontal where you set up the configuration of the session
   ``` swift
    configuration.planeDetection = .horizontal
  ```

   * Create a class member reference planes which is a dictionary storing key value pairs of SceneKit's PlaneAnchor identifiers (uuidString) and it's nodes. Place it alongside where you placed the treeNode reference. This will hold the reference to all the planes ARKit finds.

   ``` swift
    var planes: [String: SCNNode] = [:]
   ```

   * Create an extension to ViewController and handle session updates for the renderer function of nodes inside of didAdd, didUpdate and didRemove.

   * When adding a node in didAdd, create a geometry matching the plane's extent on the X and Z axes and keep a reference to the new node in a dictionary for later updates.

   * In the didUpate, ARKit has registrered more anchor points to an existing plane. In the didRemove, ARKit sees that two different planes actually is one and merges them.

   * Run the app to see the ARKit plane detection work in action. Note! Give your device and ARKit a hand by moving it forward/backward towards a well lit flat surface preferably with some contrasting details. This might take some seconds.

   ``` swift
   extension ViewController {

     func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
         // This visualization covers only detected planes.
         guard let planeAnchor = anchor as? ARPlaneAnchor else { return }

         // Create a SceneKit plane to visualize the node using its position and extent.
         let plane = SCNPlane(width: CGFloat(planeAnchor.extent.x), height: CGFloat(planeAnchor.extent.z))
         let planeNode = SCNNode(geometry: plane)
         planeNode.position = SCNVector3Make(planeAnchor.center.x, 0, planeAnchor.center.z)

         // SCNPlanes are vertically oriented in their local coordinate space.
         // Rotate it to match the horizontal orientation of the ARPlaneAnchor.
         planeNode.transform = SCNMatrix4MakeRotation(-Float.pi / 2, 1, 0, 0)

         // Set a semi transparent (blue) color on the plane so it will be visible to the eye.
         let color = SCNMaterial()
         color.diffuse.contents = UIColor(red: 0, green: 0, blue: 1, alpha: 0.4)
         plane.materials = [color]

         // ARKit owns the node corresponding to the anchor, so make the plane a child node.
         node.addChildNode(planeNode)

         // keep node for reference later to make updates
         let key = planeAnchor.identifier.uuidString
         self.planes[key] = planeNode
     }

     func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
         guard let planeAnchor = anchor as? ARPlaneAnchor else { return }

         let key = planeAnchor.identifier.uuidString
         if let existingPlane = self.planes[key] {
             if let geo = existingPlane.geometry as? SCNPlane {
                 geo.width = CGFloat(planeAnchor.extent.x)
                 geo.height = CGFloat(planeAnchor.extent.z)
             }
             existingPlane.position = SCNVector3Make(planeAnchor.center.x, -0.005, planeAnchor.center.z)
         }
     }

     func renderer(_ renderer: SCNSceneRenderer, didRemove node: SCNNode, for anchor: ARAnchor) {
         guard let planeAnchor = anchor as? ARPlaneAnchor else { return }

         let key = planeAnchor.identifier.uuidString
         if let existingPlane = self.planes[key] {
             existingPlane.removeFromParentNode()
             self.planes.removeValue(forKey: key)
         }
     }
   }
   ```

8. **Implement an override for touchesBegan and move the model when touching the device screen.**

   * Take the touch point of the user from the device and apply hitTest on that point. hitTest on an ARScene gives a matrix with anchors that ARKit is tracking and has identified as a surface of a real world object.

   * Position the model to the anchor found through hitTest. hitTest results in a 4x4 matrix containing the scale, rotation, and position data. Set the position to a new vector object from the 4th row of the matrix (it's first row and column start with 0).

   * Use HitTest to determine which anchor points in the real world matches the touch point on the screen.

   * Play with different types of intersection algorithms for finding anchor points: featurePoint, estimatedHorizontalPlane, existingPlane, existingPlaneUsingExtent. Several can be used by adding more to the array of types.

   ``` swift
   override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        guard let touch = touches.first else { return }
        var results = sceneView.hitTest(touch.location(in: sceneView), types: [.existingPlaneUsingExtent])
        //[.featurePoint, .estimatedHorizontalPlane, .existingPlane, .existingPlaneUsingExtent])

        if let match = results.first {
            let t = match.worldTransform
            let hitPosition  = SCNVector3(x: t.columns.3.x, y: t.columns.3.y, z: t.columns.3.z)
            treeNode?.position = hitPosition
        }
    }
   ```

9. **If you want to copy the model instead of moving it at device touch point**

   _Play around for a better understanding! Skip this step if time is running out._

   * At the end of the override function touchesBegan, create a new instance of the model from the touch point by adding a reference to the clone. Set the position and add it to the scene's node tree.

   * Don't forget to remove the one line of code from previous step which was moving the model to a new location.

   * Run the app and place the model on different locations of the room. Notice that you can walk away from one point and the model will still be there when you return!

   ``` swift

   //treeNode?.position = hitPosition

   let modelClone = treeNode!.clone()
   modelClone.position = hitPosition
   sceneView.scene.rootNode.addChildNode(modelClone)
   ```
