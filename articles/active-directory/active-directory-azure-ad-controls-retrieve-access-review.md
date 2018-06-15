---
title: Azure AD hozzáférési felülvizsgálatok eredményeinek lekérése | Microsoft Docs
description: Az Azure Active Directory hozzáférési felülvizsgálatok eredményeinek lekérése.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/16/2018
ms.author: rolyon
ms.openlocfilehash: c30d166335e31cdbf03283371da42fa6b7cda162
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233114"
---
# <a name="retrieve-access-review-results"></a>Hozzáférési felülvizsgálatok eredményeinek lekérése

A rendszergazdák az Azure Active Directory (Azure AD) használatával [hozzáférési felülvizsgálatokat](active-directory-azure-ad-controls-create-access-review.md) hozhatnak létre a csoporttagok vagy alkalmazáshoz rendelt felhasználók számára.  A **Globális rendszergazda**, **Biztonsági rendszergazda** vagy **Biztonsági olvasó** szerepkörben lévő felhasználók a hozzáférési felülvizsgálatok eredményeit is olvashatják.  Ha felhasználókat szeretne rendelni ezen szerepkörök egyikéhez, egy kiemelt szerepkörű rendszergazda az Azure AD PIM használatával jogosulttá teheti a felhasználókat a szerepkör aktiválására, vagy egy globális rendszergazda végleg [a szerepkörhöz rendelhet egy felhasználót](active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Hozzáférési felülvizsgálat keresése

Ha tudja, hogy mely program tartalmazza a hozzáférési felülvizsgálatot, lépjen a Hozzáférési felülvizsgálatok oldalra, válassza a **Programok** lehetőséget, és válassza ki a hozzáférési felülvizsgálati vezérlőt tartalmazó programot.  Ezután válassza a **Vezérlők** lehetőséget, és válassza ki a hozzáférési felülvizsgálati vezérlőt. Ha a programban sok vezérlő van, szűrhet adott típusú vagy állapotú vezérlőkre. A hozzáférési felülvizsgálati vezérlő neve vagy az azt létrehozó tulajdonos megjelenített neve alapján is kereshet. 

Ha nem tudja, hogy melyik program tartalmazza a hozzáférési felülvizsgálatot, lépjen a Hozzáférési felülvizsgálatok oldalra, és válassza a **Vezérlők** lehetőséget.  Szűrhet adott típusú vagy állapotú vezérlőkre, és a hozzáférési felülvizsgálati vezérlő neve vagy az azt létrehozó tulajdonos megjelenített neve alapján is kereshet. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Az eredmények lekérése hozzáférési felülvizsgálathoz

Ha a felülvizsgálat egyszeri ismétlődési típusú, akkor az aktív hozzáférési felülvizsgálatok állapota és a befejezett hozzáférési felülvizsgálatok eredményei az **Eredmények** szakaszból kérhetők le.  Beírhatja egy olyan felhasználó megjelenített nevét vagy egyszerű felhasználónevét, akinek a hozzáférése felülvizsgálat alatt áll, hogy csak ennek a felhasználónak a hozzáférését tekintse meg.  Egy befejezett hozzáférési felülvizsgálat összes eredményének lekéréséhez kattintson a **Letöltés** gombra.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Ismétlődő hozzáférési felülvizsgálat több példánya eredményeinek lekérése

Ha egy ismétlődő aktív hozzáférési felülvizsgálat állapotát szeretné megtekinteni, kattintson az **Eredmények** szakaszra.  Beírhatja egy olyan felhasználó megjelenített nevét vagy egyszerű felhasználónevét, akinek a hozzáférése felülvizsgálat alatt áll.

Ha ismétlődő hozzáférési felülvizsgálat befejezett példányának eredményeit szeretné megtekinteni, válassza a **Felülvizsgálati előzmények** lehetőségre, majd válassza ki az adott példányt a befejezett hozzáférési felülvizsgálat példányok listájából a példány kezdő és befejező dátuma alapján.   A példány eredményei az **Eredmények** szakaszról kérhetők le.  Beírhatja egy olyan felhasználó megjelenített nevét vagy egyszerű felhasználónevét, akinek a hozzáférése felülvizsgálat alatt áll, hogy csak ennek a felhasználónak a hozzáférését tekintse meg.  Egy ismétlődő hozzáférési felülvizsgálat befejezett példánya összes eredményének lekéréséhez kattintson a **Letöltés** gombra.


## <a name="removing-users-from-an-access-review"></a>Felhasználók eltávolítása egy hozzáférési felülvizsgálatból

Alapértelmezés szerint a törölt felhasználók 30 napig töröltek maradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket.  A felhasználók 30 nap után véglegesen törlődnek.  Ezenkívül egy globális rendszergazda az Azure Active Directory portálon explicit módon [véglegesen törölhet egy közelmúltban törölt felhasználót](active-directory-users-restore.md) az időszak lejárta előtt.  Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív hozzáférési felülvizsgálatokból.  A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="next-steps"></a>További lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Programok és vezérlők felügyelete az Azure AD hozzáférési felülvizsgálatokkal](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](active-directory-privileged-identity-management-how-to-start-security-review.md)


