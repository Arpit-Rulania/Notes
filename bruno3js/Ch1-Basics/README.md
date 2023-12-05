# ```CHAPTER 1 - Basics of Three JS```
## Creating a new node js project:
To create a new project we go into destination folder.
<br> * Run ```npm init-y```. _adds package.json_
<br> * Next we run ```npm install vite``` _This will install dependencies from package.json_
<br> * In package.json inside scripts tag add dev and build shortcuts for terminal: 
```json
"scripts": {
    "dev": "vite",
    "build": "vite build"
}
```
Now from the terminal we can run commands: ```npm run dev```
<br> ```npm install three```: This will istall 3js dependencies.

If stater files are given just run ```npm install``` to install dependencies.

When server starts and network url is not provided, run the following command:<br>
```npm run dev -- --host```

## Creating a scene in script.js:
First add a script tag to the body element in the html.
```html
<body>
    <canvas class="webgl"></canvas>
    <script type="module" src="./script.js"></script>
</body>
```
Next import three js into script.js: ```import * as THREE from 'three'```

To create a 3js scene we need 4 elements:<br>
    &nbsp;&nbsp;&nbsp;&nbsp; 1. Scene<br>
    &nbsp;&nbsp;&nbsp;&nbsp; 2. Objects<br>
    &nbsp;&nbsp;&nbsp;&nbsp; 3. Camera<br>
    &nbsp;&nbsp;&nbsp;&nbsp; 4. Renderer

### Create a scene
```js
const scene = new THREE.Scene()
```

### Create an object
We ened to create a mesh that is a combo of geometry(shape) and material (how it looks).
```js
const geometry = new THREE.BoxGeometry(1, 1, 1) //dimensions of the box
const material = new THREE.MeshBasicMaterial({color: 0xff0000}) 
// another property 'wireframe: true' will make the solid box into a wire frame.
const mesh = new THREE.Mesh(geometry, material) //Combine geometry and material
scene.add(mesh) // Finally add mesh to scene
```

### Create a camera
There are different types of cameras: We will start with perspective cameras.
<br>Parameters of a camera:<br>
* field of view: angle of vision verically.
* aspect ratio: width/heigth
```js
// Sizes
const sizes = {
    width: 800,
    height: 600
}
```
```js
const camera = new THREE.PerspectiveCamera(75, sizes.width/sizes.height) //Create camera
camera.position.z = 3 //Move it back so the box becomes visible
scene.add(camera) //Add it to the scene!
```

### Create the canvas
You can let the renderer make the canvas for you or you can do it  yourself.<br>
Add canvas element to the body element in the html.<br>
```html
<body>
    <canvas class="webgl"></canvas>
    <script type="module" src="./script.js"></script>
</body>
```

Now bring this canvas into script.js<br>
Add this line to the top of script.js
```js
const canvas = document.querySelector('canvas.webgl')
```

### Create the renderer
```js
const renderer = new THREE.WebGLRenderer({
    canvas: canvas
})
renderer.setSize(sizes.width, sizes.height) //Give the renderer a size.
renderer.render(scene,camera) //Finally add the scene and the camera to the renderer.
```
<br><br>
***

# ```CHAPTER 2 - Transforming Objects```
### 4 properties to transform objects in the scene
> 1. Position
> 2. Scale
> 3. rotation
> 4. quaternion

## Axes helper
- This will help you to visualize the axes orientation.
```js
const axesHelper = new THREE.AxesHelper(2) // 2 is the length of the axes lines
scene.add(axesHelper)
```

## Position
- Position is a property of the Vector3 class.<br>
- ___It is important to change position before calling render because render is like taking a photo you cant change the photo once it is taken.___
```js
// Individually change each position
mesh.position.x = 0.7
mesh.position,y = -0.6
mesh.position.z = 1
// Or do it in one go
mesh.position.set(0.7, -0.6, 1)
```
Some tools to console log position properties to debug:
```js
console.log(mesh.position.length()) //Get the length of the vector
console.log(mesh.position.distanceTo(camera.position)) //Get distance from another Vector3 
// use distanceTo camera after creating camera.
console.log(mesh.position.normalize()) //Reduce length of vector to 1.
```

## Scale
- Scale will help to resize your mesh(object). It is a Vector3 <br>
- Default scale in 3js is 1.
```js
mesh.scale.x = 2
mesh.scale.y = 0.25
mesh.scale.z = 0.5
// Or do it in one go
mesh.scale.set(2,0.25,0.5)
```

