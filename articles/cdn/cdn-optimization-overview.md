---
title: Az Azure CDN optimalizálása a tartalomkézbesítés típusához
description: Az Azure CDN optimalizálása a tartalomkézbesítés típusához
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: da8f17da9225da1d2b92bd8515d645bce9a1bbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252115"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Az Azure CDN optimalizálása a tartalomkézbesítés típusához

Amikor nagy globális közönségnek juttatsz el tartalmat, rendkívül fontos, hogy a tartalom optimális annektálását biztosítsd. [Az Azure Content Delivery Network (CDN)](cdn-overview.md) a tartalom típusától függően optimalizálhatja a kézbesítési élményt. A tartalom lehet webhely, élő közvetítés, videó vagy nagy méretű fájl letöltésre. CdN-végpont létrehozásakor az **Optimalizált beállításban** meg kell adni egy forgatókönyvet. A választás határozza meg, hogy melyik optimalizálás vonatkozik a CDN-végpontról szállított tartalomra.

Az optimalizálási lehetőségek célja, hogy az ajánlott eljárások nak megfelelő viselkedéssel javítsák a tartalomkézbesítési teljesítményt és javítsák az eredetkiszervezést. A forgatókönyv-beállítások befolyásolják a teljesítményt a részleges gyorsítótárazás, az objektum-darabolás és az eredethiba-újrapróbálkozási házirend konfigurációinak módosításával. 

Ez a cikk áttekintést nyújt a különböző optimalizálási funkciókról, és ahasználatukról. A funkciókról és korlátozásokról az egyes optimalizálási típusokra vonatkozó cikkekben talál további információt.

> [!NOTE]
> CDN-végpont létrehozásakor az **Optimalizált** beállítások a végpont létrehozásának típusától függően változhatnak. Az Azure CDN-szolgáltatók a forgatókönyvtől függően különböző módon alkalmazzák a továbbfejlesztést. 

## <a name="provider-options"></a>Szolgáltatói beállítások

**A Microsoft-profilokból származó Azure CDN Standard** a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery). Ez az optimalizálás is használják a média streaming és a nagy fájl letöltés.

> [!NOTE]
> A Microsoft dinamikus webhelygyorsítását az [Azure Front Door Service kínálja.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

**A Verizon Azure CDN Standard és** a **Verizon-profilokból származó Azure CDN Premium** a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery). Ez az optimalizálás is használják a média streaming és a nagy fájl letöltés.

