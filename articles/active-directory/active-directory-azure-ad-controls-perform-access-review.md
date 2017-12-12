---
title: "Tekintse át a hozzáférést az Azure AD használatával értékelést eléréséhez |} Microsoft Docs"
description: "Útmutató: Azure Active Directory hozzáférési értékelést használatával tekintse át a hozzáférést."
services: active-directory
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 98658a2b9fdd0ede98b3f6c10a19af527a8677cc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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



