<script setup>
import { ref, reactive } from 'vue'
import { currencies } from './data/currencies'
const subTotal = ref('0.00')
const invoiceData = reactive({
    title: 'INVOICE',
    invoiceNumber: '',
    basicInfos: {
          date: {
            key: 'Date',
            value: '',
          },
          paymentTerms: {
            key: 'Payment Terms',
            value: ''
          },
          dueDate: {
            key: 'Due Date',
            value: ''
          },
          poNumber: {
            key: 'PO Number',
            value: 0
          } 
        },
    from: '',
    currency: '৳',
    billTo: '',
    shipTo: '',
    items: [{
        amount: 0,
        quantity: 1,
        rate: 0,
        description: ''
    }],
    notes: {
        key: 'Notes',
        value: ''
    },
    terms: {
        key: 'Terms',
        value: ''
    },
    subTotal: {
        key: 'Sub Total',
        value: 0
    },
    extras: [],
    total: {
        key: 'Total',
        value: 0
    },
    amountPaid: {
        key: 'Amount Paid',
        value: 0
    },
    balanceDueKey: 'Balance Due',
});
const addNewItem = () => {
    invoiceData.items.push({
        amount: 0,
        quantity: 1,
        rate: 0,
        description: ''
    })
}
const removeItem = (index) => {
    invoiceData.items.splice(index, 1);
}
const addExtra = (keyName, idNumber) => {
    invoiceData.extras.push({
       key: keyName,
       value: 0,
       id: idNumber,
    })
}
const removeExtra = (index) => {
    invoiceData.extras.splice(index, 1);
}
const getSubtotal = () => {
    let subtotal = 0;
    invoiceData.items.forEach( item => {
        subtotal += item.amount;
    })
    invoiceData.subTotal.value = parseInt(subtotal);
    return subtotal;
}
const getTotal = () => {
    let extraTotal = 0;
    let total = 0;
    const discountAmount = invoiceData.extras.find(item => item.id ===1)
    if(discountAmount){
        extraTotal = invoiceData.extras.reduce((acc, item) => {
            if(item.id !== 1){
                return acc + item.value;
            }
            return acc;
        }, 0) - discountAmount.value;
    } else {
        extraTotal = invoiceData.extras.reduce((acc, item) => acc + item.value, 0)
    }
    total = parseInt(invoiceData.subTotal.value) + parseInt(extraTotal);
    invoiceData.total.value = total;
    return total;
}
const balanceDue = () => {
    return invoiceData.total.value - invoiceData.amountPaid.value
}
</script>

