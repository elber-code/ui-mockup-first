# ui-mockup-first

Metodología y conjunto de skills para Claude que generan mockups visuales **antes** de escribir lógica, backend o base de datos.

---

## Skills

| Skill | Stack | Descripción |
|-------|-------|-------------|
| [ui-mockup-first](./skills/ui-mockup-first/) | Cualquiera (HTML, EJS, HBS, Laravel, …) | Skill base — detecta tu stack y genera mockups para cualquier framework |
| [ui-mockup-first-laravel](./skills/ui-mockup-first-laravel/) | Laravel (Blade) | Específico para Laravel: lee tus layouts, componentes y convenciones antes de generar |

Más variantes por framework en camino.

---

## El problema

Dos situaciones donde esto te salva:

**1. Construir algo nuevo desde cero**

Tienes una idea. Le pides a una IA que la construya. La IA genera controladores, modelos, migraciones, rutas y vistas — y cuando por fin lo ves corriendo, te das cuenta de que:

- Falta un campo
- El flujo no era lo que tenías en mente
- El layout no funciona para ese caso de uso
- El cliente dice "no era así"

Ahora tienes que rehacer código real. No un boceto — lógica de backend con queries a la base de datos. Eso es caro en tiempo, energía y tokens.

**2. Agregar una funcionalidad o acción a algo que ya existe**

Quieres agregar un botón, un formulario, un flujo nuevo a un módulo existente. Se ve pequeño así que le pides a la IA que lo implemente directo. Pero moverse rápido hace que las cosas se escapen:

- Un input que debería estar no está
- Una validación que parecía obvia se saltó
- La nueva acción no conecta bien con el resto del flujo
- La UI rompe un patrón que el resto de la app sigue

Lo notas después de que el código está escrito, no antes. Ahora estás arreglando código real en vez de ajustar una vista.

**La solución es la misma en ambos casos: vélo antes de construirlo.**

---

## El enfoque

Genera **solo las vistas** primero. Estáticas, navegables, con datos ficticios realistas. Sin controladores, sin base de datos, sin rutas que hagan algo real. Solo la interfaz visual — suficiente para abrirla en el navegador, hacer clic, y decir "sí, es esto" o "ese input falta" o "ese flujo está mal".

Una vez aprobado, *entonces* construyes. El mockup se convierte en la especificación.

Iterar sobre una vista estática es rápido y barato. Iterar sobre código funcional conectado a una base de datos no lo es.

---

## Cómo funciona con un proyecto existente

Los skills no generan en el vacío. Antes de escribir cualquier cosa, escanean el proyecto:

- Qué stack y framework CSS está en uso
- Qué layouts ya existen
- Qué componentes ya están construidos
- Qué convenciones de carpetas y nombres sigue el proyecto

Si tu proyecto ya tiene un layout, una biblioteca de componentes, un sidebar — el mockup usa esos. No archivos paralelos nuevos que se vean diferentes al resto de la app.

El mockup se ve como tu app. Ese es el punto.

---

## Flujo recomendado

```
Idea (módulo nuevo o funcionalidad nueva en uno existente)
  ↓
El skill escanea el proyecto (stack, layouts, componentes)
  ↓
Genera vistas de mockup usando lo que ya existe
  ↓
Abres en el navegador — navegas cada estado
  ↓
Detectas lo que falta o está mal → la IA ajusta (rápido, barato)
  ↓
Aprobado ✓
  ↓
Construyes el backend real — el mockup es la especificación
```

---

## Instalación

Copia el `SKILL.md` del skill que quieras en la carpeta `.claude/` de tu proyecto.

> 📄 [English version → README.md](./README.md)
