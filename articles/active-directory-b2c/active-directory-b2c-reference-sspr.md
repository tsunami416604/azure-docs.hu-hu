---
title: Az önkiszolgáló jelszó-átállítási |} Microsoft Docs
description: Bemutatja, hogyan állíthat be az önkiszolgáló jelszó-változtatási az ügyfeleknek az Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
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

