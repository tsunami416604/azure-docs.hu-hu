---
title: Hozzáférés áttekintése az Azure AD hozzáférési felülvizsgálatokkal |} A Microsoft Docs
description: Ismerje meg, hogyan hozzáférés az Azure Active Directory hozzáférési felülvizsgálatok áttekintése.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 09247a430d2ece44c307297838b7f4dc2819d56d
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607870"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Tekintse át a hozzáférés az Azure AD hozzáférési felülvizsgálatokkal

Az Azure Active Directory (Azure AD) egyszerűbbé teszi a vállalatok alkalmazások és a csoportok tagjai való hozzáférés kezelése az Azure AD és más Microsoft Online Services szolgáltatás hozzáférési felülvizsgálatok. Például egy e-mailben kapott, amely rákérdez, hogy tekintse át a hozzáférést egy csoporthoz vagy alkalmazáshoz való hozzáféréssel rendelkező felhasználók tagjai számára a Microsoft. 

## <a name="open-an-access-review"></a>Nyissa meg a hozzáférési felülvizsgálat

Megtekintheti a függőben lévő hozzáférési felülvizsgálatokat, a felülvizsgálat hozzáférés hivatkozásra az e-mailben. A 2018 augusztus-től kezdődően az Azure AD-szerepkörökhöz tartozó e-mail-értesítések egy új megjelenés rendelkezik. Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor egy felhasználót a cégbe felülvizsgálót lehet. 

![E-mailek hozzáférési felülvizsgálat](./media/perform-access-review/new-ar-email.png)

Ha nem rendelkezik az e-mailt, keresse meg a hozzáférési felülvizsgálatok az alábbi lépéseket:

1. Jelentkezzen be a [Azure AD hozzáférési panel](https://myapps.microsoft.com).

2. Válassza ki a felhasználó szimbólum a oldal, amely megjeleníti a szervezet nevét és az alapértelmezett a jobb felső sarkában. Ha egynél több szervezet szerepel a listán, válassza ki a szervezet, amely a hozzáférési felülvizsgálat kért.

3. Ha egy csempe feliratú **hozzáférési felülvizsgálatokkal** van a lapon válassza a jobb oldalán. A csempe nem látható, ha nincsenek hozzáférési felülvizsgálatok végrehajtani az adott szervezet lesznek, és nem kell módosítania jelenleg.

## <a name="fill-out-an-access-review"></a>Töltse ki a hozzáférési felülvizsgálat

Hozzáférési felülvizsgálat válassza ki a listából, amikor az adatgyűjtés kiterjed a felhasználók, akik át kell tekinteni. Láthatja, hogy csak egy név – a saját – Ha a kérés az volt, hogy a saját hozzáférés áttekintése.

A lista minden egyes sorára eldöntheti, hogy jóváhagyja vagy elutasítja a felhasználói hozzáférés-e. Válassza ki a sort, és hagyja jóvá vagy utasítsa el. (Ha a felhasználó nem tudja, adhatja meg, amely túl.)

A bíráló szükség lehet, hogy hagyja jóvá a folyamatos hozzáférés vagy a csoporttagság indoklása adnia.

## <a name="next-steps"></a>További lépések

A felhasználó hozzáférése megtagadva nem törlődnek azonnal. A felülvizsgálat befejezésekor, vagy ha egy rendszergazda leállítja a felülvizsgálatot el kell távolítani. Ha szeretné módosítani a válasz, és hagyja jóvá a korábban letiltott felhasználó vagy egy korábban már jóváhagyott felhasználó hozzáférésének megtagadása, válassza ki a sort, alaphelyzetbe állítása a választ, és válassza ki az új válasz. Ebben a lépésben a hozzáférési felülvizsgálat befejezése teheti meg.



