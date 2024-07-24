<template>
  <div class="advanced-search">
    <div class="ads-top">
      <div class="dropdown">
        <button class="dropbtn" @click="toggleDropdown">選擇分析功能</button>
        <div class="dropdown-content" v-show="dropdownOpen">
          <a
            @click="
              hidden_btn();
              show_search(1);
            "
            >根據年份區間做關鍵字分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(2);
            "
            >根據關鍵字出現次數做分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(3);
            "
            >關鍵字成長趨勢</a
          >
          <a
            @click="
              hidden_btn();
              show_search(4);
            "
            >根據年份區間對作者分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(5);
              startAnalysis5();
            "
            >根據引用次數做分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(6);
            "
            >根據年份區間做研究領域分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(7);
            "
            >根據研究領域出現次數做分析</a
          >
          <a
            @click="
              hidden_btn();
              show_search(8);
            "
            >研究領域每年的成長趨勢</a
          >
        </div>
      </div>
      <!-- <button class="btn-option" @click="hidden_btn()" v-if="!btn_show">
        返回
      </button> -->
  
      <div class="search-group">
        <div class="year-keyword search-item" v-if="search_block === 1">
          <p>請輸入年份區間，系統會顯示該區間之關鍵字及出現次數</p>
          <label>
            開始年:
            <input type="number" v-model="startYear" class="small-input" />
          </label>
          <label>
            結束年:
            <input type="number" v-model="endYear" class="small-input" />
          </label>
          <input type="button" value="開始分析" @click="startAnalysis1()" />
        </div>
  
        <div class="occurence-keyword search-item" v-if="search_block === 2">
          <p>根據關鍵字出現次數做分析，請輸入下限</p>
          <label>
            最少出現次數(下限):
            <input
              type="number"
              v-model="lower_limit"
              @keyup.enter="startAnalysis2()"
              class="small-input"
            />
          </label>
          <input type="button" value="開始分析" @click="startAnalysis2()" />
        </div>
  
        <div class="single-keyword search-item" v-if="search_block === 3">
          <p>根據關鍵字做查詢，可觀察該關鍵字每年的成長趨勢</p>
          <label>
            輸入關鍵字
            <input
              type="text"
              v-model="single_key"
              @keyup.enter="startAnalysis3()"
              class="small-input"
            />
            <input type="button" value="開始分析" @click="startAnalysis3()" />
          </label>
        </div>
  
        <div class="year-author search-item" v-if="search_block === 4">
          <p>根據年份區間對作者做分析（看年份區間內作者發表了幾篇）</p>
          <label>
            開始年:
            <input type="number" v-model="startYear" class="small-input" />
          </label>
          <label>
            結束年:
            <input type="number" v-model="endYear" class="small-input" />
          </label>
          <input type="button" value="開始分析" @click="startAnalysis4()" />
        </div>
  
        <div class="author-cite search-item" v-if="search_block === 5">
          <p>根據引用次數做分析（看年份區間內作者發表了幾篇）</p>
        </div>
  
        <div class="field-year search-item" v-if="search_block === 6">
          <p>根據年份區間做研究領域分析</p>
          <label>
              開始年:
              <input type="number" v-model="startYear" class="small-input" />
            </label>
            <label>
              結束年:
              <input type="number" v-model="endYear" class="small-input" />
            </label>
          <input type="button" value="開始分析" @click="startAnalysis6()" />
        </div>
  
        <div class="field-occurrence search-item" v-if="search_block === 7">
          <p>根據研究領域出現次數做分析（設定下限）</p>
          <label>
              最少出現次數(下限):
              <input
                type="number"
                v-model="lower_limit"
                @keyup.enter="startAnalysis7()"
                class="small-input"
              />
            </label>
          <input type="button" value="開始分析" @click="startAnalysis7()" />
        </div>
  
        <div class="single-field search-item" v-if="search_block === 8">
          <p>根據研究領域做查詢，可觀察該研究領域每年的成長趨勢</p>
          <label>
            輸入關鍵字
            <input
              type="text"
              v-model="single_field"
              @keyup.enter="startAnalysis8()"
              class="small-input"
            />
            <input type="button" value="開始分析" @click="startAnalysis8()" />
          </label>
        </div>
  
      </div>
    </div>
    

    <div class="chart-show">
      <div id="chart" v-if="showChart"></div>
      <VueSpinnerHourglass size="40px" color="blue" v-if="waitComp" />
    </div>
  </div>
</template>
  
  <script setup>
import { ref, watch } from "vue";
import { VueSpinnerHourglass } from "vue3-spinners";

