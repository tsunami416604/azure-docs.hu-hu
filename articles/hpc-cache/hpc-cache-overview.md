---
title: Az Azure HPC cache áttekintése
description: Leírja az Azure HPC cache-t, amely egy fájl-hozzáférési gyorsító megoldás a nagy teljesítményű számítástechnika számára
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/03/2020
ms.author: v-erkel
ms.custom: references_regions
ms.openlocfilehash: b51cd938747c1cfa82b397160f077474e176b2f5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91612965"
---
# <a name="what-is-azure-hpc-cache"></a>Mi az az Azure HPC Cache?

Az Azure HPC cache nagy teljesítményű számítástechnikai (HPC) feladatokhoz biztosít hozzáférést az adatokhoz. Az Azure-ban tárolt fájlok gyorsítótárazásával az Azure HPC cache a Felhőbeli számítástechnika méretezhetőségét a meglévő munkafolyamatba helyezi. Ez a szolgáltatás még olyan munkafolyamatok esetében is használható, ahol az adatait WAN-kapcsolatokon keresztül tárolják, például a helyi Datacenter hálózati tároló (NAS) környezetében.

Az Azure HPC cache könnyen indítható és figyelhető a Azure Portal. A meglévő NFS-tárolók vagy új blob-tárolók az összesített névtér részévé válhatnak, ami egyszerűvé teszi az ügyfelek hozzáférését, még akkor is, ha megváltoztatja a háttérbeli tároló célját.

## <a name="overview-video"></a>Áttekintő videó

[![videó miniatűrje: az Azure HPC cache áttekintése – ide kattintva megtekintheti a videó oldalt](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Kattintson a fenti képre az [Azure HPC cache rövid áttekintésének](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)megtekintéséhez.

## <a name="use-cases"></a>Használati esetek

Az Azure HPC cache a következőhöz hasonló munkafolyamatok esetében növeli a termelékenységet:

* Olvasási – nagy mennyiségű fájl-hozzáférési munkafolyamat
* Az NFS-en keresztül elérhető tárolóban, az Azure blobban vagy mindkettőben tárolt adattárolás
* Akár 75 000 CPU-magokból álló számítási farmok

Az Azure HPC cache számos különböző munkafolyamathoz hozzáadható a legkülönbözőbb iparágakban. Minden olyan rendszer, amelyben nagy mennyiségű gépnek kell hozzáférni egy bizonyos méretű fájlhoz, és az alacsony késéssel jár, a szolgáltatás előnyeit élvezheti. Az alábbi fejezetek konkrét példákat biztosítanak.

### <a name="visual-effects-vfx-rendering"></a>Vizuális effektek (VFX) renderelése

A Media és a Entertainment szolgáltatásban az Azure HPC cache felgyorsíthatja az adathozzáférést az időkritikus megjelenítési projektekhez. A VFX renderelési munkafolyamatainak gyakran nagy számú számítási csomópont általi feldolgozást kell végezniük. Ezen munkafolyamatok esetében általában egy helyszíni NAS-környezet található. Az Azure HPC cache képes a felhőben tárolt fájlok gyorsítótárazására a késés csökkentése és az igény szerinti renderelés rugalmasságának növelése érdekében.

### <a name="life-sciences"></a>Élettudományok

Számos élettudományok munkafolyamata kihasználhatja a kibővíthető fájl gyorsítótárazását.

Egy Kutatóintézet, amely a genomikai elemzési munkafolyamatokat az Azure-ba szeretné irányítani, egyszerűen átválthatja őket az Azure HPC cache használatával. Mivel a gyorsítótár a POSIX-fájlok elérését biztosítja, nincs szükség ügyféloldali módosításra a meglévő ügyfél-munkafolyamat felhőben való futtatásához.

Az Azure HPC cache emellett kihasználható az olyan feladatok hatékonyságának javításához is, mint a másodlagos elemzés, a farmakológiai szimuláció vagy az AI-vezérelt rendszerképek elemzése.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Azure HPC cache üzembe helyezésével felgyorsíthatja a mennyiségi elemzési számításokat, a kockázatelemzési munkaterheléseket és a Monte Carlo szimulációkat, hogy a pénzügyi szolgáltatások vállalatai jobban betekintést nyerjenek a stratégiai döntések elvégzésére.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure [globális infrastruktúra-termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) lapon megtudhatja, hol érhető el az Azure HPC cache.

Az [ügyfél által felügyelt kulcsok funkció](customer-keys.md) csak az alábbi Azure-régiókban támogatott:

* USA keleti régiója
* USA déli középső régiója
* USA 2. nyugati régiója
* West Europe
* USA-beli államigazgatás – Arizona
* USA-beli államigazgatás – Virginia

## <a name="next-steps"></a>További lépések

* A képességeivel kapcsolatos további információkért olvassa el az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache)
* További tudnivalók a termékek [előfeltételeiről](hpc-cache-prerequisites.md)
* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md) a Azure Portal
