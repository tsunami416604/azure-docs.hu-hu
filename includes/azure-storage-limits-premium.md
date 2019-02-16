---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331068"
---
Premium storage-fiókok a következő teljesítménycélokat rendelkezik:

| Teljes számla kapacitás | Helyileg redundáns tárfiókok teljes sávszélesség |
| --- | --- | 
| Lemez kapacitása: 35 TB <br>Pillanatkép-kapacitás: 10 TB | Felfelé 50 Gigabit / másodperc a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> tárfiók küldött összes adatot (kérést)

<sup>2</sup> egy tárfiókból fogadott összes adatot (választ)

Ha a premium storage-fiókok nem felügyelt lemezeket használ, és az alkalmazás meghaladja a skálázhatósági célokat, az egy tárfiókban, érdemes migrálása felügyelt lemezekre. Ha nem szeretné migrálása felügyelt lemezekre, hozhat létre az alkalmazás több tárfiók használata. Ezt követően az adatok particionálása ezen a tárfiókon keresztül. Például, ha 51 TB-os lemezek csatolása több virtuális gép között elosztva őket két tárfiókot. 35 TB csak az egyetlen prémium szintű storage-fiókok. Győződjön meg arról, hogy egyetlen premium storage-fiók soha nem rendelkezik több mint 35 TB kiosztott lemezeket.