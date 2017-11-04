---
title: "Tekintse át a hozzáférést az Azure AD használatával értékelést eléréséhez |} Microsoft Docs"
description: "Útmutató: Azure Active Directory hozzáférési értékelést használatával tekintse át a hozzáférést."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 9cd11d41c68c29bfcba44673ae6dbd154fc463f6
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Tekintse át az Azure AD hozzáférési értékelést eléréséhez

Azure Active Directory (Azure AD) egyszerűbbé teszi a vállalatok alkalmazások és a csoportok tagjai való hozzáférés kezelése az Azure AD és más Microsoft Online Services access nevezett szolgáltatással ellenőrzi. Lehet, hogy e-mailben kapott, amely rákérdez, hogy tekintse át a hozzáférést egy csoport vagy az alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai a Microsoft. 

## <a name="open-an-access-review"></a>Nyisson meg egy áttekintése

A függőben lévő hozzáférés értékelést, jelölje ki a kapcsolat az e-mailben. Ha még nem rendelkezik az e-mailt, az access értékelést keresheti meg az alábbiak szerint:

1. Jelentkezzen be a [Azure AD hozzáférési panel](https://myapps.microsoft.com).

2. Válassza ki a felhasználói szimbólum a lapon, amely megjeleníti a nevet és az alapértelmezett szervezet jobb felső sarkában. Ha egynél több szervezet szerepel a listán, válassza ki a szervezet által kért egy áttekintése.

3. Ha egy csempére címkézve **értékelést eléréséhez** van a lapon, jelölje be a jobb oldalon. A csempe nem látható, ha nincsenek végrehajtani a szervezet nem hozzáférés értékelést, és jelenleg nincs szükség semmilyen műveletre.

## <a name="fill-out-an-access-review"></a>Töltse ki az áttekintése

Ha egy áttekintése válasszon a listából, megjelenik a felhasználók neveit, akik át kell tekinteni. Láthatja, hogy csak egy név – a saját – Ha a kérelem volt, hogy tekintse át a saját hozzáférés.

A listán szereplő minden egyes sorhoz kapcsolódóan is meghatározható jóváhagyja vagy elutasítja a felhasználó hozzáférése. Válassza ki a, és válassza ki, hogy jóváhagyásához vagy elutasításához. (Ha a felhasználó nem tudja, adhatja meg, hogy túl.)

A felülvizsgáló képes lehet szükség, hogy az adnia jóváhagyása is folyamatos hozzáférést biztosíthasson vagy csoporttagság indokát.

## <a name="next-steps"></a>Következő lépések

A felhasználó hozzáférése tiltott azonnal nincs eltávolítva. A felülvizsgálati befejezésekor, vagy ha egy rendszergazda nem a felülvizsgálati el kell távolítani. Ha a válasz és hagyja jóvá a korábban letiltott felhasználó vagy egy korábban már jóváhagyott felhasználó hozzáférésének megtagadása jelölje ki a sort, a válasz, és válassza ki az új válasz. Ebben a lépésben végezhető el, amíg befejeződik a áttekintése.



