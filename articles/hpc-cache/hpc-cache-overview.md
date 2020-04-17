---
title: Az Azure HPC-gyorsítótár áttekintése
description: Az Azure HPC cache, egy nagy teljesítményű számítástechnikafájl-gyorsító megoldása
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 084c33874b474fc1789df93e088d3cec4263eac9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536641"
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

| Észak-Amerika      | Európa         | Ázsia            | Ausztrália      |
|--------------------|----------------|-----------------|----------------|
| USA keleti régiója            | Észak-Európa   | Dél-Korea középső régiója   | Kelet-Ausztrália |
| USA 2. keleti régiója          | Nyugat-Európa    | Délkelet-Ázsia  |               |
| USA déli középső régiója | | | |
| USA nyugati régiója, 2.        | | | |

Az [ügyfél által felügyelt kulcsok funkció](customer-keys.md) csak ezekben a régiókban támogatott:

* USA keleti régiója
* USA déli középső régiója
* USA nyugati régiója, 2.

Tekintse meg az [Azure HPC cache termékoldalon](https://azure.microsoft.com/services/hpc-cache) a legfrissebb rendelkezésre állási információkat.

## <a name="next-steps"></a>További lépések

* Olvassa el az [Azure HPC cache termékoldalát,](https://azure.microsoft.com/services/hpc-cache) ha többet szeretne megtudni a képességeiről
* További információ a termék [előfeltételeiről](hpc-cache-prereqs.md)
* [Hozzon létre egy Azure HPC-gyorsítótárat](hpc-cache-create.md) az Azure Portalról
