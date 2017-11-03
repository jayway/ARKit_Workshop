# ARKit Workshop @VRConf 6 november 2017

### ARKit ARKit "Hello World" with Unity Game Engine

   **Documentation**

  Unity Documentation:  [https://docs.unity3d.com/Manual/index.html](https://docs.unity3d.com/Manual/index.html)

  Unity Scripting API:
  [https://docs.unity3d.com/ScriptReference/index.html](https://docs.unity3d.com/ScriptReference/index.html)

   Inside the Unity ARKit plugin package there are two very helpful files: README and TUTORIAL

1. **Start up a new Unity project**

    * Create your project and choose "3D" and disable "Analytics" (would have put your project in the cloud).

    * Download the "Unity ARKit Plugin" released by Apple from the Unity Asset Store (3rd tab on the main window beside the tabs "Scene" and "Game"). Create a free Unity ID account and import the full package to your project. It will show up under "Assets" in the "Project Panel".

    Tip! If the Asset Store doesn't load properly, you can reload by right-clicking on the tab.

    * Import the full package that comes with the plugin. The files can be found under "Assets" in the "Project Panel".

2. **Setup a scene**

    * Open the ARKit-plugin Demo Scene by double-clicking on the "UnityARKitScene" from the catalogue "Examples".

    * In the tab "Scene" on the main window, notice the Game Objects:

      - Directional light - adding light source to the scene
      - RandomCube - the 3D model used in this Demo Scene
      - CameraParent - representing your device font facing camera
      - HitCubeParent - holds script to receive touch points by user on device screen
      - GeneratePlanes - holds script visualising ARKit plane detection
      - PointCloudParticleExample - holds script visualising ARKit Feature Points

3. **Compile and build the example project**

    * Open up the "Build Settings" from the "File"-menu.

    * Make sure the checkbox for the scene "UnityARKitScene" is selected.

    * Select the "iOS platform" and don't forget to press the "Switch Platform"-button to enable your choice.

    * Change the "Run in Xcode as" to "Debug" and mark the checkbox for "Development Build".

    * Some needed XCode settings can be modified by pressing the "Player Settings"-button that will open up int the "Inspector Panel". Scroll down and fill in:
      - "Build Identifier"
      - Set the "Target minimum iOS Version" to "11.0"
      - Set your "Target Device"
      - Ask the user for camera usage permission in the "Camera Usage Description" (standard Apple requirement)

      - Tip! If you also want to pre-fill the "Automatic Signing Team ID"-field you can find your Apple Developer Account Team ID String  (under the Membership page) here

      [https://developer.apple.com/account/#/membership](https://developer.apple.com/account/#/membership)

    * Press the "Build"-button.

    * Pick a destination on your drive to save the Xcode-project that will be outputted from Unity.

      Tip! When prompted to save, give the name "xcode" or similar to make it easy for you to find the folder when needed.

      Tip ahead! When making a new build next time by overriding the old one, choose "Replace" if "Append" does not update as expected. You would then also need to close down the open project in Xcode.

    * When the build process has completed, a "Finder"-window will show up. Open up your project named "Unity-iPhone.xcodeproj"

4. **Deploy to your iOS-device**

    * In the "General Settings" for the Xcode-project, choose your Apple Developer Account for the "Team" which enables you to run the app on your device.

    Tip! This step can be avoided if you pre-filled your Team ID according to a tip in one of the previous step when filling out "Player Settings" in Unity before the build.

    * ARKit is not supported by the Xcode Simulator. Select your personal device to run from and then hit the "Run"-button.

    * You now see the "RandomCube" in front of you. When you touch the device screen, a grey cube will be placed on the scene since the "UnityARHitTestExample"-script is attached to the "HitCube"-GameObject, a child of the "HitCubeParent". Touch a new place and the cube will move to the new place. You can also drag the cube by holding it while dragging.

5. **Add a 3D model to the scene**

    * Download the "Low Poly Dancing Rabbit" 3D animated model made by wHiteRabbiT sTudio for free from the Unity Asset Store and import the full package to your project.

    * Find the animation file ending with .fbx in the "Project Panel" somewhere deep under the "Model"-folder and drag it into the scene. The Prefab of the model is baked into the animation file.

    * Place the model in a hierarchy under the "HitCubeParent" by dragging it to the "Hierarchy Panel".

    * Position the model where the "RandomCube" is on the scene. We are going to replace this GameObject. Use either the "Position-field" in the "Inspector Panel" or drag the x-y-z-axis centered on the model by moving it in one direction at a time.

      Tip! The small "2D"-button on the top of the "Scene"-window toggles the scene between 2D and 3D. Also experiment with pressing the perspective arrows on the top right of the "Scene"-window.

    * Goto the "Inspector Panel" and set an appropriate scale. Scale is defined in meters.

6. **Set the model to face the camera**

    * You can see from the "Game"-tab next to your "Scene"-tab how it will look when starting up the app. Same thing can be achieved by pressing the "Run"-button on top of the screen. If adjustment is needed, go ahead and change the position and rotation of the model.

7. **Remove the Game Objects not needed**

    * Right-click and remove following objects from the "Hierarchy Panel"

      - RandomCube
      - PointClourdExample
      - AR3DOFCameraManager

    * Build and run the app to make sure that you successfully added the model.

    Tip! Before you build, you need to save the project for your changes to have effect.

8. **Move the 3D model when performing ARKit HitTest"**

    * Move the position of the "HitCubeParent"-GameObject to align with the model. And also set the same rotation as the model.

    * With your 3D model selected in the "Hierarchy Panel", press the "Add Component"-button at the bottom of the "Inspector Panel". Find the "UnityARHitTestExample"-script and double click to populate the model's "Script"-field.

    * For the "Hit Transform"-field, right under the "Script"-field, open up the cog-wheel next to the field and from the popup, go to the "Scene"-tab. Find the "HitCubeParent" and populate the field by double-clicking it. This step is connecting the plugin ARKit HitTest functionality with a reference to the 3D-model on the scene.

    * Make the "HitCube" unavailable in the scene by unmarking the checkbox for this GameObject in it's "Inspector Panel". Or remove it by deleting it.

    * Build and run the app to see that you successfully can move your model around when touching your device screen.

9. **Set the ARKit Plane avoiding model hanging in midair**

    * Find the "UnityARHitTestExample"-script from the "Project Panel". It's under "Helpers" inside the iOS Plugin folder structure.

    * Double click the script to bring it up in MonoDevelop, the script editor bundled with Unity.

    * In the Update() function comment out every resultTypes exept the first one: ARHitTestResultTypeExistingPlaneUsingExtent.

    ```CS
    ARHitTestResultType[] resultTypes = {
      ARHitTestResultType.ARHitTestResultTypeExistingPlaneUsingExtent,
      // if you want to use infinite planes use this:
      //ARHitTestResultType.ARHitTestResultTypeExistingPlane,
      //ARHitTestResultType.ARHitTestResultTypeHorizontalPlane,
      //ARHitTestResultType.ARHitTestResultTypeFeaturePoint
    };
    ```

    * Save, build and run again to look at the result.

10. **Create a script to control the models movements**

    * With your 3D model selected in the "Hierarchy Panel", add another component from the "Inspector Panel". Type "RabbitController" and click to create as a new C# script.

    * Open your script by double clicking it from the newly added "Script"-field at the bottom of the "Inspector Panel".

    * Keep a reference to the animation and set a bool variable stating if the animation should move at the start of the app. Also set it to false. Also create a public transform variable that will be assigned to HitCubeParent in a coming step.

    ```CS
    public class RabbitController : MonoBehaviour {

  	public Transform m_ModelTransform;
  	private Animation animation;
  	private bool shouldMove = false;

  	// Use this for initialization
  	void Start () {
  		animation = GetComponent<Animation> ();
  	}
    ```

    * Goto the "Inspector Panel" of the model and uncheck the checkbox for "Play Automatically" under the "Animation"-settings.

    * The public transform variable you just created now shows up in the "Inspector Panel" for the model. Set the "ModelTransform"-field under the "RabbitControllerScript"-settings to "HitCubeParent" by dragging it from the "Hierarchy Panel" into the field in the "Inspector Panel".

    * Go back to MonoDevelop. Add a function Move() to play and stop the dancing animation.

    ```CS
    public void Move(){

  		if (!animation.isPlaying) {

  			animation.Play ();
  			shouldMove = true;
  		} else {
  			animation.Stop ();
  			shouldMove = false;
  		}
  	}
    ```

    * Add code for the model to walk forward (besides animating movements of dancing) in the Update()-function. In the class Transform for a matrix, there is a Translate that will move the object in the local coordinate frame. We want our model to move forward in its own coordinate system, eg since it's rotated 180 in relation to the global coordinate system, so set an inverse on the forward-vector.

    ```CS
    // Update is called once per frame
  	void Update () {

  		if (shouldMove) {

  			Vector3 localForward = m_ModelTransform.InverseTransformDirection(m_ModelTransform.forward);

  			m_ModelTransform.Translate (localForward * Time.deltaTime * .03f);
  		}
  	}
    ```

    * Since your script is linked to move the "HitCubeParent", the rotation for both the "Rabbit_LowPoly_RigUnity_Final" and the "HitCubeParent" needs to be set in the y-axis to 180 for the model to move correctly in the World coordinate system.

      Tip! You can check for the 180 rotation on the model by finding the model's prefab called "Rabbit_LowPoly" in the "Hierarchy Panel" and have a look on the rotation that is pre-defined. This is due to how the model was created in the 3D-modelling program in relation to it's own local coordinate system. This can be changed in a 3D-modelling program such as Blender, but is something outside the scoop of this workshop.

      Tip! Later when you run the app, experiment with the speed of the forward moving by changing the time factor inside the Translate-function. You might want a different speed depending on the size of your model and where you want to run it (eg. on a table, on the floor).

11. **Add to the script some other handy functionalities**

    * Make the model bigger by doubling in size.

    ```CS
    public void Bigger(){
  		m_ModelTransform.localScale = m_ModelTransform.localScale * 2;
  	}
    ```

    * Make the model smaller by half in size.

    ```CS
    public void Smaller(){
  		m_ModelTransform.localScale = m_ModelTransform.localScale * 0.5f;
  	}
    ```

    * Make the model face the camera.

    ```CS
    public void Face(){
      // LookAt sets rotation so the model faces the camera
      // The EulerAngles of the LookAt-matrix assume it's level and rotate around y-axis only
      m_ModelTransform.LookAt (Camera.main.transform.position);
      m_ModelTransform.eulerAngles = new Vector3 (0, m_ModelTransform.eulerAngles.y, 0);
    }
    ```

12. **Build a menu to the UI to link to the functionality controls**

    * Go back to the "Hierarchy Panel" and right click anywhere. Then choose to create a new UI "Button". This will automatically create a "Canvas"-GameObject and it's event system for you.

    * In the "Inspector Panel", under the "Canvas Scaler"-settings, set the "UI Scale Mode" to "Constant Physical Size".

    * For the button's childtext, set a text similar to "Move" under the "Text"-settings. Choose your font and size.

    * For the button itself, set the width to 40 and height to 20 to start with. Set the position to x:0 and y:15, also to start with. Choose "bottom, center" for alignment.

    * Make 3 copies of the button and set respectively text to something matching the functionalities from your script: "Smaller", "Bigger", "Face".

    * Use positioning to give all the buttons a place of it's own on the canvas. Use the "Game"-tab to visually follow what you are doing.

    * Each button now needs to be coupled with it's corresponding action controller. In the "Inspector Panel" for the "Button"-settings, find the "On Click()"-section, click the "+"-button and add the model in the field where it says "Object". Now in the field where it says "Function", choose your script and the function that you want to map to the button.

    * The button "Move" should change text to "Stop" when it's playing and vice verse. In the "Inspector Panel", set the reference name for the GameObject, found at the very top of the panel, to "moveButton". Then add the following code twice to your script inside Move() at the appropriate places, with the text set to either "Move" or "Stop".

    ```CS
    GameObject.Find("moveButton").GetComponentInChildren<UnityEngine.UI.Text>().text = "Stop";
    ```

    * Build, run and iterate till you are happy with the user interface and user interaction.

      Tip! The buttons are on a canvas overlay. This means that if you press a button and it happens to be inside a detected plane, ARKit HitTest will be activated and you will move the model to the place you touched the screen (even if you only intended to touch the button and not move the model). Fix this problem if time permitting. See lab instructions further down.

      Tip! Print syntax for debugging:

      ```CS
      print ("inside Move");
      ```

      * Well done!

 ### EXTRA LAB ASSIGNMENTS for the eager ones


1. **Ignore HitTest when user touches the menu buttons**

  * Open up the script "UnityARHitTestExample" and before setting the ARHitTestResultType, add something similar to the following code, depending on where you positioned your buttons on the canvas (the code below implies you added them at the bottom of the device screen):

  ```CS
  // Ignore part of UI covered by buttons
	if (touch.position.y < 150) {
		return;
	}
  ```

2. **Turn off ARKit debugOptions showing Plane Detection and Feature Points**

    * Select "GeneratePlanes"-GameObject in the "Hierarchy Panel". Unmark the checkbox to the top left corner in the "Inspector Panel".

    * Repeat the above for the "PointCloudParticleExample"-GameObject.

3. **Make a video of your ARKit Demo**

    * Add "Screen Recording" (new iOS 11 feature) by changing the "Customised Controls" in the "Control Centre" of your device. You find it inside "Settings" under "Control Centre"

4. **Use the Unity ARKit Remote for Development**

    * It's quite time consuming to make changes in Unity, compile and build and deploy it to an iOS-device. With the ARKit Remote app, one can send the ARKit-features from the device camera to the Unity Editor thus being able to see changes in Unity directly without having to build and deploy.

    * Check out this step-by-step instruction to set up ARKit Remote:

    [https://forum.unity.com/threads/arkit-support-for-ios-via-unity-arkit-plugin.474385/page-13#post-3153415](https://forum.unity.com/threads/arkit-support-for-ios-via-unity-arkit-plugin.474385/page-13#post-3153415)

5. **Import another animation model from the Asset Store and exchange it with the rabbit**

    * For the animation to work with the script you created, it needs to be an animation of the format "Legacy".

    * The tricky part will probably be to make the model move in the direction and sense you intend it to. It all depends on how the model was created and what animation settings came bundled with it. Experiment with setting the position and the rotation for the model and the "HitCubeParent". Also figure out from documentation and experimentation how to translate the m_HitTransform in the Update()-function in your script.
