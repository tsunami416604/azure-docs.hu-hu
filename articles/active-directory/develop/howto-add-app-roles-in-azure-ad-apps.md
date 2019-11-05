---
title: Alkalmazás-Szerepkörök hozzáadása a Azure Active Directory regisztrált alkalmazásban és fogadása a jogkivonatban
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat hozzá alkalmazás-szerepköröket egy Azure Active Directory regisztrált alkalmazásban, hogyan rendelhet hozzá felhasználókat és csoportokat ezekhez a szerepkörökhöz, és hogyan fogadhatja azokat a jogkivonatban található `roles` jogcímben.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c838c6fe256e2960842c5b40f58f8a5d5b1fdf3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473779"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazáshoz és fogadása a jogkivonatban

A szerepköralapú hozzáférés-vezérlés (RBAC) egy népszerű mechanizmus az alkalmazások engedélyezésének kényszerítéséhez. A RBAC használatakor a rendszergazda engedélyeket biztosít a szerepkörökhöz, és nem az egyes felhasználókhoz vagy csoportokhoz. A rendszergazda ezután szerepköröket rendelhet a különböző felhasználókhoz és csoportokhoz annak szabályozásához, hogy ki férhet hozzá a tartalomhoz és a funkciókhoz.

Az RBAC és a szerepkör-jogcímek használatával a fejlesztők biztonságosan kikényszerítik az alkalmazásokban való engedélyezést, és csak kevés erőfeszítéssel rendelkeznek.

Egy másik módszer az Azure AD-csoportok és a csoportos jogcímek használata a [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet)-ben látható módon. Az Azure AD-csoportok és az alkalmazás-szerepkörök semmiképpen nem zárják ki egymást; a használatuk párhuzamosan is használható, ami még finomabb hozzáférés-vezérlést is biztosít.

## <a name="declare-roles-for-an-application"></a>Szerepkörök deklarálása egy alkalmazáshoz

Ezek az alkalmazási szerepkörök az alkalmazás regisztrációs jegyzékfájljának [Azure Portal](https://portal.azure.com) vannak meghatározva.  Amikor egy felhasználó bejelentkezik az alkalmazásba, az Azure AD egy `roles` jogcímet bocsát ki minden olyan szerepkörhöz, amelyet a felhasználó egyénileg adott meg a felhasználó és a csoporttagság alapján.  A felhasználók és csoportok szerepkörökhöz való hozzárendelése a portál felhasználói felületén vagy programozott módon, [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview)használatával végezhető el.

### <a name="declare-app-roles-using-azure-portal"></a>Alkalmazás szerepköreinek deklarálása Azure Portal használatával

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját, majd **váltson át a könyvtárra**.
1. Miután megnyitotta a **címtár + előfizetés** panelt, válassza ki azt a Active Directory bérlőt, ahová regisztrálni kívánja az alkalmazást, a **Kedvencek** vagy az **összes címtár** listából.
1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali navigációs menüben, majd válassza a **Azure Active Directory**lehetőséget.
1. Az **Azure Active Directory** ablaktáblán válassza a **Alkalmazásregisztrációk (örökölt)** lehetőséget az alkalmazások listájának megtekintéséhez.

     Ha nem látja a használni kívánt alkalmazást, használja a **Alkalmazásregisztrációk (örökölt)** lista tetején található különféle szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.

1. Válassza ki azt az alkalmazást, amelyben az alkalmazás-szerepköröket meg szeretné adni.
1. Az alkalmazás paneljén válassza a **jegyzékfájl**lehetőséget.
1. Szerkessze az alkalmazás-jegyzékfájlt úgy, hogy megkeresi a `appRoles`-beállítást, és hozzáadja az összes alkalmazás-szerepkört.

     > [!NOTE]
     > A jegyzékfájlban szereplő összes alkalmazás-szerepkör definíciójának eltérő érvényes GUID-azonosítóval kell rendelkeznie a `id` tulajdonsághoz. 
     > 
     > Az egyes alkalmazás-szerepkörök definíciójának `value` tulajdonságának pontosan egyeznie kell az alkalmazás kódjában használt karakterláncokkal. A `value` tulajdonság nem tartalmazhat szóközöket. Ha igen, hibaüzenetet kap a jegyzékfájl mentésekor.
     
1. Mentse a jegyzékfájlt.

### <a name="examples"></a>Példák

A következő példában a `users`hoz hozzárendelhető `appRoles` láthatók.

> [!NOTE]
>A `id` egyedi GUID-azonosítónak kell lennie.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>A `displayName` nem tartalmazhat szóközt.

Megadhatja az alkalmazáshoz tartozó szerepköröket, `users`, `applications`vagy mindkettőt. Ha a `applications`elérhetővé válik, az alkalmazás szerepkörei a **szükséges engedélyek** panelen jelennek meg alkalmazási engedélyekként. Az alábbi példa egy `Application`re irányuló alkalmazás-szerepkört mutat be.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

A definiált szerepkörök száma hatással van az alkalmazás jegyzékfájljának korlátaira. A [jegyzékfájlok korlátai](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) oldalon részletesen ismertetjük őket.

### <a name="assign-users-and-groups-to-roles"></a>Felhasználók és csoportok társítása szerepkörökhöz

Miután hozzáadta az alkalmazás-szerepköröket az alkalmazáshoz, hozzárendelheti a felhasználókat és csoportokat ezekhez a szerepkörökhöz.

1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások** lehetőséget a **Azure Active Directory** bal oldali navigációs menüjéből.
1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

     Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található különféle szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.

1. Válassza ki azt az alkalmazást, amelyben hozzá kívánja rendelni a felhasználókat vagy a biztonsági csoportokat a szerepkörökhöz.
1. Válassza ki a **felhasználók és csoportok** panelt az alkalmazás bal oldali navigációs menüjében.
1. A **felhasználók és csoportok** lista tetején válassza a **felhasználó hozzáadása** gombot a **hozzárendelés hozzáadása** ablaktábla megnyitásához.
1. Válassza ki a **felhasználók és csoportok** választót a **hozzárendelés hozzáadása** panelen.

     A felhasználók és a biztonsági csoportok listája a szövegmezővel együtt jelenik meg egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ezen a képernyőn egyszerre több felhasználót és csoportot is kijelölhet.

1. Ha elkészült a felhasználók és a csoportok kiválasztásával, kattintson a lenti **kiválasztás** gombra a következő részre való áttéréshez.
1. Válassza a **szerepkör** kiválasztása lehetőséget a **hozzárendelés hozzáadása** ablaktáblán. Megjelenik az alkalmazás jegyzékfájljában korábban megadott összes szerepkör.
1. Válasszon egy szerepkört, és kattintson a **kiválasztás** gombra.
1. A felhasználók és csoportok alkalmazásban való hozzárendelésének befejezéséhez nyomja le az alján található **hozzárendelés** gombot.
1. Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a frissített **felhasználók és csoportok** listában.

## <a name="more-information"></a>További információ

- [Engedélyezés egy webalkalmazásban az Azure AD-alkalmazás szerepköreinek &amp; szerepkör-jogcímek (minta) használatával](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Biztonsági csoportok és alkalmazás-szerepkörök használata az alkalmazásokban (videó)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, a Group jogcímek és az alkalmazás szerepköreivel](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory alkalmazás jegyzékfájlja](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [HRE hozzáférési jogkivonatok](access-tokens.md)
- [HRE `id_tokens`](id-tokens.md)
