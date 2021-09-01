<template>
      <div class="request-form">
        <form class="form-section" @submit.prevent="handleSubmit">
            <div class="input-section">
            <select class="select-type" v-model="request_type">
                <option value="GET" selected>GET</option>
                <option value="POST">POST</option>
                <option value="PUT">PUT</option>
                <option value="PATCH">PATCH</option>
                <option value="DELETE">DELETE</option>
            </select>
            <input class="standard-input url-input" required type="URL" v-model="request_url" placeholder="https://www.example.com"/>
            <button type="submit" class="btn btn-primary submit-btn">SUBMIT</button>
            </div>
            <tab-section :params="params_array" @params-change="handleParamsChange" @delete-row="handleRowDeletion" @insert-row="handleRowInsertion" />
        </form>
      </div>
</template>
<script>
import TabSection from "./TabSection";
import axios from "axios";
const _ = require("lodash");

export default {
    name: 'request-info',
    data() {
        return {
            request_type: 'GET',
            request_url: "",
            params_array: [],
            paramsObject: {}
        };
    },
    components: {
        TabSection,
    },
    methods: {
        handleSubmit() {
            axios({
                url: this.request_url,
                method: this.request_type,
                params: {},
                headers: {}
            }).then(response => {
                console.log('Response', response);
            })
        },
        handleRowDeletion(index) {
            this.params_array.splice(index, 1);
            this.handleParamsChange(this.params_array);
        },
        handleRowInsertion() {
            this.params_array.push({key: '', value: ''});
        },
        handleParamsChange(newVal) {
            this.params_array = _.cloneDeep(newVal);
            this.paramsObject = {};
            this.params_array.forEach(param => {
                if(param.key !== '') this.paramsObject[param.key] = param.value;            
            });
        }
    }
}
</script>

<style lang="scss">
 .request-form {
     padding: 20px;
     .form-section {
         .input-section {
             display: flex;
             width: 100%;
             select {
                 width: 100px;
             }
             .url-input {
                margin-left: 5px;
                width: 85%;
             }
            .submit-btn {
                margin-left: 5px;
                width: 100px;
            }
         }
     }
 }
</style>