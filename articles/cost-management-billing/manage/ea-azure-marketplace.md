---
title: Azure Piactér
description: Ismerteti, hogyan használhatják az Azure Marketplace-t az EA-ügyfelek.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: banders
ms.openlocfilehash: 73c9e011359d2f86ec9f91d7e1e9b2869e68c794
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648642"
---
# <a name="azure-marketplace"></a>Azure Piactér

Ez a cikk azt ismerteti, hogy az EA-ügyfelek és a partnerek hogyan tekinthetik meg a Marketplace díjait, és hogyan engedélyezhetik az Azure Marketplace-en történő vásárlásokat.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace EA-ügyfelek számára

A közvetlen ügyfelek esetében az Azure Marketplace-díjak az Azure Enterprise Portalon láthatók. Az Azure Marketplace-vásárlások és felhasználás számlázása a pénzügyi kereten kívül, negyedévente, havonta és utólagosan történik.

A közvetett ügyfelek megtalálhatják Azure Marketplace-előfizetéseiket az Azure Enterprise Portal **Előfizetések kezelése** lapján, de a díjszabás rejtve marad. Az Azure Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz (LSP) kell fordulniuk.

Az új havi vagy évente ismétlődő Azure Marketplace-vásárlások esetében a teljes összeg lesz kiszámlázva arra az időszakra, amelyben az Azure Marketplace-tételeket megvásárolták. Ezek a tételek a következő időszakban automatikusan megújulnak az eredeti vásárlás napján.

A havonta ismétlődő díjak megújítása továbbra is az egyes naptári hónapok első napján történik. Az éves díjak megújítása a vásárlási dátum évfordulóján történik.

Az Azure Marketplace-en elérhető külső viszonteladói szolgáltatások némelyike mostantól a nagyvállalati szerződés (EA) pénzügyi keretét használja. Ezeknek a szolgáltatásoknak a számlázása korábban az EA pénzügyi keretén kívül, külön számlákkal történt. A szolgáltatásokra az Azure Marketplace-en érvényes EA pénzügyi keret hozzájárul az ügyfelek vásárlás- és fizetéskezelésének egyszerűsítéséhez. A pénzügyi keretet használó szolgáltatások teljes listája az [Azure-webhely 2018. március 6-ai frissítésében](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) érhető el.

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

Ez a szakasz elmagyarázza, hogy az Azure pénzügyi kerete hogyan vonatkozhat egyes harmadik felek viszonteladói szolgáltatásaira az Azure Marketplace-en.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Mi változott az Azure Marketplace-szolgáltatásokkal és az EA pénzügyi keretével kapcsolatban?

2018. március 1-jétől néhány harmadik fél viszonteladói szolgáltatása az EA pénzügyi keretét (MC) használja. Az Azure Reserved VM Instances kivételével ezeknek a szolgáltatásoknak a számlázása korábban az EA pénzügyi keretén kívül, külön számlákkal történt.

Kiterjesztettük a pénzügyi keret felhasználhatóságát, hogy az ügyfeleink által leggyakrabban vásárolt, külső féltől származó Azure Marketplace-szolgáltatásokat is lefedje. Az Azure Marketplace-en érvényes EA pénzügyi keret kiterjesztése ezen szolgáltatásokra hozzájárul az Ön vásárlás- és fizetéskezelésének egyszerűsítéséhez.

### <a name="why-did-we-make-this-change"></a>Mi oka van ennek a változtatásnak?

Az ügyfelek folyamatosan további lehetőségeket keresnek a pénzügyi keret formájában elvégzett befizetés kihasználása érdekében. Ezzel egy gyakran felmerülő vásárlói kérésre adtunk választ, illetve Azure Marketplace-ügyfeleink jelentős részének kedveztünk általa.

### <a name="how-do-you-benefit"></a>Milyen előnyt jelent ez az Ön számára?

Egyszerűbb lesz a számlázás az ügyfelek számára, és könnyebbé válik az EA pénzügyi keret teljes kihasználása. Mivel ezeket a szolgáltatásokat fedezi az előre kifizetett pénzügyi keret, az EA pénzügyi kerete értékesebb lesz.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Melyik Azure Marketplace-szolgáltatások használják az EA pénzügyi keretét? Honnan lehet ezt tudni?

Ha olyan szolgáltatást vásárol, amely a pénzügyi keretet használja, az Azure Marketplace figyelmezteti Önt. A jelenleg támogatottak közé a Red Hat, a SUSE, az Autodesk és az Oracle által közzétett egyes szolgáltatások tartoznak. A más felek által közzétett hasonló nevű szolgáltatások jelenleg nem fedezhetők a pénzügyi kerettel. A GYIK végén a teljes lista elérhető.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Mi a teendő, ha a kimerítem az EA pénzügyi keretem?

Ha felhasználta a teljes pénzügyi keretet, és jelenleg többlethasználatot folytat, az ezen szolgáltatásokkal kapcsolatos díjak a soron következő túlhasználati számlán jelennek meg a többi használatalapú szolgáltatással együtt. A 2018. március 1. előtti változtatást megelőzően ezek a díjak a többi Azure Marketplace-szolgáltatással együtt lettek kiszámlázva.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Miért nem használja az Azure Marketplace összes szolgáltatása az EA pénzügyi keretét?

Sokat dolgozunk azon, hogy az EA pénzügyi kerethez kapcsolódóan a legjobb felhasználói élményt nyújthassuk. Ez a változás számos ügyfelet érint, és az Azure Marketplace-beli teljes költés jelentős részére hatással van. A későbbiekben további szolgáltatásokkal is bővülhet a kör.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hogyan befolyásolja ez a közvetett regisztrációt és partnereket?

Mindennek nincs hatása a közvetett regisztrációs ügyfeleinkre és partnereinkre. Ezekre a szolgáltatásokra ugyanazok a partneri árréslehetőségek vonatkoznak, mint más használatalapú szolgáltatások esetében. Az egyetlen változás az, hogy a díjak egy másik számlán jelennek meg, és a díjak ellenértéke az ügyfél EA pénzügyi keretéből vonódik le.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Van lista azokról az Azure Marketplace-szolgáltatásokról, amelyek az EA pénzügyi keretét használják?

Bizonyos Azure Marketplace-ajánlatok esetében a pénzügyi keret összegei is felhasználhatók. A programban részt vevő termékek teljes listájáért tekintse meg [a pénzügyi keretet használó, harmadik féltől származó szolgáltatások](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) szakaszt.


## <a name="next-steps"></a>További lépések

- Itt további információt találhat a [díjszabásról](ea-pricing-overview.md).