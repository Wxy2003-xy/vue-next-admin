<template>
    <div class="whole">
        <div class="up">
            <el-form :inline="true" :model="formInline" class="demo-form-inline"@submit="onSubmit">
                <el-form-item label="Approved by">
                <el-input v-model="formInline.user" placeholder="Approved by" clearable />
                </el-form-item>
                <el-form-item label="Activity zone">
                <el-select
                    v-model="formInline.region"
                    placeholder="Activity zone"
                    clearable
                >
                    <el-option label="Zone one" value="shanghai" />
                    <el-option label="Zone two" value="beijing" />
                </el-select>
                </el-form-item>
                <el-form-item label="Activity time">
                <el-date-picker
                    v-model="formInline.date"
                    type="date"
                    placeholder="Pick a date"
                    clearable
                />
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit">Query</el-button>
                </el-form-item>
            </el-form>
        </div>
        <div class="down">
            <div class="lower-left">
                <p>User: {{submittedValue.user}}</p>
                <p>Region: {{submittedValue.region}}</p>
                <p>Date: {{submittedValue.date}}</p>
            </div>
            <div class="lower-right">
                <el-space class="list">
                    <el-card v-for="i in formDataList" :key="i" class="box-card">
                    <template #header>
                        <div class="card-header">
                        <span>{{i.user}}</span>
                        <el-button class="button" text>Operation button</el-button>
                        </div>
                    </template>
                    <div v-for="o in 1" :key="o" class="text item">
                        {{ 'List item ' + o + i.region + '\n' + i.date}}
                    </div>
                    <div class="demo-rate-block">
                        <el-rate v-model="i.value" :colors="colors" />
                    </div>
                    </el-card>
                </el-space>

            </div>
            <div>
                
            </div>
        </div>
    </div>
</template>

<script setup lang="ts" name="tmp">
import { reactive, watch, ref } from 'vue'
interface formData {
    user: string;
    region: string;
    date: string;
    value: number;
}
const colors = ref(['#99A9BF', '#F7BA2A', '#FF9900'])
const formInline = reactive({
  user: '',
  region: '',
  date: '',
  value: 0,
})

var submittedValue = reactive<formData>({
    user: 'placeholder',
    region: 'placeholder',
    date: 'placeholder',
    value: 0,
})

const formDataList = reactive<formData[]>([]);

const createFormItem = (form: formData, reference: formData) => {
    reference.user = form.user;
    reference.region = form.region;
    reference.date = form.date;
    reference.value = form.value;
} 
const clearForm = (form: formData) => {
    form.user = '',
    form.region = '',
    form.date = '',
    form.value = 0
}

const onSubmit = () => {
  console.log('submit!')
  console.log(formInline)
    createFormItem(formInline, submittedValue);
    const newItem: formData = {
        user: '',
        region: '',
        date: '',
        value: formInline.value,
    }
    createFormItem(formInline, newItem);
    formDataList.push(newItem);
    clearForm(formInline);
    console.log(formDataList);
}

watch(
	() => formInline,
	(form) => {
        Object.assign(submittedValue, form);
	},
	{
		deep: true,
	}
);
</script>

<style scoped lang="scss">
    .whole {
        padding: 1em;
    }
    .up {
        
    }
    .down {
        display: flexbox;
        flex-direction: row;
    }
    .lower-left {
        background-color: aliceblue;
        padding: 1em;
        
    }
    .lower-right {
        background-color: antiquewhite;
        padding: 1em;

        /* Flexbox setup */
        display: flex;
        flex-wrap: wrap; /* Allow items to wrap to the next line */
        justify-content: space-evenly; /* Space items evenly, including at the edges */
        align-items: center; /* Vertically center items */
        gap: 1em; /* Add spacing between items */
        flex-wrap: wrap;
    }

    .box-card {
        width: 20em; /* Ensure consistent width for cards */
        margin: 0.5em; /* Add some margin for spacing (optional) */
        height: 18em;
    }
    .list {
        display: flex;
        flex-wrap: wrap; /* Allow items to wrap to the next line */
        justify-content: space-evenly; /* Space items evenly, including at the edges */
        align-items: start; /* Vertically center items */
    }
    .demo-rate-block {
        padding: 30px 0;
        text-align: center;
        border-right: solid 1px var(--el-border-color);
        display: inline-block;
        width: 49%;
        box-sizing: border-box;
    }
    .demo-rate-block:last-child {
        border-right: none; 
    }
    .demo-rate-block .demonstration {
        display: block;
        color: var(--el-text-color-secondary);
        font-size: 14px;
        margin-bottom: 20px;
    }
</style>
