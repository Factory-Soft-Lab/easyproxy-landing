# easyproxy-landing

Sitio público de **EasyProxy**: la portada y los tres documentos que exigen App
Store y Google Play —privacidad, términos y soporte—, en español e inglés.

🔗 https://easyproxy.factorysoftlab.com (dominio propio, archivo `CNAME`; antes https://factory-soft-lab.github.io/easyproxy-landing/, que GitHub redirige)

## Por qué vive en un repo aparte

Los repos de la app (`easyproxy` y `easyproxy-android`) son privados, y GitHub
Pages sólo publica desde repos privados con plan de pago. De paso, publicar
cuatro páginas no obliga a exponer el código de la app.

## Cómo se despliega

GitHub Pages compila la rama `main` con **Jekyll** en cada `push`; no hay que
construir nada a mano. A diferencia de `zubscriptions-landing` (HTML suelto con
`.nojekyll`), aquí se usa Jekyll a propósito: el nombre de la app, la empresa,
el correo, las fechas, los planes y los límites gratis se escriben **una vez** y
todas las páginas los leen.

```
_config.yml           nombre de la app, empresa, correo, fecha de vigencia, URL
_data/plans.yml       los tres planes de Pro y la prueba gratis
_data/free_tier.yml   lo que entra gratis
_data/nav.yml         textos de la barra en cada idioma
_layouts/default.html cabecera, barra y pie de todas las páginas
_includes/plans.html  las tarjetas de planes

index.html  privacidad.html  terminos.html  soporte.html          ← español
en/index.html  en/privacy.html  en/terms.html  en/support.html    ← inglés
assets/styles.css  assets/icon.svg  404.html
```

Para verlo en local sin instalar Ruby, con la misma imagen que usa Pages:

```sh
docker run --rm -v "$PWD":/srv/jekyll -p 4000:4000 jekyll/jekyll:4 \
  jekyll serve --host 0.0.0.0 --baseurl ""
# http://localhost:4000/
```

## Si la app cambia de nombre

1. Cambia `app.name`, `app.wordmark_lead`/`app.wordmark_accent` y `app.pro` en
   `_config.yml`. Las ocho páginas, los títulos y el pie cambian solos.
2. Las URLs **no** cambian solas: van en el nombre de este repo y están
   compiladas dentro de las dos apps. Si renombras el repo, actualiza `baseurl`
   aquí, `LegalLinks` en las dos apps y las fichas de las dos tiendas.

## Qué tiene que seguir copiando a la app

| Aquí | En la app |
|---|---|
| `_data/plans.yml` | iOS `App/Services/StoreKitEntitlement.swift` · Android `features/pro/PlayStore.kt` |
| `_data/free_tier.yml` | `FreeTier` en `EasyProxyDomain/Entitlement.swift` y `domain/pro/Entitlement.kt` |
| `trial_days` | la oferta introductoria de App Store Connect y Play Console |
| Lo que dice `privacidad.html` | lo que hace de verdad la app: si algún día lleva analítica o un SDK de terceros, **esta página cambia antes de publicar esa versión** |

## Dónde se consumen estas URLs

| Página | Quién la pide |
|---|---|
| `/privacidad.html` · `/en/privacy.html` | App Store Connect · Play Console · pantalla de Pro y «Más» en las dos apps (`LegalLinks`) |
| `/terminos.html` · `/en/terms.html` | EULA de la pantalla de Pro (obligatorio con suscripción auto-renovable) · `LegalLinks` |
| `/soporte.html` · `/en/support.html` | *Support URL* de App Store Connect · correo de contacto de Play |

⚠️ Si cambia una ruta, hay que actualizar `LegalLinks` en las dos apps **y** las
fichas de las dos tiendas. Una redirección no basta: la app abre la URL literal
que lleva compilada.

## Estilo

Estructura de vidrio sobre resplandores, igual que `zubscriptions-landing`, con
la paleta de EasyProxy (suelo del icono, rojo de «Proxy», tinta del splash). Los
colores están copiados a mano en el bloque `:root` de `assets/styles.css`.
Tema oscuro único, como la pantalla de Pro. El rojo de marca nunca en texto
pequeño: los enlaces usan `--red-ink`, que da 7:1 sobre el suelo.
