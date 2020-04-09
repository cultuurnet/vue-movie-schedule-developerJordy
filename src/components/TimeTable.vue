<template>

  <div class="timetable-container">

    <table class="time-table-container">


      <!-- HEADER ROW -->
      <tr>
        <th></th>
        <th v-for="timestamp_index in 7" :key="timestamp_index">
          t{{timestamp_index}}
          <i v-on:click="copyColumn(timestamp_index)" class="fa fa-files-o" aria-hidden="true" title="copy column"></i>
          <i v-on:click="pasteColumn(timestamp_index)" class="fa fa-clipboard" aria-hidden="true" title="paste column"></i>
        </th>
      </tr>

      <!-- ROWs PER DAY OF WEEK -->
      <tr v-for="(day, date_index) in weeklyTimeStamps"
          v-bind:key="`${date_index} - ${day}`">

        <span>{{day.date}}</span>
        <i v-on:click="copyRow(date_index)" class="fa fa-files-o" aria-hidden="true" title="copy row"></i>
        <i v-on:click="pasteRow(date_index)" class="fa fa-clipboard" aria-hidden="true" title="paste row"></i>

        <!-- COLUMNs PER TIMESTAMP OF DAY -->
        <th v-for="(timestamp, t_index) in day.todaysTimeStamps"
            v-bind:key="`${t_index} - ${timestamp}`">
            <input  v-bind:value="weeklyTimeStamps[date_index].todaysTimeStamps[t_index]"
                    v-on:input="weeklyTimeStamps[date_index].todaysTimeStamps[t_index] = $event.target.value"
                    @paste="onPaste($event, date_index, t_index, )">
        </th>
      </tr>


    </table>

  </div>

</template>



<script>
  export default {
    name: 'TimeTable',
    props: {
      weeklyTimeStamps: {
        type: Array,
        required: false,
        default: () => [
          { date: '23/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '24/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '25/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '26/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '27/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '28/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '29/03', todaysTimeStamps: ['', '', '', '', '', '', '']},
          { date: '30/03', todaysTimeStamps: ['', '', '', '', '', '', '']}
        ]
      }
    },
    data: function() {
      return {
        copiedValues: {
          type: Array
        },
        copiedMode: {
          type: String
        }
      }
    },
    methods: {

      copyRow: function(date_index) {
        this.copiedValues = this.weeklyTimeStamps[date_index].todaysTimeStamps;
        this.copiedMode = "ROW";
      },
      pasteRow: function(date_index) {
        if(this.copiedMode == "ROW") {
          this.weeklyTimeStamps[date_index].todaysTimeStamps = this.copiedValues;
        }
      },

      copyColumn: function(timestamp_index) {
        this.copiedValues = this.weeklyTimeStamps.map(day => day.todaysTimeStamps[timestamp_index-1]);
        this.copiedMode = "COLUMN";
      },
      pasteColumn: function(timestamp_index) {
        if(this.copiedMode == "COLUMN") {
          this.weeklyTimeStamps.forEach((day, index) =>
            this.$set(
              this.weeklyTimeStamps[index].todaysTimeStamps,
              timestamp_index-1,
              this.copiedValues[index]
            ));
        }
      },

      onPaste(evt, date_index, timestamp_index) {
        evt.preventDefault();
        if(this.copiedMode == "ROW") {
          this.pasteRow(date_index);
        }
        else if(this.copiedMode == "COLUMN") {
          this.pasteColumn(timestamp_index+1);
        }
      }

    }
  }
</script>




<style scoped>
  table, td {
    border: 1px solid black;
    padding: 5px;
  }
  th {
    border: 0;
    padding: 0 5px;
  }

  .time-table-container {
    width:100%;
    background-color: #efefef;
    border-spacing: 15px;
  }

  input {
    max-width: 7vw;
  }

  .fa-files-o {
    margin: 0 5px;
  }

</style>
