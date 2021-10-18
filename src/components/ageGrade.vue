<template>
  <!-- <form action="https://ivw09k.deta.dev/" method="post" target="_blank"> -->
  <h2> Age Graded Scores </h2>
  <form @submit.prevent="handleSubmit" >

  <label>Your Gender:</label>
  <select v-model="gender" required>
   		 	<option value="F">Female</option>
   		 	<option value="M">Male</option>
  </select>
  
  <label :class="{'inputerror':ageInvalid}">Your Age :</label>
  <input type="text" v-model="age" required placeholder="00">
  <label :class="{'inputerror':racetimeInvalid}">Racetime </label>
  <input type="text" v-model="racetime" required placeholder="hh:mm:ss">
  
  <label>Race distance:</label>
  		<select v-model="racedistance" required>
   		 	<option value="5kmRoad">5km Road</option>
            <option value="10kmRoad">10km Road</option>
            <option value="HalfMar">Half Marathon</option>
            <option value="Marathon">Marathon</option>
			<option value="25km">25km</option>
			<option value="30km">30km</option>
			
			<option value="6kmRoad">6km Road</option>
			<option value="4MileRoad">4 Mile Road</option>
			<option value="8kmRoad">8km Road</option>
			<option value="5MileRoad">5 Mile Road</option>
			
			<option value="12km">12km</option>
			<option value="15km">15km</option>
			<option value="10Mile">10 Mile</option>
			<option value="20km">20 km</option>
			
			<option value="50km">50km</option>
			<option value="50Mile">50Mile</option>
			<option value="100km">100km</option>
			<option value="150km">150km</option>
			<option value="100Mile">100Mile</option>
			<option value="200km">200km</option>
  		</select>
  <div class="submit">
      <button>Show Age Grade Score</button> <button @click="clear">Clear</button>
  </div>
  
</form>

<div v-if="Object.entries(result).length">
    
    <div class="results">Age Record: {{result.age_standard}}</div>
    <div class="results">Age Grade time {{result.age_graded_result}}</div>
    <div class="results">Age Grade {{result.age_grade_performace}}%</div>
   
</div>
<!-- <div v-if="ageInvalid" class="error"> Age is invalid </div> -->
<!-- <div v-if="racetimeInvalid" class="error"> Racetime is invalid </div> -->
<!-- <p v-if="responseAvailable">{{ result }}</p> -->

</template>

<script>
export default {
    data() {
        return { 
                gender: '',
                age:null,
                racetime:'',
                racedistance : '10kmRoad',
                result : {},
                responseAvailable: false,
                racetimeInvalid:false,
                ageInvalid:false
                }
                
        
        },
    methods: {
        clear() {
            console.log("Clearing all inputs")
            this.gender=''
            this.age=null
            this.racetime=null
            this.racedistance='10kmRoad'
            this.result={}
            this.responseAvailable=false
            this.racetimeInvalid=false
            this.ageInvalid=false

        },
        handleSubmit() {
            // validate data
            this.responseAvailable=false
            this.racetimeInvalid=false
            this.ageInvalid=false 
            this.result={}

            // fetch result using API
            this.fetchAPIageGrade()
            
        },
////
        fetchAPIageGrade( ) {
            var urlencoded = new URLSearchParams();
            urlencoded.append("gender", this.gender);
            urlencoded.append("age", this.age);
            urlencoded.append("event", this.racedistance);
            urlencoded.append("racetime", this.racetime); 
            
            var myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/x-www-form-urlencoded");
            
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: urlencoded,
                redirect: 'follow'
            };
            fetch("https://ivw09k.deta.dev/",requestOptions)
                .then(response => {
	            if (response.ok) {
                    return response.json()
                } else {
                    alert("Server returned "+response.status+" "+response.text)
                }
            })
            .then(response => {
                this.result = response;
                this.responseAvailable = true;
                if (this.result.age === null) {
                    // age is invalid
                    this.ageInvalid=true
                }
                if (this.result.secs === false) {
                    // racetime is invalid
                    this.racetimeInvalid=true
                }
             })
            .catch(err => {
	            console.log(err);
            });
        }
////

    }
}
</script>


<style>
    form {
        max-width: 300px;
        margin: 10px auto;
        background: white;
        text-align: left;
        padding: 10px;
        border-radius: 10px;
    }
    label {
        color: #aaa;
        display: inline-block;
        margin: 25px 0 15px;
        font-size: 0.6em;
        text-transform: uppercase;
        letter-spacing: 1px;
        font-weight: bold;
    }
    input, select {
        display: block;
        padding:10px 6px;
        width: 100%;
        box-sizing: border-box;
        border: none;
        border-bottom: 1px solid #ddd;
        color: #555;

    }
    input[type="checkbox"] {
        display: inline-block;
        width: 16px;
        margin: 0 10px 0 0;
        position: relative;
        top: 2px;
    }
    .pill {
        display: inline-block;
        margin: 20px 10px 0 0;
        padding: 6px 12px;
        background: #eee;
        border-radius: 20px;
        font-size: 12px;
        letter-spacing: 1px;
        font-weight: bold;
        color: #777;
        cursor: pointer;
    }
    button {
        display: inline-block;
        background: #0b6dff;
        border: 0;
        padding: 10px 20px;
        margin-top: 20px;
        color: white;
        border-radius: 20px;
    }
    .submit {
        text-align: center;
    }
    .error {
        color: red;
        margin-top: 10px;
        font-size: 0.8em;
        font-weight: bold;
    }
    .inputerror {
        color: red;
    }
    p {
        font-size: .8em;
        color: blue;
    }
    div.results {
        display: inline-block;
        background: lightblue;
        padding: 10px;
        border-radius: 10px;
        margin: 10px 5px;
        max-width: 200px;
        color: #444;
    }

</style>