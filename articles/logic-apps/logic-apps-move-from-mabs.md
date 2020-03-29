---
title: Alkalmazások áttelepítése a BizTalk-szolgáltatásokból az Azure Logic Apps alkalmazásba
description: Alkalmazások és megoldások áthelyezése a Microsoft Azure BizTalk Services (MABS) szolgáltatásból az Azure Logic Apps szolgáltatásba
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905433"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Alkalmazások és megoldások áttelepítése a BizTalk-szolgáltatásokból az Azure Logic Apps alkalmazásba

A Microsoft Azure BizTalk services (MABS) kivonul. A MABS-integrációs megoldások áthelyezése az [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)kövesse a jelen cikkben található útmutatást. 

## <a name="introduction"></a>Bevezetés

A BizTalk szolgáltatások két alszolgáltatásból áll:

* Microsoft BizTalk services hibrid kapcsolatok
* EAI és EDI hídalapú integráció

[Az Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) felváltja a BizTalk services hibrid kapcsolatait. Az Azure Hybrid Connections az Azure App Service szolgáltatással érhető el az Azure Portalon keresztül. Ez a szolgáltatás hibrid kapcsolatkezelőt biztosít, így kezelheti a meglévő BizTalk Services hibrid kapcsolatokat és a portálon létrehozott új hibrid kapcsolatokat is. 

[A Logic Apps](../logic-apps/logic-apps-overview.md) az EAI és az EDI hídalapú integrációt a BizTalk szolgáltatások és egyebek összes többi képességével váltja fel. Ez a szolgáltatás felhőszintű fogyasztásalapú munkafolyamat- és vezénylési funkciókat biztosít, amelyekkel gyorsan és egyszerűen hozhat létre összetett integrációs megoldásokat böngészőn keresztül vagy a Visual Studióval.

Ez a táblázat a BizTalk-szolgáltatások képességeit logic apps-hez rendeli hozzá.

| BizTalk szolgáltatások   | Logic Apps            | Cél                      |
| ------------------ | --------------------- | ---------------------------- |
| Összekötő          | Összekötő             | Adatok küldése és fogadása   |
| Bridge             | Logikai alkalmazás             | Csővezeték-processzor           |
| Fázis ellenőrzése     | XML-érvényesítési művelet | XML-dokumentum ellenőrzése sémával szemben | 
| Gazdagítsa a színpadot       | Adattoktok           | Tulajdonságok előléptetése üzenetekké vagy útválasztási döntésekké |
| Fázis átalakítása    | Átalakítás művelet      | XML-üzenetek konvertálása egyik formátumból a másikba |
| Dekódolási szakasz       | Egyfájlos dekódolási művelet | Átalakítás egyfájlból XML formátumba |
| Kódolási szakasz       | Egylapos fájlkódolási művelet | Konvertálás XML-fájlból egylapos fájllá |
| Üzenetfelügyelő  | Azure-függvények vagy API-alkalmazások | Egyéni kód futtatása az integrációkban |
| Útvonalművelet       | Feltétel vagy kapcsoló | Üzenetek átirányítása a megadott összekötők egyikére |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk szolgáltatások összetevői

BizTalk Services többféle leletek. 

## <a name="connectors"></a>Összekötők

A BizTalk Services-összekötők segítségével áthidalják az adatok küldését és fogadását, beleértve a HTTP-alapú kérés-válasz műveleteket lehetővé tevő kétirányú hidakat is. A Logic Apps ugyanazt a terminológiát használja, és több száz összekötővel rendelkezik, amelyek ugyanazt a célt szolgálják a technológiák és szolgáltatások széles köréhez való csatlakozással. Összekötők például a felhőalapú SaaS- és PaaS-szolgáltatásokhoz, például a OneDrive, az Office365, a Dynamics CRM és egyebek höz, valamint a helyszíni rendszerekhez érhetők el a helyszíni adatátjárón keresztül, amely felváltja a BizTalk-szolgáltatások BizTalk adapterszolgáltatását. A BizTalk-szolgáltatások forrásai FTP-, SFTP- és ServiceBus-várólistára vagy Témakör-előfizetésre korlátozódnak.

![](media/logic-apps-move-from-mabs/sources.png)

Alapértelmezés szerint minden híd rendelkezik egy HTTP-végpontdal, amely a híd futásidejű cím és relatív cím tulajdonságaival van konfigurálva. Ha ugyanazt az eredményt a Logic Apps, használja a [kérelem és válasz](../connectors/connectors-native-reqres.md) műveleteket.

## <a name="xml-processing-and-bridges"></a>XML-feldolgozás és hidak

A BizTalk szolgáltatásokban a híd hasonló a feldolgozási folyamathoz. A híd vehet egy összekötőtől kapott adatokat, némi munkát az adatokkal, és elküldi az eredményeket egy másik rendszerbe. A Logic Apps ugyanezt teszi a BizTalk-szolgáltatásokkal azonos folyamatalapú interakciós minták támogatásával, valamint más integrációs minták biztosításával. A BizTalk szolgáltatások [XML-kérelem-válasz hídja](https://msdn.microsoft.com/library/azure/hh689781.aspx) VETER-folyamatnak minősül, amely az alábbi feladatokat végző szakaszokból áll:

* (V) Érvényesítés
* (E) Gazdagodás
* (T) Átalakítás
* (E) Gazdagodás
* R) Útvonal

