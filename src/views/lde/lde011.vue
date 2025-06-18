<route lang="yaml">
layout: DefaultLayout
meta:
  title: '설문대상'
</route>

<template>
    <!-- 현재페이지 -->
    <subTitle :useButton="['Serach']" @click="clickEvent"/>

    <!-- 조회조건 -->
    <div class="searchCondition">
        <AppInput mode="select" :itmes="items" :value="value" :tilte="title" v-model="username" label="프로젝트" />

    </div>
    <!-- 그리드 -->
    <div class="grdWapper__full">
        <grid ref="grdPar" :fields="fields" :columns="columns" :rows="rows"/>
        <grid ref="grdChild" :fields="fields1" :columns="columns1" :rows="rows"/>
    </div>
    <ldePopUp 
      :open="open" 
      :title="popupTitle" 
      :formData="formData"
      @close="open = false" 
      @save="Save"
    >
      <div class="form-row">
        <label for="ProjectName">프로젝트명</label>
        <input v-model="formData.ProjectName" type="text" />
      </div>

      <div class="form-row">
        <label for="Explanation">설명</label>
        <textarea v-model="formData.Explanation"></textarea>
      </div>

      <div class="form-row">
        <label for="State">상태</label>
        <select v-model="formData.State">
          <option value="오픈">오픈</option>
          <option value="설문지수정중">설문지수정중</option>
        </select>
      </div>

      <div class="form-row">
        <label for="StartDate">시작일</label>
        <input type="date" v-model="formData.StartDate" />
      </div>

      <div class="form-row">
        <label for="EndDate">종료일</label>
        <input type="date" v-model="formData.EndDate" />
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

const username = ref('')

const open = ref(false)

const popupTitle = ref('');
const editingRowData = ref(null);

const formData = ref({})

const grdPar = ref()
const grdChild = ref()

const grdContoll = ref()

const items = reactive([
    {value:'벨류', title:'타이틀'},
    {value:'벨류1', title:'타이틀1'},
    {value:'벨류2', title:'타이틀2'},
    {value:'벨류3', title:'타이틀3'},
])

const clickEvent =(e)=>{
    if(e.id ==='Save'){
        console.log('Save')
    }
      if(e.id ==='Serach'){
        console.log('Serach')
        Serach();
    }
      if(e.id ==='Del'){
        console.log('Del')
        delRow();
    }
      if (e.id === "Add") {
      open.value = false;
      addPop();
      setTimeout(() => {
        open.value = true;
      }, 0);
    }
      if(e.id==="Close"){
        console.log('닫기')
      }
}

  onMounted(() => {
    grdContoll.value = grdPar.value.realgrid;

    const grid = grdContoll.value.gridView;
    if (grid) {
      doubleClick()
    }
  });
  
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
  console.log("data : ", data)

  if(popupTitle.value === '프로젝트 생성') {
    console.log("프로젝트명 생성 :  ", popupTitle.value)
      rows.value.push(data)

  } else if (popupTitle.value === '프로젝트 수정'){
    console.log("프로젝트명 수정 : ", popupTitle.value)
    for(const i in data) {
      if (editingRowData.value[i] !== data[i]) {
        editingRowData.value[i] = data[i]
      }
    }
  }
  const dp = grdContoll.value.dataProvider;
  if (dp) {
    dp.setRows([...rows.value]);
  }
  open.value = false;
}

const Serach = () => {
  const dp = grdContoll.value.dataProvider;
  if (dp) {
    dp.setRows([...rows.value]); 
  }
}

const addPop = () => {
  formData.value = {
    ProjectName: '',
    Explanation: '',
    State: ' ',
    StartDate: '',
    EndDate: '',
    Remark: '',
    UseYn: 'Y',
  };

  editingRowData.value = null;
  popupTitle.value = '프로젝트 생성';
  open.value = true;
}

const updatePop = (rowData) => {
  editingRowData.value = rowData;
  popupTitle.value = '프로젝트 수정';
  open.value = true;
}

const delRow = () => {
  const grid = grdContoll.value.gridView
  grid.deleteSelection(true)

  const checked = gridView.getCheckedRows()
  
  const dp = grdContoll.value.dataProvider
  const index = checked.dataRow

  rows.value.splice(index, 1)
  console.log("index :  ", index)

  if (index === undefined || index < 0) {
  alert("삭제할 행을 선택해주세요.");
  return;
}
  
  dp.setRows([...rows.value])
  dp.removeRow(checked.dataRow)

  console.log("삭제 되었나? ")
}

// 그리드 데이터
const fields =ref([ {
    fieldName: "ProjectName",
    dataType: ValueType.TEXT,
  },

  {
    fieldName: "Explanation",
    dataType: ValueType.TEXT,
  },
])

const columns =ref([
  {
    name: "ProjectName",
    fieldName: "ProjectName",
    header: {
      text: "프로젝트",
      styleName: "orange-column",
    },
    width: "200",
    editable: false,
    editor: { 
      type: "text" 
    }
  },

  {
    name: "Explanation",
    fieldName: "Explanation",
    width: "300",
    editable: false,
    header: {
      text: "개요",
    },
    editor: {
      type: "multiline"
    },
      styleName: 'multiline-editor'
  },
])


// 그리드 데이터

const fields1 =ref([ {
    fieldName: "Code",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Company",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Adress",
    dataType: ValueType.TEXT,
  },
])

const columns1 =ref([
  {
        name: "Code",
        fieldName: "Code",
        header: {
        text: "코드",
        styleName: "orange-column",
        },
        width: "100",
        // editable: false,
        editor: { 
        type: "text" 
        }
  },

  {
        name: "Company",
        fieldName: "Company",
        header: {
        text: "회사",
        styleName: "orange-column",
        },
        width: "50",
        // editable: false,
        editor: { 
        type: "text" 
        }
  },

  {
        name: "Adress",
        fieldName: "Adress",
        width: "150",
        header: {
        text: "주소",
        },
        editor: {
        type: "text"
        },
        styleName: "right-column",
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