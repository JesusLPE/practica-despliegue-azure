# Hito 2 — Guion de Supergrow para cinco minutos

**Integrantes:** Jesús Leonel Palma Pérez y Joan Samir Intriago Rade.  
**Materia:** Aplicaciones para el Cliente Web · ULEAM · 2026-2.

Material de estudio. No es un entregable obligatorio y no necesitas subirlo al repositorio.

## Antes de comenzar

El docente sortea quién maneja las demostraciones 1 y 2. El otro integrante realiza la 3 y la 4. Ambos deben saber hacer todo.

Dejar preparado:

- Aplicación funcionando con `npm run dev`.
- Si se quiere mostrar una evaluación nueva de Gemini, otra terminal con `node --env-file=.env.server server/gemini.mjs`.
- Navegador con DevTools en Network, filtro Fetch/XHR.
- Supabase abierto en Table Editor.
- VS Code con `src/tipos.ts` y `docs/hito2_hoja.md`.
- Cuenta de prueba `juanperez@gmail.com` y la contraseña que crearon en Supabase.
- Una publicación con historial: por ejemplo, «Aprender programación con práctica diaria», si conserva su revisión inicial.
- Las capturas de respaldo disponibles.

Empezar con la sesión cerrada. No volver a ejecutar el esquema inicial.

## 0:00–0:25 · Mostrar la versión entregada

En la terminal:

```powershell
git status
git log -1
```

Debe verse el árbol de trabajo limpio y el último commit entregado dentro del plazo.

**Qué decir:**

> Este es nuestro repositorio de la organización y esta es la versión que entregamos.

## 0:25–1:10 · Demostración 1: contrato de datos

**Qué hacer:**

1. Mostrar las tablas `usuarios`, `publicaciones` y `revisiones` en Supabase, con sus datos.
2. Explicar las relaciones: una publicación tiene un autor y puede tener varias revisiones; cada revisión tiene un Revisor.
3. Mostrar `src/tipos.ts`: las propiedades de `Publicacion` y la unión de estados.
4. Mostrar la tabla «Vista × consulta» de `docs/hito2_hoja.md`.

**Qué decir:**

> Estas son nuestras tablas: usuarios, publicaciones y revisiones. Este es el tipo Publicacion, con los mismos nombres de columnas de la base y los tres estados permitidos. La hoja indica qué consulta realiza cada una de nuestras cuatro vistas.

**Lo que debes entender:**

- `publicaciones.autor_id` apunta a `usuarios.id`.
- `revisiones.publicacion_id` apunta a `publicaciones.id`.
- `revisiones.revisor_id` apunta a `usuarios.id`.
- Los estados son `Borrador`, `En Revisión` y `Publicado`.
- `auth_id` vincula el perfil del producto con la cuenta de Supabase Auth.
- Los campos de objetivo, público, tono y evaluación IA amplían el modelo conservando el flujo presentado.

## 1:10–2:05 · Demostración 2: leer listado y detalle

**Qué hacer:**

1. Iniciar sesión como Creador.
2. Mostrar el listado y su petición a Supabase con respuesta **200**.
3. Pulsar «Ver detalle e IA» en una publicación que tenga revisiones humanas.
4. Mostrar el contenido y el historial.
5. Mostrar la petición del detalle con respuesta **200**.
6. Señalar el estado escrito como palabra.

**Qué decir:**

> El listado de publicaciones viene de Supabase: esta petición respondió 200. Al abrir una publicación, el detalle trae también sus revisiones. El estado se muestra en texto.

**Consultas que debes reconocer:**

Listado:

```text
/rest/v1/publicaciones?select=id,autor_id,titulo,red_social,estado,actualizado_en&order=actualizado_en.desc,id.desc
```

Detalle, usando 3 como ejemplo de identificador:

```text
/rest/v1/publicaciones?id=eq.3&select=*,revisiones(*)
```

El identificador real depende del registro que abras. El orden de los parámetros y su codificación pueden verse diferentes en Network sin cambiar el significado.

## 2:05–2:45 · Demostración 3: validar antes de enviar

**Qué hacer:**

1. Cerrar sesión.
2. Completar el formulario con los datos de ejemplo indicados abajo.
3. Escribir `abc` como título inválido.
4. Limpiar Network cuando haya terminado la carga.
5. Pulsar «Guardar borrador».
6. Mostrar el aviso junto al título, los demás datos conservados y Network sin nuevas peticiones de guardado.

**Qué decir:**

> Si escribo un título de menos de cinco caracteres, el formulario avisa junto al campo, conserva lo escrito y no envía ninguna petición al servicio.

**Importante:** no pulsar «Revisar con IA» durante esta prueba, porque generaría otra petición.

## 2:45–3:50 · Demostración 4: quién puede crear

**Qué hacer:**

1. Sin recargar y todavía sin sesión, corregir el título.
2. Pulsar «Guardar borrador».
3. Mostrar la petición rechazada por Supabase y el aviso en la página.
4. Iniciar sesión como Creador sin recargar.
5. Volver a pulsar «Guardar borrador» sobre el mismo formulario.
6. Mostrar el POST con respuesta **201**, la confirmación y la nueva publicación en el listado.

