<template>
  <div class="shop">
    <h1>Shop</h1>
    <div id="main">
      <div id="select-quantity">
        <div id="selectItem">
          <h1>Select item:</h1>
          <select name="items" id="items" v-model="selected">
            <option v-for="item in items" :value="item.val" :key="item.id" @click="addInfo(item.itemPrice,item.itemType,item.itemSource)">
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
                {{item.qtyAdded}} {{item.itemName}} at {{item.priceAdded}}<br>
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
            <div v-if="itemAdded.itemSourceAdded === 'Import' && itemAdded.qtyAdded > 1">
              Imported {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}} ({{itemAdded.qtyAdded}} @ {{itemAdded.priceAdded}})<br>
            </div>
            <div v-else-if="itemAdded.itemSourceAdded === 'Import'">
              Imported {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}}<br>
            </div>
            <div v-else-if="itemAdded.qtyAdded > 1">
              {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}} ({{itemAdded.qtyAdded}} @ {{itemAdded.priceAdded}})<br>
            </div>
            <div v-else>
              {{itemAdded.itemName}}: &nbsp;{{itemAdded.selectedTotal}}<br>
            </div>
          </div>
          Sales Taxes: &nbsp;{{ salesTax }}<br>
          Total: &nbsp;{{ grandTotal }}
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'Shop',
   data() {
    return {
      itemsAdded: [] as any,
      selected: 'Book',
      qty: '' as any | number | string,
      selectedPrice: 12.49,
      itemType: '' as any | number | string,
      itemSource: '' as any | number | string,
      selectedTotal: '' as any | number | string,
      salesTax: '' as any | number | string,
      importTax: '' as any | number | string,
      totalCost: '' as any | number | string,
      grandTotal: '' as any | number | string,
      items: {
        1: {id: 1, val: 'War and Peace', itemPrice: 10.79, itemType: 'Book', itemSource: "Domestic"},
        2: {id: 2, val: 'Drogas Wave', itemPrice: 16.59, itemType: 'Music CD', itemSource: "Domestic"},
        3: {id: 3, val: 'Theo Dark Chocolate', itemPrice: 2.59, itemType: 'Food', itemSource: "Domestic"},
        4: {id: 4, val: 'Tension Headache Relief', itemPrice: 2.49, itemType: 'Medical', itemSource: "Domestic"},
        5: {id: 5, val: 'Etokki Fight Stick', itemPrice: 209.95, itemType: 'Video Games', itemSource: "Import"},
        6: {id: 5, val: 'Babolat Pure Drive', itemPrice: 229, itemType: 'Tennis Racquet', itemSource: "Domestic"},
        7: {id: 7, val: 'USB-C Cable', itemPrice: 5.60, itemType: 'Adapter', itemSource: "Domestic"},
      },
      showReceipt: false
    }
  },
  methods: {
    addInfo(addPrice: any,addType: any,addSource: any){
      this.selectedPrice = addPrice
      this.itemType = addType
      this.itemSource = addSource
    },
    addItem(){
      this.showReceipt = false;
      // If array empty add item
      if(this.itemsAdded.length === 0){
        this.itemsAdded.push({itemName: this.selected, qtyAdded: this.qty, priceAdded: this.selectedPrice, totalAdded: this.selectedTotal, itemTypeAdded: this.itemType, itemSourceAdded: this.itemSource})
        console.log('Items '+JSON.stringify(this.itemsAdded))
      }else{
        var result = this.itemsAdded.find((e: { itemName: any; }) => e.itemName === this.selected)
        // If item in array add quantity
        if(result){
          result.qtyAdded = +result.qtyAdded + +this.qty;
        // Else if item not in array add item
        }else{
        if(!this.itemsAdded.find((e: { itemName: any; }) => e.itemName === this.selected)){
          this.itemsAdded.push({itemName: this.selected, qtyAdded: this.qty, priceAdded: this.selectedPrice, totalAdded: this.selectedTotal, itemTypeAdded: this.itemType, itemSourceAdded: this.itemSource})
          console.log('Items '+JSON.stringify(this.itemsAdded))
          }
        }
      }
    },
    calculate(){
      this.showReceipt = true;
      this.itemsAdded.forEach((itemAdded: any) => {
      // Calculate item cost with quantity
        itemAdded.selectedTotal = (itemAdded.qtyAdded * itemAdded.priceAdded).toFixed(2);
      })

      this.salesTax = 0;
      this.itemsAdded.forEach((itemAdded: any) => {
        var type = String(itemAdded.itemTypeAdded)
        if(type !== "Book" && type !== "Food" && type !== "Medical"){
          // Calculate item sales tax
          this.salesTax = Math.round((.1 * itemAdded.selectedTotal) * 5) / 5;
        }
        console.log("The sales tax is "+this.salesTax)
      })

      this.importTax = 0;
      this.itemsAdded.forEach((itemAdded: any) => {
        var source = String(itemAdded.itemSourceAdded)
        if(source !== "Domestic"){
          // Calculate item import tax
          this.importTax = (.05 * itemAdded.selectedTotal).toFixed(2);
          console.log('Import tax '+ this.importTax)
        }
      })
      
      this.totalCost = 0;
      this.itemsAdded.forEach((itemAdded: any) => {
      // Calculate item total cost
        this.totalCost = (+itemAdded.selectedTotal + +this.totalCost).toFixed(2);
      })

      // Calculate the grand total
      this.grandTotal = (+this.salesTax + +this.totalCost + +this.importTax).toFixed(2);

    }
  }
});
</script>