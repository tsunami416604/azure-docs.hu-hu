---
title: Adjon hozzá egy webalkalmazás – Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egy webalkalmazás, az Active Directory B2C-bérlő.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757653"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>A webes API-alkalmazás az Azure Active Directory B2C-bérlő hozzáadása

Webes API-erőforrásokhoz regisztrálni kell a bérlőn belüli előtt fogadja el, és védett erőforrás-kérelmek megválaszolásához ügyfélalkalmazások, amelyek egy hozzáférési jogkivonatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy nevet az alkalmazásnak. Ha például *webapi1*.
6. A **közé tartozik a webalkalmazás vagy webes API** és **implicit engedélyezési folyamat engedélyezése**válassza **Igen**.
7. A **válasz URL-cím**, adja meg a végpont, ahol az Azure AD B2C-t adja vissza az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:44332`.
8. A **Alkalmazásazonosító URI-t**, adja meg a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

Hatókört biztosít a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

1. Válassza ki **alkalmazások**, majd válassza ki *webapi1*.
2. Válassza ki **közzétett hatókörök**.
3. A **hatókör**, adja meg `Hello.Read`, és adja meg a leírást `Read access to hello`.
4. A **hatókör**, adja meg `Hello.Write`, és adja meg a leírást `Write access to hello`.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök ügyfél megadására is használható alkalmazás számára a webes API-t.

## <a name="grant-permissions"></a>Engedélyek megadása

Egy védett webes API hívása egy alkalmazásból, az alkalmazásengedélyeket az API-nak kell. Az előfeltételnek számító oktatóanyagot hozott létre egy webalkalmazást az Azure AD B2C nevű *webapp1*. Ez az alkalmazás használhatja a webes API meghívásához.

1. Válassza ki **alkalmazások**, és válassza ki a webes alkalmazást.
2. Válassza ki **API-hozzáférés**, majd válassza ki **Hozzáadás**.
3. Az a **API kiválasztása** legördülő menüben válasszon ki *webapi1*.
4. Az a **hatókörök kiválasztása** legördülő menüben válasszon ki a **Hello.Read** és **Hello.Write** , amelyet korábban megadott hatókörök.
5. Kattintson az **OK** gombra.

Az alkalmazás regisztrálva van a védett webes API meghívásához. Egy felhasználó hitelesíti magát, az Azure AD B2C használni az alkalmazást. Az alkalmazás Azure AD B2C-vel hozzáférni a védett webes API-engedélyek kapja.