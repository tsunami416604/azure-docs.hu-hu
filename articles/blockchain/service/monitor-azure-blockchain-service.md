---
title: Az Azure Blockchain szolgáltatás (ABS) figyelése
description: Az Azure Blockchain szolgáltatás figyelése az Azure Monitoron keresztül
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293249"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Az Azure Blockchain szolgáltatás figyelése az Azure Monitoron keresztül  

Mivel az ügyfelek éles szintű blokklánc-forgatókönyveket futtatnak az Azure Blockchain Szolgáltatáson (ABS), kritikus fontosságúvá válik az erőforrások figyelése a rendelkezésre állás, a teljesítmény és a műveletek szempontjából. Ez a cikk ismerteti az Azure Blockchain Szolgáltatás által létrehozott figyelési adatokat, és hogyan használhatja az Azure Monitor különböző funkcióit és integrációit az éles környezetek elemzéséhez és riasztásához.  

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

Az Azure Blockchain Service figyelési adatokat hoz létre az Azure Monitor használatával, amely egy teljes körű készletfigyelési szolgáltatás az Azure-ban, amely az Azure-erőforrások figyeléséhez teljes körű funkciókat biztosít. Az Azure Monitorról az [Azure-erőforrások figyelése az Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)című témakörben talál további információt.
 

A következő szakaszok erre a cikkre épülnek az Azure Blockchain Szolgáltatásból gyűjtött konkrét adatok leírásával, valamint példákkal az adatgyűjtés konfigurálásához és az adatok Azure-eszközökkel való elemzéséhez.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Az Azure Blockchain szolgáltatásból gyűjtött adatok figyelése  

