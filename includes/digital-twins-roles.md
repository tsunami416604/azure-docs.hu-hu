---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044941"
---
Az alábbi táblázat ismerteti az Azure Digital Twins ben elérhető szerepköröket:

| **Szerepkör** | **Leírás** | **Azonosító** |
| --- | --- | --- |
| Helykezelő | *CREATE,* *READ*, *UPDATE*és *DELETE* engedély a megadott területhez és az alatta lévő összes csomóponthoz. Globális engedély. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Felhasználói rendszergazda| *CREATE,* *READ*, *UPDATE*és *DELETE* engedélyek a felhasználók és a felhasználókkal kapcsolatos objektumok számára. *OLVASSA EL* A szóközökre vonatkozó engedélyt. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Eszközrendszergazda | *CREATE,* READ , UPDATE és DELETE engedélyek létrehozása, *OLVASÁS,* *FRISSÍTÉS*és *TÖRLÉS* az eszközökhöz és az eszközökhöz kapcsolódó objektumokhoz. *OLVASSA EL* A szóközökre vonatkozó engedélyt. | 3cdfde07-bc16-40d9-ágy3-66d49a8f52ae |
| Kulcskezelő | *CREATE*, *READ*, *UPDATE*és *DELETE* engedély a hozzáférési kulcsokhoz. *OLVASSA EL* A szóközökre vonatkozó engedélyt. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token rendszergazda |  *Olvassa el* *és frissítse* a hozzáférési kulcsokhoz szükséges engedélyt. *OLVASSA EL* A szóközökre vonatkozó engedélyt. | 38a3bb21-5424-43b4-b0bf-78ee2228840c3 |
| Felhasználó |  *OLVASSA el* a szóközök, érzékelők és felhasználók engedélyét, amely tartalmazza a hozzájuk tartozó objektumokat. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Támogatási szakember |  *OLVASSA el* az engedélyt mindenhez, kivéve a hozzáférési kulcsokat. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Eszköztelepítő | *OLVASSA el* és *frissítse* az eszközöket és érzékelőket, amelyek tartalmazzák a hozzájuk tartozó objektumokat. *OLVASSA EL* A szóközökre vonatkozó engedélyt. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Átjáró eszköz | *Hozzon létre* engedélyt az érzékelőkhöz. *OLVASSA el* az eszközöket és érzékelőket, amelyek tartalmazzák a hozzájuk tartozó objektumokat. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |