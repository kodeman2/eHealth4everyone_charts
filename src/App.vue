 <template>
  <!-- main app container -->
  <div class="app">
   <!-- Header section with app name and description -->
   <div class="header">
      <h1>MEDICharts</h1>
      <p>Charts of Blood Group and Age Range</p>
   </div>
   <!-- Charts section -->
   <div class="charts">
<!-- Blood Group Chart -->
    <highcharts :options="bloodGroupChartOptions"></highcharts>
<!-- Age Range Chart -->
    
    <highcharts :options="ageRangeChartOptions"></highcharts>
   </div>
   <!-- Footer Section with links -->

    <div class="footer">
    <a href="https://github.com/kodeman2/eHealth4everyone_charts" target="_blank"><button>Git Repo</button></a> 
     <a href="https://kodeman.tech" target="_blank">@kodeman.tech 2023</a>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { Chart } from "highcharts-vue";

export default {
  name: "ChartsComponent",
  components: {
    highcharts: Chart,
  },

  data() {
    return {
     // configuration for blood group chart
      bloodGroupChartOptions: {
        chart: {
          type: "bar",
        },
        title: {
          text: "Blood Group",
        },

        subtitle: {
          text: "Source: eHealth4EveryOne",
        },
        labels: {
            rotation: 0,
            style: {
              fontSize: '13px',
              fontFamily: 'Verdana, sans-serif'
            }
          },
        xAxis: {
          title: {
            text: "Blood Group",
          },
         
          categories: [],
        },
        yAxis: {
          title: {
            text: "Number of People",
          },
        },
        series: [
          {
            name: "Number of People",
            data: [],
            color: '#FF0000',
            allowPointSelect: true,
            cursor: 'pointer',
            
            
            
          },
        ],
      },
// configuration for age range chart
      ageRangeChartOptions: {
        chart: {
          type: "bar",
        },
        title: {
          text: "Age Range",
        },
        subtitle: {
          text: "Source: eHealth4EveryOne",
        },
        labels: {
            rotation: 0,
            style: {
              fontSize: '13px',
              fontFamily: 'Verdana, sans-serif'
            }
          },

        
        xAxis: {
          title: {
            text: "Age Range",
           
          },
          categories: ["Ages 1-17", "Ages 18-29", "Ages 30+"],
        },
        yAxis: {
          title: {
            text: "Number of People",
          },
        },
        series: [
          {
            name: "Number of People",
            data: [],
            color: '#008000',
            allowPointSelect: true,
            cursor: 'pointer',
            
          },
        ],
      },
    };
  },

  mounted() {
   // fetch data when the component is mounted
  this.fetchData();
},
methods: {
    async fetchData() {
      try {
       // Fetch data from API
        const response = await axios.get('https://ehealth4every1-1d367-default-rtdb.firebaseio.com/people.json');
        const data = response.data;

        if (typeof data === 'object') {
         // Log the data to confirm
         console.log('Data from API:', data);
          // Convert the object into an array of objects
          const dataArray = Object.values(data);

          // Process and Format the data for the chart
          this.formatData(dataArray);

          // Store the data in local storage
          localStorage.setItem('Bloodgroup', JSON.stringify(dataArray));
        } else {
          console.error('Data is not an object:', data);
        }
      } catch (error) {
        console.error(error);

        // If fetching data from API fails, get data from local storage
        const storedData = localStorage.getItem('Bloodgroup');
        if (storedData) {
          const parsedData = JSON.parse(storedData);

          // Process and Format the stored data 
          this.formatData(parsedData);
        }
      }
    },

    formatData(data) {
      const bloodGroups = {};
      const ageGroups = [0, 0, 0]; // [Children, Young Adults, Adults]

      data.forEach(item => {
        const bloodType = item.blood_group;
        const age = item.age;

        // Count blood groups
        if (bloodGroups[bloodType]) {
          bloodGroups[bloodType]++;
        } else {
          bloodGroups[bloodType] = 1;
        }

        // Categorize age groups
        if (age >= 1 && age <= 17) {
          ageGroups[0]++;
        } else if (age >= 18 && age <= 29) {
          ageGroups[1]++;
        } else {
          ageGroups[2]++;
        }
      });

      // Prepare data for the Blood Group chart
      const bloodGroupCategories = Object.keys(bloodGroups);
      const bloodGroupCounts = Object.values(bloodGroups);

      // Update chart data fot the Blood Group chart
      this.bloodGroupChartOptions.xAxis.categories = bloodGroupCategories;
      this.bloodGroupChartOptions.series[0].data = bloodGroupCounts;

      // Update chart data fot the Age Range chart
      this.ageRangeChartOptions.series[0].data = ageGroups;
    },
  },


};

</script>

<style>
* {
  box-sizing: border-box;
}
.header {
 display: flex;
 justify-content: space-between;
 align-items: center;
  padding: 10px 16px;
  background: #555;
  color: #f1f1f1;
  font-size: 20px;
  font-weight: bold;
  font-family: 'Courier New', Courier, monospace;
  }
  .charts{
   display: flex;
   flex-direction: column;
   
   padding: 10px 20px;
   gap: 50px;
  }
  .footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 30px 16px;
    background: #dad6d6;
    color: #020202;
    font-size: 20px;
    font-weight: bold;
    font-family: 'Courier New', Courier, monospace;
  }

  .footer button{
    padding: 10px 20px;
    background: #555;
    color: #f1f1f1;
    font-size: 20px;
    font-weight: bold;
    font-family: 'Courier New', Courier, monospace;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .footer button:hover{
    background: #f1f1f1;
    color: #555;
  }
  .footer a{
    text-decoration: none;
    color: #020202;
    font-size: 20px;
    font-weight: bold;
    font-family: 'Courier New', Courier, monospace;
  }

  .footer a:hover{
    color: #555;
  }

@media screen and (max-width: 600px) {
 
  .header{
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
  }

  .footer{
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    gap: 10px;
  }
}

 


</style>