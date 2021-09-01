<template>
  <!-- <div class="p-4"> -->
      <div class="request-form">
        <form class="form-section">
            <div class="input-section">
            <select class="select-type">
                <option value="GET" selected>GET</option>
                <option value="POST">POST</option>
                <option value="PUT">PUT</option>
                <option value="PATCH">PATCH</option>
                <option value="DELETE">DELETE</option>
            </select>
            <input class="standard-input url-input" required type="URL" placeholder="https://www.example.com"/>
            <button type="submit" class="btn btn-primary submit-btn">SUBMIT</button>
            </div>
            <tab-section/>
        </form>
      </div>
    <!-- </div> -->
</template>
<script>
import TabSection from "./TabSection"

export default {
    name: 'request-info',
    components: {
        TabSection,
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