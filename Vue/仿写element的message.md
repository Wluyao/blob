# 组件
/src/components/base/Reminder/Reminder.vue
```vue
<template>
  <transition name="reminder-fade">
    <div class="reminder" :class="typeClass" v-show="visible">
      <i class="reminder__icon" :class="iconClass"></i>
      <span class="reminder__content" v-text="message"></span>
      <i class="reminder__closeBtn el-icon-close" @click="close"></i>
    </div>
  </transition>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      type: 'info',
      message: '',
      duration: 3000,
      onClose: null,
      timer: null
    }
  },
  computed: {
    typeClass() {
      return `reminder--${this.type}`
    },
    iconClass() {
      const TYPE_CLASSES_MAP = {
        info: 'el-icon-info',
        success: 'el-icon-success',
        warning: 'el-icon-warning',
        error: 'el-icon-error',
        loading: 'el-icon-loading'
      }
      return TYPE_CLASSES_MAP[this.type]
    }
  },
  mounted() {
    this.startTimer()
    document.addEventListener('keydown', this.keydown)
  },
  destroyed() {
    // 销毁实例的时候必须移除对docoment的事件监听，不然会造成重复监听
    document.removeEventListener('keydown', this.keydown)
  },
  methods: {
    close() {
      // 防止重复点击关闭，点一次之后动画要经过一段时间才结束，此时组件还未完全消失还可以再点击。
      if (!this.visible) return
      clearTimeout(this.timer)
      this.visible = false
      //监听过渡结束事件，也可以使用transition组件的atfer-leave钩子
      this.$el.addEventListener('transitionend', () => {
        // 销毁实例是为了触发destroyed钩子
        this.$destroy()
        // 将组件从DOM中移除
        this.$el.parentNode.removeChild(this.$el)
      })
      // 关闭之后的回调
      if (typeof this.onClose === 'function') {
        // 参数为当前reminder实例
        this.onClose(this)
      }
    },
    // 自动关闭
    startTimer() {
      // duration为0，不会自动关闭
      if (this.duration !== 0) {
        this.timer = setTimeout(this.close, this.duration)
      }
    },
    // 监听ESC键
    keydown(event) {
      if (event.keyCode === 27) {
        this.close()
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.reminder {
  min-width: 380px;
  box-sizing: border-box;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, 50%);
  padding: 16px;
  display: flex;
  align-items: center;
  border-width: 1px;
  border-style: solid;
  border-radius: 4px;
  z-index: 1000;

  .reminder__icon {
    margin-right: 10px;
  }

  .reminder__content {
    padding-right: 16px;
  }

  .reminder__closeBtn {
    position: absolute;
    top: 50%;
    right: 15px;
    transform: translateY(-50%);
    font-size: 16px;
    color: #c0c4cc;
    cursor: pointer;

    &:hover {
      color: #aaa;
    }
  }
}

.reminder--info {
  background-color: #edf2fc;
  border-color: #ebeef5;
  color: #909399;
}

.reminder--success {
  background-color: #f0f9eb;
  border-color: #e1f3d8;
  color: #67c23a;
}

.reminder--warning {
  background-color: #fdf6ec;
  border-color: #faecd8;
  color: #e6a23c;
}

.reminder--error {
  background-color: #fef0f0;
  border-color: #fde2e2;
  color: #f56c6c;
}

.reminder--loading {
  background-color: mix(#fff, #409eff, 90%);
  border-color: mix(#fff, #409eff, 80%);
  color: #409eff;
}

.reminder-fade-enter,
.reminder-fade-leave-to {
  opacity: 0;
}

.reminder-fade-enter-active,
.reminder-fade-leave-active {
  transition: opacity 0.5s;
}
</style>
```

# 创建组件的实例
/src/components/base/Reminder/index.js
```javascript
import Vue from 'vue'
import Reminder from './Reminder'

// 使用Vue的基础构造函数创建一个子类，参数是组件选项对象。
let RemindConstructor = Vue.extend(Reminder)

// 参数处理
let argManage = function(options) {
  options = options || {}
  if (typeof options === 'string') {
    options = {
      message: options
    }
  }
  return options
}

let reminder = function(options) {
  options = argManage(options)
  // 创建实例
  // 手动挂载实例，不传参数，模板被渲染为html文档之外的元素(文档碎片)，之后可以用原生DOM方法插入到文档中。
  let vm = new RemindConstructor({
    data: options
  }).$mount()
  // 将实例插入到html文档中.这样挂载不会覆盖掉挂载元素的内容，仅仅是添加到body最后。
  document.body.appendChild(vm.$el)

  // 必须先将组件实例挂载之后再显示，不然不会出现显示时的动画效果。
  vm.visible = true
  // 执行this.$reminder或reminder()方法将会返回reminder组件实例，然后可以调用实例的close()方法关闭。
  return vm
}

//设置每种类型调用的别名
const TYPES = ['success', 'warning', 'info', 'error', 'loading']
TYPES.forEach((type) => {
  reminder[type] = (options) => {
    options = argManage(options)
    options.type = type
    return reminder(options)
  }
})

export default reminder

```

# 全局注册
```javascript
import reminder from '@/components/base/reminder'
//注册为全局方法
Object.defineProperty(Vue.prototype, '$reminder', {
  value: reminder
})
```

# 使用
```vue
<template>
  <div>
    <input type="button" value="全局调用" @click="global" />
    <input type="button" value="在当前组件内调用" @click="part" />
    <input type="button" value="别名调用" @click="alias" />
  </div>
</template>

<script>
// 在组件内使用
import remind from '../components/Remind/index.js'

export default {
  data() {
    return {}
  },
  methods: {
    global() {
      this.$remind('这是一段普通消息！')
    },
    part() {
      remind({
        type: 'success',
        message: '这是一段成功消息！',
        onClose(arg) {
          console.log(arg)
          console.log('关闭')
        }
      })
    },
    alias() {
      this.$remind.error('这是一段错误消息！')
    }
  }
}
</script>
```