## Rotation
- Rotation is a Euler.<br> 
- Half a rotation is ```Math.Pi```. <br> 
- By default rotation occours in the order: ```x -> y -> z```. This can cause weird behaviour like gimbal lock when one axis has no more effect because of the previous one.
```js
mesh.rotation.reorder('YX') //When you want to change default order of rotation
mesh.rotation.x = Math.PI * 0.25
mesh.rotation.y = Math.PI * 0.25
```

## Quaternion
Quaternion property also expresses a rotation, but in a more mathematical way, which solves the order problem. This is why most engines and 3D softwares use it instesd of Euler rotation.

## LookAt
This Object3D instance property lets you ask an object to look at something.<br>
Example usage: move a cannon in a game or move characters eyes. <br>
parameter -> It is the target which is Vector3.
```js
//Example 1
camera.lookAt(new THREE.Vector3(0,-1,0))
//Example 2
camera.lookAt(mesh.position)
```
<br>

### ___You can use the 3 properties postion, rotation/quaternion, and scale in any order.___

<br>

## Creating Groups (Scene Graph)
Say you are building a house with walls, doors, windows, roof, bushes etc. <br>
Now you need to rescale each object for sizing.<br>
So it is much easier to toss all of these object into a group and use one tranformation that will apply to the whole group.
1. Instantiate a group and add it to the scene 
```js
const group = new THREE.Group()
scene.add(group)
```
2. Create an object
```js
const cube1 = new THREE.Mesh(
    new THREE.BoxGeometry(1,1,1),
    new THREE.MeshBasicMaterial({ color: 0xff0000 })
)
```
3. Add it to the group ***not the scene*** using 
```js
group.add(cube1)
```

## Complete all in one code block till now.

```js
import * as THREE from 'three'

// Canvas
const canvas = document.querySelector('canvas.webgl')

// Scene
const scene = new THREE.Scene()

// Axes helper
const axesHelper = new THREE.AxesHelper()
scene.add(axesHelper)

//Groups - multiple objects/meshes in one group and can be transformed togther.
const group = new THREE.Group() //Create new group
group.position.y = 1
group.scale.y = 2
group.rotation.y = Math.PI
scene.add(group)

// Create 3 Cubes

const cube1 = new THREE.Mesh(
    new THREE.BoxGeometry(1,1,1),
    new THREE.MeshBasicMaterial({ color: 0xff0000 })
)
group.add(cube1)

const cube2 = new THREE.Mesh(
    new THREE.BoxGeometry(1,1,1),
    new THREE.MeshBasicMaterial({ color: 0x00ff00 })
)
group.add(cube2)
cube2.position.x = -2 // You can change individual cube position also if you want to.

const cube3 = new THREE.Mesh(
    new THREE.BoxGeometry(1,1,1),
    new THREE.MeshBasicMaterial({ color: 0x0000ff })
)
group.add(cube3)
cube3.position.x = 2

// Sizes
const sizes = {
    width: 800,
    height: 600
}

// Camera
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height)
camera.position.set(0,0,3)
scene.add(camera)

//camera.lookAt(new THREE.Vector3(3,0,0))
//camera.lookAt(mesh.position)

// Renderer
const renderer = new THREE.WebGLRenderer({
    canvas: canvas
})
renderer.setSize(sizes.width, sizes.height)
renderer.render(scene, camera)
```
<br><br>
***

# ```CHAPTER 3 - Animations```

## Request Animation Frame
When we add request animation frame into a function then it will call that function again in the next frame endlessly.
```js
const tick = () =>
{
    console.log('tick')
    window.requestAnimationFrame(tick)
    // This will print tick to the console forever.
    // Tick will be printed once every frame. So 60fps means 60 ticks.
}
tick()
```

Now we can move ```renderer.render(scene,camera)``` into tick function to get called at every frame and re-render continiously. 
```js
const tick = () =>
{
    // Update objects
    mesh.position.x += 0.001
    mesh.rotation.x += 0.01
    mesh.rotation.y += 0.01
    // Renderer
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}
tick()
```

***Major problem is that if the framerate(fps) is higher than the animation will be faster <br> Lets try to fix this problem.***

1. Get the time outside tick function. ```let time = Date.now()```
2. Get current time inside tick function. ```const currentTime = Date.now()```
3. Get the difference
4. Use difference to update transformation. ```mesh.rotation.y += 0.001 * deltaTime```
5. Update ```time = currentTime```

**Three JS has a built in solution called Clock**

```js
const clock = new THREE.Clock() //instantiate 3js Clock
const tick = () =>
{
    // Time 
    const elapsedTime = clock.getElapsedTime()
    // Update objects
    mesh.rotation.y = elapsedTime * Math.PI * 2 //One full revolution/second.
    mesh.position.set(Math.sin(elapsedTime),0,0) //Moves cube along x axis cause of sin oscilation.
    // Renderer
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}
tick()
```

