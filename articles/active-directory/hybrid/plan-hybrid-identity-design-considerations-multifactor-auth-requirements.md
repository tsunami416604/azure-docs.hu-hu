---
title: Hibrid identitás Tervező – az Azure multi-factor authentication követelményeinek |} A Microsoft Docs
description: A feltételes hozzáférés-vezérléssel Azure Active Directory ellenőrzi a meghatározott feltételek, válassza ki, amikor a felhasználó hitelesítése és engedélyezése az alkalmazáshoz való hozzáférés előtt. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférhessen az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 90df9926ca15666b94a72a7ff9f6f306f1193e14
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492691"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás a multi-factor authentication követelményeinek meghatározása
A világon a mobilitás, a felhasználói adatokat és alkalmazásokat a felhőben, és bármilyen eszközről Ez az információ biztonságossá tétele vált a kiemelkedő.  Minden nap van egy új főcíme kapcsolatos biztonsági incidensek.  Bár a nincs garancia arra, ilyen adatszivárgás elleni, a többtényezős hitelesítés, ezeket az adatszivárgás megakadályozása érdekében biztonsági réteget biztosít.
Indítsa el a multi-factor authentication a szervezet követelményeinek kiértékelése. Azt jelenti mi van a szervezet szeretne biztonságossá tenni.  Ezt a próbaidőszakot fontos, hogy beállításához és a szervezetek felhasználók a multi-factor authentication lehetővé teszi a technikai követelmények meghatározása.

Ügyeljen arra, hogy válaszoljon a következő:

* A vállalat próbál biztonságossá tétele a Microsoft-alkalmazások? 
* Hogyan közzétett ezeket az alkalmazásokat?
* A vállalat nyújt a munkatársak számára a helyszíni alkalmazások elérését engedélyezi a távoli hozzáférést?

Ha igen, milyen típusú távoli hozzáférést? Is kell kiértékelése, ahová a felhasználók ezeket az alkalmazásokat elérő kerülnek. Ezt a próbaidőszakot egy másik fontos lépés a megfelelő többtényezős hitelesítés stratégia meghatározása. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol vannak a felhasználók fog található?
* Ezek bárhol lehetnek?
* Nem a vállalat szeretne létesíteni a korlátozásokat a felhasználó helye szerint?

Miután megértette ezeknek a követelményeknek, fontos is értékelheti ki a felhasználói követelmények a multi-factor Authentication hitelesítéshez. Ezt a próbaidőszakot fontos, mert azt határozza meg a multi-factor authentication szolgáltatás bevezetéséről követelményei. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Ismeri a felhasználókat a multi-factor authentication?
* Néhány felhasználása lesz szükség további hitelesítésre?  
  * Ha igen, minden esetben, ha külső hálózatokat, vagy konkrét alkalmazások elérésére, vagy más feltételek érkező?
* Kell-e a felhasználók és beállítása és a multi-factor authentication végrehajtása a betanítási?
* Mik a főbb forgatókönyvek megvalósítását, amely a vállalat szeretne a multi-factor authentication szolgáltatás engedélyezése a felhasználók számára?

A fenti kérdések megválaszolását, után lesz megértéséhez, ha nincsenek a helyszíni multi-factor authentication szolgáltatás már implementálva. Ezt a próbaidőszakot fontos, hogy beállításához és a szervezetek felhasználók a multi-factor authentication lehetővé teszi a technikai követelmények meghatározása. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A vállalatának van szüksége az MFA kiemelt jogosultságú fiókok védelméhez?
* A vállalatának van szüksége ahhoz, hogy az egyes alkalmazás megfelelőségi okokból MFA?
* Szüksége van-e alkalmazás-vagy csak a rendszergazdák az összes jogosult felhasználók számára engedélyezi az MFA Használatát a vállalatának?
* Szükség van MFA mindig engedélyezve van, vagy csak amikor a felhasználók bejelentkeznek a vállalati hálózaton kívül?

## <a name="next-steps"></a>További lépések
[A hibrid identitás bevezetési stratégia kidolgozása](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

