---
title: Alkalmazások áthelyezése az Azure Logic Apps BizTalk szolgáltatások |} Microsoft Docs
description: Helyezze át vagy Azure BizTalk szolgáltatások (MABS) Azure Logic Apps alkalmazások áttelepítése
services: logic-apps
documentationcenter: ''
author: jonfancey
manager: anneta
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: jonfan; LADocs
ms.openlocfilehash: f5ad003189d70216198f2d5c7719a971c16d5479
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>Az Azure Logic Apps BizTalk szolgáltatások áthelyezése

A Microsoft Azure BizTalk szolgáltatások (MABS) kivonás alatt áll. MABS integrációs megoldásokat áthelyezése [Azure Logic Apps](../logic-apps/logic-apps-overview.md), kövesse az ebben a cikkben szereplő útmutatást. 

## <a name="introduction"></a>Bevezetés

BizTalk szolgáltatások két subservices áll:

* Microsoft BizTalk szolgáltatások hibrid kapcsolatok
* EAI- és EDI híd-alapú integrációs

[Az Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) BizTalk szolgáltatások hibrid kapcsolatok váltja fel. Az Azure hibrid kapcsolatok érhető el az Azure App Service az Azure portálon keresztül. Ez a szolgáltatás a Hybrid Connection Manager biztosítja, így a meglévő BizTalk szolgáltatások hibrid kapcsolatok, valamint az új hibrid kapcsolatok létrehozása a portálon kezelheti. 

[A Logic Apps](../logic-apps/logic-apps-overview.md) EAI- és EDI híd-alapú integrációs lecseréli az összes ugyanazokat a képességeket a BizTalk szolgáltatások és egyéb. Ez a szolgáltatás a felhőméretű fogyasztás alapján munkafolyamat és a vezénylési szolgáltatásokat biztosítja, hogy gyorsan és könnyen készítsen böngészőn keresztül vagy a Visual Studio összetett integrációs megoldásokat.

Ez a táblázat leképezve Logic Apps BizTalk szolgáltatások képességeit.

| BizTalk Services   | Logic Apps            | Cél                      |
| ------------------ | --------------------- | ---------------------------- |
| Összekötő          | Összekötő             | Adatokat küldeni és fogadni   |
| Híd             | Logikai alkalmazás             | Feldolgozási sor processzor           |
| Szakasz ellenőrzése     | XML-érvényesítés művelet | Az XML-dokumentum, a séma érvényesítése | 
| Szakasz kiegészítése       | Adatok jogkivonatok           | Tulajdonságok előléptetni üzenetbe vagy a útválasztási döntések |
| Átalakítás szakasz    | Átalakítási művelet      | Átalakítása XML-üzenetek egy adott formátumból más |
| Dekódolás szakasz       | Egybesimított fájl dekódolási művelet | Egybesimított fájlból átalakítása XML |
| Szakasz kódolása       | Egybesimított fájl kódolása művelet | Az XML-átalakítása egybesimított fájl |
| Üzenet Inspector  | Az Azure Functions vagy API-alkalmazások | Futtassa a egyéni kódot a integrációja |
| Útvonal-művelet       | Az állapot vagy a kapcsoló | A megadott összekötők egyikéhez üzenetek |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk szolgáltatások összetevők

BizTalk szolgáltatások van több különböző összetevők. 

## <a name="connectors"></a>Összekötők

BizTalk szolgáltatások összekötők hidak adatokat, többek között, amelyek lehetővé teszik a HTTP-alapú kérelem/válasz kapcsolati kétirányú hidak küldeni és fogadni segítik. A Logic Apps ugyanazokat a kifejezéseket, és 180 + összekötők csatlakozva azokat a technológiákat és a szolgáltatások ugyanazt a célt szolgálják. Például összekötők érhetők el a cloud SaaS és PaaS szolgáltatások, például a onedrive-on, Office365, Dynamics CRM és egyéb, és a helyszíni az On-Premises adatok átjárón, amely a felváltja a BizTalk szolgáltatás BizTalk szolgáltatások-rendszerekhez. BizTalk szolgáltatások források FTP, SFTP, és a Service Bus-üzenetsorba vagy témakör előfizetés korlátozódnak.

![](media/logic-apps-move-from-mabs/sources.png)

Alapértelmezés szerint minden híd HTTP-végponttal, amely van konfigurálva, a futtatókörnyezet címmel és relatív cím tulajdonságait, amely a híd rendelkezik. A Logic Apps ugyanaz az eredmény elérése érdekében használja a [kérelem-válasz](../connectors/connectors-native-reqres.md) műveletek.

## <a name="xml-processing-and-bridges"></a>XML-feldolgozás és a hidak

