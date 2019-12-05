<template>
  <div class="hello">
    <span>Working {{data}}</span>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data() {
    return {
      data: null,
    }
  },
  mounted () {
    axios
      .get('https://data.gov.au/data/api/3/action/datastore_search_sql?sql=SELECT%20*%20from%20%229e26683b-6b30-424e-ace7-59047d811d1c%22')
      .then(response => (this.data = response.data.result.records))
  },
}
</script>

<style scoped lang="scss">

</style>
