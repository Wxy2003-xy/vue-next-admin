<template lang="">
  <div enter-class="whole">
    <div class="alert-penal" id="banner">
      <div style="max-width: 600px">
        <el-alert title="Invalid Input!" type="error" show-icon />
      </div>
    </div>
    <div class="alert-penal" id="banner4">
      <div style="max-width: 600px">
        <el-alert title="Duplicate!" type="error" show-icon />
      </div>
    </div>
    <div class="alert-penal2" id="banner2">
      <div style="max-width: 600px">
        <el-alert title="Items deleted!" type="warning" show-icon />
      </div>
    </div>
    <div class="alert-penal3" id="banner3">
      <div style="max-width: 600px">
        <el-alert type="success" show-icon>
          {{successMessage}}
        </el-alert>
      </div>
    </div>
    <div enter-class="table">
        <el-table :data="tableData" style="width: 100%"  @selection-change="handleSelectionChange">
            <el-table-column data="tableData" type="selection"
                    width="55"/>

            <el-table-column type="index"property="index" label="Index" width="120">

            </el-table-column>
            <el-table-column property="name" label="Name" width="120" />
            <el-table-column
            property="score"
            label="Score"
            width="240"
            show-overflow-tooltip
            />
            <el-table-column property="selected" label="Selected" />
        </el-table>
    </div>
    <div enter-class="form">
        <el-form :inline="true" :model="newSubject" class="form-inline">
            <el-form-item label="Subject Name">
            <el-input v-model="newSubject.name" placeholder="subject name" clearable />
            </el-form-item>
            <el-form-item label="score">
                <div class="slider-demo-block">
                    <span class="demonstration">Default value</span>
                    <el-slider v-model="newSubject.score" />
                </div>
            </el-form-item>
            <el-form-item>
            <el-button type="primary" @click="onSubmit">Add</el-button>
            </el-form-item>
        </el-form>

        <el-form :inline="true" :model="modifiedSubject" class="form-inline">
            <el-form-item label="Index to Modify">
              <div class="flex flex-wrap gap-4 items-center">
                <el-select
                  v-model="placeHolderName"
                  placeholder="Select"
                  size="large"
                  style="width: 240px"
                  @change="selectToChange"
                >
                  <el-option
                    v-for="item in tableData"
                    :key="item.name"
                    :label="item.name"
                    :value="item.name"
                  />
                </el-select>
              </div>
            </el-form-item>
            <el-form-item label="New Subject Name">
            <el-input v-model="modifiedSubject.name" placeholder="new subject name" clearable />
            </el-form-item>
            <el-form-item label="new score">
                <div class="slider-demo-block">
                    <span class="demonstration">Default value</span>
                    <el-slider v-model="modifiedSubject.score" />
                </div>
            </el-form-item>
            <el-form-item>
            <el-button type="primary" @click="onSubmitChange">Modify</el-button>
            </el-form-item>
        </el-form>
    </div>
    <div enter-class="stats">
      <el-row>
        <el-col :span="6">
          <el-statistic title="Total" :value="sum" />
        </el-col>
        <el-col :span="6">
          <el-statistic title="Average" :value="average">
            <template #suffix>
              <el-icon style="vertical-align: -0.125em">
                <ChatLineRound />
              </el-icon>
            </template>
          </el-statistic>
        </el-col>
        <el-col :span="6">
          <el-button type="primary" :icon="Delete" @click="deleteSelected"/>
        </el-col>
      </el-row>
    </div>
  </div>
    
</template>
<script setup lang="ts">
import { reactive, watch, ref } from 'vue'
import { ElTable } from 'element-plus'
import { Delete } from '@element-plus/icons-vue'

interface Subject {
  name: string
  score: number
  selected: boolean
}



const tableData: Subject[] = reactive([
  {
    name: 'subject 1',
    score: 100,
    selected: false
  },
  {
    name: 'subject 2',
    score: 60,
    selected: false
  },
  {
    name: 'subject 3',
    score: 80,
    selected: false
  },
])

const createFormItem = (form: Subject, reference: Subject) => {
    reference.name = form.name
    reference.score = form.score
    reference.selected = form.selected
} 
const delay = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));
var successMessage = ref("Successfully added new subject!")
const showErrorInvalid = async () => {
    var banner = document.getElementById("banner");
    if (banner != null) {
        banner.style.display = "block";
        await delay(1000)
        banner.style.display = "none";
    }
}

const showErrorDuplicate = async () => {
    var banner = document.getElementById("banner4");
    if (banner != null) {
        banner.style.display = "block";
        await delay(1000)
        banner.style.display = "none";
    }
}

const showWarning = async () => {
  var banner = document.getElementById("banner2");
    if (banner != null) {
        banner.style.display = "block";
        await delay(1000)
        banner.style.display = "none";
    }
}

