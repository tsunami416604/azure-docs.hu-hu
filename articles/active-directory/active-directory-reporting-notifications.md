---
title: "Az Azure Active Directory jelentéskészítési értesítései"
description: "Hogyan használható az Azure Active Directory reporting gyanús bejelentkezési értesítéseket moduljainak."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 7aacb31f708e8c3221a5b8cf4223c65160ccb019
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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
Ha a hivatkozásra kattint, a klasszikus Azure portálon belül a jelentés oldalra irányítja. A jelentés eléréséhez kell lennie mindkét:

* Rendszergazdaként vagy az Azure-előfizetéshez társadminisztrátornak
* A címtár globális rendszergazdája és az Active Directory Premium licenc hozzárendelése. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

## <a name="can-i-turn-off-these-emails"></a>Lehet kikapcsolni az e-maileket?
Igen, a rendellenes bejelentkezések a klasszikus Azure portálon belül kapcsolatos értesítések kikapcsolásához kattintson **konfigurálása**, majd válassza ki **letiltott** alatt a **értesítések** szakasz.

## <a name="whats-next"></a>A következő lépések
* Fejezetét milyen biztonsági, naplózási és tevékenységjelentéseket állnak rendelkezésre? Tekintse meg [az Azure AD biztonsági, naplózási és tevékenységjelentéseket](active-directory-view-access-usage-reports.md)
* [Bevezetés a Prémium szintű Azure Active Directory használatába](active-directory-get-started-premium.md)
* [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon](customize-branding.md)

