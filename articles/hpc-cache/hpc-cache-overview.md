---
title: Az Azure HPC cache előzetes verziójának áttekintése
description: Leírja az Azure HPC cache-t, amely egy fájl-hozzáférési gyorsító megoldás a nagy teljesítményű számítástechnika számára
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/19/2019
ms.author: v-erkell
ms.openlocfilehash: f4c858d7a71cf02b4a8fe181deecbf3a1b652885
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180684"
---
# <a name="what-is-azure-hpc-cache-preview"></a>Mi az az Azure HPC Cache? (Előzetes verzió)

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

## <a name="preview-availability"></a>Előzetes verzió elérhetősége

Az Azure HPC cache nyilvános előzetes verziója korlátozott a szolgáltatás minőségének biztosítása érdekében. Hozzáférés kérése az [űrlap](https://aka.ms/onboard-hpc-cache)kitöltésével. Miután hozzáadta az előfizetést a hozzáférési listához, létrehozhat tesztelési gyorsítótárat.

## <a name="next-steps"></a>További lépések

* A képességeivel kapcsolatos további információkért olvassa el az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache)
* További tudnivalók a termékek [előfeltételeiről](hpc-cache-prereqs.md)
* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md) a Azure Portal