* [Dinamikus helygyorsulás](#dynamic-site-acceleration) 


**Az Akamai-profilokazure-CDN Standard szolgáltatása** a következő optimalizálásokat támogatja:

* [Általános webes kézbesítés](#general-web-delivery) 

* [Általános médiaadatfolyam-továbbítás](#general-media-streaming)

* [Igény szerinti videomédia-adatfolyam-továbbítás](#video-on-demand-media-streaming)

* [Nagy fájl letöltése](#large-file-download)

* [Dinamikus helygyorsulás](#dynamic-site-acceleration) 

A Microsoft azt javasolja, hogy tesztelje a különböző szolgáltatók közötti teljesítménybeli eltéréseket, hogy kiválassza a kézbesítés optimális szolgáltatóját.

## <a name="select-and-configure-optimization-types"></a>Optimalizálási típusok kiválasztása és konfigurálása

CdN-végpont létrehozásakor válasszon ki egy optimalizálási típust, amely a legjobban megfelel a végpont által kézbesíteni kívánt forgatókönyvnek és tartalomtípusnak. **Az általános webes kézbesítés** az alapértelmezett beállítás. A meglévő Azure CDN Standard csak **Akamai** végpontok csak frissítheti az optimalizálási lehetőség bármikor. Ez a módosítás nem szakítja meg az Azure CDN-ből történő kézbesítést. 

1. Az **Akamai-profil Azure CDN-szabványában** válasszon ki egy végpontot.

    ![Végpont kijelölése](./media/cdn-optimization-overview/01_Akamai.png)

2. A BEÁLLÍTÁSOK csoportban válassza az **Optimalizálás**lehetőséget. Ezután válasszon ki egy típust az **Optimalizált legördülő** listából.

    ![Optimalizálás és típuskijelölés](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimalizálás adott forgatókönyvekhez

A CDN-végpont ot az alábbi esetek egyikére optimalizálhatja. 

### <a name="general-web-delivery"></a>Általános webes kézbesítés

Az általános webes kézbesítés a leggyakoribb optimalizálási lehetőség. Általános webes tartalomoptimalizálásra tervezték, például weboldalakra és webes alkalmazásokra. Ez az optimalizálás fájl- és videóletöltéshez is használható.

Egy tipikus webhely statikus és dinamikus tartalmat tartalmaz. A statikus tartalom képeket, JavaScript-könyvtárakat és stíluslapokat tartalmaz, amelyek gyorsítótárazhatók és kézbesíthetők a különböző felhasználók számára. A dinamikus tartalom személyre szabott az egyes felhasználók számára, például a felhasználói profilhoz szabott hírek. A dinamikus tartalom, például a bevásárlókosár tartalma, nem kerül a gyorsítótárba, mert az minden felhasználó számára egyedi. Általános webes szállítás optimalizálhatja a teljes honlapon. 

> [!NOTE]
> Ha **egy Akamai-profilból származó Azure CDN Standardot** használ, válassza ezt az optimalizálási típust, ha az átlagos fájlméret kisebb, mint 10 MB. Ellenkező esetben, ha az átlagos fájlméret nagyobb, mint 10 MB, válassza a **Nagy fájl letöltése** lehetőséget az **Optimalizált** legördülő listából.

### <a name="general-media-streaming"></a>Általános médiaadatfolyam-továbbítás

Ha a végpontot élő streameléshez és az igény szerinti videóstreameléshez kell használnia, válassza ki az általános médiaadatfolyam-optimalizálási típust.

A médiaadatfolyam-továbbítás időérzékeny, mivel az ügyfélre későn érkező csomagok, például a videotartalom gyakori pufferelése, csökkent látóélményt okozhatnak. A médiaadatfolyam-továbbítás optimalizálása csökkenti a médiatartalom-kézbesítés késését, és zökkenőmentes streamelési élményt nyújt a felhasználók számára. 

Ez a forgatókönyv gyakori az Azure media service-ügyfelek számára. Az Azure médiaszolgáltatások használatakor egyetlen streamelési végpontot kap, amely élő és igény szerinti streameléshez is használható. Ebben a forgatókönyvben az ügyfeleknek nem kell átváltaniuk egy másik végpontra, amikor élőről igény szerinti streamelésre váltanak. Az általános médiaadatfolyam-optimalizálás támogatja az ilyen típusú forgatókönyveket.

A **Microsoft Azure CDN Standard,** a **Verizon Azure CDN Standard**és a Verizon Azure **CDN Premium**esetében az általános webes kézbesítésoptimalizálási típus használatával általános folyamatos átvitelű médiatartalmakat biztosíthat.

A médiaadatfolyam-optimalizálásról további információt a [Médiaadatfolyam-optimalizálás című](cdn-media-streaming-optimization.md)témakörben talál.

### <a name="video-on-demand-media-streaming"></a>Igény szerinti videomédia-adatfolyam-továbbítás

Az igény szerinti médiaadatfolyam-továbbítás optimalizálása javítja az igény szerinti videoadatfolyam-tartalmakat. Ha egy végpontot használ az igény szerinti videóstreameléshez, használja ezt a beállítást.

A **Microsoft Azure CDN Standard,** a **Verizon Azure CDN Standard**és a **Verizon-profilokból származó Azure CDN Premium** esetén használja az általános webes kézbesítésoptimalizálási típust az igény szerinti folyamatos átvitelű médiatartalmak továbbításához.

A médiaadatfolyam-optimalizálásról további információt a [Médiaadatfolyam-optimalizálás című](cdn-media-streaming-optimization.md)témakörben talál.

> [!NOTE]
> Ha a CDN-végpont elsősorban az igény szerinti videotartalmat szolgálja ki, használja ezt az optimalizálási típust. A fő különbség ez az optimalizálási típus és az általános médiaadatfolyam-optimalizálási típus között a kapcsolat újrapróbálkozási időtúllépése. Az időtúlidőpont sokkal rövidebb az élő streamelési forgatókönyvekkel való munkához.
>

### <a name="large-file-download"></a>Nagy fájl letöltése

Az **Akamai-profilokból származó Azure CDN Standard** esetén a nagy fájlletöltések 10 MB-nál nagyobb tartalomra vannak optimalizálva. Ha az átlagos fájlméret kisebb, mint 10 MB, használja az általános webes kézbesítést. Ha az átlagos fájlméretek folyamatosan nagyobbak 10 MB-nál, hatékonyabb lehet külön végpontot létrehozni a nagy fájlok számára. A belső vezérlőprogram vagy a szoftverfrissítések például általában nagyméretű fájlok. Az 1,8 GB-nál nagyobb fájlok kézbesítéséhez a nagy fájlletöltésoptimalizálás szükséges.

A **Microsoft Azure CDN Standard,** a **Verizon Azure CDN Standard**és a **Verizon-profilokból származó Azure CDN Premium** esetén használja az általános webes kézbesítésoptimalizálási típust a nagy fájlletöltési tartalom továbbításához. Nincs korlátozás a fájl letöltési mérete.

A nagyfájlok nagyméretű optimalizálásáról a [Nagyfájl-optimalizálás](cdn-large-file-optimization.md)című témakörben talál további információt.

### <a name="dynamic-site-acceleration"></a>Dinamikus helygyorsítás

 A dinamikus helygyorsítás (DSA) az **Akamai,** a **Verizon Azure CDN Standard**és a **Verizon-profilokból származó Azure CDN Premium** azure-beli CDN Standard esetén érhető el. Ez az optimalizálás további használati díjat von maga után; további információt a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/)című témakörben talál.

> [!NOTE]
> A Microsoft dinamikus webhelygyorsítását az [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) szolgáltatás a Microsoft globális globális hálózatát kihasználva globálisan [működő, az](https://en.wikipedia.org/wiki/Anycast) alkalmazásszámítási feladatok szolgáltatása ként kínálja.

A DSA különböző technikákat tartalmaz, amelyek a dinamikus tartalom késése és teljesítménye szempontjából előnyösek. A technikák közé tartozik az útvonal- és hálózatoptimalizálás, a TCP-optimalizálás és egyebek. 

Ezzel az optimalizálással felgyorsíthatja a webalkalmazás, amely számos válasz, amely nem gyorsítótárazható. Ilyenek például a keresési eredmények, a fizetési tranzakciók vagy a valós idejű adatok. Továbbra is használhatja az alapvető Azure CDN-gyorsítótárazási képességek statikus adatokhoz. 

A hely dinamikus gyorsításáról a [Dinamikus helygyorsítás](cdn-dynamic-site-acceleration.md)című témakörben talál további információt.



