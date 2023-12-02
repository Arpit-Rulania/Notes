# CHAPTER 1 - Basics of Three JS

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
