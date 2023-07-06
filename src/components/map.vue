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

              (function(result) {
                marker.addListener('mouseover', function() {
                  var contentString = '<div id="content">'+
                    '<h1>' + result.organization_name + '</h1>'+
                    '<h2>' + result.resource_descriptive_name + '</h2>'+
                    '<p>' + result.resource_description + '</p>'+
                    '<ul>';
                  for (var j = 0; j < result.features_list.length; j++) {
                    contentString += '<li>' + result.features_list[j] + '</li>';
                  }
                  contentString += '</ul></div>';

                  infowindow.setContent(contentString);
                  infowindow.open(map, this);
                });
              })(results[i]);

              marker.addListener('click', function() {
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

#map {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  color: black;
}
</style>