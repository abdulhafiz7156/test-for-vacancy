<template>
  <div class="table">
    <table>
      <thead>
      <tr>
        <th class="id__and__draggable"></th>
        <th class="action">Действие</th>
        <th class="unit-name">Наименование еденицы</th>
        <th class="price">Цена</th>
        <th class="quantity">Кол-во</th>
        <th class="product-name">Название товара</th>
        <th class="total">Итого</th>
      </tr>
      </thead>
      <tbody v-for="(item, index) in this.items" :key="index" draggable="true"
             @dragstart="startDrag(index)" @dragover="overDrag($event)" @drop="handleDrop(index)"
             @dragend="handleDragEnd" :class="{ 'dragged' : index === this.draggedItem }">
      <tr>
        <td class="sequence"><img src="../assets/32143241223Combined%20Shape.svg" alt=""> {{ item.id }}</td>
        <td class="action">
          <button>Удалить</button>
        </td>
        <td class="unit-name">{{ item.unitName }}</td>
        <td class="price">{{ item.price }}</td>
        <td class="quantity">{{ item.count }}</td>
        <td class="product-name">{{ item.productName }}</td>
        <td class="total">{{ item.total }}</td>
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
        },
        {
          id: 2,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 2,
          count: 2,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
        },
        {
          id: 3,
          action: null,
          unitName: "Мраморный щебень фр. 2-5 мм, 25кг",
          price: 3,
          count: 3,
          productName: "Мраморный щебень фр. 2-5 мм, 25кг",
          total: 0,
        },
      ],
      draggedItem: null,
    }
  },
  methods: {
    multiplyFunc() {
      this.items.forEach(item => {
        item.total = item.price * item.count
      })
    },
    startDrag(index) {
      this.draggedItem = index;
      console.log("but start drag: " + index)
    },
    overDrag(event) {
      event.preventDefault();
      // console.log("but over drag: " + event)
    },
    handleDrop(index) {
      let droppedItem = this.items.splice(this.draggedItem, 1)[0]
      this.items.splice(index, 0, droppedItem)
      this.draggedItem = null
      this.items.forEach((item, i) => {
        item.id = i + 1;
      });
      this.draggedItem = null

      console.log("but handle drop: " + index)
    },
    handleDragEnd() {
    },
  },
  created() {
    this.multiplyFunc()
  },

  components: {}
}
</script>

<style scoped>
.table {
  width: 100%;
  height: 100%;
  margin: 25px 0 175px 0;
  padding: 9px 1px 25px;
  border-radius: 10px;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  background-color: #fff;
}

table {
  width: 100%;
  border-collapse: collapse;
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
  cursor: pointer;
}

.dragged {
  border: 2px #a6b7d4 dashed;
  border-radius: 5px;
}
</style>
