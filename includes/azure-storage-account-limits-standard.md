---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79382191"
---
Az alábbi táblázat az Azure általános célú v1- és v2-, a Blob Storage- és BlockBlobStorage-fiókok, illetve a Data Lake Storage Gen2-kompatibilis tárfiókok alapértelmezett korlátait ismerteti. A *bejövő forgalom* korlátja a tárfióknak elküldött összes adatra vonatkozik. A *kimenő forgalom* korlátja a tárfióktól fogadott összes adatra vonatkozik.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A tárfiókok száma régiónként és előfizetésenként, beleértve a standard, a prémium és a Data Lake Storage Gen2-kompatibilis tárfiókokat is.<sup>3</sup> | 250 |
| Tárfiók maximális kapacitása | 5 PiB <sup>1</sup>|
| Blobtárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy üzenetek maximális száma tárfiókonként | Korlátlan |
| Maximális kérelemmennyiség<sup>1</sup> tárfiókonként | 20 000 kérelem másodpercenként |
| Maximális bejövő adatforgalom<sup>1</sup> tárfiókonként (az USA és Európa régióiban) | 25 Gb/s |
| Maximális bejövő adatforgalom<sup>1</sup> tárfiókonként (az USA-n és Európán kívül) | 5 Gb/s, ha az RA-GRS/GRS engedélyezve van; 10 Gb/s LRS/ZRS esetén<sup>2</sup> |
| Maximális kimenő forgalom általános célú v2- és Blob Storage-fiókok esetén (minden régióban) | 50 Gb/s |
| Maximális kimenő forgalom általános célú v1-tárfiókok esetén (az USA régióiban) | 20 Gb/s, ha az RA-GRS/GRS engedélyezve van; 30 Gb/s LRS/ZRS esetén<sup>2</sup> |
| Maximális kimenő forgalom általános célú v1-tárfiókok esetén (az USA-n kívüli régiókban) | 10 Gb/s, ha az RA-GRS/GRS engedélyezve van; 15 Gb/s LRS/ZRS esetén<sup>2</sup> |
| Virtuális hálózati szabályok maximális száma tárfiókonként | 200 |
| IP-cím-szabályok maximális száma tárfiókonként | 200 |

<sup>1</sup> Az Azure Storage standard fiókjai külön kérésre magasabb kapacitási és a bejövő forgalomra vonatozó korlátokat biztosítanak. Ha kérni szeretné a fiókjára vonatkozó korlátok növelését, lépjen kapcsolatba az [Azure ügyfélszolgálatával](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Ha a tárfiókja olvasási hozzáféréssel rendelkezik és georedundáns (RA-GRS) vagy földrajzizóna-redundáns (RA-GZRS) tárolást használ, akkor a másodlagos hely kimenő forgalmának céljai megegyeznek az elsődleges hely céljaival. Az [Azure Storage replikálási](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségek a következők:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> Az [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) az Azure Blob Storage-ra épülő big data-elemzésekhez dedikált képességek gyűjteménye. Az Azure Storage és a Blob Storage korlátozásai a Data Lake Storage Gen2-re is érvényesek.

> [!NOTE]
> A Microsoft a legtöbb esetben az általános célú v2-tárfiókok használatát javasolja. Az általános célú v1- vagy az Azure Blob Storage-fiókok egyszerűen frissíthetők általános célú v2-fiókra, leállás és az adatok másolása nélkül. További információk: [Frissítés általános célú v2-tárfiókra](../articles/storage/common/storage-account-upgrade.md).

Ha az alkalmazás igényei meghaladják egyetlen tárfiók skálázhatósági céljait, akkor az alkalmazás úgy is felépíthető, hogy több tárfiókot használjon. Ilyenkor az adatobjektumok particionálhatók a tárfiókok között. További információkat a mennyiségi díjszabásról [Az Azure Storage díjszabásának áttekintése](https://azure.microsoft.com/pricing/details/storage/) oldalon talál.

Az összes Storage-fiók egy egyszintű hálózati topológián fut, függetlenül attól, hogy mikor lettek létrehozva. További információk az Azure Storage egyszintű hálózati architektúrájáról és a skálázhatóságról: [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) (Egy magas rendelkezésre állású, felhőalapú szolgáltatás erős konzisztenciával). 
