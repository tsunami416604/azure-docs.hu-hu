---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC-gyorsítótár-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582175"
---
# <a name="plan-the-aggregated-namespace"></a>Az aggregált névtér tervezése

Az Azure HPC cache lehetővé teszi az ügyfelek számára, hogy a különböző tárolórendszerek egy virtuális névtér, amely elrejti a részleteket a háttér-tároló rendszer.

Amikor tárolási célt ad hozzá, beállítja az ügyfél felé néző fájl elérési útját. Az ügyfélgépek csatlakoztatják ezt a fájlelérési utat, és a tárolórendszer közvetlen csatlakoztatása helyett fájlolvasási kérelmeket is elhelyezhetnek a gyorsítótárban.

Mivel az Azure HPC Cache kezeli ezt a virtuális fájlrendszert, módosíthatja a tárolási célt az ügyfél felé néző elérési út módosítása nélkül. Például lecserélhet egy hardvertároló rendszert felhőalapú tárolóra anélkül, hogy újra kellene írnia az ügyfélfelé irányuló eljárásokat.

## <a name="aggregated-namespace-example"></a>Példa összesített névtérre

Az összesített névteret úgy tervezze meg, hogy az ügyfélgépek kényelmesen elérhessék a szükséges információkat, és hogy a rendszergazdák és a munkafolyamat-mérnökök könnyen megkülönböztethessék az elérési utakat.

Vegyünk például egy olyan rendszert, amelyben egy Azure HPC-gyorsítótár-példány t akar az Azure Blobban tárolt adatok feldolgozásához. Az elemzés hez olyan sablonfájlokszükségesek, amelyek egy helyszíni adatközpontban tárolódnak.

A sablonadatok egy adatközpontban tárolódnak, a feladathoz szükséges információk pedig a következő alkönyvtárakban:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Az adatközpont-tároló rendszer a következő exportálásokat teszi elérhetővé:

    /
    /goldline
    /goldline/templates

Az elemzendő adatokat a [CLFSLoad segédprogrammal](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)egy "sourcecollection" nevű Azure Blob-tárolóba másoltuk.

A gyorsítótáron keresztüli könnyű hozzáférés érdekében fontolja meg a tárolócélok létrehozását a virtuális névtér elérési útvonalaival:

| Háttértároló rendszer <br/> (NFS-fájl elérési útja vagy Blob-tároló) | Virtuális névtér elérési útja |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /sablonok/sku980      |
| forrásgyűjtemény                        | /forrás/               |

Az NFS-tárolócél több virtuális névtérelérési úttal is rendelkezhet, feltéve, hogy mindegyik egyedi exportálási útvonalra hivatkozik.

Mivel az NFS-forrásútvonalak azonos exportálású alkönyvtárak, ugyanabból a tárolási célból több névtérútvonalat kell definiálnia.

| Tárolási célállomás neve  | NFS exportálási útvonal      | Alkönyvtár elérési útja | Névtér elérési útja    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-cím vagy állomásnév* | /goldline/sablonok  | acme2017/sku798   | /templates/sku798 |
| *IP-cím vagy állomásnév* | /goldline/sablonok  | acme2017/sku980   | /sablonok/sku980 |

Az ügyfélalkalmazások csatlakoztathatják a gyorsítótárat, és könnyen ``/source``elérhetik az összesített névtérfájl elérési útjait , ``/templates/sku798``és ``/templates/sku980``a.

## <a name="next-steps"></a>További lépések

Miután eldöntötte, hogyan kell beállítani a virtuális fájlrendszert, [hozzon létre tárolási célokat](hpc-cache-add-storage.md) a háttértároló nak az ügyfélfelé néző virtuális fájlelérési utakhoz való hozzárendeléséhez.
