---
title: Azure Piactér
description: Ismerteti, hogyan használhatják az Azure Marketplace-t az EA-ügyfelek.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 91079310d4d566a65c6e8bc7f913ba92d04dece1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689932"
---
# <a name="azure-marketplace"></a>Azure Piactér

Ez a cikk azt ismerteti, hogy az EA-ügyfelek és a partnerek hogyan tekinthetik meg a Marketplace díjait, és hogyan engedélyezhetik az Azure Marketplace-en történő vásárlásokat.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace EA-ügyfelek számára

A közvetlen ügyfelek esetében az Azure Marketplace-díjak az Azure Enterprise Portalon láthatók. Az Azure Marketplace-vásárlások és a használat számlázása az Azure-előrefizetési kereten kívül, negyedévente, havonta és utólagosan történik.

A közvetett ügyfelek megtalálhatják Azure Marketplace-előfizetéseiket az Azure Enterprise Portal **Előfizetések kezelése** lapján, de a díjszabás rejtve marad. Az Azure Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz (LSP) kell fordulniuk.

Az új havi vagy évente ismétlődő Azure Marketplace-vásárlások esetében a teljes összeg lesz kiszámlázva arra az időszakra, amelyben az Azure Marketplace-tételeket megvásárolták. Ezek a tételek a következő időszakban automatikusan megújulnak az eredeti vásárlás napján.

A havonta ismétlődő díjak megújítása továbbra is az egyes naptári hónapok első napján történik. Az éves díjak megújítása a vásárlási dátum évfordulóján történik.

Az Azure Marketplace-en elérhető külső viszonteladói szolgáltatások némelyike mostantól a Nagyvállalati Szerződés (EA) Azure-előrefizetését használja. Ezeknek a szolgáltatásoknak a számlázása korábban az EA Azure-előrefizetési keretén kívül, külön számlákkal történt. A szolgáltatásokra az Azure Marketplace-en érvényes EA Azure-előrefizetési keret hozzájárul az ügyfelek vásárlás- és fizetéskezelésének egyszerűsítéséhez. Az Azure-előrefizetést használó szolgáltatások teljes listája az [Azure-webhely 2018. március 6-i frissítésében](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) érhető el.

### <a name="partners"></a>Partnerek

A licencmegoldás-szolgáltatók letölthetik az Azure Marketplace-árlistát az Azure Enterprise Portal Árlistájáról. Válassza a jobb felső sarokban található **Marketplace-árlista** lehetőséget. Az Azure Marketplace-árlistában az összes rendelkezésre álló szolgáltatás és azok ára is megjelenik.

Az árlista letöltéséhez:

1. Az Azure Enterprise Portalon lépjen a **Jelentések** > **Árlista** lapra.
1. A jobb felső sarokban, a felhasználóneve alatt keresse meg az Azure Marketplace-árlistára mutató hivatkozást.
1. Kattintson a jobb egérgombbal a hivatkozásra, majd válassza a **Cél mentése másként** lehetőséget.
1. A **Mentés** ablakban módosítsa a dokumentum címét a következőre: `AzureMarketplacePricelist.zip`. Ezáltal a fájl formátuma .xlsx helyett .zip lesz.
1. Ha a letöltés befejeződött, az országspecifikus árlisták egy zip-fájlban lesznek elérhetők.
1. A licencmegoldás-szolgáltatóknak az egyes országok fájljaira kell hivatkozniuk az országspecifikus díjszabás megállapításához. A licencmegoldás-szolgáltatók az **Értesítések** lapon találják azokat a termékváltozatokat, amelyek teljesen újak, illetve amelyek el lettek távolítva.
1. Ritkán fordul elő árváltozás. Ha mégis megtörténik, a licencmegoldás-szolgáltatók 30 nappal korábban e-mailben kapnak értesítést az áremelésekről és a valutaárfolyamok módosulásáról.
1. A licencmegoldás-szolgáltatók regisztrációnként, független szoftvergyártók szerint, illetve negyedévente egy számlát kapnak.

### <a name="enabling-azure-marketplace-purchases"></a>Az Azure Marketplace-vásárlások engedélyezése

A vállalati rendszergazdák engedélyezhetik vagy letilthatják az Azure Marketplace-vásárlásokat a regisztrációjukhoz tartozó összes Azure-előfizetésre vonatkozóan. Ha a vállalati rendszergazda letiltja a vásárlásokat, és vannak olyan Azure-előfizetések, amelyek már rendelkeznek Azure Marketplace-előfizetésekkel, az intézkedés ezeket az Azure Marketplace-előfizetéseket nem érinti.

Bár az ügyfelek átalakíthatják közvetlen Azure-előfizetéseiket Azure EA-előfizetéssé, ha az Azure Enterprise Portalon lévő regisztrációjukhoz társítják őket, a művelet nem konvertálja automatikusan az alárendelt gyermek előfizetéseket.

Az Azure Marketplace-vásárlások engedélyezése:

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Lépjen a **Kezelés** menüpontra.
1. A **Regisztráció részletei** szakaszban válassza az **Azure Marketplace** vonalelem mellett található ceruzaikont.
1. Válassza igény szerint az **Engedélyezett/Letiltott** vagy az ingyenes **Csak BYOL-termékváltozat** lehetőséget.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> A BYOL (saját licenc használata) és a Csak ingyenes lehetőség az Azure Marketplace-vásárlásokat és -beszerzéseket a BYOL- és az ingyenes termékváltozatokra korlátozza.

