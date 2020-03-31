---
title: Az Azure AD B2C által támogatott alkalmazástípusok
titleSuffix: Azure AD B2C
description: Ismerje meg, hogy milyen típusú alkalmazásokat használhat az Azure Active Directory B2C-vel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190143"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Az Active Directory B2C könyvtárban használható alkalmazástípusok

Az Azure Active Directory B2C (Azure AD B2C) számos modern alkalmazásarchitektúra hitelesítését támogatja. Ezek mindegyike az iparági szabványnak számító [OAuth 2.0](protocols-overview.md) vagy [OpenID Connect](protocols-overview.md) protokollon alapul. Ez a cikk ismerteti, hogy milyen típusú alkalmazásokat hozhat létre, függetlenül a kívánt nyelvtől vagy platformtól. A cikk segíthet az összetettebb feladatok megértésében, ezért érdemes elolvasni, mielőtt nekifog az alkalmazások létrehozásának.

Az Azure AD B2C-t használó minden alkalmazást regisztrálni kell az [Azure AD B2C-bérlőben](tutorial-create-tenant.md) az [Azure Portal](https://portal.azure.com/)használatával. A jelentkezési regisztrációs folyamat értékeket gyűjt és rendel hozzá, például:

* **Alkalmazásazonosító,** amely egyedileg azonosítja az alkalmazást.
* **Válasz URL-cím,** amely segítségével közvetlen válaszokat az alkalmazás.

Az Azure AD B2C-nek küldött minden egyes kérelem egy **felhasználói folyamatot** (beépített szabályzatot) vagy egy **egyéni szabályzatot** határoz meg, amely az Azure AD B2C viselkedését szabályozza. Mindkét házirendtípus lehetővé teszi, hogy nagymértékben testreszabható felhasználói élményt hozzon létre.

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás irányítja a felhasználót, hogy a v2.0 végpont ot a [házirend](user-flow-overview.md)végrehajtásához.
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás kap egy biztonsági jogkivonatot a v2.0 végpont.
4. Az alkalmazás a biztonsági jogkivonatot használja a védett információk vagy védett erőforrások eléréséhez.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

Ezek a lépések némileg eltérhetnek az ön által létrehozható alkalmazás típusától függően.

## <a name="web-applications"></a>Webalkalmazások

A kiszolgálón üzemeltetett és böngészőn keresztül elérhető webalkalmazások (beleértve a .NET, PHP, Java, Ruby, Python és Node.js) alkalmazásokat, amelyek et egy kiszolgálón üzemeltetik, és amelyek böngészőn keresztül érhetők el, az Azure AD B2C minden felhasználói élményhez támogatja az [OpenID Connect szolgáltatást.](protocols-overview.md) Az OpenID Connect Azure AD B2C implementációjában a webalkalmazás hitelesítési kérelmek et bocsát ki az Azure AD-nek. A kérés eredménye egy `id_token`. Ez a biztonsági jogkivonat tartalmazza a felhasználó identitását. Ezenfelül jogcímek formájában információkat nyújt a felhasználóról is:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

További információ az [Azure AD B2C tokenhivatkozásban](tokens-overview.md)egy alkalmazás számára elérhető jogkivonatok és jogcímek típusairól.

Egy webalkalmazásban a [házirendek](user-flow-overview.md) minden egyes végrehajtása a következő magas szintű lépéseket teszi:

1. A felhasználó megkeresi a webalkalmazást.
2. A webalkalmazás átirányítja a felhasználót az Azure AD B2C-re, jelezve a végrehajtandó szabályzatot.
3. A felhasználó befejezi a házirendet.
4. Az Azure AD B2C visszaadja a `id_token` böngészőbe.
5. Az `id_token` fel van könyvelve az átirányítási URI-ba.
6. A `id_token` validált, és a munkamenet cookie van beállítva.
7. A rendszer egy biztonságos lapot ad vissza a felhasználónak.

A felhasználó identitásának ellenőrzéséhez elegendő az Azure AD-tól kapott nyilvános aláírókulcs segítségével ellenőrizni a `id_token`érvényességét. Ez a folyamat beállít egy munkamenet-cookie-t is, amely a felhasználó azonosítására használható a következő oldalkérelmekben.

Ha működés közben szeretné látni ezt a forgatókönyvet, próbálja ki a webalkalmazás bejelentkezési kódmintáit az [Első lépések szakaszban.](overview.md)

Az egyszerű bejelentkezés megkönnyítése mellett előfordulhat, hogy egy webkiszolgáló-alkalmazásnak is hozzá kell férnie egy háttéralapú webszolgáltatáshoz. Ebben az esetben a webalkalmazás egy kissé eltérő [OpenID Connect folyamatot](openid-connect.md) hajthat végre, és jogkivonatokat szerezhet be engedélyezési kódok és frissítési jogkivonatok használatával. Ezt a folyamatot a következő, [Webes API-k](#web-apis) című fejezet írja le.

## <a name="web-apis"></a>Webes API-k

Az Azure AD B2C használatával biztonságos sáfőket, például az alkalmazás RESTful webes API-ját biztosíthatja. A webes API-k az OAuth 2.0 használatával biztosíthatják az adatok védelmét a bejövő HTTP-kérések jogkivonatokkal történő hitelesítésével. A webes API hívója hozzáfűz egy jogkivonatot a HTTP-kérés hitelesítési fejlécéhez:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Így a webes API a jogkivonat segítségével ellenőrizheti az API hívójának identitását, valamint a jogkivonatban kódolt jogcímek segítségével további információkhoz juthat a hívóról. Az [Azure AD B2C-jogkivonatok referenciájából](tokens-overview.md) további információkat tudhat meg az alkalmazásban elérhető jogkivonatok és jogcímek különböző típusairól.

A webes API-k számos típusú ügyféltől fogadhatnak jogkivonatokat, beleértve a webalkalmazásokat, az asztali és mobilalkalmazásokat, az egyoldalas alkalmazásokat, a kiszolgálóoldali démonokat és más webes API-kat. Íme egy példa egy webes API-t meghívja egy webes API-t meghívja webalkalmazás teljes folyamatára:

1. A webalkalmazás végrehajt egy házirendet, és a felhasználó befejezi a felhasználói élményt.
2. Az Azure AD B2C egy (OpenID Connect) `id_token` és egy engedélyezési kódot ad vissza a böngészőnek.
3. A böngésző `id_token` az átirányítási URI-ba teszi a és az engedélyezési kódot.
4. A webkiszolgáló ellenőrzi `id_token` a és beállít egy munkamenet-cookie-t.
5. A webkiszolgáló kéri az Azure AD B2C egy `access_token` az engedélyezési kódot, az alkalmazás ügyfél-azonosítót és az ügyfél hitelesítő adatait.
6. A `access_token` `refresh_token` és visszakerül a webkiszolgálóra.
7. A webes API-t `access_token` egy engedélyezési fejlécben hívják meg.
8. A webes API érvényesíti a jogkivonatot.
9. A biztonságos adatok at visszaadja a webalkalmazásnak.

Ha többet szeretne tudni a hitelesítési kódokról, frissítési jogkivonatokról, valamint a jogkivonatok lekérésének lépéseiről, olvasson az [OAuth 2.0-protokollról](authorization-code-flow.md).

Ha szeretné megtanulni, hogyan biztosíthat védelmet a webes API-k számára az Azure AD B2C segítségével, olvassa el a [kezdeti lépéseket bemutató cikk](overview.md) webes API-kra vonatkozó oktatóanyagát.

## <a name="mobile-and-native-applications"></a>Mobil és natív alkalmazások

Az eszközökre, például a mobil- és asztali alkalmazásokra telepített alkalmazásoknak gyakran a felhasználók nevében kell hozzáférniük a háttérszolgáltatásokhoz vagy a webes API-khoz. Az Azure AD B2C és az [OAuth 2.0 engedélyezési kódfolyamat](authorization-code-flow.md)használatával testre szabott identitáskezelési élményeket adhat hozzá a natív alkalmazásokhoz, és biztonságosan hívhatja meg a háttérszolgáltatásokat.

Ebben a folyamatban az alkalmazás [szabályzatokat](user-flow-overview.md) `authorization_code` hajt végre, és kap egy Azure AD-től, miután a felhasználó befejezi a szabályzatot. Ez `authorization_code` az alkalmazás azon engedélyét jelöli, amely a jelenleg bejelentkezett felhasználó nevében hívja vissza a háttérszolgáltatásokat. Az alkalmazás ezután `authorization_code` cserélje ki `access_token` a `refresh_token`háttérben egy és egy .  Az alkalmazás használhatja a `access_token` hitelesítést egy háttérwebes API HTTP-kérelmekben. Az `refresh_token` alkalmas ezenfelül új `access_token` kérésére is, ha a régi lejárna.

## <a name="current-limitations"></a>Aktuális korlátozások

### <a name="unsupported-application-types"></a>Nem támogatott alkalmazástípusok

#### <a name="daemonsserver-side-applications"></a>Démonok/kiszolgálóoldali alkalmazások

A hosszú ideig futó folyamatokat tartalmazó vagy a felhasználó jelenléte nélkül működő alkalmazásoknak is szükségük van a biztonságos erőforrások, például a webes API-k elérésének módjára. Ezek az alkalmazások hitelesíthetik és jogkivonatokat kaphatnak az alkalmazás identitásának használatával (nem a felhasználó delegált identitásával), valamint az OAuth 2.0 ügyfél hitelesítő adatok folyamatának használatával. Az ügyfél hitelesítő adatok áramlása nem ugyanaz, mint a nevében történő folyamat, és a kiszolgálók között történő hitelesítéshez nem használható a nevében történő folyamat.

Bár az ügyfél hitelesítő adatok áramlását jelenleg nem támogatja az Azure AD B2C, beállíthatja az ügyfél hitelesítő adatok áramlását az Azure AD használatával. Egy Azure AD B2C-bérlő bizonyos funkciókat megoszt az Azure AD vállalati bérlőivel.  Az ügyfél hitelesítő adatok folyamata az Azure AD B2C-bérlő Azure AD-funkcióinak használatával támogatott.

Az ügyfélhitelesítő adatok folyamatának beállításához olvassa el [az Azure Active Directory 2.0-s és az OAuth 2.0-s ügyfélhitelesítő adatok folyamatát.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds) A sikeres hitelesítés egy úgy formázott jogkivonat fogadását eredményezi, hogy az Azure AD az [Azure AD-re vonatkozó hivatkozásban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)leírtak szerint használhassa.

#### <a name="web-api-chains-on-behalf-of-flow"></a>Webes API-láncok (meghatalmazásos folyamat)

Számos architektúrában szerepelnek olyan webes API-k, amelyek más, alsóbb rétegbeli webes API-kat hívnak meg, és mindkét API biztonságát az Azure AD B2C garantálja. Ez a forgatókönyv gyakori a webes API-háttérrendszerrel rendelkező natív ügyfeleknél, és meghívja a Microsoft online szolgáltatását, például a Microsoft Graph API-t.

Ez a láncolatba fűzött webes API-megoldás az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával (vagy más néven a meghatalmazásos folyamat) segítségével valósítható meg.  A meghatalmazásos folyamatot azonban még nem implementáltuk az Azure AD B2C-be.

### <a name="faulted-apps"></a>Hibás alkalmazások

Az alábbi módokon ne szerkesztse az Azure AD B2C-alkalmazásokat:

- Más alkalmazáskezelési portálokon, például az [Alkalmazásregisztrációs portálon.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
- Graph API vagy PowerShell használatával.

Ha az Azure AD B2C-alkalmazást az Azure Portalon kívül szerkeszti, az hibás alkalmazássá válik, és az Azure AD B2C-vel már nem használható. Törölje az alkalmazást, és hozza létre újra.

Az alkalmazás törléséhez lépjen az [alkalmazásregisztrációs portálra,](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) és itt törölje az alkalmazást. Ahhoz, hogy láthatóvá lehessen tenni az alkalmazást, Önnek kell lennie az alkalmazás tulajdonosának (és nem csak a bérlő rendszergazdájának).

## <a name="next-steps"></a>További lépések

További információ az [Azure Active Directory B2C felhasználói folyamatai](user-flow-overview.md)által biztosított beépített szabályzatokról.