Ez a kép bemutatja, hogyan oszlik meg a feldolgozás a kérés és a válasz között, amely külön-külön biztosítja a kérelem és a válaszelérési utak vezérlését, például az egyes útvonalakhoz tartozó különböző térképek használatával:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Emellett egy egyirányú XML-híd dekódolási és kódolási fázisokat ad hozzá a feldolgozás kezdetén és végén. Az áthaladási híd egyetlen Enrich fázist tartalmaz.

### <a name="message-processing-decoding-and-encoding"></a>Üzenetek feldolgozása, dekódolása és kódolása

A BizTalk szolgáltatásokban különböző típusú XML-üzeneteket fogadhat, és meghatározhatja a kapott üzenet megfelelő sémáját. Ez a munka a fogadási feldolgozási folyamat *Üzenettípusok* szakaszában történik. A Decode szakasz ezután az észlelt üzenettípus t használja az üzenet dekódolásához a megadott séma használatával. Ha a séma egy sima fájlséma, ez a szakasz a bejövő egyenlapos fájlt XML-fájllá alakítja. 

Logic Apps hasonló képességeket biztosít. A különböző összekötő-eseményindítók (fájlrendszer, FTP, HTTP és így tovább) használatával különböző protokollokon keresztül egy sima fájl jelenik meg, és a [Síkfájl-dekódolás](../logic-apps/logic-apps-enterprise-integration-flatfile.md) művelettel konvertálja a bejövő adatokat XML-re. A meglévő egyenlemezes sémákat módosítás nélkül közvetlenül áthelyezheti a Logic Apps alkalmazásba, majd sémákat tölthet fel az integrációs fiókba.

### <a name="validation"></a>Ellenőrzés

Miután a bejövő adatokat XML-vé konvertálta (vagy ha az XML az üzenetformátum ot kapta), az érvényesítés fut annak megállapítására, hogy az üzenet megfelel-e az XSD-sémának. A feladat logic apps végrehajtásához használja az [XML-érvényesítési](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) műveletet. A BizTalk services ből származó sémákat módosítások nélkül használhatja.

### <a name="transform-messages"></a>Üzenetek átalakítása

A BizTalk szolgáltatásokban az Átalakítás szakasz egy XML-alapú üzenetformátumot konvertál át egy másikba. Ez a munka a TRFM-alapú leképező használatával történik. A Logic Apps, a folyamat hasonló. Az Átalakítás művelet egy térképet hajt végre az integrációs fiókból. A fő különbség az, hogy a Logic Apps térképek XSLT formátumban vannak. Az XSLT lehetővé teszi a már meglévő XSLT újrafelhasználását, beleértve a BizTalk Server számára létrehozott térképeket is, amelyek functoidokat tartalmaznak. 

### <a name="routing-rules"></a>Útválasztási szabályok

A BizTalk services útválasztási döntést hoz arról, hogy melyik végpontot vagy összekötőt küldi be a bejövő üzeneteket vagy adatokat. Az előre konfigurált végpontok közül való választás az útválasztási szűrő beállítással lehetséges:

![](media/logic-apps-move-from-mabs/route-filter.png)

A BizTalk szolgáltatások ban, ha csak két lehetőség van, a *Feltétel* használata a legjobb módja az útválasztási szűrők konvertálásának a BizTalk szolgáltatásokban. Ha kettőnél több van, akkor használjon **kapcsolót**.

