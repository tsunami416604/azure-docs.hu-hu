---
title: Az Azure NetApp-fájlok teljesítményével kapcsolatos szempontok | Microsoft dokumentumok
description: Az Azure NetApp-fájlok teljesítményével kapcsolatos szempontok ismertetése.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454138"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményével kapcsolatos szempontok

A kötet [átviteli korlátját](azure-netapp-files-service-levels.md) a kötethez rendelt kvóta és a kiválasztott szolgáltatásszint kombinációja határozza meg. Amikor teljesítményterveket készít az Azure NetApp-fájlokkal kapcsolatban, több szempontot is meg kell értenie. 

## <a name="quota-and-throughput"></a>Kvóta és átviteli-átmenő  

Az átviteli korlát csak egy meghatározó a tényleges teljesítmény, amely realizálva lesz.  

Tipikus tárolási teljesítmény szempontok, beleértve az olvasási és írási mix, az átviteli méret, véletlenszerű vagy szekvenciális minták, és sok más tényező hozzájárul a teljes teljesítmény szállított.  

A vizsgálat során megfigyelt maximális empirikus áteresztőteljesítmény 4500 MiB/s.  A prémium szintű tárolási szinten egy 70,31 TiB-szintű kötetkvóta olyan átviteli korlátot biztosít, amely elég magas ahhoz, hogy elérje ezt a teljesítményszintet.  

Ha a 70,31 TiB-t meghaladó mennyiségi kvótaösszegek hozzárendelését tervezi, további adatok tárolására további kvótát rendelhet egy kötethez. A hozzáadott kvóta azonban nem eredményezi a tényleges átviteli érték további növekedését.  

További információkért lásd [az Azure NetApp-fájlok teljesítményreferenciaértékeit.](azure-netapp-files-performance-benchmarks.md)

## <a name="overprovisioning-the-volume-quota"></a>A mennyiségi kvóta túlterhelése

Ha egy számítási feladat teljesítménye átviteli korláthoz kötött, a kötetkvótát túlépítheti egy magasabb átviteli szint beállításához és nagyobb teljesítmény eléréséhez.  

Ha például a prémium szintű tárolási réteg egy kötete csak 500 GiB adattal rendelkezik, de 128 MiB/s átviteli forgalmat igényel, beállíthatja a kvótát 2 TiB-re, hogy az átviteli szint ennek megfelelően be van állítva (64 MiB/s/TB * 2 TiB = 128 MiB/s).  

Ha következetesen túlegy kötetet a nagyobb átviteli érték elérése érdekében, fontolja meg egy magasabb szolgáltatási szint helyett.  A fenti példában ugyanazt az átviteli korlátot a kötetkvóta felével érheti el az Ultra storage réteg használatával (128 MiB/s tib- onként * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamikusan növekvő vagy csökkenő mennyiségi kvóta

Ha a teljesítménykövetelmények ideiglenes jellegűek, vagy ha egy meghatározott ideig megnövekedett a teljesítményigénye, dinamikusan növelheti vagy csökkentheti a mennyiségi kvótát, hogy azonnal állítsa be az átviteli korlátot.  Vegye figyelembe a következő szempontokat: 

* A kötetkvóta növelhető vagy csökkenthető az IO szüneteltetése nélkül, és a kötethez való hozzáférés nem szakad meg vagy nem befolyásolja.  

    A kvótát egy aktív I/O-tranzakció során egy kötethez igazíthatja.  Vegye figyelembe, hogy a kötetkvóta soha nem csökkenthető a kötetben tárolt logikai adatok mennyisége alá.

* A kötetkvóta módosításakor az átviteli korlát megfelelő változása szinte azonnali. 

    A módosítás nem szakítja meg vagy befolyásolja a kötethez való hozzáférést vagy az I/O-t.  

* A kötetkvóta beállításához módosítani kell a kapacitáskészlet méretét.  

    A kapacitáskészlet mérete dinamikusan és a kötet rendelkezésre állásának vagy az I/O-nak a befolyásolása nélkül állítható be.

## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Az Azure NetApp Files teljesítményével kapcsolatos mérőszámok](azure-netapp-files-performance-benchmarks.md)