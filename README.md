## Xây dựng ứng dụng bán PET sử dụng Nodejs và Vuejs

Hôm nay, chúng ta sẽ thực hành việc học tập, nghiên cứu NodeJs và VueJs bằng việc xây dựng một ứng dụng nho nhỏ xử dụng NodeJS làm backend (gọi API), và VueJS làm frontend (hiển thị kết quả).

## Create the BackEnd

Trước hết, tạo một thư mục chứa tất cả code của project chúng ta sẽ làm rồi cd vào trong thư mục đó

```
mkdir animal-adoption-site
cd animal-adoption-site
```

Tiếp theo, chúng ta sẽ tạo ra 2 thư mục tách biệt, một thư mục chứa code backend, thư mục còn lại sẽ chứa code FE

```
mkdir express-server
mkdir vue-client
```

## Cài đặt NodeJS

Như phần đầu bài tôi có trình bày, trong ví dụ này chúng ta sẽ dùng NodeJS để code server cho app, vì vậy việc đầu tiên chúng ta cần làm là cài đặt NodeJs vào project

```
cd express-server
npm init
```

Sau khi câu lệnh trên hoàn tất, nó sẽ tạo cho chúng ta file package.json, file này giúp chúng ta dễ dàng quản lý những dependencies cần thiết. 

## Cài đặt những Dependencies

Dưới đây là một số Dependencies cần thiết

- Express: Đây là một framework dùng để code Nodejs, không bắt buộc bạn phải dùng framework, nhưng nó sẽ giúp công việc code của bạn trở nên dễ dàng hơn rất nhiều

- Nodemon: Giữ cho server chúng ta luôn luôn chạy và trang web của chúng ta sẽ được tự động cập nhật lại khi chúng ta sửa code

- CORS: Gói này sẽ cho phép chúng ta tạo request từ FE, nếu không có thì bạn sẽ thấy thông báo lỗi 
```
The CORS policy for this site does not allow access from the specified Origin.
```

- Request: chúng ta sẽ thực hiện một số thao tác gọi API, vì vậy cần có package request

- body-parser: Cho phép chúng ta extract the body từ kết quả của request trả về

Chúng ta có thể cài tất cả các package trên trong một câu lệnh. Đối với Nodemon, chúng ta cài nó cho global với flag -g.

```
npm install nodemon -g
npm install express cors request body-parser --save
```

## Tạo API Endpoint

Trước tiên, tạo một file index.js 

```
touch index.js
```

Đây là file index khi chúng ta chạy lệnh _npm init_ sẽ được gọi tới. Đầu tiền, hãy import tất cả Dependencies cần thiết vào đầu file

```
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
const cors = require('cors');
const request = require('request');

app.use(bodyParser.json());
app.use(cors());
app.use(express.urlencoded({ extended: true }));
```

Tiếp theo, chúng ta cần định nghĩa một port để chạy trên web

```
// set the port
const port = 8000;

// listen on the port
app.listen(port);
```

Để ứng dụng của bạn có thể khởi động ngay khi start server, hãy mở file package.json và thêm command sau vào trong thử scripts

```
// package.json
"scripts": {
  "start": "nodemon index.js"
},
```

Sau khi lưu file lại, bạn chỉ cần chạy

```
npm start
```

Như vậy là server của chúng ta đang chạy. Nhưng bạn có thể để ý rằng chúng ta chưa có bất kỳ một route nào cả. Mở lại file index.js lên và định nghĩa một route đầu tiên

```
// index.js
app.get('/', (req, res) => {
    res.send(`Hi! Server is listening on port ${port}.`)
});
```

Bây giờ bạn có thể truy cập vào đường link (localhost:8000)[localhost:8000]và trên màn hình của bạn sẽ hiện ra dòng chữ

```
Hi! Server is listening on port 8000.
```

Quay lại để ý hàm chúng ta vừa tạo một chút, đầu tiên chúng ta tạo một sự kiện lắng nghe khi method GET được gọi (khi chúng ta truy cập vào server với đường dẫn '/'). Sau đó, trong callback function, chúng ta sendback lại một chuỗi ký tự được định nghĩa sẵn.

Trước khi chúng ta chuyển sang FE, hãy trả về một vài dữ liệu để tiện cho việc hiển thị bên FE

