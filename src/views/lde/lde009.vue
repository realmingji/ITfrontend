<route lang="yaml">
layout: DefaultLayout
meta:
  title: '프로젝트'
</route>

<template>
    <!-- 현재페이지 -->
    <subTitle :useButton="['Serach','Add','Close']" @click="clickEvent"/>

    <!-- 조회조건 -->
    <div class="searchCondition">
        <AppInput mode="input" v-model="username1" label="프로젝트" />
        <AppInput mode="select" :itmes="items" :value="value" :tilte="title" v-model="username2" label="상태" />

    </div>
    <!-- 그리드 -->
    <div class="grdWapper__full">
        <grid ref="grdPar" :fields="fields" :columns="columns" :rows="rows"/>
        <grid ref="grdChild" :fields="fields1" :columns="columns1" :rows="rows1"/>
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
          <option 
            v-for="opt in items" 
            :key="opt.value" 
            :value="opt.value"
          >
            {{ opt.title }}
          </option>
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

const items = reactive([
    {value:'aaa', title:'설문지수정중'},
    {value:'bbb', title:'오픈'},
])

const clickEvent =(e)=>{
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
  const grdPar = ref()
  const grdChild = ref()

  const grdContoll = ref()

  onMounted(() => {
    grdContoll.value = grdPar.value.realgrid;

    const grid = grdContoll.value.gridView;
    if (grid) {
      doubleClick()
    }

    // 로컬스토리지
    const savedData = localStorage.getItem('projectRows');
    if (savedData) {
      rows.value = JSON.parse(savedData);
    }

  });``
  
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
  if(popupTitle.value === '프로젝트 생성') {
      rows.value.push(data)
  } else if (popupTitle.value === '프로젝트 수정'){
    for(const i in data) {
      if (editingRowData.value[i] !== data[i]) {
        editingRowData.value[i] = data[i]
      }
    }``
  }
  const dp = grdContoll.value.dataProvider;
  if (dp) {
    dp.setRows([...rows.value]);
  }

  // 로컬스토리지  
  localStorage.setItem('projectRows', JSON.stringify(rows.value));

  open.value = false;
}

const Serach = () => {
  const dp = grdContoll.value.dataProvider;
  if (dp) {
    dp.setRows([...rows.value]); 
    console.log('그리드 데이터 조회 :', rows.value);
  }
}

const addPop = () => {
  formData.value = {
    ProjectName: '',
    Explanation: '',
    State: '설문지수정중',
    StartDate: '',
    EndDate: '',
    Remark: '',
    UseYn: '사용',
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
  const grid = grdContoll.value.gridView;
  const dp = grdContoll.value.dataProvider;
  const checked = grid.getCheckedRows();

  for (let i = checked.length - 1; i >= 0; i--) {
    const index = checked[i]
    rows.value.splice(index, 1)
  }

  dp.setRows([...rows.value]);
};


// 그리드 데이터
const fields =ref([ {
    fieldName: "ProjectName",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Explanation",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "State",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Model",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "StartDate",
    dataType: ValueType.DATETIME,
    datetimeFormat: "yyyy-MM-dd",
    amText: "오전",
    pmText: "오후",
  },
  {
    fieldName: "EndDate",
    dataType: ValueType.DATETIME,
    datetimeFormat: "yyyy-MM-dd",
    amText: "오전",
    pmText: "오후",
  },
  {
    fieldName: "Remark",
    dataType: ValueType.TEXT,
  },
])

const columns =ref([
  {
    name: "ProjectName",
    fieldName: "ProjectName",
    header: {
      text: "프로젝트명",
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
      text: "설명",
    },
    editor: {
      type: "multiline"
    },
      styleName: 'multiline-editor'
  },

  {
    name: "State",
    fieldName: "State",
    width: "150",
    editable: false,
    header: {
      text: "상태",
    },
    values: ["오픈", "설문지수정중"],
      labels: ["오픈", "설문지수정중"],
      lookupDisplay: true,
      editor: {
        type: "dropdown",
      },
    editButtonVisibility: "always"
  },

  {
    name: "Model",
    fieldName: "Model",
    width: "200",
    editable: false,
    header: {
      text: "진단모델",
    },
    styleName: "right-column",
  },

  {
    name: "StartDate",
    fieldName: "StartDate",
    width: "100",
    editable: false,
    header: {
      text: "시작일",
    },
    editor: {
      type: "date",
    },    
  },

  {
    name: "EndDate",
    fieldName: "EndDate",
    width: "100",
    editable: false,
    header: {
      text: "종료일",
    },
    editor: {
      type: "date",
    },
  }, 

  {
    name: "Remark",
    fieldName: "Remark",
    width: "100",
    editable: false,
    header: {
      text: "비고",
    },
    editor: {
      type: "multiline"
    },
      styleName: 'multiline-editor',
  },    
])


// 그리드 데이터

const fields1 =ref([ {
    fieldName: "Company",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Exponent",
    dataType: ValueType.TEXT,
  },
  {
    fieldName: "Industry",
    dataType: ValueType.TEXT,
  },
])

const columns1 =ref([
  {
    name: "Company",
    fieldName: "Company",
    header: {
      text: "회사",
      styleName: "orange-column",
    },
    width: "100",
    // editable: false,
    editor: { 
      type: "text" 
    }
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
  },

  {
    name: "Industry",
    fieldName: "Industry",
    width: "100",
    header: {
      text: "업종",
    },
    editor: {
      type: "text"
    },
  },

])

const rows =ref([])
const rows1 =ref([])
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