<template>
  <el-row v-if="!isItemListSelected">
    <el-col :span="24">
      <el-form-item class="filter-form-item input-small">
        <el-select v-model="itemListSelectedTemp"
                   placeholder="- Pilih Barang -"
                   filterable
                   clearable
                   value-key="supplier_id"
        >
          <el-option v-for="item, index in itemList" :key="index" :label="item.item_name" :value="item" />
        </el-select>
      </el-form-item>
    </el-col>
    <el-col :span="24">
      <el-button style="width: 100%" round type="primary" @click="addNewItem">Tambah Barang Baru</el-button>
    </el-col>
  </el-row>
  <el-row v-else-if="isItemListSelected" style="margin-top: 30px">
    <el-col :span="4">
      <el-form-item class="filter-form-item input-small" prop="item_name">
        <el-input ref="item_name" v-model="list.item_name" :disabled="isDisabled" placeholder="Nama Item" clearable @input="emitInput" />
      </el-form-item>
    </el-col>
    <el-col :span="7">
      <el-form-item class="filter-form-item input-small" prop="item_description">
        <el-input ref="item_description" v-model="list.item_description" placeholder="Deksripsi Item" clearable @input="emitInput" />
      </el-form-item>
    </el-col>
    <el-col :span="3">
      <el-form-item class="filter-form-item input-small" prop="item_quantity">
        <el-input ref="item_quantity" v-model="list.item_quantity" type="number" placeholder="Kuantitas Item" clearable @input="emitInput" />
      </el-form-item>
    </el-col>
    <el-col :span="3">
      <el-form-item class="filter-form-item input-small" prop="item_purchase_price">
        <el-input ref="item_purchase_price" v-model="list.item_purchase_price" :disabled="isDisabled" placeholder="Harga Beli" clearable @input="emitInput" />
      </el-form-item>
    </el-col>
    <el-col :span="3">
      <el-form-item class="filter-form-item input-small" prop="item_sell_price">
        <el-input ref="item_sell_price" v-model="list.item_sell_price" type="number" placeholder="Harga Jual" clearable @input="emitInput" />
      </el-form-item>
    </el-col>
    <el-col :span="2">
      <p class="item-total">{{ list.item_total_price }}</p>
    </el-col>
    <el-col :span="2">
      <p class="item-total">
        <el-button class="table-icon-button danger" @click="removeItem(index)"><i class="el-icon-delete" />
        </el-button>
      </p>
    </el-col>
  </el-row>
</template>

<script>
export default {
  name: 'FormItem',
  props: {
    item_name: {
      type: String,
      default: ''
    },
    item_description: {
      type: String,
      default: ''
    },
    item_quantity: {
      type: String,
      default: ''
    },
    item_purchase_price: {
      type: String,
      default: ''
    },
    item_sell_price: {
      type: String,
      default: ''
    },
    item_total: {
      type: String,
      default: ''
    },
    isSupplierListSelected: {
      type: Boolean,
      default: false
    },
    item_key: {
      type: Number
    }
  },
  data() {
    return {
      itemList: [],
      isItemListSelected: false,
      list: {
        item_name: this.item_name,
        item_description: this.item_description,
        item_quantity: this.item_quantity,
        item_purchase_price: this.item_purchase_price,
        item_sell_price: this.item_sell_price,
        item_total_price: 0,
        item_status: ''
      },
      itemListSelectedTemp: '',
      isDisabled: false
    }
  },
  watch: {
    // whenever supplierListSelected question changes, this function will run
    itemListSelectedTemp() {
      if (this.itemListSelectedTemp != '') {
        this.list.item_name = this.itemListSelectedTemp.item_name
        this.list.item_description = this.itemListSelectedTemp.item_description
        this.list.item_quantity = 1
        this.list.item_purchase_price = this.itemListSelectedTemp.item_purchase_price
        this.list.item_sell_price = this.itemListSelectedTemp.item_sell_price
        this.list.item_total_price = parseInt(this.itemListSelectedTemp.item_sell_price) * 1
        this.list.item_status = 'ready'
        this.isDisabled = true
        this.isItemListSelected = true
        this.emitInput()
      } else {
        this.isItemListSelected = false
      }
    }
  },
  created() {
    this.getItemList()
  },
  methods: {
    getItemList() {
      this.itemList = [
        {
          item_name: 'Pulpen',
          item_description: 'Mantab dah pokoknya',
          item_sell_price: 10000,
          item_purchase_price: 9000
        },
        {
          item_name: 'Buku',
          item_description: 'Mantab dah pokoknya',
          item_sell_price: 2000,
          item_purchase_price: 1500
        }
      ]
    },
    addNewItem() {
      this.isItemListSelected = true
      this.list.item_status = 'new'
    },
    removeItem(index) {
      this.$emit('remove-item', index)
      this.itemList.splice(index, 1)
    },
    emitInput() {
      const total = parseInt(this.list.item_quantity) * parseInt(this.list.item_sell_price)
      this.list.item_total_price = total || 0
      this.$emit('input', {
        item_key: this.item_key,
        item_name: this.list.item_name,
        item_description: this.list.item_description,
        item_quantity: this.list.item_quantity,
        item_purchase_price: this.list.item_purchase_price,
        item_sell_price: this.list.item_sell_price,
        item_total_price: this.list.item_total_price,
        item_status: this.list.item_status
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.item-total {
    text-align: right;
}
</style>
