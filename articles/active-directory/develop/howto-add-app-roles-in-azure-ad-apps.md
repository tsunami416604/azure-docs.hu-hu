---
title: Alkalmazás-Szerepkörök hozzáadása és jogkivonatból való beszerzése | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat hozzá alkalmazás-szerepköröket egy Azure Active Directory regisztrált alkalmazáshoz, hogyan rendelhet hozzá felhasználókat és csoportokat ezekhez a szerepkörökhöz, és hogyan fogadhatja azokat a tokenben található "roles" jogcímben.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706013"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazáshoz és fogadása a jogkivonatban

A szerepköralapú hozzáférés-vezérlés (RBAC) egy népszerű mechanizmus az alkalmazások engedélyezésének kényszerítéséhez. A RBAC használatakor a rendszergazda engedélyeket biztosít a szerepkörökhöz, és nem az egyes felhasználókhoz vagy csoportokhoz. A rendszergazda ezután szerepköröket rendelhet a különböző felhasználókhoz és csoportokhoz annak szabályozásához, hogy ki férhet hozzá a tartalomhoz és a funkciókhoz.

Az RBAC és a szerepkör-jogcímek használatával a fejlesztők biztonságosan kényszerítik ki az alkalmazásokban való engedélyezést, és kevesebb erőfeszítéssel használják a részüket.

Egy másik módszer az Azure AD-csoportok és a csoportos jogcímek használata, ahogyan azt a GitHubon a [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) code minta mutatja. Az Azure AD-csoportok és az alkalmazás-szerepkörök nem zárják ki egymást. a használatuk párhuzamosan is használható, ami még finomabb hozzáférés-vezérlést is biztosít.

## <a name="declare-roles-for-an-application"></a>Szerepkörök deklarálása egy alkalmazáshoz

