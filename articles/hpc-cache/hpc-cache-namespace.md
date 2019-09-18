---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC cache-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036843"
---
# <a name="configure-aggregated-namespace"></a>Összesített névtér konfigurálása
<!-- change link in GUI -->

Az Azure HPC cache lehetővé teszi, hogy az ügyfelek különböző tárolási rendszerekhez férhessenek hozzá egy virtuális névtéren keresztül, amely elrejti a háttérrendszer részletes adatait.

Tárolási cél hozzáadásakor beállíthatja az ügyfél felé irányuló filepath. Az ügyfélszámítógépek csatlakoztatják ezt a filepath. Módosíthatja az ehhez az elérési úthoz társított tárolási célt. Például lecserélheti a hardveres tárolót a felhőalapú tárolóra anélkül, hogy újra kellene írnia az ügyfelekre irányuló eljárásokat.

## <a name="aggregated-namespace-example"></a>Példa aggregált névtérre

Tervezze meg az összesített névteret, hogy az ügyfélszámítógépek kényelmesen el tudják érni a szükséges információkat, és a rendszergazdák és a munkafolyamat-mérnökök könnyedén azonosíthatják az elérési utakat.

Vegyünk például egy olyan rendszerrendszert, amelyben az Azure-blobban tárolt adatfeldolgozás során egy Azure HPC cache-példány használatos. Az elemzéshez a helyszíni adatközpontokban tárolt sablonfájlok szükségesek.

A sablon adatai egy adatközpontban tárolódnak, és a feladathoz szükséges információk a következő alkönyvtárakban tárolódnak:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Az adatközpont tárolási rendszere a következő exportálásokat teszi elérhetővé: 

    /
    /goldline
    /goldline/templates

Az elemezni kívánt adattípust a rendszer átmásolta egy "sourcecollectionnek" nevű Azure Blob Storage-tárolóba a [CLFSLoad segédprogram](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)használatával.

A gyorsítótáron keresztüli egyszerű hozzáférés engedélyezéséhez érdemes lehet tárolási célokat létrehozni a következő virtuális névtér elérési útjaival:

| Háttérbeli NFS-filepath vagy blob-tároló | Virtuális névtér elérési útja |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollectionnek                        | /Source               |

Mivel az NFS-forrás elérési útjai azonos exportálási alkönyvtárak, meg kell határoznia több névtér elérési útját is ugyanabból a tárolási céltól. 

| Tárolási cél állomásneve  | NFS-exportálási útvonal      | Alkönyvtár elérési útja | Névtér elérési útja    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-cím vagy állomásnév* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP-cím vagy állomásnév* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Egy ügyfélalkalmazás csatlakoztathatja a gyorsítótárat, és egyszerűen elérheti az összesített névtér filepaths-/Source,/templates/sku798 és/templates/sku980.

## <a name="next-steps"></a>További lépések

Miután eldöntötte, hogyan állíthatja be a virtuális fájlrendszert, [hozzon létre tárolási célokat](hpc-cache-add-storage.md) a háttérbeli tárolónak az ügyfél felé irányuló virtuális filepaths való leképezéséhez.
