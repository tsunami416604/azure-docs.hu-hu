---
title: Alkalmazások áthelyezése az Azure Logic Apps a BizTalk Services |} A Microsoft Docs
description: Az Azure BizTalk Services (MABS) migrálhat az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f27e82e780917e00625ef6a14ab8317d1f5b8ae8
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124799"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Az Azure Logic Apps-alkalmazások áttelepítése a BizTalk Services

A Microsoft Azure BizTalk Services (MABS) kivonás alatt áll. Helyezze át a MABS integrációs megoldásait a [Azure Logic Apps](../logic-apps/logic-apps-overview.md), kövesse az ebben a cikkben leírtakat. 

## <a name="introduction"></a>Bevezetés

A BizTalk Services két subservices áll:

* A Microsoft BizTalk Services hibrid kapcsolatok
* EAI és EDI-t hídalapú integrációját

[Az Azure App Service hibrid kapcsolataira](../app-service/app-service-hybrid-connections.md) váltja fel a BizTalk Services hibrid kapcsolatok. Az Azure hibrid kapcsolatok az Azure App Service az Azure Portalon érhető el. Ez a szolgáltatás biztosítja a Hibridkapcsolat-kezelő, hogy a meglévő BizTalk Services hibrid kapcsolatok, valamint az új hibrid kapcsolatok létrehozása a portálon kezelheti. 

[A Logic Apps](../logic-apps/logic-apps-overview.md) EAI és EDI-t hídalapú integrációját lecseréli az összes ugyanazokat a képességeket a BizTalk Services stb. Ez a szolgáltatás felhőméretű fogyasztásalapú munkafolyamatok és a vezénylési olyan szolgáltatásokat nyújt, hogy gyorsan és egyszerűen hozhat létre összetett integrációs megoldások böngészőn keresztül vagy a Visual Studio használatával.

Ez a táblázat a Logic Apps a BizTalk Services lehetőségei képez le.

| BizTalk Services   | Logic Apps            | Cél                      |
| ------------------ | --------------------- | ---------------------------- |
| Összekötő          | Összekötő             | Adatokat küldeni és fogadni   |
| Híd             | Logikai alkalmazás             | Folyamat processzor           |
| Fázis ellenőrzése     | XML-érvényesítés művelet | Az XML-dokumentum, a séma érvényesítése | 
| Bővítés a szakaszában       | Adatjogkivonatok           | Tulajdonságok előléptetése üzenetek vagy a útválasztási döntéseket hozhat |
| Átalakítás fázis    | A művelet átalakítása      | Átalakítása XML-üzenetek egy adott formátumból egy másikba |
| Fázis-dekódolást.       | Egybesimított fájl dekódolása művelet | Az egybesimított fájl átalakítása XML |
| Fázis kódolása       | Egybesimított fájl kódolása művelet | XML átalakítása egybesimított fájl |
| Üzenet vizsgáló  | Az Azure Functions és az API Apps | Egyéni kód futtatását a Integrációk |
| Útvonal-művelet       | Az állapot vagy a kapcsoló | Az üzenetek útválasztását a megadott összekötők egyikéhez |
|||| 

## <a name="biztalk-services-artifacts"></a>A BizTalk Services-összetevők

A BizTalk Services különféle összetevőket tartalmaz. 

## <a name="connectors"></a>Összekötők

A BizTalk Services-összekötők segítségével hidak küldhet és fogadhat adatokat, többek között, amelyek lehetővé teszik a HTTP-alapú kérés/válasz interakciók kétirányú hidak. A Logic Apps használja ugyanazokat a kifejezéseket, és 180 több olyan összekötővel rendelkezik, amely ugyanazt a célt szolgálják, számos különféle technológiákat és szolgáltatásokat való csatlakozással. Ha például összekötők érhetők el a felhőalapú SaaS és PaaS szolgáltatások, például a onedrive vállalati verzió, Office 365, Dynamics CRM és egyéb, valamint a helyszíni rendszerekben az On-Premises Data Gateway, amely lecseréli a BizTalk Adapter Service a BizTalk Services révén. A BizTalk Services források korlátozva, FTP-, SFTP, és a Service Bus-üzenetsor vagy témakör-előfizetés.

![](media/logic-apps-move-from-mabs/sources.png)

Alapértelmezés szerint minden egyes híd rendelkezik egy HTTP-végpontot, a futásidejű címhez és a relatív cím tulajdonságait, amely a híd konfigurált. A Logic Apps ugyanazokat az eredményeket érhet el, használja a [kérések és válaszok](../connectors/connectors-native-reqres.md) műveleteket.

## <a name="xml-processing-and-bridges"></a>XML-feldolgozás és a hidak

