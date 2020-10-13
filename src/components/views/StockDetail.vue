<template>
  <!-- Content Wrapper. Contains page content -->
  <!-- Main content -->
  <section class="content">
    <h2>{{headerValue}}</h2>
    <!-- SELECT2 EXAMPLE -->
    <div class="row">
      <div class="col-md-7">
        <div class="box box-info">
          <div class="box-header with-border">
            <h3 class="box-title">Stock Details Of {{headerValue}}</h3>
            <div class="box-tools pull-right">
              <button class="btn btn-box-tool" data-widget="collapse"><i class="fa fa-minus"></i></button>
              <button class="btn btn-box-tool" data-widget="remove"><i class="fa fa-times"></i></button>
            </div>
          </div><!-- /.box-header -->
          <div class="box-body">
            <div class="table-responsive">
              <table class="table no-margin">
                <thead>
                <tr>
                  <th class="text-center">Key</th>
                  <th class="text-center">Value</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(item, key, index) in formValue">
                  <td class="text-center"><b style="color: #0d6aad">{{key}}</b></td>
                  <td class="text-center">{{item}}</td>
                </tr>
                </tbody>
              </table>
            </div><!-- /.table-responsive -->
          </div><!-- /.box-body -->
          <div class="box-footer clearfix">

          </div><!-- /.box-footer -->
        </div>
      </div>
      <div class="col-md-5">
        <div class="box box-primary">
          <div class="box-header with-border">
            <h3 class="box-title">Stock Entry</h3>
            <div class="box-tools pull-right">
              <button class="btn btn-box-tool" data-widget="collapse"><i class="fa fa-minus"></i></button>
              <button class="btn btn-box-tool" data-widget="remove"><i class="fa fa-times"></i></button>
            </div>
          </div><!-- /.box-header -->
          <div class="box-body">
            <div class="panel-body ">
              <!--Only code you need is this label-->
              <label class="switch ">
                <input type="checkbox" class="" @click="toggleCheckbox">
                <div class="slider round"></div>
              </label>
              <label class="customLabel">Refresh Window</label>
            </div>
            <div class="form-group" v-if="formValue.JUMP !== undefined">
              <label>Jump</label>
              <div class="input-group">
                <button v-for="i in jump" v-model="jump" style="margin-left: 5px;"
                        :class="{'btn-btn-success' : jumpNew !== i, 'btn btn-primary' : jumpNew === i}" value="i"
                        @click="addJumpValue('JUMP',i)"> {{i}}
                </button>
              </div><!-- /.input group -->
            </div><!-- /.form group -->

            <div class="form-group">

              <label>Profit Override</label>
              <div class="input-group">
                <button v-for="i in profitOverride" style="margin-left: 5px;"
                        :class="{'btn btn-danger' : profitOverrideNew !== i, 'btn btn-primary' : profitOverrideNew === i}"
                        @click="addPropertyOverrideValue('profitOverride',i)"> {{i}}
                </button>
              </div>
              <!-- /.input group -->
            </div>
            <div class="box-footer clearfix">
              <button class="btn btn-sm btn-success btn-flat pull-left" @click="onSubmit(headerValue)">Update</button>
            </div><!-- /.box-footer -->
            <!-- /.form group -->
            <!--<div class="form-group" v-for="(item, key, index) in formValue">
              <label>{{key}} - {{item}}</label>
            </div>-->
          </div><!-- /.box-body -->
          <!-- /.box-footer -->
        </div><!-- /.box -->

      </div>
    </div>


  </section><!-- /.content -->

