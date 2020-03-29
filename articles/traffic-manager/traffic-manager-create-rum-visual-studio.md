---
title: Valós felhasználói mérések a Visual Studio Mobile Center rel – Azure Traffic Manager
description: A Visual Studio Mobile Center használatával kifejlesztett mobilalkalmazás beállítása a valós felhasználói mérések nek a Traffic Manager nek történő küldéséhez
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939187"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Valódi felhasználói mérések küldése a Traffic Manager alkalmazásba a Visual Studio Mobile Center segítségével

A Visual Studio Mobile Center segítségével kifejlesztett mobilalkalmazását a következő lépések végrehajtásával állíthatja be a Forgalomkezelőnek:

>[!NOTE]
> Jelenleg a Valós felhasználói mérések küldése a Traffic Manager-nek csak Android esetén támogatott.

A valós felhasználói mérések konfigurálásához be kell szereznie egy kulcsot, és az alkalmazást a RUM csomaggal kell beállítania.

## <a name="step-1-obtain-a-key"></a>1. lépés: Kulcs beszerzése
    
A méréseket vesz el, és elküldte a Traffic Manager az ügyfélalkalmazás által azonosított egy egyedi karakterlánc, az úgynevezett valós felhasználói mérések (RUM) kulcs. Beszerezhet egy RUM-kulcsot az Azure Portalon, egy REST API-t vagy a PowerShell / CLI-felületek használatával.

A RUM-kulcs beszerzése az Azure Portal használatával az alábbi eljárással:
1. Egy böngészőben jelentkezzen be az Azure Portalra. Ha még nincs fiókja, regisztrálhat egy egy hónapos ingyenes próbaverzióra.
2. A portál keresősávjában keressen rá a módosítani kívánt Traffic Manager-profil nevére, majd kattintson a Traffic Manager-profilra a megjelenített eredmények között.
3. A Traffic Manager profillapon kattintson a **Valós felhasználói mérések** elemre a Beállítások **csoportban.**
4. Új RUM-kulcs létrehozásához kattintson a **Kulcs létrehozása** gombra.
        
   ![Valós felhasználói mérések kulcs létrehozása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: Valós felhasználói mérések kulcsgenerálása**

5. A lapon megjelenik a létrehozott RUM-kulcs és a HTML-lapba beágyazandó JavaScript-kódrészlet.
 
   ![Javascript kód a valós felhasználói mérések kulcshoz](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **2. ábra: Valós felhasználói mérések kulcs és mérés JavaScript**
 
6. Kattintson a **Másolás** gombra a RUM-kulcs másolásához. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2. lépés: Az alkalmazás instrumentálása a Mobile Center SDK RUM csomagjával

Ha most jön a Visual Studio Mobile Center, látogasson el a [webhelyére.](https://mobile.azure.com) Az SDK-integrációval kapcsolatos részletes utasításokról az [Android SDK – Első lépések](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)című témakörben talál.

A valós felhasználói mérések használatához hajtsa végre az alábbi eljárást:

1.  Az SDK hozzáadása a projekthez

    Az ATM RUM SDK előzetes verziója során explicit módon kell hivatkoznia a csomagtárházra.

    Az **alkalmazás/build.gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Az **alkalmazás/build.gradle** fájlban adja hozzá a következő sorokat:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Az SDK indítása

    Nyissa meg az alkalmazás fő tevékenységosztályát, és adja hozzá a következő importálási kimutatásokat:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Keresse meg `onCreate` a visszahívást ugyanabban a fájlban, és adja hozzá a következő kódot:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>További lépések
- További információ a [valós felhasználói mérésekről](traffic-manager-rum-overview.md)
- A [Traffic Manager működésének megismerése](traffic-manager-overview.md)
- További információ a [Mobilközpontról](https://docs.microsoft.com/mobile-center/)
- [Regisztráció](https://mobile.azure.com) a Mobilközpontra
- További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)
- További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-create-profile.md)

