---
title: Alkalmazás-Szerepkörök hozzáadása és jogkivonatból való beszerzése | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat hozzá alkalmazás-szerepköröket egy Azure Active Directory regisztrált alkalmazásban, hogyan rendelhet hozzá felhasználókat és csoportokat ezekhez a szerepkörökhöz, és hogyan fogadhatja azokat a `roles` jogkivonatban lévő jogcímben.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: da697ad6fac5c75faacbfb20b768a4ec96528881
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041459"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazáshoz és fogadása a jogkivonatban

A szerepköralapú hozzáférés-vezérlés (RBAC) egy népszerű mechanizmus az alkalmazások engedélyezésének kényszerítéséhez. A RBAC használatakor a rendszergazda engedélyeket biztosít a szerepkörökhöz, és nem az egyes felhasználókhoz vagy csoportokhoz. A rendszergazda ezután szerepköröket rendelhet a különböző felhasználókhoz és csoportokhoz annak szabályozásához, hogy ki férhet hozzá a tartalomhoz és a funkciókhoz.

Az RBAC és a szerepkör-jogcímek használatával a fejlesztők biztonságosan kikényszerítik az alkalmazásokban való engedélyezést, és csak kevés erőfeszítéssel rendelkeznek.

Egy másik módszer az Azure AD-csoportok és a csoportos jogcímek használata a [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet)-ben látható módon. Az Azure AD-csoportok és az alkalmazás-szerepkörök semmiképpen nem zárják ki egymást; a használatuk párhuzamosan is használható, ami még finomabb hozzáférés-vezérlést is biztosít.

## <a name="declare-roles-for-an-application"></a>Szerepkörök deklarálása egy alkalmazáshoz

Ezek az alkalmazási szerepkörök az alkalmazás regisztrációs jegyzékfájljának [Azure Portal](https://portal.azure.com) vannak meghatározva.  Amikor egy felhasználó bejelentkezik az alkalmazásba, az Azure AD egy `roles` jogcímet bocsát ki minden olyan szerepkörhöz, amelyet a felhasználó egyénileg adott meg a felhasználó és a csoporttagság alapján.  A felhasználók és csoportok szerepkörökhöz való hozzárendelése a portál felhasználói felületén vagy programozott módon, [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview)használatával végezhető el.

### <a name="declare-app-roles-using-azure-portal"></a>Alkalmazás szerepköreinek deklarálása Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán.
1. A **Kedvencek** vagy az **összes könyvtár** listában válassza ki azt a Active Directory bérlőt, ahová regisztrálni kívánja az alkalmazást.
1. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.
1. Az **Azure Active Directory** ablaktáblán válassza a **Alkalmazásregisztrációk** lehetőséget az alkalmazások listájának megtekintéséhez.
1. Válassza ki azt az alkalmazást, amelyben az alkalmazás-szerepköröket meg szeretné adni. Ezután válassza a **jegyzékfájl**lehetőséget.
1. Szerkessze az alkalmazás-jegyzékfájlt úgy, hogy megkeresi a `appRoles` beállítást, és hozzáadja az összes alkalmazás-szerepkört.

     > [!NOTE]
     > A jegyzékfájlban szereplő összes alkalmazás-szerepkör definíciójának eltérő érvényes GUID-azonosítóval kell rendelkeznie a tulajdonság jegyzékfájljának kontextusában `id` .
     >
     > Az `value` egyes alkalmazás-szerepkör-definíciók tulajdonságának pontosan egyeznie kell az alkalmazás kódjában használt karakterláncokkal. A `value` tulajdonság nem tartalmazhat szóközöket. Ha igen, hibaüzenetet kap a jegyzékfájl mentésekor.

1. Mentse a jegyzékfájlt.

### <a name="examples"></a>Példák

Az alábbi példa azt szemlélteti, hogy milyen műveleteket `appRoles` lehet hozzárendelni `users` .

> [!NOTE]
>A `id` kötelezően egyedi GUID-azonosítónak kell lennie.

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
>A tartalmazhat `displayName` szóközt.

Megadhatja az alkalmazáshoz tartozó szerepköröket, `users` `applications` vagy mindkettőt. Ha elérhető, az alkalmazás `applications` szerepkörei a **kezelés** szakaszban > API-engedélyek területen jelennek meg, **> > engedély hozzáadása a saját API-kkal > válasszon API > alkalmazási engedélyeket**. Az alábbi példa egy alkalmazás-szerepkört mutat be a felé `Application` .

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

### <a name="receive-roles-in-tokens"></a>Szerepkörök fogadása tokenekben

Ha a különböző alkalmazás-szerepkörökhöz hozzárendelt felhasználók bejelentkeznek az alkalmazásba, a jogkivonatok a jogcímek számára a hozzárendelt szerepkörökkel rendelkeznek `roles` .

## <a name="more-information"></a>További információ

- [Engedélyezés hozzáadása az alkalmazás szerepköreinek & a szerepkörök jogcímeinek használata egy ASP.NET Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Engedélyezés alkalmazása az alkalmazásokban a Microsoft Identity platformmal (videó)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, a Group jogcímek és az alkalmazás szerepköreivel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-alkalmazás jegyzékfájlja](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [HRE hozzáférési jogkivonatok](access-tokens.md)
- [HRE`id_tokens`](id-tokens.md)
