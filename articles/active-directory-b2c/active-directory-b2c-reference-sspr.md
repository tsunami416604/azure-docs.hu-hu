---
title: "Az Azure Active Directory B2C: Az önkiszolgáló jelszó-átállítási |} Microsoft Docs"
description: "A témakör bemutatja, hogyan lehet az önkiszolgáló jelszó-visszaállítást a felhasználók az Azure Active Directory B2C beállítása"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: beaf7dc6260db7509b2202c7801bcc0d2dd2c69e
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Az Azure Active Directory B2C: Az önkiszolgáló jelszó-visszaállítást a felhasználók beállítása
Az önkiszolgáló jelszó-visszaállítási szolgáltatással (akiknek regisztráltak-e helyi fiókok esetében) a felhasználók alaphelyzetbe állíthatja a saját maguk a jelszavukat. Ez jelentősen csökkenti a jelentős terhet róhat a támogató személyzete számára, különösen akkor, ha az alkalmazás rendelkezik több millió fogyasztók rendszeresen használja azt. Jelenleg csak támogatjuk egy helyreállítási módszer az egy ellenőrzött és érvényes e-mail címet. A jövőben további helyreállítási módszerek (ellenőrzött telefonszám, a biztonsági kérdések stb.) adunk hozzá.

> [!NOTE]
> Ez a cikk vonatkozik az önkiszolgáló jelszó alaphelyzetbe állítása a bejelentkezési házirend környezetben használják. Ha módosítania kell meghívni a az alkalmazás teljes mértékben testreszabható jelszó alaphelyzetbe állítása házirendek, lásd: [Ez a cikk](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Alapértelmezés szerint a címtárban nincs önkiszolgáló jelszó-visszaállítási-e kapcsolva. Az alábbi lépések segítségével kapcsolja be:

1. Jelentkezzen be a [klasszikus Azure-portálra](https://manage.windowsazure.com/) előfizetés-rendszergazdaként. Ez az ugyanaz a munkahelyi vagy iskolai fiók vagy a Microsoft-fiók, mint amellyel a címtárban.
2. Lépjen a bal oldalon található navigációs sávban az Active Directory bővítményre.
3. A könyvtár alatt található a **Directory** fülre, és kattintson rá.
4. Kattintson a **Configure** (Konfigurálás) lapra.
5. Görgessen le a **felhasználói jelszó-visszaállítási házirend** szakasz és váltása a **jelszó-visszaállításhoz engedélyezett felhasználók** lehetőséggel **Igen**. Figyelje meg, hogy a **másodlagos E-mail-cím** beállítást; hagyja, mert az.
   
    ![Új jelszó önkiszolgáló kérése](./media/active-directory-b2c-reference-sspr/sspr.png)
6. Kattintson a lap alján található **Mentés** gombra. Elkészült!

Teszteléséhez használja a "Futtatás most" a szolgáltatás bármely bejelentkezési házirend, amely rendelkezik a helyi fiókok identitás-szolgáltatóként. A helyi fiók bejelentkezési a lapon (ha ad meg egy e-mail címet és jelszót, vagy felhasználónév és jelszó), kattintson **nem fér hozzá a fiókjához?** ellenőrizheti a felhasználói élmény.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítási lapok használatával testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md).
> 
> 

