# Klasifica — Android Releases

Versiones oficiales de la **app Android de Klasifica**, para descarga directa (sideload).

## Descargar la app

1. Ve a [**Releases**](https://github.com/klasifica/klasifica-releases/releases)
2. Descarga el `.apk` de la versión más reciente
3. En Android: **Ajustes → Seguridad → Instalar apps de fuentes desconocidas** ✓
4. Abre el `.apk` e instala

Desde [app.klasifica.com](https://app.klasifica.com) en Android también aparece un
banner que enlaza al APK más reciente (variable `VITE_ANDROID_APK_URL` de la web,
apuntando al asset del último release).

## Identidad de la app

| Dato | Valor |
|------|-------|
| **Application ID / package** | `com.klasifica.app` |
| **minSdk** | 24 (Android 7.0) |
| **compileSdk / target** | 36 |
| **Firma** | keystore propio `CN=Klasifica, O=Klasifica, L=Valencia, C=ES` (esquemas v1+v2) |
| **Assets por release** | `klasifica-android-v<versión>-release.apk` (sideload) · `klasifica-android-v<versión>-release.aab` (Google Play) |

> El **package `com.klasifica.app` es la identidad permanente** de la app: no
> cambia entre versiones. Es el mismo identificador que se usará en Google Play.
> Cambiarlo crearía una app distinta (los usuarios no recibirían la actualización),
> así que **nunca se cambia**.

## Versionado

- El **`versionName`** (p. ej. `0.204.0`) es la versión del día en el
  `CHANGELOG.md` de [klasifica/klasifica-fe](https://github.com/klasifica/klasifica-fe).
- **Una versión por subida de APK a producción.** La primera del día es
  `X.Y.0`; si el mismo día se vuelve a subir, se incrementa el patch:
  `X.Y.1`, `X.Y.2`, … Cada subida a prod = un release aquí.
- El **`versionCode`** (entero que exige Android/Play y que debe crecer siempre)
  se calcula: **`minor*1000 + patch`**.
  - `0.204.0` → `204000` · `0.204.1` → `204001` · `0.205.0` → `205000`

Ejemplos:

| versionName | versionCode |
|-------------|-------------|
| 0.198.1 | 198001 |
| 0.204.0 | 204000 |
| 0.204.1 | 204001 |

## APK vs AAB — cuándo se usa cada uno

| Formato | Para qué | Se instala directo |
|---------|----------|--------------------|
| **`.apk`** | Descarga directa / sideload y el **botón de la web** | ✅ Sí |
| **`.aab`** | **Subir a Google Play** (Play ya **no** acepta APK) | ❌ No (Play genera los APK por dispositivo) |

Cada release incluye **los dos**. El usuario final descarga el `.apk`; el `.aab`
es solo para publicar en Play Console.

## Publicación (cómo se genera cada versión)

Cada vez que se sube la app a producción se **construye, firma y publica** aquí,
con la misma versión del CHANGELOG. Se puede hacer:

- **GitHub Actions** (`build-android.yml` en el repo de código): manual o al
  hacer push de un tag `v*`. Construye, firma (v1/v2/v3) y crea el release.
- **Local** (cuando Actions no está disponible):
  1. Build web con las variables de producción (`VITE_API_URL=https://api.klasifica.com/api`, …).
  2. `cap sync android`.
  3. **APK**: `./gradlew assembleRelease` → firma con el keystore vía `keystore.properties` (JDK 21).
  4. **AAB**: `./gradlew bundleRelease` → mismo keystore.
  5. `gh release create v<versión>` subiendo el `.apk` **y** el `.aab`.

La build apunta al backend de producción (`https://api.klasifica.com/api`).

## Publicar en Google Play (paso a paso)

1. **Play Console → Crear app**. Package **`com.klasifica.app`** (definitivo, no
   se cambia nunca — cambiarlo crea otra app y los usuarios no reciben la
   actualización).
2. **Firma de apps de Google Play (Play App Signing)**: activada. Se sube el
   `.aab` firmado con **nuestra clave de subida** (`CN=Klasifica`, keystore
   `klasifica-release.jks`); Google re-firma con su clave de distribución.
   ⚠️ **Guardar el keystore + contraseñas**: perder la clave de subida impide
   publicar actualizaciones.
3. **Crear versión** en una pista (Interna → Cerrada → Producción) y **subir el
   `.aab`** de este release.
4. **`versionCode` creciente** en cada subida (fórmula `minor*1000 + patch`); dos
   subidas no pueden repetir versionCode.
5. **Ficha de Play**: nombre, descripción, **icono 512×512**, capturas (teléfono
   + tablet), gráfico destacado, categoría.
6. **Cuestionarios obligatorios**: política de privacidad (URL), seguridad de los
   datos, clasificación de contenido, público objetivo, anuncios.
7. **Target API level** al día según requisitos de Play (compileSdk 36).
8. Revisar y **lanzar** en la pista elegida.

> Mientras no esté en Play, la distribución es por **sideload** con el `.apk` de
> este repo (enlace directo desde la web).

---

*Código fuente: [klasifica/klasifica-fe](https://github.com/klasifica/klasifica-fe).*
