---
title: Egy-és több-bérlős alkalmazások az Azure AD-ben
titleSuffix: Microsoft identity platform
description: Ismerje meg az egybérlős és a több-bérlős Azure AD-beli alkalmazások funkcióit és különbségeit.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 38cb1222a64b1759528749caa15dfb1bb906cef6
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159913"
---
# <a name="tenancy-in-azure-active-directory"></a>Bérlet Azure Active Directory

Azure Active Directory (Azure AD) olyan objektumokat szervez, mint a felhasználók és az alkalmazások a *bérlők*nevű csoportokban. A bérlők lehetővé teszik, hogy a rendszergazda szabályzatokat állítson be a szervezeten belüli felhasználókon és a szervezet tulajdonában lévő alkalmazásokkal, hogy azok megfeleljenek a biztonsági és működési házirendeknek. 

## <a name="who-can-sign-in-to-your-app"></a>Ki tud bejelentkezni az alkalmazásba?

Az alkalmazások fejlesztése során a fejlesztők úgy is dönthetnek, hogy az alkalmazás regisztrálásakor egy vagy több bérlőt konfigurálnak a [Azure Portal](https://portal.azure.com).
* Az egybérlős alkalmazások csak abban a bérlőben érhetők el, amelyet regisztráltak, más néven a saját bérlők.
* A több-bérlős alkalmazások a felhasználók saját bérlői és más bérlői számára egyaránt elérhetők.

A Azure Portalban úgy konfigurálhatja az alkalmazást, hogy egybérlős vagy több-bérlő legyen a célközönségnek a következőképpen történő beállításával.

| Célközönség | Egyetlen/több-bérlős | Kik jelentkezhetnek be | 
|----------|--------| ---------|
| Csak ebben a könyvtárban lévő fiókok | Egybérlős alkalmazás | A címtárban lévő összes felhasználói és vendég fiók használhatja az alkalmazást vagy az API-t.<br>*Akkor használja ezt a beállítást, ha a célközönség belső a szervezete számára.* |
| Fiókok bármely Azure AD-címtárban | Több-bérlős | A Microsoft által használt munkahelyi vagy iskolai fiókkal rendelkező felhasználók és vendégek az alkalmazást vagy az API-t használhatják. Ilyenek például az Office 365-et használó iskolák és vállalkozások.<br>*Akkor használja ezt a lehetőséget, ha a célközönség üzleti vagy oktatási ügyfél.* |
| Fiókok bármely Azure AD-címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com) | Több-bérlős | Minden munkahelyi vagy iskolai felhasználó, vagy személyes Microsoft-fiók használhatja az alkalmazást vagy az API-t. Olyan iskolákat és vállalkozásokat foglal magába, amelyek az Office 365-et használják, valamint olyan személyes fiókokat, amelyeket az Xbox és a Skype szolgáltatásba való bejelentkezéshez használtak.<br>*Ezzel a beállítással megcélozhatja a Microsoft-fiókok legszélesebb körét.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Ajánlott eljárások több-bérlős alkalmazásokhoz

A nagyszerű, több-bérlős alkalmazások kiépítése kihívást jelenthet, mert a rendszergazdák által a bérlők számára megadható különböző szabályzatok száma. Ha több-bérlős alkalmazást szeretne létrehozni, kövesse az alábbi ajánlott eljárásokat:

* Tesztelje az alkalmazást egy olyan bérlőn, amely beállította a [feltételes hozzáférési szabályzatokat](../azuread-dev/conditional-access-dev-guide.md).
* A minimális felhasználói hozzáférés elve alapján győződjön meg arról, hogy az alkalmazás csak a ténylegesen szükséges engedélyeket kéri. Kerülje a rendszergazdai belefoglalást igénylő engedélyek megadását, mivel ez megakadályozhatja, hogy a felhasználók bizonyos szervezeteknél is beszerezzék az alkalmazást. 
* Adja meg az alkalmazás részeként közzétett engedélyek megfelelő nevét és leírását. Ez segíti a felhasználókat és a rendszergazdákat arról, hogy mit fogadnak el az alkalmazás API-jai használatának megkísérlése során. További információ: az [engedélyek útmutatójának](v2-permissions-and-consent.md)ajánlott eljárások szakasza.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazások átalakítása több-bérlővé](howto-convert-app-to-be-multi-tenant.md)
