---
title: A csatlakoztatott gyári topológia konfigurálása - Azure | Microsoft dokumentumok
description: Ez a cikk a Connected Factory megoldásgyorsító tanlógiáját is beleértve a konfigurálását ismerteti.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820121"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>A csatlakoztatott gyári megoldásgyorsító konfigurálása

A Connected Factory megoldásgyorsító egy szimulált irányítópultot jelenít meg egy kitalált contoso-i vállalat számára. Ez a vállalat világszerte számos globális helyszínen rendelkezik gyárakkal.

Ez a cikk a Contoso-t használja példaként a csatlakoztatott gyári megoldás topológiájának konfigurálásához.

## <a name="simulated-factories-configuration"></a>Szimulált gyárak konfigurációja

Minden Contoso-gyár rendelkezik gyártósorokkal, amelyek egyenként három állomásból állnak. Minden állomás egy igazi OPC UA szerver egy adott szerepkörrel:

* Összeszerelő állomás
* Vizsgálati állomás
* Csomagolóállomás

Ezek az OPC UA-kiszolgálók OPC UA-csomópontokkal rendelkeznek, és az [OPC Publisher](overview-opc-publisher.md) elküldi ezeknek a csomópontoknak az értékeit a Connected Factory számára. Az érintett műveletek közé tartoznak az alábbiak:

* Jelenlegi működési állapot, például az aktuális energiafogyasztás.
* Termelési információk, például az előállított termékek száma.

Az irányítópult segítségével a Contoso gyári topológiájába részletezheti a globális nézettől az állomásszintű nézetig. A Connected Factory műszerfal a következőket teszi lehetővé:

* OEE és KPI-számok megjelenítése a topológia egyes rétegeihez.
* Az OPC UA-csomópontok aktuális értékeinek megjelenítése az állomásokon.
* Az OEE és a KPI-adatok összesítése az állomás szinttől a globális szintig.
* A riasztások és a végrehajtandó műveletek vizualizálása, ha az értékek elérnek bizonyos küszöbértékeket.

## <a name="connected-factory-topology"></a>Csatlakoztatott gyári topológia

A gyárak, gyártósorok és állomások topológiája hierarchikus:

* A globális szint nek gyári csomópontjai vannak, mint gyerekek.
* A gyáraknak gyermekként gyártósor-csomópontjai vannak.
* A gyártósorok állomáscsomópontjai gyermekként vannak.
* Az állomások (OPC UA-kiszolgálók) gyermekként OPC UA-csomópontokkal rendelkeznek.

A topológia minden csomópontja rendelkezik egy közös tulajdonságkészletével, amely meghatározza:

* A topológia csomópont egyedi azonosítója.
* Egy nevet.
* Egy személyleírás.
* Egy képet.
* A topológia csomópont gyermekei.
* Az OEE- és KPI-adatok minimális, cél- és maximális értékei, valamint a végrehajtandó riasztási műveletek.

## <a name="topology-configuration-file"></a>Topológia konfigurációs fájl

Az előző szakaszban felsorolt tulajdonságok konfigurálásához a Connected Factory megoldás a [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json)nevű konfigurációs fájlt használja.

Ez a fájl a megoldás forráskódjában található a `WebApp/Contoso/Topology` mappában.

A következő kódrészlet a `ContosoTopologyDescription.json` konfigurációs fájl vázlatát mutatja be:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

A `<global_configuration>`, , `<factory_configuration>` `<production_line_configuration>`és `<station_configuration>` a következők:

* **Név** (karakterlánc)

  Egy leíró nevet határoz meg, amely csak egy szó lehet a topológia csomópont számára az irányítópulton.

* **Leírás** (karakterlánc típusa)

  Részletesebben ismerteti a topológia csomópontot.

* **Kép** (karakterlánc)

  A webapp-megoldásban lévő lemezkép elérési útja, amely megmutatja, hogy mikor jelennek meg a topológiacsomóponttal kapcsolatos információk az irányítópulton.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (típus) `<performance_definition>`

  Ezek a tulajdonságok minimális, cél- és maximális értékeket határoznak meg a riasztások létrehozásához használt működési értékből. Ezek a tulajdonságok is meghatározzák a riasztások észlelése esetén végrehajtandó műveleteket.

