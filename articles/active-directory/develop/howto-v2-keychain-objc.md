---
title: Kulcskarika konfigurálása
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan konfigurálhatja a kulcskarikát úgy, hogy az alkalmazás gyorsítótárazza a tokeneket a kulcskarikában.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085860"
---
# <a name="configure-keychain"></a>Kulcskarika konfigurálása

Amikor a [Microsoft Authentication Library for iOS és macOS](msal-overview.md) (MSAL) bejelentkezik egy felhasználóba, vagy frissít egy jogkivonatot, megpróbálja gyorsítótárazni a tokeneket a kulcskarikában. A kulcstartóban lévő gyorsítótárazási tokenek lehetővé teszik az MSAL számára, hogy csendes egyszeri bejelentkezést (SSO) biztosítson több olyan alkalmazás között, amelyeket ugyanaz az Apple-fejlesztő terjeszt. Az sso a kulcskarika-hozzáférési csoportok funkcióin keresztül érhető el. További információt az Apple [kulcskarika-dokumentációjában](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)talál.

Ez a cikk bemutatja, hogyan konfigurálhatja az alkalmazásjogosultságokat, hogy az MSAL gyorsítótárazott jogkivonatokat írhasson az iOS és a macOS kulcskarikába.

## <a name="default-keychain-access-group"></a>Alapértelmezett kulcskarika-hozzáférési csoport

### <a name="ios"></a>iOS

Az iOS-es `com.microsoft.adalcache` MSAL alapértelmezés szerint a hozzáférési csoportot használja. Ez az MSAL és az Azure AD authentication Library (ADAL) SDK-k által használt megosztott hozzáférési csoport, és biztosítja a legjobb egyszeri bejelentkezési (SSO) élményt az ugyanazon közzétevőtől származó több alkalmazás között.

IOS rendszeren adja `com.microsoft.adalcache` hozzá a kulcskarika-csoportot az alkalmazás jogosultságához az XCode alkalmazásban a **Projektbeállítások** > **– Képességek kulcskarika** > **megosztása** csoportban.

### <a name="macos"></a>macOS

MacOS rendszeren az `com.microsoft.identity.universalstorage` MSAL alapértelmezés szerint hozzáférési csoportot használ.

A macOS kulcskarika korlátai miatt `access group` az MSAL nem fordítja le közvetlenül a kulcskarika-hozzáférési csoport attribútumát (lásd: [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)a macOS 10.14-es és korábbi rendszerén. Azonban hasonlóan viselkedik az SSO szempontjából, biztosítva, hogy az ugyanazon Apple-fejlesztő által terjesztett több alkalmazás csendes SSO-val rendelkezzen.

A macOS 10.15-től (macOS Catalina) az MSAL az iOS-hez hasonlóan a kulcslánc-hozzáférési csoport attribútumot használja a csendes SSO eléréséhez.

## <a name="custom-keychain-access-group"></a>Egyéni kulcskarika-hozzáférési csoport

Ha egy másik kulcskarika-hozzáférési csoportot szeretne használni, a létrehozáskor `MSALPublicClientApplicationConfig` `MSALPublicClientApplication`átadhatja az egyéni csoportot a létrehozás előtt, például:

# <a name="objective-c"></a>[Célkitűzés-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Kulcskarika megosztásának letiltása

Ha nem szeretné megosztani az SSO-állapotot több alkalmazás között, vagy nem szeretne kulcskarika-hozzáférési csoportot használni, tiltsa le a kulcskarika-megosztást úgy, hogy az alkalmazásköteg-azonosítót kulcslánccsoportként adja meg:

# <a name="objective-c"></a>[Célkitűzés-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle -34018 hiba (nem sikerült beállítani az elemet a kulcskarikába)

A -34018 hiba általában azt jelenti, hogy a kulcskarika nincs megfelelően konfigurálva. Győződjön meg arról, hogy az MSAL-ban konfigurált kulcskarika-hozzáférési csoport megegyezik a jogosultságokban konfiguráltcsoporttal.

## <a name="ensure-your-application-is-properly-signed"></a>Az alkalmazás megfelelő aláírása

A macOS rendszeren az alkalmazások a fejlesztő aláírása nélkül is végrehajthatók. Míg az MSAL legtöbb funkciója továbbra is működni fog, az SSO kulcskarikás hozzáférésen keresztül az alkalmazást alá kell írni. Ha több kulcskarika-kérdést tapasztal, ellenőrizze, hogy az alkalmazás aláírása érvényes-e.

## <a name="next-steps"></a>További lépések

További információ a kulcskarika-hozzáférési csoportokról az Apple [kulcskarika-elemekhez való hozzáférésének megosztásáról az alkalmazások gyűjteménye között](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) cikkben.
