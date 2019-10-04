---
title: Teljesítménnyel kapcsolatos megfontolások a NetApp Azure-fájlok |} A Microsoft Docs
description: Teljesítménnyel kapcsolatos Azure NetApp fájlokat ismerteti.
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
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454138"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményével kapcsolatos szempontok

A [átviteli sebességhatár](azure-netapp-files-service-levels.md) a kötet a kötethez hozzárendelt kvóta és a kiválasztott szolgáltatás kombinációja határozza meg. Ha Azure NetApp fájlokkal kapcsolatos teljesítmény tervek, több szempontokat kell. 

## <a name="quota-and-throughput"></a>Kvóta és az átviteli sebesség  

Az átviteli sebesség határértéke csak egy determinánsa a tényleges teljesítmény, a rendszer kell megvalósítani.  

Tipikus storage teljesítményével kapcsolatos megfontolások, beleértve a és olvasási vegyesen, az átviteli méret, véletlenszerű vagy egymást követő minták és sok más tényező hozzájárul a teljes teljesítmény-i.  

A tapasztalati által maximális átviteli sebesség tesztelése során tapasztalt 4,500 MiB-vagy HTTPS.  A prémium szintű storage, a-kötet kvótát 70.31 Tib-ra is üzembe helyezi egy átviteli sebességhatár, amely elég nagy ahhoz, ezt a szintű teljesítmény elérése érdekében.  

Fontolgatja hozzárendelése köteten kvóta összegek túli 70.31 Tib-ra, ha további kvótát egy további adatok tárolására szolgáló kötetet rendelt. A hozzáadott kvóta azonban nem eredményez további tényleges átviteli sebesség növekedése.  

Lásd: [TELJESÍTMÉNYMÉRÉSEK Azure NetApp fájlok](azure-netapp-files-performance-benchmarks.md) további információt.

## <a name="overprovisioning-the-volume-quota"></a>A kötet kvóta túlzott

Ha egy számítási feladat teljesítményére átviteli sebességhatár kötve, is lehet overprovision a köteten kvóta magasabb átviteli sebesség szinten, és nagyobb teljesítmény érdekében.  

Például, ha a kötet a prémium szintű storage csak 500 GB adat rendelkezik, de 128 MiB/s adatátviteli kapacitást igényel, beállíthatja a kvóta 2 Tib-ra, hogy ennek megfelelően van-e állítva az átviteli szinten (64 TB-os gépenként MiB/s * 2 Tib-ra = 128 MiB/s).  

Ha konzisztens módon overprovision egy magasabb átviteli sebesség eléréséhez egy kötetet, fontolja meg egy magasabb szolgáltatási szintre helyette.  A fenti példában, úgy érheti el az azonos átviteli sebességhatár fele a mennyiségi-kvótához használja helyette a Ultra tárolási szint (128 gépenként TiB MiB/s * 1 Tib-ra = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamikusan növekvő vagy csökkenő köteten kvóta

Ha a teljesítményre vonatkozó követelmények átmeneti jellegű, vagy ha egy meghatározott időtartamra vonatkozóan esetében sorozatot növelte, akkor dinamikusan növelheti vagy csökkentheti a köteten kvóta azonnal módosíthatja az átviteli sebességhatár.  Vegye figyelembe az alábbiakat: 

* Kötet kvóta is növelhető vagy csökkenthető, nem kell IO szüneteltetése, és elérni a kötetet nem megszakad vagy negatív.  

    Módosíthatja a kvóta egy kötet elleni i/o aktív tranzakció során.  Vegye figyelembe, hogy a köteten kvóta soha nem alábbi logikai a köteten tárolt adatok mennyisége csökkenthető.

* Kötet kvóta módosul, az átviteli sebességhatár megfelelő módosítása esetén szinte azonnali. 

    A módosítás nem kiszolgálására, és hatással a kötet hozzáférésével vagy i/o.  

* A kapacitás a készlet méretét az módosításának köteten kvóta követel meg.  

    A kapacitás a készlet méretét módosítható dinamikusan és a kötetek rendelkezésre vagy i/o befolyásolása nélkül.

## <a name="next-steps"></a>További lépések

- [Szolgáltatási szintek a NetApp Azure-fájlok](azure-netapp-files-service-levels.md)
- [TELJESÍTMÉNYMÉRÉSEK NetApp Azure-fájlok](azure-netapp-files-performance-benchmarks.md)