A `<factory_configuration>` `<production_line_configuration>` cikkek nek van egy tulajdonsága:

* **Guid** (karakterlánc)

  Egyedileg azonosítja a topológia csomópontot.

`<factory_configuration>`rendelkezik egy ingatlannal:

* **Hely** (típus) `<location_definition>`

  Itt adható meg, hogy hol található a gyár.

`<station_configuration>`tulajdonságokkal rendelkezik:

* **OpcUri** (karakterlánc típus)

  Ezt a tulajdonságot az OPC UA-kiszolgáló OPC UA alkalmazásURI-jára kell beállítani.
  Mivel az OPC UA specifikáció szerint globálisan egyedinek kell lennie, ez a tulajdonság az állomástopológia csomópont azonosítására szolgál.

* **OpcNodes**, amelyek az OPC UA csomópontok `<opc_node_description>`tömbje (típus)

`<location_definition>`tulajdonságokkal rendelkezik:

* **Város** (karakterlánc típusa)

  A helyhez legközelebb eső város neve

* **Ország** (karakterlánc típus)

  A hely országa

* **Szélesség** (dupla típus)

  A hely szélessége

* **Hosszúság** (kettős típus)

  A hely hosszúsága

`<performance_definition>`tulajdonságokkal rendelkezik:

* **Minimum** (dupla típus)

  Alacsonyabb küszöbérték, amelyet az érték elérhet. Ha az aktuális érték nem éri el ezt a küszöbértéket, riasztás jön létre.

* **Cél** (dupla típusú)

  Ideális célérték.

* **Maximum** (dupla típus)

  Az érték felső küszöbértéke. Ha az aktuális érték meghaladja ezt a küszöbértéket, riasztás jön létre.

* **MinimumAlertActions** (típus) `<alert_action>`

  Meghatározza a műveletek készletét, amely egy minimális riasztásra adott válaszként használható.

* **MaximumAlertActions** (típus) `<alert_action>`

  Meghatározza a műveletek készletét, amely a maximális riasztásra adott válaszként használható.

`<alert_action`> tulajdonságokkal rendelkezik:

* **Típus** (karakterlánc típus)

  A riasztási művelet típusa. A következő típusok ismertek:

  * **AcknowledgeAlert**: a riasztás állapotának nyugtázottra kell változnia.
  * **CloseAlert**: az összes régebbi, azonos típusú riasztás t nem jelenik meg az irányítópulton.
  * **CallOpcMethod**: opc UA metódust kell meghívni.
  * **OpenWebPage**: meg kell nyitni egy böngészőablakot, amely további környezetfüggő információkat jelenít meg.

* **Leírás** (karakterlánc típusa)

  Az irányítópulton megjelenő művelet leírása.

* **Paraméter** (típuskarakterlánc)

  A művelet végrehajtásához szükséges paraméterek. Az érték a művelet típusától függ.

  * **AcknowledgeAlert**: nincs szükség paraméterre.
  * **CloseAlert**: nincs szükség paraméterre.
  * **CallOpcMethod**: az OPC UA metódus hívócsomópont-információi és paraméterei a "Szülőcsomópont csomópont csomópontja, metódus nodeid,URI az OPC UA kiszolgálóURI-ja" formátumban.
  * **OpenWebPage**: a böngészőablakban megjelenítandó URL.OpenWebPage : the URL to show in the browser window.

`<opc_node_description>`információkat tartalmaz az állomáson (OPC UA-kiszolgáló) lévő OPC UA csomópontokról. Azok a csomópontok, amelyek nem jelentenek létező OPC UA-csomópontokat, de tárolóként használatosak a Connected Factory számítási logikájában, szintén érvényesek. Ez birtokol a következő tulajdonságok:

* **NodeId** (típuskarakterlánc)

  Az OPC UA csomópont címe az állomás (OPC UA-kiszolgáló) címterében. A szintaxisnak meg kell felelnie az OPC UA specifikációnak egy nodeid esetében.

* **Szimbolikusnév** (karakterlánc)

  Az irányítópulton megjelenítendő név, ha az OPC UA csomópont értéke megjelenik.

