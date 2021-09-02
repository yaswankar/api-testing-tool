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
            <tab-section :params="params_array" :headers="headers_array" @attrs-change="handleAttrsChange" @delete-row="handleRowDeletion" @insert-row="handleRowInsertion" />
        </form>
        <response-section :headersContent="responseHeaders" :responseDetails="responseDetails"/>
      </div>
</template>
<script>
import TabSection from "./TabSection";
import ResponseSection from "./ResponseSection";
import axios from "axios";
// import prettyBytes from 'pretty-bytes'
const _ = require("lodash");

export default {
    name: 'request-info',
    data() {
        return {
            request_type: 'GET',
            request_url: "",
            params_array: [],
            headers_array: [],
            paramsObject: {},
            headersObject: {},
            responseHeaders: {},
            responseDetails: {}
        };
    },
    components: {
        TabSection,
        ResponseSection
    },
    mounted() {
        // axios.interceptors.request.use(request => {
        //     request.customData = request.customeData || {};
        //     request.customData.startTime = new Date().getTime();
        //     return request;
        // });
        // axios.interceptors.response.use(e => {
        //     Promise.reject(this.updateEndTime(e.response));
        // })
    },
    methods: {
        handleSubmit() {
            axios({
                url: this.request_url,
                method: this.request_type,
                params: this.paramsObject,
                headers: this.headersObject
            }).then(response => {
                console.log('Response', response);
                // this.updateResponseEditor(response.data);
                this.updateResponseHeaders(response.headers);
                // this.updateResponseDetails(response);
            }).catch(e => e.response);
        },
        // updateEndTime(response) {
        //     response.customData = response.customData || {}
        //     response.customData.time =
        //         new Date().getTime() - response.config.customData.startTime
        //     return response;
        // },
        updateResponseHeaders(headers) {
            this.responseHeaders = headers;
        },
        // updateResponseDetails(response) {
        //     this.responseDetails.status = response.status;
        //     this.responseDetails.time = response.customData.time;
        //     this.responseDetails.size = prettyBytes(
        //         JSON.stringify(response.data).length +
        //         JSON.stringify(response.headers).length
        //     )
        // },
        handleRowDeletion(args) {
            this[args.page].splice(args.index, 1);
            if(args.page === 'params_array') this.handleAttrsChange({newVal : this[args.page], page: 'params_array'});
            else this.handleAttrsChange({newVal : this[args.page], page: 'headers_array'});
        },
        handleRowInsertion(args) {
           this[args].push({key: '', value: ''});
        },
        handleAttrsChange(args) {
            this[args.page] = _.cloneDeep(args.newVal);
            if(args.page === 'params_array') {
                this.paramsObject = {};
                this[args.page].forEach(param => {
                    if(param.key !== '') this.paramsObject[param.key] = param.value;            
                });
            } else {
                this.headersObject = {};
                this[args.page].forEach(header => {
                    if(header.key !== '') this.headersObject[header.key] = header.value;            
                });
            }
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