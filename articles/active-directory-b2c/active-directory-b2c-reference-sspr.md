---
title: "Az Azure Active Directory B2C: Az önkiszolgáló jelszó-átállítási |} Microsoft Docs"
description: "A témakör bemutatja, hogyan lehet az önkiszolgáló jelszó-visszaállítást a felhasználók az Azure Active Directory B2C beállítása"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: c0a10869477647820828b22b6291522be255844d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Az Azure Active Directory B2C: Az önkiszolgáló jelszó-visszaállítást a felhasználók beállítása
Az önkiszolgáló jelszó-visszaállítási szolgáltatással (akiknek regisztráltak-e helyi fiókok esetében) a felhasználók alaphelyzetbe állíthatja a saját maguk a jelszavukat. Ez jelentősen csökkenti a jelentős terhet róhat a támogató személyzete számára, különösen akkor, ha az alkalmazás rendelkezik több millió fogyasztók rendszeresen használja azt. Jelenleg csak támogatjuk egy helyreállítási módszer az egy ellenőrzött és érvényes e-mail címet. A jövőben további helyreállítási módszerek (ellenőrzött telefonszám, a biztonsági kérdések stb.) adunk hozzá.

> [!NOTE]
> Ez a cikk vonatkozik az önkiszolgáló jelszó alaphelyzetbe állítása a bejelentkezési házirend környezetben használják. Ha módosítania kell meghívni a az alkalmazás teljes mértékben testreszabható jelszó alaphelyzetbe állítása házirendek, lásd: [Ez a cikk](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Alapértelmezés szerint a címtárban nincs önkiszolgáló jelszó-visszaállítási-e kapcsolva. Az alábbi lépések segítségével kapcsolja be:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) előfizetés-rendszergazdaként. Ez az ugyanaz a munkahelyi vagy iskolai fiók vagy a Microsoft-fiók, mint amellyel a címtárban.
2. Nyissa meg az Active Directory (a bal oldali navigációs sáv).
3. Válassza ki **tulajdonságok**.
4. Görgessen le a **önkiszolgáló jelszó-visszaállításhoz engedélyezett** szakaszt, és úgy, hogy váltása **összes**. 
5. Kattintson a **mentése** az oldal tetején. Elkészült!

Teszteléséhez használja a "Futtatás most" a szolgáltatás bármely bejelentkezési házirend, amely rendelkezik a helyi fiókok identitás-szolgáltatóként. A helyi fiók bejelentkezési a lapon (ha ad meg egy e-mail címet és jelszót, vagy felhasználónév és jelszó), kattintson **nem fér hozzá a fiókjához?** ellenőrizheti a felhasználói élmény.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítási lapok használatával testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md).
> 
> 

