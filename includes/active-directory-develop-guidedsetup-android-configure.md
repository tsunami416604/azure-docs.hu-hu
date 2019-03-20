---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 2b30f95e050887130db1b2395f51e543a50e25d0
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203729"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="option-1-express"></a>Option 1: Express

1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. A **alkalmazásnév**, adjon meg egy nevet az alkalmazásnak.
3. Ügyeljen arra, hogy a **interaktív telepítés** kiválasztott, és adja meg a jelölőnégyzet nincs **létrehozás**.
4. Kövesse az utasításokat az Alkalmazásazonosító beszerzése, és illessze be a kódot.

### <a name="option-2-advanced"></a>Option 2: Speciális

1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).
2. Az **Alkalmazás neve** mezőben nevezze el az alkalmazását.
3. Ügyeljen arra, hogy az **Irányított beállítás** jelölőnégyzet ne legyen bejelölve, majd válassza a **Létrehozás** lehetőséget.
4. Válassza a **Platform hozzáadása**, a **Natív alkalmazás**, majd a **Mentés** lehetőséget.
5. A **alkalmazás** > **java** > **{gazdagép}. { névtér}**, nyissa meg `MainActivity`. 
6. Cserélje le *[adja meg az alkalmazás azonosítója itt]* az alkalmazással vagy ügyfél-azonosító:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
   <!-- Workaround for Docs conversion bug -->
7. A **alkalmazás** > **jegyzékfájlok**, nyissa meg a *AndroidManifest.xml* fájlt.
8. Az a `manifest\application`, adja hozzá a következő tevékenységet. A `BrowserTabActivity` tevékenységgel, amely lehetővé teszi a Microsoft visszahívja az alkalmazáshoz, a hitelesítés befejezése után:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
   <!-- Workaround for Docs conversion bug -->
9. Az a `BrowserTabActivity`, cserélje le `[Enter the application Id here]` az alkalmazás / ügyfél-azonosító.