* **Relevancia** (karakterlánctípusú tömb)

  Azt jelzi, hogy az OEE vagy a KPI kiszámítása az OPC UA csomópont értéke melyik szempontjából releváns. Minden tömbelem a következő értékek egyike lehet:

  * **OeeAvailability_Running**: az érték az OEE rendelkezésre állásának kiszámításához releváns.
  * **OeeAvailability_Fault**: az érték az OEE rendelkezésre állásának kiszámításához releváns.
  * **OeePerformance_Ideal**: az érték az OEE teljesítményének kiszámításához fontos, és általában állandó érték.
  * **OeePerformance_Actual**: az érték az OEE-teljesítmény kiszámításához releváns.
  * **OeeQuality_Good**: az érték az OEE-minőség kiszámításához releváns.
  * **OeeQuality_Bad**: az érték az OEE-minőség kiszámításához releváns.
  * **Kpi1:** az érték a KPI1 kiszámításához releváns.
  * **Kpi2:** az érték a KPI2 kiszámításához releváns.

* **OpCode** (típuskarakterlánc)

  Azt jelzi, hogy az OPC UA-csomópont értékét hogyan kezeli a time series insight-lekérdezések és az OEE/KPI-számítások. Minden Time Series Insight lekérdezés egy adott időtartományt céloz meg, amely a lekérdezés paramétere, és eredményt ad. Az OpCode szabályozza az eredmény kiszámításának módját, és az alábbi értékek egyike lehet:

  * **Diff**: az utolsó és az első érték közötti különbség az időtartamban.
  * **Átlag**: az összes érték átlaga az időtartományban.
  * **Összeg:** az összes érték összege az időtartamban.
  * **Utolsó**: jelenleg nem használt.
  * **Darabszám:** az értékek száma az időtartamban.
  * **Max:** a maximális érték az időtartamban.
  * **Min:** a minimális érték az időtartományban.
  * **Konstans**: az eredmény a ConstValue tulajdonság által megadott érték.
  * **SubMaxMin:** a maximális érték és a minimális érték közötti különbség.
  * **Timespan**: az időtartomány.

* **Egységek** (karakterlánc típusa)

  Az irányítópulton megjelenendő érték egy egységét határozza meg.

* **Látható** (logikai típus)

  Azt szabályozza, hogy az érték megjelenjen-e az irányítópulton.

* **ConstValue** (dupla típusú)

  Ha az **OpCode** **konstans,** akkor ez a tulajdonság a csomópont értéke.

* **Minimum** (dupla típus)

  Ha az aktuális érték ez az érték alá csökken, akkor egy minimális riasztás jön létre.

* **Maximum** (dupla típus)

  Ha az aktuális érték ezen érték fölé emelkedik, akkor a rendszer maximális riasztást hoz létre.

* **MinimumAlertActions** (típus) `<alert_action>`

  Meghatározza a műveletek készletét, amely egy minimális riasztásra adott válaszként használható.

* **MaximumAlertActions** (típus) `<alert_action>`

  Meghatározza a műveletek készletét, amely a maximális riasztásra adott válaszként használható.

Az állomás szintjén **szimulációs** objektumok is láthatók. Ezek az objektumok csak a Csatlakoztatott gyár szimulációjának konfigurálására szolgálnak, és nem használhatók valódi topológia konfigurálására.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>A konfigurációs adatok használatba állása futásidőben

A konfigurációs fájlban használt összes tulajdonság különböző kategóriákba csoportosítható a használatuk módjától függően. Ezek a kategóriák a következők:

### <a name="visual-appearance"></a>Vizuális megjelenés

Ebben a kategóriában a tulajdonságok határozzák meg a Csatlakoztatott gyár imáteresztő irányítópultjának vizuális megjelenését. Példák erre vonatkozóan:

* Név
* Leírás
* Kép
* Hely
* Egység
* Látható

### <a name="internal-topology-tree-addressing"></a>Belső topológiafa kezelése

A WebApp egy belső adatszótárt tart fenn, amely az összes topológiacsomópont adatait tartalmazza. A **Guid** és **az OpcUri** tulajdonságok a szótár eléréséhez kulcsokként használatosak, és egyedinek kell lenniük.

### <a name="oeekpi-computation"></a>OEE/KPI számítás

A csatlakoztatott gyár szimulációjának OEE/KPI-adatai a következők szerint vannak paraméterezve:

