---
title: Magas rendelkezésre állás a Azure Cosmos DBban
description: Ez a cikk azt ismerteti, hogy a Azure Cosmos DB hogyan biztosít magas rendelkezésre állást
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 3f882375197fa45cfbc74ff7a80ed33fd33f33a3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400298"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hogyan biztosítja a Azure Cosmos DB magas rendelkezésre állást? 

Azure Cosmos DB transzparens módon replikálja az adatait az Azure Cosmos-fiókjához társított összes Azure-régióban. Azure Cosmos DB a következő képen látható módon több redundancia-réteget alkalmaz az adatokhoz:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fizikai particionálás" border="false":::

- Az Azure Cosmos-tárolókban lévő adatkeretek [horizontálisan particionálva](partitioning-overview.md)vannak.

- A partíciók készlete több replika-készlet gyűjteménye. Minden egyes régión belül minden partíciót egy replikával véd, és a replikák többsége által véglegesített összes írás replikálva és tartósan. A replikák több mint 10-20 tartalék tartományba vannak elosztva.

- Az összes régió összes partíciója replikálódik. Mindegyik régió egy Azure Cosmos-tároló összes adatpartícióját tartalmazza, és írási és olvasási műveleteket is képes fogadni.  

Ha az Azure Cosmos-fiókját *N* Azure-régióban osztják el, az összes adatnak legalább *N* x 4 példánya lesz. Általában a több mint 2 régióban található Azure Cosmos-fiók javítja az alkalmazás rendelkezésre állását, és kis késleltetést biztosít a kapcsolódó régiók között. 

## <a name="slas-for-availability"></a>SLA-kat a rendelkezésre álláshoz

Globálisan elosztott adatbázisként Azure Cosmos DB átfogó SLA-kat biztosít, amelyek az átviteli sebességre, a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állásra vonatkozó késést foglalnak magukban. Az alábbi táblázat az egyes és a többrégiós fiókok Azure Cosmos DB által biztosított magas rendelkezésre állási garanciákat mutatja be. A magas rendelkezésre állás érdekében mindig konfigurálja az Azure Cosmos-fiókokat több írási régióval (más néven több főkiszolgálóval).

|Művelettípus  | Egyetlen régió |Több régió (egyrégiós írások)|Többrégiós (több régiós írások) |
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban a kötött elavulás, munkamenet, konzisztens előtag és végleges konzisztencia-modell tényleges írási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat. Az összes konzisztencia tényleges olvasási rendelkezésre állása jelentősen meghaladja a közzétett SLA-kat.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Magas rendelkezésre állás a Azure Cosmos DB a regionális kimaradások esetén

A regionális leállás ritka eseteiben Azure Cosmos DB biztosítja, hogy az adatbázis mindig rendelkezésre álljon. Az alábbi részletek az Azure Cosmos-fiók konfigurációjától függően a leállás során Azure Cosmos DB viselkedést rögzítik:

- Ha Azure Cosmos DB, az írási művelet elfogadását megelőzően az ügyfél számára az adatok tartósan a régióban lévő replikák kvóruma végzi el, amely elfogadja az írási műveleteket.

- Az írási és olvasási műveletek esetében a többrégiós fiókok több írható régióval vagy több főkiszolgálóval is elérhetők. A regionális feladatátvételek azonnaliek, és nem igényelnek semmilyen változást az alkalmazásból.

- Az egyrégiós fiókok a regionális leállás után elveszíthetik a rendelkezésre állást. A magas rendelkezésre állás biztosítása érdekében mindig ajánlott **legalább két régiót** (lehetőleg legalább két írási régiót) beállítani az Azure Cosmos-fiókkal.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Többrégiós fiókok egyetlen írási régióval (írási régió kimaradása)

