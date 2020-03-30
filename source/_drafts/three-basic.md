---
title: three.js 基础教程
date: 2020-03-20 21:47:06
tags:
---

## 初始环境

```json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "sourceMap": true,
    "noImplicitAny": true,
    "module": "es6",
    "target": "es6", // add
    "moduleResolution": "node", // add
    "jsx": "react",
    "allowJs": true,
    "allowSyntheticDefaultImports": true
  },
  "include": ["./src/**/*.ts"] // add
}
```

#### hello world

```typescript
import * as THREE from 'three'

const fieldOfView = 75 // 视野角度
const aspectRatio: number = window.innerWidth / window.innerHeight
const near: number = 0.1
const far: number = 1000
const scene: THREE.Scene = new THREE.Scene()
const camera: THREE.PerspectiveCamera = new THREE.PerspectiveCamera(
  fieldOfView,
  aspectRatio,
  near,
  far
)

const renderer: THREE.WebGLRenderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

// init box
const geometry: THREE.BoxGeometry = new THREE.BoxGeometry()
const material: THREE.MeshBasicMaterial = new THREE.MeshBasicMaterial({
  color: 0x00ff00
})
const cube: THREE.Mesh = new THREE.Mesh(geometry, material)
scene.add(cube)

camera.position.z = 5

var animate = function() {
  requestAnimationFrame(animate)

  cube.rotation.x += 0.01
  cube.rotation.y += 0.01

  renderer.render(scene, camera)
}

animate()
```

ts 配置参考[这里](https://threejs.org/docs/index.html#manual/zh/introduction/Typescript-setup)
