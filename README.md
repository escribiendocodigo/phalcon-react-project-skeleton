# Phalcon React project skeleton

Skeleton application using [Phalcon framework](https://phalcon.io) and [Vite](https://vitejs.dev) + [React](https://react.dev).

## Requirements

- PHP >= 7.4.1
- Phalcon >= 5.0.0
- Node >= 18.0.0

## Structure

```
my-project/
  ¦
  ├-- backend/
  ¦
  └-- frontend/
```

### Backend

[Phalcon micro application](https://github.com/escribiendocodigo/phalcon-micro-project-skeleton)
that provides a RESTful API running port 9000

### Frontend

React web application running port 5173

Configure custom [server.proxy](https://vitejs.dev/config/server-options.html#server-proxy)

http://localhost:5173/api -> http://localhost:9000

```js
// vite.config.js

import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

const BACKEND_PORT = process.env.BACKEND_PORT || 9000;

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      "/api": {
        target: `http://127.0.0.1:${BACKEND_PORT}`,
        // changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ""),
      },
    },
  },
});
```

## Installing via Composer

```bash
composer create-project escribiendocodigo/phalcon-react-project-skeleton my-project
```

Once installed, you can test it out immediately using PHP's built-in web server:

```bash
cd my-project
```

Run backend

```bash
cd backend
composer serve
# OR use the composer alias:
composer serve-backend
```

Run frontend

```bash
cd frontend
npm run dev
# OR use the composer alias:
composer serve-frontend
```

Build frontend

```bash
cd frontend
npm run build
# OR use the composer alias:
composer build-frontend
```

## Web server setup

### Nginx

```nginx
server {
    listen 80;
    server_name my-domain www.my-domain;

    index index.html index.php;

    access_log /var/log/nginx/my-domain.access.log;
    error_log /var/log/nginx/my-domain.error.log;

    location / {
        root /var/www/my-project/frontend/dist;

        try_files $uri $uri/ /index.html;
    }

    location /api {
        rewrite ^/api(.*)$ /index.php;
        set $request_url $1;
    }

    location ~ \.php$ {
        root /var/www/my-project/backend/public;

        include /etc/nginx/snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param REQUEST_URI $request_url;
    }

    location ~ /\.ht {
        deny all;
    }

}
```
