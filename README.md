# Leap Motion CodeLab 01 : Leap for Web

Ce répository github contient le code pour réaliser une petite application web de ~200 lignes avec le SDK du Leap Motion Controller. 
L'application permet d'afficher la main de l'utilisateur en récupérant les informations provenant du Leap grâce au framework ThreeJS (WebGL).

1. Ajouter les SDK de Leap Motion et ThreeJS
```html
<!-- Inclusion du SDK de Leap Motion (dernière version stable) -->
<script src=http://js.leapmotion.com/leap-0.6.4.js ></script>
<script src=http://js.leapmotion.com/leap-plugins-0.1.10.js ></script>

<!-- Inclusion du SDK ThreeJS pour le WebGL -->
<script src=http://mrdoob.github.io/three.js/build/three.min.js ></script>
<script src=http://mrdoob.github.io/three.js/examples/js/controls/TrackballControls.js ></script>
<script src=http://mrdoob.github.io/three.js/examples/js/libs/stats.min.js ></script>
```

2. Déclaration de toutes les variables de portée globale
```js
	var fingerlings = {}; // La liste des doigts
	var handies = {}; // La liste des mains
	var loop = {};

	var info, stats, renderer, scene, camera, controls; // Variables ThreeJS
```

3. Appel de la fonction principale du script Javascript
```js
	init();
```

4. Définition de la méthode principale init qui va contenir tout le code de traitement Leap Motion et WebGL
```js
	function init() {
		
	}
```

5. Ajout de l'affichage en temps réel des données qui proviennent du Leap Motion dans la page HTML
```js
		document.body.style.cssText = 'font: 600 12pt monospace; margin: 0; overflow: hidden;' ;

		var info = document.body.appendChild( document.createElement( 'div' ) );

		info.style.cssText = 'left: 20px; position: absolute; ';
		info.innerHTML = '<a href="" ><h1>' + document.title + '</h1></a>' +
			'<div id=handData ></div>' +
			'<div id=fingerData ></div>' +
		'';
```

6. Ajout de l'affichage en temps réel des statistiques WebGL de la page web
```js
		stats = new Stats();
		stats.domElement.style.cssText = 'position: absolute; right: 0; top: 0; z-index: 100; ';
		document.body.appendChild( stats.domElement );
```

7. 
```js
		renderer = new THREE.WebGLRenderer( { alpha: 1, antialias: true, clearColor: 0xffffff }  );
		renderer.setSize( window.innerWidth, window.innerHeight );
		document.body.appendChild( renderer.domElement );
```