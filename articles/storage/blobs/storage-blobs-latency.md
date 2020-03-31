---
title: Késés a Blob storage-ban - Azure Storage
description: A Blob storage-műveletek késését és mérését, és megtudhatja, hogyan tervezheti meg a Blob storage-alkalmazások alacsony késésű.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749726"
---
# <a name="latency-in-blob-storage"></a>Késés a Blob Storage-ban

A válaszidőként hivatkozott késés az az idő, ameddig egy alkalmazásnak meg kell várnia a kérelem befejezését. A késés közvetlenül befolyásolhatja az alkalmazás teljesítményét. Alacsony késleltetés gyakran fontos a forgatókönyvek az emberek a hurok, mint például a hitelkártya-tranzakciók lebonyolítása vagy weboldalak betöltése. A bejövő események magas ütemben történő feldolgozásához szükséges rendszerek, például a telemetriai naplózás vagy az IoT-események szintén alacsony késést igényelnek. Ez a cikk ismerteti, hogyan lehet megérteni és mérni a blokkblobok on műveletek késését, és hogyan tervezheti meg az alkalmazások alacsony késésű.

Az Azure Storage két különböző teljesítménybeállítást kínál a blokkblobok hoz: prémium és standard. A prémium szintű blokkblobok jelentősen alacsonyabb és konzisztensebb késleltetést kínálnak, mint a normál blokkblobok a nagy teljesítményű SSD-lemezeken keresztül. További információ: **Prémium szintű teljesítményblokk blobstorage** az [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek.](storage-blob-storage-tiers.md)

## <a name="about-azure-storage-latency"></a>Az Azure Storage késése

Az Azure Storage késése az Azure Storage-műveletek kérési díjaihoz kapcsolódik. A kérelmek díjai másodpercenkénti bemeneti/kimeneti műveleteknek is nevezik.

A kérelem sebességének kiszámításához először határozza meg, hogy az egyes kérelmek mennyi időt vesz igénybe a befejezéshez, majd kiszámítja, hogy hány kérelmet lehet másodpercenként feldolgozni. Tegyük fel például, hogy egy kérelem teljesítéséhez 50 ezredmásodperc (ms) szükséges. Egy alkalmazás egy szál egy függőben lévő olvasási vagy írási művelet et kell elérni 20 IOPS (1 másodperc vagy 1000 ms / 50 ms kérésenként). Elméletileg, ha a szálszám megduplázódik kettőre, akkor az alkalmazás nak képesnek kell lennie 40 IOPS elérésére. Ha a függőben lévő aszinkron olvasási vagy írási műveletek minden szál megduplázódik két, majd az alkalmazás képesnek kell lennie 80 IOPS eléréséhez.

A gyakorlatban a kérelem díjak nem mindig skálázható lineárisan, mivel a terhelés az ügyfél a feladatütemezés, a környezetváltás, és így tovább. A szolgáltatási oldalon az Azure Storage-rendszerre nehezedő nyomás, a használt tárolóadathordozók közötti különbségek, az egyéb munkaterhelések zaja, karbantartási feladatok és egyéb tényezők miatt a késés variabilitása lehet. Végül az ügyfél és a kiszolgáló közötti hálózati kapcsolat hatással lehet az Azure Storage késése miatt torlódás, átirányítás, vagy egyéb zavarok miatt.

Az Azure Storage-sávszélesség, más néven átviteli sebesség, a kérelem sebességéhez kapcsolódik, és kiszámítható a kérelem sebességének (IOPS) és a kérelem méretének szorzata. Például másodpercenként 160 kérelem feltételezése, minden 256 KiB adat 40 960 KiB másodpercenkénti átviteli, vagy 40 MiB másodpercenkénti átviteli forgalmat eredményez.

## <a name="latency-metrics-for-block-blobs"></a>Késési metrikák blokkblobokhoz

Az Azure Storage két késési metrikát biztosít a blokkblobok számára. Ezek a metrikák az Azure Portalon tekinthetők meg:

- **Végpontok közötti (E2E) késés** méri az időközt, amikor az Azure Storage megkapja a kérelem első csomagját, amíg az Azure Storage nem kap ügyfélnyugtát a válasz utolsó csomagján.

- **A kiszolgáló késése** azt az időtartamot méri, amely az Azure Storage-kérés utolsó csomagját kapja meg, amíg a válasz első csomagját vissza nem adja az Azure Storage-ból.

