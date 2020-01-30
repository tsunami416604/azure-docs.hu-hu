---
title: B2B-üzenetek nyomon követése Azure Monitor-naplókkal
description: Az Azure-beli integrációs fiókok és Azure Logic Apps B2B-kommunikációjának nyomon követése Log Analytics
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 6e66bdfcfe9e84c1095f03a41439b904c7cb96df
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773716"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>B2B-üzenetek nyomon követése Azure Monitor-naplókkal

Miután beállította a VÁLLALATKÖZI kommunikációt a kereskedelmi partnerek között az integrációs fiókban, ezek a partnerek olyan protokollokkal válthatnak üzeneteket, mint például az AS2, a X12 és a EDIFACT. Az üzenetek megfelelő feldolgozásának ellenőrzéséhez [Azure monitor naplók](../log-analytics/log-analytics-overview.md)használatával követheti nyomon ezeket az üzeneteket. Használhatja például ezeket a webes nyomkövetési funkciókat az üzenetek követéséhez:

* Üzenetek száma és állapota
* Nyugták állapota
* Üzenetek korrelációja a nyugtákkal
* Hibák részletes leírása
* Keresési képességek

> [!NOTE]
> Ez az oldal korábban ismertette a feladatok végrehajtásának lépéseit a Microsoft Operations Management Suite (OMS) használatával, amely a [2019 januári](../azure-monitor/platform/oms-portal-transition.md)kivonása esetén a lépéseket az Azure log Analytics helyett felváltja. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Diagnosztikai naplózással beállított logikai alkalmazás. Megtudhatja, [hogyan hozhat létre logikai alkalmazást](quickstart-create-first-logic-app-workflow.md) , és [hogyan állíthatja be a logikai alkalmazás naplózását](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Figyeléssel és naplózással beállított integrációs fiók. Ismerje meg, [hogyan hozhat létre integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , és [hogyan állíthatja be a fiók figyelését és naplózását](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette meg, [tegye közzé a diagnosztikai információkat Azure monitor naplókba](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Az előző követelmények teljesítése után Log Analytics munkaterületre is szüksége lesz, amelyet a VÁLLALATKÖZI kommunikáció nyomon követésére használhat Log Analyticson keresztül. Ha nem rendelkezik Log Analytics munkaterülettel, megtudhatja, [hogyan hozhat létre log Analytics-munkaterületet](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B megoldás telepítése

Ahhoz, hogy Azure Monitor naplók nyomon kövessék a logikai alkalmazás B2B-üzeneteit, adja hozzá a **Logic apps B2B** megoldást Azure monitor naplókhoz. További információ a [megoldások Azure monitor naplókhoz való hozzáadásáról](../azure-monitor/learn/quick-create-workspace.md).

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe keresse meg a "log Analytics" kifejezést, és válassza a **log Analytics**lehetőséget.

   ![Log Analytics kiválasztása](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. A **log Analytics**alatt keresse meg és válassza ki log Analytics munkaterületét. 

   ![Log Analytics munkaterület kiválasztása](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Az **első lépések a Log Analytics > a** **figyelési megoldások konfigurálása**területen válassza a **megoldások megtekintése**lehetőséget.

   ![Válassza a "megoldások megtekintése" lehetőséget.](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Az Áttekintés lapon válassza a **Hozzáadás**lehetőséget, amely megnyitja a **felügyeleti megoldások** listáját. A listából válassza a **Logic apps B2B**lehetőséget. 

   ![Logic Apps B2B megoldás kiválasztása](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Ha nem találja a megoldást, a lista alján válassza a **Betöltés** lehetőséget, amíg a megoldás meg nem jelenik.

1. Válassza a **Létrehozás**lehetőséget, erősítse meg log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, majd válassza a **Létrehozás** újra lehetőséget.   

   ![Válassza a létrehozás lehetőséget Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Ha nem szeretne meglévő munkaterületet használni, akkor most már létrehozhat egy új munkaterületet is.

1. Ha elkészült, lépjen vissza a munkaterület **Áttekintés** lapjára. 

   Az Logic Apps B2B megoldás most megjelenik az Áttekintés oldalon. 
   A B2B-üzenetek feldolgozásakor a rendszer frissíti az üzenetek számait ezen az oldalon.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>B2B-üzenet adatainak megtekintése

A B2B-üzenetek feldolgozása után megtekintheti az üzenetek állapotát és részleteit a **Logic apps B2B** csempén.

1. Lépjen a Logic Analytics-munkaterületre, és nyissa meg az Áttekintés lapot. Válassza a **Logic apps B2B**lehetőséget.

   ![Frissített üzenetek száma](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Alapértelmezés szerint a **Logic apps B2B** csempe egyetlen nap alapján jeleníti meg az értékeket. Az adathatókör más intervallumra való módosításához válassza ki a hatókör-vezérlőelemet az oldal tetején:
   > 
   > ![Változási időköz](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Az üzenet állapota irányítópult megjelenése után megtekintheti az adott üzenet típusát, amely egyetlen nap alapján jeleníti meg az adatokat. Válassza ki az **AS2**, a **X12**vagy a **EDIFACT**csempét.

   ![Üzenet állapotának megtekintése](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Megjelenik az üzenetek listája a kiválasztott csempéhez. 
   Az egyes Üzenetbeállítások tulajdonságaival kapcsolatos további tudnivalókért tekintse meg a következő üzenet-tulajdonságok leírását:

   * [AS2-üzenet tulajdonságai](#as2-message-properties)
   * [X12 üzenet tulajdonságai](#x12-message-properties)
   * [EDIFACT üzenet tulajdonságai](#EDIFACT-message-properties)

   Például az AS2-üzenetek listája a következőhöz hasonló lehet:

   ![AS2-üzenetek megtekintése](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Adott üzenetekhez tartozó bemenetek és kimenetek megtekintéséhez vagy exportálásához válassza ki az üzeneteket, és válassza a **Letöltés**lehetőséget. Amikor a rendszer kéri, mentse a. zip fájlt a helyi számítógépre, majd bontsa ki a fájlt. 

   A kibontott mappa minden kiválasztott üzenethez tartalmaz egy mappát. 
   Ha a nyugtákat állítja be, az üzenet mappája is tartalmazza a visszaigazolási adatokat tartalmazó fájlokat. 
   Minden üzenet mappája legalább a következő fájlokat tartalmazhatja: 
   
   * Ember által olvasható fájlok a bemeneti adattartalommal és a kimeneti adattartalommal kapcsolatos adatokkal
   * Kódolt fájlok bemenetekkel és kimenetekkel

   Minden egyes üzenet típusa esetén itt találja a mappa-és fájlnevek formátumát:

   * [AS2-mappa és fájlnév-formátumok](#as2-folder-file-names)
   * [X12 mappájának és fájlnevének formátuma](#x12-folder-file-names)
   * [EDIFACT mappájának és fájlnevének formátuma](#edifact-folder-file-names)

   ![Üzenetfájl letöltése](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Ha az összes műveletet ugyanazzal a futtatási AZONOSÍTÓval szeretné megtekinteni, a **napló keresése** lapon válasszon egy üzenetet az üzenetlistában.

   Ezeket a műveleteket oszlop szerint rendezheti, vagy megkeresheti az adott eredményeket.

   ![Ugyanazzal a futtatási AZONOSÍTÓval rendelkező műveletek](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Ha előre elkészített lekérdezésekkel szeretné keresni az eredményeket, válassza a **Kedvencek**lehetőséget.

   * Megtudhatja [, hogyan hozhat létre lekérdezéseket szűrők hozzáadásával](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   További információ arról, [hogyan keresheti meg a naplókban végzett keresések adatait Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md).

   * Ha módosítani szeretné a lekérdezést a keresőmezőbe, frissítse a lekérdezést a szűrőként használni kívánt oszlopokkal és értékekkel.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Az AS2-, X12-és EDIFACT-üzenetek tulajdonságainak leírása és formátuma

Minden üzenet típusa esetén itt láthatók a letöltött üzenetek tulajdonságainak leírása és formátuma.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2-üzenet tulajdonságainak leírása

Az alábbiakban láthatók az egyes AS2-üzenetek tulajdonságainak leírása.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A **fogadási beállításokban**megadott vendég partner vagy az AS2-szerződés **küldési beállításai** között megadott gazda partner |
| Fogadó | A **fogadási beállításokban**megadott gazda partner vagy az AS2-szerződés **küldési beállításai** között megadott vendég partner |
| Logic App | A logikai alkalmazás, amelyben az AS2-műveletek vannak beállítva |
| Állapot | Az AS2-üzenet állapota <br>Sikeres = kapott vagy elküldött egy érvényes AS2-üzenetet. Nincs beállítva MDN. <br>Sikeres = kapott vagy elküldött egy érvényes AS2-üzenetet. A MDN beállítása és fogadása, vagy a MDN küldése történik. <br>Sikertelen = érvénytelen AS2-üzenetet kapott. Nincs beállítva MDN. <br>Függőben = fogadott vagy küldött egy érvényes AS2-üzenetet. A MDN beállítása megtörténik, és a rendszer MDN vár. |
| Nyugta | A MDN üzenet állapota <br>Elfogadva = pozitív MDN kapott vagy küldött. <br>Függőben = várakozás egy MDN fogadására vagy elküldésére. <br>Elutasítva = fogadott vagy negatív MDN küldött. <br>Nem kötelező = a MDN nincs beállítva a szerződésben. |
| Irány | Az AS2-üzenet iránya |
| Korrelációs azonosító | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| Üzenet azonosítója | Az AS2-üzenet azonosítója az AS2-üzenetek fejlécében |
| Időbélyeg | Az az idő, amikor az AS2-művelet feldolgozta az üzenetet |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>A letöltött üzenetek fájljainak AS2-formátuma

Itt látható a letöltött AS2-üzenetek mappájának és fájljainak formátuma.

| Mappa vagy fájl | Név formátuma |
| :------------- | :---------- |
| Üzenet mappája | [Sender]\_[fogadó]\_AS2\_[korrelációs azonosító]\_[Message-ID]\_[időbélyeg] |
| Bemenet, kimenet, és ha be van állítva, a nyugtázott fájlok | **Bemeneti adattartalom**: [sender]\_[fogadó]\_AS2\_[korrelációs azonosító]\_input_payload. txt </p>**Kimeneti adattartalom**: [sender]\_[fogadó]\_AS2\_[korrelációs azonosító]\_kimenet\_hasznos adat. txt </p></p>**Bemenetek**: [sender]\_[fogadó]\_AS2\_[korrelációs azonosító]\_Inputs. txt </p></p>**Kimenetek**: [sender]\_[fogadó]\_AS2\_[korrelációs azonosító]\_kimenet. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12-üzenet tulajdonságainak leírása

Az alábbiakban az egyes X12-üzenetekhez tartozó tulajdonságok leírását olvashatja.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A **fogadási beállításokban**megadott vendég partner vagy egy X12-szerződés **küldési beállításaiban** megadott gazda partner |
| Fogadó | A **fogadási beállításokban**megadott gazda partner vagy egy X12-szerződés **küldési beállításaiban** megadott vendég partner |
| Logic App | Az a logikai alkalmazás, amelyben a X12 műveletek be vannak állítva |
| Állapot | A X12 üzenet állapota <br>Sikeres = fogadott vagy elküldött egy érvényes X12 üzenetet. Nincs beállítva funkcionális ACK. <br>Sikeres = fogadott vagy elküldött egy érvényes X12 üzenetet. A funkcionális ACK beállítása és fogadása, vagy egy funkcionális ACK küldése. <br>Sikertelen = kapott vagy érvénytelen X12 üzenetet küldött. <br>Függőben = fogadott vagy küldött egy érvényes X12 üzenetet. A funkcionális ACK beállítása be van állítva, és a rendszer funkcionális ACK-t vár. |
| Nyugta | Funkcionális ACK (997) állapot <br>Elfogadva = kapott vagy pozitív működési nyugtát küldött. <br>Elutasítva = kapott vagy negatív működési nyugtát küldött. <br>Függőben = várt működési ACK, de nem érkezett. <br>Függőben = létrehozta a funkcionális ACK-t, de nem tud elküldeni a partnernek. <br>Nem kötelező = a funkcionális ACK nincs beállítva. |
| Irány | A X12 üzenet iránya |
| Korrelációs azonosító | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| Msg típusa | Az EDI X12 üzenet típusa |
| ICN | A X12-üzenethez tartozó Interchange Control-szám |
| TSCN | A X12 üzenethez tartozó készlettranzakció-készlet vezérlőelem száma |
| Időbélyeg | Az az idő, amikor a X12 művelet feldolgozta az üzenetet |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 a letöltött üzenetek fájljainak formátuma

Az alábbiakban a letöltött X12-üzenetek és-fájlok formátuma látható.

| Mappa vagy fájl | Név formátuma |
| :------------- | :---------- |
| Üzenet mappája | [Sender]\_[fogadó]\_X12\_[Interchange-Control-Number]\_[globális-Control-Number]\_[tranzakció-set-Number]\_[timestamp] |
| Bemenet, kimenet, és ha be van állítva, a nyugtázott fájlok | **Bemeneti adattartalom**: [sender]\_[fogadó]\_X12\_[Interchange-Control-number]\_input_payload. txt </p>**Kimeneti adattartalom**: [sender]\_[fogadó]\_X12\_[Interchange-Control-number]\_kimeneti\_hasznos adat. txt </p></p>**Bemenetek**: [sender]\_[fogadó]\_X12\_[Interchange-Control-number]\_Inputs. txt </p></p>**Kimenetek**: [sender]\_[fogadó]\_X12\_[Interchange-Control-number]\_kimenet. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT-üzenet tulajdonságainak leírása

Az alábbiakban az egyes EDIFACT-üzenetekhez tartozó tulajdonságok leírását olvashatja.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A **fogadási beállításokban**megadott vendég partner vagy egy EDIFACT-szerződés **küldési beállításaiban** megadott gazda partner |
| Fogadó | A **fogadási beállításokban**megadott gazda partner vagy egy EDIFACT-szerződés **küldési beállításaiban** megadott vendég partner |
| Logic App | Az a logikai alkalmazás, amelyben a EDIFACT műveletek be vannak állítva |
| Állapot | A EDIFACT üzenet állapota <br>Sikeres = fogadott vagy elküldött egy érvényes EDIFACT üzenetet. Nincs beállítva funkcionális ACK. <br>Sikeres = fogadott vagy elküldött egy érvényes EDIFACT üzenetet. A funkcionális ACK beállítása és fogadása, vagy egy funkcionális ACK küldése. <br>Sikertelen = kapott vagy érvénytelen EDIFACT üzenetet küldött <br>Függőben = fogadott vagy küldött egy érvényes EDIFACT üzenetet. A funkcionális ACK beállítása be van állítva, és a rendszer funkcionális ACK-t vár. |
| Nyugta | Funkcionális ACK (CONTRL) állapot <br>Elfogadva = kapott vagy pozitív működési nyugtát küldött. <br>Elutasítva = kapott vagy negatív működési nyugtát küldött. <br>Függőben = várt működési ACK, de nem érkezett. <br>Függőben = létrehozta a funkcionális ACK-t, de nem tud elküldeni a partnernek. <br>Nem kötelező = a funkcionális ACK nincs beállítva. |
| Irány | A EDIFACT üzenet iránya |
| Korrelációs azonosító | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| Msg típusa | A EDIFACT üzenet típusa |
| ICN | A EDIFACT-üzenethez tartozó Interchange Control-szám |
| TSCN | A EDIFACT üzenethez tartozó készlettranzakció-készlet vezérlőelem száma |
| Időbélyeg | Az az idő, amikor a EDIFACT művelet feldolgozta az üzenetet |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT a letöltött üzenetek fájljainak formátuma

Az alábbiakban a letöltött EDIFACT-üzenetek és-fájlok formátuma látható.

| Mappa vagy fájl | Név formátuma |
| :------------- | :---------- |
| Üzenet mappája | [Sender]\_[fogadó]\_EDIFACT\_[Interchange-Control-Number]\_[globális-Control-Number]\_[tranzakció-set-Number]\_[timestamp] |
| Bemenet, kimenet, és ha be van állítva, a nyugtázott fájlok | **Bemeneti adattartalom**: [sender]\_[fogadó]\_EDIFACT\_[Interchange-Control-number]\_input_payload. txt </p>**Kimeneti adattartalom**: [sender]\_[fogadó]\_EDIFACT\_[Interchange-Control-number]\_kimeneti\_hasznos adat. txt </p></p>**Bemenetek**: [sender]\_[fogadó]\_EDIFACT\_[Interchange-Control-number]\_Inputs. txt </p></p>**Kimenetek**: [sender]\_[fogadó]\_EDIFACT\_[Interchange-Control-number]\_kimenet. txt |
|          |             |

## <a name="next-steps"></a>Következő lépések

* [B2B-üzenetek lekérdezése Azure Monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
