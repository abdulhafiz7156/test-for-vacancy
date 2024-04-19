<template>
  <div class="table">
    <div class="create__table">
      <div class="create__table__div">
        <button type="button" @click="addItem()" ><img src="../assets/plus.svg" alt=""> Добавить строку</button>
      </div>
    </div>
    <table>
      <thead>
      <tr>
        <th class="id__and__draggable" v-for="(i, index) in columns" :key="index" draggable="true" @dragstart="startDragHeader(index)"
            @dragover="overDragHeader($event)" @drop="handleDropHeader(index)">
          {{ i.title }}
        </th>
      </tr>
      </thead>
      <tbody v-for="(item, index) in items" :key="index" draggable="true"
             @dragstart="startDrag(index)" @dragover="overDrag($event)" @drop="handleDrop(index)"
             :class="{ 'dragged' : index === draggedItem }">
      <tr>
        <td class="sequence"><img src="../assets/menu-burger.svg" alt=""> {{ item.id }}</td>
        <td class="action" @click="openPopup($event)">
          <img src="../assets/three-dots.svg" alt="">
          <div v-if="popupVisible" class="popup">
            <p>Удалить</p>
            <button @click="deleteItem(index)">Удалить</button>
          </div>
        </td>
        <td class="unit-name">
          <select @input="item.edited = true" id="select1">
            <option  v-for="(unitName, unitNameIndex) in this.unitNames" :key="unitNameIndex" :value="unitName.name">{{ unitName.name }}</option>
          </select>
        </td>
        <td class="price">
          <input type="number" v-model="item.price" @input="item.edited = true">
        </td>
        <td class="quantity">
          <input type="number" v-model="item.count" @input="item.edited = true">
        </td>
        <td class="product-name">
          <select @input="item.edited = true">
            <option  v-for="(productName, productNameIndex) in this.products" :key="productNameIndex" :value="productName.name">{{ productName.name }}</option>
          </select>
        </td>
        <td class="total">
          <input type="number" v-model="item.total" @input="item.edited = true">
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
      columns: [
        {id: 1, title: 'Номер'},
        {id: 2, title: 'Действие'},
        {id: 3, title: 'Наименование еденицы'},
        {id: 4, title: 'Цена'},
        {id: 5, title: 'Кол-во'},
        {id: 6, title: 'Название товара'},
        {id: 7, title: 'Итого'},
      ],
      products: [
        {id : 1, name: 'Мраморный щебень фр.'},
        {id : 2, name: 'Немраморный щебень фр.'},
      ],
      unitNames: [
        {id: 1, name: "Мраморный щебень фр. 2-5 мм, 25кг (белый)"},
        {id: 1, name: "Мраморный щебень фр. 2-5 мм, 25кг (вайт)"},
        {id: 1, name: "Мраморный щебень фр. 2-5 мм, 1т"},
      ],
      items: [
        {
          id: 1,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 1,
          count: 1,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
          edited: false // Добавлено свойство для редактирования
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
      draggedItem2: null,
      popupVisible: false,
      popupPosition: {top: "0", left: "0"},
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
    startDragHeader(index) {
      this.draggedItem2 = index;
    },
    overDragHeader(event) {
      event.preventDefault();
    },
    handleDropHeader(index) {
      let droppedItem = this.columns.splice(this.draggedItem2, 1)[0];
      this.columns.splice(index, 0, droppedItem);

      // cannot realize this function like for handledropheader

      // this.items.forEach(item => {
      //   // Get the data of the dropped column
      //   let temp = item[droppedItem.id];
      //
      //   // Remove the data of the dropped column from the item
      //   delete item[droppedItem.id];
      //
      //   // Insert the data of the dropped column at the new index
      //   item[droppedItem.id] = temp;
      // });

      this.draggedItem2 = null;


      console.log("but handle drop: " + index);
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
    },
    // this function has been wrote for change the width of option this is not necessary but if you want you can call it here
    // shortOption() {
    //   this.products.forEach((x) => {
    //     if(x.name.length>20) {
    //         x.name = x.name.substring(0, 20) + '...'
    //     }
    //   })
    // },
    // this function has been wrote for change the width of option this is not necessary but if you want you can call it here
    addItem() {
      this.items.push({
        id: this.items.length + 1,
        action: null,
        unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
        price: 1,
        count: 1,
        productName: "Мраморный щебень фр. 2-5 мм, 25кг",
        total: 0,
        edited: true
      });
      this.showUnitNameSelect = true
    },
  },
  created(){
    this.multiplyFunc();
  },
};
</script>


<style scoped>

.create__table button{
  width: 146px;
  height: 35px;
  padding: 10px 15px 10px 10px;
  border-radius: 5px;
  background-color: #2f8cff;
  border: none;
  font-family: MyriadPro;
  font-size: 14px;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  color: #fff;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}

.create__table img {
  margin-right: 4px;
}

.create__table {
  width: 100%;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  border-radius: 10px;
  margin: 25px 0 25px 0;
  padding: 20px 20px 20px 25px;
}


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
  resize: horizontal;
  overflow: auto;
}

::-webkit-resizer {
  display: none;
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #dddddd;
  font-family: MyriadPro;
  font-size: 16px;
  font-weight: normal;
}

input {
  border: 2px solid #cccccc;
  padding: 10px 15px;
  border-radius: 5px;
  height: 35px;
  outline: none;
  font-family: MyriadPro;
  font-size: 16px;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  color: #000;
}
.product-name select {
  width: 150px;
}

select {
  border: 2px solid #cccccc;
  padding: 0px 10px;
  border-radius: 5px;
  height: 35px;
  outline: none;
  font-family: MyriadPro;
  font-size: 16px;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  color: #000;
}
option {
  width: 150px;
}

input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0; /* Убираем пробел между внутренней и внешней стрелками */
}

/* Для Firefox */
input[type="number"] {
  -moz-appearance: textfield;
}


td.action {
  width: 80px;
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
