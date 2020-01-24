---
title: Védett webes API-alkalmazások regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t és az alkalmazás regisztrálásához szükséges információkat.
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
ms.openlocfilehash: ffa5c0ff9aac636a584743f839d780d2df2db5ef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701994"
---
# <a name="protected-web-api-app-registration"></a>Védett webes API: alkalmazás regisztrálása

Ez a cikk ismerteti az alkalmazások regisztrálásának sajátosságait egy védett webes API-hoz.

Az alkalmazások regisztrálásának általános lépéseiért lásd: gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](quickstart-register-app.md) .

## <a name="accepted-token-version"></a>Elfogadott jogkivonat-verzió

A Microsoft Identity platform végpontja két típusú tokent tud kiadni: v 1.0 tokenek és v 2.0 tokenek. További információ ezekről a jogkivonatokról: [hozzáférési tokenek](access-tokens.md). Az elfogadott jogkivonat verziója az alkalmazás létrehozásakor választott **támogatott fióktípus** függ:

- Ha a **támogatott fióktípus** a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com) lévő fiókok**, az elfogadott jogkivonat-verzió a v 2.0 lesz.
- Ellenkező esetben az elfogadott jogkivonat-verzió a v 1.0 lesz.

Az alkalmazás létrehozása után az alábbi lépéseket követve meghatározhatja vagy módosíthatja az elfogadott jogkivonat verzióját:

1. A Azure Portal válassza ki az alkalmazást, majd válassza ki az alkalmazáshoz tartozó **jegyzékfájlt** .
2. A jegyzékfájlban keressen rá a **"accessTokenAcceptedVersion"** kifejezésre. Vegye figyelembe, hogy az értéke **2**. Ez a tulajdonság azt a Azure Active Directory (Azure AD), amelyet a webes API a 2.0-s verzióban elfogad. Ha az érték **Null**, az elfogadott jogkivonat verziója a v 1.0.
3. Ha módosította a jogkivonat verzióját, válassza a **Mentés**lehetőséget.

> [!NOTE]
> A webes API meghatározza, hogy melyik jogkivonat-verziót (v 1.0 vagy v 2.0) fogadja el. Ha az ügyfelek jogkivonatot kérnek a webes API-hoz a Microsoft Identity platform (v 2.0) végponttól, akkor egy jogkivonatot kapnak, amely jelzi, hogy a webes API melyik verziót fogadja el.

## <a name="no-redirect-uri"></a>Nincs átirányítási URI

A webes API-knak nem kell regisztrálniuk egy átirányítási URI-t, mert egyetlen felhasználó sincs interaktív módon aláírva.

## <a name="expose-an-api"></a>API közzététele

A webes API-kra vonatkozó egy másik beállítás a kitett API és az elérhető hatókörök.

### <a name="resource-uri-and-scopes"></a>Erőforrás URI-ja és hatókörök

A hatókörök általában a `resourceURI/scopeName`formában vannak. Microsoft Graph esetén a hatókörök olyan parancsikonokkal rendelkeznek, mint a `User.Read`. Ez a karakterlánc a `https://graph.microsoft.com/user.read`parancsikonja.

Az alkalmazás regisztrálása során meg kell határoznia ezeket a paramétereket:

- Az erőforrás URI-ja. Alapértelmezés szerint az alkalmazás regisztrációs portálja a `api://{clientId}`használatát javasolja. Ez az erőforrás URI egyedi, de nem olvasható. Megváltoztathatja, de gondoskodhat arról, hogy az új érték egyedi legyen.
- Egy vagy több *hatókör*. (Az ügyfélalkalmazások számára *delegált engedélyként* fog megjelenni a webes API-hoz.)
- Egy vagy több *alkalmazás-szerepkör*. (Az ügyfélalkalmazások számára a webes API-hoz *alkalmazási engedélyekként* jelennek meg.)

A hatókörök az alkalmazás végfelhasználói számára bemutatott beleegyezési képernyőn is megjelennek. Ezért meg kell adnia a megfelelő karakterláncokat, amelyek leírják a hatókört:

- A végfelhasználók láthatják.
- A bérlői rendszergazda láthatja, hogy ki engedélyezheti a rendszergazdai jóváhagyást.

### <a name="exposing-delegated-permissions-scopes"></a>Delegált engedélyek elmagyarázása (hatókörök)

1. Válassza ki az **API közzététele** szakaszt az alkalmazás regisztrálásához.
1. Válassza a **Hatókör hozzáadása** lehetőséget.
1. Ha a rendszer kéri, fogadja el a javasolt alkalmazás-azonosító URI-t (`api://{clientId}`) a **Mentés és folytatás**lehetőség kiválasztásával.
1. Adja meg a következő paramétereket:
      - A **hatókör neve mezőben**használja a **access_as_user**.
      - Válassza ki a **rendszergazdák és a felhasználók** lehetőséget, hogy **ki is beleegyezik**.
      - A **rendszergazdai jogosultság megjelenítendő neve**mezőben adja meg **a hozzáférés TodoListService felhasználóként**értéket.
      - A **rendszergazdai engedély leírása**mezőben adja meg **a TodoListService webes API-hoz való hozzáférést felhasználóként**.
      - A **felhasználói beleegyező megjelenítendő név**mezőben adja meg **a hozzáférés TodoListService felhasználóként**.
      - A **felhasználói hozzáférés leírása**mezőben adja meg **a hozzáférést a TodoListService webes API-hoz felhasználóként**.
      - Állítsa az **állapotot** **engedélyezve**értékre.
      - Válassza a **hatókör hozzáadása**elemet.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Ha a webes API-t egy Daemon-alkalmazás hívja meg

