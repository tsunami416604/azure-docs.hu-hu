---
title: Azure Active Directory regisztrált alkalmazás felhasználóinak korlátozása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan korlátozhatja az Azure AD-ban regisztrált alkalmazásaihoz való hozzáférést egy kiválasztott felhasználói csoportba.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1cd52a62d5ab8185e18c849f6009e4632c96ede
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917624"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>Útmutató: az Azure AD-alkalmazás korlátozása felhasználói csoportra

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők.

Hasonlóképpen, a [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazások esetében az Azure ad-bérlő minden olyan felhasználója hozzáférhet az alkalmazáshoz, amikor sikeresen hitelesítik magukat a megfelelő bérlőn.

A bérlői rendszergazdák és a fejlesztők gyakran rendelkeznek olyan követelményekkel, amelyekben az alkalmazásnak egy adott felhasználói csoportra kell korlátozódnia. A fejlesztők ugyanúgy tudják elérni a népszerű engedélyezési mintákat, mint a szerepköralapú Access Control (RBAC), de ez a megközelítés jelentős mennyiségű munkát igényel a fejlesztő részéről.

Az Azure AD lehetővé teszi, hogy a bérlői rendszergazdák és fejlesztők egy alkalmazást a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára korlátozzák.

## <a name="supported-app-configurations"></a>Támogatott alkalmazások konfigurációi

Egy adott alkalmazás a következő típusú alkalmazásokkal rendelkező felhasználók vagy biztonsági csoportok meghatározott csoportjára való korlátozására használható:

- SAML-alapú hitelesítéssel összevont egyszeri bejelentkezésre konfigurált alkalmazások
- Az Azure AD előzetes hitelesítést használó alkalmazásproxy-alkalmazásai
- A közvetlenül az Azure AD alkalmazás platformján létrehozott alkalmazások, amelyek a OAuth 2.0/OpenID Connect hitelesítést használják, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba.

     > [!NOTE]
     > Ez a funkció csak a Web App/web API-hoz és a vállalati alkalmazásokhoz érhető el. A [natívként](quickstart-v1-integrate-apps-with-azure-ad.md) regisztrált alkalmazások nem korlátozhatók a bérlőben lévő felhasználók vagy biztonsági csoportok csoportjára.

## <a name="update-the-app-to-enable-user-assignment"></a>Az alkalmazás frissítése a felhasználó-hozzárendelés engedélyezéséhez

Az engedélyezett felhasználói hozzárendeléssel kétféleképpen hozhatók létre alkalmazások. Egy **globális rendszergazdai** szerepkörre van szükség, a második nem.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Vállalati alkalmazások (globális rendszergazda szerepkört igényel)

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
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen**értékre. Ha a beállítás értéke **Igen**, akkor a felhasználóknak először hozzá kell rendelniük ezt az alkalmazást ahhoz, hogy hozzáférhessenek hozzá.
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
1. Keresse meg a **kötelező felhasználói hozzárendelés szükséges** beállítást, és állítsa **Igen**értékre. Ha a beállítás értéke **Igen**, akkor a felhasználóknak először hozzá kell rendelniük ezt az alkalmazást ahhoz, hogy hozzáférhessenek hozzá.
1. A konfigurációs módosítás mentéséhez válassza a **Mentés** lehetőséget.

## <a name="assign-users-and-groups-to-the-app"></a>Felhasználók és csoportok társítása az alkalmazáshoz

Ha úgy konfigurálta az alkalmazást, hogy engedélyezze a felhasználó-hozzárendelést, akkor a felhasználók és csoportok az alkalmazáshoz rendelhetők.

1. Válassza ki a **felhasználók és csoportok** panelt az alkalmazás bal oldali navigációs menüjében.
1. A **felhasználók és csoportok** lista tetején válassza a **felhasználó hozzáadása** gombot a **hozzárendelés hozzáadása** ablaktábla megnyitásához.
1. Válassza ki a **felhasználók** választót a **hozzárendelés hozzáadása** panelen. 

     A felhasználók és a biztonsági csoportok listája a szövegmezővel együtt jelenik meg egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ezen a képernyőn egyszerre több felhasználót és csoportot is kijelölhet.

1. Ha elkészült a felhasználók és a csoportok kiválasztásával, kattintson a lenti **kiválasztás** gombra a következő részre való áttéréshez.
1. A felhasználók és csoportok alkalmazásban való hozzárendelésének befejezéséhez nyomja le az alján található **hozzárendelés** gombot. 
1. Győződjön meg arról, hogy a hozzáadott felhasználók és csoportok megjelennek a frissített **felhasználók és csoportok** listában.

