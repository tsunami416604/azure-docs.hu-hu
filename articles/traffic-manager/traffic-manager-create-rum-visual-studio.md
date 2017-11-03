---
title: "Az az Azure Traffic Manager a Visual Studio Mobile központtal felhasználó mérések |} Microsoft Docs"
description: "A Visual Studio Mobile Center küldése valós felhasználói mérések felvétele a Traffic Manager segítségével létrehozott mobilalkalmazás beállítása"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 756496e5291d932ee9ac89265291e6892c4304fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Valós felhasználói mérések felvétele a Traffic Manager Visual Studio Mobile Center küldése

>[!NOTE]
>A tényleges felhasználói mérések szolgáltatást a Traffic Manager nyilvános előzetes verziójában, és nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. A szolgáltatás nem támogatott, van, korlátozott képességeket, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure Traffic Manager-frissítések](https://azure.microsoft.com/updates/?product=traffic-manager) lap.

A Visual Studio Mobile Center küldendő valós felhasználói mérések felvétele a Traffic Manager következő lépések segítségével létrehozott mobilalkalmazás állíthat be:

>[!NOTE]
> Jelenleg valós felhasználói mérések küld a Traffic manager használata csak támogatott Android rendszeren.

Valós felhasználói mérések konfigurálásához meg kell-kulcs beszerzése és állíthatnak be az alkalmazás a RUM csomag.

## <a name="step-1-obtain-a-key"></a>1. lépés: A kulcs beszerzése
    
A mérési igénybe vehet, és az ügyfélalkalmazás által kiküldött felvétele a Traffic Manager azonosítja a szolgáltatást egy egyedi karakterlánc, a tényleges felhasználói mérések (RUM) kulcsot. Az Azure portál, a REST API használatával RUM kulcs kaphat vagy a PowerShell használatával / CLI felületek.

Az Azure-portálon az alábbi eljárást követve RUM kulcs beszerzése:
   1. Egy böngészőből jelentkezzen be az Azure-portálon. Ha már nincs fiókja, regisztrálhat egy ingyenes egy hónapos próbaverzióra.
   2. A portál keresősávban, keresse meg a módosítani kívánt Traffic Manager-profil neve, és kattintson a Traffic Manager-profilt az eredmények között, amelyek a jelennek meg.
   3. A Traffic Manager oldalon, kattintson **valós felhasználói mérések** alatt **beállítások**.
   4. Kattintson a **készítése kulcs** RUM új kulcs létrehozásához.
        
   ![Valós felhasználói mérések kulcs létrehozása](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **1. ábra: Valós felhasználói mérések kulcs létrehozása**

   5.   A lap megjeleníti a RUM kulcs, amely akkor jön létre, és a JavaScript kódrészletet, amelyet a beilleszthető a HTML-lapot.
 
   ![Valós felhasználói mérések kulcs JavaScript-kód](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **2. ábra: Felhasználó mérések kulcs és a mérési JavaScript**
 
   6. Kattintson a **másolási** RUM kulcsának az átmásolása gombra. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2. lépés: Állíthatnak be az alkalmazás a Mobile Center SDK RUM csomag

Ha most ismerkedik a Visual Studio Mobile Center, látogasson el a [webhely](https://mobile.azure.com). Az SDK-integráció részletes utasításokért lásd: [Bevezetés az Android SDK-val](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

A tényleges felhasználói mérések használatához kövesse az alábbi lépéseket:

1.  Az SDK hozzáadása a projekthez

    Az előzetes ATM RUM SDK explicit módon a csomag összetevőtárházat hivatkoznia kell.

    Az a **app/build.gradle** fájlt adja hozzá a következő sorokat:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Az a **app/build.gradle** fájlt adja hozzá a következő sorokat:

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

## <a name="next-steps"></a>Következő lépések
- További információ [valós felhasználói mérések](traffic-manager-rum-overview.md)
- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- További információ [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Regisztráció](https://mobile.azure.com) Mobile Center
- További információ a [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) Traffic Manager által támogatott
- Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

