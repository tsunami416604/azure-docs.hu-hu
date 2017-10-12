---
title: "Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal| Microsoft Docs"
description: "Megtudhatja, hogyan kezelhetők a felhasználói hozzáférések csoporttagságként vagy alkalmazáshoz való hozzárendelésként az Azure Active Directory hozzáférési felülvizsgálatokkal"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directoryval könnyedén biztosíthatja a felhasználók megfelelő hozzáférését úgy, hogy magukat a felhasználókat, esetleg egy döntéshozót megkéri a hozzáférési felülvizsgálatban való részvételre és a felhasználó hozzáférésének újbóli hitelesítésére (vagy „igazolására”).  A felülvizsgálatot végzők az Azure AD-ből származó javaslatok alapján dönthetnek arról, hogy az egyes felhasználóknak szükségük van-e folyamatos hozzáférésre. A hozzáférési felülvizsgálat lezárása után módosításokat végezhet, és eltávolíthatja azon felhasználók hozzáférését, akiknek többé nincs rá szükségük.

> [!NOTE]
> Ha csak a vendégfelhasználók hozzáférését szeretné felülvizsgálni, akkor tekintse meg [vendégfelhasználók hozzáférésének a hozzáférési felülvizsgálatokkal történő felügyeletével](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) foglalkozó részt.  Ha pedig a rendszergazdai szerepkörrel rendelkező felhasználók (például a globális rendszergazdák) tagságát szeretné felülvizsgálni, tekintse meg a [hozzáférési felülvizsgálat Azure AD PIM-ben történő indításával](active-directory-privileged-identity-management-how-to-start-security-review.md) foglalkozó cikket. 
>
>

## <a name="prerequisites"></a>Előfeltételek 

A hozzáférési felülvizsgálatok az Azure Active Directory Premium P2 kiadásában érhetők el, az EMS E5 csomagban. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).  Minden olyan felhasználónak licencre van szüksége, aki a szolgáltatást felülvizsgálat létrehozása, hozzáférés felülvizsgálata vagy felülvizsgálat alkalmazása céljából használja.


## <a name="creating-and-performing-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása és végrehajtása

A hozzáférési felülvizsgálatokban egy vagy több felhasználó is szerepelhet felülvizsgálóként.  

1. Válasszon ki egy legalább egy taggal rendelkező csoportot az Azure Active Directoryban, vagy egy olyan, az Azure Active Directoryhoz csatlakozó alkalmazást, amelyhez legalább egy felhasználó hozzá van rendelve. 
2. Döntse el, hogy minden felhasználó a saját hozzáférését vizsgálja-e felül, vagy inkább kijelöl egy vagy több felhasználót a többiek hozzáférésének felülvizsgálatára.
3. Engedélyezze a hozzáférési felülvizsgálatok megjelenítését a felülvizsgáló hozzáférési paneljein.  Globális rendszergazdaként nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Indítsa el a hozzáférési felülvizsgálatot. További információk: [hozzáférési felülvizsgálat létrehozása](active-directory-azure-ad-controls-create-access-review.md).
5. Kérje meg a felülvizsgálókat, hogy nyilvánítsanak véleményt. Alapértelmezés szerint mindannyian kapnak egy e-mailt az Azure AD-től, amely a hozzáférési panelre mutató hivatkozást tartalmazza, ahol [elvégezhetik a hozzáférési felülvizsgálatot](active-directory-azure-ad-controls-perform-access-review.md).
6. Ha a felülvizsgálók nem végezték el a felülvizsgálatot, az Azure AD-n keresztül emlékeztetőt is küldhet nekik.  Alapértelmezés szerint az Azure AD a rendelkezésre álló idő felénél automatikusan emlékeztetőt küld azoknak a felülvizsgálóknak, akik még nem tettek eleget a kérésnek.
7. Ha mindenki elvégezte a felülvizsgálatot, állítsa le a hozzáférési felülvizsgálatot, és alkalmazza a módosításokat. Bővebb információk: [hozzáférési felülvizsgálatok elvégzése](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Következő lépések

- [Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)




