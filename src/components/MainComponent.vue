<template>
  <div class="sidebar__home">
    <div class="sidebar"></div>
    <div class="main">
      <div class="container">
        <h1>  Проведение ТО и мелкий ремонт</h1>
        <div class="main__ul__settings df">
          <ul class="main__ul df">
            <li>Общее</li>
            <li class="main_ul__li__active">Товары</li>
            <li>Доп. расходы</li>
          </ul>
          <div class="settings" @click="togglePopup">
            <img src="../assets/settings.svg" alt="Settings icon">
          </div>
          <div class="popup" id="popup" v-if="popupVisible">
            <!-- Здесь содержимое вашего всплывающего окна -->
            <ul>
              <li>Отображение столбцов <img src="../assets/right-side.svg" alt="Right-side icon"></li>
              <li>Порядок столбцов  <img src="../assets/right-side.svg" alt="Right-side icon"></li>
            </ul>
          </div>
        </div>
        <TableMain></TableMain>
      </div>
    </div>
  </div>
</template>

<script>
import "./MainComponent.css"
import TableMain from "@/components/Table";
export default {
    name: 'MainComponent',
  components: {TableMain},
  data() {
    return {
      popupVisible: false,
    }
  },
  methods: {
    togglePopup() {
      this.popupVisible = !this.popupVisible;
    }
  }
}
</script>