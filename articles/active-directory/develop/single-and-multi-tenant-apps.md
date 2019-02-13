---
title: Az Azure Active Directoryban egyetlen és több-bérlős alkalmazások
description: Ismerje meg a szolgáltatásokat és egybérlős és több-bérlős alkalmazások az Azure AD közötti különbségeket.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ec1939dd1f5445583200776e489f7596f572f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178447"
---
# <a name="tenancy-in-azure-active-directory"></a>Az Azure Active Directoryban bérlős

Az Azure Active Directory (Azure AD) csoportokba rendezi az objektumok, például felhasználók és alkalmazások nevű *bérlők*. Bérlők lehetővé teszik a rendszergazdáknak a szabályzatok beállítását a felhasználók az alkalmazásokat, amelyek a szervezet tulajdonában lévő biztonsági és működési szabályzatai a szervezeten belül. 

## <a name="who-can-sign-in-to-your-app"></a>Ki jelentkezhet be az alkalmazásba?

Használatával történő alkalmazásfejlesztésbe származik, amikor a fejlesztők megadhatja alkalmazás az egybérlős vagy több-bérlős lehet az alkalmazás regisztrációja során a [az Azure portal](https://portal.azure.com).
* Egybérlős alkalmazások csak a bérlő azok volt regisztrálva, más néven a saját bérlőjén érhető el.
* Több-bérlős alkalmazások a felhasználók a saját bérlőjén és a többi bérlő számára érhetők el.

Az Azure Portalon konfigurálhatja úgy az alkalmazását az egybérlős vagy több-bérlős célközönség módon beállításával.

| Célközönség | Egyetlen/több-tenant | Ki jelentkezhet be | 
|----------|--------| ---------|
| A fiókok csak ebben a címtárban | Egybérlős alkalmazás | A címtárában található összes felhasználói és vendégfiók használhatja az alkalmazást vagy az API-t.<br>*Használja ezt a beállítást, ha a cégen belüli használatra, a célközönséget.* |
| Tetszőleges Azure AD-címtárban található fiókok | Több-bérlős | Minden felhasználó és a vendégek egy munkahelyi vagy iskolai fiókkal, a Microsoft API-t vagy az alkalmazás használhatja. Ez magában foglalja az iskolák és az Office 365 használó vállalatok.<br>*Használja ezt a beállítást, ha a célközönséget üzleti vagy oktatási ügyfelek.* |
| Minden olyan Azure AD-címtár és a személyes Microsoft-fiókok (például a Skype, Xbox és Outlook.com) fiókok | Több-bérlős | A munkahelyi, iskolai vagy személyes Microsoft-fiókkal rendelkező felhasználókat az alkalmazás- vagy API-t használhatja. Az iskolák és az Office 365-höz, valamint a személyes fiókok, mint az Xbox és a Skype-szolgáltatásokhoz való bejelentkezéshez használt használó vállalatok tartalmazza.<br>*Ezt a beállítást használja, amelyekre a Microsoft-fiókok legszélesebb készletét.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Ajánlott eljárások a több-bérlős alkalmazások

Több-bérlős nagyszerű alkalmazások készítésére kihívást jelenthet, amely a bérlői rendszergazdák állíthatja be különböző szabályzatok száma miatt. Ha úgy dönt, hogy egy több-bérlős alkalmazás készítése, kövesse az alábbi gyakorlati tanácsokat:

* Az alkalmazás tesztelése a konfigurált bérlő [feltételes hozzáférési szabályzatok](conditional-access-dev-guide.md).
* Kövesse a felhasználói hozzáférés legalább győződjön meg arról, hogy csak az alkalmazás kéri a ténylegesen szükséges engedélyek elvét. Kerülje a rendszergazdai jóváhagyásra van szükség, mivel ez megakadályozhatja a felhasználók az alkalmazás minden egyes szervezetek beszerzése a engedélyeket kérő. 
* Adja meg a megfelelő nevekkel, és elérhetővé teszi az alkalmazás részeként engedélyek leírását. Ez segít a felhasználóknak, és a rendszergazdák tudják, mire azok elfogadja meghatározott, amikor azok megkísérlik az alkalmazás API-k használata. További információkért tekintse meg az ajánlott eljárások szakasz a [engedélyek útmutató](v1-permissions-and-consent.md).

## <a name="next-steps"></a>További lépések

* [Kell több-bérlős alkalmazás átalakítása](howto-convert-app-to-be-multi-tenant.md)
