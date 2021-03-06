<script>
import { GlSingleStat } from '@gitlab/ui/dist/charts';
import { SUPPORTED_FORMATS, getFormatter } from '~/lib/utils/unit_format';
import { graphDataValidatorForValues } from '../../utils';

const defaultPrecision = 2;

export default {
  components: {
    GlSingleStat,
  },
  inheritAttrs: false,
  props: {
    graphData: {
      type: Object,
      required: true,
      validator: graphDataValidatorForValues.bind(null, true),
    },
  },
  computed: {
    queryInfo() {
      return this.graphData.metrics[0];
    },
    queryResult() {
      return this.queryInfo.result[0]?.value[1];
    },
    /**
     * This method formats the query result from a promQL expression
     * allowing a user to format the data in percentile values
     * by using the `maxValue` inner property from the graphData prop
     * @returns {(String)}
     */
    statValue() {
      let formatter;

      if (this.graphData?.maxValue) {
        formatter = getFormatter(SUPPORTED_FORMATS.percent);
        return formatter(this.queryResult / Number(this.graphData.maxValue), defaultPrecision);
      }

      formatter = getFormatter(SUPPORTED_FORMATS.number);
      return `${formatter(this.queryResult, defaultPrecision)}${this.queryInfo.unit}`;
    },
    graphTitle() {
      return this.queryInfo.label;
    },
  },
};
</script>
<template>
  <div>
    <gl-single-stat :value="statValue" :title="graphTitle" variant="success" />
  </div>
</template>
