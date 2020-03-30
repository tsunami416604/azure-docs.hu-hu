---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179634"
---
A folyamatos fejlesztés miatt előfordulhat, hogy az Android Studio-ban telepített Android SDK-verzió nem egyezik meg a kódban megadott verzióval. Az Android SDK hivatkozott ez a bemutató a 26-os verzió, a legújabb idején írásban. A verziószám növekedhet az SDK új kiadásainak megjelenésével, és javasoljuk, hogy a legújabb verziót használja.

A verzióeltérés két tünete:

- A projekt létrehozásakor vagy újraépítésekor a Gradle hibaüzeneteket kaphat, például `Gradle sync failed: Failed to find target with hash string 'android-XX'`a .
- A kódban lévő szabványos Android-objektumok, amelyeket utasítások alapján `import` kell megoldani, hibaüzeneteket generálhatnak.

Ha ezek bármelyike megjelenik, előfordulhat, hogy az Android Studio-ban telepített Android SDK-verzió nem egyezik meg a letöltött projekt SDK-céljával. A verzió ellenőrzéséhez hajtsa végre a következő módosításokat:

1. Az Android Studio alkalmazásban kattintson **az Eszközök** > **az Android** > **SDK-kezelő elemre.** Ha még nem telepítette az SDK platform legújabb verzióját, kattintson rá a telepítéshez. Jegyezze fel a verziószámot.

2. A **Project Explorer** lap **Gradle-parancsfájlok**csoportjában nyissa meg a **build.gradle fájlt (Modul: alkalmazás)**. Győződjön meg arról, hogy a **compileSdkVersion** és **a targetSdkVersion** a telepített legújabb SDK-verzióra van beállítva. A `build.gradle` lehet, hogy így néz ki:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
