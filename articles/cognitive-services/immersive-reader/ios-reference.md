---
title: Az olvasói iOS SDK-referenciája
titleSuffix: Azure Cognitive Services
description: Útmutató a beolvasó iOS SDK-hoz
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 615c09dd8a7287918bb009ce11854278b21554c1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899415"
---
# <a name="immersive-reader-sdk-reference"></a>A részletes olvasó SDK-referenciája

A lebilincselő olvasó iOS SDK egy gyors CocoaPod, amely lehetővé teszi, hogy az iOS-alkalmazásba integrálja a magával ragadó olvasót.

## <a name="functions"></a>Funkciók

Az SDK egyetlen függvényt `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`tesz elérhetővé.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Elindítja a lebilincselő olvasót az iOS-alkalmazásban található View vezérlő elindításával.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Paraméterek

| Name (Név) | Típus | Leírás |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Az iOS-alkalmazás navigációs vezérlője, amelyet a függvény hívása folyamatban van. |
| `token` | Sztring | Az Azure AD hitelesítési jogkivonata. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `subdomain` | Sztring | Az Azure-beli magától elolvasó erőforrás egyedi altartománya. Lásd: [Azure ad-hitelesítés – útmutató](./azure-active-directory-authentication.md). |
| `content` | [Tartalom](#content) | Egy objektum, amely a magába foglaló olvasóban megjelenítendő tartalmat tartalmazza. |
| `options` | [Beállítások](#options) | Beállítások a magával ragadó olvasó bizonyos viselkedésének konfigurálásához. Nem kötelező. |
| `onSuccess` | () – > Void | A magával ragadó olvasó sikeres elindítása után meghívott Bezárás. |
| `onFailure` | (_ hiba: [Hiba](#error)) – > Void | Egy olyan Bezárás, amely akkor lép fel, amikor az olvasó nem töltődik be. Ez a Bezárás egy [`Error`](#error) olyan objektumot ad vissza, amely a hibához tartozó hibakódot és hibaüzenetet jelöli. További információ: hibakódok. [](#error-codes) |

## <a name="types"></a>Típusok

### <a name="content"></a>Tartalom

A lebilincselő olvasóban megjelenítendő tartalmat tartalmazza.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Támogatott MIME-típusok

| MIME-típus | Leírás |
| --------- | ----------- |
| szöveg/egyszerű | Egyszerű szöveg. |
| Application/MathML + XML | Matematikai Markup Language (MathML). [További információk](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Beállítások

Azokat a tulajdonságokat tartalmazza, amelyek a magába foglaló olvasó bizonyos viselkedéseit konfigurálják.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Hiba

A hibával kapcsolatos információkat tartalmaz.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Hibakódok

| Kód | Leírás |
| ---- | ----------- |
| BadArgument | A megadott argumentum érvénytelen. a `message` részletekért tekintse meg a következőt:. |
| Időtúllépés | Nem sikerült betölteni a magával ragadó olvasót a megadott időkorláton belül. |
| TokenExpired | A megadott jogkivonat lejárt. |
| Szabályozott | Túllépte a hívási sebesség korlátját. |
| InternalError | Belső hiba történt a megtekintő olvasó nézet vezérlőn belül. Részletekért lásd: `message` .|

## <a name="os-version-support"></a>Operációs rendszer verziójának támogatása

Az iOS 9,0-es vagy újabb verzióban az olvasói iOS-SDK az iPaden és az iPhone-on is támogatott.

## <a name="next-steps"></a>További lépések

* Fedezze fel az [olvasói iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) -t a githubon
* [Rövid útmutató: Hozzon létre egy iOS-alkalmazást, amely elindítja a részletes olvasót (Swift)](./ios-quickstart.md)