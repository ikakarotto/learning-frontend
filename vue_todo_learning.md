# Vue.js 前端开发入门学习指南：从零到代办事项项目

欢迎进入前端开发的世界！本指南是为初学者设计的，目标是帮助你从零开始学习 Vue.js 前端开发，并完成一个简单的代办事项（To-Do List）项目。你将使用 Vue.js 框架和 Element Plus UI 库，前端通过 API 调用后端（基于 Flask 和 PostgreSQL）。本教程会从基础语法讲起，逐步带你完成项目。

#### 训练：https://play.vuejs.org



---

## 第一部分：前端基础知识与语法

### 1.1 前端开发是什么？
- **定义**：前端开发是创建用户界面的过程，用户通过浏览器看到和操作的内容由前端代码实现。

- **三大核心技术**：
  - **HTML**：网页的骨架，定义结构。例如：
    
    ```html
    <h1>Hello World</h1> <!-- 标题 -->
    <p>This is a paragraph.</p> <!-- 段落 -->
    ```
    
  - **CSS**：网页的外衣，控制样式。例如：

    ```css
    h1 {
      color: blue; /* 文字颜色 */
      font-size: 24px; /* 字体大小 */
    }
    ```

  - **JavaScript**：网页的大脑，添加交互。例如：

    ```
    alert('Hello!'); // 弹出提示框
    ```

    

### 1.2 Vue.js 入门

- **什么是 Vue.js**

  - 一个简单易学的 JavaScript 框架，帮助你快速构建动态网页。
  - 特点：组件化（可复用代码块）、数据驱动（数据变化自动更新界面）。

- **基础语法**

  1. **数据绑定**

     - 使用 {{}} 显示变量：

       ```html
       <div>{{ message }}</div>
       ```

       ```javascript
       data() {
         return { message: 'Hello Vue!' };
       }
       ```

     - 使用 v-model 双向绑定输入框：

       ```html
       <input v-model="message" />
       ```
     
  2. **数据绑定**

     - 使用 {{}} 显示变量：

       ```html
       <ul>
         <li v-for="item in items" :key="item.id">{{ item.name }}</li>
       </ul>
       ```

       ```javascript
       data() {
         return { items: [{ id: 1, name: 'Task 1' }, { id: 2, name: 'Task 2' }] };
       }
       ```
     
  3. **条件渲染**：

     - 使用 v-if 控制显示：

       ```html
       <p v-if="isCompleted">Done!</p>
       ```

       ```javascript
       data() {
         return { isCompleted: true };
       }
       ```
  4. **事件处理**：

     - 使用 v-on 或 @ 绑定点击事件：

       ```html
       <button @click="sayHello">Click Me</button>
       ```

       ```javascript
       methods: {
         sayHello() {
           alert('Hello!');
         }
       }
       ```

### 1.3 开发工具

​	**Node.js**：运行 JavaScript 的环境，下载地址：https://nodejs.org/。

​	**npm**：安装和管理工具包。

​	**Vue CLI**：快速创建 Vue 项目。

​	**VSCode**：推荐编辑器，安装插件如 Volar（Vue 支持）。



## 第二部分：搭建开发环境

### 2.1 安装 Node.js 和 npm

1. 下载并安装 Node.js（建议 LTS 版本，如 16.x 或 18.x）。

2. 验证安装：

   ```bash
   node -v  # 示例：v18.16.0 npm -v   # 示例：9.5.1
   ```

### 2.2 安装 Vue CLI

   ```bash
npm install -g @vue/cli
vue --version  # 示例：@vue/cli 5.0.8
   ```

### 2.3 创建项目

1. 创建项目：

   ```bash
   vue create todo_frontend
   ```

2. 选择预设：`Default ([Vue 3] babel, eslint)`。

3. 进入目录：

   ```bash
   cd todo_frontend
   ```

## 第三部分：项目结构与基础代码

### 3.1 项目结构

```text
todo_frontend/
├── node_modules/         # 依赖包
├── public/
│   ├── index.html        # 主页面
│   └── favicon.ico
├── src/
│   ├── assets/           # 资源文件夹
│   ├── components/       # 组件文件夹
│   ├── App.vue           # 根组件
│   └── main.js           # 入口文件
├── package.json          # 项目配置
└── README.md
```

