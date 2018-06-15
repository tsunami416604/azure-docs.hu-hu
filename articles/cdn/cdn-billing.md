---
title: Az Azure CDN számlázási ismertetése |} Microsoft Docs
description: Ez a GYIK az Azure CDN számlázási működését ismerteti.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 218c493c772dc8fd212efaf60a0599fa2e896411
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163517"
---
# <a name="understanding-azure-cdn-billing"></a>Az Azure CDN számlázási ismertetése

Ez a GYIK az Azure tartalom Delivery Network (CDN) által szolgáltatott tartalom számlázási struktúra ismerteti.

## <a name="what-is-a-billing-region"></a>Mi az a számlázási régió?
A számlázási régió határozza meg, milyen mértékű van szó, a kézbesítés objektumok Azure CDN egy földrajzi terület. Az aktuális elszámolási zónák és azok a területek a következők:

- Zóna 1: Észak-Amerikában, Európában, Közel-Kelet és Afrika

- 2. zóna: Ázsia és a csendes-óceáni térség (és Japán)

- 3. zóna: Dél-Amerika

- 4. zóna: Ausztrália és Új-Zéland

- 5. zóna: India

Pont nyújtó jelenléti (POP)-régiók kapcsolatos információkért lásd: [Azure CDN POP-helyek régiónként](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Mexikói található POP például Észak-Amerika régióban van, és ezért része a zóna 1. 

További információ az Azure CDN-díjszabást: [Content Delivery Network árképzési](https://azure.microsoft.com/is-is/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Kézbesítési díjak kiszámítási módját régiónként?
Az Azure CDN számlázási régió végfelhasználóhoz a tartalmat a forráskiszolgáló helyét alapul. A cél (fizikai helyének) az ügyfél nem tekinthető a számlázási régióban.

Például ha mexikói lévő felhasználó kibocsát egy kérelmet, és társviszony-létesítés vagy forgalom feltételek miatt az Amerikai Egyesült Államok POP található kiszolgáló által kiszolgált ehhez a kérelemhez, a számlázási terület lesz az Amerikai Egyesült Államokban.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Mi az Azure CDN számlázható tranzakció?
Bármely, amely a CDN: HTTP (S) kérelme, mert az egy számlázható esemény, és a következő minden válasz: sikeres, sikertelen vagy más. Azonban más válaszok különböző adatforgalmi díjak hozhat létre. Például *304 nem módosított* és más csak a fejlécet válaszok minimális adatforgalmat generálnak, mert azok egy kis válasz fejrészét; ehhez hasonlóan hibaválaszok (például *404 nem található*) számlázható de fel Önnek egy kis költség miatt az apró válasz forgalma.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Milyen egyéb Azure költségek társított Azure CDN szolgáltatás használata?
Azure CDN használatával is terhel néhány használati a szolgáltatásokat az objektumok származási használja. Ezek a költségek jellemzően töredéke CDN használati teljes költség szempontjából.

Azure Blob Storage tárolóban származási a tartalomhoz használ, ha is a következő tárolási költségek gyorsítótár tölti fel Önnek:

- Tényleges GB használt: A tényleges az adatforrás-objektumok tárolására.

- GB-ban átvitelek: Töltse ki a CDN-gyorsítótárak az átvitt adatok mennyisége.

- Tranzakciók: a gyorsítótár kitöltésének igény szerint.

Azure Storage számlázással kapcsolatos további információkért lásd: [az Azure Storage számlázási – sávszélesség, a tranzakciók és a kapacitás](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Ha használ *üzemeltetett szolgáltatás kézbesítési*, lesz függő díj terheli az alábbiak szerint:

- Az Azure számítási idejét: A számítási példányokért, amelyek származási működni.

- Az Azure compute átviteli: a számítási példányokért az Azure CDN gyorsítótárak kitöltésének átviszi az adatokat.

Ha az ügyfél bájttartomány kérelmek (függetlenül a forrás-szolgáltatás) használ, a következők érvényesek:

- A *bájttartomány kérelem* van a CDN számlázható tranzakciót. Amikor egy ügyfél egy bájttartomány kérelmet ad ki, a kérelme, mert az az objektum egy részéhez (tartomány). A CDN válaszol, a kért tartalom csak egy részleges részét. A részleges válasz számlázható tranzakció, ezért az átviteli összeget korlátozódik összege a tartomány válasz (fejlécekkel együtt).

- Amikor egy kérelem érkezik egy objektum csak egy része (bájttartomány fejléc megadásával), a a CDN hívjon le a a teljes objektumot a saját gyorsítótárába. Annak ellenére, hogy a CDN számlázható tranzakció részleges választ, emiatt a forrásból számlázható tranzakció magában foglalhatja az objektum teljes mérete.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Mekkora átviteli tevékenység akkor fordul elő, a gyorsítótár támogatásához?
Minden alkalommal, amikor egy CDN POP-ra kell töltse ki a gyorsítótárat, azt egy kérést küld a forrás az objektum a gyorsítótárba. Ennek eredményeképpen a forrás azt eredményezi azok háromszorosa minden gyorsítótár-tévesztései egy számlázható tranzakciót. A gyorsítótárbeli száma számos tényezőtől függ:

- Hogyan kérelmeznék tartalma: a tartalom még nagy élettartam (idő TTL) / lejárati értékeket, és elérni, annak népszerű gyorsítótár, majd a többsége a terhelés kezeli a CDN-t. Egy tipikus jó gyorsítótár találati arány, jól több mint 90 %-át, tehát, hogy ügyfélkérelmek 10 %-nál kisebb kell térjen vissza az eredeti, vagy egy gyorsítótár-tévesztései vagy objektum frissítése.

- Hány csomópontok az objektum betöltéséhez szükséges: egy számlázható tranzakció felmerült minden alkalommal, amikor egy csomópont objektum betöltése a forrásból. (További csomópontjáról elérhető) több globális tartalom eredményeképpen több számlázható tranzakciók eredményez.

- Élettartam befolyásoló: az objektum magasabb TTL azt jelenti, hogy be szeretné olvasni a forrásból ritkábban kell. Azt is jelenti, az ügyfelek, például böngészők, az objektum, amely csökkentheti a tranzakciók és a CDN hosszabb, képes gyorsítótárazni.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hogyan kezelhetem a költségeket a leghatékonyabban?
A leghosszabb élettartam a tartalom lehet beállítani. 
