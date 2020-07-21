---
title: Az Azure HPC cache összesített névterének használata
description: Az Azure HPC-gyorsítótár virtuális névterének megtervezése
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: c16d2f9e9c94603361d9a096f33d559105f2d28d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497029"
---
# <a name="plan-the-aggregated-namespace"></a>Az aggregált névtér tervezése

Az Azure HPC cache lehetővé teszi, hogy az ügyfelek különböző tárolási rendszerekhez férhessenek hozzá egy virtuális névtéren keresztül, amely elrejti a háttérrendszer részletes adatait.

Tárolási cél hozzáadásakor az ügyféloldali fájl elérési útját kell beállítania. Az ügyfélszámítógépek a fájl elérési útját csatlakoztatják, és a tárolórendszer közvetlen csatlakoztatása helyett a fájl olvasási kérelmét is elvégezhetik a gyorsítótárban.

Mivel az Azure HPC cache kezeli ezt a virtuális fájlrendszert, a tárolási célt úgy módosíthatja, hogy az ügyfél felé irányuló elérési utat módosítaná. Például lecserélheti a hardveres tárolót a felhőalapú tárolóra anélkül, hogy újra kellene írnia az ügyfelekre irányuló eljárásokat.

## <a name="aggregated-namespace-example"></a>Példa aggregált névtérre

Tervezze meg az összesített névteret, hogy az ügyfélszámítógépek kényelmesen el tudják érni a szükséges információkat, így a rendszergazdák és a munkafolyamat-mérnökök könnyedén azonosíthatják az elérési utakat.

Vegyünk például egy olyan rendszerrendszert, amelyben az Azure-blobban tárolt adatfeldolgozás során egy Azure HPC cache-példány használatos. Az elemzéshez a helyszíni adatközpontokban tárolt sablonfájlok szükségesek.

A sablon adatai egy adatközpontban tárolódnak, és a feladathoz szükséges információk a következő alkönyvtárakban tárolódnak:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Az adatközpont tárolási rendszere a következő exportálásokat teszi elérhetővé:

* */*
* */goldline*
* */goldline/templates*

Az elemezni kívánt adattípust a rendszer átmásolta egy "sourcecollectionnek" nevű Azure Blob Storage-tárolóba a [CLFSLoad segédprogram](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)használatával.

A gyorsítótáron keresztüli egyszerű hozzáférés engedélyezéséhez érdemes lehet tárolási célokat létrehozni a következő virtuális névtér elérési útjaival:

| Háttérrendszer tárolási rendszere <br/> (NFS-fájl elérési útja vagy blob-tároló) | Virtuális névtér elérési útja |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollectionnek                        | /Source               |

Egy NFS-tárolási cél több virtuális névtér elérési úttal is rendelkezhet, feltéve, hogy mindegyik egy egyedi exportálási útvonalra hivatkozik.

Mivel az NFS-forrás elérési útjai azonos exportálási alkönyvtárak, meg kell határoznia több névtér elérési útját is ugyanabból a tárolási céltól.

| Tárolási cél állomásneve  | NFS-exportálási útvonal     | Alkönyvtár elérési útja | Névtér elérési útja    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP-cím vagy állomásnév* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP-cím vagy állomásnév* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Egy ügyfélalkalmazás csatlakoztathatja a gyorsítótárat, és egyszerűen elérheti az összesített névtér elérési útját, ``/source`` ``/templates/sku798`` és ``/templates/sku980`` .

## <a name="next-steps"></a>Következő lépések

Miután eldöntötte, hogyan állíthatja be a virtuális fájlrendszert, [hozzon létre tárolási célokat](hpc-cache-add-storage.md) a háttérbeli tároló leképezéséhez az ügyfélre irányuló virtuális fájlok elérési útjaihoz.
