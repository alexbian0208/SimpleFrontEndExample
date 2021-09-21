<template>

  <div>
    <!-- Search Bars -->
    <input v-model.trim="searchName" type="text" placeholder="Search by name" class="Search"/>
    <input v-model.trim="searchTags" type="text" placeholder="Search by tags" class="Search"/>
    <!-- Div contains all of students information -->
    <div v-for="student in filterStudentsDataList" :key="student.id" class="StudentData">
      <img :src="student.pic" class="ava"/>
      <div class="body">
      <h1>{{student.firstName}} {{student.lastName}}</h1>
      <ul class="info" style="list-style:none">
        <li>Email: {{student.email}}</li>
        <li>Company: {{student.company}}</li>
        <li>Skill: {{student.skill}}</li>
        <li>Average: {{averageGrade(student.grades)}}%</li>
      </ul>
      <div v-if="student.flag">
        <ul style="list-style:none" v-for="grade in student.grades" :key="student.grades.indexOf(grade)" class="StudentGrade">
          <li>Test{{student.grades.indexOf(grade)+1}}:  {{grade}}%</li>
        </ul>
      </div>
      <!-- Added tags display -->
      <div class="tags">
      <span class="tag" v-for="tag in student.tags" :key="student.tags.indexOf(tag)">{{tag}}</span>
      </div>
      <!-- Add tags input line -->
      <input class="addtags" @keyup.enter="storeTag($event,student)" type="text" placeholder="Add a tag" />
      </div>
      <!-- Button for grades display -->
      <button class="plus" @click="showGrade(student)">{{(student.flag)?'-':'+'}}</button>
    </div>
  </div>
</template>

<script>

export default {
  name: 'StudentsData',
  data(){
      return {
        StudentsDataList:[],
        searchName:"",
        searchTags:"",
        };
  },
  methods:{
    // Calculate average grade for each student
    // Parse grade from String to Int to calculate
    averageGrade:function(grades){
      return grades.reduce((a,b)=>parseInt(a)+parseInt(b))/grades.length;
    }, 
    // A boolean function binding with button
    // to determine whether show the detailed grades or not 
    showGrade:function(student){
      if(student.flag==undefined){
        student.flag=false;
      }
      return student.flag=!student.flag;
    },
    // Store the tags added from user input
    storeTag:function(event,student){
      if(student.tags==undefined){
        student.tags=[];
      }
      student.tags.push(event.target.value);
      event.target.value="";
    }
  },

  computed:{
    // Filter original data list 
    // Only show the student(s) containing both input Name and Tags
    filterStudentsDataList:function(){
      var filterStudentsDataList = this.StudentsDataList;
      var searchName = this.searchName;
      var searchTags = this.searchTags;
      if(searchName!=""){
        filterStudentsDataList = filterStudentsDataList.filter(function(student){
          return student.firstName.toLowerCase().match(searchName.toLowerCase()) || student.lastName.toLowerCase().match(searchName.toLowerCase())
        });
      }
      if(searchTags!=""){
        filterStudentsDataList = filterStudentsDataList.filter(function(student){
          if(student.tags==undefined){
            student.tags=[];
          }
          return student.tags.find(function(tag){
            return tag.toLowerCase().match(searchTags.toLowerCase());
          })
        });
      }
      return filterStudentsDataList;
    }
  },

  mounted(){
    // Fetch the student data from API
    fetch('https://api.hatchways.io/assessment/students')
    .then(res=>{return res.json()})
    .then(data=>(this.StudentsDataList=data.students))
    .then(err=>console.log(err))
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Raleway:wght@200;900&display=swap');
.StudentData{
  font-family: 'Raleway', sans-serif;
  border-bottom: 1px solid lightgray;

}
h1{
  font-weight: bold;
}
.Search{
  padding: 10px;
  font-size: 17px;
  border: none;
  border-bottom: 1px solid lightgrey;
  float: left;
  width: 95%;
}
.ava{
  border-radius: 50%;
  border: 1px solid black;
  float: left;
  height: 100px;
  margin: 20px;
}
.body{
  display: inline-block;
}
.plus{
  float: right;
  margin: 2px;
  font-size: 48px;
  color: lightgray;
  border: none;
  background-color: transparent;
}
.addtags{
  padding: 10px;
  margin: 10px;
  margin-left: 35px;
  font-size: 17px;
  border: none;
  border-bottom: 1px solid lightgrey;
  float: left;
  width: 200px;
}
.tag{
  padding: 5px;
  background-color: lightgray;
  margin: 5px;
  border-radius: 10%;
}
.tags{
  margin-left: 35px;
}
.info li{
  margin: 5px;
}
</style>