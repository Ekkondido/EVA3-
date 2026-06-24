# --- Stage 1: Construcción (Build) ---
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# --- Stage 2: Imagen final para producción (Nginx) ---
FROM nginx:alpine AS production

# Copiamos los archivos estáticos de React
COPY --from=build /app/dist /usr/share/nginx/html

# EXTREMADAMENTE IMPORTANTE: Cambiar el puerto base de Nginx al 8080 vía comandos
RUN sed -i 's/listen\.+80;/listen 8080;/g' /etc/nginx/conf.d/default.conf

# Exponemos el puerto alto seguro
EXPOSE 8080

# Ajustamos permisos para que el usuario limitado pueda usar las carpetas de Nginx
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown nginx:nginx /var/run/nginx.pid

# Cambiamos explícitamente al usuario sin privilegios antes de ejecutar
USER nginx

CMD ["nginx", "-g", "daemon off;"]