---
title: A kapacitás módosítása a lekérdezési és indexelési feladatokhoz
titleSuffix: Azure Cognitive Search
description: A partíciós és a replika számítógép erőforrásainak módosítása az Azure Cognitive Searchban, ahol az egyes erőforrások számlázható keresési egységekben vannak.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: bc90ecb029afe70ed61e94a727c67c53bb968b96
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212550"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Kapacitás módosítása az Azure-ban Cognitive Search

A [keresési szolgáltatás](search-create-service-portal.md) üzembe helyezése és egy adott díjszabási szinten való zárolása előtt szánjon néhány percet a szolgáltatásbeli replikák és partíciók szerepének megismerésére, akár arányos, akár gyorsabb partícióra van szüksége, és hogyan konfigurálhatja a szolgáltatást a várt terheléshez.

A kapacitás a [kiválasztott rétegek](search-sku-tier.md) függvénye (a szintek határozzák meg a hardver jellemzőit), valamint a tervezett munkaterhelésekhez szükséges replika és partíciós kombinációt. Ez a cikk a replika-és partíciós kombinációkkal és interakciókkal foglalkozik.

## <a name="terminology-replicas-and-partitions"></a>Terminológia: replikák és partíciók

|||
|-|-|
|*Partíciók* | Az írási és olvasási műveletekhez (például az indexek újraépítésekor vagy frissítésekor) index-tárolót és I/O-t biztosít. Mindegyik partíció rendelkezik a teljes index egy megosztásával. Ha három partíciót foglal le, az index a harmadikra van osztva. |
|*Replikák* | A keresési szolgáltatás azon példányai, amelyek elsődlegesen a lekérdezési műveletek terheléselosztására szolgálnak. Minden replika egy index egy példánya. Ha három replikát oszt ki, akkor a lekérdezési kérések kiszolgálásához három példánya lesz elérhető.|

## <a name="how-to-allocate-replicas-and-partitions"></a>Replikák és partíciók lefoglalása

A szolgáltatás eredetileg egy partíciót és egy replikát tartalmazó minimális szintű erőforrásokat foglal le. 

Egyetlen szolgáltatásnak elegendő erőforrással kell rendelkeznie az összes számítási feladat (indexelés és lekérdezések) kezeléséhez. Egyik munkaterhelés sem fut a háttérben. Az indexelést ütemezhet olyan időpontokra, amikor a lekérdezési kérések természetesen ritkábbak, de a szolgáltatás más módon nem rangsorolja az egyik feladatot.