## GSAP - GreenSock
GSAP is one of the many libraries for doing animations in specific ways. <br>
Greensock has its own tick. Internally the library is doing request animation frame on its own so you don't have to. But you still need to render the results yourself.

```js 
// --save tag will save dependencies to package.json so someone else can work on the correct version.
npm install --save gsap@3.12
//  Here we specify version to match with what Bruno is teaching.
// But for personal projects
npm install --save gsap
```

Import GreenSock library to the top of script.js 
```js
import gsap as 'gsap'
```

Example usage of GSAP:
```js
gsap.to(mesh.position, {duration: 1, delay: 1, x: 2}) //Sends cube to x = 2.
gsap.to(mesh.position, {duration: 1, delay: 2, x: 0}) //Brings back cube from 2 to 0.
const tick = () =>
{
    // Renderer
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}
tick()
```
<br><br>
***

# ```CHAPTER 4 - Cameras```
- Array Camera: Renders scene from multiple cameras on specific areas of the render. eg. multiplayer game with split screen.
- Stereo camera: Uses 2 cameras to mimic eyes. eg. used with vr etc.
- Cube camera: Does 6 renders for each direction. eg. environment map
- Orthographic camera: Renders without perspective. eg. Something far looks the same size as something near.

## Perspective Camera
### Field of view
Ideally this angle is kept between 35-75 degrees to prevent distortion. When the angle is too large, then distortion occours because everything is being squeezed into the render frame.
### Aspect Ration
width of render/height of render. Create const sizes for ease of use.
### Near and Far
Last 2 parameters that are a bound for how much of the cameras vision is being rendered from point 1 (near) to point 2 (far) along cameras axis of view.  
***z-fighting***: This is when the two faces near and far are too close to each other so the gpu cant tell which face is actually closer and accidentally inverts the object causing a glitch. Do NOT use extreme near/far values to resolve z-fighting.
```js
const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 100)
```

## Orthographic Camera
Instead of field of view, we provide how far the camera can see in each direction.  
Parameters: (left, right, top, bottom, near, far)
```js
const camera = new THREE.OrthographicCamera(- 1, 1, 1, - 1, 0.1, 100)
```
Now when we change the size of the renderer, the cube will also stretch or compress and will not look like a cube anymore.  
The values we provide to left, right, top, and bottom are 1, -1 which means we render a square area. But that square area is stretched out to fit the renderer canvas which might not be square.
The solution to this is to calculate aspect ratio.
```js
const aspectRatio = sizes.width / sizes.height
const camera = new THREE.OrthographicCamera(- 1 * aspectRatio, 1 * aspectRatio, 1, - 1, 0.1, 100)
//Now the camera render area has a [width > height] because the [canvas width > canvas height] (w:800, h:600)
```

## Custom Controls
Lets create an event listener at the start of the file to track cursor movement.
```js
window.addEventListener('mousemove', (event) => 
{
    console.log(event.clientX) 
    console.log(event.clientY) // (0,0) is top left corner
})
```
Now this is responsive, so if your screen is bigger than clientX will be larger.  
So we want to get cursor values between 0 and 1. Or anything else like -0.5 -> 0.5
```js
// Create cursor object to stay organised.
const cursor = {
    x: 0, 
    y: 0
}
window.addEventListener('mousemove', (event) => 
{
    cursor.x = event.clientX/sizes.width - 0.5 // Dividing by width brings range between 0 -> 1
    cursor.y = event.clientY/sizes.height - 0.5 // Subtracting 0.5 will move range back to -0.5 -> 0.5
})
```

Now inside the tick function we want to update camera position to follow cursor at each frame:
```js
const tick = () =>
{
    // Update camera
    camera.position.x = cursor.x * 3 // times 3 makes camera move 3 times faster than the cursor
    camera.position.y = cursor.y * 3
    // Render
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}
```

Now if we tell the camera to look at the cube then the cube will stay centered and the camera will move around it. 
```js
// Update camera
camera.position.x = cursor.x * 3
camera.position.y = -cursor.y * 3
camera.lookAt(mesh.position)
```

But we can still not see the back of the cube. We can do a full rotation around the mesh with a combination of sin and cos.  
sin and cos combined with the same angle lets us put things on a circle.  
Full rotation = `2*PI`
```js
const tick = () =>
{
    // ...
    // Update camera
    camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2 // (*2) makes the circle larger so the camera is not close to the cube.
    camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2 // We update x and z because that is the plane we want to make the circle on.
    camera.position.y = cursor.y * 3 // The height of the camera on the y axis is determined by cursor.y and (*3) makes the movement of the camera faster and covers a larger range on y axis comared to amount the cursor travels.
    camera.lookAt(mesh.position)
    // ...
}
```

