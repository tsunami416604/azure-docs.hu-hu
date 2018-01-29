---
title: "A forgatókönyvnek Azure tartalomkézbesítési optimalizálása"
description: "A tartalom az adott forgatókönyveket kézbesítését optimalizálása"
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: fa4cf306eeb1a8372da5b2a86ac73fbba2832666
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>A forgatókönyvnek Azure tartalomkézbesítési optimalizálása

Amikor tartalmat továbbít hatalmas, globális közönségek számára, fontos annak tartalmaihoz optimalizált kézbesítését érdekében. Az Azure tartalom Delivery Network optimalizálhatja a tartalomtípushoz, hogy a kézbesítési élményt. A tartalom lehet egy webhely, élő adatfolyam, videó, vagy letölthető nagy fájlok. Amikor létrehoz egy tartalomkézbesítési hálózat (CDN) végpont, megadhatja a esetén a **optimalizálva** lehetőséget. A kiválasztott határozza meg, melyik optimalizálás a tartalmat a CDN-végpont szállított vonatkozik.

Optimalizálás választható bevált gyakorlat viselkedések segítségével tartalomkézbesítési teljesítményének javítása és a jobb eredetű-kiszervezés. A forgatókönyvben a választott részleges gyorsítótárazás objektum adattömbösítő és a forrás hiba újrapróbálkozási házirendje konfigurációi módosításával hatása a teljesítményre. 

Ez a cikk áttekintése különböző optimalizálási funkcióját, és ha kell használnia. A szolgáltatások és korlátozások további információkért lásd: a megfelelő cikkek minden egyes optimalizálási típus.

> [!NOTE]
> A **optimalizálva** lehetőségek a kiválasztott szolgáltató függően változhat. CDN-szolgáltatók a fejlesztés alkalmazni a helyzettől függően különböző módon. 

## <a name="provider-options"></a>Szolgáltatói beállítása

**Az Azure Content Delivery Network Akamai** az alábbi optimalizálások támogatja:

