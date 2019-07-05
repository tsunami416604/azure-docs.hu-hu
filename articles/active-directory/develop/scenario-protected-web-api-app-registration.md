---
title: Védett webes API - alkalmazás regisztrációja |} Az Azure
description: Ismerje meg, hogyan hozhat létre egy védett webes API-t és az alkalmazás regisztrálásához szükséges információkat.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536852"
---
# <a name="protected-web-api-app-registration"></a>Védett webes API-hoz: Alkalmazásregisztráció

Ez a cikk ismerteti a alkalmazásregisztráció tulajdonságairól védett webes API-hoz.

Lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](quickstart-register-app.md) regisztrál egy alkalmazást, az általános lépéseit.

## <a name="accepted-token-version"></a>Token elfogadott verzió

A Microsoft identity platform végpont kiadhatnak kétféle jogkivonatokat: 1.0-s verziójú jogkivonatok és 2.0-s verziójú jogkivonatok. Ezek a jogkivonatok kapcsolatos további információkért lásd: [hozzáférési jogkivonatokat](access-tokens.md). Az elfogadott token verzió attól függ, a **támogatott fióktípusok** választotta, hogy az alkalmazás létrehozásakor:

- Ha értékét **támogatott fióktípusok** van **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)** , az elfogadott token verzió v2.0 lesz.
- Ellenkező esetben az elfogadott jogkivonat-verzió lesz az 1.0-s verziója.

Miután létrehozta az alkalmazást, határozza meg, vagy módosítsa a elfogadott token verzióját az alábbi lépéseket:

1. Az Azure Portalon válassza ki az alkalmazást, és válassza ki a **Manifest** az alkalmazáshoz.
2. A jegyzékfájlban, keressen **"accessTokenAcceptedVersion"** . Vegye figyelembe, hogy a kulcs értéke **2**. Ez a tulajdonság megadja, hogy az Azure Active Directory (Azure AD), hogy a webes API 2.0-s verziójú jogkivonatokat fogad. Ha az érték **null**, a elfogadott token verziószáma 1.0-s verziója.
3. Ha módosította a token verzióját, válassza ki a **mentése**.

> [!NOTE]
> A webes API-t adja meg, melyik token verziója (1.0 vagy 2.0-s verzió), fogadja. Ügyfelek számára a webes API-hoz jogkivonatot kérhet a Microsoft identity platform v2.0-végpont, ha azok egy jogkivonatot, amely azt jelzi, hogy melyik verzió van fogadja el a webes API-t fog kapni.

## <a name="no-redirect-uri"></a>Nincs átirányítási URI-ja

Webes API-k nem szükséges regisztrálni az átirányítási URI-t, mert nincs bejelentkezett felhasználó interaktív módon.

## <a name="expose-an-api"></a>Tegye elérhetővé az API-k

Egy másik webes API-kat az adott érték a közzétett API-t és a közzétett hatókörök.

### <a name="resource-uri-and-scopes"></a>Erőforrás-URI és hatókörök

Hatókörök rendszerint a `resourceURI/scopeName`. A Microsoft Graph, a hatókörök van például a parancsikonok `User.Read`. Ez a karakterlánc a parancsikont `https://graph.microsoft.com/user.read`.

Alkalmazásregisztráció, során kell ezek a paraméterek megadása:

- Az erőforrás URI azonosítója. Alapértelmezés szerint az alkalmazásregisztrációs portálon azt javasolja, hogy használhatja `api://{clientId}`. Az erőforrás URI-t az egyedi, de nem emberi olvasható. Módosítsa azt, de ellenőrizze, hogy az új érték (egyedi).
- Egy vagy több *hatókörök*. (Az ügyfélalkalmazások számára, akkor fognak megjelenni *delegált engedélyek* a webes API-hoz.)
- Egy vagy több *alkalmazás-szerepkörök*. (Az ügyfélalkalmazások számára, akkor fognak megjelenni *Alkalmazásengedélyek* a webes API-hoz.)

A hatókörök is jelennek meg a beleegyezést kérő oldalon, a végfelhasználók számára az alkalmazás számára látható. Ezért meg kell adnia a megfelelő karakterláncok, amelyek ismertetik a hatókör:

- Mivel a végfelhasználó által látott.
- Ahogy a bérlői rendszergazda, jogosultságot tud biztosítani a rendszergazdai jóváhagyás.

### <a name="exposing-delegated-permissions-scopes"></a>Adatokhoz hozzáférést biztosító delegált engedélyeket (hatóköröket)

1. Válassza ki a **közzé API-k** az alkalmazás regisztrációja szakaszában.
1. Válassza a **Hatókör hozzáadása** lehetőséget.
1. Ha a rendszer kéri, fogadja el a javasolt Alkalmazásazonosító URI-ja (`api://{clientId}`) kiválasztásával **menti és folytatja azt**.
1. Adja meg ezeket a paramétereket:
      - A **hatókör neve**, használjon **access_as_user**.
      - A **ki hagyhatja jóvá**, győződjön meg arról, hogy **rendszergazdák és felhasználók** van kiválasztva.
      - A **rendszergazdai jóváhagyás megjelenítendő neve**, adja meg **felhasználói hozzáférési TodoListService**.
      - A **rendszergazdai jóváhagyás leírása**, adja meg **fér hozzá a TodoListService webes API-t egy felhasználóval**.
      - A **felhasználói jóváhagyás megjelenítendő neve**, adja meg **felhasználói hozzáférési TodoListService**.
      - A **felhasználói jóváhagyás leírása**, adja meg **fér hozzá a TodoListService webes API-t egy felhasználóval**.
      - Tartsa **állapot** beállítása **engedélyezve**.
      - Válassza ki **hatókör hozzáadása**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Ha a démon alkalmazás hívja meg a webes API

