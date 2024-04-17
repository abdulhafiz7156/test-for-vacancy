<template>
  <div class="table">
    <table>
      <thead>
      <tr>
        <th class="id__and__draggable">Номер</th>
        <th class="action">Действие</th>
        <th class="unit-name">Наименование еденицы</th>
        <th class="price">Цена</th>
        <th class="quantity">Кол-во</th>
        <th class="product-name">Название товара</th>
        <th class="total">Итого</th>
      </tr>
      </thead>
      <tbody v-for="(item, index) in items" :key="index" draggable="true"
             @dragstart="startDrag(index)" @dragover="overDrag($event)" @drop="handleDrop(index)"
             :class="{ 'dragged' : index === draggedItem }">
      <tr>
        <td class="sequence"><img src="../assets/menu-burger.svg" alt=""> {{ item.id }}</td>
        <td class="action" @click="openPopup($event)">
          <img src="../assets/three-dots.svg" alt="">
          <!-- Popup -->
          <div v-if="popupVisible" class="popup">
            <p>Удалить</p>
            <button @click="deleteItem(index)">Удалить</button>
          </div>
        </td>
        <td class="unit-name">
          <input type="text" v-model="item.unitName"  @input="item.edited = true"  >
        </td>
        <td class="price">
          <input type="number" v-model="item.price"   @input="item.edited = true">
        </td>
        <td class="quantity">
          <input type="number" v-model="item.count"  @input="item.edited = true" >
        </td>
        <td class="product-name">
          <input type="text" v-model="item.productName"  @input="item.edited = true" >
        </td>
        <td class="total">
          <input type="number" v-model="item.total"   @input="item.edited = true" >
        </td>
        <button v-if="item.edited" @click="saveDataToServer(item)" class="popup-button">Сохранить</button>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "Table-main",
  data() {
    return {
      fak: {id: 1, name: "fak"},
      items: [
        {
          id: 1,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 1,
          count: 1,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
          editing: false // Добавлено свойство для редактирования
        },
        {
          id: 2,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 2,
          count: 2,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
          editing: false // Добавлено свойство для редактирования
        },
        {
          id: 3,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 3,
          count: 3,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
          editing: false // Добавлено свойство для редактирования
        },
      ],
      draggedItem: null,
      popupVisible: false,
      popupPosition: { top: "0", left: "0" },
    };
  },
  methods: {
    multiplyFunc() {
      this.items.forEach(item => {
        item.total = item.price * item.count;
      });
    },
    startDrag(index) {
      this.draggedItem = index;
      console.log("but start drag: " + index);
    },
    overDrag(event) {
      event.preventDefault();
      // console.log("but over drag: " + event)
    },
    handleDrop(index) {
      let droppedItem = this.items.splice(this.draggedItem, 1)[0];
      this.items.splice(index, 0, droppedItem);
      this.draggedItem = null;
      this.items.forEach((item, i) => {
        item.id = i + 1;
      });
      this.draggedItem = null;

      console.log("but handle drop: " + index);
    },
    deleteItem(index) {
      this.items.splice(index, 1);
      this.popupVisible = false; // Close the popup after deletion
    },
    openPopup(event) {
      // Get the position of the clicked td element
      const tdRect = event.currentTarget.getBoundingClientRect();

      // Calculate the top and left position for the popup
      const popupTop = tdRect.top + window.scrollY + tdRect.height;
      const popupLeft = tdRect.left + window.scrollX;

      // Update the position of the popup
      this.popupPosition = {
        top: popupTop + "px",
        left: popupLeft + "px",
      };
      console.log(popupLeft);
      this.popupVisible = !this.popupVisible;
    },

    saveDataToServer(item) {
      const url = 'https://example/api/'; // Замените на нужный URL вашего API

      const data = {};

      if (item.edited) {
        data.id = item.id;
        data.unitName = item.unitName;
        data.price = item.price;
        data.count = item.count;
        data.productName = item.productName;
        data.total = item.total;
        // Отправляем AJAX запрос на сервер
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        })
            .then(response => {
              if (!response.ok) {
                throw new Error('Network response was not ok');
              }
              return response.json();
            })
            .then(data => {
              console.log('Data sent successfully:', data);
            })
            .catch(error => {
              console.error('There was a problem with your fetch operation:', error);
            });

        item.edited = false;
      }
    }
  },
  created() {
    this.multiplyFunc();
  },
};
</script>


<style scoped>


.table {
  width: 100%;
  height: 100%;
  margin: 25px 0 175px 0;
  padding: 32px 0px 25px;
  border-radius: 10px;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  background-color: #fff;
  border: 1px solid #eeeff1;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th {
  border: 1px solid #eeeff1;
  resize: horizontal; /* Разрешаем изменение размера только по горизонтали */
  overflow: auto; /* Добавляем полосы прокрутки при изменении размера столбца */
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #dddddd;
}

th.id__and__draggable, th.option {
  width: 30px; /* Adjust width as needed */
}

td.action {
  width: 80px; /* Adjust width as needed */
}

td.total {
  font-weight: bold;
}

button {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #0056b3;
}

.sequence {
  display: flex;
  cursor: pointer;
}

.sequence img {
  padding-right: 5px;
}

.dragged {
  border: 2px #a6b7d4 dashed;
  border-radius: 5px;
}

.popup {
  position: absolute;
  background-color: #fff;
  border: 1px solid #ccc;
  padding: 10px;
  z-index: 1;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.popup p {
  margin: 0 0 10px;
}

.popup-button {
  margin-left: 10px;

}

</style>
