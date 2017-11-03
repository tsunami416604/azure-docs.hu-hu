---
title: "Az Operations Management Suite - Azure Logic Apps B2B üzenetek nyomon követése |} Microsoft Docs"
description: "Az integráció fiók és a logikai alkalmazások a műveletek Suite (OMS) az Azure Naplóelemzés B2B kommunikációs nyomon követése"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>A Microsoft Operations Management Suite (OMS) B2B kommunikációs nyomon követése

Miután beállította a B2B kommunikációját két üzleti folyamatok vagy a integrációs fiókon keresztül alkalmazásokat futtató entitásokból tudjon cserélni egymással üzeneteket. Ellenőrizze, hogy ezek az üzenetek feldolgozása helyesen, AS2, X12, nyomon követheti és EDIFACT az üzeneteket a [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md) a a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Például a web-alapú nyomkövetési lehetőségeket biztosítanak is használhatja az üzenetek nyomon követése:

* Üzenet számán és állapota
* Nyugták állapota
* A nyugtázások összefüggésbe üzenetek
* Hibák részletes hiba leírása
* Keresési képességek

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást a diagnosztikai naplózás be van állítva. Ismerje meg, [logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md) és [adott logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Integráció fiók be van állítva a figyelés és naplózás. Ismerje meg, [integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [figyelés és naplózás fiók beállításával](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette, [diagnosztikai adatok közzétételére Naplóelemzési](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) az OMS Szolgáltatáshoz.

> [!NOTE]
> Miután teljesítette az előző követelményeknek, rendelkeznie kell egy munkaterület a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Az azonos OMS-munkaterület nyomon követése a B2B kommunikáció OMS kell használnia. 
>  
> Ha még nem rendelkezik az OMS-munkaterület, [OMS-munkaterület létrehozása](../log-analytics/log-analytics-get-started.md).

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Adja hozzá a Logic Apps B2B megoldás az Operations Management Suite (OMS)

Ahhoz, hogy a logikai alkalmazás B2B messages nyomon OMS, hozzá kell adnia a **Logic Apps B2B** megoldást jelent az OMS-portálon. További információ [megoldások hozzáadása OMS](../log-analytics/log-analytics-get-started.md).

1. Az a [Azure-portálon](https://portal.azure.com), válassza a **több szolgáltatások**. Keresse meg a "naplóelemzési", és válassza a **Naplóelemzési** itt látható módon:

   ![A Naplóelemzési keresése](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. A **Naplóelemzési**, található, és válassza ki az OMS-munkaterület. 

   ![Az OMS-munkaterület kiválasztása](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. A **felügyeleti**, válassza a **OMS-portálon**.

   ![Válassza ki az OMS-portálon](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Ha megnyílt az OMS kezdőlapját, válassza ki a **megoldások gyűjtemény**.    

   ![Válassza ki a megoldások gyűjteménye](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. A **minden megoldás**, keresése és kiválasztása **Logic Apps B2B**.     

   ![Válassza ki a Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. A **Logic Apps B2B**, válassza a **Hozzáadás**.

   ![Válasszon hozzáadása](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   A kezdőlapon OMS, a csempe **Logic Apps B2B üzenetek** csomópontként jelenik meg. 
   Ez a csempe frissíti az üzenetek száma, amikor a B2B üzenetek feldolgozása.

   ![Logic Apps B2B üzenetek csempe OMS kezdőlapján](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Nyomon követheti a Messaging-állapot és az Operations Management Suite részletes adatait

1. Után a B2B üzenetek feldolgozása, megtekintheti az állapot és az üzenetek adatait. Az OMS kezdőlapján válassza ki a **Logic Apps B2B üzenetek** csempére.

   ![Frissített üzenetek száma](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Alapértelmezés szerint a **Logic Apps B2B üzenetek** csempe megjeleníti az adatokat egy nap alapján. Az adatok hatókör egy másik időköz módosításához válassza ki a hatókör vezérlő az OMS-oldal tetején:
   > 
   > ![Adatok hatókörének módosítása](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Az üzenet állapota irányítópult megjelenése után további részleteket az adott üzenettípus, megjelenítheti az adatokat egy nap alapján. Válassza ki a csempéjére a hozzá tartozó **AS2**, **X12**, vagy **EDIFACT**.

   ![Üzenet állapotának megtekintése](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   A kiválasztott csempe jelenik meg az üzenetek listáját. 
   Az egyes üzenet tulajdonságaival kapcsolatos további tudnivalókért tekintse meg az üzenet tulajdonság leírások:

   * [AS2-üzenet tulajdonságai](#as2-message-properties)
   * [X12 üzenet tulajdonságai](#x12-message-properties)
   * [EDIFACT üzenet tulajdonságai](#EDIFACT-message-properties)

   Például ez hogyan nézhet ki egy AS2 állapotüzenet-listában:

   ![AS2-üzenet megtekintése](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Megtekintéséhez, vagy exportálja a be- és kimenetekkel adott üzenetek, válassza ki azokat az üzeneteket, és válassza a **letöltése**. Amikor a rendszer kéri, mentse a .zip-fájlt a helyi számítógépen, és bontsa ki a fájlt. 

   A kibontott mappát egy mappát az összes kijelölt üzenet tartalmazza. 
   A nyugtázás állít be, ha az üzenet mappa is fájlok nyugtázási adatokkal. 
   Minden üzenet mappa van legalább ezeket a fájlokat: 
   
   * A bemeneti forgalma és a kimeneti hasznos részletek emberek számára olvasható fájlokat
   * A be- és kimenetekkel kódolt fájlok

   Az egyes üzenet a mappa és fájl formátumok itt található:

   * [AS2 mappához és fájlhoz formátumok](#as2-folder-file-names)
   * [X12 mappa és fájl neve formátumok](#x12-folder-file-names)
   * [EDIFACT mappához és fájlhoz formátumok](#edifact-folder-file-names)

   ![Fájlok letöltése](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Megtekintéséhez, amely az összes műveletet futtatásához Azonosítóját, a a **naplófájl-keresési** lapon, az üzenet listájából válasszon ki egy üzenetet.

   Ezek a Műveletek oszlop, vagy keresse meg az adott eredmények rendezheti.

   ![Azonos műveletek futtatás azonosítója](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * A keresési eredményekben előre elkészített lekérdezésekkel, válassza a **Kedvencek**.

   * Ismerje meg, [lekérdezések létrehozása szűrők hozzáadásával](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   További információ vagy [hogyan napló keresések rendelkező adatok kereséséhez a Naplóelemzési](../log-analytics/log-analytics-log-searches.md).

   * Módosíthatja a lekérdezés a keresési mezőbe, frissítse a lekérdezést az oszlopok és szűrők használni kívánt értékeket.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Tulajdonság-leírások és formátumok az AS2, X 12 és EDIFACT-üzenetek

Az egyes üzenet az alábbiakban a tulajdonságleírások és a letöltött fájlok formátumok.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2-üzenet tulajdonságleírások

Az alábbiakban a tulajdonságleírások minden egyes AS2-üzenet esetében.

| Tulajdonság | Leírás |
| --- | --- |
| Feladó | A megadott Vendég partner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** AS2 megállapodás |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a Vendég partner megadott **küldési beállítások** AS2 megállapodás |
| Logikai alkalmazás | A logikai alkalmazást, ahol az AS2 műveletek beállítása |
| status | Az AS2 üzenet állapota <br>Sikeres = fogadott, vagy egy érvényes AS2 üzenetet küldött. Nincs MDN be van állítva. <br>Sikeres = fogadott, vagy egy érvényes AS2 üzenetet küldött. MDN beállítása és kapott, vagy MDN zajlik. <br>Nem sikerült = érvénytelen AS2 üzenetet kapott. Nincs MDN be van állítva. <br>Függőben lévő = fogadott, vagy egy érvényes AS2 üzenetet küldött. MDN be van állítva, és MDN várt. |
| Nyugtázási | A MDN Messaging-állapot <br>Elfogadható = fogadott vagy elküldött egy pozitív MDN. <br>Függőben lévő egy MDN küld és fogad váró =. <br>Elutasított = fogadott vagy elküldött egy negatív MDN. <br>Nem szükséges = MDN nincs beállítva a szerződésben. |
| Irány | Az AS2-üzenet irányát |
| Korrelációs azonosító | A hibához az eseményindítók és műveletek a logikai alkalmazás azonosítója |
| Üzenet azonosítója | Az AS2 üzenetfejlécek a AS2 üzenet azonosítója |
| időbélyeg | Az AS2 művelet feldolgozásának az üzenet időpontja |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>A letöltött fájlok AS2 formátumok

Az alábbiakban a formátumok minden letöltött AS2 üzenet mappában és fájlokat.

| Fájl vagy mappa | Nevének formátuma |
| :------------- | :---------- |
| Üzenet mappa | [küldő] \_[fogadó]\_AS2\_[korrelációs azonosító]\_[üzenet-azonosítója]\_[időbélyeg] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_input_payload.txt </p>**Kimeneti hasznos**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_AS2\_[korrelációs azonosító]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 tulajdonságleírások üzenet

Az alábbiakban a tulajdonság leírásainak minden X12 üzenet.

| Tulajdonság | Leírás |
| --- | --- |
| Feladó | A megadott Vendég partner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** egy X12 a megállapodás |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a Vendég partner megadott **küldési beállítások** egy X12 a megállapodás |
| Logikai alkalmazás | A logikai alkalmazás ahol a X12 műveletek be vannak állítva. |
| status | A X12 üzenet állapot <br>Sikeres = fogadott, vagy egy érvényes X12 küldött üzenetet. Egyetlen működési nyugtázási be van állítva. <br>Sikeres = fogadott, vagy egy érvényes X12 küldött üzenetet. Működési nyugtázási beállítása és kapott, illetve funkcionális nyugtázási elküldésekor történik. <br>Nem sikerült = fogadott vagy elküldött egy érvénytelen X12 üzenet. <br>Függőben lévő = fogadott, vagy egy érvényes X12 küldött üzenetet. Funkcionális nyugtázási be van állítva, és működési nyugtázási várt. |
| Nyugtázási | Funkcionális nyugtázási (997) állapota <br>Elfogadható = fogadott, vagy egy pozitív működési nyugtát küldött <br>Elutasított = fogadott, vagy egy negatív működési nyugtát küldött <br>Függőben lévő = működési nyugtázási várt, de nem érkezett meg. <br>Függőben lévő = előállított működési nyugtázási, de nem lehet elküldeni a partnernek. <br>Nem szükséges funkcionális = nyugtázási nincs beállítva. |
| Irány | A X12 üzenet irányát |
| Korrelációs azonosító | A hibához az eseményindítók és műveletek a logikai alkalmazás azonosítója |
| Üzenet típusa | EDI X 12 üzenettípus |
| ICN | Az a X12 Interchange ellenőrző szám üzenet |
| TSCN | A tranzakció beállítása vezérlő számot a X12 üzenet |
| időbélyeg | Az idő során a X12 műveletet az üzenet feldolgozása |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 formátumok nevet letöltött Üzenetfájl

Minden egyes X12 letölteni az alábbiakban a formátumok üzenetek mappa és a fájlokat.

| Fájl vagy mappa | Nevének formátuma |
| :------------- | :---------- |
| Üzenet mappa | [küldő] \_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_[globális-ellenőrző-szám]\_[tranzakció-set-ellenőrző-szám]\_[időbélyeg] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_input_payload.txt </p>**Kimeneti hasznos**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_X12\_[adatcsere-ellenőrző-szám]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT üzenet tulajdonságleírások

Az alábbiakban a tulajdonságleírások minden egyes EDIFACT-üzenet esetében.

| Tulajdonság | Leírás |
| --- | --- |
| Feladó | A megadott Vendég partner **fogadási beállítások**, vagy a fogadó partner megadott **küldési beállítások** EDIFACT megállapodás |
| Fogadó | A megadott fogadó partner **fogadási beállítások**, vagy a Vendég partner megadott **küldési beállítások** EDIFACT megállapodás |
| Logikai alkalmazás | A logikai alkalmazást, ahol a EDIFACT műveletek beállítása |
| status | A EDIFACT Messaging-állapot <br>Sikeres = fogadott, vagy egy érvényes EDIFACT üzenetet küldött. Egyetlen működési nyugtázási be van állítva. <br>Sikeres = fogadott, vagy egy érvényes EDIFACT üzenetet küldött. Működési nyugtázási beállítása és kapott, illetve funkcionális nyugtázási elküldésekor történik. <br>Nem sikerült fogadott = vagy EDIFACT érvénytelen üzenetet küldött <br>Függőben lévő = fogadott, vagy egy érvényes EDIFACT üzenetet küldött. Funkcionális nyugtázási be van állítva, és működési nyugtázási várt. |
| Nyugtázási | Funkcionális nyugtázási (997) állapota <br>Elfogadható = fogadott, vagy egy pozitív működési nyugtát küldött <br>Elutasított = fogadott, vagy egy negatív működési nyugtát küldött <br>Függőben lévő = működési nyugtázási várt, de nem érkezett meg. <br>Függőben lévő = előállított működési nyugtázási, de nem lehet elküldeni a partnernek. <br>Nem szükséges = működési nyugtázási nincs beállítva. |
| Irány | A EDIFACT-üzenet irányát |
| Korrelációs azonosító | A hibához az eseményindítók és műveletek a logikai alkalmazás azonosítója |
| Üzenet típusa | A EDIFACT-üzenet típusa |
| ICN | Az EDIFACT üzenet Interchange ellenőrző szám |
| TSCN | A tranzakció beállítása ellenőrző szám az EDIFACT-üzenet |
| időbélyeg | A EDIFACT művelet feldolgozásának az üzenet időpontja |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>A letöltött fájlok EDIFACT formátumok

Az alábbiakban a formátumok minden letöltött EDIFACT üzenet mappában és fájlokat.

| Fájl vagy mappa | Nevének formátuma |
| :------------- | :---------- |
| Üzenet mappa | [küldő] \_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_[globális-ellenőrző-szám]\_[tranzakció-set-ellenőrző-szám]\_[időbélyeg] |
| Bemeneti, kimeneti és if beállítása nyugtázási fájlok | **Bemeneti forgalma**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_input_payload.txt </p>**Kimeneti hasznos**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_kimeneti\_payload.txt </p></p>**Bemenetek**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_inputs.txt </p></p>**Kimenetek**: [küldő]\_[fogadó]\_EDIFACT\_[adatcsere-ellenőrző-szám]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Következő lépések

* [Az Operations Management Suite B2B üzenetek lekérdezés](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)