### 3.2 运行项目

```bash
npm run serve
```

​	访问 http://localhost:8080，看到默认页面。

## 第四部分：实现代办事项功能

### 4.1 安装依赖

- **Axios**：发送 HTTP 请求：

  ```bash
  npm install axios
  ```

- **Element Plus**：美观的 UI 组件：

  ```bash
  npm install element-plus
  ```

### 4.2 配置 Element Plus

- 创建 `src/main.js`：

  ```javascript
  import { createApp } from 'vue';
  import App from './App.vue';
  import ElementPlus from 'element-plus';
  import 'element-plus/dist/index.css';
  
  const app = createApp(App);
  app.use(ElementPlus);
  app.mount('#app');
  ```

- 实现核心功能

  **目标**：添加代办事项、标记完成、分页显示。

  **创建**`src/App.vue`：

  ```vue
  <template>
    <div class="todo-container">
      <el-card class="box-card">
        <template #header>
          <div class="card-header">
            <span>To-Do List</span>
          </div>
        </template>
        <!-- 添加代办事项表单 -->
        <el-form @submit.prevent="addTodo" inline>
          <el-form-item>
            <el-input v-model="newTodo" placeholder="Add a new task" clearable></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" native-type="submit">Add</el-button>
          </el-form-item>
        </el-form>
        <!-- 任务表格 -->
        <el-table :data="todos" style="width: 100%">
          <el-table-column prop="title" label="Task"></el-table-column>
          <el-table-column label="Status" width="120">
            <template #default="scope">
              <el-tag :type="scope.row.is_completed ? 'success' : 'info'">
                {{ scope.row.is_completed ? 'Completed' : 'Pending' }}
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column label="Actions" width="120">
            <template #default="scope">
              <el-button v-if="!scope.row.is_completed" type="success" size="small" @click="markComplete(scope.row.id)">
                Complete
              </el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页控件 -->
        <div class="pagination-container">
          <span class="page-info">当前页: {{ currentPageRecords }} 条 / 共 {{ total }} 条</span>
          <el-pagination
            v-model:current-page="currentPage"
            v-model:page-size="pageSize"
            :page-sizes="[10, 20, 50, 100]"
            :total="total"
            layout="total, sizes, prev, pager, next, jumper"
            @current-change="fetchTodos"
            @size-change="fetchTodos"
          />
        </div>
      </el-card>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import { ElMessage } from 'element-plus';
  
  export default {
    name: 'App',
    data() {
      return {
        newTodo: '',        // 输入框内容
        todos: [],          // 任务列表
        currentPage: 1,     // 当前页码
        pageSize: 20,       // 每页记录数
        total: 0            // 总记录数
      };
    },
    computed: {
      // 计算当前页记录数
      currentPageRecords() {
        const start = (this.currentPage - 1) * this.pageSize + 1;
        const end = Math.min(this.currentPage * this.pageSize, this.total);
        return end - start + 1;
      }
    },
    methods: {
      // 添加代办事项
      async addTodo() {
        try {
          await axios.post('http://localhost:5000/api/todos', { title: this.newTodo });
          this.newTodo = '';
          this.fetchTodos();
          ElMessage.success('Task added!');
        } catch (error) {
          ElMessage.error('Error adding task');
          console.error('Error:', error);
        }
      },
      // 标记完成
      async markComplete(id) {
        try {
          await axios.put(`http://localhost:5000/api/todos/${id}/complete`);
          this.fetchTodos();
          ElMessage.success('Task completed!');
        } catch (error) {
          ElMessage.error('Error marking task');
          console.error('Error:', error);
        }
      },
      // 获取任务列表
      async fetchTodos() {
        try {
          const response = await axios.get('http://localhost:5000/api/todos', {
            params: {
              page: this.currentPage,
              page_size: this.pageSize
            }
          });
          this.todos = response.data.todos;
          this.total = response.data.total;
          this.currentPage = response.data.page;
        } catch (error) {
          ElMessage.error('Error fetching tasks');
          console.error('Error:', error);
        }
      }
    },
    mounted() {
      this.fetchTodos(); // 页面加载时获取数据
    }
  };
  </script>
  
  <style scoped>
  .todo-container {
    max-width: 800px;
    margin: 20px auto;
  }
  
  .card-header {
    font-size: 1.5em;
    font-weight: bold;
  }
  
  .pagination-container {
    margin-top: 20px;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
  }
  
  .page-info {
    font-size: 14px;
    color: #606266;
  }
  </style>
  ```

## 第五部分：调试与优化

### 5.1 优化编译警告

- **问题**：chunk-vendors.js 超过 1 MiB，影响性能。

- 解决

  ：按需导入 Element Plus：

  - 修改 src/main.js：

    ```javascript
    import { createApp } from 'vue';
    import App from './App.vue';
    import { ElTable, ElPagination, ElForm, ElInput, ElButton, ElMessage } from 'element-plus';
    import 'element-plus/theme-chalk/el-table.css';
    import 'element-plus/theme-chalk/el-pagination.css';
    import 'element-plus/theme-chalk/el-form.css';
    import 'element-plus/theme-chalk/el-input.css';
    import 'element-plus/theme-chalk/el-button.css';
    import 'element-plus/theme-chalk/el-message.css';
    
    const app = createApp(App);
    app.use(ElTable);
    app.use(ElPagination);
    app.use(ElForm);
    app.use(ElInput);
    app.use(ElButton);
    app.config.globalProperties.$message = ElMessage;
    app.mount('#app');
    ```

    

## 第六部分：学习建议

### 6.1 掌握基础语法

- 练习

  1. 创建一个按钮，点击时弹出 `Hello Vue!`：

     ```vue
     <button @click="sayHello">Click Me</button>
     <script>
     export default {
       methods: {
         sayHello() {
           alert('Hello Vue!');
         }
       }
     };
     </script>
     ```

  2. 显示一个列表：

     ```vue
     <ul>
       <li v-for="item in items" :key="item">{{ item }}</li>
     </ul>
     <script>
     export default {
       data() {
         return { items: ['Apple', 'Banana', 'Orange'] };
       }
     };
     </script>
     ```

### 6.2 项目扩展

- **删除功能**
  - 添加后端路由：DELETE /api/todos/<id>。
  - 前端按钮：<el-button type="danger" @click="deleteTodo(id)">Delete</el-button>。
- **样式调整**：修改 .todo-container 的背景色或字体。

### 6.3 学习资源

- **Vue.js 官网**：https://vuejs.org/
- **Vue.js 官方文档**: https://cn.vuejs.org/guide/quick-start
- **Element Plus 文档**：https://element-plus.org/
- **JavaScript 基础**：https://www.w3schools.com/js/



---

## 继续练习

### 如何使用这个文档

1. **复制到本地**：
   - 复制以上内容，粘贴到文本编辑器（如 VSCode）。
   - 保存为 `vue_todo_learning.md`。

2. **预览**：
   - 在 VSCode 中安装 Markdown 插件（如 `Markdown+Math`），右键选择 “Open Preview” 查看格式化效果。

3. **学习步骤**：
   - 从 “第一部分” 开始，阅读并尝试每个代码片段。
   - 按照 “第四部分” 重现代办事项项目。
   - 完成后再练习 “第六部分” 的扩展任务。

---

### 协助学习前端语法

#### 基础语法讲解
- **HTML**：定义页面结构。
  - 示例：`<button>Click</button>` 创建一个按钮。
- **CSS**：控制样式。
  - 示例：`button { color: red; }` 将按钮文字变为红色。
- **JavaScript**：添加逻辑。
  - 示例：`let x = 5; console.log(x);` 定义变量并打印。
- **Vue 语法**：
  - `v-model`：双向绑定输入框和数据。
  - `v-for`：循环显示列表。
  - `@click`：绑定点击事件。

#### 练习任务
##### 1. **任务 1**：显示一个静态列表。

   - 创建 `src/components/StaticList.vue`：
     ```vue
     <template>
       <ul>
         <li v-for="fruit in fruits" :key="fruit">{{ fruit }}</li>
       </ul>
     </template>
     <script>
     export default {
       data() {
         return {
           fruits: ['Apple', 'Banana', 'Orange']
         };
       }
     };
     </script>
     ```

   - 在 App.vue 中使用：

     ```vue
     <template>
       <StaticList />
     </template>
     <script>
     import StaticList from './components/StaticList.vue';
     export default {
       components: { StaticList }
     };
     </script>
     ```

  ##### **任务 2**：添加计数器。

   - 修改 App.vue：

     ```vue
     <template>
       <div>
         <p>Count: {{ count }}</p>
         <button @click="increment">Add 1</button>
       </div>
     </template>
     <script>
     export default {
       data() {
         return { count: 0 };
       },
       methods: {
         increment() {
           this.count++;
         }
       }
     };
     </script>
     ```

#### 下一步

- 完成这两个任务，理解 data、methods 和模板语法。
- 然后按照文档第四部分逐步实现代办事项功能。
- 如果遇到问题，告诉我具体代码或错误，我会详细解答！



# VSCode开发

## 安装配置

### 基本环境安装

- nodejs

- **Vue CLI 或 Vite（二选一）**

  选择建议：Vue 2 用 Vue CLI，Vue 3 推荐 Vite。

>- Node.js 是 JavaScript 运行时，npm 是包管理器，用于安装 Vue CLI、Vite 等工具及前端依赖。
>
>https://nodejs.org/： https://nodejs.org/dist/v22.14.0/node-v22.14.0-x64.msi
>
>```
>node -v  # 检查 Node.js 版本  v22.14.0
>npm -v   # 检查 npm 版本  10.9.2
>```
>
>- 安装Vue CLI：Vue 官方提供的脚手架工具，适合快速搭建 Vue 项目
>
>  npm install -g @vue/cli
>
>- 安装Vite：新一代构建工具，启动速度快，推荐用于 Vue 3 项目
>
>  无需全局安装，可直接创建项目：npm create vite@latest
>
>- 安装yarn（另一个流行的包管理器）
>
>  npm install -g yarn

### VSCode 插件安装

- 安装插件：Vue - Official

  Vue.js 官方推荐的插件，取代了之前广泛使用的 **Vetur**（适用于 Vue 2）。

- ESLint

  代码规范检查。

- Prettier - Code formatter

  代码格式化。

- Vue VSCode Snippets

  Vue 代码片段，加快开发。

- Live Server

  实时预览静态页面（适合非 Vite/CLI 项目）。

- Auto Rename Tag

  自动同步修改 HTML 标签。

- 在 VS Code 的 settings.json 中添加：

  ```
  {
    "editor.codeActionsOnSave": {
      "source.fixAll": true
    },
    "editor.formatOnSave": true,
    "[vue]": {
      "editor.defaultFormatter": "Vue.volar"
    }
  }
  ```

### 示例：用 Vite 创建 Vue 项目：

```
npm create vite@latest my-vue-app -- --template vue
# vite@latest: 指定使用最新版本的 Vite 创建工具。
# 指定的项目文件夹名称，命令会在当前目录下创建一个名为 my-vue-app 的文件夹。
# -- --template vue: 指定项目模板为 Vue。
## 第一个 -- 是分隔符，告诉 npm 后面的参数传给 create-vite 脚本。
## --template vue 表示选择 Vue 模板（Vite 支持多种模板，如 React、Preact 等，这里选的是 Vue）。

cd my-vue-app
npm install
# npm 的命令，用于根据项目中的 package.json 文件下载并安装所有依赖。
# 在 Vite 创建的项目中，package.json 已经预定义了 Vue 和其他必要的库（如 vite）。
# 结果: 会在项目目录下生成一个 node_modules 文件夹，包含所有依赖库。初次运行可能需要几秒到几分钟，取决于网络速度。

npm run dev
# 启动 Vite 开发服务器，运行 Vue 项目。
# npm run: npm 的命令，用于执行 package.json 中 scripts 字段定义的脚本。
# dev: 在 package.json 的 scripts 中，通常是这样定义的：
"scripts": {
  "dev": "vite"
}
# 结果: 启动一个本地服务器，默认地址通常是 http://localhost:5173（端口可能因占用而变化）。终端会显示具体地址，打开浏览器访问即可看到 Vue 项目运行效果。

```



### 其他前端开发注意事项

- **依赖管理**：项目初始化后，常用库（如 Axios、Vue Router、Pinia/Vuex）可通过 npm 安装。

- **网络环境**：国内用户可能因网络问题导致 npm 下载慢，可切换淘宝镜像：

  ```bash
  npm config set registry https://registry.npmmirror.com
  ```

  















