Az alábbi képen látható az **átlagos sikeres E2E-késés** és az átlagos `Get Blob` **sikeresség kiszolgálói késés** egy minta számítási feladatok, amely meghívja a műveletet:

![Képernyőkép a Blob beolvasása művelet késési metrikáiról](media/storage-blobs-latency/latency-metrics-get-blob.png)

Normál körülmények között kevés a különbség a végpontok közötti késés és a kiszolgáló késése között, amit a rendszerkép a minta számítási feladatokhoz mutat.

Ha áttekinti a végpontok közötti és a kiszolgáló késésmetrikáit, és úgy találja, hogy a végpontok közötti késés jelentősen magasabb, mint a kiszolgáló késése, majd vizsgálja meg, és a további késés forrásának kezelése.

Ha a végpontok közötti és a kiszolgáló késése hasonló, de alacsonyabb késést igényel, majd fontolja meg a prémium szintű blokkblob-tárolóba való áttelepítést.

## <a name="factors-influencing-latency"></a>A késést befolyásoló tényezők

A késést befolyásoló fő tényező a művelet mérete. Hosszabb időt vesz igénybe a nagyobb műveletek végrehajtásához, mivel az Azure Storage által feldolgozott adatok mennyisége.

Az alábbi ábra a különböző méretű műveletek teljes idejét mutatja. Kis mennyiségű adat esetén a késési időköz túlnyomórészt a kérés kezelésére, nem pedig az adatok átvitelére szolgál. A késési időköz csak kis mértékben nő, ahogy a művelet mérete nő (az alábbi ábrán 1 jelöléssel). A művelet méretének további növekedésével több időt fordítanak az adatok átvitelére, így a teljes késési időköz megoszlik a kérelmek kezelése és az adatátvitel között (az alábbi ábrán 2 jelöléssel). Nagyobb üzemidő esetén a késési intervallumot szinte kizárólag az adatok átvitelére fordítják, és a kérés kezelése nagyrészt jelentéktelen (az alábbi ábrán 3 jelöléssel).

![Képernyőkép a teljes működési időről műveletméret szerint](media/storage-blobs-latency/operation-time-size-chart.png)

Az ügyfél konfigurációs tényezői, például az egyidejűség és a szálka is befolyásolják a késést. A teljes átviteli kapacitás attól függ, hogy hány tárolási kérelmek repülés egy adott időpontban, és hogyan kezeli az alkalmazás szálfűz. Az ügyfélerőforrások, beleértve a processzort, a memóriát, a helyi tárolót és a hálózati adaptereket, szintén befolyásolhatják a késést.

Az Azure Storage-kérelmek feldolgozása ügyfélprocesszor- és memória-erőforrásokat igényel. Ha az ügyfél nyomás alatt van egy alultáplált virtuális gép vagy a rendszer valamilyen elszabadult folyamata miatt, kevesebb erőforrás áll rendelkezésre az Azure Storage-kérelmek feldolgozásához. Bármilyen versengés vagy az ügyfél-erőforrások hiánya a végpontok közötti késés növekedését eredményezi a kiszolgáló késésének növekedése nélkül, növelve a két metrika közötti rést.

Ugyanilyen fontos a hálózati adapter és a hálózati cső az ügyfél és az Azure Storage között. A fizikai távolság önmagában is jelentős tényező lehet, például ha egy ügyfél virtuális gép egy másik Azure-régióban vagy a helyszínen. Más tényezők, például a hálózati ugrások, az internet-szolgáltató-útválasztás és az internet állapota is befolyásolhatja a teljes tárolási késést.

A késés felméréséhez először hozza létre a forgatókönyv alapmodell metrikák. Az alapkonfigurációs metrikák biztosítják a várt végpontok és kiszolgálók késését az alkalmazáskörnyezetkörnyezetben, a számítási feladatok profiljától, az alkalmazás konfigurációs beállításaitól, az ügyfélerőforrásoktól, a hálózati csőtől és más tényezőktől függően. Ha alapvonali mérőszámokkal rendelkezik, könnyebben azonosíthatja a rendellenes és a normál körülményeket. Alapkonfigurációs metrikák is lehetővé teszik, hogy megfigyelje a módosított paraméterek hatásait, például az alkalmazás konfigurációját vagy a virtuális gép mérete.

## <a name="next-steps"></a>További lépések

- [Méretezhetőségi és teljesítménycélok a Blob storage-hoz](scalability-targets.md)
- [Teljesítmény- és méretezhetőségi ellenőrzőlista a Blob storage-hoz](storage-performance-checklist.md)
