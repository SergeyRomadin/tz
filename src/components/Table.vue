<template>
  <div>
    <!-- Таблица из библеотеки vuetify, смотреть документацию v-data-table -->
    <v-data-table
      :headers="headers"
      :items="tableData"
      :items-per-page="10"
      class="elevation-1"
      @click:row="editItem"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>ВНИИЖТ</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <!-- Модальное окно добавления нового элемента смотреть документацию v-dialog -->
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Новый элемент
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.ordNumber"
                        label="№ п/п"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.carNumber"
                        label="Номер вагона"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.trainIndex"
                        label="Индекс поезда"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.trainNumber"
                        label="Номер поезда"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.carStatus"
                        label="Статус"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.lastOperDt"
                        label="Дата операции"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.invoiceId"
                        label="№ накладной"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.invoiceNumber"
                        label="ИД накладной"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.stateId"
                        label="stateId"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
    </v-data-table>
    <!-- Круговая диаграмма смотреть документацию vue-google-charts -->
    <v-container>
      <v-row class="overflow-hidden">
        <v-col cols="12" sm="6" md="4">
          <GChart type="PieChart" :data="chartData" :options="chartOptions" />
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
// Импорт библиотеки для преобразования ременных форматов
import moment from "moment";
// Импорт библиотеки круговой Диаграммы
import { GChart } from "vue-google-charts/legacy";

export default {
  // Подключаем наш компонент с Диаграммой
  components: {
    GChart,
  },
  data() {
    return {
      // Если true отображает модальное окно
      dialog: false,
      // Заголовки для таблицы
      headers: [
        {
          text: "№ п/п",
          align: "center",
          sortable: true,
          value: "ordNumber",
        },
        {
          text: "Номер вагона",
          value: "carNumber",
          align: "center",
        },
        {
          text: "Индекс поезда",
          value: "trainIndex",
          align: "center",
        },
        {
          text: "Номер поезда",
          value: "trainNumber",
          align: "center",
        },
        {
          text: "Статус",
          value: "carStatus",
          align: "center",
        },
        {
          text: "Дата-время операции",
          value: "lastOperDt",
          align: "center",
        },
        {
          text: "№ накладной",
          value: "invoiceNumber",
          align: "center",
        },
        {
          text: "ИД накладной",
          value: "invoiceId",
          align: "center",
        },
        {
          text: "stateId",
          value: "stateId",
          align: "center",
        },
      ],
      // Проверяем поля даты и изменяем формат вывода на более понятный пользователю
      tableData: this.tableData.map((el) => {
        el.lastOperDt
          ? (el.lastOperDt = moment(el.lastOperDt).format("DD.MM.YYYY hh:mm"))
          : (el.lastOperDt = "-");
      }),
      // Переменная для проверки действия. Если -1 то открывается форма для редактирования элемента, в остальных случаях откроется модалка для создания нового элемента
      editedIndex: -1,
      // Дефолтные значения для формы редактирования
      editedItem: {
        ordNumber: 0,
        carNumber: 0,
        trainIndex: 0,
        trainNumber: 0,
        carStatus: "",
        invoiceId: 0,
        invoiceNumber: "",
        stateId: 0,
        lastOperDt: "ДД.ММ.ГГГГ ЧЧ:ММ",
      },
      defaultItem: {
        ordNumber: 0,
        carNumber: 0,
        trainIndex: 0,
        trainNumber: 0,
        carStatus: "",
        invoiceId: 0,
        invoiceNumber: "",
        stateId: 0,
        lastOperDt: "ДД.ММ.ГГГГ ЧЧ:ММ",
      },
      // Переменная содержит значения для отображения в круговой диаграмме
      chartData: [["stateID", "Количество элементов"]],
      // Настройки для круговой диаграммы
      chartOptions: {
        title: "StateID",
        pieHole: 0.4,

        width: document.documentElement.clientWidth,
        height: 500,
      },
    };
  },
  props: {
    tableData: Array,
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1
        ? "Новый элемент"
        : "Редактировать элемент";
    },
  },

  watch: {
    dialog(val) {
      this.computeChart();
      val || this.close();
    },
  },
  // вешаем и убираем прослушки на изменение размера окна
  mounted() {
    window.addEventListener("resize", this.getDimensions);
  },
  unmounted() {
    window.removeEventListener("resize", this.getDimensions);
  },
  created() {
    this.computeChart();
  },

  updated() {},

  methods: {
    // получаем ширину окна
    getDimensions() {
      this.chartOptions.width = document.documentElement.clientWidth;
    },
    // формируем масив данных для круговой диаграммы
    computeChart() {
      this.chartData = [];
      let arr = [];
      this.tableData.forEach((el) => {
        arr.push(el.stateId);
      });
      // считаем количество одинаковых значений в массиве
      const result = arr.reduce((acc, rec, index) => {
        return typeof acc[rec] !== "undefined"
          ? { ...acc, [rec]: acc[rec] + 1 }
          : { ...acc, [rec]: 1 };
      }, {});
      // формируем масив в нужном формате для вывода в круговой диаграмме [[элемент, количество]...]
      this.chartData = [["stateID", "Количество элементов"]];
      for (let key in result) {
        this.chartData.push([key, result[key]]);
      }
    },
    // Делаем кастомную сортировку для поля времени в таблице
    customSort(items, index, isDesc) {
      items.sort((a, b) => {
        if (index === "lastOperDt") {
          if (!isDesc) {
            let aDate = +new Date(a.lastOperDt),
              bDate = +new Date(b.lastOperDt);
            return compare(aDate, bDate);
          } else compare(bDate, aDate);
        }
      });
      return items;
    },
    // функция редактирования элементов таблицы
    editItem(item) {
      this.editedIndex = this.tableData.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    // Функция закрытия модального окна
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    // Функция сохранения результата
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.tableData[this.editedIndex], this.editedItem);
      } else {
        this.tableData.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>
