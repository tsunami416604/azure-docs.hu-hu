---
title: A csatlakoztatott gyár topológia konfigurálása |} A Microsoft Docs
description: Hogyan konfigurálható egy Okosgyár-megoldásgyorsító topológiája.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 8cb3cae396016545c5d78a2ff7ccde4a053c4cf1
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746979"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Az Okosgyár-megoldásgyorsító konfigurálása

Az Okosgyár-megoldásgyorsító a szimulált irányítópult egy kitalált, Contoso vállalat mutatja be. A vállalat globálisan rendelkezik előállítók számos globális helyeken.

Ez a cikk ismerteti, hogyan konfigurálhatja a topológiát a csatlakoztatott gyár megoldás a Contoso használja példaként.

## <a name="simulated-factories-configuration"></a>Szimulált előállítók konfiguráció

Minden egyes Contoso factoryban három állomások álló gyártósorok rendelkezik. Minden állomás egy adott szerepkörrel valós OPC UA-kiszolgáló:

* Összeszerelő állomásból
* Tesztelő állomásból
* Csomagoló állomásból

Ezek az OPC UA-kiszolgálók rendelkeznek OPC UA-csomópontok és [az OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) ezek a csomópontok értékeit küld a csatlakoztatott gyár. Az érintett műveletek közé tartoznak az alábbiak:

* Például az aktuális energiafogyasztás aktuális működési állapotát.
* Előállított éles adatokat, például a termékek száma.

Az Irányítópult segítségével feltárni a Contoso gyári topológia lefelé az állomásszintű nézetig globális nézetben. Lehetővé teszi a csatlakoztatott gyári irányítópultot:

* A Vizualizáció a topológiában az egyes rétegekhez végén OEE és KPI-t.
* A Vizualizáció az OPC UA-csomópontok állomásokon aktuális értékek.
* A globális szinten, az állomás szintről OEE és KPI adatok összesítését.
* A Vizualizáció, riasztások és az értékek eléri a megadott küszöbértékek végrehajtandó műveleteket.

## <a name="connected-factory-topology"></a>Csatlakoztatott Factory-topológia

A topológia gyárak, gyártósorok és állomások hierarchikus:

* A globális szinten feldolgozó csomópont gyermekeként rendelkezik.
* Az előállítók gyártósor csomóponttal rendelkezik gyermekeként.
* A gyártósorok állomás csomópont gyermekeként rendelkezik.
* Az állomások (OPC UA-kiszolgálók) OPC UA-csomópont gyermekeként rendelkezik.

A topológia összes csomópontjában tartozik egy közös meghatározó tulajdonságok:

* A topológia csomópont egyedi azonosítója.
* Egy név.
* Leírása.
* Kép.
* A topológia csomópont gyermekeinek.
* Minimális, cél és maximális értékeket OEE és KPI-értékek és a riasztási műveletek végrehajtásához.

## <a name="topology-configuration-file"></a>Topológia konfigurációs fájl

