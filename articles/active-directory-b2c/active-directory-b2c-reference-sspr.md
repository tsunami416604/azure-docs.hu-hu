---
title: Az önkiszolgáló jelszó-visszaállítás, az Azure Active Directory B2C |} A Microsoft Docs
description: Bemutatja, hogyan állítható be az önkiszolgáló jelszó-visszaállítás, az Azure Active Directory B2C az ügyfelei számára
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c9d6a66a9d54c75fbb20775f0ae4de4d22a7d599
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840866"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Állítsa be az önkiszolgáló jelszó-visszaállítás, az ügyfelek számára
Az önkiszolgáló jelszó-visszaállítási szolgáltatással az ügyfelek, akik regisztráltak a helyi fiókok alaphelyzetbe állíthatja a saját jelszavukat. Ez jelentősen csökkenti a a megoldás a támogatási csapat találkozik, különösen ha az alkalmazás ügyfélbázisra rendszeresen használja. Ellenőrzött e-mail cím használata jelenleg az egyetlen támogatott helyreállítási módszer.

> [!NOTE]
> Ez a cikk vonatkozik az önkiszolgáló jelszó alaphelyzetbe állítása a V1 környezetében használt **jelentkezzen be a** felhasználói folyamatot, amely használja **helyi fiókba** Identitásszolgáltatóként. Ha a megoldásportálról indított az alkalmazás teljes mértékben testre szabható jelszó alaphelyzetbe állítása felhasználói folyamatok van szüksége, tekintse meg [Ez a cikk](active-directory-b2c-reference-policies.md#create-a-password-reset-user-flow).
> 
> 

Alapértelmezés szerint a címtárban nem rendelkezik önkiszolgáló jelszó-visszaállítás van kapcsolva. A következő lépések segítségével kapcsolja be:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) előfizetés-rendszergazdaként. Ez a ugyanazzal a munkahelyi vagy iskolai fiókkal vagy a Microsoft-fiók, amely a címtár létrehozásához használt.
2. Nyissa meg **Azure Active Directory** (a bal oldali navigációs sávban).
4. Állítsa be **önkiszolgáló jelszóátállítás engedélyezve** való **összes**. 
5. Kattintson az oldal tetején lévő **Mentés** elemre. Kész!

Ha tesztelni szeretné, a "Futtatás most" funkciót használja minden olyan bejelentkezési felhasználói folyamatot, amely rendelkezik a helyi fiókok identitás-szolgáltatóként. A helyi fiókkal jelentkezzen be a lapon (amelyben ad meg egy e-mail-cím és jelszó vagy felhasználónév és jelszó), kattintson **nem tudja elérni a fiókját?** ellenőrzése a vásárlói élményt.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítás lapok használatával testre szabható a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/fundamentals/customize-branding.md).
> 
> 

