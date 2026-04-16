# qfremove

Herramienta web personal para quitar el fondo de imagenes sin perder calidad. Similar a [remove.bg](https://www.remove.bg) pero 100% local — la imagen nunca sale de tu navegador.

## Caracteristicas

- **Procesamiento local** — usa `@imgly/background-removal` con el modelo ISNet directamente en el navegador
- **Sin perdida de calidad** — sin compresion ni redimensionado, exporta PNG a resolucion completa
- **Privado** — nada se sube a ningun servidor
- **Interfaz intuitiva** — arrastra, pega (Ctrl+V) o selecciona la imagen
- **Comparacion antes/despues** — slider interactivo para ver el resultado
- **Descarga directa** — un clic para descargar el PNG sin fondo

## Tech stack

- Svelte 5 (runes)
- Tailwind CSS 4
- Vite 8
- TypeScript
- `@imgly/background-removal` (cliente, ~40MB modelo, se cachea en el navegador)

## Uso

```bash
npm install
npm run dev
```

## Comandos

| Comando | Descripcion |
|---------|-------------|
| `npm run dev` | Servidor de desarrollo con HMR |
| `npm run build` | Build de produccion (`dist/`) |
| `npm run preview` | Preview del build de produccion |
| `npm run check` | Verificacion de tipos (svelte-check + tsc) |

## Nota

La primera vez que proceses una imagen, el modelo ISNet (~40MB) se descargara y quedara cacheado en el navegador para usos futuros.
