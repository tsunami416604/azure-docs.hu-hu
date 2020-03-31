---
title: Alkalmazásszerepkörök hozzáadása és lekérés ük tokenből | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat hozzá alkalmazásszerepköröket az Azure Active Directoryban regisztrált alkalmazásokban, hogyan rendelhet felhasználókat és csoportokat ezekhez a szerepkörökhöz, és hogyan kaphatja meg őket a `roles` jogkivonatban.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3a911db36fd03ebcb5e0fc53d4d7f36d68648249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399087"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Útmutató: Alkalmazásszerepkörök hozzáadása az alkalmazásban, és a jogkivonatban való fogadásuk

A szerepköralapú hozzáférés-vezérlés (RBAC) egy népszerű mechanizmus az alkalmazások ban az engedélyezés érvényesítésére. Az RBAC használatakor a rendszergazda engedélyeket ad a szerepköröknek, és nem az egyes felhasználóknak vagy csoportoknak. A rendszergazda ezután szerepköröket rendelhet különböző felhasználókhoz és csoportokhoz annak szabályozásához, hogy ki milyen tartalmakhoz és funkciókhoz férhozzá.

Az RBAC alkalmazásszerepkörökkel és szerepkörjogcímekkel való használatával a fejlesztők biztonságosan kényszeríthetik az engedélyezést az alkalmazásaikban, kevés erőfeszítéssel a részükről.

Egy másik megközelítés az Azure AD-csoportok és csoportjogcímek használata, ahogy az a [WebApp-GroupClaims-DotNet-ben látható.](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) Az Azure AD-csoportok és alkalmazási szerepkörök egyáltalán nem zárják ki egymást; ezek lehet használni párhuzamosan, hogy még finomabb szemcsés hozzáférés-szabályozás.

## <a name="declare-roles-for-an-application"></a>Szerepkörök deklarálása egy alkalmazáshoz

Ezek az alkalmazásszerepkörök az [Azure Portalon](https://portal.azure.com) vannak definiálva az alkalmazás regisztrációs jegyzékében.  Amikor egy felhasználó bejelentkezik az alkalmazásba, `roles` az Azure AD jogcímet ad ki minden egyes szerepkörhöz, amelyet a felhasználó egyénileg kapott a felhasználónak és a csoporttagságból.  A felhasználók és csoportok szerepkörökhöz való hozzárendelése a portál felhasználói felületén keresztül, vagy programozott módon a [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview)segítségével végezhető el.

### <a name="declare-app-roles-using-azure-portal"></a>Alkalmazásszerepkörök deklarálása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A portál eszköztárán válassza a **Könyvtár + Előfizetés** ikont.
1. A **Kedvencek** vagy az **Összes könyvtár** listában válassza ki azt az Active Directory-bérlőt, ahol regisztrálni szeretné az alkalmazást.
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. Az **Azure Active Directory** ablaktáblában válassza **az alkalmazásregisztrációk** az összes alkalmazás listájának megtekintéséhez.
1. Válassza ki azt az alkalmazást, amelyben alkalmazásszerepköröket szeretne definiálni. Ezután válassza **a Jegyzékfájl**lehetőséget.
1. Az alkalmazásjegyzék szerkesztéséhez tegye `appRoles` a beállítást, és adja hozzá az összes alkalmazásszerepkört.

     > [!NOTE]
     > A jegyzékfájlban szereplő minden egyes alkalmazásszerepkör-definíciónak más érvényes `id` GUID azonosítóval kell rendelkeznie a tulajdonság jegyzékfájljának környezetében.
     >
     > Az `value` egyes alkalmazásszerepkör-definíciók tulajdonságának pontosan meg kell egyeznie az alkalmazás ban használt karakterláncokkal. A `value` tulajdonság nem tartalmazhat szóközt. Ha igen, hibaüzenetet kap, amikor menti a jegyzékfájlt.

1. Mentse az utaslistát.

### <a name="examples"></a>Példák

A következő példa `appRoles` a hoz `users`rendelhető példát mutatja be.

> [!NOTE]
>A `id` kell egy egyedi GUID.

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

Megadhatja a célként `users` `applications`megcélozandó alkalmazásszerepköröket, vagy mindkettőt. Ha elérhető `applications`a, az alkalmazásszerepkörök alkalmazásengedélyekként jelennek meg a **Szükséges engedélyek** panelen. A következő példa egy alkalmazásszerepkört mutat be, amely a `Application`számára irányul.

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

A definiált szerepkörök száma hatással van az alkalmazásjegyzék korlátaira. Ezeket részletesen megvitatták a [jegyzékkorlátok](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) oldalon.

### <a name="assign-users-and-groups-to-roles"></a>Felhasználók és csoportok hozzárendelése szerepkörökhöz

Miután hozzáadott alkalmazásszerepköröket az alkalmazásban, felhasználókat és csoportokat rendelhet ezekhez a szerepkörökhöz.

1. Az **Azure Active Directory** ablaktáblán válassza **a vállalati alkalmazások** az Azure Active **Directory** bal oldali navigációs menüben.
1. Válassza **a Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.

     Ha nem látja a kívánt alkalmazást, akkor a **Minden alkalmazás** lista tetején található különböző szűrőkkel korlátozhatja a listát, vagy görgessen le a listáról az alkalmazás megkereséséhez.

1. Jelölje ki azt az alkalmazást, amelyben felhasználókat vagy biztonsági csoportot szeretne hozzárendelni a szerepkörökhöz.
1. Válassza a **Felhasználók és csoportok** ablaktáblát az alkalmazás bal oldali navigációs menüjében.
1. A **Felhasználók és csoportok** lista tetején a Felhasználó **hozzáadása** gombra kattintva nyissa meg a **Hozzárendelés hozzáadása** ablaktáblát.
1. Válassza ki a **Felhasználók és csoportok** választót a Hozzárendelés **hozzáadása** ablaktáblán.

     A felhasználók és a biztonsági csoportok listája megjelenik egy szövegdobozzal együtt egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ez a képernyő lehetővé teszi, hogy egyszerre több felhasználót és csoportot jelöljön ki.

1. Miután végzett a felhasználók és csoportok kijelölése, nyomja meg a **Kijelölés** gombot az alsó lépés a következő részre.
1. Válassza a **Szerepkör kiválasztása** választógombot a **Hozzárendelés hozzáadása** ablaktáblán. Az alkalmazásjegyzékkorábbi, korábban deklarált szerepkörök megjelennek.
1. Válasszon ki egy szerepkört, és nyomja meg a **Kijelölés** gombot.
1. Az alsó **rész Hozzárendelés** gombjával befejezve a felhasználók és csoportok hozzárendelését az alkalmazáshoz.
1. Ellenőrizze, hogy a hozzáadott felhasználók és csoportok megjelennek-e a frissített **Felhasználók és csoportok** listában.

## <a name="more-information"></a>További információ

- [Engedélyezés hozzáadása alkalmazásszerepkörök kel & szerepkörjogcímek egy ASP.NET Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Biztonsági csoportok és alkalmazásszerepkörök használata az alkalmazásokban (videó)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, most antól csoportjogcímekkel és alkalmazásszerepkörökkel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Az Azure Active Directory alkalmazás jegyzékfájlja](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD Access-tokenek](access-tokens.md)
- [AAD között`id_tokens`](id-tokens.md)
