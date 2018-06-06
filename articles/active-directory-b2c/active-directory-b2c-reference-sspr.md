---
title: Az önkiszolgáló jelszó-változtatási az Azure Active Directory B2C |} Microsoft Docs
description: Bemutatja, hogyan állíthat be az önkiszolgáló jelszó-változtatási az ügyfeleknek az Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ea8b23618b382f557340643afd62e56932bbfb2d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712096"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Állítsa be az önkiszolgáló jelszó-visszaállítás, az ügyfelek számára
Az önkiszolgáló jelszó-visszaállítási szolgáltatással az ügyfelek, akik regisztráltak-e a helyi fiókok alaphelyzetbe állíthatja a saját maguk a jelszavukat. Ez jelentősen csökkenti a jelentős terhet róhat a támogató személyzete számára, különösen ha az alkalmazás felhasználója azt rendszeresen több millió tartalmaz. Egy hitelesített e-mail cím használatával jelenleg az egyetlen támogatott helyreállítási módszer.

> [!NOTE]
> Ez a cikk vonatkozik az önkiszolgáló jelszó alaphelyzetbe állítása a bejelentkezési házirend környezetben használják. Ha módosítania kell meghívni a az alkalmazás teljes mértékben testreszabható jelszó alaphelyzetbe állítása házirendek, lásd: [Ez a cikk](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Alapértelmezés szerint a címtárban nem rendelkezik önkiszolgáló jelszó-visszaállítási-e kapcsolva. Az alábbi lépések segítségével kapcsolja be:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) előfizetés-rendszergazdaként. Ez az ugyanaz a munkahelyi vagy iskolai fiók vagy a Microsoft-fiók, mint amellyel a címtárban.
2. Nyissa meg **Azure Active Directory** (a bal oldali navigációs sáv).
4. Állítsa be **önkiszolgáló jelszóváltoztatás szolgáltatás engedélyezve** való **összes**. 
5. Kattintson a **mentése** az oldal tetején. Elkészült!

Teszteléséhez használja a "Futtatás most" a szolgáltatás bármely bejelentkezési házirend, amely rendelkezik a helyi fiókok identitás-szolgáltatóként. A helyi fiók bejelentkezési a lapon (ha ad meg egy e-mail címet és jelszót, vagy felhasználónév és jelszó), kattintson **nem fér hozzá a fiókjához?** ellenőrizheti a felhasználói élmény.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítási lapok használatával testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md).
> 
> 