Az Azure Blockchain Szolgáltatás ugyanolyan típusú figyelési adatokat gyűjt, mint más Azure-erőforrások, amelyek et az Azure-erőforrásokból származó [adatok figyelése](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) ismertet. [Az Azure Blockchain Service adatainak figyelése című](#monitor-azure-blockchain-service-data-reference) témakörben részletesen ismertheti az Azure Blockchain Szolgáltatás által létrehozott naplókat és metrikákat.

Az azure-beli portálon az egyes Azure Blockchain Service-tagok erőforrás áttekintő lapja rövid áttekintést nyújt a tranzakciókról, beleértve a kezelt és feldolgozott blokkokat. Ezen adatok egy része automatikusan összegyűjtésre kerül, és az Azure Blockchain Service tagerőforrás létrehozása után elemzésre is elérhetővé válik, miközben további konfigurációval további adatgyűjtést engedélyezhet.

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások  

A platformmetrikák és a tevékenységnapló automatikusan összegyűjtésre kerülnek, de létre kell hoznia egy diagnosztikai beállítást az erőforrásnaplók összegyűjtéséhez vagy az Azure Monitoron kívülre továbbításhoz. Olvassa [el a Diagnosztikai beállítás létrehozása platformnaplók és metrikák gyűjtéséhez az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) az Azure Portalon, cli-n vagy PowerShellen keresztül igöveszthető diagnosztikai beállítások létrehozásának részletes folyamatához.

Diagnosztikai beállítás létrehozásakor megadhatja, hogy mely naplókat kell gyűjteni. Az Azure Blockchain Szolgáltatás kategóriái az alábbiakban találhatók.

**Blockchain proxy naplók** – Válassza ki a kategóriát, ha azt szeretné, hogy figyelje az NGNIX proxy naplók. Az összes ügyféltranzakció-adat rendelkezésre áll naplózási és hibakeresési célokra.  

**Blockchain alkalmazásnaplók** – Válassza ki a kategóriát a felügyelt szolgáltatás által üzemeltetett blockchain alkalmazás naplóinak lehívásához. Például egy ABS-Quorum tag, ezek a naplók lenne a naplók kvórum a kvórum is.  

**Metrikakérések:** Válassza ki a lehetőséget, hogy metrikus adatokat gyűjtsön az Azure Cosmos DB a célok a diagnosztikai beállítás, amely automatikusan gyűjti az Azure Metrics. Metrikaadatok gyűjtése erőforrásnaplókkal mindkét típusú adat együtt elemzéséhez és metrikaadatok küldéséhez az Azure Monitoron kívül.

## <a name="analyze-metric-data"></a>Metrikaadatok elemzése  

Elemezheti metrikák az Azure Blockchain Szolgáltatás metrikák explorer, keresse meg a Metrikák lap figyelése szakasz ABS erőforrás panel. Az [azure metrics-kezelő használatának megkezdéséről](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) az eszköz használatával kapcsolatos részletekről. Az Azure Blockchain Service teljes metrikák a névtérBen Az Azure Blockchain Service standard metrikák.

A **csomópontdimenziót** szűrő hozzáadásakor vagy a metrikák felosztásakor használhatja, amely alapvetően az ABS-tag tranzakciócsomópontonkénti és validator csomópontjaiszerinti metrikaértékeket biztosít.

## <a name="analyze-log-data"></a>Naplóadatok elemzése

Íme néhány lekérdezés, amely et megadhat a Napló keresősávjában, hogy segítsen az Azure Blockchain szolgáltatás tagjainak figyelésében. Ezek a lekérdezések az [új nyelvvel](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)működnek.

A Blockchain alkalmazásnaplókban található hibafeltételek lekérdezéséhez használja az alábbi lekérdezést:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

A Blockchain proxynaplókban található hibafeltételek lekérdezéséhez használja az alábbi lekérdezést  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Az Azure-naplókban elérhető időszűrők segítségével szűrheti a lekérdezést egy adott időtartományra.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Az Azure Blockchain szolgáltatás adatainak figyelése  

Ez a cikk az Azure Blockchain Szolgáltatás teljesítményének és rendelkezésre állásának elemzéséhez gyűjtött napló- és metrikaadatok hivatkozását tartalmazza.  

### <a name="resource-logs"></a>Erőforrásnaplók

Minden erőforrásnaplók osztozik egy legfelső szintű közös séma néhány egyedi tulajdonságok at a blockchain szolgáltatás. Hivatkozhat a [cikk felső szintű erőforrás naplók séma](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), részleteit az Azure Blockchain szolgáltatás specifikus tulajdonságok az alábbiakban  

Az alábbi táblázat felsorolja az Azure Blockchain proxynaplók tulajdonságait, amikor az Azure Monitor naplók ban vagy az Azure Storage-ban gyűjtik őket.  


| Tulajdonság neve  | Leírás |
|:---|:---|
| time | A művelet bekövetkezésének dátuma és időpontja (UTC). |
| erőforrásazonosító  | Az Azure Blockchain Service erőforrás, amelyhez a naplók engedélyezve vannak.  |
| category  |Az Azure Blockchain Szolgáltatás esetében a lehetséges értékek **proxynaplók** és **applicationlogs.** |
| operationName  | Az esemény által képviselt művelet neve.   |
| Napló szintje  | Alapértelmezés szerint az Azure Blockchain Szolgáltatás engedélyezi **az információs** napló szintjét.   |
| Csomópont helye  | Az Azure-régió, ahol a blokklánc-tag telepítve van.  |
| BlockchainNodeName  | Annak az Azure Blockchain szolgáltatástagnak a csomópontja, amelyen a művelet et végrehajtják.   |
| EthMódszer  | A módszer, amelyet az alapul szolgáló blockchain protokoll nevez meg, a Quorumban lehet eth_sendTransactions, eth_getBlockByNumber stb.  |
| Ügynök  | A felhasználó nevében eljáró felhasználó ügynök, például a Mozilla, az Edge böngésző stb. Példák az értékekre: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Kód   | HTTP hibakódok. Általában 4XX és 5XX a hiba feltételeket.  |
| NodeHost (NodeHost)  | A csomópont DNS-neve.   |
| RequestMethodName | HTTP metódus hívott, a lehetséges értékek itt put létrehozására tag, GET a szerzés részleteit a meglévő tag, DELETE a delete tag, PATCH frissítése tag.   |
| BlockchainMemberName  | Az Azure Blockchain Service tag a felhasználó által megadott nevét.  |
| Konzorcium | A konzorcium neve a felhasználó által megadott aszerint.   |
| Remote  | Annak az ügyfélnek az IP-címe, ahol a kérés érkezik.  |
| Kérésméretet  | A bájtban küldött kérelem mérete.  |
| RequestTime (Kérésidő)  | A kérelem időtartama ezredmásodpercben.|




Az alábbi táblázat az Azure Blockchain alkalmazásnaplók tulajdonságait sorolja fel.


| Tulajdonság neve  | Leírás |
|:---|:---|
| time | A művelet bekövetkezésének dátuma és időpontja (UTC). |
| erőforrásazonosító  | Az Azure Blockchain Service erőforrás, amelyhez a naplók engedélyezve vannak.|
| category  |Az Azure Blockchain Szolgáltatás esetében a lehetséges érték **proxynaplók** és **applicationlogs.**  |
| operationName  | Az esemény által képviselt művelet neve.   |
| Napló szintje  | Alapértelmezés szerint az Azure Blockchain Szolgáltatás engedélyezi **az információs** napló szintjét.   |
| Csomópont helye  | Az Azure-régió, ahol a blokklánc-tag telepítve van.  |
| BlockchainNodeName  | Annak az Azure Blockchain szolgáltatástagnak a csomópontja, amelyen a művelet et végrehajtják.   |
| BlockchainMessage    | Ez a mező a Blockchain alkalmazásnaplót fogja tartalmazni, amely az adatok egyszerű naplói. Az ABS-Quorum esetében ez a Quorum naplóival rendelkezne. Azt információt arról, hogy milyen típusú napló bejegyzés ez, hogy a tájékoztató, hiba, figyelmeztetés és egy karakterláncot, amely további információt aművelet végrehajtása.   |
| TenantID    | Az Azure Blockchain szolgáltatás régióspecifikus bérlője. A mező formátuma https://westlake-rp-prod. <region>.cloudapp.azure.com, ahol a régió határozza meg a telepített tag Azure-régióját.       |
| SourceSystem   | A rendszer feltölti a naplókat, ebben az esetben az **Azure.**    |



### <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok az Azure Blockchain Szolgáltatáshoz gyűjtött platformmutatókat sorolják fel. Az összes metrika az **Azure Blockchain Service** standard metrikák névtérben tárolódik.

Az Azure Monitor által támogatott metrikák (beleértve az Azure Blockchain Service) listáját az [Azure Monitor által támogatott metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)című témakörben található.

### <a name="blockchain-metrics"></a>Blokklánc-mérőszámok

Az alábbi táblázat az Azure Blockchain Service tagerőforráshoz gyűjtött blockchain-metrikák listáját tartalmazza.


| Metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás   |
|---|---|---|---|
| Függőben lévő tranzakciók   | Darabszám  |  Átlag | A kibányászandó tranzakciók száma.   |
| Feldolgozott blokkok   | Darabszám  | Összeg  |  Az egyes időintervallumokban feldolgozott blokkok száma. Jelenleg a blokk mérete 5 másodperc, így egy perc alatt minden csomópont feldolgozza 12 blokkok és 60 blokkok 5 perc alatt.   |
|Feldolgozott tranzakciók    | Darabszám  | Összeg  | A blokkban feldolgozott tranzakciók száma.    |
|Várólistán lévő tranzakciók    |  Darabszám | Átlag  | A nem azonnal bányászható tranzakciók száma. Ez azért lehet, mert nem sorrendben érkeztek, és a jövő várja az előző tranzakció érkezését. Vagy lehet két tranzakció, amelyek csak egyszer (nonce) használják ugyanazt a számot, és ugyanaz a gázérték, ezért a második nem bányászható.   |

### <a name="connection-metrics"></a>Csatlakozási mutatók  

Az alábbi táblázat felsorolja az Azure Blockchain Service tagerőforráshoz gyűjtött különböző kapcsolati metrikákat. Ezek NGINX proxy metrikák.


| Metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás |
|---|---|---|---|
| Elfogadott kapcsolatok   | Darabszám  |  Összeg | Az elfogadott ügyfélkapcsolatok teljes száma.   |
| Aktív kapcsolatok  | Darabszám  | Átlag  |  Az aktív ügyfélkapcsolatok aktuális száma, beleértve a várakozó kapcsolatokat is.    |
|Kezelt kapcsolatok    | Darabszám  | Összeg  | A kezelt kapcsolatok teljes száma. A paraméterértéke általában megegyezik az elfogadott kapcsolatokkal, hacsak nem értek el bizonyos erőforráskorlátokat.     |
|Kezelt kérelmek     |  Darabszám | Összeg  | Az ügyfélkérelmek teljes száma.  |


### <a name="performance-metrics"></a>Teljesítmény-metrikák

Az alábbi táblázat az Azure Blockchain tag-erőforrás egyes csomópontjaihoz összegyűjtött teljesítménymutatókat sorolja fel.  


| Metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás   |
|---|---|---|---|
| CPU-használat százaléka   | Százalék  |  Max | A CPU-használat százalékos aránya.     |
| Io olvasási bájtjai   | Kilobyte   | Összeg  |  Az IO olvasási bájtjainak összege a blockchain tag erőforrás összes csomópontja között.      |
|IO írási bájtok     | Kilobyte   | Összeg  | Az IO-k által írt bájtok összege a blokklánc tag erőforrás összes csomópontjára.     |
|Memóriakorlát       |  Gigabájt   | Átlag    | A blokklánc-folyamathoz csomópontonként rendelkezésre álló maximális memória. |
|Memóriahasználat     | Gigabájt  |  Átlag | Az összes csomóponton használt memória mennyisége.  |
| Memóriahasználat százaléka     | Százalék   | Átlag  |  A felhasznált memória százalékos aránya az összes csomóponton.       |
|Tárhely használat      | Gigabájt   | Átlag  | A használt GB-os tárterület az összes csomópont átlagában.       |


## <a name="next-steps"></a>Következő lépések

További információ a [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) ről a blokklánc-adatok Rögzítéséhez és átalakításához az Azure Event Gridbe.