//前置資料
const dropdownOpen = ref(false);
const btn_show = ref(true);
const search_block = ref(0);
const startYear = ref(1950);
const endYear = ref(2024);
const lower_limit = ref(1);
const single_key = ref("");
const single_field = ref("");
const showChart = ref(false);
const waitComp = ref(false);

const toggleDropdown = () => {
  dropdownOpen.value = !dropdownOpen.value;
};

const props = defineProps({
  selectedWorkspace: {
    type: String,
    required: true,
  },
  files: {
    type: Array,
    required: true,
  },
});

const currentWorkspace = ref(props.selectedWorkspace);
const work_file = ref(props.files.map((file) => file.name));

watch(
  () => props.selectedWorkspace,
  (newValue) => {
    currentWorkspace.value = newValue;
    btn_show.value = true;
    search_block.value = 0;
    showChart.value = false;
  }
);

watch(
  () => props.files,
  (newFiles) => {
    work_file.value = newFiles.map((file) => file.name);
  }
);

const hidden_btn = () => {
  btn_show.value = !btn_show.value;
  if (btn_show.value === true) search_block.value = 0;
  showChart.value = false;
};

const show_search = (index) => {
  search_block.value = index;
};

const getCookie = (name) => {
      const nameEQ = name + "=";
      const ca = document.cookie.split(";");
      for (let i = 0; i < ca.length; i++) {
        let c = ca[i];
        while (c.charAt(0) == " ") c = c.substring(1, c.length);
        if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length, c.length);
      }
      return null;
    };
// console.log(getCookie("token"))

//功能開始
async function startAnalysis1() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    start: startYear.value,
    end: endYear.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/year",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart1);
}

async function startAnalysis2() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    threshold: lower_limit.value,
  };

  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/occurence",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart1);
}

async function startAnalysis3() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    keyword: single_key.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/keywordAnalysis/keyword",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart2);
}

async function startAnalysis4() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    start: startYear.value,
    end: endYear.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/authorAnalysis/year",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart3);
}

async function startAnalysis5() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    start: startYear.value,
    end: endYear.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/referenceCountAnalysis/generalInfo",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart4);
}

//根據年份區間做研究領域分析
async function startAnalysis6() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    start: startYear.value,
    end: endYear.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/fieldAnalysis/year",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart5);
    // const result = await get_results();
    // console.log(result)
}

//領域，下限次數
async function startAnalysis7() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    threshold: lower_limit.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/fieldAnalysis/occurence",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart5);
}

async function startAnalysis8() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token"),
    workspace: currentWorkspace.value,
    files: work_file.value,
    field: single_field.value,
  };
  try {
    const response = await fetch(
      "https://wos-data-analysis-backend.onrender.com/api/fieldAnalysis/field",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(requestData),
      }
    );

    if (!response.ok) {
      throw new Error("API request failed");
    }

    const responseData = await response.json();
    console.log(responseData);
  } catch (error) {
    console.error("Error fetching data:", error);
  }

  google.charts.load("current", { packages: ["corechart"] });
  google.charts.setOnLoadCallback(drawChart6);
  // const result = await get_results();
  // console.log(result)
}

//獲取資料區塊
async function get_results() {
  const requestData = {
    // email: localStorage.getItem("email"),
    // password: localStorage.getItem("password"),
    token: getCookie("token")
  };
  waitComp.value = true;
  let maxRetries = 5;
  let attempt = 0;

  while (attempt < maxRetries) {
    try {
      const response = await fetch(
        "https://wos-data-analysis-backend.onrender.com/api/getResult",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(requestData),
        }
      );

      if (!response.ok) {
        throw new Error("API request failed");
      }
      waitComp.value = false;
      showChart.value = true;
      const responseData = await response.json();
      return responseData;
    } catch (error) {
      console.error("Error fetching data, retrying...", error);
      attempt++;
      await new Promise((resolve) => setTimeout(resolve, 3000));
    }
  }
  waitComp.value = false;
  throw new Error("API request failed after maximum retries");
}

async function drawChart1() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);

  const data = new google.visualization.DataTable();
  data.addColumn("string", "Keyword");
  data.addColumn("number", "Count");

  const dataArray = topData.map((item) => [item.keyword, item.count]);
  data.addRows(dataArray);

  const options = {
    title: "Keyword Analysis",
    legend: { position: "none" },
    width: '100%',
    height: '100%'
  };

  const chart = new google.visualization.ColumnChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}

async function drawChart2() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);

  const data = new google.visualization.DataTable();
  data.addColumn("number", "Year");
  data.addColumn("number", "Count");

  const dataArray = topData.map((item) => [item.year, item.count]);
  data.addRows(dataArray);

  const options = {
    title: "Keyword Analysis",
    legend: { position: "none" },
    hAxis: {
      title: "Year",
    },
    vAxis: {
      title: "Count",
    },
  };

  const chart = new google.visualization.LineChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}

