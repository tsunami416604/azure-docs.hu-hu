---
title: Egy-és több-bérlős alkalmazások Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6aa8f63b6e7355ae387a321acf77683fac22e028
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803654"
---
# <a name="tenancy-in-azure-active-directory"></a>Bérlet Azure Active Directory

Azure Active Directory (Azure AD) olyan objektumokat szervez, mint a felhasználók és az alkalmazások a *bérlők*nevű csoportokban. A bérlők lehetővé teszik, hogy a rendszergazda szabályzatokat állítson be a szervezeten belüli felhasználókon és a szervezet tulajdonában lévő alkalmazásokkal, hogy azok megfeleljenek a biztonsági és működési házirendeknek. 

## <a name="who-can-sign-in-to-your-app"></a>Ki tud bejelentkezni az alkalmazásba?

Az alkalmazások fejlesztése során a fejlesztők úgy is dönthetnek, hogy az alkalmazás regisztrálásakor egy vagy több bérlőt konfigurálnak a [Azure Portal](https://portal.azure.com).
* Az egybérlős alkalmazások csak abban a bérlőben érhetők el, amelyet regisztráltak, más néven a saját bérlők.
* A több-bérlős alkalmazások a felhasználók saját bérlői és más bérlői számára egyaránt elérhetők.

A Azure Portalban úgy konfigurálhatja az alkalmazást, hogy egybérlős vagy több-bérlő legyen a célközönségnek a következőképpen történő beállításával.

| Közönség | Egyetlen/több-bérlős | Kik jelentkezhetnek be | 
|----------|--------| ---------|
| Csak ebben a könyvtárban lévő fiókok | Egybérlős alkalmazás | A címtárban lévő összes felhasználói és vendég fiók használhatja az alkalmazást vagy az API-t.<br>*Akkor használja ezt a beállítást, ha a célközönség belső a szervezete számára.* |
| Fiókok bármely Azure AD-címtárban | Több-bérlős | A Microsoft által használt munkahelyi vagy iskolai fiókkal rendelkező felhasználók és vendégek az alkalmazást vagy az API-t használhatják. Ilyenek például az Office 365-et használó iskolák és vállalkozások.<br>*Akkor használja ezt a lehetőséget, ha a célközönség üzleti vagy oktatási ügyfél.* |
| Fiókok bármely Azure AD-címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com) | Több-bérlős | Minden munkahelyi vagy iskolai felhasználó, vagy személyes Microsoft-fiók használhatja az alkalmazást vagy az API-t. Olyan iskolákat és vállalkozásokat foglal magába, amelyek az Office 365-et használják, valamint olyan személyes fiókokat, amelyeket az Xbox és a Skype szolgáltatásba való bejelentkezéshez használtak.<br>*Ezzel a beállítással megcélozhatja a Microsoft-fiókok legszélesebb körét.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Ajánlott eljárások több-bérlős alkalmazásokhoz

A nagyszerű, több-bérlős alkalmazások kiépítése kihívást jelenthet, mert a rendszergazdák által a bérlők számára megadható különböző szabályzatok száma. Ha több-bérlős alkalmazást szeretne létrehozni, kövesse az alábbi ajánlott eljárásokat:

* Tesztelje az alkalmazást egy olyan bérlőn, amely beállította a [feltételes hozzáférési szabályzatokat](conditional-access-dev-guide.md).
* A minimális felhasználói hozzáférés elve alapján győződjön meg arról, hogy az alkalmazás csak a ténylegesen szükséges engedélyeket kéri. Kerülje a rendszergazdai belefoglalást igénylő engedélyek megadását, mivel ez megakadályozhatja, hogy a felhasználók bizonyos szervezeteknél is beszerezzék az alkalmazást. 
* Adja meg az alkalmazás részeként közzétett engedélyek megfelelő nevét és leírását. Ez segíti a felhasználókat és a rendszergazdákat arról, hogy mit fogadnak el az alkalmazás API-jai használatának megkísérlése során. További információ: az [engedélyek útmutatójának](v1-permissions-and-consent.md)ajánlott eljárások szakasza.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazások átalakítása több-bérlővé](howto-convert-app-to-be-multi-tenant.md)
