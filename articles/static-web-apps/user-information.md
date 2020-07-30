---
title: Felhasználói információk elérése az Azure statikus Web Apps
description: Ismerje meg, hogyan olvashatja el az engedélyezési szolgáltató által visszaadott felhasználói információkat.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-javascript
ms.openlocfilehash: 7e1f56fc4601b271bf4a0718a944741016509ce4
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430524"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Felhasználói információk elérése az Azure statikus Web Apps előzetes verziójában

Az Azure statikus Web Apps a hitelesítéssel kapcsolatos felhasználói adatokat egy [közvetlen hozzáférési végponton](#direct-access-endpoint) és az [API functions](#api-functions)szolgáltatáson keresztül biztosítja.

Számos felhasználói felület nagy mértékben támaszkodik a felhasználói hitelesítési adatokra. A közvetlen elérésű végpont egy olyan segédprogram-API, amely az egyéni függvények megvalósítása nélkül teszi elérhetővé a felhasználói adatokat. A közvetlen elérésű végpont a kényelem mellett nem vonatkozik a kiszolgáló nélküli architektúrához kapcsolódó, nem megfelelő, nem érvényes indítási késésekre.

## <a name="client-principal-data"></a>Ügyfél rendszerbiztonsági adatbázisa

Az ügyfél elsődleges adatobjektuma a felhasználó által azonosítható adatokat teszi elérhetővé az alkalmazás számára. Az ügyfél egyszerű objektumában a következő tulajdonságok szerepelnek:

| Tulajdonság  | Leírás |
|-----------|---------|
| `identityProvider` | Az [identitás-szolgáltató](authentication-authorization.md)neve. |
| `userId` | A felhasználó Azure-beli statikus Web Apps egyedi azonosítója. <ul><li>Az érték egyedi az alkalmazáson belüli alapon. Például ugyanaz a felhasználó más értéket ad vissza `userId` egy másik statikus Web Apps erőforráson.<li>Az érték továbbra is fennáll a felhasználó élettartama. Ha törli és hozzáadja ugyanazt a felhasználót az alkalmazáshoz, a `userId` rendszer létrehoz egy újat.</ul>|
| `userDetails` | A felhasználó felhasználóneve vagy e-mail-címe. Egyes szolgáltatók a [felhasználó e-mail-címét](authentication-authorization.md)adják vissza, míg mások elküldik a [felhasználói leírót](authentication-authorization.md). |
| `userRoles`     | A [felhasználó hozzárendelt szerepköreinek](authentication-authorization.md)tömbje. |

A következő példa egy példa ügyféloldali ügyfél-objektum:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Közvetlen hozzáférési végpont

Küldhet egy `GET` kérést az `/.auth/me` útvonalra, és közvetlen hozzáférést kap az ügyfél elsődleges adatszolgáltatásához. Ha a nézet állapota az engedélyezési adatra támaszkodik, használja ezt a módszert a legjobb teljesítmény érdekében.

A bejelentkezett felhasználók esetében a válasz egy ügyfél-egyszerű JSON-objektumot tartalmaz. A nem hitelesített felhasználóktól érkező kérések visszaadása `null` .

Az<sup>1</sup> . [beolvasás](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)API használatával az alábbi szintaxissal érheti el az ügyfél elsődleges információit.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API-függvények

Az ügyfél elsődleges adatai a kérelem fejlécében szereplő API-függvények számára lettek átadva `x-ms-client-principal` . Az ügyfél rendszerbiztonsági azonosítóját [Base64](https://www.wikipedia.org/wiki/Base64)kódolású karakterláncként küldi el a rendszer, amely egy szerializált JSON-objektumot tartalmaz.

A következő példa azt mutatja be, hogyan lehet beolvasni és visszaadni a felhasználói adatokat.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Feltételezve, hogy a fenti függvény neve `user` , a következő szintaxissal érheti el az API válaszát az<sup>1</sup> . [BEolvasott](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)böngésző API használatával.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> a [beolvasási](https://caniuse.com/#feat=fetch) API és a [várakozási](https://caniuse.com/#feat=mdn-javascript_operators_await) operátor nem támogatott az Internet Explorerben.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásbeállítások konfigurálása](application-settings.md)
