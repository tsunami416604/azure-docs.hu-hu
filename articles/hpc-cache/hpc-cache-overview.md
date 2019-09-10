---
title: Az Azure HPC cache áttekintése
description: Leírja az Azure HPC cache-t, amely egy fájl-hozzáférési gyorsító megoldás a nagy teljesítményű számítástechnika számára
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: d772f66d14e0c0ab5ac73603e8faeb7e31e9891f
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843779"
---
# <a name="what-is-azure-hpc-cache"></a>Mi az az Azure HPC Cache?

Az Azure HPC cache nagy teljesítményű számítástechnikai (HPC) feladatokhoz biztosít hozzáférést az adatokhoz. Az Azure-ban gyorsítótárazott fájlok lehetővé teszik a felhőalapú számítástechnika méretezhetőségét még azon munkafolyamatok esetében is, amelyekben az adatait WAN-kapcsolatokon keresztül tárolják, például a helyi Datacenter hálózati tároló (NAS) környezetében.

Az Azure HPC cache könnyen indítható és figyelhető a Azure Portal. A meglévő NFS-tárolók vagy új blob-tárolók az összesített névtér részévé válhatnak, ami egyszerűvé teszi az ügyfelek hozzáférését, még akkor is, ha megváltoztatja a háttérbeli tároló célját.

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

Egy Kutatóintézet, amely a genomikai elemzési munkafolyamatokat az Azure-ba szeretné irányítani, egyszerűen átválthatja őket az Azure HPC cache használatával. Mivel a gyorsítótár a POSIX-fájlok elérését biztosítja, a meglévő ügyféloldali munkafolyamatot a felhőben is futtathatja módosítások nélkül.

Az Azure HPC cache emellett kihasználható az olyan feladatok hatékonyságának javításához is, mint a másodlagos elemzés, a farmakológiai szimuláció vagy az AI-vezérelt rendszerképek elemzése.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Azure HPC cache segítségével felgyorsíthatja a mennyiségi elemzési számításokat, a kockázatelemzési munkaterheléseket és a Monte Carlo szimulációkat, hogy a pénzügyi szolgáltatások vállalatai jobban betekintést nyerjenek a stratégiai döntések elvégzéséhez.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure HPC cache a következő Azure-régiókban érhető el:

* East US
* USA 2. keleti régiója
* Észak-Európa
* Nyugat-Európa
* Délkelet-Ázsia
* USA nyugati régiója, 2.

A legfrissebb rendelkezésre állási információkért tekintse meg az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache) .

## <a name="next-steps"></a>További lépések

* A képességeivel kapcsolatos további információkért olvassa el az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache)
* További tudnivalók a termékek [előfeltételeiről](hpc-cache-prereqs.md)
* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md) a Azure Portal