- Az írási régió meghibásodása során az Azure Cosmos-fiók automatikusan előléptet egy másodlagos régiót az új elsődleges írási régiónak, ha az **automatikus feladatátvétel engedélyezése** az Azure Cosmos-fiókban be van állítva. Ha ez a beállítás engedélyezve van, a feladatátvétel egy másik régióba kerül a megadott régió-prioritás sorrendjében.
- Ha a korábban érintett régió újra online állapotba került, az [ütközések csatornán](how-to-manage-conflicts.md#read-from-conflict-feed)keresztül elérhetővé tett írási és olvasási műveletek a régió hibája miatt nem replikálódnak. Az alkalmazások elolvashatják az ütközések csatornáját, elhárítják az alkalmazás-specifikus logikán alapuló ütközéseket, és szükség szerint visszaírják a frissített információt az Azure Cosmos-tárolóba.
- A korábban érintett írási régió helyreállítása után a rendszer automatikusan elérhetővé válik olvasási régióként. Az írási régióként visszaválthat a visszaállított régióra. A régiókat a [PowerShell, az Azure CLI vagy a Azure Portal](how-to-manage-database-account.md#manual-failover)használatával állíthatja át. Az írási régió és az alkalmazás továbbra is rendelkezésre áll, amíg az **adatvesztés és a rendelkezésre állás még nem** érhető el.

> [!IMPORTANT]
> Határozottan javasoljuk, hogy az **automatikus feladatátvétel engedélyezéséhez**konfigurálja az éles számítási feladatokhoz használt Azure Cosmos-fiókokat. A manuális feladatátvételhez kapcsolat szükséges a másodlagos és az elsődleges írási régió között a konzisztencia-ellenőrzés elvégzéséhez, hogy a feladatátvétel során ne legyen adatvesztés. Ha az elsődleges régió nem érhető el, ez a konzisztencia-ellenőrzés nem hajtható végre, és a manuális feladatátvétel nem fog sikerülni, ami az írási rendelkezésre állás elvesztését eredményezi.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Többrégiós fiókok egyetlen írási régióval (olvasási régió kimaradása)

- Az olvasási régió meghibásodása esetén az Azure Cosmos-fiókok bármely konzisztencia-szintet, vagy a három vagy több olvasási régióval való erős konzisztencia esetén is magas rendelkezésre állást biztosítanak az olvasáshoz és íráshoz.
- Az Azure Cosmos-fiókok erős konzisztencia használatával két vagy kevesebb olvasási régióval rendelkeznek (amely magában foglalja az olvasási & írási régiót is), az olvasási régió meghibásodása során elveszítik az írási írási rendelkezésre állást.
- Az érintett régiót a rendszer automatikusan leválasztja, és offline állapotba állítja. A [Azure Cosmos db SDK](sql-api-sdk-dotnet.md) -k átirányítják az olvasási hívásokat a következő elérhető régióba az előnyben részesített régiók listájában.
- Ha a régiók rangsorolt listáján egyetlen régió sem érhető el, a beérkező kérések automatikusan az aktuális írási régióra váltanak vissza.
- Az olvasási régió meghibásodásának kezeléséhez nincs szükség módosításra az alkalmazás kódjában. Amikor az érintett olvasási régió újra online állapotba kerül, automatikusan szinkronizál az aktuális írási régióval, és ismét elérhető lesz az olvasási kérések kiszolgálásához.
- A további olvasásokat a szolgáltatás a helyreállt régiókhoz irányítja át anélkül, hogy módosítania kellene az alkalmazáskódot. Egy korábban sikertelen régió feladatátvétele és újracsatlakozása során a konzisztencia-garanciák továbbra is tiszteletben maradnak Azure Cosmos DB.
- Még egy ritka és szerencsétlen eseményen is, amikor az Azure-régió véglegesen behajthatatlan, nincs adatvesztés, ha a többrégiós Azure Cosmos-fiók *erős* konzisztencia-konfigurációval van konfigurálva. Ha tartósan letiltott írási régiót használ, egy többrégiós Azure Cosmos-fiók, amely a határértékek konzisztenciájával van konfigurálva, a lehetséges adatvesztési időszak az elavult (*k* vagy *T*) értékre van korlátozva, ahol k = 100000 frissítés és T = 5 perc. A munkamenet, a konzisztens előtag és a végleges konzisztencia-szintek esetében a lehetséges adatvesztési időszak legfeljebb 15 percet vesz igénybe. További információ a Azure Cosmos DB RTO és RPO céljairól: a [konzisztencia szintjei és az adatok tartóssága](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Rendelkezésre állási zóna támogatása

A régiók közötti rugalmasság mellett mostantól engedélyezheti a **zónák redundanciát** az Azure Cosmos-adatbázishoz társítandó régió kiválasztásakor.

A rendelkezésre állási zónák támogatásával Azure Cosmos DB biztosítja, hogy a replikák egy adott régióban több zónába kerüljenek, hogy magas rendelkezésre állást és rugalmasságot biztosítson a zónákon belüli meghibásodások során. Ebben a konfigurációban nem változnak a késés és a többi SLA. Egyetlen zóna meghibásodása esetén a Zone redundancia teljes körű adattartósságot biztosít a RPO = 0 és a rendelkezésre állás RTO = 0 segítségével való elérhetősége esetén.

A zóna-redundancia a [többrégiós írási funkciók replikálásának](how-to-multi-master.md) *kiegészítő* funkciója. A zónák redundancia önmagában nem lehet a regionális rugalmasság elérésére támaszkodni. Ha például regionális kimaradások vagy kis késleltetésű hozzáférés van a régiók között, azt javasoljuk, hogy több írási régióval is rendelkezzen a zóna redundancia mellett.

Ha többrégiós írásokat konfigurál az Azure Cosmos-fiókhoz, külön díj nélkül is dönthet a zóna-redundancia szolgáltatásban. Ellenkező esetben tekintse meg az alábbi megjegyzést a zóna redundancia támogatásának díjszabását illetően. Az Azure Cosmos-fiók meglévő régiójába engedélyezheti a zóna redundanciát, ha eltávolítja a régiót, és újból hozzáadja a zóna redundancia beállítással.

Ez a funkció a következő helyen érhető el: *Egyesült Királyság déli régiója, Délkelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Nyugat-Európa, Nyugat-USA 2, Kelet-Japán, Észak-Európa, Közép-Franciaország, Kelet-Ausztrália, 2. keleti* régió

> [!NOTE]
> Az egyetlen régióhoz tartozó Azure Cosmos-fiók Availability Zonesának engedélyezése olyan díjakat eredményez, amelyek egy további régiónak a fiókhoz való hozzáadásával egyenértékűek. A díjszabással kapcsolatos részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) és a [többrégiós költségeket Azure Cosmos db](optimize-cost-regions.md) cikkekben.

A következő táblázat összefoglalja a különböző fiókok konfigurációinak magas rendelkezésre állási képességét:

|KPI  |Egyetlen régió Availability Zones nélkül (nem AZ)  |Egyetlen régió Availability Zones (AZ)  |Multi-region writes with Availability Zones (AZ, 2 Regions) – a javasolt beállítás |
|---------|---------|---------|---------|
|Rendelkezésre állási SLA írása | 99,99% | 99,99% | 99.999% |
|Rendelkezésre állási SLA olvasása  | 99,99% | 99,99% | 99.999% |
|Ár | Egyetlen régió számlázási díja | Az önálló régió rendelkezésre állási zónájának számlázási sebessége | Többrégiós számlázási díj |
|Zónák hibái – adatvesztés | Adatvesztés | Nincs adatvesztés | Nincs adatvesztés |
|Zónák hibái – rendelkezésre állás | Rendelkezésre állás elvesztése | Nincs rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség |
|Olvasási késés | Régiók közötti régió | Régiók közötti régió | Alacsony |
|Írási késés | Régiók közötti régió | Régiók közötti régió | Alacsony |
|Regionális leállás – adatvesztés | Adatvesztés |  Adatvesztés | Adatvesztés <br/><br/> Ha a kötött elavulás konzisztenciáját több írási régióval és több régióval együtt használja, az adatvesztés a fiókon beállított korláton belülre korlátozódik. <br /><br />A regionális leállás során elkerülhető az adatvesztés azáltal, hogy erős konzisztenciát konfigurál több régióval. Ez a lehetőség olyan kompromisszumokat tartalmaz, amelyek befolyásolják a rendelkezésre állást és a teljesítményt. Csak az egyrégiós írásokhoz konfigurált fiókokon konfigurálható. |
|Regionális leállás – rendelkezésre állás | Rendelkezésre állás elvesztése | Rendelkezésre állás elvesztése | Nincs rendelkezésre állási veszteség |
|Teljesítmény | X RU/s kiosztott átviteli sebesség | X RU/s kiosztott átviteli sebesség | 2X RU/s kiosztott átviteli sebesség <br/><br/> Ennek a konfigurációs módnak kétszer kell megfelelnie az átviteli sebességnek, ha egyetlen régióhoz képest Availability Zones van, mert két régió van. |

> [!NOTE]
> Ha engedélyezni szeretné a rendelkezésre állási zóna támogatását egy több régióból álló Azure Cosmos-fiók esetében, a fióknak engedélyezve kell lennie a többrégiós írási írásoknak.

A zóna redundancia engedélyezhető, ha új vagy meglévő Azure Cosmos-fiókokhoz ad hozzá régiót. Ha engedélyezni szeretné a zóna redundanciát az Azure Cosmos-fiókjában, állítsa be a `isZoneRedundant` jelölőt `true` egy adott helyre. Ezt a jelzőt a Locations (helyszínek) tulajdonságon belül állíthatja be. A következő PowerShell-kódrészlet például lehetővé teszi a zóna redundanciát a "Délkelet-ázsiai" régióban:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

A következő parancs bemutatja, hogyan engedélyezhető a zóna redundancia a "EastUS" és a "WestUS2" régiók számára:

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Az Azure Cosmos-fiók létrehozásakor Azure Portal használatával engedélyezheti Availability Zones. Fiók létrehozásakor ügyeljen arra, hogy engedélyezze a **geo-redundancia**, a **többrégiós írások**használatát, és válasszon egy régiót, ahol a Availability Zones támogatott:

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="Availability Zones engedélyezése a Azure Portal használatával"::: 

## <a name="building-highly-available-applications"></a>Magasan elérhető alkalmazások fejlesztése

- Tekintse át az [Azure Cosmos SDK](troubleshoot-sdk-availability.md) -k várható viselkedését ezekben az eseményekben, valamint azokat a konfigurációkat, amelyek hatással vannak rá.

- A magas írási és olvasási rendelkezésre állás biztosítása érdekében konfigurálja az Azure Cosmos-fiókot úgy, hogy legalább két, több írási régióval rendelkező régióra legyen kiterjedhet. Ez a konfiguráció biztosítja a legmagasabb rendelkezésre állást, a legalacsonyabb késést és a legjobb skálázhatóságot a SLA-kat támogató olvasások és írások számára. További információ: [Az Azure Cosmos-fiók konfigurálása több írási régióval](tutorial-global-distribution-sql-api.md).

- Az egyírásos régióval konfigurált többrégiós Azure Cosmos-fiókok esetében az [Azure CLI vagy a Azure Portal használatával engedélyezze az automatikus feladatátvételt](how-to-manage-database-account.md#automatic-failover). Miután engedélyezte az automatikus feladatátvételt, ha regionális katasztrófa van, Cosmos DB automatikusan feladatátvételt hajt végre a fiókjában.  

- Még ha az Azure Cosmos-fiókja is nagyon elérhető, előfordulhat, hogy az alkalmazás nem megfelelően van kialakítva, hogy továbbra is elérhető legyen. Az alkalmazás végpontok közötti magas rendelkezésre állásának teszteléséhez az alkalmazás tesztelési vagy vész-helyreállítási (DR) gyakorlatának részeként átmenetileg tiltsa le a fiók automatikus feladatátvételét, a [PowerShell, az Azure CLI vagy a Azure Portal használatával indítsa el a manuális feladatátvételt](how-to-manage-database-account.md#manual-failover), majd figyelje az alkalmazás feladatátvételét. Ha elkészült, visszatérhet az elsődleges régióhoz, és visszaállíthatja a fiók automatikus feladatátvételét.

- Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási időre vonatkozó célkitűzés (RTO). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). A Azure Cosmos DB RPO és RTO lásd: a [konzisztencia szintjei és az adattartósság](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>További lépések

Ezután olvassa el a következő cikkeket:

- [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md)
- [Kiosztott átviteli sebesség globális méretezése](scaling-throughput.md)
- [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
- [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)
- [Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)
- [Az SDK működése többrégiós környezetekben](troubleshoot-sdk-availability.md)