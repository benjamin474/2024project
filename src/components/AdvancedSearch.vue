<template>
<!-- 控制整個page的 -->
<div class="advance_page">  
    <!-- 搜尋功能選擇 -->
    <div class="btn-group" >
        <input type="button" value="返回" class="btn-option" id="btn-back"
         @click="hidden_btn()" v-if="btn_show === false">
         
        <input type="button" value="根據年份區間做關鍵字分析" class="btn-option"
         @click="hidden_btn();show_search(1)" v-if="btn_show === true">
         
        <input type="button" value="根據關鍵字出現次數做分析" class="btn-option"
         @click="hidden_btn();show_search(2)" v-if="btn_show === true">
         
        <input type="button" value="關鍵字成長趨勢" class="btn-option"
         @click="hidden_btn();show_search(3)" v-if="btn_show === true">
         
        <input type="button" value="根據年份區間對作者分析" class="btn-option"
         @click="hidden_btn();show_search(4)" v-if="btn_show === true">
         
        <!-- 因為不用輸入資料，直接分析 -->
        <input type="button" value="根據引用次數做分析" class="btn-option"
         @click="hidden_btn(); show_search(5); startAnalysis5()" v-if="btn_show === true">

    </div>
    <!-- 功能顯示 -->
    <div class="search-group">
        <div class="year-keyword search-item" v-if="search_block === 1">
            <p>請輸入年份區間，系統會顯示該區間之關鍵字及出現次數</p>
            <label>
                開始年:
                <input type="number" v-model="startYear"/>
            </label>
            <label>
                結束年:
                <input type="number" v-model="endYear"/>
            </label>
            <input type="button" value="開始分析" @click="startAnalysis1()">
        </div>

        <div class="occurence-keyword search-item" v-if="search_block === 2">
            <p>根據關鍵字出現次數做分析，請輸入下限</p>
            <label>
                最少出現次數(下限):
                <input type="number" v-model="lower_limit" @keyup.enter="startAnalysis2()"/>
            </label>
            <input type="button" value="開始分析" @click="startAnalysis2()">
        </div>

        <div class="single-keyword search-item" v-if="search_block === 3">
            <p>根據關鍵字做查詢，可觀察該關鍵字每年的成長趨勢</p>
            <label>
                輸入關鍵字
                <input type="text" v-model="single_key" @keyup.enter="startAnalysis3()"/>
                <input type="button" value="開始分析" @click="startAnalysis3()">
            </label>
        </div>
       
        <div class="year-author search-item" v-if="search_block === 4">
            <p>根據年份區間對作者做分析（看年份區間內作者發表了幾篇）</p>
            <label>
                開始年:
                <input type="number" v-model="startYear"/>
            </label>
            <label>
                結束年:
                <input type="number" v-model="endYear"/>
            </label>
            <input type="button" value="開始分析" @click="startAnalysis4()">
        </div>

        <div class="author-cite search-item" v-if="search_block === 5">
            <p>根據引用次數做分析（看年份區間內作者發表了幾篇）</p>
        </div>
  
    </div>
  
    <!-- 圖表直接用標籤去套css -->
    <div class="chart-show" v-if="showChart">
        <div id="chart" ></div>
    </div>
</div>
  
</template>

<script setup>
  import { ref } from 'vue';

  const f = JSON.parse(localStorage.getItem("file_lists"))
  console.log(f)
  const work_file = f.map(file =>file.name)
