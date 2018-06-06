---
title: Tiltsa le az e-mailek ellenőrzése során az Azure Active Directory B2C-előfizetési fogyasztói |} Microsoft Docs
description: A témakör bemutatja, hogyan lehet letiltani az e-mail ellenőrzése során az Azure Active Directory B2C-előfizetési fogyasztói.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 24242054ea4af3797fbeca1ed48bb698e4f4296b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712011"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Az Azure Active Directory B2C: Letiltása e-mail ellenőrzése felhasználói regisztráció során
Ha engedélyezve van, Azure Active Directory (Azure AD) B2C segítségével ügyféllel az előfizetés az alkalmazások megadása az e-mail címet és egy helyi fiók létrehozása. Az Azure AD B2C azzal, hogy a fogyasztók ellenőrzése őket a regisztráció során érvényes e-mail címet biztosítja. Is megakadályozza, hogy egy rosszindulatú automatikus folyamat alkalmazások hamis fiókok létrehozása.

Néhány alkalmazásfejlesztők inkább a regisztrációs folyamat során e-mail-ellenőrzés kihagyása és helyette a fogyasztók később ellenőrizze e-mail címét. Ennek támogatásához az Azure AD B2C beállítható úgy, hogy letiltja az e-mailek ellenőrzése. Ezzel létrehoz egy gördülékenyebb bejelentkezési folyamatot, és lehetőséget nyújt a fejlesztőknek különbséget tenni a felhasználóknak az ellenőrizte az e-mail címüket a fogyasztók, amelyek nem rendelkeznek a rugalmasságot.

Alapértelmezés szerint előfizetési házirendek rendelkeznek-e kapcsolva e-mail ellenőrzése. Az alábbi lépések segítségével kapcsolja ki:

1. [Kövesse az alábbi lépéseket az Azure portálon a B2C funkciók panelje navigáljon](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **előfizetési házirendek** vagy **regisztráció vagy bejelentkezés házirendek** attól függően, hogy a konfigurált előfizetési.
3. Kattintson a házirendre (például "B2C_1_SiUp") való megnyitásához. Kattintson a **szerkesztése** a panel tetején.
4. Kattintson a **lap felhasználói felületének testreszabása**.
5. Kattintson a **helyi fiók előfizetéshez**.
6. Kattintson a **E-mail cím** a a **neve** alatti a **regisztrációs attribútumokat** szakasz.
7. Váltás a **ellenőrzés megkövetelése** lehetőséggel **nem**.
8. Kattintson a **OK** alján, amíg el nem éri a **házirend szerkesztése** panelen.
9. Kattintson a **mentése** a panel tetején. Elkészült!

> [!NOTE]
> A regisztrációs folyamat e-mail ellenőrzése letiltása vezethet a levélszemét. Ha letiltja az alapértelmezett, ajánlott hozzáadása a saját ellenőrzési rendszerétől.
> 
> 

Azt mindig nyitva a visszajelzések és tanácsok! Ha bármilyen nehézségbe ütközik az ebben a témakörben, vagy az ehhez a tartalomhoz javítására állnak, azt fogadjuk visszajelzéseit az oldal alján. A szolgáltatás kéréseket, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
