---
title: Védett webes API-alkalmazások regisztrációja | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t és az alkalmazás regisztrálásához szükséges információkat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 997dd98d35b74effe5d195f9a781fa0935286ee9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537219"
---
# <a name="protected-web-api-app-registration"></a>Védett webes API: Alkalmazásregisztráció

Ez a cikk ismerteti a védett webes API-regisztráció jának sajátosságait.

Az alkalmazások regisztrálásának gyakori lépéseit a [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platformmal című témakörben tés) című témakörben található.](quickstart-register-app.md)

## <a name="accepted-token-version"></a>Elfogadott tokenverzió

A Microsoft identity platform végpont javunkra 1.0-s jogkivonatokat és 2.0-s jogkivonatokat adhat ki. Ezekről a jogkivonatokról további információt az [Access-jogkivonatok](access-tokens.md)című témakörben talál.

Az elfogadott tokenverzió az alkalmazás létrehozásakor kiválasztott **támogatott fióktípusok** értékét.

- Ha a **Támogatott fióktípusok** értéke **Bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com) lévő fiókok,** az elfogadott tokenverzió a 2.0-s verzió.
- Ellenkező esetben az elfogadott token verzió az 1.0-s verzió.

Az alkalmazás létrehozása után az alábbi lépésekkel meghatározhatja vagy módosíthatja az elfogadott jogkivonat-verziót:

1. Az Azure Portalon válassza ki az alkalmazást, majd válassza **a Manifest**lehetőséget.
1. Keresse meg az **accessTokenAcceptedVersion tulajdonságot** a jegyzékfájlban. A tulajdonság alapértelmezett értéke 2.
1. Az érték az Azure Active Directory (Azure AD) számára adja meg, hogy a webes API melyik jogkivonat-verziót fogadja el.
    - Ha az érték 2, a webes API elfogadja a 2.0-s jogkivonatokat.
    - Ha az érték **null,** a webes API elfogadja a v1.0-s tokeneket.
1. Ha módosította a tokenverziót, válassza a **Mentés gombot.**

> [!NOTE]
> A webes API határozza meg, hogy melyik jogkivonat-verziót fogadja el. Amikor egy ügyfél egy jogkivonatot kér a webes API-hoz a Microsoft identity platform (v2.0) végpontról, az ügyfél kap egy jogkivonatot, amely jelzi, hogy a webes API melyik jogkivonat-verziót fogadja el.

## <a name="no-redirect-uri"></a>Nincs átirányításURI

A webes API-knak nem kell átirányítási URI-t regisztrálniuk, mert egyetlen felhasználó sincs interaktívan bejelentkezve.

## <a name="exposed-api"></a>Elérhető API

A webes API-kra jellemző egyéb beállítások a kitett API és a kitett hatókörök.

### <a name="application-id-uri-and-scopes"></a>Alkalmazásazonosító URI-azonosítója és hatókörei

A hatókörök általában `resourceURI/scopeName`a formát kapják. A Microsoft Graph esetében a hatókörök parancsikonokkal rendelkeznek. Például `User.Read` egy parancsikon `https://graph.microsoft.com/user.read`a hoz.

Az alkalmazásregisztráció során meg kell határoznia a következő paramétereket:

- Az erőforrás URI-ja
- Egy vagy több hatókör
- Egy vagy több alkalmazásszerepkör

Alapértelmezés szerint az alkalmazásregisztrációs portál azt javasolja, `api://{clientId}`hogy használja az erőforrás URI. Ez az URI egyedi, de nem olvasható emberi. Ha módosítja az URI-t, győződjön meg arról, hogy az új érték egyedi.

Az ügyfélalkalmazások számára a hatókörök *delegált engedélyekként* jelennek meg, az alkalmazásszerepkörök pedig *alkalmazásengedélyekként* jelennek meg a webes API-hoz.

A hatókörök az alkalmazás felhasználóinak bemutatott jóváhagyási ablakban is megjelennek. Tehát meg kell adnia a megfelelő karakterláncokat, amelyek leírják a hatókört:

- Ahogy a felhasználó látja.
- Abérlői rendszergazda szerint, aki rendszergazdai jóváhagyást adhat.

### <a name="exposing-delegated-permissions-scopes"></a>Delegált engedélyek (hatókörök) felfedése

1. Válassza **az API felfedése** lehetőséget az alkalmazás regisztrációjában.
1. Válassza a **Hatókör hozzáadása** lehetőséget.
1. Ha a rendszer kéri, fogadja el`api://{clientId}`a javasolt alkalmazásazonosító URI-t ( ) a **Mentés és folytatás**lehetőség kiválasztásával.
1. Adja meg a következő értékeket:
    - Válassza **a Hatókör neve lehetőséget,** és írja be **access_as_user.**
    - Válassza a **Ki járulhat hozzá,** és győződjön meg arról, **hogy a rendszergazdák és a felhasználók** ki vannak jelölve.
    - Válassza a **Rendszergazda hozzájárulása megjelenítendő nevet,** és adja meg **az Access TodoListService felhasználói nevet.**
    - Válassza a **Rendszergazda hozzájárulási leírását,** és adja meg **az Accesses service webes API-t felhasználóként.**
    - Válassza a **Felhasználói hozzájárulás megjelenítendő nevét,** és írja be **az Access TodoListService szolgáltatást felhasználóként.**
    - Válassza a **Felhasználó hozzájárulásának leírását,** és adja meg **az Accesses service webes API-t felhasználóként.**
    - Tartsa az **Állapot** értéket Engedélyezve értékre **állítva.**
 1. Válassza **a Hatókör hozzáadása**lehetőséget.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Ha a webes API-t démonalkalmazás nevezi meg

