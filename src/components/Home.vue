<template>
  <div class="home">
    <el-container>
      <el-header style="text-align: right; font-size: 20px; background:#3b3b3b;">
        <li>
          <el-button type="primary" style="float:right;width:80px;background:#ffcc00;border-color:#ffcc00;margin:10px;" v-on:click="onLogout">Logout</el-button>
          <h1 style="float:right;margin-top:15px;margin-right:10px;color:#ffffff;">Hello, {{ id }}</h1>
        </li>
      </el-header>
      <el-main>
        <div style='display:inline;'>
          <el-select v-model="searchTarget" style="display:inline;float:left;width:12%;">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
          <el-input v-model="searchText" style="display:inline;float:left;width:80%;margin-left:10px;"></el-input>
          <el-button
            icon="el-icon-search"
            style="display:inline;float:left;margin-left:10px;"
            v-on:click="onSeacrh(1)"
            v-loading.fullscreen.lock="fullscreenLoading"
            circle></el-button>
        </div>
        <div style="margin-top:50px;margin-left:10px">
          <el-radio v-model="searchSort" label="latest" style="float:left">최신순</el-radio>
          <el-radio v-model="searchSort" label="accuracy" style="float:left">정확도순</el-radio>
        </div>
        <div style="margin-top:100px;">
          <div style="height:40px;border-top-style:groove;border-bottom-style:groove;">
            <div style="float:left;margin-top:10px;margin-left:5px;">
              <el-dropdown>
                <span class="el-dropdown-link;">
                  나의 히스토리<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item>{{history[4]}}</el-dropdown-item>
                  <el-dropdown-item>{{history[3]}}</el-dropdown-item>
                  <el-dropdown-item>{{history[2]}}</el-dropdown-item>
                  <el-dropdown-item>{{history[1]}}</el-dropdown-item>
                  <el-dropdown-item>{{history[0]}}</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
            <div style="float:right;margin-top:10px;margin-right:5px;">
              <el-dropdown>
                <span class="el-dropdown-link;">
                  실시간<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item>{{rate[0]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[1]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[2]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[3]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[4]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[5]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[6]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[7]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[8]}}</el-dropdown-item>
                  <el-dropdown-item>{{rate[9]}}</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <el-table
            :data="bookData"
            style="margin-top:30px;width: 100%">
            <el-table-column
              prop="title"
              label="제목">
            </el-table-column>
            <el-table-column
              prop="price"
              label="가격"
              width="180">
            </el-table-column>
            <el-table-column
              label=""
              width="180">
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  @click="onClickDetail(scope.$index, scope.row)">상세보기</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
              layout="prev, pager, next"
              @current-change="onSetPagination"
              :current-page="currentPage"
              v-bind:total="pageTotalCount"
              v-on:current-change="onSetPagination">
          </el-pagination>
        </div>
      </el-main>
    </el-container>
    <el-dialog
      v-bind:title="bookTitle"
      :visible.sync="dialogVisible"
      width="35%">
      <ul>
        <li style="text-align: left;">제목 : {{bookTitle}}</li>
        <li style="text-align: left;">저자 : {{bookAuthors}}</li>
        <li style="text-align: left;">출판사 : {{bookPublisher}}</li>
        <li style="text-align: left;">소개 : {{bookContents}}</li>
        <li style="text-align: left;">판매가 : {{bookSalePrice}}</li>
        <li style="text-align: left;">정가 : {{bookPrice}}</li>
        <li style="text-align: left;">ISBN : {{bookISBN}}</li>
        <li style="text-align: left;">출판일 : {{bookDatetime}}</li>
        <el-image
          style="margin-top: 15px;width:35%"
          v-for="image in bookThumbnail"
          :key="image"
          :src="image" lazy></el-image>
      </ul>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">종료</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Home',
  props: {
    id: String
  },
  created () {
    this.onPolling(0)
  },
  methods: {
    onPolling: function (delay) {
      setTimeout(() => {
        const _baseURI = `http://localhost:9090/rate/`
        this.$http.get(`${_baseURI}`).then(response => {
          const _rate = []
          for (let i = 0; i < response.data.length; i++) {
            const _keyword = response.data[i].keyword
            const _number = response.data[i].number
            const _rateItem = `${i + 1} : ${_keyword}(${_number})`
            _rate.push(_rateItem)
          }
          this.rate = _rate

          this.onPolling(2000)
        })
      }, delay)
    },
    onLogout: function () {
      this.$router.push({ name: 'loginView' })
      this.$message({
        showClose: true,
        message: '로그아웃되었습니다.',
        type: 'warning'
      })
    },
    onClickDetail: function (index, row) {
      const _bookDetail = this.bookData[index]
      this.bookTitle = _bookDetail.title
      this.bookAuthors = _bookDetail.authors[0]
      this.bookPublisher = _bookDetail.publisher
      this.bookContents = _bookDetail.contents
      this.bookSalePrice = _bookDetail.sale_price
      this.bookPrice = _bookDetail.price
      this.bookISBN = _bookDetail.isbn
      this.bookDatetime = _bookDetail.datetime
      const _bookThumbnail = [ _bookDetail.thumbnail ]
      this.bookThumbnail = _bookThumbnail

      this.dialogVisible = true
    },
    onSetPagination: function (currentPage) {
      this.pageTotalCount = this.totalBookResponse.data.meta.pageable_count

      const _bookData = []
      for (let i = (currentPage - 1) * 10; i < (currentPage - 1) * 10 + 10; i++) {
        const _book = this.totalBookResponse.data.documents[i]
        _bookData.push(_book)
      }
      this.bookData = _bookData
    },
    onSeacrh: function (currentPage) {
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      })

      if (this.history.length < 5) {
        if (this.history.find(x => x === this.searchText) !== undefined) {
          const _targetIdx = this.history.indexOf(this.searchText.toString())
          if (_targetIdx !== -1) this.history.splice(_targetIdx, 1)
        }
        this.history.push(this.searchText)
      } else if (this.history.length === 5) {
        if (!this.history.find(x => x === this.searchText) !== undefined) {
          const _targetIdx = this.history.indexOf(this.searchText.toString())
          if (_targetIdx !== -1) this.history.splice(_targetIdx, 1)
        }
        const _history = this.history.splice(1, 5)
        _history.push(this.searchText)
        this.history = _history
      }

      const _query = this.searchText
      const _sort = this.searchSort
      const _page = currentPage
      const _size = '10'
      const _target = this.searchTarget
      const _baseURI = `http://localhost:9090/book/${_query}/${_sort}/${_page}/${_size}/${_target}`
      this.$http.get(`${_baseURI}`).then(response => {
        if (response.status === 200) {
          this.totalBookResponse = response

          this.onSetPagination(currentPage)
          setTimeout(() => {
            loading.close()
          }, 100)

          const _baseURI = `http://localhost:9090/rate/search/${this.searchText}`
          this.$http.get(`${_baseURI}`)
        }
      })
    }
  },
  data: function () {
    return {
      fullscreenLoading: false,
      options: [{
        value: 'title',
        label: '제목'
      }, {
        value: 'isbn',
        label: 'ISBN'
      }, {
        value: 'publisher',
        label: '출판사'
      }, {
        value: 'person',
        label: '인명'
      }],
      bookData: [{
        title: '',
        name: 'price'
      }],
      totalBookResponse: '',
      searchTarget: 'title',
      searchText: '',
      searchSort: 'latest',
      pageTotalCount: 0,
      currentPage: 1,
      dialogVisible: false,
      bookTitle: '',
      bookAuthors: '',
      bookPublisher: '',
      bookContents: '',
      bookSalePrice: '',
      bookPrice: '',
      bookISBN: '',
      bookDatetime: '',
      bookThumbnail: [
        ''
      ],
      history: [
        ''
      ],
      rate: [
        ''
      ]
    }
  }
}
</script>

<style lang="stylus">
el-header
  background-color: #B3C0D1
  color: #333
  line-height: 60px
</style>