Ebben a szakaszban megismerheti fogja a védett webes API regisztrálása, így biztonságosan hívható démon alkalmazások.

- tegye elérhetővé kell *Alkalmazásengedélyek*. Csak az alkalmazás engedélyeit fogja deklarálnia, mert a démon alkalmazások nem használhatják a felhasználókkal, így delegált engedélyeket nincs értelme megtiltani.
- Bérlői rendszergazdák probléma tokenek csak egy alkalmazás engedélyeket a webes API-k eléréséhez regisztrált alkalmazások számára a webes API-hoz való lehet szükség az Azure Active Directory (Azure AD).

#### <a name="exposing-application-permissions-app-roles"></a>Illetéktelenül elért fiók(ok) Alkalmazásengedélyek (alkalmazás-szerepkörök)

Alkalmazásengedélyek elérhetővé, szüksége lesz a jegyzékfájl szerkesztése.

1. Válassza ki az alkalmazás alkalmazásregisztráció, **Manifest**.
1. A jegyzékfájl szerkesztése megkeresésével a `appRoles` beállítást, és a egy vagy több alkalmazás-szerepkörök hozzáadása. A szerepkör-definíció van megadva a következő példa JSON-kódblokkot. Hagyja a `allowedMemberTypes` beállítása `"Application"` csak. Győződjön meg arról, hogy a `id` egy egyedi GUID Azonosítót, és `displayName` és `value` nem tartalmazhat szóközt.
1. A jegyzékfájl mentése.

A következő minta bemutatja a tartalmát `appRoles`. (A `id` bármilyen egyedi GUID azonosító lehet.)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Annak biztosítása, hogy az Azure AD jogkivonatokat a webes API-t csak engedélyezett ügyfelek

A webes API ellenőrzi az alkalmazás-szerepkör. (Ez a fejlesztői módja Alkalmazásengedélyek elérhetővé.) De a webes API-hoz jogkivonatok kiállítása csak az API eléréséhez a bérlői rendszergazda által jóváhagyott alkalmazások az Azure ad-ben is beállítható. A fokozott biztonság hozzáadása:

1. Az alkalmazás **áttekintése** az alkalmazás regisztrációs lapra, jelölje be a hivatkozás alatt az alkalmazás nevével **felügyelt alkalmazás a helyi címtárban**. Előfordulhat, hogy csonkolja a cím mezőhöz. Például előfordulhat, hogy, lásd **felügyelt alkalmazás a...**

   > [!NOTE]
   >
   > Amikor kiválasztja ezt a hivatkozást, kattintva megnyithatja a **vállalati alkalmazás áttekintése** a szolgáltatásnevet, a bérlőben, amelyben létrehozta az alkalmazáshoz társított oldal. A böngésző Vissza gomb használatával az alkalmazás regisztrációs oldalra navigálhat.

1. Válassza ki a **tulajdonságok** lapját a **kezelés** a vállalati alkalmazás lapok szakaszában.
1. Ha azt szeretné, hogy engedélyezi a hozzáférést a webes API-hoz csak egyes ügyfelek az Azure AD, **kell felhasználó-hozzárendelés?** való **Igen**.

   > [!IMPORTANT]
   >
   > Ha **kell felhasználó-hozzárendelés?** való **Igen**, az Azure AD ellenőrizni fogja az ügyfelek a szerepkör-hozzárendeléseit, amikor azok a hozzáférési jogkivonatot a webes API-hoz. Ha az ügyfél nincs hozzárendelve egyetlen alkalmazás-szerepkör, az Azure AD vissza, a hiba `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Ha a **kell felhasználó-hozzárendelés?** beállítása **nem**, *Azure ad-ben nem ellenőrizze a szerepkör-hozzárendeléseit, amikor egy ügyfél a webes API-hoz egy hozzáférési jogkivonatot kér*. Bármely démon ügyfél (azaz bármely ügyfél az ügyfélhitelesítő adatok folyamata segítségével) fogja tudni hozzáférési jogkivonat beszerzése az API-hoz csak a célközönség megadásával. Bármely alkalmazás fogja tudni az API eléréséhez a hozzá tartozó engedélyek kérése nélkül. De a webes API mindig, az előző szakaszban leírtak szerint ellenőrizheti, hogy az alkalmazás rendelkezik-e a megfelelő szerepkör (amely a bérlői rendszergazda által engedélyezett). Az API-t az ellenőrzést végez ellenőrzése, hogy a hozzáférési jogkivonat rendelkezik-e a szerepkörök és a kérelmet, hogy ez a jogcím értéke helyes-e. (Ebben az esetben az értéke `access_as_application`.)

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás kód konfigurációját](scenario-protected-web-api-app-configuration.md)
