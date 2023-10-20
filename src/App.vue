

<template>
  <main>
    <div class="cardSection">
      <div class="backCard">
        <p class="cvc">{{ cvc || '000' }}</p>
      </div>
      <div class="frontCard">
        <img class="card_logo" src="./assets/images/card-logo.svg" alt="card_logo"/>
        <p class="cardNumber">{{ cardNumber || '0000 0000 0000 0000' }}</p>
        <p class="name">{{ name || 'Jane Appleseed' }}</p>
        <p class="expiredInfo">{{ `${expMonth || '00'}/${expYear || '00'}` }}</p>
      </div>
    </div>
    <div class="inputSection">
      <form v-if="!successSubmit" action="#">
        <div class="formItem">
          <label for="cardholder">CARDHOLDER NAME</label>
          <input :class="{error : errorList.includes('name')}" type="text" id="name" v-model="name" name="cardholder" placeholder="E.g. Jane Appleseed" @input="formatChar">
          <p class="errorText" id="error_name">{{ errorText.name }}</p>
        </div>
        <div class="formItem">
          <label for="cardNumber">CARD NUMBER</label>
          <input :class="{error : errorList.includes('cardNumber')}" id="cardNumber" v-model="cardNumber" type="text" name="cardNumber" placeholder="E.g. 1234 5678 9123 0000" @input="formatCardNumber">
          <p class="errorText" id="error_cardNumber">{{ errorText.cardNumber }}</p>
        </div>
        <div class="formItem">
          <div class="subFormItem">
            <div class="formWrap">
              <label class="expired">EXP. DATE (MM/YY)</label>
              <label class="cvc">CVC</label>
              <div class="month">
                <input :class="{error : errorList.includes('monthYear')}" type="text" id="month" placeholder="MM" v-model="expMonth" maxlength="2" @input="formatNumber">
                <p class="errorText" id="errorMonthYear">{{ errorText.monthYear }}</p>
              </div>
              <div class="year">
                <label for=""></label>
                <input :class="{error : errorList.includes('monthYear')}" type="text" id="year" placeholder="YY" v-model="expYear" maxlength="2" @input="formatNumber">
              </div>
              <div class="cvc">
                <input :class="{error : errorList.includes('cvc')}" type="text" name="cvc" id="cvc" v-model="cvc" placeholder="e.g. 123" maxlength="3" @input="formatNumber">
                <p class="errorText" id="error_cvc">{{ errorText.cvc }}</p>
              </div>
            </div>
          </div>
        </div>
        <button @click="submitCardInfo" class="submit_btn" type="submit">Confirm</button>
      </form>
      <Transition>
        <div class="successContainer" v-if="successSubmit">
          <img src="./assets/images/icon-complete.svg" />
          <h2>THANK YOU</h2>
          <p>We've added your card details</p>
          <button class="submit_btn">Continue</button>
        </div>
      </Transition>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
const cardNumber = ref('');
const name = ref('');
const cvc = ref('');
const expMonth = ref('');
const expYear = ref('');
const errorList = ref([]);
const errorText = {
  name: '',
  cardNumber: '',
  cvc: '',
  monthYear: ''
}
const currentYear = new Date().getFullYear();
const formattedYear = currentYear.toString().substr(-2);
const successSubmit = ref(false);
const formatCardNumber = () => {
  cardNumber.value = cardNumber.value.replace(/[^\dA-Z]/g, '').replace(/(.{4})/g, '$1 ').trim();
  if (cardNumber.value.length > 19) {
    cardNumber.value = cardNumber.value.substr(0, 19);
  }
}
const formatChar = () => {
  // format non number
  name.value = name.value.replace(/[^a-zA-Z\s]/g, '');
}
const formatNumber = () => {
  // format only number
  expMonth.value = expMonth.value.replace(/[^\dA-Z]/g, '');
  expYear.value = expYear.value.replace(/[^\dA-Z]/g, '');
  cvc.value = cvc.value.replace(/[^\dA-Z]/g, '');
}
const submitCardInfo = (e) => {
  e.preventDefault();
  if (!name.value) {
    errorText.name = 'Please fill in the name';
    errorList.value.push('name');
  } 
  if (!cardNumber.value) {
    errorText.cardNumber = 'Please fill in the card number';
    errorList.value.push('cardNumber');
  }
  if (!cvc.value) {
    errorText.cvc = 'Please fill in the cvc';
    errorList.value.push('cvc');
  }
  if (!expMonth.value || !expYear.value || parseInt(expMonth.value) > 12 || parseInt(expYear.value) < parseInt(formattedYear)) {
    errorText.monthYear = 'Please fill in the correct month and year';
    errorList.value.push('monthYear');
  }
  const valid = 
    name.value &&
    cardNumber.value &&
    cvc.value &&
    (expMonth.value && parseInt(expMonth.value) < 13) &&
    (expYear.value && parseInt(expYear.value) > parseInt(formattedYear));

  if(valid) {
    errorText.name = '';
    errorText.cardNumber = '';
    errorText.cvc = '';
    errorText.monthYear = '';
    errorList.value = [];
    successSubmit.value = true;
  }
}
</script>
