<route lang="yaml">
layout: DefaultLayout
meta:
  title: '회원'
</route>

<template>
    <!-- 현재페이지 -->
    <subTitle :useButton="['Serach','Add','Del']" @click="clickEvent"/>

    <!-- 조회조건 -->
    <div class="searchCondition">
        <AppInput mode="input" v-model="username1" label="회사" />
        <AppInput mode="input" v-model="username2" label="회원" />
        
    </div>
    <!-- 그리드 -->
    <div class="grdWapper__full">
        <grid ref="grdPar" :fields="fields" :columns="columns" :rows="rows"/>
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
        <label for="Dept">부서</label>
        <input v-model="formData.Dept"></input>
      </div>

      <div class="form-row">
        <label for="Type">속성</label>
        <select v-model="formData.UseYn">
          <option value="컨설턴트">컨설턴트</option>
          <option value="BM">BM</option>
          <option value="IT담당">IT담당</option>
          <option value="현업">현업</option>
        </select>
      </div>

      <div class="form-row">
        <label for="Name">이름</label>
        <input v-model="formData.Name"></input>
      </div>

      <div class="form-row">
        <label for="Email">이메일</label>
        <input v-model="formData.Email"></input>
      </div>

      <div class="form-row">
        <label for="Task">업무개요</label>
        <textarea v-model="formData.Task"></textarea>
      </div>

      <div class="form-row">
        <label for="Solution">솔루션개요</label>
        <textarea v-model="formData.Solution"></textarea>
      </div>

      <div class="form-row">
        <label for="Type">구분</label>
        <select v-model="formData.UseYn">
          <option value="관리자">관리자</option>
          <option value="사용자">사용자</option>
        </select>
      </div>

      <div class="form-row">
        <label for="UseYn">사용여부</label>
        <select v-model="formData.UseYn">
          <option value="사용">사용</option>
          <option value="미사용">미사용</option>
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

const username1 = ref('')
const username2 = ref('')

const open = ref(false)

const popupTitle = ref('');
const editingRowData = ref(null);

const formData = ref({})

const grdPar = ref()
const grdContoll = ref()

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
    fieldName: "Auth",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Company",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Dept",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Attribute",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Name",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Email",
    dataType: ValueType.TEXT,
  },  
  {
    fieldName: "Task",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Solution",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "UseYn",
    dataType: ValueType.TEXT,
  },
])

const columns =ref([
  {
    name: "Auth",
    fieldName: "ProjectName",
    header: {
      text: "권한",
      styleName: "orange-column",
    },
    width: "50",
    editable: false,
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
    width: "100",
    editable: false,
    editor: { 
      type: "text" 
    }
  },

   {
    name: "Dept",
    fieldName: "Dept",
    header: {
      text: "부서",
      styleName: "orange-column",
    },
    width: "100",
    editable: false,
    editor: { 
      type: "text" 
    }
  },

  {
    name: "Attribute",
    fieldName: "Attribute",
    width: "100",
    editable: false,
    header: {
      text: "속성",
    },
   editor: {
        type: "dropdown",
    },
    values: ["컨설턴트", "BM", "IT담당", "현업"],
    labels: ["컨설턴트", "BM", "IT담당", "현업"],
    lookupDisplay: true,
    editButtonVisibility: "always"
  },

  {
    name: "Name",
    fieldName: "Name",
    width: "50",
    editable: false,
    header: {
      text: "이름",
    },
    editor: {
      type: "text",
    },    
  },

  {
    name: "Email",
    fieldName: "Email",
    width: "150",
    editable: false,
    header: {
      text: "이메일",
    },
    editor: {
      type: "text",
    },    
  },

  {
    name: "Task",
    fieldName: "Task",
    width: "50",
    editable: false,
    header: {
      text: "업무개요",
    },
    editor: {
      type: "text",
    },    
  },

  {
    name: "Solution",
    fieldName: "Solution",
    width: "50",
    editable: false,
    header: {
      text: "솔루션개요",
    },
    editor: {
      type: "text",
    },    
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
    values: ["사용", "미사용"],
    labels: ["사용", "미사용"],
    lookupDisplay: true,
    editButtonVisibility: "always"
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