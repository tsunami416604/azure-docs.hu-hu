---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
ms.openlocfilehash: ca7a47fbe2c5ee2a4eb10abf3b9b50a2d28c252e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466182"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása
Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálon*:
1. Regisztrálja az alkalmazást a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-mailben
3.  Győződjön meg arról, hogy az interaktív telepítés esetén alkalmazott beállítás be van jelölve
4.  Kövesse az utasításokat az Alkalmazásazonosító beszerzése, és illessze be a kódot

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban

1.  Lépjen a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app)
2.  Adjon meg egy nevet az alkalmazásnak
3.  Ellenőrizze, hogy az interaktív telepítés a beállítás nincs bejelölve
4.  Kattintson a `Add Platform`, majd `Native Application` kattintson `Save`
5.  Vissza az xcode-ban. A `ViewController.swift`, cserélje le a sort, kezdve "`let kClientID`" az imént regisztrált alkalmazás azonosítójával:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + kattintson <code>Info.plist</code> a helyi menü megjelenítéséhez, majd: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Alatt a <code>dict</code> gyökércsomópont, adja hozzá a következő:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Cserélje le <i> <code>[Your_Application_Id_Here]</code> </i> az imént regisztrált alkalmazás azonosítójával
</li>
</ol>