Ebben a szakaszban megtudhatja, hogyan regisztrálhatja a védett webes API-t, hogy a démonalkalmazások biztonságosan meghívhatják.

- Csak *az alkalmazásengedélyeket* deklarálja és teszi elérhetővé, mert a démonalkalmazások nem kommunikálnak a felhasználókkal. A delegált engedélyeknek nem lenne értelme.
- A bérlői rendszergazdák megkövetelhetik az Azure AD-től, hogy csak olyan alkalmazásoknak adjon ki webes API-jogkivonatokat, amelyek regisztráltak az API egyik alkalmazásengedélyének eléréséhez.

#### <a name="exposing-application-permissions-app-roles"></a>Alkalmazásengedélyek (alkalmazásszerepkörök) felfedése

Az alkalmazásengedélyek elérhetővé, a jegyzékfájl szerkesztéséhez.

1. A jelentkezési lapon válassza a **Manifest**lehetőséget.
1. A jegyzékfájl szerkesztéséhez `appRoles` keresse meg a beállítást, és adjon hozzá alkalmazásszerepköröket. A szerepkör-definíciók a következő minta JSON blokkban találhatók.
1. Hagyja `allowedMemberTypes` csak `"Application"` beállítottra.
1. Győződjön `id` meg róla, hogy egy egyedi GUID.
1. Győződjön `displayName` `value` meg arról, hogy nem tartalmaz szóközöket, és nem tartalmaz szóközöket.
1. Mentse az utaslistát.

A következő minta `appRoles`a tartalmát mutatja, ahol az értéke bármilyen egyedi GUID `id` lehet.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Annak biztosítása, hogy az Azure AD csak az engedélyezett ügyfelek számára adja ki a webes API-hoz a jogkivonatokat

A webes API megkeresi az alkalmazásszerepkört. Ez a szerepkör egy szoftverfejlesztő módja az alkalmazásengedélyek felfedésének. Az Azure AD-t is beállíthatja, hogy csak olyan alkalmazásoknak adjon ki API-jogkivonatokat, amelyeket a bérlői rendszergazda jóváhagy az API-hozzáféréshez.

A fokozott biztonság hozzáadása:

1. Nyissa meg az alkalmazás **áttekintése** lapot az alkalmazás regisztrációjához.
1. A **Helyi címtárban a Felügyelt alkalmazás csoportban**válassza ki az alkalmazás nevét mutató hivatkozást. Lehet, hogy a kijelölés címkéje csonkolva van. Előfordulhat például, hogy **a Felügyelt alkalmazás a ...**

   > [!NOTE]
   >
   > Ha ezt a hivatkozást választja, a **Vállalati alkalmazás áttekintése** lapot. Ez a lap az alkalmazás egyszerű szolgáltatásához van társítva abban a bérlőben, ahol létrehozta. Az alkalmazás regisztrációs oldalát a böngésző vissza gombjával oldhatja meg.

1. Válassza a **Tulajdonságok** lapot a Vállalati alkalmazáslapok **Kezelés** szakaszában.
1. Ha azt szeretné, hogy az Azure AD csak bizonyos ügyfelektől engedélyezze a webes API-hoz való hozzáférést, állítsa a **szükséges felhasználói hozzárendelést igen** beállításra. **Yes**

   > [!IMPORTANT]
   >
   > Ha a **felhasználó hozzárendelésszükséges?** **Igen,** az Azure AD ellenőrzi az ügyfél alkalmazásszerepkör-hozzárendelések, amikor egy webes API-hozzáférési jogkivonatot kér. Ha az ügyfél nincs hozzárendelve egyetlen alkalmazásszerepkörhöz sem, az Azure AD a következő hibaüzenetet adja \<vissza: "invalid_client: AADSTS501051: Az alkalmazásalkalmazás neve\> nincs hozzárendelve a \<webes API\>szerepköréhez".
   >
   > Ha **megtartja a felhasználó-hozzárendelés szükséges?** értéke **Nem,** az Azure AD nem ellenőrzi az alkalmazás szerepkör-hozzárendelések, amikor egy ügyfél hozzáférési jogkivonatot kér a webes API-t. Bármely démon-ügyfél, azaz bármely ügyfél az ügyfél hitelesítő adatok folyamatát, kaphat egy hozzáférési jogkivonatot az API-t csak a közönség megadásával. Bármely alkalmazás anélkül férhet hozzá az API-hoz, hogy engedélyt kellene kérnie.
   >
   > De az előző szakaszban kifejtette, a webes API-t mindig ellenőrizheti, hogy az alkalmazás rendelkezik-e a megfelelő szerepkör, amely a bérlői rendszergazda által engedélyezett. Az API ezt az ellenőrzést úgy hajtja végre, hogy ellenőrzi, hogy a hozzáférési jogkivonat rendelkezik-e szerepkörjogcímekkel, és hogy a jogcím értéke helyes-e. Az előző JSON-mintában `access_as_application`az érték .

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazáskód-konfiguráció](scenario-protected-web-api-app-configuration.md)
