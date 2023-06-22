<template>
  <div id="map"></div>
</template>

<script>
export default {
  name: 'Map',
  mounted() {
    window.initMap = () => {
      console.log('initMap called');
      var map = new google.maps.Map(document.getElementById('map'), {
        center: { lat: -34.397, lng: 150.644 },
        zoom: 8,
      });

      // Get the infrastructure locations from the REST API.
      var markers = [];
      var data = [];
      $.getJSON(
        'https://operations-api.access-ci.org/wh2/cider/v1/access-allocated/',
        function (data) {
          var results = data.results;
          var bounds = new google.maps.LatLngBounds();
          for (var i = 0; i < results.length; i++) {
            if (
              typeof results[i].latitude === 'number' &&
              typeof results[i].longitude === 'number'
            ) {
              var marker = new google.maps.Marker({
                position: {
                  lat: results[i].latitude,
                  lng: results[i].longitude,
                },
                map: map,
              });
              marker.addListener('click', function () {
                window.open(
                  'https://www.google.com/maps/search/' +
                    this.getPosition().lat() +
                    ',' +
                    this.getPosition().lng()
                );
              });
              markers.push(marker);
              bounds.extend(marker.getPosition());
            }
          }
          map.fitBounds(bounds);
        }
      );
    };
    console.log('initMap before calling', initMap);
    initMap();
  },
};
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

p {
  margin: 20px;
}

#map {
  width: 800px;
  height: 600px;
  margin: 0px auto 0 auto;
}
</style>



<!-- <script>
export default {
  name: 'Map',
  mounted() {
    window.initMap = () => {
      console.log('initMap called');
      var map = new google.maps.Map(document.getElementById('map'), {
        center: { lat: -34.397, lng: 150.644 },
        zoom: 8,
      });

      // Get the infrastructure locations from the REST API.
      var markers = [];
      var data = [];
      $.getJSON(
        'https://operations-api.access-ci.org/wh2/cider/v1/access-allocated/',
        function (data) {
          var results = data.results;
          var bounds = new google.maps.LatLngBounds();

          var infowindow = new google.maps.InfoWindow({
            content: "Loading..."
          });

          for (var i = 0; i < results.length; i++) {
            if (
              typeof results[i].latitude === 'number' &&
              typeof results[i].longitude === 'number'
            ) {
              var marker = new google.maps.Marker({
                position: {
                  lat: results[i].latitude,
                  lng: results[i].longitude,
                },
                map: map,
              });

              marker.addListener('mouseover', function() {
                infowindow.setContent("Loading...");
                infowindow.open(map, this);
                $.get("https://www.google.com/maps/search/" + this.getPosition().lat() + "," + this.getPosition().lng(), function(data) {
                  infowindow.setContent(data);
                });
              });

              marker.addListener('mouseout', function() {
                infowindow.close();
              });

              markers.push(marker);
              bounds.extend(marker.getPosition());
            }
          }
          map.fitBounds(bounds);
        }
      );
    };
    console.log('initMap before calling', initMap);
    initMap();
  },
};
</script> -->