A BizTalk Services-hidat csatlakoztatja, akkor egy feldolgozási folyamat. A híd is igénybe vehet egy összekötő érkező adatokat, néhány használni az adatokat, és elküldi az eredményeket egy másik system. A Logic Apps pedig ugyanezt a azonos folyamat-alapú interakció minták, a BizTalk Services támogatásával, és más integrációs minták is segíti. A [XML kérés-válasz híd](https://msdn.microsoft.com/library/azure/hh689781.aspx) a BizTalk Services néven VETER folyamat, amely, amelyek ezeket a feladatokat tartalmazza:

* (V) ellenőrzése
* (E) bővítés
* (T) átalakítás
* (E) bővítés
* (R) útvonal

Ez a kép bemutatja, hogyan feldolgozási helykérelemmel és válasszal, amelyek segítségével szabályozhatja, a kérelem között van felosztva, és a válasz elérési utak külön-külön, például a különböző térképek tartozó mindegyik elérési út:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Egyirányú híd XML is, a kezdő és a feldolgozás végén hozzáadja az dekódolása és kódolása szakaszában. Az átmenő híd tartalmaz egyetlen Enrich szintre.

### <a name="message-processing-decoding-and-encoding"></a>Üzenetfeldolgozás, dekódolása és kódolása

A BizTalk Services különböző típusú XML-üzenetek fogadásához, és határozza meg a megfelelő sémát az üzenet érkezett. A munka történik a *üzenettípusok* fázis a receive-feldolgozási folyamat. A dekódolási szakaszban észlelt üzenettípus használja fel a megadott séma használatával-üzenet dekódolása. Ha a séma egy egybesimított fájlos sémában, ebben a szakaszban átalakítja a bejövő egybesimított fájlt XML. 

A Logic Apps hasonló képességeket biztosít. A másik összekötő eseményindítók (File System, FTP, a HTTP és így tovább) használatával különböző protokollokon keresztül egy egybesimított fájlt kap, és használja a [Egybesimított fájl dekódolása](../logic-apps/logic-apps-enterprise-integration-flatfile.md) művelet a bejövő adatok átalakítása XML. Helyezze át a meglévő egybesimított fájl sémák közvetlenül a logikai alkalmazások módosítása nélkül, és ezután töltse fel a sémákat az integrációs fiókba.

### <a name="validation"></a>Ellenőrzés

A bejövő adatok átalakítása után XML (vagy ha XML-kódja az érkezett üzenet formátuma), ellenőrzés futtatása, és határozza meg, ha az üzenet betartja az XSD-séma. Ez a feladat végrehajtásához a Logic Apps, használja a [XML-érvényesítés](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) művelet. A BizTalk Services ugyanazon sémával módosítása nélkül is használhatja.

### <a name="transform-messages"></a>Üzenetek átalakítása

A BizTalk Services az átalakítási szakasz egy másikra egy XML-alapú üzenetformátum konvertálja. Ezt a munkát egy térkép TRFM-alapú leképezőjét használatával alkalmazásával történik meg. A Logic Apps a folyamat hasonlít. Az átalakítási műveletet egy térkép végrehajtja az integrációs fiókból. A fő különbség az, hogy a Logic Apps maps XSLT-formátumban vannak-e. XSLT alkalmazásunk már rendelkezik, beleértve a maps a BizTalk Server létrehozott functoids tartalmazó meglévő XSLT újból. 

### <a name="routing-rules"></a>Útválasztási szabályok

A BizTalk Services lehetővé teszi, hogy melyik végponthoz vagy összekötő döntés bejövő üzenetek vagy adatok küldése. Útválasztási szűrje használatával lehetségessé válik a ki az előre konfigurált végpontokból:

![](media/logic-apps-move-from-mabs/route-filter.png)

A BizTalk Servicesben, ha csak két lehetőség van, használja a *feltétel* alakítása. a BizTalk Services útválasztási szűrőkkel a legjobb megoldás. Ha meghaladja a kettőt, majd használja a **váltson**.

A Logic Apps biztosítja a segítségükkel kifinomult logika képességek és speciális átvitelvezérlés és az Útválasztás [feltételes utasításokat](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [switch-utasítások](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Bővítés

A BizTalk Services feldolgozása, a Enrich szakaszban további tulajdonságokkal bővít a fogadott adatok társított üzenet környezete. Például elősegítő használatához egy adatbázis-lekérdezés, illetve oly módon, egy értéket egy XPath kifejezés használatával útválasztáshoz tulajdonság. A Logic Apps hozzáférést biztosít az összes környezetfüggő adatok kimenetének az előző művelet, így könnyen érthető megjegyzésblokkok írására viselkedést replikálni. Például a `Get Row` SQL kapcsolati művelet, adatokat adja vissza egy SQL Server-adatbázisból, és használhatja az adatokat egy döntési működés útválasztás. Ehhez hasonlóan bejövő a Service Bus tulajdonságainak várólistára helyezett üzenetek-triggerek által a megcímezhető, valamint XPath az xpath-munkafolyamat definition Sablonnyelv-kifejezés használatával.

### <a name="run-custom-code"></a>Egyéni kód futtatása

A BizTalk Services révén [egyéni kóddal](https://msdn.microsoft.com/library/azure/dn232389.aspx) , amelyek a saját szerelvényekben töltenek fel. Ez a funkció valósít meg a [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) felületet. Minden egyes fázisában a híd tartalmaz két tulajdonságait (adja meg vizsgáló a és a kilépési vizsgáló), amely a .NET-típus hozott létre, amely ezt a felületet valósítja meg. Egyéni kód lehetővé teszi a összetettebb feldolgozási adatokon végrehajtandó, és újra felhasználhatja a meglévő kód a közös üzleti logikát végrehajtó szerelvényeket. 

A Logic Apps egyéni kódot két elsődleges lehetőséget biztosít: az Azure Functions és az API Apps. Az Azure Functions létre, és a logic apps-fájlból. Lásd: [hozzáadása és a logic apps segítségével az Azure Functions futtatása egyéni kód](../logic-apps/logic-apps-azure-functions.md). A saját eseményindítók és műveletek létrehozásához használja az API-alkalmazások Azure App Service része. Tudjon meg többet [használata a Logic Apps egyéni API létrehozásával](../logic-apps/logic-apps-create-api-app.md). 

Ha egyéni kódot szerelvényekben a BizTalk Services hívják meg, akkor ez a kód áthelyezése az Azure Functions, vagy is létrehozhat egyéni API-k az API Apps függően mi implementálja. Például ha kódot, amely egy másik szolgáltatás, amelynek a Logic Apps-összekötő nem rendelkezik, majd API-alkalmazás létrehozása, és a műveletek az API-alkalmazás biztosítja a logikai alkalmazásban használja. Ha már segédfüggvények és a könyvtárakat, majd az Azure Functions, valószínűleg a legmegfelelőbb.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-feldolgozás és trading partner management

A BizTalk Services és a Logic Apps közé tartozik az AS2-támogatással EDI és B2B feldolgozás (alkalmazhatósági nyilatkozatot 2), X12 és EDIFACT. A BizTalk Services szolgáltatásban a EDI hidak létrehozása, és hozzon létre vagy kereskedelmi partnerek és egyezmények a dedikált nyomon követését és a felügyeleti portálon kezelheti.
A Logic Apps esetében kap ezt a funkciót a [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Az EIP biztosít [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és B2B-műveletek EDI, B2B és feldolgozás céljából. Integrációs fiók létrehozásához és kezeléséhez is használhatja [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) és [szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md). Miután létrehozott egy integrációs fiókhoz, a fiók egy vagy több logikai alkalmazások kapcsolat. B2B-műveletek segítségével ezután hozzáférhet a kereskedelmi partnerek a logikai alkalmazást. A következő műveletek a következők:

* AS2-kódolást
* AS2-dekódolást.
* X12 kódolása
* X12-dekódolást.
* EDIFACT-kódolást
* EDIFACT-dekódolást.

BizTalk Services-ellentétben ezek a műveletek vannak választva a az átviteli protokollokat. Így amikor hoz létre a logic apps, sokkal rugalmasabban mely összekötők az, hogy küldeni és fogadni az adatokat használja. Például kaphat mellékletként e-mailt, és ezután folyamat a fájlok X12 ezeket a fájlokat, a logikai alkalmazás. 

## <a name="manage-and-monitor"></a>Kezelése és figyelése

A BizTalk Services szolgáltatásban egy dedikált portál megadott nyomkövetési lehetőségeket biztosítanak, monitorozásának és hibaelhárításának problémákat. A Logic Apps gazdagabb nyomon követése és monitorozási lehetőségeket keresztül biztosítja a [az Azure portal](../logic-apps/logic-apps-monitor-your-logic-apps.md), és tartalmaz egy mobilalkalmazást szemmel tartja a dolgokat, az áthelyezés közben.

## <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre ÁLLÁS a BizTalk Servicesben megoszthatja a feldolgozási terhelés egy adott régióban több példány használatával. A Logic Apps biztosítja a régióban a magas rendelkezésre ÁLLÁS további költségek nélkül. 

A BizTalk Services-B2B feldolgozás ki a régiót a vész-helyreállítása szükséges egy biztonsági mentési és helyreállítási folyamatokat. Üzleti folytonosság a Logic Apps biztosítja a régiók közötti aktív/passzív [DR képesség](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), amely lehetővé teszi a B2B-adatok szinkronizálása a különböző régiókban lévő integrációs fiókok között.

## <a name="next-steps"></a>További lépések

* [Mi a Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md), vagy a használat gyors megkezdése [előre elkészített sablonokkal](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Megtekintheti az összes elérhető összekötő](../connectors/apis-list.md) a logic Apps alkalmazásokban használható