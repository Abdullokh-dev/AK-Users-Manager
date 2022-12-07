<template>
  <div class="row">
    <div class="col">
      <!-- Modal -->
      <div v-if="this.isModalActive" class="row">
        <div class="col">
          <div class="type-undo"></div>
        </div>
      </div>
      <div v-if="this.isModalActive" class="row d-flex justify-content-center">
        <div class="col modal-box">
          <div class="row text-center">
            <div class="col-12">
              <div class="modal-text"> Вы уверены, что хотите удалить пользователя?</div>
            </div>
            <div class="col-6 pb-4">
              <button class="confirm-btn" @click="removeElement">Да</button>
            </div>
            <div class="col-6">
              <button class="undo-btn" @click="closeModal">Нет</button>
            </div>
          </div>
        </div>
      </div>
      <!-- Header -->
      <div class="row mt-3 d-flex justify-content-center">
        <div class="col-11 ps-0">
          <h1>Список пользователей</h1>
        </div>
      </div>

      <div class="row mt-3 d-flex justify-content-center">
        <div class="col-11 p-3 bg-white search-box">
          <input v-model="searchValue" placeholder="Поиск по имени или e-mail" type="text">
          <div v-if="filtering" class="mt-4">
            <img alt="#" src="../assets/clear.svg">
            <span class="ps-2 clear-text" @click="unFilter">Очистить фильтр</span>
          </div>
        </div>
      </div>
      <!-- sorting row -->
      <div class="row mt-5 mb-3 d-flex justify-content-center">
        <div class="col-11 ps-0 d-flex">
          <span class="sort-text">Сортировка:</span>
          <button id="sort-btn" :class="this.isSortedByDate ? 'selected' : 'unselected'" @click="sortByDate"> Дата регистрации</button>
          <button :class="this.isSortedByRate ? 'selected' : 'unselected'" @click="sortByRate"> Рейтинг</button>
        </div>
      </div>
      <!-- Table -->
      <div class="row mb-5 d-flex justify-content-center">
        <div class="col-11 table-box">
          <!-- table header -->
          <div class="row px-3 tablet-names">
            <div class="col-6 col-lg-3 ps-0">Имя пользователя</div>
            <div class="col-6 col-lg-3">E-mail</div>
            <div class="col-6 col-lg-3 ps-0">Дата регистрации</div>
            <div class="col-6 col-lg-3">Рейтинг</div>
          </div>
          <!-- users list -->
          <div v-for="user in userList" :key="user.id" class="row px-3">
            <hr class="top-hr"/>
            <div class="col-6 col-lg-3 ps-0">
              <div class="username"> {{ user.username }}</div>
            </div>
            <div class="col-6 col-lg-3">
              <div class="email"> {{ user.email }}</div>
            </div>
            <div class="ps-0 col-6 col-lg-3">
              <div class="date"> {{ user.registration_date.slice(0, 10).replaceAll('-', '.') }}</div>
            </div>
            <div class="col col-lg-auto me-lg-auto">
              <span class="rating"> {{ user.rating }} </span>
            </div>
            <div class="col-1 col-lg-auto">
              <svg class="del-btn" fill="none" height="15" viewBox="0 0 24 24" width="15" xmlns="http://www.w3.org/2000/svg" @click="showDelModal(user.id)">
                <path d="M4 4L20 20" stroke="#000000" stroke-linecap="round" stroke-width="4.5"/>
                <path d="M4 20L20 4" stroke="#000000" stroke-linecap="round" stroke-width="4.5"/>
              </svg>
            </div>
            <hr class="bottom-hr"/>
          </div>
        </div>
      </div>
      <!-- pagination -->
      <div class="row pb-5">
        <div class="col d-flex justify-content-center">
          <span class="pe-3 btn-prev" @click="prevPage"> &#8810; </span>
          <button v-for="n in countOfPage" id="btn-page" :key="n" :class="{'active-page' : currentPage === n}" @click="changePage(n)">
            {{ n }}
          </button>
          <span class="ps-3 btn-prev" @click="nextPage"> &#8811; </span>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'HelloWorld',
  data() {
    return {
      users: [],
      searchValue: '',
      sortedDateAsc: true,
      sortedRateAsc: true,
      filtering: false,
      isSortedByDate: false,
      isSortedByRate: false,
      countOfPage: 1,
      currentPage: 1,
      usersInPage: [],
      isModalActive: false,
      selectedItem: null,
      page: 0,
      record_per_page: 5
    }
  },
  methods: {
    fetchUsers() {
      axios.get("https://5ebbb8e5f2cfeb001697d05c.mockapi.io/users")
        .then((response) => this.users = response.data)
        .then(() => {
          this.usersInPage = this.users.slice()
        })
        .then(() => {
          this.countOfPage = Math.ceil(this.users.length / 5)
        })
    },
    showDelModal(id) {
      this.isModalActive = true
      this.selectedItem = id
    },
    closeModal() {
      this.isModalActive = false
      this.selectedItem = null
    },
    removeElement() {
      this.users.forEach((item, index) => {
        if (item.id === this.selectedItem) {
          this.users.splice(index, 1)
        }
      })
      this.usersInPage.forEach((item, index) => {
        if (item.id === this.selectedItem) {
          this.usersInPage.splice(index, 1)
        }
      })
      this.closeModal()
      if (this.userList.length === 0 && this.countOfPage === 1) {
        this.countOfPage = 1
        this.currentPage = 1
      }
    },
    prevPage() {
      if (this.currentPage !== 1) {
        this.currentPage--
        this.changePage(this.currentPage)
      }
    },
    nextPage() {
      if (this.currentPage !== this.countOfPage) {
        this.currentPage++
        this.changePage(this.currentPage)
      }
    },
    changePage(number) {
      this.currentPage = number
      this.page = number * 5 - 5
      this.record_per_page = number * 5
    },
    unFilter() {
      this.searchValue = ''
      this.filtering = false
      this.isSortedByDate = false
      this.isSortedByRate = false
      this.userList = this.usersInPage
    },
    sortByDate() {
      this.filtering = true
      this.isSortedByDate = true
      this.isSortedByRate = false
      if (this.sortedDateAsc) {
        this.users.sort((a, b) => a.registration_date > b.registration_date ? 1 : -1);
        return this.sortedDateAsc = false
      } else {
        this.users.sort((a, b) => a.registration_date > b.registration_date ? -1 : 1);
        return this.sortedDateAsc = true
      }
    },
    sortByRate() {
      this.filtering = true
      this.isSortedByRate = true
      this.isSortedByDate = false
      if (this.sortedRateAsc) {
        this.users.sort((a, b) => a.rating > b.rating ? 1 : -1);
        return this.sortedRateAsc = false
      } else {
        this.users.sort((a, b) => a.rating > b.rating ? -1 : 1);
        return this.sortedRateAsc = true
      }
    },
  },
  computed: {
    userList: {
      get(){
        let filter = new RegExp(this.searchValue, 'i')
        return this.users.filter(el => el.username.match(filter) || el.email.match(filter)).slice(this.page, this.record_per_page)
      },
      set(newValue) {
        this.users = newValue
      }
    }
  },
  mounted() {
    this.fetchUsers()
  },
  watch: {
    'searchValue.length'() {
      this.searchValue.length ? this.filtering = true : this.filtering = false
      this.countOfPage = Math.ceil(this.users.filter(el => el.username.match(this.searchValue) || el.email.match(this.searchValue)).length / 5)
      if(this.countOfPage === 0) {
        return this.countOfPage = 1
      }
    },
    'users'() {
      this.countOfPage = Math.ceil(this.users.length / 5)
    },
    'userList'() {
      if(this.userList.length === 0) {
        this.currentPage = 1
      }
    },
    'countOfPage'() {
      if(this.userList.length === 0 && this.countOfPage === 5) {
        this.countOfPage = 1
      }
    }
  }
}
</script>

