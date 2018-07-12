---
title: Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal| Microsoft Docs
description: Megtudhatja, hogyan kezelhetők a felhasználói hozzáférések csoporttagságként vagy alkalmazáshoz való hozzárendelésként az Azure Active Directory hozzáférési felülvizsgálatokkal
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 1f780a557c7993822de2d00963238dc865e4df36
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38528637"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directoryval (Azure AD) könnyedén biztosíthatja a felhasználók megfelelő hozzáférését. Megkérheti magukat a felhasználókat, esetleg egy döntéshozót, hogy vegyenek részt a hozzáférési felülvizsgálatban és hitelesítsék újból (vagy igazolják) a felhasználó hozzáférését. A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. A hozzáférési felülvizsgálat lezárása után módosításokat végezhet, és eltávolíthatja azon felhasználók hozzáférését, akiknek többé nincs rá szükségük.

> [!NOTE]
> Ha csak a vendégfelhasználók hozzáférését szeretné felülvizsgálni, akkor tekintse meg [vendégfelhasználók hozzáférésének a hozzáférési felülvizsgálatokkal történő felügyeletével](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) foglalkozó részt. Ha a rendszergazdai szerepkörrel rendelkező felhasználók (például a globális rendszergazdák) tagságát szeretné felülvizsgálni, tekintse meg a [hozzáférési felülvizsgálat az Azure AD Privileged Identity Management alkalmazásban történő indításával](privileged-identity-management/pim-how-to-start-security-review.md) foglalkozó cikket. 
>
>

## <a name="prerequisites"></a>Előfeltételek 


A hozzáférési felülvizsgálatok az Azure AD Premium P2 kiadásában érhetők el, a Microsoft nagyvállalati mobilitási + biztonsági E5 csomagban. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások). Az ezt a funkciót használó összes felhasználónak, beleértve a felülvizsgálatot létrehozó, a felülvizsgálatot kitöltő és a hozzáférését megerősítő felhasználóknak is licencre van szükségük. 

## <a name="create-and-perform-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása és végrehajtása

A hozzáférési felülvizsgálatokban egy vagy több felhasználó is szerepelhet felülvizsgálóként.  

1. Válasszon ki egy legalább egy taggal rendelkező csoportot az Azure AD-ben. Vagy válasszon ki egy olyan, az Azure AD-hez csatlakozó alkalmazást, amelyhez legalább egy felhasználó hozzá van rendelve. 

2. Döntse el, hogy minden felhasználó a saját hozzáférését vizsgálja-e felül, vagy inkább kijelöl egy vagy több felhasználót a többiek hozzáférésének felülvizsgálatára.

3. Engedélyezze a hozzáférési felülvizsgálatok megjelenítését a felülvizsgálók hozzáférési paneljein. Globális rendszergazdaként vagy felhasználói fiók adminisztrátoraként nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Indítsa el a hozzáférési felülvizsgálatot. További információkért lásd: [Hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).

5. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt az Azure AD-től, amely a hozzáférési panelre mutató hivatkozást tartalmazza, ahol [elvégezhetik a hozzáférési felülvizsgálatot](active-directory-azure-ad-controls-perform-access-review.md).

6. Ha a felülvizsgálók nem végezték el a felülvizsgálatot, az Azure AD-n keresztül emlékeztetőt is küldhet nekik. Alapértelmezés szerint az Azure AD a rendelkezésre álló idő felénél automatikusan emlékeztetőt küld azoknak a felülvizsgálóknak, akik még nem tettek eleget a kérésnek.

7. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. További információkért lásd: [Hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Következő lépések

[Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)