<template>
    <div class="container max-w-[600px] py-5 px-5 my-5 mx-auto rounded border-2 border-inherit md:max-w-[800px] lg:max-w-[1000px]">
        <form>
            <div class="flex flex-col justify-between gap-8 lg:flex-row-reverse mb-2">
                <div class="flex flex-col justify-between basis-1/2">
                    <div class="flex flex-col lg:items-end">
                        <input v-model="invoiceData.title" type="text" class="text-gray-900 rounded-lg outline-none placeholder-gray-600 block w-full p-2.5 text-3xl md:text-3.5xl lg:text-4xl font-semibold mb-2 lg:text-end">
                        <div class="flex">
                            <span class="inline-flex items-center px-3 text-sm text-gray-900 bg-gray-200 border rounded-e-0 border-gray-300 rounded-s-md">#</span>
                            <input v-model="invoiceData.invoiceNumber" type="text" class="rounded-none rounded-e-lg w-26 bg-gray-50 border text-gray-900 focus:ring-blue-500 focus:border-blue-500 text-sm border-gray-300 p-2.5 outline-none">
                        </div>
                    </div>
                    <div class="flex flex-col gap-1 mt-5">
                       <div class="flex justify-end">
                        <input type="text" v-model="invoiceData.basicInfos.date.key"
                                class="focus:bg-gray-50 focus:border  text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500 p-2.5 text-end">
                                <input type="date"  v-model="invoiceData.basicInfos.date.value"
                                class="w-40 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500  px-2">
                       </div> 
                       <div class="flex justify-end">
                        <input type="text" v-model="invoiceData.basicInfos.paymentTerms.key"
                                class="focus:bg-gray-50 focus:border  text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500 p-2.5 text-end">
                                <input type="text"  v-model="invoiceData.basicInfos.paymentTerms.value"
                                class="w-40 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500  px-2">
                       </div>
                       <div class="flex justify-end">
                        <input type="text" v-model="invoiceData.basicInfos.dueDate.key"
                                class="focus:bg-gray-50 focus:border  text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500 p-2.5 text-end">
                                <input type="date" v-model="invoiceData.basicInfos.dueDate.value"
                                class="w-40 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500  px-2">
                       </div>
                       <div class="flex justify-end">
                        <input type="text" v-model="invoiceData.basicInfos.poNumber.key"
                                class="focus:bg-gray-50 focus:border  text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500 p-2.5 text-end">
                                <input type="text"  v-model="invoiceData.basicInfos.poNumber.value"
                                class="w-40 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg placeholder-gray-600 focus:placeholder-gray-500 outline-none focus:border-blue-500  px-2">
                       </div>
                    </div>
                </div>
                <div class="flex flex-col gap-5 basis-1/2 lg:justify-between">
                    <div class="flex flex-col gap-5">
                    <input type="file" placeholder="+ Add your logo">
                    <textarea v-model="invoiceData.from" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg  outline-none focus:placeholder-gray-500 focus:border-blue-500 p-2.5" placeholder="Who is the invoice from? (required)"></textarea>
                    </div>
                    <!-- ====Select Currency==== -->
                    <div>
                        <label class="block mb-2 text-sm font-medium text-gray-900 dark:text-white" for="currency">Currency</label>
                        <select v-model="invoiceData.currency" class="select select-primary w-full max-w-xs px-2 py-2 outline-none border rounded-lg" id="currency">
                            <option v-for="(currency, key, index) in currencies" :key="index" :value="currency.symbol">{{ currency.name }} ({{ key }})</option>
                        </select>
                    </div>
                     <!-- ====Select Currency==== -->
                    <div class="flex flex-col gap-5 md:flex-row">
                        <div class="basis-1/2">
                            <label class="block mb-2 text-sm font-medium text-gray-900 dark:text-white" for="bill-to">Bill to</label>
                            <textarea v-model="invoiceData.billTo" class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg  focus:placeholder-gray-500 outline-none focus:border-blue-500 block w-full p-2.5" id="bill-to" placeholder="Who is the invoice to? (required)"></textarea>
                        </div>
                        <div class="basis-1/2">
                            <label class="block mb-2 text-sm font-medium text-gray-900 dark:text-white" for="ship-to">Ship to</label>
                            <textarea v-model="invoiceData.shipTo" type="text"
                                class="bg-gray-50 border border-gray-300  text-sm rounded-lg  text-gray-900 outline-none focus:placeholder-gray-500 focus:border-blue-500 block w-full p-2.5" id="ship-to" placeholder="(optional)"></textarea>
                        </div>
                    </div>
                </div>  
            </div>
            <!-- ========Items Start======== -->
            <div class="hidden md:flex mt-7 mb-0 px-3 py-1 rounded justify-between text-white bg-neutral-950">
              <div class="md:w-3/5">Item</div>
              <div class="flex">
                <div class="w-32 px-2 text-start">Rate</div>
                <div class="w-32 px-3 text-start">Quantity</div>
                <div class="w-20 text-end">Amount</div>
              </div>
            </div>
            <div v-show="invoiceData.items.length > 0" v-for="(item, index) in invoiceData.items" :key="index" class="parent relative mb-2 md:mb-0">
                <span @click="removeItem(index)" class="absolute hidden child right-2 p-2 cursor-pointer hover:text-orange-700 md:-right-2 md:top-0.5">x</span>
                <div class="flex flex-col border rounded-lg p-4 md:p-0 md:flex-row-reverse md:justify-between md:border-0">
                    <div class="md:flex md:flex-row-reverse md:items-center">
                    <!-- Item amount -->
                   <div class="md:w-20 md:text-end md:pr-3"><span class="md:hidden">Amount: </span>{{ invoiceData.currency }}{{ item.amount = item.rate * item.quantity }}</div>
                   <div class="mt-2 flex md:gap-2">
                    <div class="flex gap-2 w-32 px-2 py-1 rounded-lg border items-center md:p-2"><span>{{ invoiceData.currency }}</span><input v-model="item.rate" class="outline-none w-[80px]" type="number" min="1" placeholder="Rate"></div>
                    <div class="mx-3 md:hidden">x</div>
                    <div class="border w-32 rounded-lg py-1 md:p-2"><input v-model="item.quantity" class="outline-none px-1 w-24" placeholder="Quantity" type="number" required min="1"></div>
                   </div>
                </div>
                   <div class="mt-2 md:w-3/5"><input v-model="item.description" class="outline-none p-2 border focus:border-blue-500  rounded-lg w-full" placeholder="Describe service or product..." type="text"></div>
                </div>
            </div>
            <div v-show="invoiceData.items.length <= 0" class="py-3"><h3 class="text-red-500">No item added. Please add a new one!</h3></div>
            <div><button @click.prevent="addNewItem()" class="bg-blue-700 mt-2 rounded py-1.5 px-5 text-white">+ Add item</button></div>
            <!-- ========Items End======== -->
            <div class="mt-10 flex flex-col md:flex-row">
                <div class="basis-1/2">
                    <input v-model="invoiceData.notes.key" type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2">
                    <textarea v-model="invoiceData.notes.value" class="w-full border py-1 px-2 rounded-lg outline-none focus:border-blue-500  text-gray-900" placeholder="Notes- any relevant information not already covered"></textarea>
                    <input v-model="invoiceData.terms.key" type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2" value="Terms">
                    <textarea v-model="invoiceData.terms.value" class="w-full border py-1 px-2 rounded-lg outline-none focus:border-blue-500  text-gray-900" placeholder="Terms and conditions - late fees, payment methods, delivery schedule"></textarea>
                </div>
                <div class="basis-1/2 flex flex-col mt-10 pr-5">
                    <!-- ==Subtotal Start== -->
                    <div class="flex justify-end gap-2">
                        <input type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2 text-end" v-model="invoiceData.subTotal.key">
                        <span class="w-32 text-end">{{ invoiceData.currency }}{{ getSubtotal() }}</span>
                    </div>
                    <!-- ==Subtotal End== -->
                    <!-- ==Add Extra Start== -->
                    <div v-for="(extra, index) in invoiceData.extras" :key="index" class="flex justify-end gap-2 parent relative">
                        <span @click="removeExtra(index)" class="absolute hidden child -right-5 top-[-5px] p-2 cursor-pointer hover:text-orange-700 md:-right-6">x</span>
                        <input v-model="extra.key" type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2 text-end">
                        <input v-model="extra.value" type="number" min="0" class="rounded-lg outline-none border py-1 px-2 mb-2 w-32">
                    </div>
                    <div class="flex justify-end gap-6 my-2">
                        <button :class="{hidden: invoiceData.extras.find(item=>item.id === 1)}" @click.prevent="addExtra('Discount(-)', 1)" class="text-green-600 font-bold">+ Discount</button>
                        <button :class="{hidden: invoiceData.extras.find(item=>item.id===2)}"  @click.prevent="addExtra('Tax(+)', 2)" class="text-green-600 font-bold">+ Tax</button>
                        <button :class="{hidden: invoiceData.extras.find(item=>item.id===3)}"  @click.prevent="addExtra('Shipping(+)', 3)"  class="text-green-600 font-bold">+ Shipping</button>

                    </div>
                    <!-- ==Add Extra End== -->
                    <!-- ==Total Start== -->
                    <div class="flex justify-end gap-2">
                        <input v-model="invoiceData.total.key" type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2 text-end font-bold">
                        <span class="w-32 text-end font-bold">{{ invoiceData.currency }}{{ getTotal() }}</span>
                    </div>
                    <!-- ==Total End== -->
                    <div class="flex justify-end gap-2">
                        <input v-model="invoiceData.amountPaid.key" type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2 text-end">
                        <input v-model="invoiceData.amountPaid.value"  type="number" min="1" class="rounded-lg outline-none border py-1 px-2 mb-2 w-32">
                    </div>
                    <div class="flex justify-end gap-2">
                        <input v-model="invoiceData.balanceDueKey"  type="text" class="rounded-lg outline-none focus:border py-1 px-2 mb-2 text-end text-red-500 font-bold">
                        <span class="w-32 text-end text-red-500 font-bold">{{ invoiceData.currency }}{{ balanceDue() }}</span>
                    </div>
                </div>
            </div>
            <div class="flex justify-end gap-3">
                <button class="bg-green-700 flex mt-2 rounded py-1.5 px-5 text-white"> Download Invoice</button>
                <button class="bg-red-700 mt-2 rounded py-1.5 px-5 text-white">Reset</button>
            </div>
        </form>
    </div>
</template>

<style scoped>
.parent:hover .child{
    display: block;
}
</style>