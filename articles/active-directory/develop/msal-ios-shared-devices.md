---
title: Megosztott eszköz mód iOS-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: További információ arról, hogyan engedélyezheti a megosztott eszközmódot, hogy az Első vonalbeli dolgozók megoszthassák az iOS-eszközöket
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550252"
---
# <a name="shared-device-mode-for-ios-devices"></a>Megosztott eszköz mód iOS-eszközökhöz

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Első vonalban dolgozók, mint a kiskereskedelmi partnerek, a hajózószemélyzet tagjai, és a helyszíni szolgáltatások munkavállalók gyakran használják a megosztott mobil eszköz munkájuk elvégzéséhez. Ezek a megosztott eszközök biztonsági kockázatot jelenthetnek, ha a felhasználók szándékosan vagy sem megosztják jelszavukat vagy PIN-adataikat, hogy hozzáférjenek az ügyfél- és üzleti adatokhoz a megosztott eszközön.

A megosztott eszköz mód lehetővé teszi, hogy az iOS 13-as vagy újabb rendszert úgy állítsa be, hogy azokat az alkalmazottak könnyebben és biztonságosabban osztják meg. Az alkalmazottak gyorsan bejelentkezhetnek és hozzáférhetnek az ügyfelek adataihoz. Amikor befejezték a műszakot vagy a feladatot, kijelentkezhetnek az eszközről, és azonnal készen állnak a következő alkalmazott használatára.

A megosztott eszköz mód az eszköz Microsoft identitással támogatott felügyeletét is biztosítja.

Ez a szolgáltatás a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md) használja az eszközön lévő felhasználók kezelésére és a [Microsoft Enterprise Egyszeri bejelentkezés Apple-eszközökre történő terjesztésére.](apple-sso-plugin.md)

## <a name="create-a-shared-device-mode-app"></a>Megosztott eszközmódú alkalmazás létrehozása

Megosztott eszközmódú alkalmazás létrehozásához a fejlesztők és a felhőalapú eszközök rendszergazdái együttműködnek:

1. **Az alkalmazásfejlesztők** egyfiókos alkalmazást írnak (a többfiókos alkalmazások nem támogatottak megosztott eszköz módban), és olyan kódot írnak, amely kezeli a megosztott eszközkijelentkezést.

1. **Az eszközadminisztrátorok előkészítik** az eszközt a megosztásra egy mobileszköz-felügyeleti (MDM) szolgáltató, például a Microsoft Intune használatával a szervezetükben lévő eszközök kezeléséhez. Az MDM leküldéses a Microsoft Authenticator alkalmazást az eszközökre, és bekapcsolja a "Megosztott mód" minden eszköz egy profil frissítés az eszközre. Ez a megosztott mód beállítás módosítja az eszközön támogatott alkalmazások viselkedését. Az MDM-szolgáltató tól kapott konfiguráció beállítja az eszköz megosztott eszközmódját, és engedélyezi a Microsoft Enterprise Egyszeri bejelentkezést az [Apple-eszközökhöz,](apple-sso-plugin.md) amely a megosztott eszközmódhoz szükséges.

1. [**Csak nyilvános előzetes verzióban szükséges**] A [Felhőeszköz-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) szerepkörrel rendelkező felhasználóknak el kell indítaniuk a [Microsoft Authenticator alkalmazást,](../user-help/user-help-auth-app-overview.md) és csatlakozniuk kell az eszközükhöz a szervezethez.

    A szervezeti szerepkörök tagságának konfigurálása az Azure Portalon: **Azure Active Directory** > **szerepkörök és rendszergazdák** > **felhőalapú eszközfelügyelő**

Az alábbi szakaszok segítségével frissítheti az alkalmazást a megosztott eszközmód támogatásához.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Az Intune használata a megosztott eszközmód engedélyezéséhez & SSO-bővítmény

> [!NOTE]
> A következő lépés csak nyilvános előzetes verzióban szükséges.

