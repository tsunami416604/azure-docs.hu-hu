---
title: "Alkalmazások áthelyezése az Azure Logic Apps BizTalk szolgáltatások |} Microsoft Docs"
description: "Helyezze át vagy Azure BizTalk szolgáltatások MABS Logic Apps alkalmazások áttelepítése"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: df26e4669158e5aa9e3b9a7af888d0dbbba273dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>A Logic Apps BizTalk szolgáltatások áthelyezése

A Microsoft Azure BizTalk szolgáltatások (MABS) kivonás alatt áll. Ez a témakör segítségével helyezze át a MABS integrációs megoldásokat Azure Logic Apps. 

## <a name="overview"></a>Áttekintés

BizTalk szolgáltatások két alárendelt szolgáltatásokból állnak:

1.  Microsoft BizTalk szolgáltatások hibrid kapcsolatok
2.  EAI- és EDI híd-alapú integrációs

Ha van szüksége a hibrid kapcsolatok, majd helyezze [Azure App Service hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) módosítások és a szolgáltatás funkcióinak működését mutatja be. Az Azure hibrid kapcsolatok BizTalk szolgáltatások hibrid kapcsolatok váltja fel. Azure hibrid kapcsolatok érhető el az Azure App Service, és az Azure portálon tartományregisztráció. Az Azure hibrid kapcsolatok emellett egy új Hybrid Connection Manager meglévő BizTalk szolgáltatások hibrid kapcsolatok és a portálon létrehozott új hibrid kapcsolatok kezeléséhez. Az Azure App Service hibrid kapcsolatok általánosan elérhető (GA).

EAI- és EDI híd-alapú integráció a Logic Apps az váltja fel. A Logic Apps a BizTalk szolgáltatásként képességeit, és több ugyanazokat biztosít. A Logic Apps felhőméretű fogyasztás alapján munkafolyamat és a vezénylési funkciókat, amelyek lehetővé teszik, hogy gyorsan és egyszerűen épület összetett integrációs megoldásokat böngésző használatával, vagy a Visual Studio eszközök segítségével biztosít.

A következő táblázat a Logic Apps BizTalk szolgáltatások képességek leképezéseket biztosít.

| BizTalk Services   | Logic Apps            | Cél                  |
| ------------------ | --------------------- | ---------------------------- |
| összekötő          | összekötő             | Adatok küldésére és fogadására   |
| Híd             | Logikai alkalmazás             | Feldolgozási sor processzor           |
| Szakasz ellenőrzése     | XML-érvényesítés művelet      | Az XML-dokumentum, a séma érvényesítése             |
| Szakasz kiegészítése       | Adatok jogkivonatok      | Tulajdonságok előléptetni üzenetbe vagy a útválasztási döntések             |
| Átalakítás szakasz    | Átalakítási művelet      | Átalakítása XML-üzenetek egy adott formátumból más             |
| Dekódolás szakasz       | Egybesimított fájl dekódolási művelet      | Egybesimított fájlból átalakítása XML             |
| Szakasz kódolása       |  Egybesimított fájl kódolása művelet      | Az XML-átalakítása egybesimított fájl             |
| Üzenet Inspector       |  Az Azure Functions vagy API-alkalmazások      | Futtassa a egyéni kódot a integrációja             |
| Útvonal-művelet      |  Az állapot vagy a kapcsoló      | A megadott összekötők egyikéhez üzenetek             |

Számos különböző típusú BizTalk Services összetevő.

## <a name="connectors"></a>Összekötők
BizTalk szolgáltatások összekötők küldeni és fogadni az adatokat, beleértve, hogy engedélyezve van a HTTP-alapú kérelem/válasz kapcsolati kétirányú hidak hidak engedélyezése. A Logic Apps ugyanazokat a kifejezéseket használjuk. Összekötők logic apps ugyanazt a célt szolgálják, és is tartalmaz a több mint 140, amely csatlakozni tudna a széles körű tömbje technológiákat és szolgáltatásokat, mind a helyszíni használatával a helyszíni Data Gateway (cseréje a BizTalk szolgáltatás BizTalk szolgáltatás által használt), és a Szolgáltatottszoftver- és PaaS felhőszolgáltatásokhoz, mint például a onedrive-on, Office365, Dynamics CRM és sok más.

BizTalk szolgáltatások források FTP, SFTP, és a Service Bus-üzenetsorba vagy témakör előfizetés korlátozódnak.

![](media/logic-apps-move-from-mabs/sources.png)

Minden egyes híd HTTP-végponttal rendelkezik alapértelmezés szerint a futásidejű cím és a híd relatív cím tulajdonságait kell konfigurálni. Ugyanez a Logic Apps eléréséhez használja a [kérelem-válasz](../connectors/connectors-native-reqres.md) műveletek.