const showSuccessBanner = async () => {
  var banner = document.getElementById("banner3");
    if (banner != null) {
        banner.style.display = "block";
        await delay(1000)
        banner.style.display = "none";
    }
}
const onSubmit = async () => {
    if (!newSubject.name || newSubject.score < 0) {
        showErrorInvalid();
        return;
    }
    if (tableData.find(x => x.name === newSubject.name)) {
      showErrorDuplicate();
      return 
    }
    const newItem: Subject = {
        name: '',
        score: -1,
        selected: false,
    }
    createFormItem(newSubject, newItem);
    tableData.push(newItem);
    showSuccessBanner();
    clearFormItem()
}
var indexToChange = ref(0)
const onSubmitChange = async () => {
  if (modifiedSubject.name === undefined || modifiedSubject.name === "") {
    showErrorInvalid();
    return;
  }
  if (tableData.find(x => x.name === modifiedSubject.name)) {
      showErrorDuplicate();
      return 
    }
  successMessage.value = "Successfully updated subject info!"
  const newItem: Subject = {
    name: modifiedSubject.name,
    score: modifiedSubject.score,
    selected: modifiedSubject.selected,
  };
  tableData.splice(indexToChange.value, 1)
  tableData.push(newItem);
  showSuccessBanner();
  successMessage.value = "Successfully added new subject!"
}

const clearFormItem = () => {
    newSubject.name = '',
    newSubject.score = -1,
    newSubject.selected = false;
}

const newSubject = reactive({
  name: '',
  score: -1,
  selected: false
})

const modifiedSubject = reactive({
  name: '',
  score: -1,
  selected: false
})

var tmp = reactive({
    name: '',
    score: -1,
    selected: false
})

const selectedRows = ref<Subject>()

var sum = ref(tableData.map(e => e.score).reduce((x, y) => x + y))
var average = ref(tableData.map(e => e.score).reduce((x, y) => x + y) / tableData.length)
const computeSum = (rows: Subject[]) => {
  if (rows == undefined || rows.length < 1) {
    sum.value = tableData.map(e => e.score).reduce((x, y) => x + y)
  } else {
    sum.value = rows.map(r => r.score).reduce((x, y) => x + y);
  }
}

const computeAverage = (rows: Subject[]) => {
  if (rows == undefined || rows.length < 1) {
    average.value = tableData.map(e => e.score).reduce((x, y) => x + y) / tableData.length
    return;
  }
  if (sum.value != 0) {
    average.value = sum.value / (rows.length)
  }
}

const toggleSelected = (rows: Subject[]) => {
  if (rows == undefined || tableData == undefined) {
    return;
  }
  for (var i = 0; i < tableData.length; i++) {
    if (tableData.at(i) == undefined) {
      throw Error("undefined object at index " + i);
    }
    tableData.at(i).selected = false;
  }
  if (rows.length > 0) {
    rows.map(r => {
      r.selected = true;
    })
  }
}

const handleSelectionChange = (rows: Subject[]) => {
    selectedRows.value = rows;
    toggleSelected(rows);
    computeSum(rows);
    computeAverage(rows);
}

const deleteSelected = () => {
  selectedRows.value.map(r => {
    tableData.splice(r, 1)
    showWarning();
  })
}

var placeHolderName = ref<String>('select')

const selectToChange = (name:string) => {
  indexToChange.value = tableData.findIndex(x => x.name == name)

  if (name == undefined || name == "" || tableData.find(x => x.name === name) == undefined) {
    showErrorInvalid()
    return;
  }
}

watch (
    newSubject,
    (newItem) =>{
        Object.assign(tmp, newItem)
    }
);


</script>
<style lang="css" scoped>
  .whole {
  }
  .form {
      display: flex
  }
  .slider-demo-block {
    max-width: 600px;
    display: flex;
    align-items: center;
  }
  .slider-demo-block .el-slider {
    margin-top: 0;
    margin-left: 12px;
  }
  .slider-demo-block .demonstration {
    font-size: 14px;
    color: var(--el-text-color-secondary);
    line-height: 44px;
    flex: 1;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    margin-bottom: 0;
  }
  .slider-demo-block .demonstration + .el-slider {
    flex: 0 0 70%;
  }
  .demo-form-inline .el-input {
    --el-input-width: 220px;
  }

  .demo-form-inline .el-select {
    --el-select-width: 220px;
  }
  .stats {
    padding: 10%;
  }

.alert-penal {
    align-content: center;
    position: absolute; 
    left: 45%;
    right: 45%;
    top: 5%;
    z-index: 1000;
    display: none;
}
.alert-penal2 {
    align-content: center;
    position: absolute; 
    left: 45%;
    right: 45%;
    top: 5%;
    z-index: 1000;
    display: none;
}
.alert-penal3 {
    align-content: center;
    position: absolute; 
    left: 45%;
    right: 45%;
    top: 5%;
    z-index: 1000;
    display: none;
}
.alert-penal4 {
    align-content: center;
    position: absolute; 
    left: 45%;
    right: 45%;
    top: 5%;
    z-index: 1000;
    display: none;
}
.el-alert {
    justify-self: center;
    min-width: 200px;
    margin: 20px 0 0;
}
.el-alert:first-child {
  margin: 0;
}
</style>