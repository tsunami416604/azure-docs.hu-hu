---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) az operációs rendszer frissítése |} A Microsoft Docs
description: Hajtsa végre az operációs rendszer frissítése az SAP Hana az Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100754"
---
# <a name="operating-system-upgrade"></a>Operációs rendszer frissítése
Ez a dokumentum ismerteti a részletek a HANA nagyméretű példányok az operációsrendszer-frissítést.

>[!NOTE]
>Az operációs rendszer frissítése a vevők felelőssége, Microsoft üzemeltetéstámogatás is végigvezeti Önt a legfontosabb területek, hogy figyelje, hogy a frissítés során. Azt tervezi, a frissítés előtt nézze át, valamint az operációs rendszer gyártója.

A kiépítés HLI egység időpontjában a Microsoft üzemeltetési csapat az operációs rendszer telepítése. Az idő függvényében, fenntartásához szükséges az operációs rendszer (Példa: Javítás, hangolása, a frissítés stb.) a HLI egységen.

Előtt a operációs rendszerben (például frissítése SP1, SP2-re) nagyobb, forduljon a támogatási jegy megnyitásával kell forduljon a Microsoft Operations csapatához.

A jegy a következők:

* Az HLI előfizetési azonosítóját.
* A kiszolgáló nevét.
* A alkalmazni kíván javítási szintet.
* Azt tervezi, hogy ez a módosítás dátuma. 

Akkor javasoljuk, hogy miatt a rendszer ellenőrzi, hogy a belső vezérlőprogram frissítése szükséges a kiszolgáló panelen lesz, a üzemeltetési csapat kellene kívánatos frissítési időpont előtt legalább egy héttel a jegy megnyitásakor.


A különböző Linux-verziók a különböző SAP HANA-verziók támogatási mátrixa, lásd: [SAP Megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Ismert problémák

Az alábbiakban néhány gyakori ismert problémákat a frissítés során:
- Termékváltozat II. típusú osztályról Termékváltozat az operációs rendszer frissítése után a rendszer eltávolítja a software foundation szoftvert (SFS). Az operációs rendszer frissítése után újra kell telepítenie a kompatibilis SFS.
- Ethernet-illesztők (ENIC és FNIC) vissza lett állítva a régebbi verziót. A frissítés után újra kell telepíteni az illesztőprogramokat kompatibilis verziója.

## <a name="next-steps"></a>További lépések
- Tekintse meg [biztonsági mentési és visszaállítási](hana-overview-high-availability-disaster-recovery.md) az operációs rendszer biztonsági mentési i. típus Termékváltozat osztály.
- Tekintse meg [operációs rendszer biztonsági mentése II. típusú termékváltozatokhoz](os-backup-type-ii-skus.md) típus II Termékváltozat osztályhoz.
