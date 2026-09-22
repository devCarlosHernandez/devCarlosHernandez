# Triply

Aplicación móvil y web (PWA) de planificación de viajes para **iOS, Android y Web**, con
backend propio y base de datos.

[![Live App](https://img.shields.io/badge/🌐_App_en_Vivo-triply--app--six.vercel.app-0ea5e9?style=for-the-badge&logo=vercel&logoColor=white)](https://triply-app-six.vercel.app/login)

> 🚀 **Pruébala en vivo:** [https://triply-app-six.vercel.app/login](https://triply-app-six.vercel.app/login)

```
travel-planner-mobile/   Expo · React Native · TypeScript · PWA
travel-planner-api/      NestJS · Prisma · PostgreSQL (Neon)
docs/                    arquitectura, modelo, API, seguridad, decisiones
docs/especificacion.md   especificación completa del producto
```

La app no accede nunca a la base de datos: consume la API.

## Arrancar

```bash
# 1. Backend
cd travel-planner-api
cp .env.example .env
npm install
npx prisma migrate deploy && npm run db:seed
npm run start:dev

# 2. App
cd ../travel-planner-mobile
npm install
npm start          # i = iOS · a = Android
```

O con Docker para el backend: `cd travel-planner-api && docker compose up`.

## Estado

Completo y probado de extremo a extremo el primer milestone de la
especificación (§63):

```
Register → Verify email → Login → Create trip → Trip overview →
Add destination → Add flight → View itinerary → Add expense →
View budget → Edit expense → Recalculate → Logout
```

| | |
|---|---|
| Endpoints | 80 |
| Tests | 250 (114 unitarios · 136 E2E) |
| Plataformas | iOS · Android |

## Lo que hay que saber antes de tocar el código

Seis decisiones gobiernan el diseño. Están razonadas en
[`docs/decisions.md`](docs/decisions.md); en corto:

1. **El itinerario es un índice, no una copia.** `ItineraryItem` no guarda
   títulos ni montos: referencia entidades. Evita que el mismo vuelo se cuente
   dos veces en el presupuesto.
2. **`Expense` es la única tabla monetaria real.** Los `priceMinor` de las
   entidades son lo *planeado*; un gasto con `sourceId` los *reemplaza*.
3. **Hora local, zona e instante son tres campos.** Se muestra la hora local,
   se calcula en UTC. Sin esto no hay conexiones correctas entre husos.
4. **Los gastos sin convertir no entran en el total.** Se muestran aparte. Un
   número incompleto presentado como completo es peor que dos honestos.
5. **La pertenencia se filtra en la consulta**, no se comprueba después. Y un
   recurso ajeno devuelve 404, no 403.
6. **Las escrituras llevan versión.** `If-Match` evita que dos dispositivos
   editando el mismo gasto produzcan una pérdida silenciosa.

## Documentación

| | |
|---|---|
| [`docs/architecture.md`](docs/architecture.md) | estructura de ambos proyectos y flujo de una petición |
| [`docs/domain-model.md`](docs/domain-model.md) | entidades, reglas transversales, manejo del tiempo |
| [`docs/api.md`](docs/api.md) | endpoints, formatos, códigos de error |
| [`docs/authentication.md`](docs/authentication.md) | tokens, rotación, detección de reuso |
| [`docs/security.md`](docs/security.md) | autorización, secretos, logs, límites |
| [`docs/decisions.md`](docs/decisions.md) | las veintitrés decisiones y por qué |
| [`docs/especificacion.md`](docs/especificacion.md) | especificación del producto, revisada y corregida |
