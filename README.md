# SGS-ATM BancoSol Bolivia

Proyecto académico en Java + Spring Boot + MySQL + Hibernate, inspirado visualmente en los colores institucionales de BancoSol Bolivia.

## Funcionalidades incluidas

- Inicio de sesión con C.I. o número de tarjeta.
- Registro de clientes con nombre completo, C.I., celular, correo, PIN, tarjeta física y fotografías del documento de identidad.
- Panel de cliente con saludo personalizado.
- Depósitos, retiros, transferencias internas e historial.
- Pago de servicios básicos: luz, agua, telecomunicaciones y gas domiciliario.
- Confirmación de factura antes del pago.
- Límite temporal de transacciones editable solo cuando vence el tiempo configurado.
- Confirmación profesional antes de cambiar usuario.
- Panel administrador para ver usuarios, saldos, transacciones y logs.
- Soporte de idioma español/inglés solo desde el inicio.

## Tecnologías

- Java 17
- Spring Boot
- Spring MVC + Thymeleaf
- Spring Data JPA
- Hibernate
- MySQL
- BCrypt para PIN cifrado
- Git / Trello como herramientas de gestión del proyecto

## Antes de ejecutar

Crea la base de datos en MySQL:

```sql
CREATE DATABASE IF NOT EXISTS sgs_atm;
```

Luego revisa `src/main/resources/application.properties` y cambia la contraseña de MySQL:

```properties
spring.datasource.username=root
spring.datasource.password=123456
```

## Ejecutar

En la carpeta del proyecto:

```bash
mvn spring-boot:run
```

Abrir en el navegador:

```text
http://localhost:8080
```

## Usuarios demo

Administrador:

- C.I.: `admin`
- PIN: `1234`

Cliente:

- C.I.: `1234567`
- Tarjeta: `4000123412341234`
- PIN: `1234`

## Nota sobre la base de datos

La aplicación usa `spring.jpa.hibernate.ddl-auto=update`, por eso crea y actualiza las tablas automáticamente. También se incluye `schema.sql` con la estructura completa para documentación o ejecución manual.


## Mejoras finales solicitadas

### Identidad visual BancoSol
La interfaz utiliza los colores definidos para el proyecto:
- Morado: `#46009B`, asociado a sensibilidad, trayectoria, seriedad, modernidad y enfoque digital.
- Naranja: `#FF6B00`, asociado a calidez, cercanía, empatía, confianza y energía.

### Login limpio
La pantalla de inicio fue simplificada para mostrar solo elementos necesarios:
- Logo de BancoSol.
- Formulario de ingreso seguro.
- Botón de crear cuenta.
- Selector de idioma únicamente al inicio.

### HTTPS y seguridad
El proyecto incorpora encabezados de seguridad mediante `SecurityHeadersFilter`:
- `X-Content-Type-Options`
- `X-Frame-Options`
- `Referrer-Policy`
- `Permissions-Policy`
- `Content-Security-Policy`

Para un despliegue real con HTTPS se dejaron propiedades comentadas en `application.properties`. En producción se debe activar SSL con un certificado válido o usar un proxy HTTPS.

### No perder autenticación al cambiar de página
La autenticación se mantiene mediante sesión HTTP. Además se agregó `AuthSessionInterceptor`, que protege las rutas `/atm/**` y `/admin/**`; si el usuario no tiene sesión activa, se redirige al login.

### Pago de servicios básicos
Se mejoró el flujo de pago:
1. Selección de categoría: electricidad, agua, telecomunicaciones o gas.
2. Selección de ciudad y empresa.
3. Selección de modalidad: pago directo o referencia QR.
4. Ingreso de código de cliente, medidor o contrato.
5. Consulta y verificación de factura.
6. Selección de la cuenta para debitar.
7. Confirmación de pago y generación de comprobante.
8. Opción de guardar como pago frecuente.

### Módulo complementario: consulta de billetes observados
Se agregó una opción desde el login para consultar billetes observados sin modificar el flujo principal del cajero.

La consulta solicita:
- Corte del billete: Bs 10, Bs 20 o Bs 50.
- Serie: por defecto `B`.
- Número de serie / código.

El sistema compara la información con la tabla `banknote_records` y muestra uno de estos estados:
- `NO_OBSERVADO`: no coincide con registros simulados de billetes siniestrados u observados.
- `OBSERVADO`: coincide con un registro simulado no apto para circulación.
- `CANJE_RECOMENDADO`: no está observado, pero se recomienda acudir a una entidad financiera para evitar rechazo comercial.
- `NO_REGISTRADO`: la consulta no corresponde a los cortes/series cargados en el prototipo.

Códigos demo cargados automáticamente:
- Bs 10, serie B, código `B10000001` → observado.
- Bs 20, serie B, código `B20000002` → observado.
- Bs 50, serie B, código `B50000003` → observado.
- Bs 10, serie B, código `B10000099` → no observado.
- Bs 20, serie B, código `B20000099` → canje recomendado.

Este módulo es académico e informativo; no reemplaza una validación oficial de una entidad financiera.
