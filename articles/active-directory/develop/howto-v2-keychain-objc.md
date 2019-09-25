---
title: Kulcstartó konfigurálása | Microsoft Identity platform
description: Ismerje meg, hogyan konfigurálhatja a kulcstartót úgy, hogy az alkalmazás gyorsítótárazza a tokeneket a kulcstartóban.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e85fc5e6e907e32c0ad67af339c48cf84ef4764
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269037"
---
# <a name="configure-keychain"></a>Kulcstartó konfigurálása

Ha az [iOS-és MacOS](msal-overview.md) -(MSAL-) Microsoft-hitelesítési kódtár egy felhasználónál jelentkezik be, vagy egy tokent frissít, megkísérli a kulcstartóban lévő tokenek gyorsítótárazását. A kulcstartóban lévő gyorsítótárazási tokenek révén a MSAL csendes egyszeri bejelentkezést (SSO-t) biztosít az azonos Apple Developer által terjesztett több alkalmazás között. Az egyszeri bejelentkezés a kulcstartó-hozzáférési csoportok funkción keresztül érhető el (lásd az [Apple dokumentációját](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)).

Ez a cikk bemutatja, hogyan konfigurálhatja az alkalmazás jogosultságait úgy, hogy a MSAL képes legyen gyorsítótárazott tokeneket írni az iOS-és macOS-kulcstartóba.

## <a name="default-keychain-access-group"></a>Alapértelmezett kulcstartó-hozzáférési csoport

### <a name="ios"></a>iOS

Az iOS-MSAL alapértelmezés `com.microsoft.adalcache` szerint a hozzáférési csoportot használja. Ez a MSAL és az Azure AD Authentication Library (ADAL) SDK-k által használt közös hozzáférésű csoport, amely biztosítja a legjobb egyszeri bejelentkezést (SSO) az ugyanazon közzétevőtől származó több alkalmazás között.

Az iOS-ben adja `com.microsoft.adalcache` hozzá a kulcstartó csoportot az alkalmazás jogosultságához a Xcode**funkciók** > **kulcstartó megosztása** **területén.**  > 

### <a name="macos"></a>macOS

A MacOS-MSAL `com.microsoft.identity.universalstorage` alapértelmezés szerint a hozzáférési csoportot használja.

A MacOS-es `access group` kulcstartó korlátozásai miatt a MSAL nem fordítja le közvetlenül a kulcstartó-hozzáférési csoport attribútumára (lásd: [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) a MacOS 10,14-es és korábbi verzióiban. Ugyanakkor az egyszeri bejelentkezéshez hasonlóan viselkedik, mert így biztosítható, hogy az azonos Apple Developer által terjesztett több alkalmazás is csendes SSO-val rendelkezzen.

MacOS 10,15-től (macOS Catalina) a MSAL a kulcstartó-hozzáférési csoport attribútumot használja a csendes SSO eléréséhez, hasonlóan az iOS-hez.

## <a name="custom-keychain-access-group"></a>Egyéni kulcstartó-hozzáférési csoport

Ha más kulcstartó-hozzáférési csoportot szeretne használni, akkor a létrehozás `MSALPublicClientApplicationConfig` `MSALPublicClientApplication`előtt átadhatja az egyéni csoportot, a következőhöz hasonlóan:

Objective-C:

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



Swift

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



## <a name="disable-keychain-sharing"></a>Kulcstartó megosztásának letiltása

Ha nem szeretné megosztani az egyszeri bejelentkezési állapotot több alkalmazás között, vagy bármilyen kulcstartó-hozzáférési csoportot használ, tiltsa le a kulcstartó-megosztást úgy, hogy átadja az alkalmazáscsomag-azonosítót a keychainGroup:

Objective-C:

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

Swift

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle-34018 hiba (az elemek nem állíthatók be a kulcstartóba)

Hiba – a 34018 általában azt jelenti, hogy a kulcstartó nincs megfelelően konfigurálva. Győződjön meg arról, hogy a MSAL konfigurált kulcstartó-hozzáférési csoport megfelel a jogosultságokban konfiguráltnak.

## <a name="ensure-your-application-is-properly-signed"></a>Győződjön meg arról, hogy az alkalmazás megfelelően van aláírva

MacOS rendszeren az alkalmazások fejlesztői regisztráció nélkül is futtathatók. Habár a legtöbb MSAL funkciója továbbra is működni fog, az SSO-t a kulcstartó-hozzáférésen keresztül kell aláírni. Ha több kulcstartó-kérést tapasztal, győződjön meg arról, hogy az alkalmazás aláírása érvényes.

## <a name="next-steps"></a>További lépések

További információ a kulcstartó-hozzáférési csoportokról az Apple [megosztási hozzáférésének megadásához az alkalmazások gyűjteménye között](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) cikk.
