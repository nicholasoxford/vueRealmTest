<script setup>
import { useQuery, useResult } from "@vue/apollo-composable";
import gql from "graphql-tag";

const { result, loading, error } = useQuery(gql`
  query AllShips {
    # {"data":null,"errors":[{"message":"reason="could not validate document: \\n\\tdepth: Invalid type. Expected: type: undefined, bsonType: string, given: [double number float mixed]"; code="SchemaValidationFailedRead"; untrusted="read not permitted"; details=map[]","locations":[{"line":2,"column":3}],"path":["shipwrecks"]}]}
    shipwreck {
      _id
      chart
      coordinates
      depth
      feature_type
      gp_quality
      history
      latdec
      londec
      quasou
      recrd
      sounding_type
      vesslterms
      watlev
    }
  }
`);

const messages = useResult(result, [], (data) => data.coordinates);

const center = [116.54875, 40.45064];
const projection = "EPSG:4326";
const zoom = 17;
const rotation = 0;
const radius = 10;
const strokeWidth = 4;
const strokeColor = "red";
const fillColor = "white";
</script>

<template>
  <ol-map
    :loadTilesWhileAnimating="true"
    :loadTilesWhileInteracting="true"
    style="height: 400px"
  >
    <ol-view
      ref="view"
      :center="center"
      :rotation="rotation"
      :zoom="zoom"
      :projection="projection"
    />

    <ol-tile-layer>
      <ol-source-osm />
    </ol-tile-layer>

    <ol-vector-layer>
      <ol-source-vector>
        <ol-feature>
          <ol-geom-multi-point :coordinates="messages"></ol-geom-multi-point>
          <ol-style>
            <ol-style-circle :radius="radius">
              <ol-style-fill :color="fillColor"></ol-style-fill>
              <ol-style-stroke
                :color="strokeColor"
                :width="strokeWidth"
              ></ol-style-stroke>
            </ol-style-circle>
          </ol-style>
        </ol-feature>
      </ol-source-vector>
    </ol-vector-layer>
  </ol-map>
</template>

<style scoped>
a {
  color: #42b983;
}
</style>