## <a name="xml-processing-and-bridges"></a>XML-feldolgozás és a hidak
BizTalk szolgáltatások hidat egy feldolgozási folyamat hasonló. Hidat is igénybe vehet egy összekötő érkező adatokat, és néhány az adatokat, és küldje el a rendszer egy másik. A Logic Apps nem azonos a azonos csővezeték-alapú kommunikáció minták BizTalk szolgáltatásként támogatásával, és is számos más integrációs kombinációját. A [XML-kérelem-válasz híd](https://msdn.microsoft.com/library/azure/hh689781.aspx) a BizTalk szolgáltatások nevezik, amelyek szakaszból álló VETER folyamat:

* (V) érvényesítése
* E kiegészítése
* (T) átalakítás
* E kiegészítése
* (R) útvonal

Az alábbi képen látható, a feldolgozási kérés és válasz elosztva, és lehetővé teszi, hogy a kérelem és a válasz elérési utak külön-külön (például az egyes különböző megtekintését):

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Emellett egyirányú híd XML dekódolási és Encode szakaszában hozzáadja a elején és végén feldolgozási, majd a csatlakoztatott híd tartalmaz egyetlen Enrich szintre.

### <a name="message-processing-and-decodingencoding"></a>Üzenet feldolgozásához, és dekódolási/kódolás
BizTalk szolgáltatások a különböző XML-üzeneteket fogadni, és határozza meg a beérkezett üzenet megfelelő sémáját. Ez történik, a **üzenettípusok** a fogadási folyamat szakasza. Ezt követően a dekódolási szakasz észlelt üzenettípus dekódolni a megadott séma használatával használja. Ha a séma flatfile a séma, átalakítja a bejövő flatfile XML. 

A Logic Apps hasonló funkciókat biztosít. Egy flatfile különböző protokollok, a másik összekötő eseményindítókat (File System, FTP, HTTP és így tovább) segítségével számos protokollal fogadjanak, és használja a [Egybesimított fájl dekódolása](../logic-apps/logic-apps-enterprise-integration-flatfile.md) műveletet úgy, hogy a bejövő adatok átalakítása XML. Helyezze át a létező fájlszintű sémák közvetlenül a logic apps módosítása nélkül, és beállíthatja, majd töltse fel a sémák integrációs fiókjába.

### <a name="validation"></a>Ellenőrzés
A bejövő adatok átalakítása után XML (vagy ha XML-kódja a üzenetformátum érkezett), érvényesítési futtatása, és határozza meg, ha az üzenet megfelelő az XSD-sémával. Ehhez a Logic Apps, használja a [XML-érvényesítés](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) művelet. Ebben az esetben az azonos sémák BizTalk szolgáltatások-módosítás nélkül is használhatja.

### <a name="transform-messages"></a>Átalakítás üzenetek
A BizTalk szolgáltatások az átalakítási szakasz alakít egy XML-alapú üzenetformátum a másikra. Ez a TRFM-alapú hozzárendelő használatával térképre alkalmazásával történik. A Logic Apps a folyamat hasonlít. Az átalakítási műveletet hajtja végre a térkép integrációs fiókjából. A fő különbség, hogy a Logic Apps maps XSLT-formátumban vannak-e. XSLT lehetővé teszi a meglévő XSLT már, beleértve a maps létre BizTalk Server functoids tartalmazó újból. 

### <a name="routing-rules"></a>Útválasztási szabályokat
BizTalk szolgáltatások lehetővé teszi, hogy mely végpont/összekötőn adatküldés bejövő üzenetek/döntés. Válasszon ki egy olyan előre konfigurált végpontok száma nem lehetséges a útválasztási szűrő beállítás használatával:

![](media/logic-apps-move-from-mabs/route-filter.png)

A Logic Apps bonyolultabb logikát képességeket biztosít a [feltétel](../logic-apps/logic-apps-use-logic-app-features.md) és [kapcsoló](../logic-apps/logic-apps-switch-case.md), speciális folyamatábrán engedélyezése és útválasztást. BizTalk szolgáltatások útválasztási szűrők konvertálása a legkönnyebben használatával egy **feltétel** *Ha* csak két lehetőség. Ha több mint két, majd használja a **kapcsoló**.

### <a name="enrich"></a>Kiegészítése
BizTalk szolgáltatások feldolgozása a Enrich szakasza további tulajdonságokkal bővít, a fogadott adatok társított üzenet környezetbe. Például támogatása (alább) az adatbázis-lekérdezés vagy XPath kifejezés segítségével érték beolvasásával útválasztási használandó tulajdonság. A Logic Apps hozzáférést biztosít a mindegyikhez környezetfüggő adatok a fenti műveletek, így egyszerű replikálásához a kívánt viselkedést eredményező beállítást. Használata esetén például a `Get Row` SQL-kapcsolat művelet, térjen vissza az adatokat egy SQL Server-adatbázisból, és az adatok felhasználásával döntési művelettel irányításához. Hasonlóképpen, a bejövő Service Buson tulajdonságok várólistára helyezett üzenetek egy eseményindító által megcímezhető, valamint a xpath munkafolyamat definition language eredményező kifejezés XPath.

### <a name="use-custom-code"></a>Egyéni kód használata
BizTalk szolgáltatások lehetővé teszi a [egyéni kódra](https://msdn.microsoft.com/library/azure/dn232389.aspx) saját szerelvényekben feltöltve. Ez megvalósítja a [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) felületet. Minden szakaszhoz a Bridge magában foglalja a két tulajdonságok (adja meg Inspector a és a kilépési Inspector) adja meg a létrehozott .net típusát, amely megvalósítja ezt a felületet. Egyéni kód lehetővé teszi összetett feldolgozási végezze el az adatokat, valamint a gyakori üzleti logika végző szerelvényeket a meglévő kódot ismét felhasználni. 

A Logic Apps egyéni kódot két elsődleges lehetőséget biztosít: az Azure Functions és API-alkalmazások. Az Azure Functions hozható létre, és a logic Apps alkalmazásokból nevezik. Lásd: [hozzáadása és az Azure Functions használatával logic Apps-alkalmazások futtatása egyéni kód](../logic-apps/logic-apps-azure-functions.md). API Apps, az Azure App Service-ben részét segítségével létrehozhat saját eseményindítók és műveletek. További információ [létrehozni egy egyéni API használata a Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Ha egyéni kód, amely meghívja a BizTalk szolgáltatásokból assmeblies tartalmaz, helyezze át a kódot az Azure Functions, vagy hozzon létre egyéni API-kat API-alkalmazások; attól függően, hogy mi meg megvalósításához. Például ha egy másik szolgáltatást, hogy a Logic Apps nem rendelkezik egy összekötő nagyságúra kódot, majd API-alkalmazás létrehozása, és a műveletek az API-alkalmazás biztosít a logikai alkalmazásban használni. Ha súgófunkciókat vagy álljon, majd az Azure Functions oka a legjobb térkihasználás érdekében.

### <a name="edi-processing-and-trading-partner-management"></a>EDI dolgoz fel, és kereskedelmipartner-kezelés
BizTalk szolgáltatások részét képező EDI és B2B feldolgozási AS2-támogatással rendelkező (alkalmazhatósági utasítás 2), X12 és EDIFACT. Ezáltal a Logic Apps. BizTalk szolgáltatások a EDI hidak létrehozása és létrehozása vagy kezelése kereskedelmi partnereknek és szerződések a dedikált nyomon követését és a felügyeleti portálon.

A Logic Apps, ez a funkció megtalálható a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md). Ez az integráció fiók és a B2B műveletek EDI és B2B feldolgozásra áll. A [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) létrehozásához és kezeléséhez használt [kereskedelmi partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) és [megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md). Miután létrehozott egy integrációs fiókkal, egy vagy több logic Apps alkalmazásokat a fiók lehet társítani. Járó, használhatja a B2B műveletek a logikai alkalmazásban kereskedelmi partneradatok eléréséhez. Itt találhatók a következő műveleteket:

* AS2-kódolása
* AS2 dekódolása
* X12 kódolása
* X12 dekódolása
* EDIFACT kódolása
* EDIFACT dekódolása

BizTalk szolgáltatások ellentétben ezek a műveletek az átviteli protokollokat vannak leválasztja. Ezért a logic apps létrehozásakor több beleszólása van mely összekötő segítségével adatokat küldeni és fogadni. Például az lehetséges X12 fogadásához fájlok mellékletként az e-mailek, és ezután folyamat ezeket a fájlokat egy logikai alkalmazást. 

## <a name="manage-and-monitor"></a>Kezelése és figyelése
Kereskedelmipartner-kezelés, valamint a dedikált portál BizTalk szolgáltatás a megadott nyomkövetési lehetőségeket biztosítanak, és problémák hibaelhárítása. 

A Logic Apps nyújt részletesebb nyomon követése és figyelési képességek a a [Azure-portálon](../logic-apps/logic-apps-monitor-your-logic-apps.md), és a [Operations Management Suite B2B megoldás](../logic-apps/logic-apps-monitor-b2b-message.md); beleértve egy mobilalkalmazást tartása követheti a művelet az áthelyezés közben.

## <a name="high-availability"></a>Magas rendelkezésre állás
BizTalk szolgáltatások magas rendelkezésre ÁLLÁS eléréséhez több példányt használ egy adott régióban a feldolgozási terhelés. A logic apps-régió magas rendelkezésre ÁLLÁSÚ beépített, tesztelhet, és minden további költség nélkül. Ki a régiót vész-helyreállítási BizTalk szolgáltatások B2B feldolgozásához a biztonsági mentési és visszaállítási folyamat szükség. A Logic Apps, a kereszt-régió aktív/passzív [vész-Helyreállítási képességet](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) biztosított; a szinkronizálás B2B adatok lehetővé teszi az üzletmenet folytonossága érdekében különböző régiókban integrációs fiókok között.

## <a name="next"></a>Következő lépés
* [Mi az a Logic Apps?](logic-apps-what-are-logic-apps.md)
* [Az első logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md), vagy a használat gyors megkezdése [előre elkészített sablonokkal](logic-apps-create-logic-apps-from-templates.md)  
* A logikai alkalmazásban használható [összes rendelkezésre álló összekötő](../connectors/apis-list.md) megtekintése
