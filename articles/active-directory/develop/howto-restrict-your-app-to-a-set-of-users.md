---
title: Az Azure AD-alkalmazás korlátozása felhasználók készletére | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan korlátozhatja az Azure AD-ban regisztrált alkalmazásaihoz való hozzáférést egy kiválasztott felhasználói csoportba.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6e20b2f8c0e12fdb23668d16e2cbd7936cc4e48c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445459"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Útmutató: az Azure AD-alkalmazás korlátozása egy Azure AD-bérlőben lévő felhasználók köré

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők.

Hasonlóképpen, a [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazások esetében az Azure ad-bérlő minden olyan felhasználója hozzáférhet az alkalmazáshoz, amikor sikeresen hitelesítik magukat a megfelelő bérlőn.

A bérlői rendszergazdák és a fejlesztők gyakran rendelkeznek olyan követelményekkel, amelyekben az alkalmazásnak egy adott felhasználói csoportra kell korlátozódnia. A fejlesztők ugyanúgy használhatják a népszerű engedélyezési mintákat, mint az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC), de ez a megközelítés jelentős mennyiségű munkát igényel a fejlesztő részéről.

A bérlői rendszergazdák és fejlesztők az Azure AD ezen beépített funkciójának használatával korlátozhatják az alkalmazásokat a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára is.

## <a name="supported-app-configurations"></a>Támogatott alkalmazások konfigurációi

Egy adott alkalmazás a következő típusú alkalmazásokkal rendelkező felhasználók vagy biztonsági csoportok meghatározott csoportjára való korlátozására használható:

- SAML-alapú hitelesítéssel összevont egyszeri bejelentkezésre konfigurált alkalmazások
- Az Azure AD előzetes hitelesítést használó alkalmazásproxy-alkalmazásai
- A közvetlenül az Azure AD alkalmazás platformján létrehozott alkalmazások, amelyek a OAuth 2.0/OpenID Connect hitelesítést használják, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba.

     > [!NOTE]
     > Ez a funkció csak a Web App/web API-hoz és a vállalati alkalmazásokhoz érhető el. A [natívként](quickstart-v1-integrate-apps-with-azure-ad.md) regisztrált alkalmazások nem korlátozhatók a bérlőben lévő felhasználók vagy biztonsági csoportok csoportjára.

## <a name="update-the-app-to-enable-user-assignment"></a>Az alkalmazás frissítése a felhasználó-hozzárendelés engedélyezéséhez

Az engedélyezett felhasználói hozzárendeléssel kétféleképpen hozhatók létre alkalmazások. Egy **globális rendszergazdai** szerepkörre van szükség, a második nem.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Vállalati alkalmazások (globális rendszergazdai szerepkört igényel)

1. Lépjen a [**Azure Portalra**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként**.
1. A felső sávon válassza ki a bejelentkezett fiókot. 
1. A **címtár**területen válassza ki azt az Azure ad-bérlőt, ahol az alkalmazás regisztrálva lesz.
1. A bal oldali navigációs sávon válassza a **Azure Active Directory**lehetőséget. Ha Azure Active Directory nem érhető el a navigációs ablaktáblán, kövesse az alábbi lépéseket:

    1. A bal oldali navigációs menü tetején válassza a **minden szolgáltatás** lehetőséget.
    1. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza ki a **Azure Active Directory** elemet az eredményből.

1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások** lehetőséget a **Azure Active Directory** bal oldali navigációs menüjéből.
1. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

     Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található különféle szűrőket a lista korlátozásához, vagy görgessen le a listából az alkalmazás megkereséséhez.

1. Válassza ki azt az alkalmazást, amelyhez felhasználót vagy biztonsági csoportot szeretne hozzárendelni a listából.
1. Az alkalmazás **Áttekintés** lapján válassza a **Tulajdonságok** lehetőséget az alkalmazás bal oldali navigációs menüjében.
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen**értékre. Ha ezt a beállítást az **Igen**értékre állítja, akkor a bérlőben lévő felhasználókat először hozzá kell rendelni ehhez az alkalmazáshoz, vagy nem lesznek képesek bejelentkezni az alkalmazásba.
1. A konfigurációs módosítás mentéséhez válassza a **Mentés** lehetőséget.

### <a name="app-registration"></a>Alkalmazásregisztráció

1. Lépjen a [**Azure Portal**](https://portal.azure.com/).
1. A felső sávon válassza ki a bejelentkezett fiókot. 
1. A **címtár**területen válassza ki azt az Azure ad-bérlőt, ahol az alkalmazás regisztrálva lesz.
1. A bal oldali navigációs sávon válassza a **Azure Active Directory**lehetőséget.
1. A **Azure Active Directory** ablaktáblán válassza az **alkalmazás-regisztrációk** lehetőséget a **Azure Active Directory** bal oldali navigációs menüjében.
1. Hozza létre vagy válassza ki a kezelni kívánt alkalmazást. Az alkalmazás regisztrációjának **tulajdonosaként** kell lennie.
1. Az alkalmazás **áttekintése** lapon kövesse a **felügyelt alkalmazás helyi címtárban** hivatkozását az oldal tetején található Essentials elem alatt. Ezzel az alkalmazás regisztrációjának _felügyelt vállalati alkalmazására_ kerül.
1. A bal oldali navigációs panelen válassza a **Tulajdonságok**lehetőséget.
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen**értékre. Ha ezt a beállítást az **Igen**értékre állítja, akkor a bérlőben lévő felhasználókat először hozzá kell rendelni ehhez az alkalmazáshoz, vagy nem lesznek képesek bejelentkezni az alkalmazásba.
1. A konfigurációs módosítás mentéséhez válassza a **Mentés** lehetőséget.

## <a name="assign-users-and-groups-to-the-app"></a>Felhasználók és csoportok társítása az alkalmazáshoz

Ha úgy konfigurálta az alkalmazást, hogy engedélyezze a felhasználó-hozzárendelést, akkor a felhasználók és csoportok az alkalmazáshoz rendelhetők.

1. Válassza ki a **felhasználók és csoportok** panelt az alkalmazás bal oldali navigációs menüjében.
1. A **felhasználók és csoportok** lista tetején válassza a **felhasználó hozzáadása** gombot a **hozzárendelés hozzáadása** ablaktábla megnyitásához.
1. Válassza ki a **felhasználók** választót a **hozzárendelés hozzáadása** panelen. 

     A felhasználók és a biztonsági csoportok listája a szövegmezővel együtt jelenik meg egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ezen a képernyőn egyszerre több felhasználót és csoportot is kijelölhet.

1. Ha elkészült a felhasználók és a csoportok kiválasztásával, kattintson a lenti **kiválasztás** gombra a következő részre való áttéréshez.
1. Választható Ha az alkalmazásban definiált alkalmazás-szerepköröket használ, a kiválasztott felhasználók és csoportok hozzárendeléséhez a **szerepkör kiválasztása** lehetőséget használhatja. 
1. A felhasználók és csoportok alkalmazásban való hozzárendelésének befejezéséhez nyomja le az alján található **hozzárendelés** gombot. 
1. Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a frissített **felhasználók és csoportok** listában.

## <a name="more-information"></a>További információ

- [Útmutató: alkalmazások szerepköreinek hozzáadása az alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Engedélyezés hozzáadása az alkalmazás szerepköreinek & a szerepkörök jogcímeinek használata egy ASP.NET Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Biztonsági csoportok és alkalmazás-szerepkörök használata az alkalmazásokban (videó)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, a Group jogcímek és az alkalmazás szerepköreivel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-alkalmazás jegyzékfájlja](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)