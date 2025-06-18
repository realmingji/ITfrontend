<route lang="yaml">
layout: DefaultLayout
meta:
  title: '회사'
</route>

<template>
    <!-- 현재페이지 -->
    <subTitle :useButton="['Serach','Add','Close']" @click="clickEvent"/>
    <!-- 조회조건 -->
    <div class="searchCondition">
        <AppInput mode="input" v-model="company" label="회사" />

    </div>
    <!-- 그리드 --> 
    <div class="grdWapper__full">
        <grid ref="grd" :fields="fields" :columns="columns" :rows="rows"/>
    </div>
  <ldePopUp 
      :open="open" 
      :title="popupTitle" 
      :formData="formData"
      @close="open = false" 
      @save="Save"
    >
      <div class="form-row">
        <label for="Company">회사</label>
        <input v-model="formData.Company" type="text" />
      </div>

      <div class="form-row">
        <label for="Adress">주소</label>
        <input v-model="formData.Adress" type="text"></input>
      </div>

      <div class="form-row">
        <label for="Exponent">대표자</label>
        <input v-model="formData.Exponent" type="text"></input>
      </div>

      <div class="form-row">
        <label for="Phone">연락처</label>
        <input v-model="formData.Phone" type="text"></input>
      </div>

      <div class="form-row">
        <label for="Business">업종</label>
        <input v-model="formData.Business" type="text"></input>
      </div>

      <div class="form-row">
        <label for="Remark">비고</label>
        <input v-model="formData.Remark" type="text"></input>
      </div>

      <div class="form-row">
        <label for="UseYn">사용여부</label>
          <select v-model="formData.UseYn">
            <option value="Y">사용</option>
            <option value="N">미사용</option>
          </select>
      </div>
    </ldePopUp>


</template>


<script setup>
import AppInput from '@/components/form/input.vue'
import subTitle from '@/components/subPage/tilte.vue'
import { reactive, ref,onMounted } from 'vue';
import grid from '@/components/grid/grid.vue'
import { ValueType } from "realgrid";
import api  from'@/api/api.js'
import ldePopUp from '@/views/lde/ldePopUp.vue'

const company = ref('')
const open = ref(false)
const popupTitle = ref('');

const formData = ref({})
const grd = ref()
const grdContoll = ref()

const editingRowData = ref(null);

const clickEvent =(e)=>{
    if(e.id ==='Save'){
        console.log('Save')
  
    }
      if(e.id ==='Serach'){
        console.log('Serach')
        Serach()
    }
      if(e.id ==='Del'){
        console.log('Del')
    }
      if(e.id==="Add"){
        addPop()
        setTimeout(() => {
        open.value = true;
      }, 0);
      }
      if(e.id==="Close"){
      }
}

onMounted(() => {
    grdContoll.value = grd.value.realgrid;

    const grid = grdContoll.value.gridView;
    if (grid) {
      doubleClick()
    }
});

const addPop = () => {
  formData.value = {
    Company: '',
    Adress: '',
    Exponent: ' ',
    Phone: '',
    Remark: '',
    State: '',
    UseYn: 'Y',
  };

  editingRowData.value = null;
  popupTitle.value = '회사정보 입력';
  open.value = true;
}

const updatePop = (rowData) => {
  editingRowData.value = rowData;
  popupTitle.value = '회사정보 수정';
  open.value = true;
}

const Serach = async () =>{
  const data = await api.get('/test/hi')
  console.log(data)
}

const doubleClick = () => {
  const grid = grdContoll.value.gridView;

  grid.onCellDblClicked = (grid, clickData) => {
    const index = clickData.dataRow;
    if (index < 0 || !rows.value[index]) {
      return;
    }  
    const row = rows.value[index];
    updatePop(row)
}};

const Save = (data) => {
  if(popupTitle.value === '회사정보 수정') {
        for(const i in data) {
      if (editingRowData.value[i] !== data[i]) {
        editingRowData.value[i] = data[i]
      }
    }
  } else if (popupTitle.value === '회사정보 입력') {
      rows.value.push(data)
  }
  const dp = grdContoll.value.dataProvider;
  if (dp) {
    dp.setRows([...rows.value]);
  }

  open.value = false;
};

// 그리드 데이터
const fields =ref([ {
    fieldName: "Code",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Company",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Exponent",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Adress",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Phone",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Business",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "UseYn",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Remark",
    dataType: ValueType.TEXT,
  },
])

const columns =ref([
    {
    name: "Code",
    fieldName: "Code",
    header: {
      text: "코드",
      styleName: "orange-column",
    },
    width: "100",
    editable: false,
  },
  {
    name: "Company",
    fieldName: "Company",
    width: "100",
    header: {
      text: "회사",
    },
    styleName: "left-column",
    editor: {
      type: "text"
    },
    editable: false,
  },
  {
    name: "Exponent",
    fieldName: "Exponent",
    width: "100",
    header: {
      text: "대표자",
    },
    editor: {
      type: "text"
    },
    editable: false,
  },
  {
    name: "Adress",
    fieldName: "Adress",
    width: "200",
    header: {
      text: "주소",
    },
    editor: {
      type: "text"
    },
    styleName: "right-column",
    editable: false,
  },
  {
    name: "Phone",
    fieldName: "Phone",
    width: "100",
    styleName: "right-column",
    header: {
      text: "전화번호",
    },
    editor: {
      type: "text"
    },
    editable: false,
  },
  {
    name: "Business",
    fieldName: "Business",
    width: "100",
    styleName: "right-column",
    header: {
      text: "업종",
    },
    editor: {
      type: "text"
    },
    editable: false,
  },
  {
    name: "UseYn",
    fieldName: "UseYn",
    width: "80",
    styleName: "right-column",
    header: {
      text: "사용여부",
    },
    editor: {
        type: "dropdown",
      },
    editable: false,
    values: ["Y", "N"],
    labels: ["사용", "미사용"],
    lookupDisplay: true,
    editButtonVisibility: "always"
  },
  {
    name: "Remark",
    fieldName: "Remark",
    width: "100",
    header: {
      text: "비고",
    },
    editor: {
      type: "text"
    },
    editable: false,
  },
])
const rows =ref([])
</script>

<style scoped>

.form-row {
  display: flex;
  align-items: flex-start;
  margin-bottom: 16px;
}

label {
  width: 100px;
  font-weight: bold;
}

input,
select,
textarea {
  flex: 1;
  padding: 6px 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

textarea {
  height: 150px;
  resize: vertical;
}

</style>