## Built In Controls
There are lots of controls in the three.js documentation. https://threejs.org/docs/index.html#api/en/math/Euler  
- ```Device Orientation Controls```: Retrieves orientation of device and allows camera rotation to mimic device. eg. VR  
- ```Fly Controls```: Moves camera like a spaceship. Can move on all 3 axes, and go forward and backward.  
- ```First Person Control```: Its like fly control but with fixed axis. Eg. Can't do barrel rolls etc. 
- ```Pointer Lock Control```: Difficult to use. Handles pointer lock, camera rotation. `Mousemove` callback.
- ```Orbit Controls```: Camera manipulation, zoom effects etc.
- ```Trackball Controls```: Same as orbit controls but without a vertical limit  
- ```Transform Controls```: Nothing to do with camera. It is like an editor maker.
- ```Drag Controls```: Nothing to do with camera. Moves objects on plane facing camera.

## Orbit Control
We do NOT update camera in the tick function now because it is handeled by custom controls.  

Import orbital controls:
```js
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
// or
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
```

Now create the control const anywhere AFTER the camera is created and added to the scene.
```js
scene.add(camera)
const controls = new OrbitControls(camera, canvas) 
// Provide camera and dom element(an element on page that is a reference to put mouse/finger on it)
// Now when mouse is clicked and moves, cube rotates/spins
// When mouse moves with right click, its drag and drop
// Zoom also works
```

You can chage default target(Center of the screen) by changing controls target.
```js
controls.target.y = 1 // Now it is looking abouve the cube slightly.
controls.update()
```

### Damping
Damping smooths the animation by adding acceleration and friction.  
For damping to work, control needs to be updated at each frame inside tick function.  
```js
const controls = new OrbitControls(camera, canvas) 
controls.enableDamping = true

const tick = () =>
{
    controls.update()
    // Render
    renderer.render(scene, camera)
    window.requestAnimationFrame(tick)
}
```

#### `Check that the controls you are going to use has all the features you need.`
<br><br>
***

# ```CHAPTER 5 - Fullscreen and Resizing```

The viewport is the html content you can see. It is NOT the screen or window.  
Use the viewport for const sizes instead of fixed numbers.
```js
const sizes = {
    width: window.innerWidth,
    height: window.innerHeight
}
```

But we can see the margins and can scroll down past the body to see more white space under it. We edit style.css file to fix this problem.  
```css
.webgl /* Target the webgl animation element. The black area. */
{
    position: fixed;
    top: 0;
    left: 0;
}

html,
body
{
    overflow: hidden;
    /* Prevent user from scrolling beyond animation area. */
}
```

## Handle Resizing
First we need to know that resizing is occuring.  
We do that by using resize event.  
```js
// Sizes
const sizes = {
    width: window.innerWidth,
    height: window.innerHeight
}

window.addEventListener('resize', () =>
{
    // Update sizes
    sizes.width = window.innerWidth
    sizes.height = window.innerHeight
    // Update camera
    camera.aspect = sizes.width / sizes.height
    // When you change camera properties like aspect you also need to update the projection matrix.
    camera.updateProjectionMatrix()
    // Update Renderer
    renderer.setSize(sizes.width, sizes.height)
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2)) //Handle pixel ratio, (next section)
})
```

## Handle Pixel Ratio
The render may be a blurry render and artifacts shaped likes stairs may be on the edges (called aliasing).  
This becomes less if the pixel ratio is larger.  
Pixel ratio is how many physical pixels are on the screen per software pixel.  
With pixel ratio = 1 when you look closly you can see all the pixel limiting how thin and precise fonts can be.  
Companies like apple made screens with pixel ratio = 2 (retina) meaning 4 pixels per software pixel.  
Pixel ratios > 2 are unnessasry. 

We need to handle pixel ratio to get better graphics but limit it to a maximum of 2 because it will cause performance issues and is unnessasary. 

```js 
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
```

## Handle Fullscreen
One way is to listen to double click.
```js
window.addEventListener('dblclick', () => 
{
    const fullScreen = document.fullscreenElement || document.webkitFullscreenElement
    // webkit prefix is to make it work on safari
    if (!fullScreen) {
        if (canvas.requestFullscreen) 
        {
            canvas.requestFullscreen()
        } 
        else if (canvas.webkitRequestFullscreen) 
        {
            canvas.webkitRequestFullscreen()
        }
        
    } else {
        if (document.exitFullscreen)
        {
            document.exitFullscreen()
        }
        else if (document.webkitExitFullscreen)
        {
            document.webkitExitFullscreen()
        }
    }
})
```