//   console.log(work_file)

  //按鈕顯示
  const btn_show = ref(true);
  //搜尋功能控制
  const search_block = ref(0);
  //年份區間控制項
  const startYear = ref(1950);
  const endYear = ref(2024);
  //出現次數作分析之下限
  const lower_limit = ref(1);
  //單一關鍵字
  const single_key = ref("");
  //圖表展示控制
  const showChart = ref(false);

  const hidden_btn = () => {
      btn_show.value = !btn_show.value;
      if(btn_show.value === true) search_block.value = 0;
      showChart.value = false;
  }

  const show_search = (index) => {
      search_block.value = index;
  }

  //根據年份區間做關鍵字分析
  //未完成部分: workspace資料抓取(先用fake)， 
  async function startAnalysis1(){
      showChart.value = true;
      const requestData = {
          
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          //workspace和file抓你file那個vue的，未處理完成
          workspace: "test2",
          files: work_file,
          start: startYear.value,
          end: endYear.value
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/year', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          
      } catch (error) {
          console.error('Error fetching data:', error);
      }

      //等待10秒
      await sleep(10000);

      google.charts.load('current', { packages: ['corechart'] });
      google.charts.setOnLoadCallback(drawChart1());
  }

  //根據關鍵字出現次數做分析（設定下限）
  //未完成部分: workspace資料抓取(先用fake)， 
  async function startAnalysis2(){
      showChart.value = true;
      const requestData = {
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          //workspace和file抓你file那個vue的，未處理完成
          workspace: "test2",
          files: work_file,
          threshold:lower_limit.value
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/occurence', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          
      } catch (error) {
          console.error('Error fetching data:', error);
      }

      //等待10秒
      await sleep(10000);

      google.charts.load('current', { packages: ['corechart'] });
      google.charts.setOnLoadCallback(drawChart1());
  }

  //根據關鍵字做查詢，可觀察該關鍵字每年的成長趨勢
  async function startAnalysis3(){
      showChart.value = true;
      const requestData = {
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          //workspace和file抓你file那個vue的，未處理完成
          workspace: "test2",
          files: work_file,
          keyword: single_key.value
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/keyword', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          
      } catch (error) {
          console.error('Error fetching data:', error);
      }

      //等待5秒
      await sleep(10000);

      google.charts.load('current', { packages: ['corechart'] });
      google.charts.setOnLoadCallback(drawChart2());
  }

  //作者，年份區間
  async function startAnalysis4(){
      showChart.value = true;
      const requestData = {
          
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          //workspace和file抓你file那個vue的，未處理完成
          workspace: "test2",
          files: work_file,
          start: startYear.value,
          end: endYear.value
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/authorAnalysis/year', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          
      } catch (error) {
          console.error('Error fetching data:', error);
      }

      //等待10秒
      await sleep(10000);

      google.charts.load('current', { packages: ['corechart'] });
      google.charts.setOnLoadCallback(drawChart3());
  }

  //作者，引用次數
  async function startAnalysis5(){
      showChart.value = true;
      const requestData = {
          
          email: localStorage.getItem("email"),
          password: localStorage.getItem("password"),
          //workspace和file抓你file那個vue的，未處理完成
          workspace: "test2",
          files: work_file,
          start: startYear.value,
          end: endYear.value
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/referenceCountAnalysis/generalInfo', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          
      } catch (error) {
          console.error('Error fetching data:', error);
      }

      //等待30秒，這個超久
      await sleep(30000);

      google.charts.load('current', { packages: ['corechart'] });
      google.charts.setOnLoadCallback(drawChart4());
  }

  async function get_results(){
      const requestData = {
        email: localStorage.getItem("email"),
        password: localStorage.getItem("password"),
      };
      try {
          const response = await fetch('https://wos-data-analysis-backend.onrender.com/api/getResult', {
              method: 'POST',
              headers: {
                  'Content-Type': 'application/json'
              },
              body: JSON.stringify(requestData)
          });

          if (!response.ok) {
              throw new Error('API request failed');
          }

          const responseData = await response.json();
          console.log(responseData)
          return responseData;
          // results_data = responseData;
      } catch (error) {
          console.error('Error fetching data:', error);
      }
      
  }

  //用於年份區間和關鍵字出現次數(下限)
  //資料已獲取，但要如何呈現未定
  async function drawChart1() {
      const result = await get_results();
      console.log(result)
      const topData= result.results.slice(0,50);

      const data = new google.visualization.DataTable();
      data.addColumn('string', 'Keyword');
      data.addColumn('number', 'Count');

      const dataArray = topData.map(item => [item.keyword, item.count]);
      data.addRows(dataArray);

      const options = {
          title: 'Keyword Analysis',
          legend: { position: 'none' }
      };

      const chart = new google.visualization.ColumnChart(document.getElementById('chart'));
      chart.draw(data, options);
  }

  //折線圖，關鍵字成長趨勢
  async function drawChart2() {
      const result = await get_results();
      console.log(result)

      const topData= result.results.slice(0,50);
    //   console.log(Object.entries(topData[0]))    之後再嘗試修改

      const data = new google.visualization.DataTable();
      data.addColumn('number', 'Year');
      data.addColumn('number', 'Count');

      const dataArray = topData.map(item => [item.year, item.count]);
      data.addRows(dataArray);

      const options = {
          title: 'Keyword Analysis',
          legend: { position: 'none' },
          hAxis: {
            title: 'Year'
            },
            vAxis: {
            title: 'Count'
            }
      };

      const chart = new google.visualization.LineChart(document.getElementById('chart'));
      chart.draw(data, options);
  }

  //年份區間內作者發表了幾篇
  //資料已獲取，但要如何呈現未定
  async function drawChart3() {
      const result = await get_results();
      console.log(result)
      const topData= result.results.slice(0,50);

      const data = new google.visualization.DataTable();
      data.addColumn('string', 'author');
      data.addColumn('number', 'Count');

      const dataArray = topData.map(item => [item.author, item.count]);
      data.addRows(dataArray);

      const options = {
          title: 'Keyword Analysis',
          legend: { position: 'none' }
      };

      const chart = new google.visualization.ColumnChart(document.getElementById('chart'));
      chart.draw(data, options);
  }

  async function drawChart4() {
    const result = await get_results();
    // console.log(result)
    const topData= result.results.slice(0,50);
    // const topData= result.results;

    console.log(topData)

    const data = new google.visualization.DataTable();
    data.addColumn('string', 'Title');

    // 收集所有作者的名字，確保每個作者都有對應的列
    const authorsSet = new Set();
    topData.forEach(item => {
        item.author.forEach(author => {
        authorsSet.add(author);
        });
    });

    // 添加每個作者作為列
    authorsSet.forEach(author => {
        data.addColumn('number', author);
    });

    // 填充數據
    topData.forEach(item => {
        const row = new Array(data.getNumberOfColumns()).fill(0);
        row[0] = item.title;
        item.author.forEach(author => {
        const authorIndex = data.getColumnIndex(author);
        row[authorIndex] = item.count / item.author.length; // 每個作者的貢獻
        });
        data.addRow(row);
    });

    const options = {
        title: 'Paper Citations by Authors',
        isStacked: true,
        hAxis: {
        title: 'Title'
        },
        vAxis: {
        title: 'Citations'
        },
        height: 500
    };

    const chart = new google.visualization.BarChart(document.getElementById('chart'));
    chart.draw(data, options);
  }


  //拿來延遲的
  function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
  }
</script>

<style scoped>
.advance_page {
    width: 1280px;
    margin-top: 50px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.btn-group {
    width: 100%;
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
    flex-wrap: wrap;
}

.btn-option {
    width: 45%;
    background-color: #3B82F6;
    color: white;
    padding: 10px 20px;
    margin: 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.btn-option:hover {
    background-color: #2563EB;
}

#btn-back{
    width: 5%;
    min-width: 75px;
}

.search-group {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.search-item {
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.search-item p {
    margin-bottom: 10px;
    font-size: 16px;
    font-weight: bold;
}

.search-item label {
    margin-bottom: 10px;
}

.search-item input[type="number"],
.search-item input[type="text"] {
    padding: 5px;
    border: 1px solid #D1D5DB;
    border-radius: 5px;
    margin-right: 10px;
}

.search-item input[type="button"] {
    background-color: #3B82F6;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.search-item input[type="button"]:hover {
    background-color: #2563EB;
}

.chart-show {
    align-items: center;
    width: 80%;
    max-width: 1200px;
    margin-top: 50px;
}




</style>
