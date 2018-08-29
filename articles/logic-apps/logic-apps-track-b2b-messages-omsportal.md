---
title: Az Azure Log Analytics – Azure Logic Apps B2B-üzenetek nyomon követése |} A Microsoft Docs
description: Integrációs fiókok és az Azure Log Analytics az Azure Logic Apps B2B kommunikációs nyomon követése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 06/19/2018
ms.openlocfilehash: 5bf5385824eb9b711a2fee547c29d24d7ef5a01d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125768"
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Nyomon követheti az Azure Log Analytics B2B-kommunikáció

Miután beállította a B2B-kommunikáció két közötti üzleti folyamatokkal vagy az integrációs fiók használatával fut ezeket az entitásokat is tudjon cserélni egymással üzeneteket. Ellenőrizze, hogy megfelelően dolgozzák fel ezeket az üzeneteket, AS2, X12, nyomon követheti és EDIFACT üzenetek [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Például üzenetek nyomon követése a webes követési képességek is használhatja:

* Üzenetek száma és állapota
* Visszaigazolás állapota
* A visszaigazolások összevetését üzenetek
* Hibák részletes leírását
* Keresési funkciókkal

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást, amely a diagnosztikai naplózás be van állítva. Ismerje meg, [Logic Apps-alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md) és [a logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Egy integrációs fiók, amely a figyelés és naplózás be van állítva. Ismerje meg, [egy integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [monitorozási és naplózási fiók beállítása](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette, [diagnosztikai adatok közzététele a Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Miután teljesítette az előbbi követelményeknek, rendelkeznie kell egy Log Analytics-munkaterületnek. A B2B-kommunikáció a Log Analytics követési használjon ugyanazon a munkaterületen. 
>  
> Ha nem rendelkezik a Log Analytics-munkaterületet, további [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="add-the-logic-apps-b2b-solution-to-log-analytics"></a>Adja hozzá a Logic Apps B2B-megoldás a Log Analyticsbe

Ahhoz, hogy a logikai alkalmazás B2B üzenetek nyomon követése a Log Analytics, hozzá kell adnia a **Logic Apps B2B** megoldás az OMS-portálra. Tudjon meg többet [megoldások hozzáadása a Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. Az a [az Azure portal](https://portal.azure.com), válassza a **minden szolgáltatás**. Keresse meg a "log analytics", és válassza a **Log Analytics** itt látható módon:

   ![Keresse meg a Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. A **felügyeleti**, válassza a **áttekintése**.

   ![Válassza ki a Log Analytics-portál](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Válassza a kezdőlap nyílik meg, miután **Hozzáadás** Logic Apps B2B-megoldás telepítéséhez.    
   ![Válassza ki a Megoldástárából](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

5. A **felügyeleti megoldások**, keresése és létrehozása **Logic Apps B2B** megoldás.     
   ![Válassza ki a Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   A kezdőlapon, csempéje **Logic Apps B2B-üzenetek** jelenik meg. 
   Ez a csempe frissíti az üzenetek száma, amikor a rendszer a B2B-üzenetek feldolgozása.

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Üzenet állapotával és részleteivel kapcsolatban a Log Analytics nyomon követése

1. Után B2B-üzenetek feldolgozása, megtekintheti a állapotának és részleteinek ezeket az üzeneteket. Az Áttekintés oldalon válassza ki a **Logic Apps B2B-üzenetek** csempére.

   ![Frissített üzenetek száma](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Alapértelmezés szerint a **Logic Apps B2B-üzenetek** csempe egy nap alapján jeleníti meg. Az adatok hatókör egy másik időköz módosításához válassza a lap tetején a hatókör vezérlő:
   > 
   > ![Adatok hatókörének módosítása](media/logic-apps-track-b2b-messages-omsportal/server-filter.png)
   >

2. Az üzenet állapota irányítópult megjelenése után egy meghatározott üzenet típusát, amely egy nap alapján adatokat jelenít meg további részleteket is megtekintheti. Válassza ki a csempét **AS2**, **X12**, vagy **EDIFACT**.

   ![Üzenet állapotának megtekintése](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Az üzenetek listáját a kiválasztott csempe jelenik meg. 
   Minden egyes üzenettípus tulajdonságaival kapcsolatos további tudnivalókért tekintse meg a message tulajdonság leírások:

   * [AS2-üzenet tulajdonságai](#as2-message-properties)
   * [X12 üzenet tulajdonságai](#x12-message-properties)
   * [EDIFACT-üzenet tulajdonságai](#EDIFACT-message-properties)

   Ha például a következő egy AS2-üzenet lista néz:

   ![AS2-üzenet megtekintése](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Megtekintéséhez, vagy exportálja a bemeneteit és kimeneteit adott üzenetek, válassza ki azokat az üzeneteket, és válassza **letöltése**. Amikor a rendszer kéri, mentse a .zip-fájlt a helyi számítógépen, és bontsa ki a fájlt. 

   A kibontott mappát tartalmaz egy mappát az egyes kiválasztott üzenetekhez. 
   Ha beállította a nyugtázás, az üzenetek mappa tartalmazza nyugtázási adatokkal fájlokat is. 
   Minden üzenet mappa van legalább ezeket a fájlokat: 
   
   * A bemeneti forgalma és a kimeneti tartalom részleteit az emberek számára olvasható fájlok
   * A bemenetek és kimenetek kódolt fájlok

   Az egyes üzenet a mappa és fájl formátumok itt találja:

   * [AS2-mappa és fájl nevét formátumok](#as2-folder-file-names)
   * [X12 mappa és fájl neve formátumok](#x12-folder-file-names)
   * [EDIFACT-mappa és fájl nevét formátumok](#edifact-folder-file-names)

   ![Üzenetek fájljainak letöltése](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Megtekintése összes műveletet, amelyek azonos futtatási Azonosítóját, a a **naplóbeli keresés** lapon, válasszon egy üzenetet az állapotüzenet-listában.

   Ezek a műveletek oszlopban, vagy konkrét találatok keresését szerint rendezheti.

   ![Ugyanazzal a tevékenységek futtatási Azonosítóját](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Keresse meg az előre létrehozott lekérdezések eredményeit, válassza a **Kedvencek**.

   * Ismerje meg, [hogyan hozhatók létre olyan lekérdezések, szűrők hozzáadásával](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Tudjon meg többet vagy [tartalmazó naplókeresések adatok megkeresése a Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Módosítsa a lekérdezést a keresőmezőbe, hogy frissítse a lekérdezést az oszlopok és szűrők használni kívánt értékeket.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Vlastnost leírásokat és névformátumok használatát AS2, X 12 és EDIFACT-üzenetek

Minden egyes üzenettípus Íme tulajdonság leírások és névformátumok használatát a letöltött üzenetfájlok.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2-üzenet tulajdonságleírások

Az alábbiakban az egyes AS2-üzenet tulajdonság leírása.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A megadott vendégpartner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** az AS2-egyezményt |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a vendégpartner megadott **küldési beállítások** az AS2-egyezményt |
| Logikai alkalmazás | A logikai alkalmazást, amennyiben vannak beállítva az AS2-műveletek |
| status | Az AS2-üzenet állapota <br>Sikeres = fogadott vagy küldött egy érvényes AS2-üzenet. Nincs MDN be van állítva. <br>Sikeres = fogadott vagy küldött egy érvényes AS2-üzenet. MDN beállítása és kapott, vagy az MDN zajlik. <br>Nem sikerült = érvénytelen AS2-üzenet érkezett. Nincs MDN be van állítva. <br>Függőben lévő = fogadott vagy küldött egy érvényes AS2-üzenet. MDN be van állítva, és MDN várt. |
| Nyomon követés | Az MDN-üzenet állapota <br>Elfogadott = fogadott vagy küldött pozitív MDN. <br>Függőben lévő Várakozás fogadni, vagy az MDN küldésének cél =. <br>Elutasított = fogadott vagy küldött negatív MDN. <br>Nem szükséges = MDN nincs beállítva a szerződés. |
| Irány | Az AS2-üzenet iránya |
| Korrelációs azonosító | Az azonosítója, amely az összes eseményindítók és műveletek, a logikai alkalmazás |
| Üzenet azonosítója | Az AS2-üzenetek fejlécének az AS2-Üzenetazonosító |
| Időbélyeg | Az idő, az üzenet az AS2-művelet feldolgozásakor |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>A letöltött üzenetfájlok AS2 formátumok

Az alábbiakban az egyes letöltött AS2-üzenet mappa és fájlok formátumok.

| Fájl vagy mappa | Névformátum |
| :------------- | :---------- |
| Üzenet mappa | [küldő] \_[fogadó]\_AS2\_[korrelációs azonosító]\_[message-ID]\_[időbélyeg] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_input_payload.txt </p>**Kimenő hasznos**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 tulajdonságleírások üzenet

Az egyes X12 tulajdonság leírása az alábbiakban üzenet.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A megadott vendégpartner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** X12 a szerződés |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a vendégpartner megadott **küldési beállítások** X12 a szerződés |
| Logikai alkalmazás | A logikai alkalmazást, a X12 műveletek beállítása |
| status | A X12 üzenet állapota <br>Sikeres = fogadott vagy küldött egy érvényes X12 üzenet. Nem funkcionális ack be van állítva. <br>Sikeres = fogadott vagy küldött egy érvényes X12 üzenet. Funkcionális ack beállítása és kapott, vagy egy funkcionális ack zajlik. <br>Nem sikerült = fogadott vagy küldött érvénytelen X12 üzenet. <br>Függőben lévő = fogadott vagy küldött egy érvényes X12 üzenet. Funkcionális ack be van állítva, és a egy funkcionális ack várt. |
| Nyomon követés | Funkcionális (997) nyugtázási állapot <br>Elfogadott = fogadott vagy küldött egy pozitív működési ACK-ra. <br>Elutasított = fogadott vagy küldött egy negatív működési ACK-ra. <br>Függőben lévő = a egy működési ack várt, de nem érkezett meg. <br>Függőben lévő = jön létre a egy működési ack, de nem tud küldeni a partner. <br>Nem szükséges funkcionális = ack nincs beállítva. |
| Irány | A X12 üzenet iránya |
| Korrelációs azonosító | Az azonosítója, amely az összes eseményindítók és műveletek, a logikai alkalmazás |
| Üzenettípus | Az EDI, X 12-üzenet típusa |
| ICN | Az adatcsere Ellenőrzőszámának a X12 az üzenet |
| TSCN | A tranzakció beállítása ellenőrzőszám a X12 az üzenet |
| Időbélyeg | Az idő során a X12 műveletet az üzenet feldolgozása |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 letöltött üzenetfájlok formátumok nevét

Az egyes letöltött X12 az alábbiakban a formátumok üzenetek mappa és fájlok.

| Fájl vagy mappa | Névformátum |
| :------------- | :---------- |
| Üzenet mappa | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_input_payload.txt </p>**Kimenő hasznos**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT-üzenet tulajdonságleírások

Az alábbiakban az egyes EDIFACT-üzenet tulajdonság leírása.

| Tulajdonság | Leírás |
| --- | --- |
| Küldő | A megadott vendégpartner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** az EDIFACT-egyezmény |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a vendégpartner megadott **küldési beállítások** az EDIFACT-egyezmény |
| Logikai alkalmazás | A logikai alkalmazás, ahol az EDIFACT-műveletek beállítása |
| status | Az EDIFACT-üzenet állapota <br>Sikeres = fogadott vagy küldött egy érvényes EDIFACT-üzenet. Nem funkcionális ack be van állítva. <br>Sikeres = fogadott vagy küldött egy érvényes EDIFACT-üzenet. Funkcionális ack beállítása és kapott, vagy egy funkcionális ack zajlik. <br>Nem sikerült = fogadott vagy egy érvénytelen EDIFACT-üzenet elküldve <br>Függőben lévő = fogadott vagy küldött egy érvényes EDIFACT-üzenet. Funkcionális ack be van állítva, és a egy funkcionális ack várt. |
| Nyomon követés | Funkcionális (997) nyugtázási állapot <br>Elfogadott = fogadott vagy küldött egy pozitív működési ACK-ra. <br>Elutasított = fogadott vagy küldött egy negatív működési ACK-ra. <br>Függőben lévő = a egy működési ack várt, de nem érkezett meg. <br>Függőben lévő = jön létre a egy működési ack, de nem tud küldeni a partner. <br>Nem szükséges = működési Ack nincs beállítva. |
| Irány | Az EDIFACT-üzenet iránya |
| Korrelációs azonosító | Az azonosítója, amely az összes eseményindítók és műveletek, a logikai alkalmazás |
| Üzenettípus | Az EDIFACT-üzenet típusa |
| ICN | Az adatcsere Ellenőrzőszámának az EDIFACT-üzenet |
| TSCN | A tranzakció beállítása ellenőrzőszám az EDIFACT-üzenet |
| Időbélyeg | Az idő, az üzenet az EDIFACT-művelet feldolgozásakor |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>A letöltött üzenetfájlok EDIFACT formátumok

Az alábbiakban az egyes letöltött EDIFACT-üzenet mappa és fájlok formátumok.

| Fájl vagy mappa | Névformátum |
| :------------- | :---------- |
| Üzenet mappa | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_input_payload.txt </p>**Kimenő hasznos**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>További lépések

* [B2B-üzenetek a Log Analytics-lekérdezést](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)