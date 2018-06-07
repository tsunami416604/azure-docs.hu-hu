---
title: Az Azure Active Directory jelentéskészítési értesítései
description: Hogyan használható az Azure Active Directory reporting gyanús bejelentkezési értesítéseket moduljainak.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 01/03/2018
ms.author: dhanyahk;rolyon
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 8f4beece9d1d351c91f2bb055eedc0ae9851f532
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588188"
---
# <a name="azure-active-directory-reporting-notifications"></a>Az Azure Active Directory jelentéskészítési értesítései
## <a name="what-reports-generate-email-notifications"></a>Milyen jelentések készítése az értesítő e-mailek
Jelenleg csak a szabálytalan bejelentkezési tevékenység jelentés eseményindítók e-mail értesítések.

## <a name="what-is-an-irregular-sign-in"></a>Mi az az "szabálytalan bejelentkezési"?
Szabálytalan bejelentkezések megegyeznek a gépi tanulási algoritmusok, alapján nem "lehetetlen odautazás" állapotot egy rendellenes bejelentkezési helye és az eszköz által azonosított. Ez azt jelezheti, hogy egy támadó megpróbálja ezzel a fiókkal bejelentkezni.

## <a name="who-receives-the-email-notifications"></a>Ki kapja meg az e-mail értesítések?
Az e-mailt küld összes globális rendszergazda, aki van rendelve egy Active Directory Premium licenc. Annak érdekében, hogy biztosítását, kapni, a rendszergazdáknak másodlagos E-mail-cím is. Tartalmaznia kell a rendszergazdák aad-alerts-noreply@mail.windowsazure.com a saját megbízható feladók listájához, így azok nem hagy ki az e-mailt.

## <a name="how-often-are-these-emails-sent"></a>Milyen gyakran történik a küldi az e-maileket?
Az e-mailt küld, ha a 10 új szabálytalan bejelentkezési tevékenység történik az elmúlt 30 napban, vagy mert a legutóbbi e-mailben küldték, attól függően kisebb.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Hogyan érhető el a jelentésben szerepel az e-mailt?
Ha a hivatkozásra kattint, az Azure-portálon belül a jelentés oldalra irányítja. A jelentés eléréséhez kell lennie mindkét:

* Rendszergazdaként vagy az Azure-előfizetéshez társadminisztrátornak
* A címtár globális rendszergazdája és az Active Directory Premium licenc hozzárendelése. További információk: [Azure Active Directory editions](active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="can-i-turn-off-these-emails"></a>Lehet kikapcsolni az e-maileket?
Igen, a rendellenes bejelentkezések az Azure-portálon belül kapcsolatos értesítések kikapcsolásához kattintson **konfigurálása**, majd válassza ki **letiltott** alatt a **értesítések** szakasz.

## <a name="whats-next"></a>A következő lépések
* Fejezetét milyen biztonsági, naplózási és tevékenységjelentéseket állnak rendelkezésre? Tekintse meg [az Azure AD biztonsági, naplózási és tevékenységjelentéseket](active-directory-view-access-usage-reports.md)
* [Bevezetés a Prémium szintű Azure Active Directory használatába](active-directory-get-started-premium.md)
* [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon](customize-branding.md)