### <a name="services-billed-hourly-for-azure-ea"></a>Az Azure EA esetében óradíj alapján számlázott szolgáltatások

Az alábbi szolgáltatások számlázása a nagyvállalati szerződés esetében óradíj alapján történik, és nem havi díjszabás szerint, mint a Microsoft Online Előfizetési Program (MOSP) esetében:

- Alkalmazásszolgáltatási hálózat
- Web Application Firewall (Webalkalmazási tűzfal)

### <a name="azure-remoteapp"></a>Azure RemoteApp

Ha nagyvállalati szerződéssel rendelkezik, akkor az Azure RemoteAppért a nagyvállalati szerződésben megállapított árszint alapján fizet. Nem számítunk fel további díjakat. A standard ár a kezdeti 40 órát tartalmazza. A korlátlan díjszabás a kezdeti 80 órát fedezi. A RemoteApp a 80 órán túl nem bocsátja ki a használatot.

## <a name="azure-marketplace-faq"></a>Azure Marketplace – Gyakori kérdések

Ez a szakasz elmagyarázza, hogy az Azure-előrefizetési keret hogyan vonatkozhat egyes harmadik felek viszonteladói szolgáltatásaira az Azure Marketplace-en.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Mi változott az Azure Marketplace-szolgáltatásokkal és az Azure EA-előrefizetéssel kapcsolatban?

2018. március 1-től néhány külső viszonteladói szolgáltatás is az Azure EA-előrefizetési keretet használja. Az Azure Reserved VM Instances kivételével ezeknek a szolgáltatásoknak a számlázása korábban az Azure EA-előrefizetési kereten kívül, külön számlákkal történt.

Kiterjesztettük az Azure-előrefizetési keret felhasználhatóságát, hogy az ügyfeleink által leggyakrabban vásárolt, külső féltől származó Azure Marketplace-szolgáltatásokat is lefedje. Az Azure Marketplace-en érvényes Azure EA-előrefizetési keret kiterjesztése ezen szolgáltatásokra hozzájárul az Ön vásárlás- és fizetéskezelésének egyszerűsítéséhez.

### <a name="why-did-we-make-this-change"></a>Mi oka van ennek a változtatásnak?

Az ügyfelek folyamatosan további lehetőségeket keresnek, hogy kihasználhassák az Azure-előrefizetés előnyeit. Ezzel egy gyakran felmerülő vásárlói kérésre adtunk választ, illetve Azure Marketplace-ügyfeleink jelentős részének kedveztünk általa.

### <a name="how-do-you-benefit"></a>Milyen előnyt jelent ez az Ön számára?

Egyszerűbb lesz a számlázás az ügyfelek számára, és könnyebbé válik az Azure EA-előrefizetési keret teljes kihasználása. Mivel ezeket a szolgáltatásokat fedezi az Azure-előrefizetés, az Azure EA-előrefizetés értékesebb lesz.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Melyik Azure Marketplace-szolgáltatások használják az Azure EA-előrefizetést? Honnan lehet ezt tudni?

Ha olyan szolgáltatást vásárol, amely az Azure-előrefizetést használja, az Azure Marketplace felhívja erre a figyelmét. A jelenleg támogatottak közé a Red Hat, a SUSE, az Autodesk és az Oracle által közzétett egyes szolgáltatások tartoznak. Más szolgáltatók által közzétett hasonló nevű szolgáltatások jelenleg nem fedezhetők az Azure-előrefizetési keretből. A GYIK végén a teljes lista elérhető.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Mi a teendő, ha kimerítem az Azure EA-előrefizetési keretemet?

Ha felhasználta a teljes Azure-előrefizetési keretet, és jelenleg azon túl használja a szolgáltatásokat, a velük kapcsolatos díjak a soron következő túlhasználati számlán jelennek meg a többi használatalapú szolgáltatással együtt. A 2018. március 1. előtti változtatást megelőzően ezek a díjak a többi Azure Marketplace-szolgáltatással együtt lettek kiszámlázva.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Miért nem használja az Azure Marketplace összes szolgáltatása az Azure EA-előrefizetést?

Sokat dolgozunk azon, hogy az Azure EA-előrefizetéshez kapcsolódóan a legjobb felhasználói élményt nyújthassuk. Ez a változás számos ügyfelet érint, és az Azure Marketplace-beli teljes költés jelentős részére hatással van. A későbbiekben további szolgáltatásokkal is bővülhet a kör.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hogyan befolyásolja ez a közvetett regisztrációt és partnereket?

Mindennek nincs hatása a közvetett regisztrációs ügyfeleinkre és partnereinkre. Ezekre a szolgáltatásokra ugyanazok a partneri árréslehetőségek vonatkoznak, mint más használatalapú szolgáltatások esetében. Az egyetlen változás az, hogy a díjak egy másik számlán jelennek meg, és a díjak ellenértéke az ügyfél Azure EA-előrefizetési keretéből vonódik le.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Van lista azokról az Azure Marketplace-szolgáltatásokról, amelyek az Azure EA-előrefizetési keretét használják?

Bizonyos Azure Marketplace-ajánlatokhoz az Azure-előrefizetési keret is felhasználható. A programban részt vevő termékek teljes listájáért tekintse meg [az Azure-előrefizetést használó, harmadik féltől származó szolgáltatások](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) szakaszát.


## <a name="next-steps"></a>További lépések

- Itt további információt találhat a [díjszabásról](ea-pricing-overview.md).