Az alkalmazás szerepkörei a [Azure Portalben](https://portal.azure.com)vannak meghatározva.  Amikor egy felhasználó bejelentkezik az alkalmazásba, az Azure AD egy `roles` jogcímet bocsát ki minden olyan szerepkörhöz, amelyet a felhasználó egyénileg adott meg a felhasználó és a csoporttagság alapján.  A felhasználók és csoportok szerepkörökhöz való hozzárendelése a portál felhasználói felületén vagy programozott módon, [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview)használatával végezhető el.

Alkalmazás-szerepkör létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a Azure Active Directory bérlőt, amely az alkalmazás regisztrációját tartalmazza, amelyhez hozzá kívánja adni az alkalmazás-szerepkört.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki azt az alkalmazást, amelyben meg szeretné adni az alkalmazás-szerepköröket.
1. **Alkalmazás-szerepkörök kiválasztása | Tekintse meg az előnézetet**, majd válassza az **alkalmazás szerepkör létrehozása**lehetőséget.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Az alkalmazás regisztrációjának alkalmazás-szerepkörök panelje a Azure Portal":::
1. Az **alkalmazás-szerepkör létrehozása** panelen adja meg a szerepkör beállításait. A képet követő táblázat ismerteti az egyes beállításokat és azok paramétereit.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Az alkalmazás-regisztráció alkalmazásbeli szerepkörei a Azure Portal helyi paneljén hozhatók létre.":::

    | Mező | Leírás | Példa |
    |-------|-------------|---------|
    | **Megjelenített név** | A rendszergazdai jogosultságok és az alkalmazás-hozzárendelések tapasztalataiban megjelenő alkalmazás-szerepkör megjelenített neve. Ez az érték szóközöket is tartalmazhat.  | `Survey Writer` |
    | **Engedélyezett tagok Típusai** | Meghatározza, hogy az alkalmazás-szerepkör hozzárendelhető-e felhasználókhoz, alkalmazásokhoz vagy mindkettőhöz. | `Users/Groups` |
    | **Érték** | Annak a szerepkör-jogcímnek az értékét adja meg, amelyet az alkalmazásnak el kell várnia a jogkivonatban. Az értéknek pontosan egyeznie kell az alkalmazás kódjában hivatkozott karakterlánccal. Az érték nem tartalmazhat szóközöket. | `Survey.Create` |
    | **Leírás** | Az alkalmazás szerepkör részletesebb leírása, amely a rendszergazdai alkalmazások hozzárendelésekor és a beleegyező felhasználói élményekben jelenik meg. | `Writers can create surveys.` |
    | **Engedélyezi ezt az alkalmazási szerepkört?** | Azt jelzi, hogy engedélyezve van-e az alkalmazás szerepköre. Egy alkalmazás-szerepkör törléséhez törölje a jelölőnégyzet jelölését, majd alkalmazza a módosítást a törlési művelet megkísérlése előtt. | *Jelölje be* |

1. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

> [!NOTE]
> A hozzáadott szerepkörök száma az alkalmazási jegyzékhez definiált korlátok irányába mutat. További információ ezekről a korlátokról: [Azure Active Directory alkalmazás jegyzékfájljának](reference-app-manifest.md) [jegyzéke – a jegyzékfájl korlátai](./reference-app-manifest.md#manifest-limits) szakasza.

## <a name="assign-users-and-groups-to-roles"></a>Felhasználók és csoportok társítása szerepkörökhöz

Miután hozzáadta az alkalmazás-szerepköröket az alkalmazáshoz, hozzárendelhet felhasználókat és csoportokat a szerepkörökhöz.

1. A Azure Portal **Azure Active Directory** a bal oldali navigációs menüben válassza a **vállalati alkalmazások** lehetőséget.
1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

     Ha az alkalmazás nem jelenik meg a listában, használja a **minden alkalmazás** lista tetején található szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.

1. Válassza ki azt az alkalmazást, amelyben hozzá kívánja rendelni a felhasználókat vagy a biztonsági csoportokat a szerepkörökhöz.
1. A **Kezelés** menüpontban válassza a **Felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** elemet a **hozzárendelés hozzáadása** ablaktábla megnyitásához.
1. Válassza ki a **felhasználók és csoportok** választót a **hozzárendelés hozzáadása** panelen.

     Megjelenik a felhasználók és a biztonsági csoportok listája. Kereshet egy adott felhasználót vagy csoportot, valamint kijelölhet több felhasználót és csoportot is, amelyek megjelennek a listában.

1. A felhasználók és csoportok kiválasztása után a folytatáshoz kattintson a **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** panelen válassza a **szerepkör kiválasztása** lehetőséget. Megjelenik az alkalmazáshoz definiált összes szerepkör.
1. Válasszon egy szerepkört, és kattintson a **kiválasztás** gombra.
1. Kattintson a **hozzárendelés** gombra a felhasználók és csoportok alkalmazáshoz való hozzárendelésének befejezéséhez.
1. Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a **felhasználók és csoportok** listában.

## <a name="receive-roles-in-tokens"></a>Szerepkörök fogadása tokenekben

Ha a különböző alkalmazás-szerepkörökhöz hozzárendelt felhasználók bejelentkeznek az alkalmazásba, a jogkivonatok a jogcímek számára a hozzárendelt szerepkörökkel rendelkeznek `roles` .

## <a name="web-api-application-permissions"></a>Webes API-alkalmazás engedélyei

Megadhatja azokat az alkalmazási szerepköröket, amelyek a **felhasználókat/csoportokat**, **alkalmazásokat**vagy **mindkettőt**célozzák meg. Ha kijelöli az **alkalmazásokat** vagy **mindkettőt**, az alkalmazási szerepkör az ügyfélalkalmazás **API-engedélyeiben**alkalmazási engedélyként jelenik meg.

Az alkalmazás engedélyének kiválasztásához az ügyfélalkalmazás regisztrálását követően, miután definiálta **az alkalmazásokra** vagy **mindkettőre**vonatkozó szerepkört:

1. A Azure Portal **Azure Active Directory** válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki az ügyfélalkalmazás regisztrációját.
1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. Válassza az **engedélyek hozzáadása**  >  **saját API**-k lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez hozzáadta az alkalmazás szerepkört, majd válassza az **alkalmazás engedélyei**lehetőséget.

## <a name="next-steps"></a>További lépések

További információ az alkalmazás szerepköreiről a következő erőforrásokkal:

- Mintakód: [hitelesítés hozzáadása az alkalmazás szerepköreivel & szerepkörök jogcímei jogcímek ASP.net Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (GitHub)
- Videó: [Engedélyezés alkalmazása az alkalmazásokban a Microsoft Identity platformmal ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Azure Active Directory-alkalmazás jegyzékfájlja](./reference-app-manifest.md)
- [Azure AD hozzáférési jogkivonatok](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
