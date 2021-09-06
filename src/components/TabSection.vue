<template>
  <div>
    <div class="nav-tabs">
        <div class="tablink" :class="{'active-tab': activeTab.query, 'inactive-tab': !activeTab.query}" @click="openPage('query')">Query params</div>
        <div class="tablink" :class="{'active-tab': activeTab.headers, 'inactive-tab': !activeTab.headers}" @click="openPage('headers')">Headers</div>
        <div class="tablink" :class="{'active-tab': activeTab.json, 'inactive-tab': !activeTab.json}" @click="openPage('json')">JSON</div>
        <div class="error-container">{{error}}</div>
    </div>
    <div v-if="activeTab.query" id="query-params" class="tabcontent">
        <div class="params-row" v-for="(param, index) in paramsLocal" :key="index">
            <input class="standard-input key-input" placeholder="Key" v-model="param.key"/>
            <input class="standard-input value-input" placeholder="Value" type="text" v-model="param.value" />
            <button type="button" class="btn btn-outline-danger remove" @click="$emit('delete-row', { page: 'params_array', index})">REMOVE</button>
        </div>
        <button type="button" class="btn btn-outline-success add" @click="$emit('insert-row', 'params_array')">ADD</button>
    </div>

    <div v-if="activeTab.headers" id="headers" class="tabcontent">
        <div class="headers-row" v-for="(header, index) in headersLocal" :key="index">
            <input class="standard-input key-input" placeholder="Key" v-model="header.key"/>
            <input class="standard-input value-input" placeholder="Value" type="text" v-model="header.value" />
            <button type="button" class="btn btn-outline-danger remove" @click="$emit('delete-row', { page: 'headers_array', index})">REMOVE</button>
        </div>
        <button type="button" class="btn btn-outline-success add" @click="$emit('insert-row', 'headers_array')">ADD</button>
    </div>

    <div v-if="activeTab.json" id="json" class="tabcontent">
        <codemirror v-model="dataBody" :options="cmOptions" ref="cmEditor"></codemirror>
    </div>
  </div>
</template>

<script>

import { codemirror } from 'vue-codemirror';
import 'codemirror/lib/codemirror.css';
const _ = require("lodash");

export default {
    name: 'tab-section',
    data() {
        return {
            rowCount: 1,
            activeTab: {
                query: true,
                headers: false,
                json: false
            },
            paramsLocal: [],
            headersLocal: [],
            cmOptions: {
                tabSize: 2,
                mode: {name: "javascript", json: true},
                theme: 'base16-dark',
                lineNumbers: true,
                line: true,
            },
            dataBody: '',
            error: '',
            jsonValue: null
        }
    },
    props: {
        params: {
            type: Array,
            default: () => {[]}
        }, 
        headers: {
            type: Array,
            default: () => {[]}
        }
    },
    components: {
        codemirror
    },
    watch: {
        dataBody(newVal) {
            this.jsonValue = null;
            this.error="";
            try {
                // try to parse
                this.jsonValue = JSON.parse(newVal);
            }
            catch(e) { 
                this.error = e.message;
            }
        },
        params: {
            immediate: true,
            handler(paramsFromProps) {
                if(paramsFromProps) {
                    if(paramsFromProps.length !== this.paramsLocal.length) this.paramsLocal = _.cloneDeep(paramsFromProps)
                    else {
                        const isDiff = this.compareTwoSets(paramsFromProps, this.paramsLocal);
                        if(isDiff) _.cloneDeep(paramsFromProps);
                    }
                }
            }
        },
        paramsLocal: {
            handler(newVal) {
                this.$emit('attrs-change', { newVal, page: 'params_array'})
            },
            deep: true
        },
        headers: {
            immediate: true,
            handler(headersFromProps) {
                if(headersFromProps) {
                    if(headersFromProps.length !== this.headersLocal.length) this.headersLocal = _.cloneDeep(headersFromProps)
                    else {
                        const isDiff = this.compareTwoSets(headersFromProps, this.headersLocal);
                        if(isDiff) _.cloneDeep(headersFromProps);
                    }
                }
            }
        },
        headersLocal: {
            handler(newVal) {
                this.$emit('attrs-change', { newVal, page: 'headers_array'})
            },
            deep: true
        }
    },
    methods: {
        openPage(pageName) {
            Object.keys(this.activeTab).forEach(key => this.activeTab[key] = false)
            this.activeTab[pageName] = true;
        },
        compareTwoSets(setOne, setTwo) {
            if(setOne.length !== setTwo.length) return true;
            for(let itr=0; itr<setOne.length; itr++) {
                if(setOne[itr].key !== setTwo[itr].key || setOne[itr].value !== setTwo[itr].value) return true;
            }
            return false;
        }
    }
}
</script>

<style lang="scss" scoped>
.nav-tabs {
    display: flex;
    width: 100%;
    margin-top: 20px;
    /* Style tab links */
    .tablink {
    color: #444;
    float: left;
    outline: none;
    cursor: pointer;
    padding: 14px 16px;
    font-size: 17px;
    box-sizing: border-box;
    width: 150px;
    text-align: center;
    border-radius: 5px 5px 0 0;
        &:hover {
            background-color: #f5f5f5; 
        }
    }
    .active-tab {
       border: 2px solid #dee2e6;
       border-bottom: none;
    //    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
    }
    .inactive-tab {
        border: none;
        border-bottom: 2px solid #dee2e6;
        box-shadow: none;
    }
    .error-container {
        width: calc(100% - 450px);
        border-bottom: 2px solid #dee2e6;
        padding: 15px 10px;
        color: red;
    }

}
.tabcontent {
    border: 2px solid #dee2e6;
    border-top: none;
    max-height: 200px;
    overflow: auto;
    .params-row, .headers-row {
        width: 100%;
        padding: 10px 15px;
        box-sizing: border-box;
        .remove {
            margin-left: 5px;
        }
        .value-input {
            margin-left: 5px;
        }
    }
    .add {
        margin: 10px 0 10px 15px;
    }
}

</style>