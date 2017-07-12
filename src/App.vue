<template>
<!-- root  -->
  <div id="app">
 <vue-progress-bar></vue-progress-bar>

    
  <!-- header  -->
    <section class="hero is-dark">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            DMS
          </h1>
          <h2 class="subtitle">
            Welcome to DMS App
          </h2>
        </div>
      </div>
    </section>

<!-- main  -->
    <section class="section">
     <div class="container">
      <label for="from">Enter DOC ID</label>

      <div class="">
      <input class="input" type="text" v-model="docId" name="docID" id="docID" style="height: 20px; width: 200px; margin:10px;" />
      </div>

      <div class="">
      <button class="button" id="document-search-submit" v-on:click="submitDoc(docId)">Submit</button>
      </div>
      <br />
      <div v-if="isError">
        {{msg}}
      </div>
      <div v-if="!isError">
        <div class="">
          DOC ID: = {{resultMsg.docID}}
        </div>
        <div class="">
          link: = {{resultMsg.link}}
        </div>
      </div>
     </div>
    </section>

<!-- footer  -->  

 	<footer class="footer">
    <div class="container">
      <div class="content has-text-centered">
        <p>Powered by AIS TEAM</p>
      </div>
    </div>
  </footer>



  </div>
</template>

<script>
import Axios from 'axios'
export default {

  name: 'app',
  mounted () {
    //  [App.vue specific] When App.vue is finish loading finish the progress bar
    this.$Progress.finish()
  },
  data() {
    return {
      msg: '',
      docId: '',
      resultMsg: {
        docID: '',
        link: ''
      },
      isError: true
    }
  },
  methods: {
    success(docData) {
      this.msg = "DOC ID: = " + docData.data.body.id + "link: =" + docData.data.body.link
      this.resultMsg.docID = docData.data.body.id
      this.resultMsg.link = docData.data.body.link
    },
    failed(docData) {
      this.msg = "DOC ID NOT FOUND"
    },
    submitDoc(id) {
       this.$Progress.start()
      if (id === "") {
        this.msg = "Please Enter DOC ID!!!"
        this.$Progress.fail()
      }
      else {
        console.log('id',id)
        let baseURL = "http://172.17.35.66:8080"
        Axios.get(baseURL + "/document/" + id)
          .then((response) => {
            let returnCode = response.data.header.code
            if (returnCode == "200") {
              this.success(response)
              this.$Progress.finish()
              this.isError = false
            } else {
              this.isError = true
              this.failed(response)
            }
            //alert(returnCode)
            // let docID = (response.data.id + " " + response.data.user + " " + response.data. + " " +response.data.description)  
            //this.msg = response
            // alert(docID)
            // alert (" " + "id:3, link:http://archive.xom.com/jlkajsdfjiwep;oij/~kjkljdfj/new_doc_3.docx")

            // this.msg = ("id:3 \n link:http://archive.xom.com/jlkajsdfjiwep;oij/~kjkljdfj/new_doc_3.docx") 
            this.$Progress.fail()
            console.log(response)
          }, (err)=>{
            console.log('err',err)
            this.msg = "ERROR : CONNECTION TIME OUT!!!"
            this.$Progress.fail()
          })
      }

    }

  }

}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.section{
  height: 50vh
}
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
