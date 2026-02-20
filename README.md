# 📦 Crzgames - Libraries

Bibliothèques précompilées utilisées par RC2D pour toutes les
plateformes\
(Windows, Linux, macOS, iOS, Android, SteamRT4, etc.)

------------------------------------------------------------------------

# ⚠️ Organisation des headers --- règle générale (toutes plateformes)

Pour éviter les collisions de noms de headers (ex: `time.h`, `list.h`,
`types.h`) et garantir une résolution d'`#include` **déterministe**, les
dossiers `include/` doivent être **isolés par bibliothèque**, sur toutes
les plateformes :

-   Windows\
-   Linux\
-   macOS\
-   iOS\
-   Android\
-   SteamRT4\
-   etc.

------------------------------------------------------------------------

## 🎯 Principe fondamental

> **1 bibliothèque = 1 include root**

Chaque bibliothèque doit exposer **un seul namespace** (un dossier
racine) utilisé explicitement dans les includes :

Exemples :

``` cpp
#include <openssl/ssl.h>
#include <rcenet/RCENET_enet.h>
#include <SDL3_shadercross/SDL_shadercross.h>
#include <libavutil/time.h>
```

Il ne doit jamais y avoir de headers "plats" partagés entre plusieurs
libs dans un même dossier `include/`.

------------------------------------------------------------------------

## ❗ Pourquoi c'est important

Même sur desktop, un include "global" du style `include/` contenant
plusieurs bibliothèques peut provoquer :

-   Collisions entre libs tierces (`time.h`, `types.h`, `list.h`, etc.)
-   Résolution imprévisible selon l'ordre des `-I`
-   Erreurs qui apparaissent uniquement sur certaines plateformes /
    compilateurs
-   Bugs subtils (mauvais header inclus → types incomplets → erreurs en
    cascade)

Ces problèmes sont souvent difficiles à diagnostiquer car ils dépendent
de l'ordre d'inclusion et de la toolchain utilisée.

------------------------------------------------------------------------

## 🤖 Cas particulier : Android (NDK)

Sur Android, cette règle devient **obligatoire**.

Le NDK compile via un **sysroot** qui fournit ses propres headers
système (`time.h`, `wchar.h`, etc.).

Si les includes tiers ne sont pas strictement isolés, les collisions
arrivent beaucoup plus facilement, et tu peux obtenir des erreurs du
genre :

-   `struct tm incomplete`
-   `clockid_t` manquant
-   Types incomplets
-   Symboles manquants / ABI incohérente

Android est particulièrement sensible à ces conflits.

------------------------------------------------------------------------

## ✅ Structure recommandée (toutes plateformes)

Chaque plateforme suit la même logique :\
**un dossier `include_*` par bibliothèque.**

### Exemple (Android)

    android/
    └── include/
        ├── include_openssl/
        │   └── openssl/...
        ├── include_ffmpeg/
        │   └── libavutil/...
        ├── include_rcenet/
        │   └── rcenet/...
        ├── include_onnxruntime/
        │   └── onnxruntime/...
        └── include_sdl3shadercross/
            └── SDL3_shadercross/...

Même principe pour :

-   `windows/include/`
-   `linux/include/`
-   `macos/include/`
-   `ios/include/`
-   `steamrt4/include/`