Ebből a szakaszból megtudhatja, hogyan regisztrálhatja a védett webes API-t, hogy a démon-alkalmazások biztonságosan meghívják őket.

- Az *alkalmazás engedélyeinek*közzététele szükséges. Csak az alkalmazás engedélyei jelennek meg, mert a Daemon-alkalmazások nem működnek együtt a felhasználókkal, így a delegált engedélyek nem lesznek ésszerűek.
- A bérlői rendszergazdák megkövetelhetik Azure Active Directory (Azure AD) számára a webes API-hoz tartozó jogkivonatok kiküldését csak azokra az alkalmazásokra, amelyek regisztráltak a webes API-k egyik alkalmazás-engedélyeinek eléréséhez.

#### <a name="exposing-application-permissions-app-roles"></a>Alkalmazás engedélyeinek kitöltése (alkalmazás-szerepkörök)

Az alkalmazás engedélyeinek közzététele előtt szerkesztenie kell a jegyzékfájlt.

1. Az alkalmazás regisztrációjában válassza a **jegyzékfájl**lehetőséget.
1. Szerkessze a jegyzékfájlt úgy, hogy megkeresi a `appRoles` beállítást, és hozzáad egy vagy több alkalmazás-szerepkört. A szerepkör-definíciót az alábbi JSON-blokkban kell megadnia. Hagyja meg a `allowedMemberTypes` beállítani a csak `"Application"`. Győződjön meg arról, hogy a `id` egyedi GUID azonosító, és hogy `displayName` és `value` nem tartalmaz szóközt.
1. Mentse a jegyzékfájlt.

A következő minta a `appRoles`tartalmát mutatja. (A `id` bármely egyedi GUID lehet.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Annak biztosítása, hogy az Azure AD a webes API-hoz tartozó jogkivonatokat csak az ügyfelek számára engedélyezzék

A webes API ellenőrzi az alkalmazás szerepkörét. (Ez a fejlesztői módszer az alkalmazás engedélyeinek kifejtésére.) Azt is megteheti, hogy az Azure AD-t úgy is beállíthatja, hogy csak a bérlői rendszergazda által jóváhagyott alkalmazások számára állítson ki tokent a webes API-hoz. A fokozott biztonság hozzáadása:

1. Az alkalmazás-regisztráció alkalmazás- **Áttekintés** oldalán válassza ki az alkalmazás nevét a **helyi címtárban található felügyelt alkalmazás**területen. A mező címe csonkolt lehet. Előfordulhat például, hogy a **felügyelt alkalmazás a** következő helyen található:...

   > [!NOTE]
   >
   > Ha ezt a hivatkozást választja, az alkalmazáshoz tartozó egyszerű szolgáltatáshoz kapcsolódó **vállalati alkalmazások áttekintése** oldal a bérlő, ahol létrehozta. A böngésző vissza gombjának használatával visszatérhet az alkalmazás regisztrációs lapjára.

1. Válassza a **Tulajdonságok** lapot a vállalati alkalmazások lapjainak **kezelés** szakaszában.
1. Ha azt szeretné, hogy az Azure AD csak bizonyos ügyfelektől engedélyezze a webes API-hoz való hozzáférést, állítsa be a **felhasználó-hozzárendelést?** **Igen**értékre.

   > [!IMPORTANT]
   >
   > Ha a **felhasználó-hozzárendelést kötelező megadni?** **Igen**, az Azure ad megkeresi az ügyfelek alkalmazás-szerepkörének hozzárendelését, amikor hozzáférési jogkivonatot kérnek a webes API-hoz. Ha az ügyfél nincs hozzárendelve egyetlen alkalmazás-szerepkörhöz sem, akkor az Azure AD visszaküldi a hibát `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Ha megtartja a **felhasználó-hozzárendelést?** a **nem**értékre van ÁLLÍTVA, *Az Azure ad nem fogja megtekinteni az alkalmazás szerepkör-hozzárendelését, ha az ügyfél hozzáférési jogkivonatot kér a webes API*- Bármely démon-ügyfél (azaz bármely ügyfél, amely az ügyfél hitelesítő adatait használja) képes lesz hozzáférési tokent beszerezni az API-hoz, csak a célközönség megadásával. Bármely alkalmazás elérheti az API-t anélkül, hogy engedélyt kellene kérnie rá. A webes API azonban mindig az előző szakaszban leírtaknak megfelelően ellenőrizheti, hogy az alkalmazás rendelkezik-e a megfelelő szerepkörrel (amelyet a bérlői rendszergazda jogosult). Az API végrehajtja ezt az ellenőrzést annak ellenőrzésével, hogy a hozzáférési jogkivonat rendelkezik szerepkör-jogcímvel, és hogy a jogcím értéke helyes-e. (Esetünkben az érték `access_as_application`.)

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-protected-web-api-app-configuration.md)