</template>
<script>
  // Require needed datatables modules
  import axios from 'axios'
  // const url = 'https://dining-out-dfec2.firebaseio.com/ROBOALGO108/.json'
  const url = 'http://localhost:9000/api/v1/'

  require('datatables.net')
  require('datatables.net-bs')

  export default {
    name: 'StockDetail',

    data() {
      return {
        headerValue: '',
        formValue: {},
        jump: [0, 1, 2, 3, 4, 5, 7, 8, 9],
        profitOverride: ['YES', 'NO', 'BUY', 'SELL'],
        jumpNew: '',
        profitOverrideNew: '',
        formSubmit: {},
        checkbox: false,
        t: ''
      }
    },
    mounted() {
      this.fetchData()
    },
    watch: {
      // call again the method if the route changes
      '$route': 'fetchData'
    },
    methods: {
      toggleCheckbox() {
        this.checkbox = !this.checkbox
        this.$emit('setCheckboxVal', this.checkbox)
        this.refreshWindow()
      },

      /* checkRefreshWindow() {
        if (this.checkbox) {
          this.refreshWindow()
        }
      }, */

      /* stopRefreshWindow() {
        console.log('this is the stoprefreshWindow', this.checkbox)
        window.clearInterval()
      }, */

      refreshWindow() {
        console.log('this is the refreshWindow', this.checkbox)
        if (this.checkbox) {
          this.t = setInterval(() => {
            this.fetchData()
          }, 5000)
        } else {
          console.log('this is the clear interval', this.t)
          clearInterval(this.t)
        }
      },

      fetchData() {
        this.headerValue = this.$route.params.item
        if (this.$route.params.item !== undefined) {
          this.getData(this.$route.params.item)
        }
      },

      getData(value) {
        console.log('this is the value of stock details', value)
        axios.get(url.concat(value)).then(response => {
          this.filterData(response.data, value)
        })
      },

      filterData(data, value) {
        console.log('this is the data', data)
        console.log('this is the value', value)
        // this.formValue = data[value]
        this.formValue = data
        this.valueForInput(this.formValue)
      },

      valueForInput(value) {
        console.log('this is the value', value)
        this.jumpNew = value.JUMP
        this.profitOverrideNew = value.profitOverride
      },

      addJumpValue(key, value) {
        this.formSubmit[key] = value
        this.jumpNew = value
      },
      addPropertyOverrideValue(key, value) {
        this.formSubmit[key] = value
        this.profitOverrideNew = value
      },

      alertDisplay() {
        this.$swal.fire({
          position: 'top-end',
          icon: 'success',
          title: 'Your work has been saved',
          showConfirmButton: false,
          timer: 1500
        })
      },

      onSubmit(value) {
        // axios.patch(`https://dining-out-dfec2.firebaseio.com/ROBOALGO108/${this.headerValue}/.json`,
        axios.post(url.concat(this.headerValue),
          this.formSubmit,
          {
            headers: {'Content-type': 'application/json'}
          })
          .then(response => {
            this.alertDisplay()
            this.fetchData()
          })
          .catch(error => {
            alert(error)
          })
      }
    }
  }
</script>

<style>
  /* Using the bootstrap style, but overriding the font to not draw in
     the Glyphicons Halflings font as an additional requirement for sorting icons.

     An alternative to the solution active below is to use the jquery style
     which uses images, but the color on the images does not match adminlte.

  @import url('/static/js/plugins/datatables/jquery.dataTables.min.css');
  */

  @import url('/static/js/plugins/datatables/dataTables.bootstrap.css');

  table.dataTable thead .sorting:after,
  table.dataTable thead .sorting_asc:after,
  table.dataTable thead .sorting_desc:after {
    font-family: 'FontAwesome';
  }

  table.dataTable thead .sorting:after {
    content: '\f0dc';
  }

  table.dataTable thead .sorting_asc:after {
    content: '\f0dd';
  }

  table.dataTable thead .sorting_desc:after {
    content: '\f0de';
  }

  .switch {
    position: relative;
    display: inline-block;
    width: 60px;
    height: 34px;
  }

  .switch input {
    display: none;
  }

  .slider {
    position: absolute;
    cursor: pointer;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: #ccc;
    -webkit-transition: 0.4s;
    transition: 0.4s;
  }

  .slider:before {
    position: absolute;
    content: "";
    height: 26px;
    width: 26px;
    left: 4px;
    bottom: 4px;
    background-color: white;
    -webkit-transition: 0.4s;
    transition: 0.4s;
  }

  input:checked + .slider {
    background-color: #101010;
  }

  input:focus + .slider {
    box-shadow: 0 0 1px #101010;
  }

  input:checked + .slider:before {
    -webkit-transform: translateX(26px);
    -ms-transform: translateX(26px);
    transform: translateX(26px);
  }

  .slider.round {
    border-radius: 34px;
  }

  .slider.round:before {
    border-radius: 50%;
  }
  .customLabel {
    top: 15px;
    margin: 6px;
  }
</style>
