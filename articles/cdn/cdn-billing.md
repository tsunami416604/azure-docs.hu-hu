---
title: Azure CDN számlázásának ismertetése |} A Microsoft Docs
description: Ez a GYIK az Azure CDN számlázásának működését ismerteti.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735800"
---
# <a name="understanding-azure-cdn-billing"></a>Az Azure CDN számlázásának ismertetése

Ez a GYIK az Azure Content Delivery Network (CDN) tárolt tartalom számlázási struktúráját ismerteti.

## <a name="what-is-a-billing-region"></a>Mi az, hogy egy számlázási régióban?
A számlázási régióban egy földrajzi területen határozza meg, milyen díjjal díjszabásának kézbesítési objektumok az Azure CDN szolgáltatással. A jelenlegi számlázási zónák és a régiók a következők:

- 1. zóna: Észak-Amerika, Európa, Közel-Kelet és Afrika

- Zone 2: (2. zóna) Ázsia Csendes-óceáni (és japán)

- 3. zóna: Dél-Amerika

- 4. zóna: Ausztrália és Új-Zéland

- 5. zóna: India

Jelenléti pontok (POP) régiókkal kapcsolatos információkért lásd: [Azure CDN POP-helyek régiónként](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Mexikói található POP például Észak-amerikai régióban található, és ezért része a 1. zóna. 

További információ az Azure CDN-tarifacsomag: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hogyan számítják ki kézbesítési díjak régióban?
Az Azure CDN számlázási régióban a forráskiszolgálón, a tartalom jusson el a felhasználó helye alapján. A cél (fizikai helyének) az ügyfél nem tekinthető a számlázási régióban.

Például ha Mexikó lévő felhasználó kibocsát egy kérelmet, és a egy Egyesült államokbeli POP társviszony-létesítés vagy felé irányuló forgalom feltételek miatt található kiszolgáló által kiszolgált ezt a kérelmet, a számlázási régióban lesz az Egyesült Államokban.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Mi az Azure CDN a számlázható tranzakciók?
Bármilyen HTTP (S)-kérelmet, amely a CDN naplósorszámig esemény számlázható, amely tartalmazza az összes válasz típusa: sikeres, sikertelen vagy más. Azonban eltérő válaszokat is okoznak a különböző forgalom. Például *304 nem módosított* és más csak fejléc válaszok kis forgalmat hoz létre, mert azok egy kis válasz fejrészét; ehhez hasonlóan a hibaválaszok (például *404 nem található*) számlázhatók de kis költségkezelési miatt a nagyon kicsi válasz hasznos adatban.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Milyen egyéb Azure-költségek kapcsolódnak az Azure CDN használata az?
Néhány az objektumok a forrásként használt szolgáltatásokra vonatkozó használati díjak is az Azure CDN szolgáltatás használata tekintetében. Ezeket a díjakat jellemzően kis teljes CDN használati költségeinek töredékéért.

Az Azure Blob storage, a forrás-tartalomra vonatkozó használ, ha a gyorsítótár kitölti a következő tárolási díjakat is díjak:

- Tényleges GB felhasználva: Az adatforrás-objektumok a tényleges tároló.

- Adatforgalom GB-ban: Töltse ki a CDN-gyorsítótárak át adatok mennyisége.

- Tranzakciók: Szükséges megtöltené a gyorsítótárat.

Azure Storage szolgáltatás számlázásának kapcsolatos további információkért lásd: [ismertetése az Azure Storage Billing – sávszélesség, tranzakciók és a kapacitás](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Ha használ *üzemeltetett szolgáltatás*, díjat számolunk a következő:

- Azure-beli számítási idő: A forrás-kiszolgálóként működő számítási példányokért.

- Azure-beli számítási átvitel: Az adatok a számítási példányok töltse ki az Azure CDN-gyorsítótárak másolja át.

Ha az ügyfél bájttartomány-kérelmek (függetlenül a forrás-szolgáltatás) használ, a következő szempontokat kell figyelembe venni:

- A *bájttartomány-kérelem* a számlázható tranzakciók, a CDN-t. Amikor egy ügyfél kiad egy bájttartomány-kérelmet, a rendszer egy részét (tartomány) az objektum. A CDN fűzi hozzá a kért tartalmat csak részleges része. A részleges választ a számlázható tranzakciók és az átviteli összeget korlátozódik, a tartomány válasz (valamint a fejlécek) méretét.

- Amikor kérelem érkezik egy objektum csak egy részét (bájttartomány-fejléc megadásával), a CDN előfordulhat, hogy olvassa be a teljes objektum saját gyorsítótárába. Ennek eredményeképpen annak ellenére, hogy a CDN-ből a számlázható tranzakciók részleges választ, a forrásból a számlázható tranzakciók is igénybe vehet az objektum teljes méretét.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Mekkora átviteli tevékenység támogatásához a gyorsítótárban történik?
Minden egyes alkalommal, amikor a CDN összes jelenléti Pontjára kell töltse ki a gyorsítótárat, adatbeolvasási kérelmet küld az objektum gyorsítótárba helyezésből parancsra a forrásnak. Ennek eredményeképpen a a forrás minden egyes gyorsítótár-tévesztés a számlázható tranzakciók tekintetében. A gyorsítótár-tévesztések száma több tényezőtől függ:

- Hogyan gyorsítótárazható tartalom van: Ha a tartalom még magas TTL (time-to-live) / lejárati értékeli és gyakran elérni, hogy marad a gyorsítótárban, majd a terhelés túlnyomó többsége népszerű kezeli a CDN-t. Egy tipikus jó gyorsítótár találati arány, jól több mint 90 %-át, tehát, hogy kevesebb mint 10 %-a-ügyfélkérelmek kell térni a forrás, vagy egy gyorsítótár-tévesztés vagy az objektum frissítése.

- Hány csomóponttal kell betölteni az objektum: Minden alkalommal, amikor egy csomópont objektum betölti a forrásból a számlázható tranzakciók tekintetében. Több globális tartalom (több csomópont elérhető), ezért több számlázható tranzakciók eredményez.

- Befolyással élettartam: Nagyobb TTL-objektum, az azt jelenti, kell lennie a forrásból kevésbé gyakran lehívott. Azt is jelenti, ügyfelek, például böngészők, az objektumot, ami csökkentheti a tranzakciók és a CDN hosszabb, képes gyorsítótárazni.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hogyan kezelhetem a költségeket a leghatékonyabban?
A leghosszabb élettartam a tartalom lehet beállítani. 