Az eszközt úgy kell konfigurálni, hogy támogassa a megosztott eszközmódot. Az iOS 13+ rendszert telepíteni kell, és be kell iratkozni. Az MDM-konfigurációnak engedélyeznie kell a [Microsoft Enterprise SSO beépülő modult is az Apple-eszközökhöz.](apple-sso-plugin.md) Ha többet szeretne megtudni az SSO-bővítményekről, tekintse meg az [Apple-videót.](https://developer.apple.com/videos/play/tech-talks/301/)

1. Az Intune konfigurációs portálján mondja meg az eszköznek, hogy engedélyezze a Microsoft Enterprise Egyszeri bejelentkezést az [Apple-eszközökhöz](apple-sso-plugin.md) a következő konfigurációval:

    - **Típus**: Átirányítás
    - **Extension ID**: com.microsoft.azureauthenticator.ssoextension
    - **Csapat azonosítója**: SGGM6D27TK
    - **URL-címek:**https://login.microsoftonline.com
    - További konfigurálandó adatok:
      - Kulcs: sharedDeviceMode
      - Típus: Logikai
      - Érték: Igaz

    Az Intune-nal való konfigurálásról az [Intune konfigurációs dokumentációjában](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)olvashat bővebben.

1. Ezután konfigurálja az MDM-et úgy, hogy a Microsoft Authenticator alkalmazást egy MDM-profilon keresztül az eszközre tolja.

    A Megosztott eszköz mód bekapcsolására a következő konfigurációs beállításokat kell beállítani:

    - 1. konfiguráció:
      - Kulcs: sharedDeviceMode
      - Típus: Logikai
      - Érték: Igaz

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Az iOS-alkalmazás módosítása a megosztott eszközmód támogatásához

A felhasználók attól függenek, hogy az adataik nem szivárogtak-e ki egy másik felhasználónak. A következő szakaszok hasznos jelzéseket adnak az alkalmazásnak, hogy változás történt, és kezelni kell.

Ön a felelős azért, hogy minden alkalommal ellenőrizze a felhasználó állapotát az eszközön, amikor az alkalmazást használja, majd törölje az előző felhasználó adatait. Ez magában foglalja, ha újra betölti a háttérben a többfeladatos.

A felhasználói módosítás esetén gondoskodnia kell arról, hogy az előző felhasználó adatai törlődjenek, és hogy az alkalmazásban megjelenő gyorsítótárazott adatok törlődjenek. Javasoljuk, hogy önnek és vállalatának végezzen biztonsági ellenőrzési folyamatot az alkalmazás frissítése után, hogy támogassa a megosztott eszközmódot.

### <a name="detect-shared-device-mode"></a>Megosztott eszköz mód észlelése

A megosztott eszköz mód észlelése fontos az alkalmazás számára. Számos alkalmazás felhasználói élményének (UX) módosítására lesz szükség, ha az alkalmazást megosztott eszközön használják. Előfordulhat például, hogy az alkalmazás rendelkezik egy "Regisztráció" funkcióval, amely nem megfelelő az első vonalbeli dolgozó számára, mert valószínűleg már rendelkezik fiókkal. Előfordulhat, hogy további biztonságot szeretne adni az alkalmazás adatkezeléséhez, ha megosztott eszköz módban van.

Az `getDeviceInformationWithParameters:completionBlock:` API segítségével `MSALPublicClientApplication` határozza meg, ha egy alkalmazás fut egy eszközön megosztott eszköz módban.

A következő kódrészletek példákat `getDeviceInformationWithParameters:completionBlock:` mutatnak be az API használatára.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>A bejelentkezett felhasználó beszereznie, és annak megállapítása, hogy a felhasználó megváltozott-e az eszközön

A megosztott eszközmód támogatásának másik fontos része a felhasználó állapotának meghatározása az eszközön, és az alkalmazásadatok törlése, ha a felhasználó megváltozott, vagy ha egyáltalán nincs felhasználó az eszközön. Ön felelős annak biztosításáért, hogy az adatok ne szivárogjanak ki egy másik felhasználónak.

Az API `getCurrentAccountWithParameters:completionBlock:` segítségével lekérdezheti a jelenleg bejelentkezett fiókot az eszközön.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globális bejelentkezés egy felhasználóba

Ha egy eszköz megosztott eszközként van konfigurálva, az alkalmazás meghívhatja az `acquireTokenWithParameters:completionBlock:` API-t a fiókba való bejelentkezéshez. A fiók globálisan elérhető lesz az eszközön lévő összes jogosult alkalmazáshoz, miután az első alkalmazás aláírja a fiókot.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Felhasználó globális kijelentkezése

A következő kód eltávolítja a bejelentkezett fiókot, és törli a gyorsítótárazott jogkivonatokat nem csak az alkalmazásból, hanem a megosztott eszköz módban lévő eszközről is. Ez azonban nem törli az *adatokat* az alkalmazásból. Törölnie kell az adatokat az alkalmazásból, valamint törölnie kell az alkalmazás által a felhasználó számára megjelenített gyorsítótárazott adatokat.

#### <a name="clear-browser-state"></a>Böngésző állapotának törlése

> [!NOTE]
> A következő lépés csak nyilvános előzetes verzióban szükséges.

Ebben a nyilvános előzetes verzióban az [Apple-eszközökmicrosoft Enterprise Egyszeri bejelentkezés e-kiszolgálója](apple-sso-plugin.md) csak az alkalmazások esetében törli az állapotot. Nem egyértelmű, hogy a Safari böngészője állapota nem egyértelmű. Azt javasoljuk, hogy manuálisan törölje a böngészőmunkamenetet, hogy a felhasználói állapot nyomai ne maradhassanak hátra. Az alábbi opcionális `signoutFromBrowser` tulajdonsággal törölheti a cookie-kat. Ez azt eredményezi, hogy a böngésző rövid időre elindul az eszközön.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>További lépések

A megosztott eszközmód működés közbeni megtekintéséhez a Következő kódminta a GitHubon tartalmaz egy példát egy Firstline Worker alkalmazás futtatására egy iOS-eszközön megosztott eszköz módban:

[MSAL iOS Swift Microsoft Graph API minta](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
