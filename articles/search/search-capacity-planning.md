---
title: A lekérdezési és indexelési számítási feladatok kapacitásának beállítása
titleSuffix: Azure Cognitive Search
description: Állítsa be a partíciós és a replika számítógép-erőforrásokat az Azure Cognitive Search alkalmazásban, ahol minden erőforrás számlázható keresési egységekben van beárazva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e2ba5301b81b1a6f5de696ab4587cd8ff43e3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462564"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>A kapacitás beállítása az Azure Cognitive Search ben

A [keresési szolgáltatás kiépítése](search-create-service-portal.md) és zárolása egy adott tarifacsomag, eltarthat néhány percig, hogy megértsék a replikák és partíciók szerepkörét a szolgáltatásban, és hogyan módosíthatja a szolgáltatást a kiugrások és az erőforrás-igény csökkenése.

A kapacitás a [kiválasztott réteg](search-sku-tier.md) (a szintek határozzák meg a hardverjellemzőket), valamint a tervezett számítási feladatokhoz szükséges replika- és partíciókombináció függvénye. A szinttől és a korrekció méretétől függően a kapacitás hozzáadása vagy csökkentése 15 perctől több óráig is eltarthat. 

Replikák és partíciók lefoglalásának módosításakor azt javasoljuk, hogy az Azure Portal használatával. A portál korlátozza a megengedhető kombinációk, amelyek a réteg maximális korlátjai alatt maradnak. Azonban ha egy parancsfájl-alapú vagy kódalapú kiépítési megközelítés, az [Azure PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternatív megoldások.

## <a name="terminology-replicas-and-partitions"></a>Terminológia: replikák és partíciók

|||
|-|-|
|*Partíciók* | Indextárolót és I/O-t biztosít az olvasási/írási műveletekhez (például index újraépítésekor vagy frissítésénekor). Minden partíció rendelkezik egy-egy részesedéssel a teljes indexből. Ha három partíciót foglal le, az index harmadokra van osztva. |
|*Replikák* | A keresési szolgáltatás példányai, amelyek elsősorban a terheléselosztási lekérdezési műveletekhez használatosak. Minden replika egy index egy példánya. Ha három replikát foglal le, a lekérdezési kérelmek kiszolgálásához rendelkezésre álló index három példánya lesz.|

## <a name="when-to-add-nodes"></a>Mikor kell csomópontokat hozzáadni?

Kezdetben egy szolgáltatás egy partícióból és egy replikaból álló erőforrások minimális szintjét rendeli hozzá. 

Egyetlen szolgáltatásnak elegendő erőforrással kell rendelkeznie az összes számítási feladat (indexelés és lekérdezés) kezeléséhez. Egyik munkaterhelés sem fut a háttérben. Ütemezheti az indexelést az olyan időszakokra, amikor a lekérdezési kérelmek természetesen ritkábbak, de a szolgáltatás egyébként nem rangsorolja az egyik feladatot a másikkal szemben. Emellett egy bizonyos mennyiségű redundancia kiegyenlíti a lekérdezési teljesítményt, amikor a szolgáltatások vagy csomópontok belső frissítése.

Általános szabály, hogy a keresési alkalmazások általában több replikák, mint a partíciók, különösen akkor, ha a szolgáltatási műveletek elfogult a lekérdezési számítási feladatok. A [magas rendelkezésre állásról](#HA) szóló rész elmagyarázza, hogy miért.

További replikák vagy partíciók hozzáadása növeli a szolgáltatás futtatásának költségeit. Győződjön meg róla, hogy ellenőrizze a [díjkalkulátor,](https://azure.microsoft.com/pricing/calculator/) hogy megértsék a számlázási következményeit további csomópontok hozzáadása. Az [alábbi táblázat](#chart) segítségével összevetheti az adott konfigurációhoz szükséges keresési egységek számát.

## <a name="how-to-allocate-replicas-and-partitions"></a>Replikák és partíciók lefoglalása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és válassza ki a keresési szolgáltatást.

1. A **Beállítások párbeszédpanelen**nyissa meg a **Méretezés** lapot a replikák és partíciók módosításához. 

   A következő képernyőképen egy szabványos szolgáltatás egy replika és partíció. Az alsó képlet azt jelzi, hogy hány keresési egységet használ (1). Ha az egységár 100 dollár (nem valós ár), a szolgáltatás futtatásának havi költsége átlagosan 100 usd lenne.

   ![Az aktuális értékeket megjelenítő oldal méretezése](media/search-capacity-planning/1-initial-values.png "Az aktuális értékeket megjelenítő oldal méretezése")

1. A csúszkával növelheti vagy csökkentheti a partíciók számát. Az alsó képlet azt jelzi, hogy hány keresési egységet használ.

   Ez a példa megduplázza a kapacitást, két replikával és partícióval. Figyelje meg a keresési egységek számát; ez most négy, mert a számlázási képlet replikák szorozva partíciók (2 x 2). A kapacitás megduplázása több mint megduplázza a szolgáltatás futtatásának költségét. Ha a keresési egység költsége 100 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 Dollár ( 100 0

   Az egyes szintek egységenkénti költségeiről a Díjszabás oldalon lehet [felkeresni.](https://azure.microsoft.com/pricing/details/search/)

   ![Replikák és partíciók hozzáadása](media/search-capacity-planning/2-add-2-each.png "Replikák és partíciók hozzáadása")

1. A módosítások megerősítéséhez kattintson a **Mentés** gombra.

   ![A méretezés és a számlázás módosításának megerősítése](media/search-capacity-planning/3-save-confirm.png "A méretezés és a számlázás módosításának megerősítése")

   A kapacitás változásai több órát vesznek igénybe. A folyamat indítása után nem szakítható meg, és nincs valós idejű figyelés a replika- és partíciókorrekciókhoz. A következő üzenet azonban látható marad, amíg a módosítások folyamatban vannak.

   ![Állapotüzenet a portálon](media/search-capacity-planning/4-updating.png "Állapotüzenet a portálon")

> [!NOTE]
> A szolgáltatás kiépítése után nem frissíthető magasabb szintre. Létre kell hoznia egy keresési szolgáltatást az új rétegen, és újra be kell töltenie az indexeket. A szolgáltatáskiépítéssel kapcsolatos segítségért tekintse [meg az Azure Cognitive Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című témakört.
>
> Emellett a partíciókat és a replikákat kizárólag és belsőleg kezeli a szolgáltatás. Nincs fogalma a processzor affinitás, vagy hozzárendeli a számítási feladatok egy adott csomóponthoz.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció- és replikakombinációk

Egy alapszintű szolgáltatás pontosan egy partíciót és legfeljebb három replikák, legfeljebb három SUs legfeljebb három SUs. Az egyetlen állítható erőforrás a replikák. Legalább két replikára van szükség a lekérdezések magas rendelkezésre állásához.

Minden standard és tárolási optimalizált keresési szolgáltatások feltételezik a replikák és partíciók következő kombinációi, a 36-SU korláttól függ. 

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíció** | **6 partíció** | **12 partíció** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 kópia** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 kópia** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replika** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 kópia** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 kópia** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

SUs, díjszabás és kapacitás részletesen ismerteti az Azure-webhely. További információ: [Árak részletei](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és partíciók száma egyenletesen oszlik 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek az az oka, hogy az Azure Cognitive Search előre felosztja az egyes indexeket 12 szegmensre, így egyenlő arányban osztható konként az összes partíció között. Például ha a szolgáltatás három partíciót tartalmaz, és létrehoz egy indexet, minden partíció négy szegmensek az index. Hogyan Azure Cognitive Search szilánkok egy index egy megvalósítási részletesen, változhat a jövőbeli kiadásokban. Bár a szám 12 ma, akkor nem várható, hogy ez a szám mindig 12 a jövőben.
>

<a id="HA"></a>

## <a name="high-availability"></a>Magas rendelkezésre állás

Mivel a rendszer könnyen és viszonylag gyorsan skálázható, általában azt javasoljuk, hogy egy partícióval és egy vagy két replikával kezdje, majd a lekérdezési kötetek létrehozása kor. A lekérdezési munkaterhelések elsősorban replikákon futnak. Ha több átviteli vagy magas rendelkezésre állású, valószínűleg további replikákra lesz szüksége.

A magas rendelkezésre állásra vonatkozó általános ajánlások a következők:

* Két replikák magas rendelkezésre állású csak olvasható számítási feladatok (lekérdezések)

* Három vagy több kópia az olvasási/írási számítási feladatok magas rendelkezésre állásához (lekérdezések, valamint indexelés az egyes dokumentumok hozzáadásakor, frissítésekor vagy törléseként)

Az Azure Cognitive Search szolgáltatásiszint-szerződései (SLA) a lekérdezési műveletekre és a dokumentumok hozzáadásából, frissítéséből vagy törléséből álló indexfrissítésekre irányulnak.

Alapszintű felsők egy partíción és három replikák. Ha azt szeretné, hogy a rugalmasság azonnal reagáljon az indexelési és a lekérdezési átviteli igény ingadozásaira, fontolja meg a Standard szintek egyikét.  Ha úgy találja, hogy a tárolási követelmények sokkal gyorsabban növekednek, mint a lekérdezési átviteli, fontolja meg a Storage-optimalizált rétegek egyikét.

## <a name="disaster-recovery"></a>Vészhelyreállítás

Jelenleg nincs beépített mechanizmus a vész-helyreállítási. Partíciók vagy replikák hozzáadása nem megfelelő stratégia a vész-helyreállítási célok eléréséhez. A leggyakoribb módszer a redundancia hozzáadása a szolgáltatás szintjén egy második keresési szolgáltatás beállításával egy másik régióban. Az index újraépítése során rendelkezésre álláshoz, az átirányítási vagy feladatátvételi logikának a kódból kell származnia.

## <a name="estimate-replicas"></a>Kópiák becslése

Egy éles szolgáltatás, le kell foglalnia három replikák SLA célokra. Ha lassú lekérdezési teljesítményt tapasztal, replikákat adhat hozzá, hogy az index további példányai online állapotba kerülhessenek a nagyobb lekérdezési munkaterhelések támogatásához és a kérelmek több replikán keresztüli terhelésének terheléséhez.

Nem adunk útmutatást, hogy hány replikák szükségesek a lekérdezési terhelések elhelyezésére. A lekérdezési teljesítmény a lekérdezés és a versengő számítási feladatok összetettségétől függ. Bár a replikák hozzáadása egyértelműen jobb teljesítményt eredményez, az eredmény nem szigorúan lineáris: három replikák hozzáadása nem garantálja a háromszoros átviteli.

A megoldás QPS-ének becsléséhez a [Teljesítmény méretezése](search-performance-optimization.md)és [a Lekérdezésfigyelés című témakörben talál útmutatást.](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Partíciók becslése

A [kiválasztott réteg](search-sku-tier.md) határozza meg a partíció méretét és sebességét, és minden réteg a különböző forgatókönyvekhez illeszkedő jellemzők köré van optimalizálva. Ha úgy dönt, egy magasabb szintű réteg, előfordulhat, hogy kevesebb partíciót, mint ha megy az S1. Az egyik kérdés, amit meg kell válaszolni a saját irányított tesztelés, hogy egy nagyobb és drágább partíció jobb teljesítményt eredményez, mint két olcsóbb partíciók egy alacsonyabb szinten kiosztott szolgáltatás.

A közel valós idejű adatfrissítést igénylő keresési alkalmazásoknak arányosan több partícióra van szükségük, mint a replikákra. Partíciók hozzáadása több számítási erőforrás olvasási/írási műveleteit osztja el. Emellett több lemezterületet biztosít a további indexek és dokumentumok tárolásához.

A nagyobb indexek lekérdezése hosszabb időt vesz igénybe. Így előfordulhat, hogy a partíciók minden növekményes növekedése a replikák kisebb, de arányos növekedését igényli. A lekérdezések és a lekérdezési kötet összetettsége figyelembe veszi, hogy milyen gyorsan fordul meg a lekérdezés végrehajtása.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search díjszabási szintjének kiválasztása](search-sku-tier.md)