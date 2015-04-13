# Leap Motion CodeLab 01 : Leap for Web

Ce répository github contient le code pour réaliser une petite application web de ~200 lignes avec le SDK du Leap Motion Controller. 
L'application permet d'afficher la main de l'utilisateur en récupérant les informations provenant du Leap grâce au framework ThreeJS (WebGL).

1. Ajouter les SDK de Leap Motion et ThreeJS
```html
<!-- Inclusion du SDK de Leap Motion (dernière version stable) -->
<script src=http://js.leapmotion.com/leap-0.6.4.js ></script>
<script src=http://js.leapmotion.com/leap-plugins-0.1.10.js ></script>

<!-- Inclusion du SDK Three.js pour le WebGL -->
<script src=http://mrdoob.github.io/three.js/build/three.min.js ></script>
<script src=http://mrdoob.github.io/three.js/examples/js/controls/TrackballControls.js ></script>
<script src=http://mrdoob.github.io/three.js/examples/js/libs/stats.min.js ></script>
```

2. Déclaration de toutes les variables de portée globale
```js
	var fingerlings = {};
	var handies = {};
	var loop = {};

	var info, stats, renderer, scene, camera, controls;
```