Az előző szakaszban felsorolt tulajdonságok konfigurálása, a csatlakoztatott gyár megoldás nevű konfigurációs fájlt használ [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Ez a fájl találhatja meg a megoldás forráskódját a `WebApp/Contoso/Topology` mappát.

Az alábbi kódrészlet vázlatát mutatja be a `ContosoTopologyDescription.json` konfigurációs fájlban:

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

  Határozza meg egy leíró nevet, és az irányítópulton megjeleníthető a topológia csomópont csak egy szót kell lennie.

* **Leírás** (írja be a karakterláncot.)

  A topológia csomópont részletesebben ismerteti.

* **Kép** (írja be a karakterláncot.)

  A WebApp megoldás megjelenítése, ha a topológia csomópont információ az irányítópulton látható-e a lemezkép elérési útja.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (írja be `<performance_definition>`)

  Ezek a tulajdonságok definiálása minimális, cél, és maximális értékeket, a üzemeltetési. ábra – riasztások létrehozásához használt. Ezek a tulajdonságok is megadhatók a műveletek végrehajtásához, ha egy riasztás észlelhető.

A `<factory_configuration>` és `<production_line_configuration>` elemek tulajdonság rendelkezik:

* **GUID** (írja be a karakterláncot.)

  A topológia csomópont egyedileg azonosítja.

`<factory_configuration>` tulajdonsággal rendelkezik:

* **Hely** (típus `<location_definition>`)

  Itt adhatja meg, ahol az előállító megtalálható.

`<station_configuration>` tulajdonságokkal rendelkezik:

* **OpcUri** (írja be a karakterláncot.)

  Az OPC UA URI-ja az OPC UA-kiszolgáló állítsa ezt a tulajdonságot.
  Az OPC UA-specifikáció szerint globálisan egyedinek kell lennie, mert ez a tulajdonság az állomás topológia csomópont azonosítására szolgál.

* **OpcNodes**, melyek OPC UA-csomópontokon tömbje (típus `<opc_node_description>`)

`<location_definition>` tulajdonságokkal rendelkezik:

* **Város** (írja be a karakterláncot.)

  A hely legközelebb eső város

* **Ország** (írja be a karakterláncot.)

  Ország, ahol a hely

* **Szélesség** (double típusa)

  A hely földrajzi szélesség

* **Hosszúsági** (double típusa)

  A hely földrajzi hosszúság

`<performance_definition>` tulajdonságokkal rendelkezik:

* **Minimális** (double típusa)

  Alsó küszöb értékét is elérheti. Az aktuális értéke a küszöbérték alá, ha riasztás jön létre.

* **Cél** (double típusa)

  Ideális célértéket.

* **Maximális** (double típusa)

  Felső küszöbérték az érték érhető el. Az aktuális értéke a küszöbérték fölött, ha riasztás jön létre.

* **MinimumAlertActions** (típus `<alert_action>`)

  Határozza meg, műveletek, amelyeket elvégezhet egy minimális riasztásra válaszként.

* **MaximumAlertActions** (típus `<alert_action>`)

  Határozza meg, műveletek, amelyeket elvégezhet a maximális riasztásra válaszként.

`<alert_action`> tulajdonságokkal rendelkezik:

* **Típus** (írja be a karakterláncot.)

  A riasztási művelet típusa. A következő típusú ismert:

  * **AcknowledgeAlert**: a riasztás állapotának a nyugtázott kell módosítani.
  * **CloseAlert**: az összes korábbi értesítés azonos típusú kell már nem jelennek meg az irányítópultot.
  * **CallOpcMethod**: egy OPC UA-metódust kell hívni.
  * **OpenWebPage**: egy böngészőablakban további környezeti információkat megjelenítő kell megnyitni.

* **Leírás** (írja be a karakterláncot.)

  Az irányítópulton látható művelet leírása.

* **A paraméter** (írja be a karakterláncot.)

  A művelet végrehajtásához szükséges paramétereket. Az érték a művelet típusától függ.

  * **AcknowledgeAlert**: nem kötelező paraméter.
  * **CloseAlert**: nem kötelező paraméter.
  * **CallOpcMethod**: a csomópont-információk és a paraméterek, az OPC UA-metódust hívja meg a formátum "NodeId szülőcsomópont NodeId metódus meghívásához, URI-ját az OPC UA-kiszolgálóval."
  * **OpenWebPage**: az URL-cím megjelenítéséhez a böngészőablakban.

`<opc_node_description>` OPC UA-csomópontok egy állomáson (OPC UA-kiszolgáló) kapcsolatos információt tartalmazza. Nincsenek meglévő OPC UA-csomópontok jelöl, hanem a számítási logika, a csatlakoztatott gyár tárolóként használt csomópontok is érvényesek. A következő tulajdonságokkal rendelkezik:

* **NodeId** (írja be a karakterláncot.)

  Az OPC UA címtér az állomáson (OPC UA-kiszolgáló a) csomópont-címe. Szintaxist kell lennie az OPC UA-specifikációt a NodeId megadottak szerint.

* **Melynek** (írja be a karakterláncot.)

  Esetén az OPC UA-csomópont értéke látható az irányítópulton megjelenítendő neve.

* **Relevancia alapján végzett** (írja be a karakterlánc tömbje)

  Azt jelzi, amelynek számítási OEE és KPI-t az OPC UA-csomópont értéke megfelelő. Egyes tömbelemeken a következő értékek egyike lehet:

  * **OeeAvailability_Running**: vonatkozó OEE rendelkezésre állási számítási értéke.
  * **OeeAvailability_Fault**: vonatkozó OEE rendelkezésre állási számítási értéke.
  * **OeePerformance_Ideal**: az érték szükséges számítási teljesítmény oee-mutatója Túllépte, és általában egy állandó értéket.
  * **OeePerformance_Actual**: a számítási teljesítmény oee-mutatója Túllépte a vonatkozó értéke.
  * **OeeQuality_Good**: számítás, a minőség oee-mutatója Túllépte a megfelelő érték.
  * **OeeQuality_Bad**: számítás, a minőség oee-mutatója Túllépte a megfelelő érték.
  * **Kpi1**: KPI1 számítási megfelelő érték.
  * **Kpi2**: KPI2 számítási megfelelő érték.

* **Feladatkategóriát** (írja be a karakterláncot.)

  Azt jelzi, hogy az OPC UA-csomópont értékének kezelésének módját a Time Series Insight-lekérdezésekben és OEE/KPI számításokat. Minden egyes Time Series Insight-lekérdezés célozza meg benne egy adott időtartam, a lekérdezési paraméter, és továbbítja egy eredményt. A Feladatkategóriát azt szabályozza, hogyan az eredmény számított, és a következő értékek egyike lehet:

  * **A diff**: az időtartam az utolsó és az első érték különbsége.
  * **Átlagos**: az időtartam az összes érték átlagát.
  * **Sum**: az időtartam minden értékek összegét.
  * **Utolsó**: jelenleg nincs használatban.
  * **Száma**: az időtartam értékeinek száma.
  * **Maximális**: a timespan maximális értékét.
  * **Min**: az időtartam minimális értéke.
  * **Konstanta**: az eredmény érték ConstValue tulajdonságban megadott egységben.
  * **SubMaxMin**: a maximálisan és a minimális érték közötti különbséget.
  * **Időtartomány**: az időtartam.

* **Egységek** (írja be a karakterláncot.)

  Az irányítópult egy egységet, a megjelenítendő érték határozza meg.

* **Látható** (írja be a logikai változó)

  Vezérlők, ha az érték megjelenjen az irányítópulton.

* **ConstValue** (double típusa)

  Ha a **Feladatkategóriát** van **Const**, akkor ez a tulajdonság értéke a csomópont.

* **Minimális** (double típusa)

  Ha a jelenlegi érték ezen érték alá csökken, majd egy minimális riasztást generál.

* **Maximális** (double típusa)

  Ha a jelenlegi érték kiváltja meghaladja ezt az értéket, majd egy maximális riasztás akkor jön létre.

* **MinimumAlertActions** (típus `<alert_action>`)

  Határozza meg, műveletek, amelyeket elvégezhet egy minimális riasztásra válaszként.

* **MaximumAlertActions** (típus `<alert_action>`)

  Határozza meg, műveletek, amelyeket elvégezhet a maximális riasztásra válaszként.

Az állomás szintjén is megjelenik **szimuláció** objektumokat. Ezek az objektumok csak a csatlakoztatott gyár szimuláció konfigurálásához használt, és nem használható egy valódi topológiát.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>A konfigurációs adatok futásidőben használatáról

A konfigurációs fájlban használt összes tulajdonságok attól függően, hogyan használhatók ezek különböző kategóriákba csoportosíthatók. Ezekben a kategóriákban a következők:

### <a name="visual-appearance"></a>Vizuális megjelenés

Ebbe a kategóriába tartozó tulajdonságok határozzák meg, annak vizuális megjelenését, a csatlakoztatott gyári irányítópultot. Példák erre vonatkozóan:

* Name (Név)
* Leírás
* Kép
* Hely
* Egység
* Látható

### <a name="internal-topology-tree-addressing"></a>Belső topológia fa-címzés

A WebApp megőrzi az adatokat az összes topológia csomópontot tartalmazó belső adatkönyvtár. A Tulajdonságok **Guid** és **OpcUri** kulcsokként tento slovník eléréséhez használt, és egyedinek kell lennie kell.

### <a name="oeekpi-computation"></a>OEE/KPI-számítások

A csatlakoztatott gyár szimuláció OEE/KPI adatok paraméterezni vannak:

* OPC UA csomópont értékek kiszámítása szerepelnek.
* Hogyan ábra számított a telemetriai adatok értékek közül.

Csatlakoztatott gyár használja az oee-mutatója Túllépte a képletek által közzétett a http://www.oeefoundation.org.

Az állomás OPC UA objektumok engedélyezze a használat az OEE/KPI számítási címkézést. A **relevancia alapján végzett** a tulajdonság azt jelzi, hogy mely OEE/KPI. ábra az OPC UA-csomópont értéket kell használni. A **Feladatkategóriát** tulajdonság határozza meg, hogyan a számítás értékét tartalmazza.

### <a name="alert-handling"></a>Riasztások kezelése

Csatlakoztatott gyár támogatja-e egyszerű minimális/maximális, küszöbalapú riasztások előállítását. Nincsenek egy adott riasztás konfigurálhatja előre definiált műveletek száma. A következő tulajdonságok ezt a mechanizmust szabályozhatja:

* Maximum
* Minimális
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>A telemetriai adatok használatával történik

Az egyes műveletek, például a Time Series Insight-lekérdezések létrehozása vagy az utolsó olyan értéket jelenítenek meg a WebApp a betöltött telemetriai adatokat egy címzési séma van szüksége. A csatlakoztatott gyár küldött telemetriát is mappájába kell menteni a belső adatszerkezetek. Ezek a műveletek engedélyezése a két tulajdonság állomás (OPC UA-kiszolgáló) és OPC UA-csomópont szintjén vannak:

* **OpcUri**

  Azonosító (globálisan egyedi) OPC UA-kiszolgáló a telemetriai adatok származnak. A feldolgozott üzenetek, ez a tulajdonság legyen elküldve, **ApplicationUri**.

* **NodeId**

  Az OPC UA-kiszolgálóval csomópont értéke azonosítja. A tulajdonság a formátumnak kell lennie az OPC UA-specifikáció megadottak szerint. A feldolgozott üzenetek, ez a tulajdonság legyen elküldve, **NodeId**.

Ellenőrizze [ez](https://github.com/Azure/iot-edge-opc-publisher) további információ a telemetriai adatokat a csatlakoztatott gyár az OPC-közzétevő használatával betöltött hogyan GitHub-oldalon.

## <a name="example-how-kpi1-is-calculated"></a>Példa: Hogyan KPI1 kiszámítása

A konfigurációt a `ContosoTopologyDescription.json` fájl szabályozza, hogy hogyan OEE/KPI-értékek kiszámítása. Az alábbi példa bemutatja, hogyan szabályozhatja az tulajdonságai ebben a fájlban a KPI1 elkészítése.

A csatlakoztatott gyári KPI1 mérésére szolgál sikeresen gyártott termékek száma az elmúlt órában. Minden állomáshoz (OPC UA-kiszolgáló) található a csatlakoztatott gyár szimuláció biztosít az OPC UA-csomópont (`NodeId: "ns=2;i=385"`), amely biztosítja, hogy a telemetriát a KPI kiszámításához.

Az OPC UA-csomópont konfigurációjának az alábbi kódrészlethez hasonlóan néz ki:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ez a konfiguráció lehetővé teszi a telemetriai adatok értékei közül ez a csomópont a Time Series Insights használatával lekérdezése. A Time Series Insights-lekérdezés kéri le:

* Értékek száma.
* A minimális érték.
* A maximális érték.
* Az összes érték átlagát.
* Az összes összes értékek összegét egyedi **OpcUri** (**ApplicationUri**), **NodeId** párok az egy adott időtartam.

Egy jellemző a **NumberOfManufactureredProducts** értéke csomópontra, hogy csak növeli. Csatlakoztatott gyár használja az az időtartam gyártott termékek számának kiszámítása a **Feladatkategóriát** **SubMaxMin**. A számítás a minimális érték elején. az időtartam és a maximális értéket az időtartam végén kérdezi le.

A **Feladatkategóriát** a konfiguráció konfigurálja a számítási logika az eredmény a maximális és minimális érték különbség kiszámítása. Ezután az eredmények állnak, amikor a gyökérszintű (globális) legfeljebb alsó és az irányítópult látható.

## <a name="next-steps"></a>További lépések

A javasolt következő lépésre az, hogy ismerje meg, hogyan [átjáró üzembe helyezése Windows vagy Linux rendszeren az az Okosgyár-megoldásgyorsító](iot-accelerators-connected-factory-gateway-deployment.md).
