---
title: A Azure NetApp Files teljesítményével kapcsolatos megfontolások | Microsoft Docs
description: A Azure NetApp Files teljesítményével kapcsolatos megfontolásokat ismerteti.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67454138"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményével kapcsolatos szempontok

A kötetek [átviteli sebességére vonatkozó korlátot](azure-netapp-files-service-levels.md) a kötethez rendelt kvóta és a kiválasztott szolgáltatási szint kombinációja határozza meg. Ha Azure NetApp Filesra vonatkozó teljesítménnyel kapcsolatos terveket készít, meg kell ismernie néhány szempontot. 

## <a name="quota-and-throughput"></a>Kvóta és átviteli sebesség  

Az átviteli sebesség korlátja csak a tényleges teljesítmény egyik meghatározó tényezője.  

A tárolási teljesítmény jellemző szempontjai, beleértve az olvasási és írási kombinációt, az átvitel méretét, a véletlenszerű vagy szekvenciális mintákat, és számos más tényező is hozzájárul a teljes teljesítményhez.  

A tesztelés során megfigyelt maximális empirikus teljesítmény 4 500 MiB/s.  A Premium Storage szintjén a 70,31 TiB mennyiségi kvótája olyan átviteli korlátot fog kiépíteni, amely elég magas a teljesítmény eléréséhez.  

Ha az 70,31 TiB-nál nagyobb mennyiségű mennyiségi kvóta kiosztását fontolgatja, további kvóták is hozzárendelhetők a további adattárolási kötetekhez. A hozzáadott kvóta azonban nem eredményez további növekedést a tényleges átviteli sebességben.  

További információért lásd [a Azure NetApp Files teljesítményének teljesítményteszteit](azure-netapp-files-performance-benchmarks.md) .

## <a name="overprovisioning-the-volume-quota"></a>A mennyiségi kvóta túlzott kiépítése

Ha a számítási feladatok teljesítménye a maximális átviteli sebesség, lehetséges, hogy a mennyiségi kvótát a magasabb átviteli sebesség beállítására és a nagyobb teljesítmény elérésére is lehetővé teszi.  

Ha például a Premium Storage-szinten lévő köteten csak 500-es adat van, de 128 MiB/s átviteli sebességre van szükség, beállíthatja a kvótát 2 TiB-ra, hogy az átviteli szint megfelelően legyen beállítva (64 MiB/s/TB * 2 TiB = 128 MiB/s).  

Ha következetesen túlépít egy kötetet a magasabb átviteli sebesség eléréséhez, érdemes inkább magasabb szolgáltatási szintet használni.  A fenti példában ugyanezt az átviteli korlátot a mennyiségi kvóta felé is elérheti az ultra Storage-rétegek használatával (128 MiB/s/TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamikusan növekvő vagy csökkenő mennyiségi kvóta

Ha a teljesítményre vonatkozó követelmények átmeneti jellegűek, vagy ha egy meghatározott időtartamnál nagyobb teljesítményre van szükség, dinamikusan növelheti vagy csökkentheti a mennyiségi kvótát, hogy azonnal módosítsa az átviteli sebességet.  Vegye figyelembe az alábbi szempontokat: 

* A mennyiségi kvóta az IO szüneteltetése nélkül növelhető vagy csökkenthető, és a kötethez való hozzáférés nem szakad meg, vagy nincs hatással rá.  

    A kvótát egy köteten lévő aktív I/O-tranzakció során is módosíthatja.  Vegye figyelembe, hogy a mennyiségi kvóta soha nem csökkenthető a köteten tárolt logikai adatmennyiség alatt.

* A mennyiségi kvóta megváltozásakor az átviteli sebesség korlátjának megfelelő módosítása majdnem azonnali. 

    A módosítás nem szakítja meg a kötet-vagy I/O-hozzáférést.  

* A mennyiségi kvóta módosítása a kapacitási készlet méretének változását igényli.  

    A kapacitási készlet mérete dinamikusan módosítható, és nem befolyásolja a kötetek rendelkezésre állását vagy az I/O-t.

## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Az Azure NetApp Files teljesítményével kapcsolatos mérőszámok](azure-netapp-files-performance-benchmarks.md)