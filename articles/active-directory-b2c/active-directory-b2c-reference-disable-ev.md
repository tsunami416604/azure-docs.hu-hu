---
title: Regisztráció az Azure Active Directory B2C fogyasztói során e-mailes ellenőrzés letiltása |} A Microsoft Docs
description: A témakör bemutatja, hogyan lehet során az Azure Active Directory B2C-előfizetés felhasználói e-mailes ellenőrzés letiltása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c1bebe46832226e822d9eeb002cb555b72a1d7fa
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441607"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Az Azure Active Directory B2C: Disable e-mailes ellenőrzés során a felhasználói regisztráció
Ha engedélyezve van, a Azure Active Directory (Azure AD) B2C egy e-mail-címet biztosít, és hozzon létre egy helyi fiók alkalmazások regisztráció lehetővé teszi egy fogyasztó. Az Azure AD B2C biztosítja, hogy érvényes e-mail-címek azzal, hogy a felhasználó számára, hogy a regisztrációs folyamat során ellenőrizni őket. Azt is megakadályozza, hogy egy rosszindulatú automatikus folyamat azon alkalmazások hamis fiókok létrehozása.

Egyes alkalmazások fejlesztői inkább a regisztrációs folyamat során az e-mail-ellenőrzés kihagyása és ehelyett a fogyasztók Ellenőrizze később, az e-mail-cím. Ehhez az Azure AD B2C-vel beállítható úgy, hogy e-mailes ellenőrzés letiltása. Ezzel létrehozza az egyenletesebb regisztrációs folyamaton, és rugalmasságot biztosít a fejlesztők a különbséget tenni a felhasználóknak az e-mail-címükkel, ezek a felhasználók, amelyek még nem ellenőrizte.

Alapértelmezés szerint a regisztrálási szabályzatok rendelkezik e-mailes ellenőrzés engedélyezve van. A következő lépések segítségével kikapcsolásához:

1. [A B2C funkciók panelje az Azure Portalon lépjen a következő lépésekkel](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **regisztrálási szabályzatok** vagy **regisztrálási vagy bejelentkezési szabályzatok** a beállításoktól függően-előfizetés.
3. Kattintson a szabályzatra (például "B2C_1_SiUp") való megnyitásához. Kattintson a **szerkesztése** a panel tetején.
4. Kattintson a **oldal-UI testreszabása**.
5. Kattintson a **helyi fiók regisztrálási oldala**.
6. Kattintson a **E-mail cím** a a **neve** oszlop alatt a **regisztrálási attribútumok** szakaszban.
7. Váltás a **követelhet meg** beállítást **nem**.
8. Kattintson a **OK** alján, amíg el nem éri a **házirend szerkesztése** panelen.
9. Kattintson a **mentése** a panel tetején. Kész!

> [!NOTE]
> A regisztrációs folyamat az e-mailes ellenőrzés letiltása fogjuk kéretlen információk küldésére vezethet. Ha letiltja az alapértelmezett, ajánlott felvenni a saját ellenőrzési rendszerétől.
> 
> 

Mindig tudjuk nyissa meg a vélemények és tanácsok! Ha ez a témakör a kérdése van, vagy javaslatok tartalmat, örömmel visszajelzését a lap alján. A szolgáltatással kapcsolatos kéréseit, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
