<script lang="ts">
  import * as geolocation from "@nativescript/geolocation";
  import { keepAwake, allowSleepAgain } from "@nativescript-community/insomnia";
  import { CoreTypes } from "@nativescript/core";
  let items: string[] = [];

  let lastPoint: {
    lat: number;
    lon: number;
    speed: number;
    direction: number;
  } = {
    lat: 0,
    lon: 0,
    speed: 0,
    direction: 0,
  };

  function addString(s: string) {
    items = [s, ...items];
  }

  function addPoint(
    lat: number,
    lon: number,
    speed: number,
    direction: number,
  ) {
    speed = Math.round(speed * 3.6);
    direction = Math.round(direction);

    if (
      lastPoint.lat === lat &&
      lastPoint.lon === lon &&
      lastPoint.direction == direction
    ) {
      return;
    }
    lastPoint = { lat, lon, speed, direction };
    addString(
      "pos: " +
        lat +
        "," +
        lon +
        " / speed: " +
        speed +
        " / direction: " +
        direction,
    );
  }

  geolocation.enableLocationRequest().then(() => {
    addString("Location enabled");
    keepAwake().then(() => {
      addString("Keep awake");
    });
    geolocation.watchLocation(
      (location) => {
        addPoint(
          location.latitude,
          location.longitude,
          location.speed,
          location.direction,
        );
      },
      (e) => {
        addString("Error: " + e.message);
      },
      {
        desiredAccuracy: CoreTypes.Accuracy.high,
        updateDistance: 1,
        updateTime: 1000,
      },
    );
    // geolocation
    //   .getCurrentLocation({
    //     desiredAccuracy: CoreTypes.Accuracy.high,
    //     maximumAge: 5000,
    //     timeout: 20000,
    //   })
    //   .then((currentLocation) => {
    //     add(
    //       "pos:" +
    //         currentLocation.latitude +
    //         "," +
    //         currentLocation.longitude +
    //         " speed:" +
    //         currentLocation.speed,
    //     );
    //     // console.log("My current latitude: ", currentLocation.latitude);
    //   })
    //   .catch((e) => {
    //     add(e.message);
    //   });
  });
</script>

<page actionBarHidden="true">
  <flexboxLayout>
    <scrollView orientation="vertical" height>
      <stackLayout orientation="vertical">
        {#each items as item}
          <label>{item}</label>
        {/each}
      </stackLayout>
    </scrollView>
  </flexboxLayout>
</page>
