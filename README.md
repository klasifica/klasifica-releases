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
| **Nombre del asset** | `klasifica-android-v<versión>-release.apk` |

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

## Publicación (cómo se sube una APK)

Cada vez que se despliega la app a producción se **construye, firma y publica**
la APK aquí, con la misma versión del CHANGELOG. Se puede hacer:

- **GitHub Actions** (`build-android.yml` en el repo de código): manual o al
  hacer push de un tag `v*`. Construye, firma (v1/v2/v3) y crea el release.
- **Local** (cuando Actions no está disponible): build web con las variables de
  producción → `cap sync android` → `./gradlew assembleRelease` (firma con el
  keystore vía `keystore.properties`, JDK 21) → `gh release create`.

La APK apunta al backend de producción (`https://api.klasifica.com/api`).

## Para Google Play (checklist)

- **Package name**: `com.klasifica.app` (definitivo).
- **App signing**: usar App Signing de Google Play, o subir con el keystore
  propio (`CN=Klasifica`). Guardar el keystore y sus contraseñas — perderlo
  impide publicar actualizaciones.
- **versionCode** siempre creciente (fórmula `minor*1000 + patch`).
- **Target API level** al día según requisitos de Play (compileSdk 36).
- **Contenido**: ficha, capturas, icono 512×512, política de privacidad, etc.
- Para **cerradas/internas**: subir el `.apk`/`.aab` a una pista de test.

---

*Código fuente: [klasifica/klasifica-fe](https://github.com/klasifica/klasifica-fe).*