<style scoped>

input {
  border-radius: 4px;
  height: 34px;
  border: none;
  width: 90%;
  padding-left: 30px;
  background: url(../assets/search-icon.svg) #ECEFF0 no-repeat scroll 7px 7px;
}

input::placeholder {
  font-size: 16px;
  font-weight: 400;
}

/* don't delete it is used for sorting buttons */
.unselected, .selected {
  border: none;
  background-color: #F7F7F7;
  border-bottom: 3px #9EAAB4 dashed;
  color: #9EAAB4;
}

.selected {
  color: #333333;
}

#sort-btn {
  margin: 0 20px;
}

.sort-text {
  color: #9EAAB4;
}

.top-hr {
  position: relative;
  border: none;
  height: 1px;
  background: #9EAAB4;
}

.bottom-hr {
  position: relative;
  border: none;
  height: 1px;
  background: #9EAAB4;
  margin-top: 18px;
}

.username {
  font-size: 12px;
  font-weight: 700;
  line-height: 16px;
  color: #0CB4F1;
}

.email, .date, .rating {
  color: #ACACAC;
  font-weight: 500;
  font-size: 12px;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}

.table-box {
  background-color: white;
  border-radius: 15px;
}

.tablet-names {
  font-size: 10px;
  font-weight: 500;
  line-height: 14px;
  color: #9EAAB4;
  padding: 16px 0 22px 0;
}

.search-box {
  border-radius: 7px;
  box-shadow: 0 18px 15px 0 #94949426;
}

.clear-text {
  color: #4F4F4F;
  font-size: 12px;
  font-weight: 500;
}

.modal-box {
  max-width: 360px;
  background-color: white;
  border-radius: 8px;
  font-size: 12px;
  position: absolute;
  top: 40%;
  margin: 0 auto;
  z-index: 1;
}

.confirm-btn, .undo-btn {
  border: none;
  border-radius: 3px;
  height: 27px;
  padding: 0 36px;
}

.confirm-btn {
  background-color: #E0E0E0;
  color: #828282;
}

.undo-btn {
  background-color: #0CB4F1;
  color: white;
}

.modal-text {
  font-size: 12px;
  padding: 36px;
}

.type-undo {
  width: 100%;
  height: 100vh;
  position: absolute;
  background-color: black;
  opacity: 0.5;
  top: 0;
  left: 0;
}

.btn-prev, .del-btn {
  cursor: pointer;
}

.btn-prev {
  margin: auto 0;
}

#btn-page {
  border: none;
  border-radius: 4px;
  text-align: center;
  padding: 5px 10px;
  background-color: #F7F7F7;
}

#btn-page:hover {
  border: 1px solid black;
}

.active-page {
  background-color: #1a2556 !important;
  color: white;
}
</style>