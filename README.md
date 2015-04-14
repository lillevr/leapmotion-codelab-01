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

 7. Création du renderer WebGL qui va permettre l'affichage de la main de l'utilisateur dans la page web grâce aux données qui proviennent du Leap Motion. Ensuite, on l'ajoute à l'arbre DOM de la page courante.
```js
		renderer = new THREE.WebGLRenderer( { alpha: 1, antialias: true, clearColor: 0xffffff }  );
		renderer.setSize( window.innerWidth, window.innerHeight );
		document.body.appendChild( renderer.domElement );
```

 8. Toute scène 3D doit contenir une caméra. C'est pourquoi nous allons ajouter une caméra perspective dans notre scène, en la positionnant aux coordonnées 500,500,500 pour x, y et z.
```js
		camera = new THREE.PerspectiveCamera( 40, window.innerWidth / window.innerHeight, 1, 5000 );
		camera.position.set( 500, 500, 500 );
```

 9. Activer la gestion de la souris dans la scène avec les outils WebGL.
```js
		controls = new THREE.TrackballControls( camera, renderer.domElement );
```

 10. Création de l'objet Scène 3D en WebGL
```js
		scene = new THREE.Scene();
```

 11. Création du sol de la scène sous forme d'un carré plat
```js
		var geometry = new THREE.BoxGeometry( 500, 2, 500 );
		material = new THREE.MeshNormalMaterial();
		var mesh = new THREE.Mesh( geometry, material );
		mesh.position.set( 0, -1, 0 );
		scene.add( mesh );
```

12. On ajoute des outils ThreeJS permettant de faire une grille et d'ajouter les axes cartésiens dans la scène 3D
```js
		mesh = new THREE.GridHelper( 250, 10 );
		scene.add( mesh );
		var axis = new THREE.AxisHelper( 250 );
		scene.add( axis );
```

13. Et enfin, on demande au renderer WebGL de rendre la scène 3D que nous avons créé. Cet appel termine le corps de la méthode init.
```js
		renderer.render( scene, camera );
```

14. Définition de la méthode de boucle de rendu principale qui récupère les données du Leap Motion Controller
```js
	loop.animate = function( frame ) {

		frame.hands.forEach( function( hand, index ) {

			// Récupération des données de la main
			var handy = ( handies[index] || ( handies[index] = new Handy()) );    
			handy.outputData( index, hand );

			// Récupération des données de chaque doigt
			hand.fingers.forEach( function( finger, index ) {

				var fingerling = ( fingerlings[index] || ( fingerlings[index] = new Fingerling() ) );  
				fingerling.outputData( index, finger );

			});

		});

		// Mise à jour de tous les outils WebGL : le rendu de la scène, les contrôles et les statistiques
		renderer.render( scene, camera );
		controls.update();
		stats.update();

	}
```

15. On dit à Leap quelle est la fonction qui va s'occuper de récupérer les données provenant du Leap Motion Controller pour qu'on puisse accéder à celles-ci.
```js
	loop = Leap.loop( loop.animate );
	loop.use( 'screenPosition', { scale: 0.25 } ); 
```

16. On définit la fonction qui va matérialiser une main sur base de données qui sont récupérées dans la méthode Loop enregistrée auprès du SDK de Leap Motion.
```js
	var Handy = function() {
		var handy = this;
		var msg = handData.appendChild( document.createElement( 'div' ) );

		// Création des géométries WebGL correspondant à une main 
		var geometry = new THREE.BoxGeometry( 50, 20, 50 );
		var material = new THREE.MeshNormalMaterial();
		var box = new THREE.Mesh( geometry, material );
		scene.add( box );

		handy.outputData = function( index, hand  ) {

			msg.innerHTML = 'Hand id:' + index + ' x:' + hand.stabilizedPalmPosition[0].toFixed(0) + 
				' y:' + hand.stabilizedPalmPosition[1].toFixed(0) + ' z:' + hand.stabilizedPalmPosition[2].toFixed(0);

			box.position.set( hand.stabilizedPalmPosition[0], hand.stabilizedPalmPosition[1], hand.stabilizedPalmPosition[2] );

			box.rotation.set( hand.pitch(), -hand.yaw(), hand.roll() );

		};

	};
```

