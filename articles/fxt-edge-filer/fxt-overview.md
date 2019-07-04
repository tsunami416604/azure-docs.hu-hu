---
title: A Microsoft Azure FXT Edge Filer áttekintése
description: Azure FXT Edge Filer hibrid szoftvertároló gyorsítótár, egy aktív archív és a fájl hozzáférési gyorsító megoldást kínál a nagy teljesítményű feldolgozás ismerteti
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542901"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Mi az Azure FXT Edge Filer hibrid szoftvertároló gyorsítótár?

Az Azure FXT Edge Filer egy hibrid tárolási berendezés, amely gyors hozzáférést és aktív archív biztosít a nagy teljesítményű feldolgozási (HPC) feladatokat gyorsítótárazás.

Több adatforráson, akár távolról, akár a felhőben tárolt helyi adatközpontban, és együttműködik. Az Azure FXT Edge Filer változatos tárolórendszerekben biztosíthatja adatokat egy egységes névteret.

Három vagy több FXT Edge Filer hardvereszközök együttesen, egy fürtözött fájlrendszert biztosít a gyorsítótárban. A szükséges hardverek megvásárlásával kapcsolatos részletekért forduljon a Microsoft helyi képviselőjéhez. 

További tudnivalókért olvassa el a termék információk és adatok munkalapon [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Használati esetek

Az Azure FXT Edge Filer növeli a termelékenységet az alábbiakhoz hasonló munkafolyamatok esetén ajánlott használni:

* Fájl olvasási hozzáférés a munkafolyamat 
* NFSv3 vagy SMB2 protokollt
* COMPUTE-farmok 1000, 100 000 Processzormagok

### <a name="nas-optimization-and-scaling"></a>A NAS-optimalizálás és méretezés

Használhatja az Azure FXT Edge Filer gyorsítótár zökkenőmentes meglévő NetApp és a Dell EMC Isilon NAS-rendszerekhez való hozzáférést. Is hozzáadhat az Azure Blob vagy egyéb felhőalapú tárolási méretezési lehetőséget biztosít data access-folyamatok az ügyféloldalon újragyártási nélkül. 

### <a name="wan-caching"></a>A WAN-gyorsítótárazás

Az Azure FXT Edge Filer gyors hozzáférés a kiemelt felhasználók támogatásához szükséges adatok máshol tárolt használható. Hozzáférést biztosít egy központosított data center a biztonsági mentési és más felügyeleti rendszerek megtartva. 

### <a name="active-archive-in-azure-blob"></a>Az Azure-Blobban található aktív archívum

Bontsa ki az adatközpontot, felhőalapú tárolás az Azure FXT Edge Filer hozzáférési pontot. 

## <a name="features"></a>Szolgáltatások 

Hardver két modellben érhető el. 

| Modell | DRAM | NVMe SSD | Hálózati portok | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6 TB | 6 x 25Gb vagy 10Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb vagy 10Gb + 2 x 1 Gb |


## <a name="next-steps"></a>További lépések

* Szeretne részletesebben is megismerkedni az Azure FXT Edge Filer olvassa el a [specifikációk](fxt-specs.md) vagy [telepítési oktatóanyag](fxt-install.md).
* Ismerje meg, hogyan vásárolhatja meg az Azure FXT Edge Filer a [Azure FXT Edge Filer termékoldalán](https://azure.microsoft.com/services/fxt-edge-filer/).