BizTalk szolgáltatások a híd olyan hasonló egy feldolgozási folyamat. Hidat is igénybe vehet egy összekötő érkező adatokat, az egyes az adatokat, és az eredményt elküldik egy másik rendszer. A Logic Apps BizTalk szolgáltatásként azonos csővezeték-alapú kommunikáció mintázatokat támogatásával, és más integrációs minták biztosítanak szerepe ugyanaz. A [XML-kérelem-válasz híd](https://msdn.microsoft.com/library/azure/hh689781.aspx) BizTalk szolgáltatásokban is ismert, egy VETER folyamat, amely ezen feladatok végrehajtásával szakaszból áll:

* (V) érvényesítése
* (E) Enrich
* (T) átalakítás
* (E) Enrich
* (R) útvonal

Ez a kép bemutatja, hogyan feldolgozási helykérelemmel és válasszal, amelyek segítségével szabályozhatja, a kérelem elosztva, és a válasz elérési utak külön-külön, például a különböző leképezéseinek mindegyik elérési út:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Egyirányú híd XML is, a kezdő és a feldolgozási végén hozzáadja az dekódolási és Encode szakaszában. A csatlakoztatott híd tartalmaz egyetlen Enrich szintre.

### <a name="message-processing-decoding-and-encoding"></a>Üzenet feldolgozása dekódolás és kódolás

BizTalk szolgáltatások, a különböző típusú XML-üzenetek fogadására, és határozza meg a beérkezett üzenet megfelelő sémáját. Ez a munkahelyi történik a *üzenettípusok* a fogadási folyamat szakasza. A dekódolási szakasz észlelt üzenettípus használja fel az üzenetet, a megadott séma használata dekódolására. Ha a séma egybesimított fájl séma, ebben a szakaszban a bejövő egybesimított fájl XML-alakítja át. 

A Logic Apps hasonló funkciókat biztosít. Egyszerű fogadni a különböző összekötő eseményindítókat (File System, FTP, HTTP és így tovább) segítségével különböző protokollokon keresztül, és használja a [Egybesimított fájl dekódolása](../logic-apps/logic-apps-enterprise-integration-flatfile.md) műveletet úgy, hogy a bejövő adatok átalakítása XML. Helyezze át a létező fájlszintű sémák közvetlenül a Logic Apps módosítások nélkül, és majd töltse fel a sémák integrációs fiókjába.

### <a name="validation"></a>Ellenőrzés

A bejövő adatok átalakítása után XML (vagy ha XML-kódja a üzenetformátum érkezett), érvényesítési futtatása, és határozza meg, ha az üzenet megfelelő az XSD-sémával. Ez a feladat végrehajtásához a Logic Apps, használja a [XML-érvényesítés](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) művelet. Ugyanannak a sémának a BizTalk szolgáltatások módosítások nélkül használható.

### <a name="transform-messages"></a>Átalakítás üzenetek

A BizTalk szolgáltatások az átalakítási szakasz alakít egy XML-alapú üzenetformátum a másikra. A munkát a TRFM-alapú hozzárendelő használatával térképre alkalmazásával. A Logic Apps a folyamat hasonlít. Az átalakítási műveletet hajtja végre a térkép integrációs fiókjából. A fő különbség, hogy a Logic Apps maps XSLT-formátumban vannak-e. XSLT lehetővé teszi a meglévő XSLT már, beleértve a maps létre BizTalk Server functoids tartalmazó újból. 

### <a name="routing-rules"></a>Útválasztási szabályokat

BizTalk szolgáltatások lehetővé teszi, hogy mely endpoint vagy az összekötő döntés bejövő üzenetek vagy az adatok küldése. Válassza ki az előre beállított végpontjaikra nem lehetséges az útválasztási szűrő beállításával:

![](media/logic-apps-move-from-mabs/route-filter.png)

BizTalk szolgáltatások, ha csak két lehetőség közül választhat, használja a *feltétel* a legjobb módszer a BizTalk szolgáltatások útválasztási szűrők alakításának. Ha több mint két, majd használja a **kapcsoló**.

A Logic Apps biztosít, segítségükkel kifinomult logika képességek, valamint speciális folyamatábrán és az Útválasztás [feltételes utasítások](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [utasítások kapcsoló](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Kiegészítése

A BizTalk szolgáltatások feldolgozása, az a Enrich szakasz tulajdonságok bővíti a fogadott adatok társított üzenet környezetet. Például előléptetni a az adatbázis-lekérdezés vagy XPath kifejezés segítségével érték beolvasásával továbbításához használni kívánt tulajdonságot. A Logic Apps hozzáférést biztosít a mindegyikhez környezetfüggő adatok a fenti műveletek, így egyszerű replikálásához a kívánt viselkedést eredményező beállítást. Használata esetén például a `Get Row` SQL-kapcsolat művelet, térjen vissza az adatokat egy SQL Server-adatbázisból, és az adatok felhasználásával döntési művelettel irányításához. Hasonlóképpen, a bejövő Service Buson tulajdonságok várólistára helyezett üzenetek egy eseményindító által megcímezhető, valamint a xpath munkafolyamat definition language eredményező kifejezés XPath.

### <a name="run-custom-code"></a>Egyéni kód futtatása

BizTalk szolgáltatások lehetővé teszi, hogy [egyéni kódra](https://msdn.microsoft.com/library/azure/dn232389.aspx) , amelyek a saját szerelvényekben feltöltődött. Ez a funkció megvalósítja a [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) felületet. Minden szakaszhoz a Bridge magában foglalja a két tulajdonságok (adja meg Inspector a és a kilépési Inspector) adja meg a létrehozott .NET típusát, amely megvalósítja ezt a felületet. Egyéni kód lehetővé teszi, hogy végezze el bonyolultabb feldolgozási adatok, és lehetővé teszi az általános üzleti logika végző szerelvényeket a meglévő kódot ismét felhasználni. 

A Logic Apps egyéni kódot két elsődleges lehetőséget biztosít: az Azure Functions és API-alkalmazások. Az Azure Functions hozható létre, és a logic Apps alkalmazásokból nevezik. Lásd: [hozzáadása és az Azure Functions használatával logic Apps-alkalmazások futtatása egyéni kód](../logic-apps/logic-apps-azure-functions.md). API Apps, az Azure App Service-ben részét segítségével létrehozhat saját eseményindítók és műveletek. További információ [létrehozni egy egyéni API használata a Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Ha egyéni kód a szerelvényt, amely meghívja a BizTalk szolgáltatásokból, helyezze át a kódot az Azure Functions, vagy hozzon létre egyéni API-k az API Apps, attól függően, hogy mi meg megvalósításához. Például ha egy másik szolgáltatás, amelynek a Logic Apps nincs összekötő nagyságúra kódot, majd API-alkalmazás létrehozása, és a műveletek az API-alkalmazás biztosít a logikai alkalmazásban használni. Ha súgófunkciókat vagy álljon, majd az Azure Functions oka a legjobb térkihasználás érdekében.

### <a name="edi-processing-and-trading-partner-management"></a>EDI dolgoz fel, és kereskedelmipartner-kezelés

BizTalk szolgáltatások és a Logic Apps tartalmazza az AS2-támogatással rendelkező EDI és B2B feldolgozási (alkalmazhatósági utasítás 2), X12 és EDIFACT. BizTalk szolgáltatások a EDI hidak létrehozása, és hozzon létre vagy kereskedelmi partnerek és a dedikált nyomon követését és a felügyeleti portálon megállapodások kezelése.
A Logic Apps elérhetővé ezt a funkciót a [vállalati Integration Pack (EIP-t a)](../logic-apps/logic-apps-enterprise-integration-overview.md). Az EIP-t biztosít [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és EDI és B2B feldolgozásra B2B műveletek. Integráció fiókot létrehozására és kezelésére is használjon [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) és [megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md). Integráció fiók létrehozása után egy vagy több logikai alkalmazások társíthatja a fiókot. Használhatja a B2B műveletek kereskedelmi partneradatok elérje a Logic Apps alkalmazást. Itt találhatók a következő műveleteket:

* AS2-kódolása
* AS2 dekódolása
* X12 kódolása
* X12 dekódolása
* EDIFACT kódolása
* EDIFACT dekódolása

BizTalk szolgáltatások ellentétben ezek a műveletek az átviteli protokollokat vannak leválasztja. Így a logic Apps alkalmazások létrehozásakor a mely összekötőket kell nagyobb rugalmasságot használt adatokat küldeni és fogadni. Például fogadhat mellékletként az e-mailek, és ezután folyamat fájlok X12 ezeket a fájlokat egy logikai alkalmazást. 

## <a name="manage-and-monitor"></a>Kezelése és figyelése

A BizTalk szolgáltatások egy dedikált portálon megadott nyomkövetési lehetőségeket biztosítanak a figyelheti és elhárítását. A Logic Apps gazdagabb nyomon követése és figyelési képességek keresztül biztosítja a [Azure-portálon](../logic-apps/logic-apps-monitor-your-logic-apps.md), és tartalmazza a mobilalkalmazások való tartásának követheti a művelet az áthelyezés közben.

## <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állás érdekében (HA) BizTalk szolgáltatások a feldolgozási terhelés megoszthatja, ha egy adott régióban egynél több példány használatával. A Logic Apps biztosít a régió magas rendelkezésre ÁLLÁSÚ, minden további költség nélkül. 

BizTalk szolgáltatások ki a régiót katasztrófa utáni helyreállítás B2B feldolgozás céljából egy biztonsági mentési és visszaállítási folyamat használatához szükséges. Az üzletmenet folytonossága érdekében a Logic Apps biztosít a kereszt-régió aktív/passzív [vész-Helyreállítási képességet](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), amely lehetővé teszi a B2B adatok szinkronizálása különböző régiókban integrációs fiókok között.

## <a name="next-steps"></a>További lépések

* [Mi az a Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md), vagy a használat gyors megkezdése [előre elkészített sablonokkal](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Megtekintheti a rendelkezésre álló összekötők](../connectors/apis-list.md) használható a logic Apps alkalmazások