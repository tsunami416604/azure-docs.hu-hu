---
title: Az Azure HPC-gyorsítótár áttekintése
description: Az Azure HPC cache, egy nagy teljesítményű számítástechnikafájl-gyorsító megoldása
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241191"
---
# <a name="what-is-azure-hpc-cache"></a>Mi az az Azure HPC Cache?

Az Azure HPC cache felgyorsítja az adatokhoz való hozzáférést a nagy teljesítményű számítástechnikai (HPC) feladatokhoz. Az Azure HPC-gyorsítótárazásával az Azure HPC-gyorsítótár a felhőalapú számítástechnika méretezhetőségét hozza el a meglévő munkafolyamathoz. Ez a szolgáltatás még olyan munkafolyamatokhoz is használható, ahol az adatok at WAN-kapcsolatokon keresztül tárolják, például a helyi adatközponthoz csatlakoztatott hálózati tároló (NAS) környezetben.

Az Azure HPC-gyorsítótár könnyen indítható és figyelhető az Azure Portalon. A meglévő NFS-tárolók vagy az új Blob-tárolók az összesített névtér részévé válhatnak, ami egyszerűvé teszi az ügyfélhozzáférést, még akkor is, ha módosítja a háttértároló-célt.

## <a name="use-cases"></a>Használati esetek

Az Azure HPC cache az alábbihoz hasonló munkafolyamatokhoz a legjobb termelékenységet növeli:

* Olvasási nehéz fájlhozzáférési munkafolyamat
* NFS-en elérhető tárhelyen, Azure Blobban vagy mindkettőben tárolt adatok
* Akár 75 000 CPU-magból álló számítási farmok

Az Azure HPC-gyorsítótár számos iparágban számos munkafolyamathoz adható hozzá. Minden olyan rendszer, ahol nagy számú gépnek kell hozzáférnie egy sor fájlhoz nagy méretekben és alacsony késleltetéssel, élvezheti ezt a szolgáltatást. Az alábbi szakaszok konkrét példákat tartalmaznak.

### <a name="visual-effects-vfx-rendering"></a>Vizuális effektek (VFX) renderelése

A médiában és a szórakoztató iparban az Azure HPC cache felgyorsíthatja az időkritikus megjelenítési projektek adatelérését. A VFX leképezési munkafolyamatai gyakran nagy számú számítási csomóponttal igényelnek last-minute feldolgozást. A munkafolyamatok adatai általában egy helyszíni NAS környezetben találhatók. Az Azure HPC cache gyorsítótárazza a fájladatokat a felhőben a késés csökkentése és az igény szerinti renderelés rugalmasságának növelése érdekében.

### <a name="life-sciences"></a>Élettudományok

Számos élettudományi munkafolyamat számára előnyös lehet a kibővített fájlgyorsítótárazás.

Egy kutatóintézet, amely a genomikai elemzési munkafolyamatait az Azure-ba kívánja portolni, könnyedén áthelyezheti azokat az Azure HPC cache használatával. Mivel a gyorsítótár POSIX fájlhozzáférést biztosít, nincs szükség ügyféloldali módosításokra a meglévő ügyfél-munkafolyamat felhőben való futtatásához.

Az Azure HPC-gyorsítótár is használható a hatékonyság növelése a feladatok, például a másodlagos elemzés, farmakológiai szimuláció, vagy a AI-alapú képelemzés.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Azure HPC-gyorsítótár központi telepítése felgyorsíthatja a kvantitatív elemzési számításokat, a kockázatelemzési munkaterheléseket és a Monte Carlo-szimulációkat, hogy a pénzügyi szolgáltatók jobb betekintést kapjanak a stratégiai döntések meghozatalába.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure HPC-gyorsítótár a következő Azure-régiókban érhető el:

* USA keleti régiója
* USA 2. keleti régiója
* Észak-Európa
* Nyugat-Európa
* Délkelet-Ázsia
* Sydney
* USA nyugati régiója, 2.
* Dél-Korea középső régiója

Tekintse meg az [Azure HPC cache termékoldalon](https://azure.microsoft.com/services/hpc-cache) a legfrissebb rendelkezésre állási információkat.

## <a name="service-availability"></a>A szolgáltatás elérhetősége

Az Azure HPC-gyorsítótárral használni kívánt minden egyes előfizetéshez hozzáférést kell kérnie. Ez a korlátozás segít biztosítani a szolgáltatás minőségét az általános elérhetőség első hónapjaiban.

Kérjen hozzáférést [az űrlap](https://aka.ms/onboard-hpc-cache)kitöltésével. Miután az előfizetést hozzáadta a hozzáférési listához, gyorsítótárakat hozhat létre.

## <a name="next-steps"></a>További lépések

* Olvassa el az [Azure HPC cache termékoldalát,](https://azure.microsoft.com/services/hpc-cache) ha többet szeretne megtudni a képességeiről
* További információ a termék [előfeltételeiről](hpc-cache-prereqs.md)
* [Hozzon létre egy Azure HPC-gyorsítótárat](hpc-cache-create.md) az Azure Portalról
