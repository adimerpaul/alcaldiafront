<template>
  <q-page class="text-center bg-blue-grey-1">
    <div class="row">
      <div class="col-12 q-mt-sm q-px-md">
        <h3 class="text-h6 text-weight-bolder">CONSULTA</h3>
<!--        <div class="q-pa-md" >-->
<!--          <q-input-->
<!--            ref="input"-->
<!--            filled-->
<!--            v-model="carnet"-->
<!--            label="Carnet de identidad"-->
<!--            :rules="[val => !!val || 'Este campo es requerido']"-->
<!--          />-->
<!--        </div>-->
        <form @submit.prevent="simulateSubmit" class="q-pb-xs">
          <!-- a simple text field watching for the enter key release -->
          <q-input
            filled
            color="teal"
            label="Numero inmueble"
            hint="Porfavor colocar el numero del inmueble registrado"
            :rules="[val => !!val || 'Este campo es requerido',val => val.length>5  || 'Debe ser mayor a 5 digitos']"
            v-model="numinmueble"
            required
          />

          <!--
            A button with v-model set to submit.
            v-model scope variable must be a strict Boolean
          -->
          <div class="row justify-end">
            <q-btn
              type="submit"
              round
              :loading="submitting"
              icon="send"
              class="q-mt-md"
              color="teal"
            >
              <template v-slot:loading>
                <q-spinner-facebook />
              </template>
            </q-btn>
          </div>
        </form>
        <q-table
          v-if="table"
          :title=total
          :data="data"
          :columns="columns"
          row-key="name"
        />
      </div>
    </div>


  </q-page>
</template>

<script>
import { api } from 'boot/axios'
export default {
  name: 'PageIndex',
  created() {
    // console.log(process.env.URL)
  },
  data () {
    return {
      numinmueble:53834601,
      table:false,
      test: '',
      submitting: false,
      columns: [
        {
          name: 'gestion',
          required: true,
          label: 'Gestion',
          align: 'left',
          field: row => row.gestion,
          // format: val => `${val}`,
          sortable: true
        },
        {
          name: 'concepto',
          required: true,
          label: 'Concepto',
          align: 'center',
          field: row => row.concepto,
          // format: val => `${val}`,
          sortable: true
        },
        {
          name: 'monto',
          required: true,
          label: 'Monto Bs.',
          align: 'right',
          field: row => row.monto,
          // format: val => `${val}`,
          sortable: true
        },
        // { name: 'calories', align: 'center', label: 'Calories', field: 'calories', sortable: true },
        // { name: 'fat', label: 'Fat (g)', field: 'fat', sortable: true },
        // { name: 'carbs', label: 'Carbs (g)', field: 'carbs' },
        // { name: 'protein', label: 'Protein (g)', field: 'protein' },
        // { name: 'sodium', label: 'Sodium (mg)', field: 'sodium' },
        // { name: 'calcium', label: 'Calcium (%)', field: 'calcium', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) },
        // { name: 'iron', label: 'Iron (%)', field: 'iron', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) }
      ],
      data: [
        {
          gestion: 2018,
          concepto: 'TASAS',
          monto: 6.0,
        },
        {
          gestion: 2019,
          concepto: 'TASAS',
          monto: 100.0,
        },
        {
          gestion: 2020,
          concepto: 'TASAS',
          monto: 120.0,
        },
      ]
    }
  },
  methods: {
    simulateSubmit () {
      this.table=false;
      this.submitting = true
      // Simulating a delay here.
      // When we are done, we reset "submitting"
      // Boolean to false to restore the
      // initial state.
      // setTimeout(() => {
      //   // delay simulated, we are done,
      //   // now restoring submit to its initial state
      //   this.submitting = false;
      //   this.table=true;
      // }, 3000)
      this.data=[];
      api.post('/api/tasas',{cantidad:this.numinmueble})
        .then((response) => {
          // this.data = response.data
          // console.log(response.data);
          if (response.data.length>0){
            response.data.forEach(re=>{
              // console.log(re);
              re.forEach(r=>{
                // console.log(r);
                this.data.push({
                  gestion: r.gest,
                  concepto: 'TASAS',
                  monto: r.Pagado,
                })
              })
            })
          }
          this.table=true;
          this.submitting = false;
        })
        .catch(() => {
          this.table=false;
          this.submitting = false;
          this.$q.notify({
            color: 'negative',
            position: 'top',
            message: 'No tiene conexion a internet',
            icon: 'report_problem'
          })
        })
    }
  },
  computed:{
    total: function(){

      var sum = 0;
      this.data.forEach(e => {
        sum += parseFloat(e.monto);
      });
      return 'Total pagado '+sum

    }
  }
}
</script>