A Logic Apps kifinomult logikai képességeket, valamint speciális vezérlési folyamatot és útválasztást biztosít [feltételes utasításokkal](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [kapcsolóutasításokkal.](../logic-apps/logic-apps-control-flow-switch-statement.md)

### <a name="enrich"></a>Gazdagítani

A BizTalk services feldolgozása kor a Gazdagítás szakasz hozzáadja a tulajdonságokat a fogadott adatokhoz társított üzenetkörnyezethez. Például egy tulajdonság előléptetése egy adatbázis-kereshető útválasztáshoz, vagy egy érték XPath-kifejezés használatával történő kibontása. A Logic Apps hozzáférést biztosít az előző műveletek összes környezetfüggő adatkimenetéhez, így egyszerű az azonos viselkedés replikálása. Az `Get Row` SQL-kapcsolat művelet használatával például adatokat ad vissza egy SQL Server-adatbázisból, és a Döntés műveletművelethez a Döntés műveletben szereplő adatokat használja. Hasonlóképpen, a bejövő Service Bus várólistára helyezett üzenetek egy eseményindító által megadott tulajdonságok címezhető, valamint az XPath az xpath munkafolyamat-definíciós nyelvi kifejezés használatával.

### <a name="run-custom-code"></a>Egyéni kód futtatása

A BizTalk Services lehetővé teszi a saját szerelvényekben feltöltött [egyéni kód futtatását.](https://msdn.microsoft.com/library/azure/dn232389.aspx) Ezt a funkciót az [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) felület valósítja meg. A híd minden szakasza két tulajdonságot tartalmaz (Az Enter Inspector és on Exit Inspector), amelyek biztosítják a kapcsolatot megvalósító .NET típust. Az egyéni kód lehetővé teszi az adatok összetettebb feldolgozását, és lehetővé teszi a meglévő kód újrafelhasználását olyan szerelvényekben, amelyek közös üzleti logikát végeznek. 

A Logic Apps két elsődleges módot kínál az egyéni kód végrehajtására: az Azure Functions és az API-alkalmazások. Az Azure Functions létrehozható, és logikai alkalmazásokból hívható. Lásd: [Egyéni kód hozzáadása és futtatása az Azure Functions en keresztül.](../logic-apps/logic-apps-azure-functions.md) Az Azure App Service részét, az API-alkalmazások segítségével saját eseményindítókat és műveleteket hozhat létre. További információ a [Logic Apps alkalmazásokkal használható egyéni API létrehozásáról.](../logic-apps/logic-apps-create-api-app.md) 

Ha egyéni kóddal rendelkezik a BizTalk Services-ből hívható szerelvényekben, áthelyezheti ezt a kódot az Azure Functionsbe, vagy létrehozhat egyéni API-kat AZ API-alkalmazások segítségével, attól függően, hogy mit valósít meg. Például ha olyan kóddal rendelkezik, amely egy másik szolgáltatást csomagol, amelyhez a Logic Apps nem rendelkezik összekötővel, majd hozzon létre egy API-alkalmazást, és használja az API-alkalmazás által a logikai alkalmazáson belül biztosított műveleteket. Ha segítő funkciókkal vagy kódtárakkal rendelkezik, akkor valószínűleg az Azure Functions a legmegfelelőbb.

### <a name="edi-processing-and-trading-partner-management"></a>EDI feldolgozó és kereskedelmi partnerek kezelése

A BizTalk szolgáltatások és logikai alkalmazások tartalmazzák az EDI és B2B feldolgozást az AS2 (2. alkalmazhatósági nyilatkozat), az X12 és az EDIFACT támogatásával. A BizTalk Szolgáltatások ban edi hidakat hozhat létre, és kereskedelmi partnereket és megállapodásokat hozhat létre vagy kezel het a dedikált Nyomonkövetési és felügyeleti portálon.
A Logic Apps alkalmazásban ezt a funkciót a [vállalati integrációs csomag (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md)keresztül kapja meg. Az EIP [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és B2B műveleteket biztosít az EDI és A B2B feldolgozáshoz. Integrációs fiókot is használhat [a kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) és [megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md)létrehozásához és kezeléséhez. Integrációs fiók létrehozása után egy vagy több logikai alkalmazást csatolhat a fiókhoz. Ezután a B2B műveletek segítségével hozzáférhet a kereskedelmi partnerek adataihoz a logikai alkalmazásból. A következő műveletek állnak rendelkezésre:

* AS2 kódolás
* AS2 dekódolás
* X12 kódolás
* X12 Dekódolni
* EDIFACT kódolás
* EDIFACT dekódolni

A BizTalk szolgáltatásokkal ellentétben ezek a műveletek levannak választva az átviteli protokolloktól. Így a logikai alkalmazások létrehozásakor nagyobb rugalmasságot biztosít, amelyben az adatok küldésére és fogadására használt összekötők. Például x12 fájlokat fogadhat mellékletként az e-mailből, majd feldolgozhatja ezeket a fájlokat egy logikai alkalmazásban. 

## <a name="manage-and-monitor"></a>Kezelés és monitorozás

A BizTalk Services szolgáltatásban egy dedikált portál rendelkezik nyomon követési képességekkel a problémák figyelésére és elhárítására. A Logic Apps gazdagabb nyomon követési és figyelési lehetőségeket biztosít [a logikai alkalmazások figyeléséhez az Azure Portalon,](../logic-apps/monitor-logic-apps.md)és tartalmaz egy mobilalkalmazást, amely figyeli a dolgokat útközben.

## <a name="high-availability"></a>Magas rendelkezésre állás

A BizTalk-szolgáltatások magas rendelkezésre állása (HA) érdekében megoszthatja a feldolgozási terhelést egy adott régióban egynél több példány használatával. A Logic Apps további költségek nélkül biztosít a régióban lévő HA-t. 

A BizTalk szolgáltatásokban a B2B-feldolgozás régión kívüli vészhelyreállításhoz biztonsági mentési és visszaállítási folyamatra van szükség. Az üzletmenet folytonossága érdekében a Logic Apps régióközi aktív/passzív [VÉSZ-képességet](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)biztosít, amely lehetővé teszi a B2B-adatok szinkronizálását a különböző régiók integrációs fiókjai között.

## <a name="next-steps"></a>További lépések

* [Mi az a Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md), vagy a használat gyors megkezdése [előre elkészített sablonokkal](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [A logikai](../connectors/apis-list.md) alkalmazásokban használható összes rendelkezésre álló összekötő megtekintése