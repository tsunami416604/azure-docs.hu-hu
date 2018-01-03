---
title: "A csatlakoztatott gyári topológia konfigurálása |} Microsoft Docs"
description: "A csatlakoztatott gyár topológia konfigurálása előre konfigurált megoldás."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 19e0f48ab817428a1f953c80296b2e23effe5a8a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="configure-the-connected-factory-preconfigured-solution"></a>Az előre konfigurált csatlakoztatott gyári megoldás konfigurálása

Az előre konfigurált csatlakoztatott gyári megoldás szimulált irányítópult egy kitalált, Contoso vállalat jelennek meg. A vállalatnak előállítók számos globális helyeken globálisan.

Ez a cikk Contoso példaként ismertetik a csatlakoztatott gyári megoldás topológiát használja.

## <a name="simulated-factories-configuration"></a>Szimulált előállítók konfiguráció

Minden Contoso gyári rendelkezik, amely három állomások áll éles sorok. Minden állomás egy adott szerepkörrel valós OPC EE-kiszolgáló:

* Szerelvény állomás
* Állomás tesztelése
* Csomagoló állomás

Ezek OPC EE-kiszolgálóknak rendelkezniük OPC EE-csomópontok és [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) csatlakoztatott gyári ezeket a csomópontokat értékének küld. Ehhez a következőket:

* Például az aktuális energiafogyasztását aktuális működési állapotát.
* Éles információkat, például a termékek száma hozott létre.

Az Irányítópult segítségével feltárni a Contoso gyári topológia állomás szintű megtekinthessék globális nézetből. A csatlakoztatott gyári irányítópult lehetővé teszi, hogy:

* A képi megjelenítés a OEE és KPI adatok az egyes rétegekhez topológiában.
* A képi megjelenítés az aktuális értékek állomásokon OPC EE-csomópontok.
* Az összesítés a a OEE és KPI adatok az állomás szintről a globális szinten.
* A képi megjelenítés riasztások, és ha az értékek elérni a megadott küszöbértékek végrehajtandó műveletek.

## <a name="connected-factory-topology"></a>Csatlakoztatott gyári topológia

A topológia előállítók, éles sorok és állomások hierarchikus:

* A globális szinten van a feldolgozó csomópontok gyermekobjektumokkal.
* A előállítók éles vonal csomópont gyermeke lehet.
* A gyártási sorok állomás csomópontok gyermeke lehet.
* Az állomások (OPC EE-kiszolgálók) OPC EE-csomópontok gyermeke lehet.

A topológia összes csomópontjában tartozik egy közös meghatározó tulajdonságok:

* A topológia csomópont egyedi azonosítója.
* Egy nevet.
* Leírását.
* Kép.
* A topológia csomópont gyermekei.
* Minimális, cél és maximális értékek OEE és a KPI-t és a riasztási műveletek végrehajtásához.

## <a name="topology-configuration-file"></a>Topológia konfigurációs fájl