**Qué decir:**

> Sin sesión, el servicio rechaza la creación y el usuario ve este mensaje. Iniciamos sesión como Creador, enviamos otra vez y el registro aparece en el listado.

**Lo que debes entender:**

- La petición anónima sí llega al servicio y se rechaza.
- La validación del formulario y los permisos de la base cumplen funciones distintas.
- El token identifica al usuario autenticado.
- El autor se obtiene desde la sesión en la base.
- La publicación se crea como `Borrador`.
- Al iniciar sesión no debes recargar: el formulario conserva el texto.

## Datos para las demostraciones 3 y 4

| Campo | Valor |
| --- | --- |
| Título inválido | `abc` |
| Título corregido | `Organiza tu semana de estudio` |
| Red social | LinkedIn |
| Contenido | Organiza tus actividades de la semana, divide las tareas grandes en pasos pequeños y reserva un momento diario para repasar. |
| Objetivo | Educar |
| Público | Estudiantes universitarios |
| Tono | Educativo |

Puedes agregar un número al título corregido en cada ensayo para reconocer la publicación nueva.

## 3:50–4:50 · Preguntas: una a cada integrante

### 1. ¿Qué tabla, columnas y filtros pide esta dirección?

**Respuesta:**

> La ruta publicaciones indica la tabla. Select elige las columnas y order las ordena por fecha, de más nueva a más antigua. En el detalle, id=eq.3 filtra por la publicación 3 y revisiones(*) incluye su historial relacionado.

**Razón para explicar:**

> En el listado pedimos las columnas necesarias para mostrar el resumen. En el detalle pedimos el contenido completo y sus revisiones. RLS restringe las filas según el usuario aunque el autor no aparezca como filtro en la URL.

### 2. ¿De dónde sale este tipo de TypeScript y qué pasa si agregan una columna?

**Respuesta:**

> Sale de las columnas de la tabla. Usamos autor_id tanto en PostgreSQL como en TypeScript porque Supabase devuelve ese mismo nombre en el JSON. Si agregamos una columna, revisamos la interfaz y las consultas o formularios que deban utilizarla.

**Razón para explicar:**

> Así el contrato del código coincide con los datos recibidos. El tipo ayuda durante el desarrollo, pero no valida por sí mismo una respuesta en ejecución.

### 3. Si el formulario ya valida, ¿por qué hace falta una política?

**Respuesta:**

> La validación ayuda al usuario a corregir los datos, pero puede saltarse enviando una petición directa. Por eso la base también comprueba quién solicita la operación y qué puede hacer.

**Ejemplo del producto:**

> La política de creación permite que un Creador autenticado guarde un borrador propio. Tener una clave publicable no concede permiso para crear cualquier publicación.

### 4. Según su matriz, ¿qué no puede hacer cada rol?

**Respuesta:**

> El Creador no puede aprobar publicaciones ni leer borradores ajenos. El Revisor no puede crear borradores ni editar su contenido. Las políticas limitan la lectura y creación, y la función de cambios de estado comprueba el rol y la transición.

**Ejemplo del producto:**

> El Creador solo envía sus borradores a revisión. El Revisor aprueba o devuelve lo que está en revisión. Una publicación publicada no puede reabrirse.

### 5. ¿Esta validación es del navegador o del negocio? ¿Dónde está escrita?

**Respuesta:**

> El mínimo de cinco caracteres para el título y los máximos por red son reglas del producto. Están en src/lib/validaciones/publicacion.ts y el formulario las utiliza antes de guardar. La base también tiene restricciones para proteger los datos.

**Detalle útil:**

> El formulario usa novalidate para gestionar los mensajes desde el código. Los avisos se relacionan con sus campos mediante atributos accesibles.

## 4:50–5:00 · Registro de notas

El docente registra la calificación. No empezar funciones adicionales al terminar los cinco minutos.

## Si preguntan por Gemini

> El Creador escribe el texto e indica objetivo, público y tono. Gemini devuelve puntuaciones y sugerencias. Esa evaluación se guarda con el borrador si se solicita, pero no cambia el estado ni aprueba. El Revisor humano toma la decisión final.

La clave privada se lee en el servidor Node. El navegador llama a `/api/revisar` mediante el proxy de Vite.

Gemini es opcional para crear un borrador y no reemplaza ninguna de las cuatro demostraciones. `Publicado` representa el estado final interno del producto; este hito no envía publicaciones a las redes sociales.

## Recordatorio final

- Ensayar el recorrido en menos de cuatro minutos para dejar espacio a las preguntas.
- Ambos deben practicar las cuatro demostraciones y las cinco respuestas.
- No borrar lo escrito ni recargar entre el rechazo anónimo y el inicio de sesión.
- No ejecutar de nuevo el esquema sobre la base existente.
- Una falla permite un reintento de veinte segundos; después se utiliza la captura de respaldo, con la limitación de nota indicada en la rúbrica.
- Las capturas son respaldo: la demostración se realiza en vivo.

