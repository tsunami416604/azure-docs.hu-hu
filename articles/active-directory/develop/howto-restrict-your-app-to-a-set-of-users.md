---
title: Hogyan korlátozhatja a felhasználók az Azure Active Directoryban regisztrált alkalmazás
description: Ismerje meg, hogyan korlátozza a hozzáférést az alkalmazások a felhasználók egy kiválasztott csoportja az Azure AD-ben regisztrált.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
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
ms.openlocfilehash: 17b76a25b3c5c2c3ce4dc4217389706a4b24d837
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210288"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Útmutató: Alkalmazás korlátozása felhasználók egy halmazára

Az Azure Active Directory (Azure AD) bérlő regisztrált alkalmazások, alapértelmezés szerint vannak, a felhasználók rendelkezésére a bérlő sikeres hitelesítése.

Ehhez hasonlóan a esetben, egy [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazás, ahol az alkalmazás ki van építve az Azure AD-bérlő összes felhasználója fogja tudni azokat a megfelelő bérlő sikeres hitelesítése után az alkalmazás eléréséhez.

A bérlői rendszergazdák és fejlesztők gyakran követelményei ahol-alkalmazásnak rendelkeznie kell a felhasználók meghatározott készlete korlátozott. A fejlesztők népszerű engedélyezési minták például szerepkör alapú hozzáférés-vezérlés (RBAC) segítségével végezheti azonos, de ezt a megközelítést igényel a része a fejlesztői munka jelentős mennyiségű.

Az Azure AD bérlő lehetővé teszi a rendszergazdák és fejlesztők alkalmazás korlátozhatja a felhasználók vagy biztonsági csoportok a bérlő egy adott készletét.

## <a name="supported-app-configurations"></a>Támogatott konfigurációk

A beállítás egy alkalmazás korlátozhatja a felhasználók vagy biztonsági csoportok a bérlő egy meghatározott készletének működik együtt a következő típusú alkalmazásokat:

- Az összevont egyszeri bejelentkezési SAML-alapú hitelesítéssel konfigurált alkalmazások
- Application proxy alkalmazásai előtti az Azure AD-hitelesítést használó
- Közvetlenül az Azure AD alkalmazás-platformra épülő alkalmazások használja az OAuth 2.0 és OpenID Connect hitelesítést követően a felhasználó vagy rendszergazda hozzájárult az alkalmazást.

     > [!NOTE]
     > Webalkalmazás vagy webes API- és nagyvállalati alkalmazásokkal csak ez a funkció érhető el. Mint regisztrált alkalmazások [natív](quickstart-v1-integrate-apps-with-azure-ad.md) nem korlátozható a felhasználók vagy biztonsági csoportok a bérlőben.

## <a name="update-the-app-to-enable-user-assignment"></a>Az alkalmazás engedélyezése a felhasználó-hozzárendelés frissítése

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be, egy **globális rendszergazdája.**
1. Az a felső menüsávon válassza ki a bejelentkezett fiókot. 
1. A **Directory**, válassza ki az Azure AD-bérlővel, ahol az alkalmazás regisztrálva lesz.
1. A bal oldali navigációs sávján válassza **Azure Active Directory**. Ha az Azure Active Directory nem érhető el a navigációs panelen, majd kövesse az alábbi lépéseket:

    1. Válassza ki **minden szolgáltatás** a fő bal oldali navigációs menü tetején.
    1. Írja be a **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory** elem az eredményből.

1. Az a **Azure Active Directory** ablaktáblán válassza előbb **vállalati alkalmazások** a a **Azure Active Directory** bal oldali navigációs menü.
1. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

     Ha nem látja a kívánt itt megjelenni alkalmazást, a különböző szűrők használata felső részén a **minden alkalmazás** korlátozhatja a görgessen lefelé a listában keresse meg az alkalmazás vagy a listában.

1. Válassza ki az alkalmazást, szeretne rendelni egy felhasználó vagy biztonsági csoportot a listából.
1. Az alkalmazás **áttekintése** lapon jelölje be **tulajdonságok** az alkalmazás bal oldali navigációs menüjében.
1. Keresse meg a beállítást **kell felhasználó-hozzárendelés?** , beállíthatja azt a **Igen**. Ha ez a beállítás értéke **Igen**, majd a felhasználók először hozzá kell rendelni ehhez az alkalmazáshoz, mielőtt elérheti azt.
1. Válassza ki **mentése** a konfigurációs módosítás mentéséhez.

## <a name="assign-users-and-groups-to-the-app"></a>Felhasználók és csoportok hozzárendelése az alkalmazáshoz

Miután konfigurálta az alkalmazást, hogy engedélyezze a felhasználó-hozzárendelés, lépjen tovább, és rendelje hozzá a felhasználókat és csoportokat az alkalmazáshoz.

1. Válassza ki a **felhasználók és csoportok** ablaktábla bal oldali navigációs menü az alkalmazás.
1. Felső részén a **felhasználók és csoportok** listáról válassza ki a **felhasználó hozzáadása** gombra kattintva nyissa meg a **hozzárendelés hozzáadása** ablaktáblán.
1. Válassza ki a **felhasználók** a választó a **hozzárendelés hozzáadása** ablaktáblán. 

     Felhasználók és biztonsági csoportok listáját egy szövegmező, és a egy bizonyos felhasználó vagy csoport megkereséséhez együtt jelenik meg. Ezen a képernyőn válassza ki a több felhasználó és csoport, lépjen egy teszi lehetővé.

1. Ha elkészült a felhasználók és csoportok kiválasztása, nyomja le az ENTER a **válassza** áthelyezése a következő részére, alsó gombjára.
1. Nyomja le az **hozzárendelése** gombra a befejezéséhez a felhasználókat és csoportokat az alkalmazás-hozzárendeléseit a képernyő aljára. 
1. Győződjön meg arról, hogy a felhasználók és csoportok hozzáadott jelennek meg a frissített **felhasználók és csoportok** listája.

