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
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044941"
---
Az alábbi táblázat az Azure Digital Twins szolgáltatásban elérhető szerepköröket ismerteti:

| **Szerepkör** | **Leírás** | **Azonosító** |
| --- | --- | --- |
| Hely rendszergazdája | *Hozzon létre*, *Olvasson*, *frissítsen*és *töröljön* engedélyt a megadott helyhez és az alatta lévő összes csomóponthoz. Globális engedély. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Felhasználói rendszergazda| Engedélyek *létrehozása*, *olvasása*, *frissítése*és *törlése* a felhasználókhoz és a felhasználóhoz kapcsolódó objektumokhoz. *Olvasási* engedély a szóközökhöz. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Eszköz rendszergazdája | Engedélyek *létrehozása*, *olvasása*, *frissítése*és *törlése* az eszközök és az eszközhöz kapcsolódó objektumok számára. *Olvasási* engedély a szóközökhöz. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Kulcs rendszergazdája | Hozzáférési kulcsok *létrehozási*, *olvasási*, *frissítési*és *törlési* engedélye. *Olvasási* engedély a szóközökhöz. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Jogkivonat-rendszergazda |  *Olvasási* és *frissítési* engedély a hozzáférési kulcsokhoz. *Olvasási* engedély a szóközökhöz. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Felhasználó |  *Olvasási* engedély a tárhelyek, érzékelők és felhasználók számára, beleértve a hozzájuk kapcsolódó objektumokat is. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Támogatási szakember |  *Olvasási* engedély mindenhez, kivéve a hozzáférési kulcsokat. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Eszköz telepítője | *Olvasási* és *frissítési* engedély az eszközök és érzékelők számára, beleértve a hozzájuk kapcsolódó objektumokat is. *Olvasási* engedély a szóközökhöz. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Átjáró eszköz | *Hozzon létre* engedélyt az érzékelőkhöz. *Olvasási* engedély az eszközök és érzékelők számára, beleértve a hozzájuk kapcsolódó objektumokat is. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |