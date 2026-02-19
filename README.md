# 📦 Crzgames - Libraries

Bibliothèques précompilées utilisées par RC2D pour toutes les
plateformes\
(Windows, Linux, macOS, iOS, Android, SteamRT4, etc.)

------------------------------------------------------------------------

## ⚠️ Spécificité Android --- Organisation des headers

Sur Android (NDK), l'organisation des dossiers `include/` doit être
**strictement isolée par bibliothèque**.

Contrairement aux plateformes Desktop (Windows, macOS, Linux), il ne
faut **pas** utiliser un dossier `include/` global contenant plusieurs
bibliothèques.\
Le NDK utilise un *sysroot* avec ses propres headers système (`time.h`,
`wchar.h`, etc.).

Si plusieurs bibliothèques partagent le même dossier `include/`, cela
peut provoquer :

-   des collisions avec des headers système\
-   des conflits entre bibliothèques tierces\
-   une mauvaise résolution des `#include`\
-   des erreurs difficiles à diagnostiquer (ex : `struct tm incomplete`,
    types incomplets, symboles manquants, etc.)

------------------------------------------------------------------------

## ✅ Structure recommandée pour Android

Chaque bibliothèque doit avoir son propre *include root* :

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
