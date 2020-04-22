---
title: Az Azure AD alkalmazás korlátozása felhasználók számára | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan korlátozhatja az Azure AD-ben regisztrált alkalmazásokhoz való hozzáférést a felhasználók kiválasztott készletére.
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
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759027"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Útmutató: Az Azure AD-alkalmazás korlátozása egy Azure AD-bérlő felhasználóinak készletére

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a bérlő minden olyan felhasználója számára elérhetők, akik sikeresen hitelesítik magukat.

Hasonlóképpen, egy [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazás esetén az Azure AD-bérlő, ahol ez az alkalmazás ki van építve, az alkalmazás minden felhasználója hozzáférhet az alkalmazáshoz, miután sikeresen hitelesíti magát a megfelelő bérlőben.

A bérlői rendszergazdák és a fejlesztők gyakran rendelkeznek olyan követelményekkel, amelyekhez az alkalmazást a felhasználók egy bizonyos csoportjára kell korlátozni. A fejlesztők ugyanezt a népszerű engedélyezési minták, például a szerepköralapú hozzáférés-vezérlés (RBAC) használatával is elvégezhetik, de ehhez a megközelítéshez jelentős mennyiségű munka szükséges a fejlesztő egy részén.

Bérlői rendszergazdák és fejlesztők korlátozhatja az alkalmazást egy adott felhasználói csoport vagy biztonsági csoportok a bérlőben az Azure AD beépített szolgáltatásának használatával.

## <a name="supported-app-configurations"></a>Támogatott alkalmazáskonfigurációk

A bérlő ben lévő felhasználók vagy biztonsági csoportok adott csoportjára való korlátozás a következő típusú alkalmazásokkal működik:

- SAML-alapú hitelesítéssel rendelkező egyszeri bejelentkezésre konfigurált alkalmazások
- Az Azure AD előhitelesítést használó alkalmazásproxy-alkalmazások
- Közvetlenül az Azure AD alkalmazásplatformra épülő alkalmazások, amelyek OAuth 2.0/OpenID Connect hitelesítést használnak, miután egy felhasználó vagy rendszergazda hozzájárult az adott alkalmazáshoz.

     > [!NOTE]
     > Ez a funkció csak webes alkalmazás/web API és vállalati alkalmazások esetén érhető el. [Natívként](quickstart-v1-integrate-apps-with-azure-ad.md) regisztrált alkalmazások nem korlátozhatók a bérlőben lévő felhasználók vagy biztonsági csoportok készletére.

## <a name="update-the-app-to-enable-user-assignment"></a>Az alkalmazás frissítése a felhasználói hozzárendelés engedélyezéséhez

Az engedélyezett felhasználói hozzárendeléssel kétféleképpen hozhat létre alkalmazást. Az egyik megköveteli a **globális rendszergazdai** szerepkört, a második nem.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Vállalati alkalmazások (globális rendszergazdai szerepkörszükséges)

1. Nyissa meg az [**Azure Portalon,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**
1. A felső sávon válassza ki a bejelentkezett fiókot. 
1. A **Címtár**csoportban válassza ki azt az Azure AD-bérlőt, ahol az alkalmazás regisztrálva lesz.
1. A bal oldali navigációs sávon válassza az **Azure Active Directory**lehetőséget. Ha az Azure Active Directory nem érhető el a navigációs ablakban, kövesse az alábbi lépéseket:

    1. Válassza a Bal oldali főnavigációs menü tetején található **Minden szolgáltatás** lehetőséget.
    1. Írja be az **Azure Active Directory** a szűrő keresőmezőbe, és válassza ki az Azure Active **Directory-elemet** az eredményből.

1. Az **Azure Active Directory** ablaktáblán válassza **a vállalati alkalmazások** az Azure Active **Directory** bal oldali navigációs menüben.
1. Válassza **a Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.

     Ha nem látja a kívánt alkalmazást, akkor a **Minden alkalmazás** lista tetején található különböző szűrőkkel korlátozhatja a listát, vagy görgessen le a listáról az alkalmazás megkereséséhez.

1. Jelölje ki azt az alkalmazást, amelyhez felhasználót vagy biztonsági csoportot szeretne rendelni a listából.
1. Az alkalmazás **Áttekintés lapján** válassza az alkalmazás bal oldali navigációs menüjének **Tulajdonságok elemét.**
1. Keresse meg a **szükséges felhasználói hozzárendelés beállítást,** és állítsa **igen (Igen)** beállításra. Ha ez a beállítás **Igen,** a bérlő felhasználóinak először hozzá kell rendelnie ezt az alkalmazást, vagy nem tudnak bejelentkezni az alkalmazásba.
1. A konfigurációmódosítás mentéséhez válassza a **Mentés** gombot.

### <a name="app-registration"></a>Alkalmazásregisztráció

1. Nyissa meg az [**Azure Portalt.**](https://portal.azure.com/)
1. A felső sávon válassza ki a bejelentkezett fiókot. 
1. A **Címtár**csoportban válassza ki azt az Azure AD-bérlőt, ahol az alkalmazás regisztrálva lesz.
1. A bal oldali navigációs sávon válassza az **Azure Active Directory**lehetőséget.
1. Az **Azure Active Directory** ablaktáblán válassza **az Alkalmazásregisztrációk** az **Azure Active Directory** bal oldali navigációs menüben.
1. Hozza létre vagy jelölje ki a kezelni kívánt alkalmazást. Meg kell **tulajdonosa** ezt az alkalmazást regisztráció.
1. Az alkalmazás **Áttekintéslapján** kövesse a Felügyelt alkalmazás helyi **könyvtárban** hivatkozását a lap tetején található alapvető beállítások között. Ezzel az alkalmazásregisztráció _felügyelt vállalati alkalmazásához_ vezet.
1. A bal oldali navigációs panelen válassza a **Tulajdonságok lehetőséget.**
1. Keresse meg a **szükséges felhasználói hozzárendelés beállítást,** és állítsa **igen (Igen)** beállításra. Ha ez a beállítás **Igen,** a bérlő felhasználóinak először hozzá kell rendelnie ezt az alkalmazást, vagy nem tudnak bejelentkezni az alkalmazásba.
1. A konfigurációmódosítás mentéséhez válassza a **Mentés** gombot.

## <a name="assign-users-and-groups-to-the-app"></a>Felhasználók és csoportok hozzárendelése az alkalmazáshoz

Miután beállította az alkalmazást a felhasználói hozzárendelés engedélyezésére, lehetővé teheti a felhasználók és csoportok hozzárendelését az alkalmazáshoz.

1. Válassza a **Felhasználók és csoportok** ablaktáblát az alkalmazás bal oldali navigációs menüjében.
1. A **Felhasználók és csoportok** lista tetején a Felhasználó **hozzáadása** gombra kattintva nyissa meg a **Hozzárendelés hozzáadása** ablaktáblát.
1. Válassza ki a **Felhasználók** választót a **Hozzárendelés hozzáadása** ablaktáblán. 

     A felhasználók és a biztonsági csoportok listája megjelenik egy szövegdobozzal együtt egy adott felhasználó vagy csoport kereséséhez és megkereséséhez. Ez a képernyő lehetővé teszi, hogy egyszerre több felhasználót és csoportot jelöljön ki.

1. Miután végzett a felhasználók és csoportok kijelölése, nyomja meg a **Kijelölés** gombot az alsó lépés a következő részre.
1. (Nem kötelező) Ha alkalmazásszerepköröket adott meg az alkalmazásban, a **Szerepkör kiválasztása** beállítással hozzárendelheti a kijelölt felhasználókat és csoportokat az alkalmazás egyik szerepköréhez. 
1. Az alsó **rész Hozzárendelés** gombjával befejezve a felhasználók és csoportok hozzárendelését az alkalmazáshoz. 
1. Ellenőrizze, hogy a hozzáadott felhasználók és csoportok megjelennek-e a frissített **Felhasználók és csoportok** listában.

## <a name="more-information"></a>További információ

- [Útmutató: Alkalmazásszerepkörök hozzáadása az alkalmazásba](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Engedélyezés hozzáadása alkalmazásszerepkörök kel & szerepkörjogcímek egy ASP.NET Core webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Biztonsági csoportok és alkalmazásszerepkörök használata az alkalmazásokban (videó)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, most antól csoportjogcímekkel és alkalmazásszerepkörökkel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Az Azure Active Directory alkalmazás jegyzékfájlja](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)