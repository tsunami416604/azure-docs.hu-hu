---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597811"
---
Az Azure-fájlmegosztásokban lévő adatok adatvesztés vagy sérülés elleni védelme érdekében az összes Azure-fájlmegosztás az egyes fájlok írásakor több példányt tárol. A számítási feladatok követelményeitől függően további redundanciafokat is kiválaszthat. Az Azure Files jelenleg a következő adatredundancia-beállításokat támogatja:

- **Helyileg redundáns:** Helyileg redundáns tárolás, gyakran LRS, azt jelenti, hogy minden fájl tárolása háromszor egy Azure storage-fürtön belül. Ez védelmet nyújt a hardverhibák, például a hibás lemezmeghajtó miatti adatvesztés ellen.
- **Zóna redundáns:** Zóna redundáns tárolás, gyakran nevezik ZRS, azt jelenti, hogy minden fájl tárolása háromszor három különböző Azure storage-fürtök között. A három különböző Azure storage-fürtök mindegyike tárolja a fájlt háromszor, csakúgy, mint a helyileg redundáns tárolás, és fizikailag elkülönítve vannak a különböző Azure *rendelkezésre állási zónákban.* A rendelkezésre állási zónák egyedi fizikai helyek egy Azure-régióban. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A tárolóba írás nem fogadható el, amíg meg nem írja a tárolófürtök mindhárom rendelkezésre állási zónában. 
- **Georedundáns:** Georedundáns tárolás, gyakran grs, olyan, mint a helyileg redundáns tárolás, az, hogy egy fájlt háromszor tárolnak egy Azure storage-fürtön belül az elsődleges régióban. Az összes írási műveletet ezután aszinkron módon replikálja egy Microsoft által definiált másodlagos régióba. Georedundáns tárolás biztosít 6 másolatot az adatok két Azure-régiók között. Abban az esetben, ha egy súlyos katasztrófa, például egy Azure-régió végleges elvesztése egy természeti katasztrófa vagy más hasonló esemény miatt, a Microsoft feladatátvételt hajt végre, hogy a másodlagos valójában lesz az elsődleges, az összes művelet kiszolgálása. Mivel az elsődleges és másodlagos régiók közötti replikáció aszinkron, súlyos katasztrófa esetén a másodlagos régióba még nem replikált adatok elvesznek. A georedundáns tárfiók manuális feladatátvételt is végrehajthat.
- **Geozóna redundáns**: Geo-zóna redundáns tárolás, gyakran nevezik GZRS, mint a zóna redundáns tárolás, az, hogy egy fájlt kilencszer tárolják a 3 különböző tárolófürtök az elsődleges régióban. Az összes írási műveletet ezután aszinkron módon replikálja egy Microsoft által definiált másodlagos régióba. A geozóna redundáns tárolásának feladatátvételi folyamata ugyanúgy működik, mint a georedundáns tárolás esetében.

A standard Azure-fájlmegosztások mind a négy redundanciatípust támogatják, míg a prémium szintű Azure-fájlmegosztások csak a helyiredundáns és zónaredundáns tárolást támogatják.

Az általános célú 2-es verziójú GPv2-tárfiókok két további redundancialehetőséget biztosítanak, amelyeket az Azure Files nem támogat: gyakran olvasható georedundáns tárolás, gyakran RA-GRS néven és olvasható geozónaredundáns tárolás ra-GZRS-nek nevezik. Ezekkel a beállításokkal azure-fájlmegosztásokat hozhat létre a tárfiókokban, azonban az Azure Files nem támogatja a másodlagos régióból történő olvasást. Az olvasásra telepíthető geo- vagy geozóna redundáns tárfiókokba telepített Azure-fájlmegosztások számlázása georedundáns vagy geozónaredundáns tárolásként lesz számlázva.