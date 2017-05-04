<!-- https://maimai.cn/article/detail?fid=64662725 -->
<template>
  <div>
    <ul class="sortview">
      <div class="info" v-if="duration > 0">
        <p>总耗时(包括动画)： {{ duration }}秒</p>
        <p>排序耗时： {{ real_duration }}毫秒</p>
      </div>
      <li v-for = "(item, index) in data "
      :style = "{ height: item + 'px' }"
      :class = "{ selected: cur[0] === index || cur[1] === index,
                  selection_low: cur[2] === 'selection' && cur[0] === index}"></li>
    </ul>
    <div class="controls">
      <ul class="sorts">
        <li v-for="sort in sorts">
          <input type="radio" name="type" :id="sort.type" :value="sort.type" v-model="type"></input>
          <label for="sort.type">{{ sort.text }}</label>
        </li>
        
      </ul>
    <input type="range"v-model="speed"min="10"max="2000"step="10"id="speed"><br>
    <label for="speed">速度</label><br><br>
    <button @click="start">开始</button>
  </div>
  </div>
</template>

<script>


export default {
  name: 'SortView',
  data () {
    return {
      origin: [],
      data: [],
      count: 40,

      list: [],

      cur: [],
      timer: '',
      speed: 10,
      type: 'bubble',

      duration: -1,
      startTime: -1,
      endTime: -2,

      real_duration: -1,
      sorts: [
        {
          type: 'bubble',
          text: '冒泡排序',
          func: this.bubbleSort
        },{
          type: 'bubble1',
          text: '冒泡排序(改进1)',
          func: this.bubbleSortImprove1
        },{
          type: 'bubble2',
          text: '冒泡排序(改进2)',
          func: this.bubbleSortImprove2
        },{
          type: 'selection',
          text: '选择排序',
          func: this.selectionSort
        },{
          type: 'insertion',
          text: '插入排序',
          func: this.insertionSort
        },{
          type: 'insertion1',
          text: '插入排序(改进)',
          func: this.insertionSortImprove
        },{
          type: 'shell',
          text: '希尔排序',
          func: this.shellSort
        },{
          type: 'merge',
          text: '归并排序',
          func: this.mergeSort
        }
      ]
    }
  },
  created () {
    for(var i = 0; i < this.count; i++) {
      this.data[i] = Math.random() * 600
      this.origin[i] = this.data[i]
    }
  },
  mounted () {
  },
  methods: {
    //冒泡排序
    bubbleSort (arr) {
      var len = arr.length
      for(var i = 0; i < len; i++) {
        for(var j = 0; j < len - 1; j++) {
          this.list.push([j, j+1])

          if(arr[j] > arr[j+1]) {
            var temp = arr[j+1]
            arr[j+1] = arr[j]
            arr[j] = temp
          }
        }
      }
    },
    //改进冒泡排序
    bubbleSortImprove1(arr) {
      var len = arr.length
     
      for(var i = 0; i < len; i++) {
        for(var j = 0; j < len - 1 - i; j++) {
          this.list.push([j, j+1])
          if(arr[j] > arr[j+1]) {
            var temp = arr[j+1]
            arr[j+1] = arr[j]
            arr[j] = temp
          }
        }
      }
    },
    //改进后的冒泡排序2
    bubbleSortImprove2 (arr) {
      var len = arr.length

      var low = 0
      var high = len - 1;
      var tmp, j;

      while(low < high) {
        for(j = low; j < high; j++){
          this.list.push([j, j+1])
          if( arr[j] > arr[j+1]) {
            tmp = arr[j]
            arr[j] = arr[j+1]
            arr[j+1] = tmp
          }
        }
        high --
        for( j = high; j>low; j--) {
          this.list.push([j-1, j])
          if( arr[j] < arr[j-1]) {
            tmp = arr[j]
            arr[j] = arr[j-1]
            arr[j-1] = tmp

          }
        }
        ++low
      }
    },

    //选择排序
    selectionSort (arr) {
      var len = arr.length

      var minIndex, temp
      for(var i = 0; i < len - 1; i++) {
        minIndex = i;
        for(var j = i + 1; j < len; j++) {
          this.list.push([minIndex, j, 'selection'])
          if(arr[j] < arr[minIndex]) {
            minIndex = j
          }
        }
        this.list.push([i, minIndex])
        temp = arr[i]
        arr[i] = arr[minIndex]
        arr[minIndex] = temp
      }
    },

    //插入排序
    insertionSort (arr) {
      var len = arr.length
      for(var i = 1; i < len; i++) {
        var key = arr[i]
        var j = i -1;

        this.list.push([j, i, 'insertion', '>&=next', key])
        while(j >=0 && arr[j] > key) {
          arr[j+1] = arr[j]
          j--
          if(j >= 0)
            this.list.push([j, j+1, 'insertion', '>&=next', key])
        }
        this.list.push([-1, j+1, 'insertion', '=next', key])
        arr[j+1] = key;
      }
    },

    //改进后的插入排序
    insertionSortImprove (arr) {
      var len = arr.length

      for(var i = 1; i < len; i++) {
        var key = arr[i], left = 0, right = i -1;

        this.list.push([left, right, 'insertion2', 'no'])
        while(left <= right) {
          var middle = parseInt((left + right) /2)

          this.list.push([i, middle, 'insertion2', 'no'])
          if(key < arr[middle]) {
            right = middle - 1;
          } else {
            left = middle + 1;
          }
        }
        for(var j = i - 1; j >= left; j--) {
          this.list.push([j, j+1, 'insertion2', '=0'])
          arr[j + 1] = arr[j]
        }
        this.list.push([-1, left, 'insertion2', '=next', key])
        arr[left] = key;
      }
    },

    //希尔排序
    shellSort (arr) {
      var len = arr.length

      var temp, gap = 1
      while(gap < len/5) {
        gap = gap * 5 + 1     //动态定义间隔序列
      }
      for(; gap > 0; gap = Math.floor(gap/5)) {
        for(var i = gap; i < len; i++) {
          temp = arr[i]

          if(i-gap >= 0)
            this.list.push([i-gap, i, 'shell', '>next&=0', temp])
          for(var j = i - gap; j >= 0 && arr[j] > temp; j -= gap) {
            arr[j + gap] = arr[j]

            if(j-gap >= 0 )
              this.list.push([j-gap, j, 'shell', '>next&=0', temp])
          }
          this.list.push([-1, j + gap, 'shell', '=next', temp])
          arr[j + gap] = temp
        }
      }
    },
    //归并排序
    mergeSort (arr) {
      var len = arr.length
      if (len < 2) {
        return arr
      }
      var middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle)
      return this.merge(this.mergeSort(left), this.mergeSort(right))
    },
    merge(left, right) {
      var result = []

      while(left.length && right.length) {
        if(left[0] <= right[0]) {
          result.push(left.shift())
        }else {
          result.push(right.shift())
        }
      }
      while(left.length) {
        result.push(left.shift())
      }
      while(right.length) {
        result.push(right.shift())
      }
      return result
    },
    updateView () {
      if (this.list.length <= 0) {
        this.end()
        return
      }
      this.cur = this.list.shift()
      //选择排序
      if(this.cur[2] === 'selection') {

      }else if(this.cur[2] === 'insertion') {
        if(this.cur[3] === '>&=next' && this.data[this.cur[0]] > this.cur[4]) {
          this.$set(this.data, this.cur[1], this.data[this.cur[0]])
        }else if(this.cur[3] === '=next'){
          this.$set(this.data, this.cur[1], this.cur[4])
        }
      }else if(this.cur[2] === 'insertion2') {
        if (this.cur[3] === '=0') {
          this.$set(this.data, this.cur[1], this.data[this.cur[0]])
        }else if(this.cur[3] === '=next') {
          this.$set(this.data, this.cur[1], this.cur[4])
        }

      }else if(this.cur[2] === 'shell') {
        if(this.cur[3] === '>next&=0' && this.data[this.cur[0]] > this.cur[4]) {
          this.$set(this.data, this.cur[1], this.data[this.cur[0]])
        }else if(this.cur[3] === '=next') {
          this.$set(this.data, this.cur[1], this.cur[4])
        }
      }else{
        if( this.data[this.cur[0]] > this.data[this.cur[1]]){
          var temp = this.data[this.cur[0]]
          this.$set(this.data, this.cur[0], this.data[this.cur[1]])
          this.$set(this.data, this.cur[1], temp)
        }
      }
    },
    animate () {
      this.timer = setTimeout(this.animate, this.speed)
      this.updateView()
    },
    restore () {
      var len = this.origin.length
      for(var i = 0; i < len - 1; i++) {
        this.data[i] = this.origin[i]
      }
      this.$set(this.data, len - 1, this.origin[len - 1])
    },
    copy () {
      var data = []
      var len = this.data.length
      for(var i = 0; i<len; i++) {
        data[i] = this.data[i]
      }

      return data
    },
    start () {
      this.list = []
      this.startTime = -1
      this.endTime = -2
      this.duration = -1

      this.restore()
      var data = this.copy()

      var _this = this
      var sort = this.sorts.find(function(sort){return sort.type===_this.type})
      var start = (new Date()).getTime();
      sort.func(data)
      var end = (new Date()).getTime();
      this.real_duration = end - start

      this.startTime = (new Date()).getTime();
      this.animate()
    },
    end () {
      clearTimeout(this.timer)
      this.endTime = (new Date).getTime()
      this.duration = (this.endTime - this.startTime)/1000
      this.cur = [-1, -1]
    }
  }

}
</script>

<style lang="styl">
body>div
  text-align: center;

ul {
  padding: 0;
}
ul.sortview
  display: flex;
  align-items: flex-end;
  min-height: 600px;
  justify-content: center;
  position: relative;

  li
    background: grey;
    margin: 10px 1px;
    width: 40px;
    list-style: none;
  li.selected
    background: red;
  li.selection_low
    background: blue;
  div.info
    position: absolute;
    text-align: left;

ul.sorts
  list-style: none;
  display: flex;
  justify-content: center;

  li
    margin: 10px 3px;

div.controls input[type='range'] {
  width: 50%;
}
</style>