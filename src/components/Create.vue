<script setup>
import { ref } from "@vue/reactivity"; // import ref  from vue
import { useMutation } from "@urql/vue";
/*  Create field for job form  - corresponds to the fields we have in our Webiny CMS */

const title = ref("");
const desc = ref("");
const jobUrl = ref("");
const location = ref("");
const contact = ref("");
const expires = ref("");
const createJobResult = useMutation(`
mutation(
  $title:String!, 
  $contact:String!, 
  $desc:String!,
   $location:String!, 
   $url:String!,
   $expiry:Date!) {

createJob(data:{
  	jobRole:$title
    jobContact:$contact
    jobDescription:$desc
    jobLocation:$location
    jobUrl:$url
    startDate:$expiry
	})
  {
    data{
      jobRole
      jobDescription
      jobContact
      jobLocation
      jobUrl
      startDate
    }
  }
}
`);

/* * This function will take all the input from the form and push it into an object.
Then it will push the object to the jobs array and reset all the input fields.
*/
const checkFields = () => {
  if (
    title.value == "" ||
    desc.value == "" ||
    contact.value == "" ||
    jobUrl.value == "" ||
    location.value == "" ||
    expires.value == ""
  ) {
    alert("Fill in the fields");
  } else {
    addJob();
  }
};
const addJob = () => {
  const variables = {
    title: title.value,
    contact: contact.value,
    desc: desc.value,
    location: location.value,
    url: jobUrl.value,
    expiry: new Date(expires.value).toISOString().split("T")[0],
    // WEBINY CMS accepts a particular format for sending Dates
  };
  createJobResult.executeMutation(variables).then((result) => {
    if (result.error) {
      alert(result.error.name);
    } else {
      window.location.reload();
    }
  });
};
/** Reset all the dynamic fields after submitting the form */
function resetFields() {
  title.value = "";
  jobUrl.value = "";
  desc.value = "";
  location.value = "";
  contact.value = "";
  expires.value = "";
}
</script>
<template>
  <h1>CREATE JOB</h1>
  <!-- Form for creating a job. This will push entered values into the CMS -->
  <form class="jobForm" @submit.prevent="checkFields()">
    <label for="title">Title</label>
    <input type="text" name="title" v-model="title" />
    <label for="desc">Job Description</label>
    <textarea name="desc" class="jobDesc" rows="5" v-model="desc"> </textarea>
    <label for="URL">Job Reference URL</label>
    <input
      type="url"
      name="URL"
      placeholder="http://"
      pattern="http://.*"
      v-model="jobUrl"
    />
    <label for="contact">Job Contact Email</label>
    <input type="email" name="contact" v-model="contact" />
    <label for="location">Job Location</label>
    <input type="text" name="location" v-model="location" />
    <label for="expiry">Job Start Date</label>
    <input type="date" v-model="expires" name="expiry" />
    <button type="submit" value="Submit">ADD JOB</button>
  </form>
</template>
