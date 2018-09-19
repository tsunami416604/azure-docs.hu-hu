---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f0e584a4a4a54fc04b5539b56d5c901bfaa42bcc
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293760"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása
Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="option-1-express"></a>1. lehetőség: expressz
1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  A **alkalmazásnév**, adjon meg egy nevet az alkalmazásnak.

3. Ügyeljen arra, hogy a **interaktív telepítés** kiválasztott, és adja meg a jelölőnégyzet nincs **létrehozás**.

4. Kövesse az utasításokat az Alkalmazásazonosító beszerzése, és illessze be a kódot.

### <a name="option-2-advanced"></a>2. lehetőség: speciális 
1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app).
2. Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak. 

3. Ügyeljen arra, hogy a **interaktív telepítés** üres, és adja meg a jelölőnégyzet nincs **létrehozás**.

4. Válassza ki **Platform hozzáadása**válassza **natív alkalmazás**, majd válassza ki **mentése**.

5. A **alkalmazás** > **java** > **{gazdagép}. { névtér}**, nyissa meg `MainActivity`. 

6.  Cserélje le *[adja meg az alkalmazás azonosítója itt]* az alkalmazással vagy ügyfél-azonosító:

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
