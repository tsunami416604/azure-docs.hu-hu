---
title: "Az Azure AD v2 Android első lépések – konfigurálása |} Microsoft Docs"
description: "Android-alkalmazás hogyan szereznie egy hozzáférési jogkivonatot és hívható meg Microsoft Graph API-val vagy a hozzáférési jogkivonatok az Azure Active Directory v2 végpont igénylő API-k"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c09937582118ebcc5b8cbc1f43a0a2019f2f7a89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz

Ebben a lépésben kell hozzáadnia az ügyfél-Azonosítót a projekthez.

1.  Nyissa meg `MainActivity` (alatt `app`  >  `java`  >   *`{host}.{namespace}`* )
2.  Cserélje le a kezdetű sort `final static String CLIENT_ID` együtt:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Nyissa meg:`app` > `manifests` > `AndroidManifest.xml`
4. Adja hozzá a következő tevékenységek `manifest\application` csomópont. A regisztráció egy `BrowserTabActivity` engedélyezi a hitelesítés befejezése után az alkalmazás folytatja az operációs rendszer:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

### <a name="what-is-next"></a>Mi az a következő

[Tesztelése és érvényesítése](active-directory-mobileanddesktopapp-android-test.md)
