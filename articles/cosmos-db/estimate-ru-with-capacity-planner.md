---
title: Becsült költségek az Azure Cosmos DB kapacitástervezőjével
description: Az Azure Cosmos DB kapacitástervező lehetővé teszi, hogy megbecsülje az átviteli -kapacitás (RU/s) szükséges és a számítási feladatok költségét. Ez a cikk ismerteti, hogyan használhatja a kapacitástervező új verzióját a szükséges átviteli és költség becsléséhez.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707629"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Ru/s becslése az Azure Cosmos DB kapacitástervezőhasználatával

Az Azure Cosmos-adatbázisok és -tárolók konfigurálása a megfelelő mennyiségű kiosztott átviteli fokkal, vagy [kérésegységek (RU/ek)](request-units.md)a számítási feladatok elengedhetetlen a költségek és a teljesítmény optimalizálásához. Ez a cikk ismerteti, hogyan használhatja az Azure Cosmos DB [kapacitástervező](https://cosmos.azure.com/capacitycalculator/) a szükséges RU/s becslésének és a számítási feladatok költségét. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Az átviteli képesség és a költség becslése az Azure Cosmos DB kapacitástervezőjével

A kapacitástervező két üzemmódban használható.

|**Mód**  |**Leírás**  |
|---------|---------|
|Basic|Gyors, magas szintű RU/s és költségbecslést biztosít. Ez a mód feltételezi az alapértelmezett Azure Cosmos DB-beállításokat a szabályzat, a konzisztencia és más paraméterek indexeléséhez. <br/><br/>Az alapmód használatával gyors, magas szintű becslést, amikor kiértékeli a potenciális számítási feladatok futtatásához az Azure Cosmos DB.|
|Speciális|Részletesebb RU/s-t és költségbecslést biztosít, amely további beállítások – indexelési házirend, konzisztenciaszint és egyéb, a költségeket és az átviteli kapacitást befolyásoló paraméterek – finomhangolásával rendelkezik. <br/><br/>Speciális módot akkor használjon, ha új projekt ru/s-ait becsüli meg, vagy részletesebb becslést szeretne. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>A kiosztott átviteli és költség becslése az alapmód használatával
Ha az alapmód használatával gyors becslést szeretne kapni a számítási feladatokról, keresse meg a [kapacitástervezőt.](https://cosmos.azure.com/capacitycalculator/) Adja meg a következő paramétereket a munkaterhelés alapján: 

|**Bemenet**  |**Leírás**  |
|---------|---------|
|Régiók száma|Az Azure Cosmos DB minden Azure-régióban elérhető. Válassza ki a számítási feladatokhoz szükséges régiók számát. A Cosmos-fiókkal tetszőleges számú régiót társíthat. További részletekért tekintse meg az Azure Cosmos DB [globális disztribúcióját.](distribute-data-globally.md)|
|Többrégiós írások|Ha engedélyezi a [többrégiós írásokat,](distribute-data-globally.md#key-benefits-of-global-distribution)az alkalmazás bármely Azure-régióba olvashat és írhat. Ha letiltja a többrégiós írásokat, az alkalmazás egyetlen régióba írhat adatokat. <br/><br/> Engedélyezze a többrégiós írási műveleteket, ha azt szeretné, hogy egy aktív-aktív számítási feladatok, amely megköveteli az alacsony késésű írási különböző régiókban. Például egy IOT-munkaterhelés, amely adatokat ír az adatbázisba nagy mennyiségű különböző régiókban. <br/><br/> Többrégiós írások garantálja 99,999% olvasási és írási elérhetőségét. Többrégiós írások igényel nagyobb átviteli képest az egyetlen írási régiók. További információ: [Miben különböznek a felelős ök az egy- és többírású régiók](optimize-cost-regions.md) cikk.|
|Tárolt adatok összesen (régiónként)|Gb-ban tárolt becsült adatok összesen egyetlen régióban.|
|Elem mérete|Az adatelem becsült mérete (pl. dokumentum), 1 KB-tól 2 MB-ig terjed. |
|Olvasás/mp régiónként|A várt olvasások száma másodpercenként. |
|Írások/mp régiónként|A másodpercenként várt írások száma. |

A szükséges adatok kitöltése után válassza a **Számítás lehetőséget.** A **Költségbecslés** lap a tárolás és a kiépített átviteli kapacitás teljes költségét jeleníti meg. Ezen a lapon a **Részletek megjelenítése** hivatkozást bonthatja ki az olvasási és írási kérelmekhez szükséges átviteli adatlebontás lekéréséhez. Minden alkalommal, amikor módosítja bármelyik mező értékét, válassza a **Számítás** lehetőséget a becsült költség újraszámításához. 

![Kapacitástervező alapmódja](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>A kiosztott átviteli és költségbecslés speciális módban

A speciális mód lehetővé teszi, hogy több olyan beállítást adjon meg, amelyek hatással vannak a RU/s becslésére. A beállítás használatához keresse meg a [kapacitástervezőt,](https://cosmos.azure.com/capacitycalculator/) és jelentkezzen be az eszközbe az Azure-hoz használt fiókkal. A bejelentkezési lehetőség a jobb sarokban érhető el. 

A bejelentkezés után további mezőket láthat az alapmódú mezőkhöz képest. Adja meg a további paramétereket a munkaterhelés alapján. 

|**Bemenet**  |**Leírás**  |
|---------|---------|
|API|Az Azure Cosmos DB egy többmodelles és több API-s szolgáltatás. Az új számítási feladatokhoz válassza az SQL (Core) API-t. |
|Régiók száma|Az Azure Cosmos DB minden Azure-régióban elérhető. Válassza ki a számítási feladatokhoz szükséges régiók számát. A Cosmos-fiókkal tetszőleges számú régiót társíthat. További részletekért tekintse meg az Azure Cosmos DB [globális disztribúcióját.](distribute-data-globally.md)|
|Többrégiós írások|Ha engedélyezi a [többrégiós írásokat,](distribute-data-globally.md#key-benefits-of-global-distribution)az alkalmazás bármely Azure-régióba olvashat és írhat. Ha letiltja a többrégiós írásokat, az alkalmazás egyetlen régióba írhat adatokat. <br/><br/> Engedélyezze a többrégiós írási műveleteket, ha azt szeretné, hogy egy aktív-aktív számítási feladatok, amely megköveteli az alacsony késésű írási különböző régiókban. Például egy IOT-munkaterhelés, amely adatokat ír az adatbázisba nagy mennyiségű különböző régiókban. <br/><br/> Többrégiós írások garantálja 99,999% olvasási és írási elérhetőségét. Többrégiós írások igényel nagyobb átviteli képest az egyetlen írási régiók. További információ: [Miben különböznek a felelős ök az egy- és többírású régiók](optimize-cost-regions.md) cikk.|
|Alapértelmezett konzisztencia|Az Azure Cosmos DB 5 konzisztenciaszintet támogat, hogy a fejlesztők egyensúlyba hozhassák a konzisztencia, a rendelkezésre állás és a késés közötti kompromisszumot. További információ: [a konzisztenciaszintek](consistency-levels.md) című cikk. <br/><br/> Alapértelmezés szerint az Azure Cosmos DB munkamenet-konzisztenciát használ, amely garantálja a saját írási műveletek olvasását egy munkamenetben. <br/><br/> Erős vagy kötött frissesség kiválasztása esetén az olvasáshoz a szükséges RU/s kétszerese szükséges, a munkamenethez, a konzisztens előtaghoz és a végleges konzisztenciához képest. A többrégiós írások erős konzisztenciája nem támogatott, és automatikusan erős konzisztenciával rendelkező egyrégiós írásoklesznek. |
|Indexelési szabályzat|Alapértelmezés szerint az Azure Cosmos DB [indexeli az összes elemet](index-policy.md) a rugalmas és hatékony lekérdezések (leképezések az **automatikus** indexelési szabályzat) összes tulajdonságát indexeli. <br/><br/> Ha **a kilehetőséget választja,** egyik tulajdonság sem lesz indexelve. Ez azt eredményezi, hogy a legalacsonyabb ru díj írási. Válassza **off** ki a házirendet, ha várhatóan csak [pontolvasásokat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (kulcsérték-lekérdezéseket) és/vagy írásokat végez, és nem végez lekérdezéseket. <br/><br/> Egyéni indexelési szabályzat lehetővé teszi, hogy az alacsonyabb írási átviteli és tárolási index bizonyos tulajdonságokat vegyen fel vagy zárjon ki. További információ: [indexelési szabályzatok](index-overview.md) és [minta indexelési szabályzatok](how-to-manage-indexing-policy.md#indexing-policy-examples) cikkek.|
|Tárolt adatok összesen (régiónként)|Gb-ban tárolt becsült adatok összesen egyetlen régióban.|
|Munkaterhelés mód|Válassza **a Folyamatos lehetőséget,** ha a munkaterhelés mennyisége állandó. <br/><br/> Válassza **a Változó** lehetőséget, ha a munkaterhelés kötete idővel megváltozik.  Például egy adott napon vagy egy hónapban. <br/><br/> A változó számítási feladatok beállításának kiválasztásakor a következő beállítások érhetők el:<ul><li>Az idő százaléka a csúcs: Az idő százaléka egy hónapban, ahol a számítási feladatok csúcs (legmagasabb) átviteli. <br/><br/> Ha például olyan számítási feladatokat végez, amelyek magas aktivitással rendelkeznek a hétköznap 9:00 és 18:00 óra között, akkor a csúcsidőben az idő százaléka: 45 óra csúcs / 730 óra / hó = ~6%.<br/><br/></li><li>Olvasás/mp régiónként csúcsértéken – A csúcsértéken másodpercenként várt olvasások száma.</li><li>Írások/mp régiónként csúcsértéken – a csúcsértéken másodpercenként várt írások száma.</li><li>Olvasás/mp régiónként csúcsidőn kívül – A csúcsidőn kívüli időszakban másodpercenként várt olvasások száma.</li><li>Írások/mp régiónként csúcsidőn kívül – a csúcsidőn kívül másodpercenként várt írások száma.</li></ul>Csúcs- és csúcsidőn kívüli intervallumokkal optimalizálhatja a költségeket a [kiosztott átviteli teljesítmény ennek megfelelően történő fel-](set-throughput.md#update-throughput-on-a-database-or-a-container) és leskálázásával.|
|Elem mérete|Az adatelem mérete (pl. dokumentum), 1 KB-tól 2 MB-ig terjed. <br/><br/>A pontosabb becslés érdekében **minta (JSON)** dokumentumot is feltölthet.<br/><br/>Ha a számítási feladatok több típusú elemek (különböző JSON-tartalom) ugyanabban a tárolóban, feltöltheti több JSON-dokumentumokat, és a becslést. Az **Új elem hozzáadása** gombbal több minta JSON-dokumentumot adhat hozzá.|

A **Becslés mentése** gombbal az aktuális becslést tartalmazó CSV-fájlt is letölthet. 

![Kapacitástervező speciális üzemmód](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Az Azure Cosmos DB kapacitástervezőben látható árak az átviteli sebesség és a tárolás nyilvános díjszabási díjai alapján becsültek. Minden ár amerikai dollárban jelenik meg. Tekintse meg az [Azure Cosmos DB díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) az összes díj régiónként.  

## <a name="estimating-throughput-for-queries"></a>Lekérdezések átviteli alapjának becslése

Az Azure Cosmos kapacitáskalkulátor feltételezi pont olvasás (egy olvasni egy elem, például a dokumentum, az azonosító és a partíciókulcs értéke) és írja a számítási feladatokhoz. A lekérdezésekhez szükséges átviteli hang megbecsüléséhez futtassa a lekérdezést egy Cosmos-tárolóban lévő reprezentatív adatkészleten, és [szerezze be a RU-díjat.](find-request-unit-charge.md) Szorozza meg a RU-díjat a másodpercenként futtatandó lekérdezések számával, hogy megkapja a szükséges teljes RU/s-t. 

Például ha a számítási feladatok ``SELECT * FROM c WHERE c.id = 'Alice'`` hoz egy lekérdezést, amely másodpercenként 100-szor fut, és a ru a lekérdezés terhelése 10 Kérelem, szüksége lesz 100 lekérdezés / mp * 10 RU / lekérdezés = 1000 RU/s összesen ezeket a kérelmeket. Adja hozzá ezeket a RU/s-t a számítási feladatokban történik olvasásokhoz vagy írásokhoz szükséges RU/s-hoz.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Cosmos DB díjszabási modelljéről.](how-pricing-works.md)
* Hozzon létre egy új [Cosmos-fiókot, adatbázist és tárolót.](create-cosmosdb-resources-portal.md)
* További információ a [kiépített átviteli költség optimalizálásáról.](optimize-cost-throughput.md)
* További információ a [költségek foglalásos kapacitással való optimalizálásáról.](cosmos-db-reserved-capacity.md)