* A számításban figyelembe veendő OPC UA csomópontértékek.
* Hogyan számítja ki az alakzatot a telemetriai értékekből.

A Connected Factory a. [http://www.oeefoundation.org](http://www.oeefoundation.org)

Az ÁLLOMÁSOK ON OPC UA csomópontobjektumai engedélyezik a címkézést az OEE/KPI-számításban. A **Relevancia** tulajdonság azt jelzi, hogy melyik OEE/KPI-értékhez kell használni az OPC UA csomópont értékét. Az **OpCode** tulajdonság határozza meg, hogy az érték hogyan szerepeljen a számításban.

### <a name="alert-handling"></a>Riasztás kezelése

A Connected Factory egy egyszerű minimális/maximális küszöbérték-alapú riasztásgenerálási mechanizmust támogat. A riasztásokra adott válaszként konfigurálható számos előre definiált művelet van. A következő tulajdonságok szabályozzák ezt a mechanizmust:

* Maximum
* Minimális
* MaximumRiasztási műveletek
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Telemetriai adatokkal való korreláció

Bizonyos műveletek, például az utolsó érték megjelenítése vagy a Time Series Insight-lekérdezések létrehozása esetén a WebApp-nak szüksége van egy címzési sémára a bevitt telemetriai adatokhoz. A csatlakoztatott gyárba küldött telemetriai adatokat is belső adatstruktúrákban kell tárolni. Az ezeket a műveleteket engedélyező két tulajdonság az állomás (OPC UA-kiszolgáló) és az OPC UA csomópont szintjén található:

* **OpcUri között**

  Azonosítja (globálisan egyedi) az OPC UA-kiszolgáló a telemetriai adatok származik. A bevitt üzenetekben ez a tulajdonság **ApplicationUri**néven kerül elküldésre.

* **NodeId**

  Az OPC UA-kiszolgáló csomópontértékét azonosítja. A tulajdonság formátumának meg kell felelnie az OPC UA specifikációnak. A bevitt üzenetekben ez a tulajdonság NodeId néven kerül **elküldésre.**

A Telemetriai adatok csatlakoztatott gyárba történő feldolgozásáról további információt a Mi [az OPC Publisher](overview-opc-publisher.md) című témakörben talál.

## <a name="example-how-kpi1-is-calculated"></a>Példa: A KPI1 számításának módja

A fájlkonfiguráció `ContosoTopologyDescription.json` határozza meg az OEE/KPI-adatok kiszámításának módját. A következő példa bemutatja, hogy ebben a fájlban a tulajdonságok hogyan szabályozzák a KPI1 számítását.

A Connected Factory KPI1-ben a sikeresen gyártott termékek számának mérésére szolgál az elmúlt órában. A Csatlakoztatott gyár szimulációjában minden állomás (OPC UA-kiszolgáló)`NodeId: "ns=2;i=385"`biztosít egy OPC UA csomópontot ( ), amely a KPI kiszámításához telemetriai adatokat biztosít.

Az OPC UA-csomópont konfigurációja a következő kódrészletnek tűnik:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ez a konfiguráció lehetővé teszi a telemetriai értékek lekérdezése a csomópont a Time Series Insights használatával. A Time Series Insights lekérdezés a következőket olvassa be:

* Az értékek száma.
* A minimális érték.
* A maximális érték.
* Az összes érték átlaga.
* Az összes egyedi **OpcUri** (**ApplicationUri**), **NodeId** pár összes értékének összege egy adott időtartamban.

A **NumberOfManufactureredProducts** csomópont értékének egyik jellemzője, hogy csak növekszik. Az időtartományban gyártott termékek számának kiszámításához a Connected Factory az **OpCode** **SubMaxMin**programot használja. A számítás a minimális értéket az időtartomány kezdetén, a maximális értéket pedig az időtartomány végén olvassa be.

A konfigurációban lévő **OpCode** úgy konfigurálja a számítási logikát, hogy kiszámítsa a maximális és a minimális érték különbségének eredményét. Ezek az eredmények ezután felhalmozott alulról felfelé a gyökér (globális) szinten, és megjelenik az irányítópulton.

## <a name="next-steps"></a>További lépések

A javasolt következő lépés a [Csatlakoztatott gyári megoldás testreszabása.](iot-accelerators-connected-factory-customize.md)
