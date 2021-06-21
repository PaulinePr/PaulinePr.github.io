<!DOCTYPE html>
    <meta charset="utf-8" />
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no" />
    <title>ArcGIS API for JavaScript Tutorials: Display a map</title>



    <link rel="stylesheet" href="https://js.arcgis.com/4.19/esri/themes/light/main.css">
    <link href="projet.css" rel="stylesheet" type="text/css">

        <!-- Load d3.js -->
    <script src="https://d3js.org/d3.v4.js"></script>


    <script src="https://js.arcgis.com/4.19/"></script>


    <div id="viewDiv"></div>

    <div id="y1"> Select the first year for the chord diagram.  <br> </div>
    <input type="radio" name="y1" id="2015_1" value="2015" checked/>2015
    <input type="radio" name="y1" id="2016_1" value="2016" />2016
    <input type="radio" name="y1" id="2017_1" value="2017" />2017
    <input type="radio" name="y1" id="2018_1" value="2018" />2018
    <input type="radio" name="y1" id="2019_1" value="2019" />2019
    <input type="radio" name="y1" id="2020_1" value="2020" />2020 <br>

    <div id="y1"> Select the second year for the chord diagram.  <br> </div>
    <input type="radio" name="y2" id="2015_2" value="2015" checked/>2015
    <input type="radio" name="y2" id="2016_2" value="2016" />2016
    <input type="radio" name="y2" id="2017_2" value="2017" />2017
    <input type="radio" name="y2" id="2018_2" value="2018" />2018
    <input type="radio" name="y2" id="2019_2" value="2019" />2019
    <input type="radio" name="y2" id="2020_2" value="2020" />2020

    <div id="button"></div>

    <!-- Create a div where the graph will take place -->
    <div id="chart"></div>

    <!-- Create a div for the tooltip-->
    <div id="tooltip"></div>

    <script>


      require(["esri/config","esri/Map", "esri/WebMap", "esri/views/MapView", "esri/widgets/Sketch",
      "esri/layers/GraphicsLayer",
      "esri/widgets/LayerList",
      "esri/layers/FeatureLayer", "esri/widgets/ScaleBar"], function (esriConfig,Map,WebMap, MapView,Sketch, GraphicsLayer,LayerList, FeatureLayer, ScaleBar) {


        var liste_2015 = [];
        var listeid2015 = [];
        var listecrop2015 = [];

        var liste_2016 = [];
        var listeid2016 = [];
        var listecrop2016 = [];

        var liste_2017 = [];
        var listeid2017 = [];
        var listecrop2017 = [];

        var liste_2018 = [];
        var listeid2018 = [];
        var listecrop2018 = [];

        var liste_2019 = [];
        var listeid2019 = [];
        var listecrop2019 = [];

        var liste_2020 = [];
        var listeid2020 = [];
        var listecrop2020 = [];

        var liste_comp = [];
        var liste_names = [];

        var map = new WebMap({
          portalItem: {
            // autocasts as new PortalItem()
            id: "bc4d3ea05b2144a5b59966b882d28a98"
          }
        });

        var view = new MapView({
          center: [16.8, 48.25],
          zoom: 15, //11
          container: "viewDiv",
          map: map
        });

        // Add sketch widget
      const graphicsLayerSketch = new GraphicsLayer();
      map.add(graphicsLayerSketch);

      const sketch = new Sketch({
        layer: graphicsLayerSketch,
        view: view,
        visibleElements: {
                    createTools: {
                        point: false,
                        polyline: false,
                        circle: false
                    },
                    selectionTools: {
                        "lasso-selection": false,
                        "rectangle-selection": false,
                    },
                },
        creationMode: "update" // Auto-select
      });

      view.ui.add(sketch, "top-right");

      // Add sketch events to listen for and execute query
      sketch.on("update", (event) => {
        // Create
        if (event.state === "start") {
          queryFeaturelayer2015(event.graphics[0].geometry);
          queryFeaturelayer2016(event.graphics[0].geometry);
          queryFeaturelayer2017(event.graphics[0].geometry);
          queryFeaturelayer2018(event.graphics[0].geometry);
          queryFeaturelayer2019(event.graphics[0].geometry);
          queryFeaturelayer2020(event.graphics[0].geometry);

          foo();

        }

        if (event.state === "complete"){

        }


        // Change
        if (event.toolEventInfo && (event.toolEventInfo.type === "scale-stop" || event.toolEventInfo.type === "reshape-stop" || event.toolEventInfo.type === "move-stop")) {
          queryFeaturelayer2015(event.graphics[0].geometry);
          queryFeaturelayer2016(event.graphics[0].geometry);
          queryFeaturelayer2017(event.graphics[0].geometry);
          queryFeaturelayer2018(event.graphics[0].geometry);
          queryFeaturelayer2019(event.graphics[0].geometry);
          queryFeaturelayer2020(event.graphics[0].geometry);
        }
      });




      function create_comparaison_list(l1,l2,l4,l5){
        liste_comp = [];
        //create_comparaison_list(lid2,lid1,liste_comp,lcrop1,lcrop2);
        for(var i = 0; i< l1.length;i++){
          var a = l1[i];
              if(l2.indexOf(a)>-1){
                liste_comp.push([l4[l2.indexOf(a)],l5[i]]);
              }

          }
          console.log("liste_comp : ");
          console.log(liste_comp);
      }

      function create_names_list(l1,l2){
        liste_names = [];
        for(var i = 0; i < l1.length; i++){
          if(liste_names.includes(l1[i]) == false){
            liste_names.push(l1[i]);
          }
        }
        for(var j = 0; j < l2.length; j++){
          if(liste_names.includes(l2[j]) == false){
            liste_names.push(l2[j]);
          }
        }
      }

      function create_matrix_empty(l2){

        var matrix = Array(l2.length);
        for (var i = 0; i < matrix.length; i++) {
          matrix[i] = new Array(l2.length);
        }
        for (var i = 0; i < matrix.length; i++) {
          for (var j = 0; j < matrix.length; j++) {
            matrix[i][j] = 0;
          }
        }
        return matrix;
      }

      function create_matrix(l1,l2){
        //l1 = liste_comp
        //l2 = liste_names
        var matrix = create_matrix_empty(l2);

        console.log("taille l1 : " + l1.length);

        for (var j = 0 ; j< l1.length; j++){
          var a = l2.indexOf(l1[j][0]);
          var b = l2.indexOf(l1[j][1]);
          matrix[a][b] += 1;
        }
        console.log(matrix);
        return matrix;

      }

      function create_diagram(matrix, names){
        var margin = {left:500, top:300, right:150, bottom:150},
            width =   Math.min(window.innerWidth, 1000)
            height =  Math.min(window.innerWidth, 1000)
            innerRadius = Math.min(width, height) * .50,
            outerRadius = innerRadius * 1.1;


            //var names = liste_names;
            var colors = Array(matrix.length);
            for(var i = 0; i< colors.length; i++){
              colors[i] = "#" + Math.floor(Math.random()*16777215).toString(16);
            }
            opacityDefault = 0.8;


          ////////////////////////////////////////////////////////////
          /////////// Create scale and layout functions //////////////
          ////////////////////////////////////////////////////////////

          var colors = d3.scaleOrdinal()
              .domain(d3.range(names.length))
            .range(colors);

          var chord = d3.chord()
            .padAngle(.15)
            .sortChords(d3.descending)

            var arc = d3.arc()
            .innerRadius(innerRadius*1.01)
            .outerRadius(outerRadius);

          var path = d3.ribbon()
          .radius(innerRadius);

        ////////////////////////////////////////////////////////////
        ////////////////////// Create SVG //////////////////////////
        ////////////////////////////////////////////////////////////

        var svg = d3.select("#chart").append("svg")
          .attr("width", width + margin.left + margin.right)
          .attr("height", height + margin.top + margin.bottom)
          .append("g")
          .attr("transform", "translate(" + (width/2 + margin.left) + "," + (height/2 + margin.top) + ")")
          .datum(chord(matrix));

        ////////////////////////////////////////////////////////////
        ////////////////// Draw outer Arcs /////////////////////////
        ////////////////////////////////////////////////////////////

        var outerArcs = svg.selectAll("g.group")
          .data(function(chords) { return chords.groups; })
          .enter().append("g")
          .attr("class", "group")
          .on("mouseover", fade(.1))
          .on("mouseout", fade(opacityDefault))


        outerArcs.append("path")
          .style("fill", function(d) { return colors(d.index); })
          .attr("id", function(d, i) { return "group" + d.index; })
          .attr("d", arc);



        ////////////////////////////////////////////////////////////
        ////////////////////// Append names ////////////////////////
        ////////////////////////////////////////////////////////////

        //Append the label names on the outside
        outerArcs.append("text")
          .each(function(d) { d.angle = (d.startAngle + d.endAngle) / 2; })
          .attr("dy", ".35em")
          .attr("class", "titles")
          .attr("text-anchor", function(d) { return d.angle > Math.PI ? "end" : null; })
          .attr("transform", function(d) {
            return "rotate(" + (d.angle * 180 / Math.PI - 90) + ")"
            + "translate(" + (outerRadius + 10) + ")"
            + (d.angle > Math.PI ? "rotate(180)" : "");
          })
          .text(function(d,i) { return names[i]; });

        ////////////////////////////////////////////////////////////
        ////////////////// Draw inner chords ///////////////////////
        ////////////////////////////////////////////////////////////

        svg.selectAll("path.chord")
          .data(function(chords) { return chords; })
          .enter().append("path")
          .attr("class", "chord")
          .style("fill", function(d) { return colors(d.source.index); })
          .style("opacity", opacityDefault)
          .attr("d", path);

        ////////////////////////////////////////////////////////////
        ////////////////// Extra Functions /////////////////////////
        ////////////////////////////////////////////////////////////



        //Returns an event handler for fading a given chord group.
        function fade(opacity) {
          return function(d,i) {
            svg.selectAll("path.chord")
                .filter(function(d) { return d.source.index != i && d.target.index != i; })
            .transition()
                .style("opacity", opacity);
          };
        }//fade


      }


      function foo(){
        document.getElementById("button").innerHTML = "";
        var button = document.createElement('button');
        button.innerHTML = 'Generate Chord Diagram';
        button.onclick = function(){
          document.getElementById("chart").innerHTML = "";
          var  lid1 = [];
          var  lcrop1 = [];
          var  lid2 = [];
          var  lcrop2 = [];


          if(document.getElementById('2015_1').checked) {
            lid1 = listeid2015;
            lcrop1 = listecrop2015;
          }
          if(document.getElementById('2016_1').checked) {
            lid1 = listeid2016;
            lcrop1 = listecrop2016;
          }
          if(document.getElementById('2017_1').checked) {
            lid1 = listeid2017;
            lcrop1 = listecrop2017;
          }
          if(document.getElementById('2018_1').checked) {
            lid1 = listeid2018;
            lcrop1 = listecrop2018;
          }
          if(document.getElementById('2019_1').checked) {
            lid1 = listeid2019;
            lcrop1 = listecrop2019;
          }
          if(document.getElementById('2020_1').checked) {
            lid1 = listeid2020;
            lcrop1 = listecrop2020;
          }



          if(document.getElementById('2015_2').checked) {
            lid2 = listeid2015;
            lcrop2 = listecrop2015;
          }
          if(document.getElementById('2016_2').checked) {
            lid2 = listeid2016;
            lcrop2 = listecrop2016;
          }
          if(document.getElementById('2017_2').checked) {
            lid2 = listeid2017;
            lcrop2 = listecrop2017;
          }
          if(document.getElementById('2018_2').checked) {
            lid2 = listeid2018;
            lcrop2 = listecrop2018;
          }
          if(document.getElementById('2019_2').checked) {
            lid2 = listeid2019;
            lcrop2 = listecrop2019;
          }
          if(document.getElementById('2020_2').checked) {
            lid2 = listeid2020;
            lcrop2 = listecrop2020;
          }



          create_comparaison_list(lid2,lid1,lcrop1,lcrop2);

          create_names_list(lcrop1,lcrop2);

          var matrix = create_matrix(liste_comp,liste_names);
          console.log(matrix);

          create_diagram(matrix,liste_names);

        };
        // where do we want to have the button to appear?
        // you can append it to another element just by doing something like
        // document.getElementById('foobutton').appendChild(button);
        document.getElementById("button").appendChild(button);
      }




      // Reference query layer
      const parcelLayer2015 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2015_POLYGO/FeatureServer");

      function queryFeaturelayer2015(geometry) {
        liste_2015 = [];
        listeid2015 = [];
        listecrop2015 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2015.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2015.push(results.features[i].attributes);
            listeid2015.push(results.features[i].attributes.GEO_ID);
            listecrop2015.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2015
      }


      // Reference query layer
      const parcelLayer2016 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2016_POLYGO/FeatureServer");

      function queryFeaturelayer2016(geometry) {
        liste_2016 = [];
        listeid2016 = [];
        listecrop2016 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2016.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2016.push(results.features[i].attributes);
            listeid2016.push(results.features[i].attributes.GEO_ID);
            listecrop2016.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2016
      }

      // Reference query layer
      const parcelLayer2017 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2017_POLYGO/FeatureServer");

      function queryFeaturelayer2017(geometry) {
        liste_2017 = [];
        listeid2017 = [];
        listecrop2017 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2017.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2017.push(results.features[i].attributes);
            listeid2017.push(results.features[i].attributes.GEO_ID);
            listecrop2017.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2017
      }

      // Reference query layer
      const parcelLayer2018 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2018_POLYGO/FeatureServer");

      function queryFeaturelayer2018(geometry) {
        liste_2018 = [];
        listeid2018 = [];
        listecrop2018 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2018.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2018.push(results.features[i].attributes);
            listeid2018.push(results.features[i].attributes.GEO_ID);
            listecrop2018.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2018
      }

      // Reference query layer
      const parcelLayer2019 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2019_POLYGO/FeatureServer");

      function queryFeaturelayer2019(geometry) {
        liste_2019 = [];
        listeid2019 = [];
        listecrop2019 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2019.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2019.push(results.features[i].attributes);
            listeid2019.push(results.features[i].attributes.GEO_ID);
            listecrop2019.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2019
      }

      // Reference query layer
      const parcelLayer2020 = new FeatureLayer("https://services2.arcgis.com/FWreTot7ighRlJtc/arcgis/rest/services/INSPIRE_SCHLAEGE_2020_POLYGO/FeatureServer");

      function queryFeaturelayer2020(geometry) {
        liste_2020 = [];
        listeid2020 = [];
        listecrop2020 = [];

        const parcelQuery = {
         spatialRelationship: "intersects", // Relationship operation to apply
         geometry: geometry,  // The sketch feature geometry
         outFields: ["GEO_ID","SNAR_BEZEI"], // Attributes to return
         returnGeometry: true
        };
        parcelLayer2016.queryFeatures(parcelQuery)
        .then((results) => {
          for(var i = 0; i< results.features.length; i++){
            liste_2020.push(results.features[i].attributes);
            listeid2020.push(results.features[i].attributes.GEO_ID);
            listecrop2020.push(results.features[i].attributes.SNAR_BEZEI);
          }

        }).catch((error) => {
          console.log(error);
        });
        return liste_2020
      }




      view.when(function() {
        // Create the LayerList widget with the associated actions
        // and add it to the top-right corner of the view.

        var layerList = new LayerList({
          view: view
        });

        // Add widget to the top right corner of the view
        view.ui.add(layerList, "top-right");
      });

      var scaleBar = new ScaleBar({
        view: view,
        unit: "dual"
      });
      // Add widget to the bottom left corner of the view
      view.ui.add(scaleBar, {
        position: "bottom-left"
      });





      });



    </script>
