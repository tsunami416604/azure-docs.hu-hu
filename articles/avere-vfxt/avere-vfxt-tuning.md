---
title: Avere vFXT fürt finomhangolás – Azure
description: Az Azure-ban Avere vFXT teljesítmény optimalizálása érdekében egyéni beállításainak áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197437"
---
# <a name="cluster-tuning"></a>Fürt beállítása


A legtöbb vFXT fürt testre szabott teljesítmény beállításait is kihasználhatják. Ezek a beállítások segítenek a fürt működnek a legjobban az adott munkafolyamat, adatkészlet és eszközöket. 

A Testreszabás mellett egy támogatási képviselő kell elvégezni, mert általában magában foglalja, amelyek nem érhetők el a Avere Vezérlőpult szolgáltatások konfigurálása.

Ez a szakasz ismerteti, hogy az egyéni hangolása, amelyek némelyike teheti meg.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Általános optimalizálási lehetőségek

Előfordulhat, hogy ajánlott a módosítások adatkészlet tulajdonságait vagy a munkafolyamat stílus alapján.

* Írási műveltekből a számítási feladatok esetén növeli az alapértelmezett 20 %-os írási gyorsítótár méretét. 
* Ha az adatkészlet magában foglalja a sok kisméretű fájlok, növelje meg a fürt gyorsítótár fájlok számának korlátját. 
* Ha a munkahelyi másolása, illetve az adatok két adattárak közötti mozgatása szerint, állítsa be az adatok áthelyezése használt szálak száma: 
  * Sebesség növelése érdekében növelheti a használt párhuzamos szálak számát.
  * Ha a háttér-tárolási kötet neve túl van terhelve, szüksége lehet a használt párhuzamos szálak számának csökkentéséhez.
* Ha a fürt egy alapvető filer NFSv4 hozzáférés-vezérlési listákat használó adatok gyorsítótárazza, engedélyezze a fájl engedélyezése az ügyfelek adott egyszerűsítésére gyorsítótár hozzáférési mód.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS vagy a felhőbeli átjáró optimalizálási lehetőségek

Cloud NAS vagy átjáró forgatókönyvek esetében (ahol a vFXT fürt egy felhőalapú tárolót NAS-style hozzáférést biztosít) vFXT fürt és a felhőalapú tárolás között magasabb sebességet kihasználásához a képviselőjével javasolhat, például azokat a további beállítások módosítása agresszív adatok leküldése a tárolási kötet a gyorsítótárból:

* A fürt és a storage-tárolót a TCP-kapcsolatok számának növelése
* Csökkentse a fürt közötti kommunikációhoz REST időtúllépési értékét, és ismételje meg a storage hamarabb ír, ha nem azonnal sikeres  
* Növelje meg a szegmens méretét úgy, hogy az egyes háttérrendszerek írási átvitelek szegmens egy 8 MB-os adattömb helyett 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>A felhőbeli tartalékkapacitás vagy hibrid WAN-optimalizálás

A felhőbeli tartalékkapacitás forgatókönyv vagy hibrid tárolási WAN-optimalizálás forgatókönyv (ahol a vFXT fürt biztosít a felhő közötti integráció és a helyszíni hardver tárolási) ezeket a módosításokat hasznos lehet:

* A fürt és az alapvető filer között engedélyezett TCP-kapcsolatok számának növelése
* Engedélyezze a WAN-optimalizálás a beállítást a távoli core filer (Ez a beállítás használható egy távoli helyszíni filer vagy a felhő alapvető filer egy másik Azure-régióban.)
* A TCP szoftvercsatorna-puffer méretét (a számítási feladatok és a teljesítményigények kielégítése) függően
* Engedélyezze a "mindig előre" beállítást (a számítási feladatok és a teljesítményigények kielégítése) függően redundantly gyorsítótárazott fájlok csökkentése érdekében

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Súgó a Avere vFXT optimalizálása az Azure-hoz

Az ismertetett eljárással [segítség a rendszer a](avere-vfxt-open-ticket.md) , forduljon a támogatási csapattal, ezek az optimalizációk kapcsolatban. 