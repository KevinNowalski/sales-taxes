<template>
  <div class="basket">
    <h1>Basket</h1>
    <div id="main">
      <div id="select-quantity">
        <div id="selectItem">
          <h1>Select item:</h1>
          <select name="items" id="items" v-model="selected">
            <option v-for="item in items" :value="item.val" :key="item.id" @click="addPrice(item.itemPrice)">
              {{ item.val }}
            </option>
          </select>
          <br><br>
        </div>
        <div id="quantity">
          <h1>Quantity:</h1>
          <input v-model="qty" type="number" />
          <br><br><br>
        </div>
        <div id="itemsAdded">
          <button @click="addItem()" :disabled="qty < 1">Add item</button><br><br>
            <div v-if="itemsAdded.length !== 0">
              <h3>Items Added:</h3>
              <div v-for="item in itemsAdded" :key="item.itemName" class="items-added">
                Item: {{item.itemName}} &nbsp; &nbsp; Quantity: {{item.qtyAdded}} &nbsp; &nbsp; Price: {{item.priceAdded}} <br>
              </div>
            </div>
            <br><br>
          <button @click="calculate()" :disabled="qty < 1">Calculate</button>
        </div>
      </div>
      <div v-show="showReceipt" class="receipt">
        <h1>Receipt:</h1>
        <div v-if="itemsAdded.length !== 0" class="receipt-detail">
          <div v-for="(itemAdded, index) in itemsAdded" :key="index">
            <div v-if ="itemAdded.qtyAdded > 1" >
              {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}} ({{itemAdded.qtyAdded}} @ {{itemAdded.priceAdded}})<br>
            </div>
            <div v-else>
              {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}}<br>
            </div>
          </div>
          <!-- Sales Taxes: {{ salesTax }}<br> -->
          Total: &nbsp;{{ totalCost }}
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'Basket',
   data() {
    return {
      // book: 12.49,
      // musicCD: 16.49,
      // chocolateBar: 0.85,
      // itemPrice: '' as any | number | string,
      salesTax: '' as any | number | string,
      totalCost: '' as any | number | string,
      // totalItemsCost: '' as any | number | string,
      itemsAdded: [] as any,
      selected: 'Book',
      qty: '' as any | number | string,
      selectedPrice: 12.49,
      selectedTotal: '' as any | number | string,
      items: {
        1: {id: 1, val: 'Book', itemPrice: 12.49},
        2: {id: 2, val: 'Music CD', itemPrice: 16.49},
        3: {id: 3, val: 'Chocolate Bar', itemPrice: 0.85},
      },
      showReceipt: false
    }
  },
  methods: {
    addPrice(addPrice: number){
      this.selectedPrice = addPrice
    },
    addItem(){
      this.showReceipt = false;
      if(this.itemsAdded.length === 0){
        this.itemsAdded.push({itemName: this.selected, qtyAdded: this.qty, priceAdded: this.selectedPrice, totalAdded: this.selectedTotal})
        console.log('Items '+JSON.stringify(this.itemsAdded))
      }else{
        var result = this.itemsAdded.find((e: { itemName: any; }) => e.itemName === this.selected)
        if(result){
          result.qtyAdded = +result.qtyAdded + +this.qty;
        }else{
        if(!this.itemsAdded.find((e: { itemName: any; }) => e.itemName === this.selected)){
          this.itemsAdded.push({itemName: this.selected, qtyAdded: this.qty, priceAdded: this.selectedPrice, totalAdded: this.selectedTotal})
          console.log('Items '+JSON.stringify(this.itemsAdded))
          }
        }
      }
    },
    calculate(){
      this.showReceipt = true;
      this.itemsAdded.forEach((itemAdded: any) => {
        itemAdded.selectedTotal = itemAdded.qtyAdded * itemAdded.priceAdded
      })
      this.totalCost = 0
      this.itemsAdded.forEach((itemAdded: any) => {
        this.totalCost = (+itemAdded.selectedTotal + +this.totalCost).toFixed(2);
      })
      console.log('Items '+JSON.stringify(this.itemsAdded))
    }
  }
});
</script>