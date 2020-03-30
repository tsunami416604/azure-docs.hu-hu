---
title: Az Azure CDN-számlázás ismertetése | Microsoft dokumentumok
description: Ez a gyIK leírja, hogyan működik az Azure CDN-számlázás.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: magattus
ms.openlocfilehash: e2827a11f4ec2a5c0467c3699cd9990aaf7ae97a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73495487"
---
# <a name="understanding-azure-cdn-billing"></a>Az Azure CDN számlázásának ismertetése

Ez a GYIK az Azure Content Delivery Network (CDN) által üzemeltetett tartalom számlázási struktúráját ismerteti.

## <a name="what-is-a-billing-region"></a>Mi az a számlázási régió?
A számlázási régió egy földrajzi terület, amely annak meghatározására szolgál, hogy milyen díjat számít unk fel az objektumok Azure CDN-ből történő kézbesítése. A jelenlegi számlázási zónák és régióik a következők:

- 1. zóna: Észak-Amerika, Európa, Közel-Kelet és Afrika

- 2. zóna: Ázsia és a csendes-óceáni térség (Japánnal együtt)

- 3. zóna: Dél-Amerika

- 4. zóna: Ausztrália és Új-Zéland

- 5. zóna: India

A jelenléti pont (POP) régióiról az [Azure CDN POP-helyek régiónként című](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)témakörben talál további információt. Például egy Mexikóban található POP az észak-amerikai régióban található, ezért az 1. 

Az Azure CDN-díjszabásáról a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/)című témakörben talál további információt.

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hogyan számítják ki a szállítási költségeket régiónként?
Az Azure CDN számlázási régió a forráskiszolgáló helyétől függ, amely a tartalmat a végfelhasználónak kézbesíti. Az ügyfél célhelye (fizikai helye) nem tekinthető számlázási régiónak.

Ha például egy Mexikóban található felhasználó kérést ad ki, és ezt a kérést egy egyesült államokbeli POP-ban található kiszolgáló a társviszony-létesítés vagy a forgalmi feltételek miatt kiszolgálja, a számlázási régió az Egyesült Államok lesz.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Mi az a számlázható Azure CDN-tranzakció?
A CDN-en lebontó HTTP(S) kérelmek számlázható esemény, amely tartalmazza az összes választípust: sikeres, sikertelen vagy egyéb. A különböző válaszok azonban eltérő forgalmi összegeket eredményezhetnek. Például a *304 Nem módosított* és más csak fejléces válaszok kis forgalmat generálnak, mert kis fejlécválaszt jelentenek; hasonlóképpen, hiba válaszok (például *404 nem található)* számlázható, de merülnek fel egy kis költség, mert az apró válasz hasznos teher.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Milyen egyéb Azure-költségek kapcsolódnak az Azure CDN-használathoz?
Az Azure CDN használata is felmerül bizonyos használati díjak az objektumok eredeteként használt szolgáltatások után. Ezek a költségek általában a teljes CDN-használati költség kis hányadát teszik ki.

Ha az Azure Blob storage-ot használja a tartalom eredeteként, a gyorsítótár-kitöltések ért a következő tárolási díjakat is felszámítja:

- Tényleges EN-GB használt: A tényleges tároló a forrásobjektumok.

- Tranzakciók: Szükség szerint, hogy töltse ki a cache.

- Átvitel GB-ban: A CDN-gyorsítótárak kitöltéséhez továbbított adatok mennyisége.

> [!NOTE]
> 2019 októberétől kezdve, ha a Microsoft Azure CDN-t használ, az Azure-ban üzemeltetett Origins-ből a CDN-POP-okba történő adatátvitel költsége ingyenes. A Verizon Azure CDN és az Akamai Azure CDN-re az alábbiakban ismertetett díjak vonatkoznak.

Az Azure Storage-számlázással kapcsolatos további információkért [lásd: Az Azure Storage-számlázás – sávszélesség, tranzakciók és kapacitás ismertetése.](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)

Ha a *házigazdája szolgáltatásnyújtást*használja, a következő díjakat kell fizetnie:

- Azure számítási idő: A számítási példányok, amelyek az eredetiként működnek.

- Azure-számítási átvitel: Az adatátvitel a számítási példányok, hogy töltse ki az Azure CDN-gyorsítótárak.

Ha az ügyfél bájttartomány-kérelmeket használ (függetlenül a származási szolgáltatástól), a következő szempontok érvényesek:

- A *bájttartományú kérelem* számlázható tranzakció a CDN-en. Amikor egy ügyfél bájttartomány-kérelmet ad ki, ez a kérés az objektum egy részhalmazára (tartományára) szól. A CDN a kért tartalomnak csak egy részével válaszol. Ez a részleges válasz egy számlázható tranzakció, és az átviteli összeg a tartományválasz méretére korlátozódik (plusz fejlécek).

- Ha egy kérelem csak egy objektum egy részére érkezik (bájttartományú fejléc megadásával), a CDN a teljes objektumot bekérheti a gyorsítótárba. Ennek eredményeképpen annak ellenére, hogy a CDN számlázható tranzakciója részleges válaszra szolgál, a kezdőügylet az objektum teljes méretét is magában foglalhatja.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Mennyi átviteli tevékenység történik a gyorsítótár támogatásához?
Minden alkalommal, amikor egy CDN POP-nak ki kell töltenie a gyorsítótárát, kérést küld a gyorsítótárazott objektum forrására. Ennek eredményeképpen az eredet minden gyorsítótár-tévesztésnél számlázható tranzakció keletkezik. A gyorsítótár-tévesztések száma számos tényezőtől függ:

- Hogyan gyorsítótárazható a tartalom: Ha a tartalom magas TTL (time-to-live)/lejárati értékek et, és gyakran érhető el, így továbbra is népszerű a gyorsítótárban, akkor a terhelés túlnyomó többségét a CDN kezeli. Egy tipikus jó gyorsítótár-találati arány jóval meghaladja a 90%-ot, ami azt jelenti, hogy az ügyfélkérelmek kevesebb mint 10%-ának vissza kell térnie az eredeti jelképhez, akár gyorsítótár-tévesztés, akár objektumfrissítés esetén.

- Hány csomópontnak kell betöltenie az objektumot: Minden alkalommal, amikor egy csomópont betölt egy objektumot az eredetiforrásból, számlázható tranzakciót von maga után. Ennek eredményeképpen több globális tartalom (több csomópontról érhető el) több számlázható tranzakciót eredményez.

- TTL hatás: Egy objektum magasabb TTL azt jelenti, hogy ritkábban kell lekérni az eredeti forrásból. Ez azt is jelenti, hogy az ügyfelek, például a böngészők, hosszabb ideig gyorsítótárazhatják az objektumot, ami csökkentheti a tranzakciókat a CDN-re.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Mely originel érkező szolgáltatások jogosultak ingyenes adatátvitelre a Microsoft Azure CDN-jével? 
Ha az alábbi Azure-szolgáltatások egyikét használja CDN-eredetként, akkor nem kell fizetnie az Originről a CDN-POP-okba történő adatátvitelből. 

- Azure Storage
- Azure Media Services
- Azure-alapú virtuális gépek
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App szolgáltatás
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL-adatbázis
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hogyan kezelhetem a leghatékonyabban a költségeimet?
Állítsa be a lehető leghosszabb TTL-t a tartalomon. 
