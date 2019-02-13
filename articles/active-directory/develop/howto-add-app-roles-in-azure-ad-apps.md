---
title: Alkalmazás-szerepkörök hozzáadása az Azure Active Directoryban regisztrált alkalmazásban, és fogadásukra a jogkivonat
description: Alkalmazás-szerepkörök felvétele az Azure Active Directoryban regisztrált egy alkalmazást, felhasználók és csoportok hozzárendelése ezeket a szerepköröket és fogadhatók az a `roles` jogcím a jogkivonatban.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: mtillman
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
ms.openlocfilehash: 427e293c28f634df9f66a7210d79e0df0d4d063c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164039"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Útmutató: Alkalmazás-szerepkörök hozzáadása az alkalmazásban, és fogadhatók a jogkivonat

Szerepköralapú hozzáférés-vezérlés (RBAC) az olyan népszerű mechanizmusa kikényszeríteni az alkalmazásokban. RBAC használata esetén a rendszergazda engedélyt ad-szerepkörökhöz, és nem egyéni felhasználókhoz vagy csoportokhoz. A rendszergazda ezután szerepköröket rendelhetnek más felhasználók és csoportok szabályozza, ki férhet hozzá, milyen tartalmakat és funkciókat.

RBAC használata az alkalmazás-szerepkörök és szerepkör jogcím, fejlesztők is biztonságosan engedélyezésének kikényszerítéséhez az eszközeiken, minimális erőfeszítéssel alkalmazásaikban.

Másik megoldás, az Azure AD-csoportokat és csoportjogcímek, ahogyan az [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). Az Azure AD-csoportokat és alkalmazás-szerepkörök, semmiképpen sem egymást kölcsönösen kizáró; Ezek segítségével párhuzamosan is kifinomultabb részletes hozzáférés-nyújtási.

## <a name="declare-roles-for-an-application"></a>Deklarálja a szerepkörök az alkalmazáshoz

E alkalmazás-szerepkörök vannak meghatározva a [az Azure portal](https://portal.azure.com) regisztrációs alkalmazásjegyzékben.  Amikor egy felhasználó bejelentkezik az alkalmazásba, az Azure AD bocsát ki egy `roles` jogcímet a felhasználó megkapta-e külön-külön a felhasználó és a csoporttagságuk minden szerepkör esetében.  Lehet, hogy a felhasználók és csoportok, szerepkörök hozzárendelés kész, a portál felhasználói felületének használatával vagy programozott módon a [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Deklarálja az alkalmazás-szerepkörök az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a felső menüsávon válassza ki a fiókját, majd **címtár váltása**.
1. Egyszer a **címtár és előfizetés** panel megnyílik, válassza ki, hol szeretné az alkalmazást, regisztrálni az Active Directory-bérlő a **Kedvencek** vagy **minden címtár**listája.
1. Válassza ki **minden szolgáltatás** a bal oldali navigációs válassza **Azure Active Directory**.
1. Az a **Azure Active Directory** ablaktáblán válassza előbb **alkalmazásregisztrációk** az alkalmazások listájának megtekintéséhez.

     Ha nem látja a kívánt itt megjelenni alkalmazást, a különböző szűrők használata felső részén a **alkalmazásregisztrációk** korlátozhatja a görgessen lefelé a listában keresse meg az alkalmazás vagy a listában.

1. Válassza ki az alkalmazás-szerepkörök definiálása a kívánt alkalmazásra.
1. Az alkalmazás panelen válassza ki a **Manifest**.
1. Az alkalmazásjegyzék szerkesztése megkeresésével a `appRoles` beállítást, és az alkalmazás-szerepkörök hozzáadása.

     > [!NOTE]
     > Minden egyes szerepkör-definíció a jegyzékfájlban rendelkeznie kell egy másik érvényes **Guid** az "Id" tulajdonsághoz. A `"value"` tulajdonságot az egyes szerepkörök pontosan egyeznie kell a karakterláncokat az alkalmazás a kódban használhatók.
     
1. A jegyzékfájl mentése.

### <a name="examples"></a>Példák

A következő példa bemutatja a `appRoles` , amelyeket hozzárendelhet a `users`.

> [!NOTE]
>  A `id` egyedi GUID azonosítónak kell lennie.

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

Cél meghatározhatja az alkalmazás-szerepkörök `users`, `applications`, vagy mindkettőt. Ha az elérhető `applications`, alkalmazás-szerepkörök jelennek meg Alkalmazásengedélyek, az a **szükséges engedélyek** panelen. Az alábbi példa bemutatja egy alkalmazás-szerepkör céloz egy `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "Consumer Apps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

### <a name="assign-users-and-groups-to-roles"></a>Felhasználók és csoportok hozzárendelése szerepkörökhöz

Miután az alkalmazás hozzáadott alkalmazás-szerepkörök, ezek a szerepkörök felhasználók és csoportok rendelhet.

1. Az a **Azure Active Directory** ablaktáblán válassza előbb **vállalati alkalmazások** a a **Azure Active Directory** bal oldali navigációs menü.
1. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

     Ha nem látja a kívánt itt megjelenni alkalmazást, a különböző szűrők használata felső részén a **minden alkalmazás** korlátozhatja a görgessen lefelé a listában keresse meg az alkalmazás vagy a listában.

1. Válassza ki az alkalmazást, amelyben a felhasználók vagy biztonsági csoport hozzárendelése szerepkörökhöz szeretné.
1. Válassza ki a **felhasználók és csoportok** ablaktábla bal oldali navigációs menü az alkalmazás.
1. Felső részén a **felhasználók és csoportok** listáról válassza ki a **felhasználó hozzáadása** gombra kattintva nyissa meg a **hozzárendelés hozzáadása** ablaktáblán.
1. Válassza ki a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

     Felhasználók és biztonsági csoportok listáját egy szövegmező, és a egy bizonyos felhasználó vagy csoport megkereséséhez együtt jelenik meg. Ezen a képernyőn válassza ki a több felhasználó és csoport, lépjen egy teszi lehetővé.

1. Ha elkészült a felhasználók és csoportok kiválasztása, nyomja le az ENTER a **válassza** áthelyezése a következő részére, alsó gombjára.
1. Válassza ki a **Szerepkörválasztás** a választó a **-hozzárendelés hozzáadása** ablaktáblán. A korábban az alkalmazás-jegyzékfájlban deklarált szerepkörök jelennek meg.
1. Válasszon ki egy szerepkört, és nyomja le az **kiválasztása** gombra.
1. Nyomja le az **hozzárendelése** gombra a befejezéséhez a felhasználókat és csoportokat az alkalmazás-hozzárendeléseit a képernyő aljára.
1. Győződjön meg arról, hogy a felhasználók és csoportok hozzáadott jelennek meg a frissített **felhasználók és csoportok** listája.

## <a name="more-information"></a>További információ

- [Az Azure AD alkalmazás-szerepkörök használatával webalkalmazásban engedélyezési &amp; szerepkörjogcímeken (minta)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [A biztonsági és alkalmazás-szerepkörök használatával az alkalmazások (videó)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Az Azure Active Directory-csoport jogcímek és az alkalmazás-szerepkörök](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Az Azure Active Directory-manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD-hozzáférési jogkivonatok](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
