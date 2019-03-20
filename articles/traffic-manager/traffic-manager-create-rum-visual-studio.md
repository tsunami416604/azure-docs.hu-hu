---
title: Valós Felhasználóiélmény-mérések az Azure Traffic Manager Visual Studio Mobile Center |} A Microsoft Docs
description: A Traffic Manager valós felhasználói mérések küldése a Visual Studio Mobile Center használatával fejlesztett mobile alkalmazás beállítása
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1a5b883a8c9688d4545c0e98c00f78a2e982a611
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079714"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Valós felhasználói mérések küldése a Traffic Manager a Visual Studio Mobile Center

Beállíthatja a mobilalkalmazást az alábbi lépéseket a Traffic Manager valós felhasználói mérések küldése a Visual Studio Mobile Center segítségével fejlesztett:

>[!NOTE]
> Jelenleg a Traffic manager valós Felhasználóiélmény-mérések küldése csak támogatott Android.

Valós felhasználói mérések konfigurálásához szüksége a az csomag az alkalmazás tagolását, és egy kulcs beszerzése.

## <a name="step-1-obtain-a-key"></a>1. lépés: Egy kulcs beszerzése
    
A mérési igénybe vehet, és a Traffic Manager, az ügyfélalkalmazás által küldött azonosítja a szolgáltatást egy egyedi karakterlánccá, neve a valós felhasználói mérések (RUM) kulcs használatával. Az Azure portal, a REST API használatával az kulcs kap vagy a PowerShell használatával / parancssori felületek.

Az Azure Portalon a következő eljárással RUM kulcs beszerzése:
1. Egy böngészőben jelentkezzen be az Azure Portalon. Ha még nincs fiókja, akkor regisztráljon egy ingyenes egy hónapos próbaidőszak.
2. A portál keresősávjában keressen a Traffic Manager-profil neve, amelyet módosítani szeretne, majd kattintson az eredményeket a Traffic Manager-profilt, amely jelenik meg.
3. A Traffic Manager-profil oldalon kattintson **valós felhasználói mérések** alatt **beállítások**.
4. Kattintson a **kulcs létrehozása** RUM új kulcs létrehozásához.
        
   ![Valós felhasználói mérések kulcsának létrehozása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: Valós Felhasználóiélmény-mérések kulcs létrehozása**

5. A lap megjeleníti a RUM kulcs, amely akkor jön létre, és a egy JavaScript kódrészletet, amelyet a HTML-oldalt beilleszthető.
 
   ![Valós felhasználói mérések kulcsát a JavaScript-kódot](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **2. ábra: Valós felhasználói mérések kulcsának és a mérési JavaScript**
 
6. Kattintson a **másolási** gombra, hogy a RUM kulcsot. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2. lépés: A Mobile Center SDK-t, az csomag az alkalmazás tagolását

Ha most ismerkedik a Visual Studio Mobile Center, keresse fel a [webhely](https://mobile.azure.com). Az SDK-integráció részletes utasításokért lásd: [az Android SDK – első lépések](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Valós felhasználói mérések használatához kövesse az alábbi lépéseket:

1.  Adja hozzá az SDK-t a projekthez

    ATM RUM SDK az előzetes verzióban, explicit módon hivatkoznia kell a csomagtárház.

    Az a **app/build.gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Az a **app/build.gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Indítsa el az SDK-val

    Nyissa meg az alkalmazás fő tevékenységosztállyal, és adja hozzá a következő importálási utasításokat:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Keresse meg a `onCreate` visszahívási ugyanazt a fájlt, és adja hozzá a következő kódot:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [valós felhasználói mérések](traffic-manager-rum-overview.md)
- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- Tudjon meg többet [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Regisztráció](https://mobile.azure.com) Mobile Center
- Tudjon meg többet a [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) a Traffic Manager által támogatott
- Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

