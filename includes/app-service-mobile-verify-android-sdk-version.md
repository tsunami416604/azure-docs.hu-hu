---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134465"
---
Miatt fejlesztést az Android Studióban telepített Android SDK-verzió nem egyeznek meg a verziót a kódban. Ebben az oktatóanyagban hivatkozott az Android SDK verziója 26., a legújabb írásának időpontjában. A verziószám növelhető, az SDK-t az új kiadásokkal jelenik meg, és a legújabb elérhető verzió használatát javasoljuk.

Verzióeltérés két tünetei a következők:

- Amikor hozhat létre, vagy a projekt újraépítéséhez, például a Gradle-hibaüzenetek kaphat `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- A kódot, amely kell feloldhatónak lennie szabványos Android objektumok alapján `import` utasítások előfordulhat, hogy generálása hibaüzenetek.

Ha ezek akármelyike jelenik meg, a előfordulhat, hogy az Android SDK az Android Studióban telepített verziója nem egyezik a letöltött projektből SDK célja. A verzió ellenőrzéséhez a következő módosításokat:

1. Az Android Studióban kattintson **eszközök** > **Android** > **SDK Manager**. Ha nem telepítette az SDK Platform legújabb verzióját, majd kattintson a telepítésre. Jegyezze fel a verziószám.

2. Az a **Project Explorer** lap **Gradle-szkriptek**, nyissa meg a fájlt **build.gradle (modul: alkalmazás)**. Ügyeljen arra, hogy a **compileSdkVersion** és **targetSdkVersion** vannak beállítva, hogy az SDK legfrissebb verzió van telepítve. A `build.gradle` ehhez hasonló lehet:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