A replikák és partíciók kiosztásának módosításakor javasoljuk, hogy használja a Azure Portal. A portál kényszeríti az olyan megengedett kombinációk korlátozásait, amelyek az adott szinten maximális korláton belül maradnak. Ha azonban parancsfájl-vagy programkód-alapú létesítési megközelítésre van szüksége, akkor a [Azure PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatív megoldás.

Általános szabályként a keresési alkalmazások általában több replikát igényelnek, mint a partíciók, különösen akkor, ha a szolgáltatási műveletek a lekérdezési munkaterhelések szempontjából elfogultak. A [magas rendelkezésre állásról](#HA) szóló szakasz ismerteti, hogy miért.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.

1. A **Beállítások**területen nyissa meg a **skálázás** lapot a replikák és partíciók módosításához. 

   Az alábbi képernyőfelvételen egy replikával és partícióval kiépített szabványos szolgáltatás látható. A lenti képlet azt jelzi, hogy hány keresési egység van használatban (1). Ha az egység ára $100 (nem valós díj), akkor a szolgáltatás futtatásának havi költsége átlagosan $100.

   ![Az aktuális értékeket mutató méretezési oldal](media/search-capacity-planning/1-initial-values.png "Az aktuális értékeket mutató méretezési oldal")

1. A csúszka használatával növelheti vagy csökkentheti a partíciók számát. A lenti képlet azt jelzi, hogy hány keresési egységet használ a rendszer.

   Ez a példa dupla kapacitást, két replikával és partícióval rendelkezik. Figyelje meg a keresési egységek darabszámát; most négy, mert a számlázási képlet a replikák szorozza a partíciókkal (2 x 2). A kapacitás megkettőzése több, mint megduplázza a szolgáltatás futtatásának költségeit. Ha a keresési egység díja $100 volt, az új havi számla mostantól $400 lesz.

   Az egyes szintek aktuális egységenkénti költségeiért látogasson el a [díjszabási oldalra](https://azure.microsoft.com/pricing/details/search/).

   ![Replikák és partíciók hozzáadása](media/search-capacity-planning/2-add-2-each.png "Replikák és partíciók hozzáadása")

1. Kattintson a **Save (Mentés** ) gombra a módosítások megerősítéséhez.

   ![A skálázás és a számlázás változásainak megerősítése](media/search-capacity-planning/3-save-confirm.png "A skálázás és a számlázás változásainak megerősítése")

   A kapacitás változásai több órát is igénybe vehetik. A folyamat elindítása után nem lehet megszakítani a műveletet, és nincs valós idejű figyelés a replika és a partíciók beállításaihoz. A következő üzenet azonban továbbra is látható, amíg megváltozik.

   ![Állapotjelző üzenet a portálon](media/search-capacity-planning/4-updating.png "Állapotjelző üzenet a portálon")

> [!NOTE]
> A szolgáltatás üzembe helyezését követően nem lehet magasabb szintre frissíteni. Létre kell hoznia egy keresési szolgáltatást az új szinten, és újra kell töltenie az indexeket. A szolgáltatás kiépítési támogatásához tekintse meg az [Azure Cognitive Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című témakört.
>
> Emellett a partíciókat és a replikákat a szolgáltatás kizárólag és belsőleg kezeli. A processzor affinitása nem áll rendelkezésre, vagy a számítási feladatok egy adott csomóponthoz rendelhetők hozzá.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció-és replika-kombinációk

Az alapszintű szolgáltatásoknak pontosan egy partíciójuk és legfeljebb három replikájuk lehet, legfeljebb három SUs esetén. Az egyetlen állítható erőforrás replika. A lekérdezésekben a magas rendelkezésre állás érdekében legalább két replikára van szükség.

A standard és a Storage-alapú optimalizált keresési szolgáltatások a 36-SU korlátnak megfelelő replikák és partíciók következő kombinációit vehetik igénybe. 

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíció** | **6 partíció** | **12 partíció** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replika** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replika** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replika** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 replika** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 replika** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

Az SUs, a díjszabás és a kapacitás részletes ismertetését az Azure webhelyén találja. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és partíciók száma egyenletesen oszlik el a 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek az az oka, hogy az Azure Cognitive Search az egyes indexeket 12 szegmensre osztja, így a partíciók között egyenlő arányban terjedhet. Ha például a szolgáltatás három partícióval rendelkezik, és létrehoz egy indexet, minden partíció az index négy szegmensét fogja tartalmazni. Az Azure Cognitive Search az indexek egy implementációs részletességgel rendelkeznek, amely a jövőbeli kiadásokban változhat. Bár a szám 12 ma, nem várható, hogy ez a szám mindig 12 lesz a jövőben.
>

<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás

Mivel a méretezés egyszerűen és viszonylag gyorsan megoldható, általában azt javasoljuk, hogy először egy partíciót és egy vagy két replikát válasszon, majd a skálázási folyamat létrehozásakor. A lekérdezési feladatok elsődlegesen a replikákat futtatják. Ha nagyobb átviteli sebességre vagy magas rendelkezésre állásra van szüksége, valószínűleg további replikákat kell megadnia.

A magas rendelkezésre állásra vonatkozó általános javaslatok a következők:

* Két replika a csak olvasási terhelések magas rendelkezésre állásához (lekérdezések)

* Három vagy több replika az olvasási/írási munkaterhelések magas rendelkezésre állásához (lekérdezések és az indexelés egyéni dokumentumok hozzáadása, frissítése vagy törlése)

Az Azure Cognitive Search szolgáltatói szerződések (SLA) a lekérdezési műveletekre és a dokumentumok hozzáadását, frissítését vagy törlését tartalmazó index-frissítésekre irányulnak.

Az alapszintű csomag egy partíción és három replikán található. Ha azt szeretné, hogy a rugalmasság azonnal reagáljon az indexelési és a lekérdezési átviteli sebesség igény szerinti ingadozására, vegye figyelembe az egyik standard szintet is.  Ha úgy találja, hogy a tárolási követelmények sokkal gyorsabban növekednek, mint a lekérdezési átviteli sebesség, vegye figyelembe a tárterület optimalizált szintjeinek egyikét.

## <a name="disaster-recovery"></a>Vészhelyreállítás

Jelenleg nincs beépített mechanizmus a vész-helyreállításhoz. Partíciók vagy replikák hozzáadása nem megfelelő stratégiát jelentene a vész-helyreállítási célkitűzések teljesítéséhez. A leggyakoribb módszer a redundancia a szolgáltatási szinten való hozzáadása egy másik régióban található második keresési szolgáltatás beállításával. Az indexek újraépítése során a rendelkezésre álláshoz hasonlóan az átirányítás vagy a feladatátvételi logikának a kódból kell származnia.

## <a name="estimate-replicas"></a>Replikák becslése

Üzemi szolgáltatás esetén három replikát kell kiosztania SLA-célokból. Ha lassú lekérdezési teljesítményre van szüksége, az egyik megoldás az, hogy replikákat adjon hozzá, hogy az index további példányai online állapotba kerüljenek a nagyobb lekérdezési feladatokhoz, valamint a kérelmek terheléselosztásához a több replikán.

Nem biztosítunk útmutatást arra vonatkozóan, hogy hány replikára van szükség a lekérdezési terhelések fogadásához. A lekérdezési teljesítmény a lekérdezés és a versengő számítási feladatok összetettsége alapján változhat. Bár a replikák hozzáadása egyértelműen jobb teljesítményt eredményez, az eredmény nem feltétlenül lineáris: három replika hozzáadása nem garantálja a háromszoros átviteli sebességet.

A megoldás QPS becslésével kapcsolatos útmutatásért lásd: [Méretezés a teljesítményhez](search-performance-optimization.md)és a [figyelési lekérdezések](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Partíciók becslése

A [kiválasztott](search-sku-tier.md) keret határozza meg a partíció méretét és sebességét, az egyes szintek pedig a különböző forgatókönyvekhez illeszkedő jellemzők körén vannak optimalizálva. Ha magasabb szintű szintet választ, előfordulhat, hogy kevesebb partícióra van szüksége, mint ha S1-et használ.

A közel valós idejű adatfrissítést igénylő alkalmazások kereséséhez a replikák több partícióra van szükségük. A partíciók hozzáadásával az írási/olvasási műveletek nagyobb számú számítási erőforráson keresztül terjednek ki. Emellett további lemezterületet biztosít a további indexek és dokumentumok tárolásához.

A nagyobb indexek lekérése hosszabb időt vesz igénybe. Ezért előfordulhat, hogy a partíciók növekményes növekedésének a replikák kisebb, de arányos növekedése szükséges. A lekérdezések és a lekérdezési kötetek összetettsége azt eredményezi, hogy milyen gyorsan történik a lekérdezés végrehajtása.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Válasszon árképzési szintet az Azure Cognitive Search](search-sku-tier.md)