async function drawChart3() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);

  const data = new google.visualization.DataTable();
  data.addColumn("string", "author");
  data.addColumn("number", "Count");

  const dataArray = topData.map((item) => [item.author, item.count]);
  data.addRows(dataArray);

  const options = {
    title: "Keyword Analysis",
    legend: { position: "none" },
  };

  const chart = new google.visualization.ColumnChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}

async function drawChart4() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);

  const data = new google.visualization.DataTable();
  data.addColumn("string", "Title");

  const authorsSet = new Set();
  topData.forEach((item) => {
    item.author.forEach((author) => {
      authorsSet.add(author);
    });
  });

  authorsSet.forEach((author) => {
    data.addColumn("number", author);
  });

  topData.forEach((item) => {
    const row = new Array(data.getNumberOfColumns()).fill(0);
    row[0] = item.title;
    item.author.forEach((author) => {
      const authorIndex = data.getColumnIndex(author);
      row[authorIndex] = item.count / item.author.length;
    });
    data.addRow(row);
  });

  const options = {
    title: "Paper Citations by Authors",
    isStacked: true,
    hAxis: {
      title: "Title",
    },
    vAxis: {
      title: "Citations",
    },
    height: 500,
  };

  const chart = new google.visualization.BarChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}

async function drawChart5() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);
//   const topData = result.results.slice(0, 15);

  const data = new google.visualization.DataTable();
  data.addColumn("string", "Field");
  data.addColumn("number", "Count");

  const dataArray = topData.map((item) => [item.field, item.count]);
  data.addRows(dataArray);

  const options = {
    title: "Field Analysis",
    legend: { position: "none" },
  };

  const chart = new google.visualization.ColumnChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}

async function drawChart6() {
  const result = await get_results();
  const topData = result.results.slice(0, 50);

  const data = new google.visualization.DataTable();
  data.addColumn("number", "Year");
  data.addColumn("number", "Count");

  const dataArray = topData.map((item) => [item.year, item.count]);
  data.addRows(dataArray);

  const options = {
    title: "Field Analysis",
    legend: { position: "none" },
    hAxis: {
      title: "Year",
    },
    vAxis: {
      title: "Count",
    },
  };

  const chart = new google.visualization.LineChart(
    document.getElementById("chart")
  );
  chart.draw(data, options);
}


</script>
  
  <style scoped>
.advanced-search {
  width: 70%;
  margin: 20px auto;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content:center;
  align-content:flex-start;
}
.ads-top {
  width: 100%;
  display: flex;
  justify-content: space-around;
  align-items:center;
}
.dropdown {
  position: relative;
  display: inline-block;
  margin-bottom: 20px;
}

.dropbtn {
  width: 120%;
  background-color: #333;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.dropbtn:hover {
  background-color: #555;
}

.dropdown-content {
  display: none;
  position: absolute;
  background-color: #f9f9f9;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
  border-radius: 10px;
  z-index: 1;
}

.dropdown-content a {
  color: black;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
  border-bottom: 1px solid #ddd;
}

.dropdown-content a:hover {
  background-color: #f1f1f1;
}

.dropdown:hover .dropdown-content {
  display: block;
}

.btn-option {
  background-color: #333;
  color: white;
  padding: 10px 20px;
  margin: 10px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.btn-group {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  margin-bottom: 20px;
  width: 100%;
}

.btn-option:hover {
  background-color: #555;
}

.search-group {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.search-item {
  margin-bottom: 20px;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 80%;
  text-align: center;
}

.search-item p {
  font-size: 24px;
  font-weight: bold;
}

.search-item label {
  display: block;
  margin: 10px 0;
}

.search-item input[type="number"],
.search-item input[type="text"] {
  padding: 10px;
  margin-top: 10px;
  width: calc(100% - 22px);
  border: 1px solid #ddd;
  border-radius: 10px;
}

.search-item button {
  background-color: #333;
  color: white;
  padding: 10px 20px;
  margin-top: 20px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-item button:hover {
  background-color: #555;
}
.chart-show {
  width: 100%;
  height: 400px;
  max-width: 1200px;
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
#chart{
  width: 100%;
  height: 100%;
}

.small-input {
  width: 60px;
  padding: 5px;
  border: 1px solid #d1d5db;
  border-radius: 5px;
  margin-right: 10px;
}

.search-item input[type="button"] {
  background-color: #3b82f6;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-item input[type="button"]:hover {
  background-color: #2563eb;
}
</style>
  