* [Általános webes kézbesítés](#general-web-delivery) 

* [Általános médiaadatfolyam-továbbítást](#general-media-streaming)

* [Videotartalom médiaadatfolyam-továbbítást](#video-on-demand-media-streaming)

* [Nagy méretű fájl letöltése](#large-file-download)

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 

**Verizon Azure Content Delivery Network** az alábbi optimalizálások támogatja:

* [Általános webes kézbesítési](#general-web-delivery) (médiaadatfolyam és nagy méretű fájlok tartalom letöltése is használható)

* [Dinamikus helygyorsítás](#dynamic-site-acceleration) 

Erősen ajánlott, hogy tesztelje a teljesítményt változata között jelölje be az optimális a kézbesítési szolgáltató különböző szolgáltatók.

## <a name="select-and-configure-optimization-types"></a>Válasszon ki és konfiguráljon optimalizálási típusok

Új végpont létrehozásához válassza ki az optimalizálás típusa, amely a legjobban illik a forgatókönyv és a végpont képes biztosítani a kívánt tartalom típusa. **Általános webes kézbesítési** az alapértelmezett beállítás. Meglévő **Azure Content Delivery Network Akamai** végpontok, a beállítást bármikor frissítheti. Ez a változás nem szakítsa meg a CDN kézbesítését. 

1. Belül egy **Azure Content Delivery Network Akamai** profilját, válasszon ki egy végpontot.

    ![Végpont kiválasztása ](./media/cdn-optimization-overview/01_Akamai.png)

2. A **beállítások**, jelölje be **optimalizálási**. Ezt követően válassza egy típus a **optimalizálva** legördülő listából.

    ![Optimalizálási és típus kiválasztása](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Az adott forgatókönyveket optimalizálása

A CDN-végpont egy forgatókönyvet is optimalizálhatja. 

### <a name="general-web-delivery"></a>Általános webes kézbesítés

Általános webes kézbesítési a leggyakrabban használt optimalizálási beállítás. Általános webes tartalom optimalizálásra, például a weboldalakon és webes alkalmazásokhoz tervezték. Az optimalizálás is használható fájlt, és a videó tölti le.

Egy tipikus webhely statikus és dinamikus tartalom tartalmazza. Statikus tartalom magában foglalja a lemezképek, a JavaScript szalagtárak és a stíluslapok, amely a gyorsítótárban, és különböző felhasználók beküldeni. Dinamikus tartalom egy adott felhasználóhoz, például a felhasználói profil vannak szabva hírek elemekre személyre szabhatja. Dinamikus tartalom nincs gyorsítótárazva, mert egyedi, például a vásárlási bevásárlókocsiból tartalmát az egyes felhasználók. Általános webes kézbesítési optimalizálhatja a teljes webhelyet. 

> [!NOTE]
> Ha **Azure Content Delivery Network Akamai**, érdemes használnia az optimalizálás, ha a átlagos mérete 10 MB-nál kisebb. Ha a átlagos mérete nagyobb, mint 10 MB, jelölje be **nagy méretű fájlok letöltési** a a **optimalizálva** legördülő listából.

### <a name="general-media-streaming"></a>Általános médiastreaming

Ha használja a végpontot az élő adatfolyam- és videotartalom streaming van szüksége, általános médiaadatfolyam-továbbítást optimalizálási ajánlott.

Idő-és nagybetűket, médiaadatfolyam azért, mert az ügyfél késői csomagokat csökkent megtekintését, például a video tartalom Gyakori pufferelés okozhat. Médiaadatfolyam-továbbítást optimalizálási media tartalomkézbesítési késését csökkentő és adatfolyam-továbbítási zökkenőmentes élményt nyújt a felhasználók számára. 

Ez a forgatókönyv esetében gyakori, az Azure media service ügyfelek esetén. Az Azure media services használata esetén nem használható élő és igény szerinti adatfolyam egy streamvégpont kap. Ebben az esetben az ügyfelek nem kell váltani egy másik végpont, amikor azok váltson az élő igényalapú streameléshez. Általános media adatfolyam-továbbítási optimalizálási támogatja ezt a forgatókönyvet.

**Verizon Azure tartalom Delivery Network** adatfolyam media tartalom az általános webes kézbesítési optimalizálási típust használ.

Media adatfolyam-továbbítási optimalizálási kapcsolatos további információkért lásd: [médiaadatfolyam-továbbítást optimalizálási](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Videotartalom médiaadatfolyam-továbbítást

Videotartalom media adatfolyam-továbbítási optimalizálási növeli a videotartalom adatfolyamok tartalmát. A végpont az videotartalom adatfolyamként történő használatakor érdemes ezt a beállítást használja.

**Verizon Azure tartalom Delivery Network** adatfolyam media tartalom az általános webes kézbesítési optimalizálási típust használ.

Media adatfolyam-továbbítási optimalizálási kapcsolatos további információkért lásd: [médiaadatfolyam-továbbítást optimalizálási](cdn-media-streaming-optimization.md).

> [!NOTE]
> Ha a végpont elsősorban videotartalom tartalmat szolgáltat, ez használható optimalizálás. Az optimalizálás és az általános médiaadatfolyam-továbbítást optimalizálása közötti fő különbség a kapcsolat újrapróbálkozási időkorlátja. Az időtúllépés értéke sokkal rövidebb élő adatfolyam-továbbítási forgatókönyv együttműködni.

### <a name="large-file-download"></a>Nagyméretű fájl letöltése

Ha **Azure Content Delivery Network Akamai**, nagy méretű fájlok letöltési képes biztosítani a 1,8 GB-nál nagyobb fájlokat kell használnia. **Verizon Azure Content Delivery Network** korlátozása nem rendelkezik a fájl letöltési mérete az általános webes kézbesítési optimalizálás.

Ha **Azure tartalom Delivery Network Akamai**, nagy fájlok letöltése tartalma 10 MB-nál nagyobb vannak optimalizálva. Ha a átlagos mérete 10 MB-nál kisebb, előfordulhat, hogy használni kívánt általános webes kézbesítését. Ha a fájlok átlagos következetesen 10 MB-nál nagyobb értékek, lehet, hatékonyabb, ha nagy fájlok esetében külön végpont létrehozásához. Például belső vezérlőprogram vagy szoftverfrissítések általában nagy méretű fájlt.

**Verizon Azure tartalom Delivery Network** képes biztosítani a nagy méretű fájlok tartalom letöltése a általános webes kézbesítési optimalizálási típust használ.

Nagy méretű fájlok optimalizálással kapcsolatos további információkért lásd: [nagy méretű fájlok optimalizálási](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Dinamikuswebhely-gyorsítás

 Dinamikus gyorsítás egyaránt megtalálható **Azure Content Delivery Network Akamai** és **Azure Content Delivery Network verizon** profilok. Az optimalizálás magában foglalja a további díjat kell használni, További információkért lásd: [Content Delivery Network árképzési](https://azure.microsoft.com/pricing/details/cdn/).

Dinamikus gyorsítás magában foglalja a különböző módszereket, amelyek kihasználják a késés és a dinamikus tartalom teljesítménye. Ennek technikái a következők: útvonal és a hálózati optimalizálás, a TCP-optimalizálása és egyebek. 

Az optimalizálás használhatja annak érdekében, a webes alkalmazás, amely tartalmazza a számos olyan válaszok, amelyek nem gyorsítótárazható. Többek között a keresési eredmények között, a kivételt tranzakciók vagy a valós idejű adatokat. Továbbra is az alapképességek CDN gyorsítótárazási használ statikus adatok. 

Dinamikus járó gyorsító kapcsolatos további információkért lásd: [dinamikus acceleration](cdn-dynamic-site-acceleration.md).