17. On définit la méthode de matérialisation des doigts d'une main en WebGL
```js
	var Fingerling = function() {

		var fingerling = this;
		var msg = fingerData.appendChild( document.createElement( 'div' ) );

		// On créé chacune des phalanges, la méthode addPhalange sera détaillée plus tard
		var tip = addPhalange();
		var dip = addPhalange();
		var pip = addPhalange();
		var mcp = addPhalange();
		var carp = addPhalange();

		fingerling.outputData = function( index, finger ) {

			msg.innerHTML = 'Finger Method: ' +
				'finger id:' + index + ' tip x:' + finger.tipPosition[0].toFixed(0) + 
				' y:' + finger.tipPosition[1].toFixed(0) + ' z:' + finger.tipPosition[2].toFixed(0);

			// On positionne les phalanges en fonction des données récupérées du Leap Motion Controller
			tip.position.set( finger.tipPosition[0], finger.tipPosition[1], finger.tipPosition[2] );
			dip.position.set( finger.dipPosition[0], finger.dipPosition[1], finger.dipPosition[2] );
			pip.position.set( finger.pipPosition[0], finger.pipPosition[1], finger.pipPosition[2] );
			mcp.position.set( finger.mcpPosition[0], finger.mcpPosition[1], finger.mcpPosition[2] );
			carp.position.set( finger.carpPosition[0], finger.carpPosition[1], finger.carpPosition[2] );

			updatePhalange( tip, dip );
			updatePhalange( dip, pip );
			updatePhalange( pip, mcp );

			if ( finger.type > 0 ) {

				updatePhalange( mcp, carp );

			}

		};

	};
```

18. On définit la méthode qui va lier les phalanges entre-elles, autrement dit qui va matérialiser virtuellement les os de la main dans la scène.
```js
	var Fingerling = function() {

		var fingerling = this;
		var msg = fingerData.appendChild( document.createElement( 'div' ) );

		var phalanges = [];

		for (var i = 0; i < 4; i++) {
			phalange = addPhalange();
			phalanges.push( phalange )
		}

		fingerling.outputData = function( index, finger ) {

			msg.innerHTML = 'Bone Method ~ ' +
				'finger tip: ' + index + ' x:' + finger.tipPosition[0].toFixed(0) + 
				' y:' + finger.tipPosition[1].toFixed(0) + ' z:' + finger.tipPosition[2].toFixed(0);

			for (var i = 0; i < 4; i++) {
				bone = finger.bones[ i ];
				cen = bone.center();
				len = bone.length;

				phalange = phalanges[ i ];
				phalange.position.set( cen[0], cen[1], cen[2] );
				if ( index > 0 || i > 0 ) {
					phalange.scale.z = len;
				}
			}
 
			phalanges[3].lookAt( v( finger.tipPosition[0], finger.tipPosition[1], finger.tipPosition[2]  ) );
			phalanges[2].lookAt( v( finger.dipPosition[0], finger.dipPosition[1], finger.dipPosition[2]  ) );
			phalanges[1].lookAt( v( finger.pipPosition[0], finger.pipPosition[1], finger.pipPosition[2]  ) );
			if ( index > 0 ) {
				phalanges[0].lookAt( v( finger.mcpPosition[0], finger.mcpPosition[1], finger.mcpPosition[2]  ) );
			}

		};

	};
```

19. Enfin, on définit les méthodes utilitaires. On commence par la méthode que nous avons utilisées plus haut, la méthode qui crée une phalange de doigt en WebGL.
```js
	function addPhalange() {

		geometry = new THREE.BoxGeometry( 20, 20, 1 );
		material = new THREE.MeshNormalMaterial();
		phalange = new THREE.Mesh( geometry, material );
		scene.add( phalange );
		return phalange;

	}
```

20.  On définit la méthode qui met à jour la position et la rotation d'une phalange en fonction des données du Leap Motion Controller.
```js
	function updatePhalange( phalange, nextPhalange ) {

			phalange.lookAt( nextPhalange.position );
			length = phalange.position.distanceTo( nextPhalange.position );
			phalange.translateZ( 0.5 * length );
			phalange.scale.set( 1, 1, length );

	}
```

21. Et pour terminer, une petite méthode utilitaire pour créer un vecteur 3D sur base des coordonnées x,y,z
```js
	function v(  x, y, z){ return new THREE.Vector3( x, y, z ); }
```

A partir de maintenant vous devriez pouvoir lancer votre page web. Pour le moment, vous devriez avoir une scène vide puisqu'il n'y a aucune donnée reçue du Leap Motion.
Demandez l'un des Leap Motion pour tester votre application. Si vous avez votre main qui s'affiche, félicitations, vous avez correctement réussi ce petit code lab !