Az előző szakaszban felsorolt tulajdonságok konfigurálásához a csatlakoztatott gyári megoldás nevű konfigurációs fájlt használ [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Ez a fájl megtalálható a megoldás forráskódját a `WebApp/Contoso/Topology` mappát.

Az alábbi kódrészletben láthatja áttekintése, amelyek a `ContosoTopologyDescription.json` konfigurációs fájlban:

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

Általános tulajdonságainak `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, és `<station_configuration>` vannak:

* **Név** (írja be a karakterláncot.)

  Határozza meg egy leíró nevet, és az irányítópulton megjelenítendő topológia csomópont csak egy szót kell lennie.

* **Leírás** (írja be a karakterláncot.)

  A topológia csomópont részletesebben ismerteti.

* **Kép** (írja be a karakterláncot.)

  A kép elérési útját a WebApp megoldás megjelenítése, ha a topológia csomópont információ az irányítópulton látható.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (típus `<performance_definition>`)

  Ezek a tulajdonságok megadása minimális, cél, és a maximális értékeket az operatív. ábra a riasztások létrehozásához használt. Ezekkel a tulajdonságokkal is definiálhat a riasztás észlelésekor végrehajtandó műveleteket.

A `<factory_configuration>` és `<production_line_configuration>` útjuk tulajdonság:

* **GUID** (írja be a karakterláncot.)

  A topológia csomópont egyedi azonosítására szolgál.

`<factory_configuration>`tulajdonsággal rendelkezik:

* **Hely** (típus `<location_definition>`)

  Itt adhatja meg, ahol a gyári.

`<station_configuration>`tulajdonságokkal rendelkezik:

* **OpcUri** (írja be a karakterláncot.)

  Ez a tulajdonság a OPC EE-kiszolgáló OPC EE alkalmazás URI-ra kell beállítani.
  Mert OPC EE-specifikáció szerint egyedinek kell lenniük, ez a tulajdonság az állomás topológia csomópont azonosítására szolgál.

* **OpcNodes**, amelyeket OPC EE-csomópontok tömbje (típus `<opc_node_description>`)

`<location_definition>`tulajdonságokkal rendelkezik:

* **Város** (írja be a karakterláncot.)

  A hely legközelebbi város neve

* **Ország** (írja be a karakterláncot.)

  A hely ország

* **A földrajzi hosszúság** (kettős típusa)

  A hely szélesség

* **A földrajzi hosszúság értéke** (kettős típusa)

  A földrajzi hosszúság értéke annak a helynek

`<performance_definition>`tulajdonságokkal rendelkezik:

* **Minimális** (kettős típusa)

  Alsó küszöbérték értéke érhető el. Ha az aktuális értéke a küszöbérték alá, egy riasztást generál.

* **Cél** (kettős típusa)

  Épp ezért tökéletes választás célértéket.

* **Maximális** (kettős típusa)

  Felső küszöbérték értéke érhető el. Ha az aktuális értéke a küszöbérték fölött, egy riasztást generál.

* **MinimumAlertActions** (típus `<alert_action>`)

  A művelet, melyek akkor van szükség, a minimális riasztások válaszként csoportját határozza meg.

* **MaximumAlertActions** (típus `<alert_action>`)

  A műveletek, amely akkor van szükség, a maximális riasztások válaszként csoportját határozza meg.

`<alert_action`> tulajdonságokkal rendelkezik:

* **Típus** (írja be a karakterláncot.)

  A riasztási művelet típusa. A következő típusú ismert:

  * **AcknowledgeAlert**: a riasztás állapota a nyugtázott kell módosítani.
  * **CloseAlert**: összes korábbi értesítés azonos típusú kell már nem jeleníthető meg az irányítópulton.
  * **CallOpcMethod**: egy OPC EE metódust kell meghívni.
  * **OpenWebPage**: környezetfüggő további információkat megjelenítő böngészőablakban kell megnyitni.

* **Leírás** (írja be a karakterláncot.)

  Az irányítópulton látható művelet leírása.

* **A paraméter** (írja be a karakterláncot.)

  A művelet végrehajtásához szükséges paraméter. Az érték a művelet típusától függ.

  * **AcknowledgeAlert**: nem kötelező paraméter.
  * **CloseAlert**: nem kötelező paraméter.
  * **CallOpcMethod**: a csomópont adatok és a paraméterek a OPC EE metódus hívása a következő formátumban: "NodeId szülőcsomópont, NodeId metódus hívása, a OPC EE-kiszolgáló URI."
  * **OpenWebPage**: az URL-cím megjelenítése a böngészőablakban.

`<opc_node_description>`állomás (OPC EE-kiszolgáló) OPC EE csomópontjának adatait tartalmazza. A csomópontokra, amelyeket nem létező OPC EE-csomópontok képviselik, de a csatlakoztatott gyári számítási logikájának tárolóként szolgálnak is érvényesek. A következő tulajdonságokkal rendelkezik:

* **NodeId** (írja be a karakterláncot.)

  A OPC EE az állomáson (OPC EE kiszolgáló) csomópont címterület címe. Szintaxis kell lennie egy NodeId OPC EE előírásának megadottak szerint.

* **Melynek** (írja be a karakterláncot.)

  Ez a OPC EE-csomópont értékének megjelenítésekor az irányítópulton megjelenítendő neve.

* **Relevanciájának** (karakterlánc típusú tömb)

  Azt jelzi, amelyhez számítási OEE vagy KPI OPC EE értékét megfelelő. Minden egyes eleméhez a következő értékek egyike lehet:

  * **OeeAvailability_Running**: OEE rendelkezésre állási kiszámítása a szükséges érték.
  * **OeeAvailability_Fault**: OEE rendelkezésre állási kiszámítása a szükséges érték.
  * **OeePerformance_Ideal**: az érték a számítási OEE teljesítmény szükséges, és általában egy állandó értékkel.
  * **OeePerformance_Actual**: érték kiszámítása OEE teljesítmény szempontjából.
  * **OeeQuality_Good**: OEE minőségi kiszámítása a szükséges érték.
  * **OeeQuality_Bad**: OEE minőségi kiszámítása a szükséges érték.
  * **Kpi1**: KPI1 kiszámítása a szükséges érték.
  * **Kpi2**: KPI2 kiszámítása a szükséges érték.

* **Opkód** (írja be a karakterláncot.)

  Azt jelzi, hogy a OPC EE-csomópont értékének idő adatsorozat Insight lekérdezésekben és OEE/KPI számítások kezelésének módját. Minden alkalommal adatsorozat Insight lekérdezés célozza megadott timespan érték, amely egy paraméter, a lekérdezés, és kézbesíti az eredményt. A műveleti kód szabályozza, hogyan eredménye számított, és a következő értékek egyike lehet:

  * **Diff**: a timespan az utolsó és az első érték közötti különbség.
  * **Átlagos**: a timespan szereplő összes érték átlagát.
  * **Sum**: a timespan szereplő összes érték összegét.
  * **Utolsó**: jelenleg nem használja.
  * **Count**: a timespan értékek száma.
  * **Maximális**: a timespan maximális értékét.
  * **Minimális**: az időtartam minimális értéke.
  * **Konstans**: eredménye ConstValue tulajdonság által megadott értéket.
  * **SubMaxMin**: a maximálisan és a minimális érték közötti különbséget.
  * **A TimeSpan**: a timespan.

* **Egységek** (írja be a karakterláncot.)

  Határozza meg a megjelenítési érték munkaegység az irányítópulton.

* **Látható** (logikai érték típusú)

  Szabályozza, hogy az értéket meg kell jelennie az irányítópulton.

* **ConstValue** (kettős típusa)

  Ha a **opkód** van **Const**, akkor ez a tulajdonság a csomópont értéke.

* **Minimális** (kettős típusa)

  Ha az aktuális érték ezen érték alá esik, a minimális riasztás generálódik.

* **Maximális** (kettős típusa)

  Ha a jelenlegi érték riasztást meghaladja ezt az értéket, majd egy maximális riasztást.

* **MinimumAlertActions** (típus `<alert_action>`)

  A művelet, melyek akkor van szükség, a minimális riasztások válaszként csoportját határozza meg.

* **MaximumAlertActions** (típus `<alert_action>`)

  A műveletek, amely akkor van szükség, a maximális riasztások válaszként csoportját határozza meg.

Az állomás szintjén is látni **szimuláció** objektumok. Ezek az objektumok csak a csatlakoztatott gyári szimuláció konfigurálásához használt, és nem használható egy valódi topológiát.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>A konfigurációs adatok futásidőben használatáról

A konfigurációs fájlban használt összes tulajdonságok különböző kategóriákba attól függően, hogy hogyan lehet csoportosítani. Ezekben a kategóriákban a következők:

### <a name="visual-appearance"></a>Vizuális megjelenését

Ebbe a kategóriába tartozó tulajdonságok meghatározása a csatlakoztatott gyári irányítópult annak vizuális megjelenését. Példák erre vonatkozóan:

* Name (Név)
* Leírás
* Kép
* Hely
* egység
* Látható

### <a name="internal-topology-tree-addressing"></a>Belső topológia fa-címzés

A webalkalmazást fenntartja egy belső adatkönyvtár csomópontjaihoz topológia információkat tartalmazó. A Tulajdonságok **Guid** és **OpcUri** kulcsként ehhez a szótárhoz eléréséhez használt, és egyedinek kell lennie kell.

### <a name="oeekpi-computation"></a>OEE/KPI számítási

A csatlakoztatott gyári szimuláció OEE/KPI adatok paraméterezni vannak:

* A számítási szereplő OPC EE csomópont értékeket.
* Hogyan ábra számított a telemetria-értékek.

Csatlakoztatott gyári a http://oeeindustrystandard.oeefoundation.org által közzétett OEE képleteket használja.

Az állomásokon OPC EE objektumok engedélyezése címkézés OEE/KPI számítás használatra. A **relevanciájának** a tulajdonság azt jelzi, hogy mely OEE/KPI. ábra a OPC EE-csomópont értéket kell használni. A **opkód** tulajdonság határozza meg, hogyan értékét a számítási szerepel.

### <a name="alert-handling"></a>Riasztások kezelése

Csatlakoztatott gyári támogatja-e egyszerű minimuma vagy maximuma, küszöbérték-alapú riasztás előállítása. Számos előre definiált műveletek konfigurálhatja azokat a riasztás. A következő tulajdonságok szabályozzák a mechanizmus:

* Maximum
* Minimális
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>A telemetriai adatok használatával történik

Az egyes műveletek, például megjelenítése a legutóbbi érték vagy idő adatsorozat Insight lekérdezések létrehozásáról a webalkalmazást a feldolgozott telemetriai adatokat egy címzési séma szüksége van. Csatlakoztatott gyári küldött telemetriai adatok tárolását a belső adatszerkezetek kell. Ezek a műveletek engedélyezése két tulajdonságai állomás (OPC EE-kiszolgáló) és OPC EE csomópont szintjén vannak:

* **OpcUri**

  Azonosítja (globálisan egyedi) a OPC EE-kiszolgáló a telemetriai adat származik. A feldolgozott üzenetek, ez a tulajdonság legyen elküldve, **ApplicationUri**.

* **NodeId**

  Azonosítja a csomópont-érték a OPC EE-kiszolgálón. A tulajdonság a következő formátumban kell megadni a OPC EE-specifikáció megadottak szerint. A feldolgozott üzenetek, ez a tulajdonság legyen elküldve, **NodeId**.

Ellenőrizze [ez](https://github.com/Azure/iot-edge-opc-publisher) bővebben hogyan van okozhatnak a telemetriai adatokat a OPC Publisher használatával csatlakoztatott gyári a GitHub-oldalon.

## <a name="example-how-kpi1-is-calculated"></a>Példa: Hogyan KPI1 kiszámítása

A konfiguráció a `ContosoTopologyDescription.json` fájl szabályozza OEE/KPI adatok kiszámítási módját. A következő példa bemutatja, hogyan szabályozza a fájl tulajdonságok a KPI1 elkészítése.

A csatlakoztatott gyári KPI1 segítségével száma sikeresen feldolgozott termékek az elmúlt órában. Minden állomáshoz (OPC EE-kiszolgáló) a csatlakoztatott gyári szimuláció biztosít egy OPC EE-csomópont (`NodeId: "ns=2;i=385"`), amely biztosítja, hogy a telemetria bekapcsolásával számítási ez KPI-t.

Ez a OPC EE-csomópont konfigurációjának a következő kódrészletet néz ki:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ez a konfiguráció lehetővé teszi, hogy ez a csomópont idő adatsorozat Insights segítségével a telemetriai adatok közül lekérdezése. Az idő adatsorozat Insights lekérdezés lekéri:

* Értékek száma.
* A minimális érték.
* A maximális értéket.
* Az összes érték átlagának.
* Minden értékeinek összege egyedi **OpcUri** (**ApplicationUri**), **NodeId** a megadott timespan érték párokat.

Egy jellemző a **NumberOfManufactureredProducts** értéke csomópont, hogy csak növeli. A TimeSpan érték, a gyártott számának kiszámítása csatlakoztatva a gyári használja a **opkód** **SubMaxMin**. A számítási lekéri a minimális elején a timespan és maximális értéke a timespan végén.

A **opkód** konfigurálja a számítási logika maximális és minimális érték különbségét értékének kiszámítása a konfiguráció. Az eredmények fogja halmozott alsó legfelső szintű (globális) szintre, és az irányítópulton látható.

## <a name="next-steps"></a>További lépések

A javasolt következő lépésre megtudhatja, hogyan [egy átjáró a Windows vagy Linux az előre konfigurált csatlakoztatott gyári megoldás üzembe helyezéséhez](iot-suite-connected-factory-gateway-deployment.md).