```
// index.js
app.get('/animals', (req, res) => {
  res.send([
    {
      id: 1,
      name: 'Lily',
      type: 'Dog',
      image: 'https://placedog.net/550/550',
      description: 'She loves to give kisses and bark at nothing.'
    },

    {
      id: 2,
      name: 'Lovely',
      type: 'Dog',
      image: 'https://placedog.net/500/500',
      description: `A little shy at first, but won't leave your side soon enough.` // note the backticks so we don't have to escape!
    },

    {
      id: 3,
      name: 'Sprinkles',
      type: 'Cat',
      image: 'https://placekitten.com/550/550',
      description: `Needs diabetes shot. Roll the insulin in your hand, don't shake it.`
    },

    {
      id: 4,
      name: 'Garbage',
      type: 'Cat',
      image: 'https://placekitten.com/500/500',
      description: 'A feral barn cat. He loves to eat garbage!'
    }
  ])
});
```

## Create FE

Tới bước này, chúng ta sẽ thao tác trong thư mục vue-client (thư mục mà đã được tạo ngay từ bước đầu cài đặt project)

Trong đầu bài tôi cũng đã giới thiệu, với demo này chúng ta sẽ xử dụng VueJs làm FE, vì vậy việc cài đặt VueJS là không thể thiếu

```
npm install -g @vue/cli
cd vue-client
vue create .
npm run serve
```

## Cài đặt Dependencies
Để hỗ trợ việc code cũng như việc chạy ứng dụng với Vue, chúng ta cần cài thêm một số thư viện sau

- Buefy: Thư viện giúp quick styling components

- Axios: Dùng để gọi API tới server của chúng ta

Chắc chắn rằng bạn đang ở thư mục vue-client nhé, chạy lệnh sau:

```
npm install buefy axios --save
```

## Cấu trúc của App

Trước tiên, trong thư mục src/components, hãy đổi tên file HelloWorld.vue thành file AnimalList.vue, tạo thêm thư mục layouts với 3 file sau ở trong nó:

```
cd src/components
mv HelloWorld.vue AnimalList.vue

cd ..
mkdir layouts

cd layouts

touch MainLayout.vue
touch Header.vue
touch Footer.vue
```

## Setting Up

Trước tiên, mở file main.js trong thư mục vue-client/src và thêm vào đoạn code sau:

```
// main.js
import Vue from 'vue';
import App from './App.vue';
import router from './router';
import Buefy from 'buefy';
import 'buefy/dist/buefy.css';

Vue.use(Buefy);
Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```

Để ý rằng , trong file trên chúng ta có import vào file App.vue. Khi chúng tạ bạo Vue app bằng CLI thì nó tự động tạo mới cho chung s ta file này, nếu bạn không tạo Vue app bằng CLI thì có thể tự tạo bằng tay với nội dung sau:

```
// App.vue
<template>
  <MainLayout id="app">
    <router-view />
  </MainLayout>
</template>

<script>

import MainLayout from "@/layouts/MainLayout";

export default {
  components: { 
    MainLayout 
  }
};
</script>
```

Trong template, chúng ta gọi tới MainLayout component, bây giờ hãy mở file MainLayout.vue trong thư mục vue-clients/src và thêm vào đoạn code sau

```
// MainLayout.vue
<template>
  <div>
    <Header />
    <section class="section">
      <div class="container">
        <slot />
      </div>
    </section>
    <Footer />
  </div>
</template>

<script>
import Header from './Header';
import Footer from './Footer';

export default {
  components: { 
    Header, 
    Footer
  }
};

</script>
```

Cấu truc file MainLayout của chúng ta gồm có header, custom component, và footer

## Header, Footer, HomePage

Bây giờ, chúng ta sẽ tạo layout cho ứng dụng của mình. Lần lượt mở các file sau và thêm vào đoạn code

```
// src/layouts/Header.vue
<template>
  <nav class="navbar is-dark is-spaced" role="navigation">
    <div class="navbar-brand">
      <a class="navbar-item is-size-5 has-text-weight-bold" href="/">
        Furry Friends
      </a>
    </div>

    <div id="navbarMain" class="navbar-menu">
      <div class="navbar-start">
        <a class="navbar-item">
          About
        </a>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
};

</script>
```

```
// src/layouts/Footer.vue
<template>
  <footer class="footer">
    <div class="content has-text-centered">
      <strong>Furry Friends</strong>
      <p>&copy; 2019</p>
    </div>
  </footer>
</template>

<script>
export default {
};

</script>
```

```
// Home.vue
<template>
  <AnimalList />
</template>

<script>

import AnimalList from '@/components/AnimalList.vue'

export default {
  components: {
    AnimalList
  }
}
</script>
```

## Kết nối BE với FE

Mở file AnimalList.vue lên và sửa thành đoạn code sau

```
// AnimalList.vue

<template>
</template>

<script>

import axios from 'axios';

export default {
  name: 'AnimalList',
  data() {
    return { animals: [] }
  },
  methods: {
    async getAllAnimals() {
        const response = await axios.get('http://localhost:8000/animals');
        this.animals = response.data;     }

  },
  mounted() {
    this.getAllAnimals(); 
  }
}

</script>
```

Trên đây, chúng ta đã tích hợp Axios vào để call API tới server, bây giờ công việc còn lại là hiển thị kết quả trên giao diện

```
// AnimalList.vue

<template>
  <div>
    {{ animals }}
  </div>
</template>
```

Mọi việc coi như đã hoàn tất, bây giờ các bạn chạy lại npm start và vào đường link (http://localhost:8000)[http://localhost:8000] và kiểm tra kết quả nhé. 

Ch
