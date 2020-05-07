---
title: Az Azure Logic Apps összekötői
description: Azure Logic Apps-összekötők, például beépített, felügyelt, helyszíni, integrációs fiók, ISE és vállalati összekötők összekapcsolása munkafolyamatok automatizálásával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: f2a2ee7a2806a753ffd159c91ed782634e74c704
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838661"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Az összekötők gyors hozzáférést biztosítanak az Azure Logic Appsből más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok eseményeihez, adataihoz és műveleteihez. A logikai alkalmazások összekötőivel kibővítheti a felhőbeli és a helyszíni alkalmazások képességeit annak érdekében, hogy feladatokat hajthassanak végre a létrehozott és már meglévő adatokkal.

Míg Logic Apps [több száz összekötőt](https://docs.microsoft.com/connectors)kínál, ez a cikk azokat a *népszerű és gyakrabban használt* összekötőket ismerteti, amelyeket több ezer alkalmazás sikeresen használ, és több millió végrehajtást biztosít az adatok és információk feldolgozásához. Az összekötők és az egyes összekötők (például triggerek, műveletek és korlátok) teljes listájának megkereséséhez tekintse át az összekötők hivatkozási lapjait az [Összekötők áttekintése](https://docs.microsoft.com/connectors)területen. További információ az [Eseményindítók és műveletek](#triggers-actions), a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md)és a [Logic apps díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Ha olyan szolgáltatással vagy API-val szeretne integrálni, amely nem rendelkezik összekötővel, közvetlenül is meghívhatja a szolgáltatást egy protokollon keresztül, például HTTP-n keresztül, vagy létrehozhat egy [Egyéni összekötőt](#custom).

## <a name="connector-types"></a>Összekötő típusai

Az összekötők beépített eseményindítóként és műveletként, illetve felügyelt összekötőként érhetők el.

<a name="built-in"></a>

* [**Beépített**](#built-ins): beépített triggerek és műveletek "natív" Azure Logic apps és segít a következő feladatok elvégzésében a logikai alkalmazásokhoz:

  * Futtatás egyéni és speciális ütemterveken.

  * Rendszerezheti és szabályozhatja a logikai alkalmazás munkafolyamatát, például a hurkokat és a feltételeket, valamint a változók és az adatműveletek működését is.

  * Kommunikáció más végpontokkal.

  * Kérések fogadása és megválaszolása.

  * Hívja meg az Azure functions, az Azure API Apps (Web Apps), a saját API-k által felügyelt és közzétett Azure API Management, valamint a beágyazott logikai alkalmazásokat, amelyek fogadhatnak kérelmeket.

<a name="managed-connectors"></a>

* [**Felügyelt összekötők**](#managed-api-connectors): a Microsoft által központilag telepített és felügyelt, ezek az összekötők a Cloud Services, a helyszíni rendszerek vagy mindkettő, például az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és egyéb szolgáltatások elérésére szolgáló eseményindítókat és műveleteket biztosítanak. Egyes összekötők kifejezetten támogatják a vállalatközi (B2B) kommunikációs forgatókönyveket, és megkövetelik a logikai alkalmazáshoz csatolt [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bizonyos összekötők használata előtt előfordulhat, hogy először a Azure Logic Apps által felügyelt kapcsolatokat kell létrehoznia.

  Ha például Microsoft-BizTalk Server használ, a logikai alkalmazások a [BizTalk Server helyszíni összekötő](#on-premises-connectors)használatával csatlakozhatnak a BizTalk Serverhoz, és kommunikálhatnak velük. Ezután az [integrációs fiók összekötői](#integration-account-connectors)használatával kiterjesztheti vagy elvégezheti a BizTalk-szerű műveleteket a logikai alkalmazásokban.

  Az összekötők a standard vagy a vállalat kategóriába tartoznak. A [vállalati összekötők](#enterprise-connectors) felár ellenében hozzáférést biztosítanak a nagyvállalati rendszerekhez, például az SAP, az IBM MQ és az IBM 3270 szolgáltatásokhoz. Annak megállapításához, hogy az összekötő szabványos vagy nagyvállalati szintű-e, tekintse meg a technikai részleteket az egyes összekötők hivatkozásait ismertető oldalon az [Összekötők áttekintésében](https://docs.microsoft.com/connectors).

  Az összekötőket ezen kategóriák használatával is azonosíthatja, bár egyes összekötők több kategóriát is használhatnak. Például az SAP egy vállalati összekötő és egy helyszíni összekötő:

  |   |   |
  |---|---|
  | [**Felügyelt összekötők**](#managed-api-connectors) | Az Azure Blob Storage, az Office 365, a Dynamics, a Power BI, a OneDrive, a Salesforce, a SharePoint Online és sok más szolgáltatást használó logikai alkalmazásokat hozhat létre. |
  | [**Helyszíni összekötők**](#on-premises-connectors) | A helyszíni [adatátjáró][gateway-doc]telepítése és beállítása után ezek az összekötők segítenek a logikai alkalmazásoknak olyan helyszíni rendszerek elérésében, mint a SQL Server, a SharePoint Server, a Oracle db, a fájlmegosztás és egyebek. |
  | [**Integrációs fiókok összekötői**](#integration-account-connectors) | Akkor érhető el, ha integrációs fiókot hoz létre és fizet ki, ezek az összekötők átalakítja és érvényesíti az XML-t, kódolja és dekódolja a lapos fájlokat, és feldolgozza a vállalatközi (B2B) üzeneteket az AS2, a EDIFACT és a X12 protokollal. |
  |||

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Csatlakozás integrációs szolgáltatási környezetből

Azon logikai alkalmazások esetében, amelyeknek közvetlen hozzáférésre van szükségük egy Azure-beli virtuális hálózat erőforrásaihoz, létrehozhat egy elkülönített [integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , ahol a logikai alkalmazásokat dedikált erőforrásokon hozhatja létre, helyezheti üzembe és futtathatja. A Logic app Designerben, amikor az ISE-ben a Logic apps-hez használni kívánt összekötőket böngészi, a **beépített** triggereken és műveleteken megjelenik egy alapfelirat, míg az **ISE** címke egyes összekötőn megjelenik:

* **Mag**: az ezzel a címkével rendelkező beépített eseményindítók és műveletek a logikai alkalmazásokkal megegyező ISE-ben futnak, például:

  ![Példa ISE-összekötőre](./media/apis-list/example-core-connector.png)

* **ISE**: az ezzel a címkével rendelkező felügyelt összekötők a logikai alkalmazásokkal megegyező ISE-ben futnak, például:

  ![Példa ISE-összekötőre](./media/apis-list/example-ise-connector.png)

  Ha egy Azure-beli virtuális hálózathoz csatlakoztatott helyszíni rendszerrel rendelkezik, az ISE lehetővé teszi, hogy a logikai alkalmazások közvetlenül hozzáférhessenek a rendszerhez a [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md)nélkül. Ehelyett használhatja a rendszer **ISE** -összekötőjét, ha van ilyen, http-művelet vagy [egyéni összekötő](#custom). Az **ISE** -összekötőket nem tartalmazó helyszíni rendszerekhez használja a helyszíni adatátjárót. Az elérhető ISE-összekötők áttekintését lásd: [ISE-összekötők](#ise-connectors).

* Az összes többi olyan összekötő, amely nem rendelkezik a **Core** vagy az **ISE** címkével, amelyet továbbra is használhat, a globális, több-bérlős Logic apps szolgáltatásban futtathatja, például:

  ![Példa több-bérlős összekötőre](./media/apis-list/example-multi-tenant-connector.png)

Az ISE-ben és azok összekötői futó Logic apps, függetlenül attól, hogy az összekötők hol futnak, a rögzített díjszabási csomagot és a fogyasztáson alapuló díjszabást kell követnie. További információt a következő lapokon talál:

* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Beépített

A Logic Apps beépített eseményindítókat és műveleteket biztosít, így ütemezhetők-alapú munkafolyamatokat hozhat létre, segítséget nyújthat a logikai alkalmazásoknak más alkalmazásokkal és szolgáltatásokkal való kommunikációhoz, a munkafolyamat vezérléséhez a logikai alkalmazásokon keresztül, valamint az adatkezeléshez és az adatkezeléshez.

|   |   |   |   |
|---|---|---|---|
| [![API-][schedule-icon]<br>ikon**ütemterve**][schedule-doc] | – Futtasson egy logikai alkalmazást egy megadott ismétlődésen, amely az alapszintű és a speciális ütemezések között az [ **ismétlődési** eseményindítóval][schedule-recurrence-doc]. <p>– Futtasson egy logikai alkalmazást, amely folyamatos adattömbökben lévő, a [ **csúszó ablakos** triggerrel][schedule-sliding-window-doc]való adatkezelésre szorul. <p>– Szüneteltetheti a logikai alkalmazást egy adott időtartamra a [ **késleltetési** művelettel][schedule-delay-doc]. <p>– Szüneteltetheti a logikai alkalmazást, amíg a [művelet **nem** ][schedule-delay-until-doc]lesz a megadott dátummal és időponttal. | [![API-][batch-icon]<br>ikon**köteg**][batch-doc] | – A Batch **üzenetek** triggerrel rendelkező kötegekben lévő üzenetek feldolgozása. <p>– Olyan logikai alkalmazásokat hívhat meg, amelyek meglévő batch-eseményindítókkal rendelkeznek az **üzenetek küldése kötegelt** művelettel. |
| [![API-][http-icon]<br>ikon**http**][http-doc] | HTTP-vagy HTTPS-végpontok hívása a HTTP-alapú eseményindítókkal és műveletekkel. Más beépített HTTP-eseményindítók és műveletek a következők: [http + hencegés][http-swagger-doc] és [http + webhook][http-webhook-doc]. | [![API-][http-request-icon]<br>ikon**kérése**][http-request-doc] | – Meghívhatja a logikai alkalmazást más alkalmazásokból vagy szolgáltatásokból, kiválthatja Event Grid erőforrás-eseményeit, vagy aktiválhatja a válaszokat a **kérések** triggerével Azure Security Center riasztásokra. <p>– Válaszok küldése egy alkalmazásnak vagy szolgáltatásnak a **Válasz** művelettel. |
| [![API-][azure-api-management-icon]<br>ikon**Azure <br>API Management**][azure-api-management-doc] | Meghívja a saját API-k által definiált eseményindítókat és műveleteket, amelyeket az Azure API Management felügyel és tesz közzé. | [![API-][azure-app-services-icon]<br>ikon**Azure <br>app Services**][azure-app-services-doc] | Hívja meg az Azure API Apps vagy Web Appst, amely Azure App Serviceon üzemel. Az alkalmazások által definiált eseményindítók és műveletek úgy jelennek meg, mint bármely más, az első osztályú eseményindító és művelet, amikor a kivágás bekerül.|
| [![API-][azure-logic-apps-icon]<br>ikon**Azure <br>Logic apps**][nested-logic-app-doc] | Hívjon fel más Logic apps-alkalmazásokat, amelyek a **kérelem** -triggerrel kezdődnek. |
|||||

### <a name="run-code-from-logic-apps"></a>Kód futtatása a Logic appsből

A Logic Apps beépített műveleteket biztosít a saját kód futtatásához a logikai alkalmazás munkafolyamataiban:

|   |   |   |   |
|---|---|---|---|
| [![API-][azure-functions-icon]<br>ikon**Azure functions**][azure-functions-doc] | Hívja meg az Azure functions szolgáltatást, amely egyéni kódrészleteket (C# vagy Node. js) futtat a logikai alkalmazásokból. | [![API-][inline-code-icon]<br>ikon**beágyazott kódja**][azure-functions-doc] | JavaScript-kódrészletek hozzáadása és futtatása a logikai alkalmazásokból. |
|||||

### <a name="control-workflow"></a>Vezérlési munkafolyamat

A Logic Apps beépített műveleteket biztosít a logikai alkalmazás munkafolyamatában lévő műveletek rendszerezéséhez és szabályozásához:

|   |   |   |   |
|---|---|---|---|
| [![Beépített ikon][condition-icon]<br>**feltétele**][condition-doc] | Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis. | [![Beépített][for-each-icon]<br>**ikon**][for-each-doc] | Hajtsa végre ugyanezeket a műveleteket egy tömb minden elemén. |
| [![Beépített ikon][scope-icon]<br>**hatóköre**][scope-doc] | A *műveletek hatókörbe*való csoportosítása, amelyek a hatókör befejezésének befejezése után kapják meg a saját állapotukat. | [![Beépített ikon][switch-icon]<br>**kapcsoló**][switch-doc] | Olyan *esetekre*csoportosíthat műveleteket, amelyek az alapértelmezett eset kivételével egyedi értékeket kapnak. Csak akkor futtassa ezt az esetet, ha a hozzárendelt érték megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet. |
| [![Beépített ikon][terminate-icon]<br>**leállítása**][terminate-doc] | Egy aktívan futó logikai alkalmazás munkafolyamatának leállítása. | [![Beépített ikon][until-icon]<br>,**amíg**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott. |
|||||

### <a name="manage-or-manipulate-data"></a>Az adatkezelés és-kezelés

A Logic Apps beépített műveleteket biztosít az adatkimenetek és azok formátumának kezeléséhez:

|   |   |
|---|---|
| [![Beépített ikon][data-operations-icon]<br>**adatműveletek**][data-operations-doc] | Műveletek végrehajtása adatokkal: <p>- **Összeállítás**: egyetlen kimenet létrehozása több bemenetből különböző típusokkal. <br>- **CSV-táblázat létrehozása**: hozzon létre egy vesszővel tagolt (CSV-) táblázatot egy olyan tömbből, amely JSON-objektumokkal rendelkezik. <br>- **HTML-táblázat létrehozása**: HTML-táblázat létrehozása egy JSON-objektumokkal rendelkező tömbből. <br>- **Tömb szűrése**: hozzon létre egy tömböt egy másik tömb elemeiből, amelyek megfelelnek a feltételeknek. <br>- **Csatlakozás**: hozzon létre egy karakterláncot egy tömb összes eleméről, és válassza szét ezeket az elemeket a megadott elválasztóval. <br>- **JSON**elemzése: hozzon létre felhasználóbarát tokeneket a tulajdonságok és azok értékei alapján a JSON-tartalomban, hogy használhassa ezeket a tulajdonságokat a munkafolyamatban. <br>- **Válassza ki**: hozzon létre egy tömböt JSON-objektumokkal egy másik tömb elemeinek vagy értékeinek átalakításával, és rendelje hozzá ezeket az elemeket a megadott tulajdonságokhoz. |
| ![Beépített ikon][date-time-icon]<br>**Dátum és idő** | Műveletek elvégzése időbélyegekkel: <p>- **Hozzáadás az időponthoz**: adja hozzá a megadott számú egységet egy időbélyeghez. <br>- **Időzóna konvertálása**: a forrás időzóna időbélyegének konvertálása a cél időzónára. <br>- **Aktuális idő**: az aktuális időbélyeg visszaadása karakterláncként. <br>- **Jövőbeli idő beolvasása**: az aktuális időbélyeg és a megadott időegységek visszaadása. <br>- **Beolvasás időpontja**: az aktuális időbélyeg visszaküldése a megadott időegységek szerint. <br>- **Kivonás az időpontból**: több időegység kivonása egy időbélyegből. |
| [![Beépített ikon][variables-icon]<br>**változók**][variables-doc] | Műveletek végrehajtása változókkal: <p>- **Hozzáfűzés a tömb változóhoz**: szúrjon be egy értéket egy változó által tárolt tömb utolsó elemeként. <br>- **Hozzáfűzés a karakterlánc-változóhoz**: az értéket egy változó által tárolt sztring utolsó karakterének szúrja be. <br>- **Változó csökkentése**: a változókat állandó értékkel csökkentheti. <br>- **Növekmény változó**: állandó értékkel növelheti a változókat. <br>- **Változó inicializálása**: hozzon létre egy változót, és állapítsa meg az adattípus és a kezdeti érték megadását. <br>- **Változó beállítása**: adjon meg egy másik értéket egy meglévő változóhoz. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Felügyelt összekötők

A Logic Apps ezeket a népszerű szabványos összekötőket biztosítja a feladatok, folyamatok és munkafolyamatok automatizálásához ezekkel a szolgáltatásokkal vagy rendszerekkel:

|   |   |   |   |
|---|---|---|---|
| [![API-][azure-service-bus-icon]<br>ikon**Azure Service Bus**][azure-service-bus-doc] | A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet. | [![API-][sql-server-icon]<br>ikon**SQL Server**][sql-server-doc] | Kapcsolódjon SQL Server a felhőben, vagy egy Azure SQL Database a felhőben, így kezelheti a rekordokat, futtathatja a tárolt eljárásokat vagy lekérdezéseket végezhet. |
| [![API-][azure-blob-storage-icon]<br>ikon**Azure<br>blob Storage**][azure-blob-storage-doc] | Kapcsolódjon a Storage-fiókhoz a blob-tartalmak létrehozásához és kezeléséhez. | [![API-][office-365-outlook-icon]<br>ikon**Office<br>365 Outlook**][office-365-outlook-doc] | Kapcsolódjon az Office 365 e-mail-fiókjához, így e-maileket, feladatokat, naptári eseményeket és értekezleteket, névjegyeket, kéréseket és egyebeket hozhat létre és kezelhet. |
| [![API][sftp-ssh-icon]<br>**-ikon SFTP – SSH**][sftp-ssh-doc] | Olyan SFTP-kiszolgálókhoz csatlakozhat, amelyek az internetről az SSH használatával érhetők el, hogy a fájlok és mappák is működjenek. | [![API-][sharepoint-online-icon]<br>**ikon<br>SharePoint Online**][sharepoint-online-doc] | Kapcsolódjon a SharePoint Online-hoz, hogy kezelje a fájlokat, a mellékleteket, a mappákat és egyebeket. | 
| [![API-][dynamics-365-icon]<br>ikon**Dynamics<br> 365**][dynamics-365-doc] | Kapcsolódjon a Dynamics 365-fiókjához, hogy rekordokat, elemeket és egyebeket lehessen létrehozni és kezelni. | [![API-][azure-queues-icon]<br>**ikon <br>Azure-várólisták**][azure-queues-doc] | Kapcsolódjon az Azure Storage-fiókjához, hogy várólistákat és üzeneteket lehessen létrehozni és kezelni |
| [![API-][ftp-icon]<br>ikon**FTP**][ftp-doc] | Csatlakozhat az internetről elérhető FTP-kiszolgálókhoz, így a fájlokkal és mappákkal is dolgozhat. | [![API-][file-system-icon]<br>**ikon <br>fájlrendszere**][file-system-doc] | Kapcsolódjon a helyszíni fájlmegosztás számára, hogy fájlokat lehessen létrehozni és kezelni. |
| [![API-][azure-event-hubs-icon]<br>ikon**Azure Event Hubs**][azure-event-hubs-doc] | Események felhasználása és közzététele az Event hub használatával. Például lekérheti a logikai alkalmazás kimenetét Event Hubs, majd elküldheti a kimenetet egy valós idejű elemzési szolgáltatónak. | [![API-][azure-event-grid-icon]<br>ikon**Azure Event**<br>**Grid**][azure-event-grid-doc] | Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a külső gyártók erőforrásai változnak. |
| [![API-][salesforce-icon]<br>ikon**Salesforce**][salesforce-doc] | Kapcsolódjon a Salesforce-fiókhoz, így olyan elemeket hozhat létre és kezelhet, mint a rekordok, a feladatok, az objektumok stb. | [![API-][twitter-icon]<br>ikon**Twitter**][twitter-doc] | Csatlakozhat a Twitter-fiókjához, hogy kezelje a tweeteket, a követőiket, az idővonalat és egyebeket. Mentse a tweeteket az SQL, az Excel vagy a SharePoint szolgáltatásba. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők

Íme néhány általánosan használt standard összekötő, amelyet Logic Apps biztosít a helyszíni rendszerekben tárolt adatok és erőforrások eléréséhez. A helyszíni rendszerhez való kapcsolódás előtt először [le kell töltenie, telepítenie és be kell állítania egy helyszíni adatátjárót][gateway-doc]. Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy be kellene állítania a szükséges hálózati infrastruktúrát.

|   |   |   |   |   |
|---|---|---|---|---|
| [![API-][biztalk-server-icon]<br>ikon**BizTalk** <br> **Server**][biztalk-server-doc] | [![API-][file-system-icon]<br>**ikon <br>fájlrendszere**][file-system-doc] | [![API-][ibm-db2-icon]<br>ikon**IBM DB2**][ibm-db2-doc] | [![API-][ibm-informix-icon]<br>ikon**IBM** <br> **Informix**][ibm-informix-doc] | [![API-][mysql-icon]<br>ikon**MySQL**][mysql-doc] |
| [![API-][oracle-db-icon]<br>ikon**Oracle db**][oracle-db-doc] | [![API-][postgre-sql-icon]<br>ikon**PostgreSQL**][postgre-sql-doc] | [![API-][sharepoint-server-icon]<br>**ikon <br>SharePoint-kiszolgáló**][sharepoint-server-doc] | [![API-][sql-server-icon]<br>**ikon <br>SQL Server**][sql-server-doc] | [![API-][teradata-icon]<br>ikon**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

A Logic Apps szabványos összekötőket biztosít a vállalatközi (B2B) megoldások létrehozásához és az Azure-beli Enterprise Integration Pack (EIP) keresztül elérhető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)kifizetéséhez. Ezzel a fiókkal B2B-összetevőket hozhat létre és tárolhat, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat stb. Ezeknek az összetevőknek a használatához társítsa a logikai alkalmazásokat az integrációs fiókjával. Ha jelenleg BizTalk Server használ, előfordulhat, hogy ezek az összekötők már ismerősnek tűnnek.

|   |   |   |   |
|---|---|---|---|
| [![API-][as2-icon]<br>**ikon <br>AS2-dekódolás**][as2-doc] | [![API-][as2-icon]<br>**ikon <br>AS2-kódolás**][as2-doc] | [![API-][edifact-icon]<br>**ikon <br>EDIFACT dekódolása**][edifact-decode-doc] | [![API-][edifact-icon]<br>**ikon <br>EDIFACT kódolása**][edifact-encode-doc] |
| [![API-][flat-file-decode-icon]<br>ikon –**lapos fájl <br>dekódolása**][flat-file-decode-doc] | [![API-][flat-file-encode-icon]<br>ikon –**lapos fájl <br>kódolása**][flat-file-encode-doc] | [![API-][integration-account-icon]<br>**ikon <br>integrációs fiókja**][integration-account-doc] | [![API-][liquid-icon]<br>ikon**folyékony** <br> **átalakítások**][json-liquid-transform-doc] |
| [![API-][x12-icon]<br>**ikon <br>X12 dekódolása**][x12-decode-doc] | [![API-][x12-icon]<br>**ikon <br>X12 kódolása**][x12-encode-doc] | [![API][xml-transform-icon]<br>**-ikon XML-** <br> **átalakítások**][xml-transform-doc] | [![API][xml-validate-icon]<br>**-ikon <br>XML-ellenőrzése**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A Logic Apps a nagyvállalati rendszerekhez (például SAP és IBM MQ) való hozzáféréshez biztosít ilyen vállalati összekötőket:

|   |   |   |
|---|---|---|
| [![API-][ibm-3270-icon]<br>ikon**IBM 3270**][ibm-3270-doc] | [![API-][ibm-mq-icon]<br>ikon**IBM MQ**][ibm-mq-doc] | [![API-][sap-icon]<br>ikon**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE-összekötők

Az elkülönített [integrációs szolgáltatási környezet (ISE)](#integration-service-environment)létrehozásához és futtatásához a Logic app Designer azonosítja a beépített eseményindítókat és műveleteket, **amelyek az ISE** -ben futnak az alapcímke használatával. Az ISE-ben futó felügyelt összekötők az **ISE** címkét jelenítik meg, míg a globális, több-bérlős Logic apps szolgáltatásban futó összekötők nem jelenítik meg a címkét. Ez a lista a jelenleg ISE-verziókkal rendelkező összekötőket jeleníti meg:

|   |   |   |   |   |
|---|---|---|---|---|
[![API-][as2-icon]<br>ikon**AS2**][as2-doc] | [![API-][azure-automation-icon]<br>**ikon <br>Azure Automation**][azure-automation-doc] | [![API-][azure-blob-storage-icon]<br>ikon**Azure<br>blob Storage**][azure-blob-storage-doc] | [![API-][azure-cosmos-db-icon]<br>ikon**Azure <br> Cosmos db**][azure-cosmos-db-doc] | [![API-][azure-event-hubs-icon]<br>ikon**Azure <br>Event hubok**][azure-event-hubs-doc] |
[![API-][azure-event-grid-icon]<br>ikon**Azure <br>Event Grid**][azure-event-grid-doc] | [![API-][azure-file-storage-icon]<br>ikon**Azure<br>file Storage**][azure-file-storage-doc] | [![API-][azure-key-vault-icon]<br>ikon az**Azure Key <br>vaultban**][azure-key-vault-doc] | [![API-][azure-monitor-logs-icon]<br>**ikon <br>Azure monitor naplók**][azure-monitor-logs-doc] | [![API-][azure-service-bus-icon]<br>ikon**Azure <br>Service Bus**][azure-service-bus-doc] |
| [![API-][azure-sql-data-warehouse-icon]<br>ikon**az <br>Azure SQL-adattárházban**][azure-sql-data-warehouse-doc] | [![API-][azure-table-storage-icon]<br>ikon**Azure <br>Table Storage**][azure-table-storage-doc] | [![API-][azure-queues-icon]<br>**ikon <br>Azure-várólisták**][azure-queues-doc] | [![API-][edifact-icon]<br>ikon**EDIFACT**][edifact-doc] | [![API-][file-system-icon]<br>**ikon <br>fájlrendszere**][file-system-doc] |
| [![API-][ftp-icon]<br>ikon**FTP**][ftp-doc] | [![API-][ibm-3270-icon]<br>ikon**IBM 3270**][ibm-3270-doc] | [![API-][ibm-db2-icon]<br>ikon**IBM DB2**][ibm-db2-doc] | [![API-][ibm-mq-icon]<br>ikon**IBM MQ**][ibm-mq-doc] | [![API-][sap-icon]<br>ikon**SAP**][sap-connector-doc] |
| [![API][sftp-ssh-icon]<br>**-ikon SFTP – SSH**][sftp-ssh-doc] | [![API-][smtp-icon]<br>ikon**SMTP**][smtp-doc] | [![API-][sql-server-icon]<br>**ikon <br>SQL Server**][sql-server-doc] | [![API-][x12-icon]<br>ikon**X12**][x12-doc] |
||||||

További információt az alábbi témakörökben talál:

* [Hozzáférés az Azure-beli virtuális hálózati erőforrásokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps díjszabási modell](../logic-apps/logic-apps-pricing.md)
* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Eseményindítók és műveleti típusok

Az összekötők *eseményindítókat*, *műveleteket*vagy mindkettőt adhatnak meg. Az *eseményindító* a logikai alkalmazások első lépése, amely általában megadja az eseményindítót kiváltó eseményt, és elindítja a logikai alkalmazás futtatását. Például az FTP-összekötő olyan triggerrel rendelkezik, amely elindítja a logikai alkalmazást "fájl hozzáadásakor vagy módosításakor". Egyes eseményindítók rendszeresen megkeresik a megadott eseményt vagy az adattípust, majd a megadott esemény vagy az adatgyűjtés észlelése után tüzet fognak. Más eseményindítók várnak, de azonnal megtörténnek egy adott esemény bekövetkezésekor, vagy amikor új adatkapcsolat áll rendelkezésre. Az eseményindítók a logikai alkalmazáshoz is továbbítanak minden szükséges adattal. A logikai alkalmazás az összes munkafolyamatban képes olvasni és felhasználni ezeket az adatait. Például a Twitter-összekötő egy triggerrel rendelkezik, "új Tweet közzétételekor", amely továbbítja a tweet tartalmát a logikai alkalmazás munkafolyamataiba.

Egy eseményindító elindítása után Azure Logic Apps létrehozza a logikai alkalmazás egy példányát, és elindítja a logikai alkalmazás munkafolyamatában lévő *műveleteket* . A műveletek a logikai alkalmazás munkafolyamataiban a triggert követő és a feladatokat elvégző lépések. Létrehozhat például egy olyan logikai alkalmazást, amely egy SQL-adatbázisból szerzi be a vásárlói adatait, és feldolgozza azokat a későbbi műveletekben.

A Azure Logic Apps az alábbi általános típusú eseményindítókat biztosítja:

* *Ismétlődési eseményindító*: ez az eseményindító megadott ütemezésen fut, és nincs szorosan társítva egy adott szolgáltatáshoz vagy rendszerhez.

* *Lekérdezési eseményindító*: ez az eseményindító rendszeresen lekérdezi egy adott szolgáltatást vagy rendszert a megadott ütemterv alapján, új adatellenőrzést végez, vagy adott esemény történt. Ha új adatok állnak rendelkezésre, vagy az adott esemény történt, az eseményindító létrehozza és futtatja a logikai alkalmazás egy új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

* *Leküldéses eseményindító*: ez az eseményindító megvárja az új vagy az esemény megkötését. Ha új adatok állnak rendelkezésre, vagy ha az esemény történik, az eseményindító létrehozza és futtatja a logikai alkalmazás új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

<a name="connections"></a>

## <a name="connector-configuration"></a>Összekötő konfigurációja

Minden összekötő eseményindítói és műveletei saját tulajdonságokat biztosítanak a konfigurálásához. Számos összekötő azt is megköveteli, hogy először létre kell hoznia egy *kapcsolódást* a cél szolgáltatáshoz vagy a rendszerhez, és meg kell adnia a hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt a logikai alkalmazásban eseményindítót vagy műveletet alkalmazhat. Az adatok eléréséhez vagy az Ön nevében történő közzétételhez például engedélyeznie kell egy Twitter-fiókhoz való kapcsolódást.

Azure Active Directory (Azure AD) OAuth használó összekötők esetén a kapcsolat létrehozása a szolgáltatásba való bejelentkezést jelenti, például az Office 365, a Salesforce vagy a GitHub szolgáltatást, ahol a hozzáférési token [titkosított](../security/fundamentals/encryption-overview.md) , és biztonságosan tárolható egy Azure titkos tárolóban. Más összekötők, például az FTP és az SQL esetében olyan kapcsolatra van szükség, amely konfigurációs adatokat tartalmaz, például a kiszolgáló címe, a Felhasználónév és a jelszó. Ezeket a kapcsolati konfigurációs adatokat is titkosítja és biztonságosan tárolja. További információ az [Azure-beli titkosításról](../security/fundamentals/encryption-overview.md).

A kapcsolatok a szolgáltatás vagy a rendszer engedélyezése esetén is hozzáférhetnek a cél szolgáltatáshoz vagy rendszerhez. Az Azure AD OAuth-kapcsolatokat használó szolgáltatások, például az Office 365 és a Dynamics esetében Azure Logic Apps határozatlan időre frissítik a hozzáférési jogkivonatokat. Előfordulhat, hogy az egyéb szolgáltatások korlátozva vannak, hogy Azure Logic Apps a tokent frissítés nélkül is használhatja. Bizonyos műveletek általában érvénytelenítik az összes hozzáférési jogkivonatot, például a jelszó módosítását.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Az egyéni kódot futtató vagy összekötőként nem elérhető API-k meghívásához [egyéni API apps létrehozásával](../logic-apps/logic-apps-create-api-app.md)kiterjesztheti a Logic apps platformot. [Létrehozhat egyéni összekötőket](../logic-apps/custom-connector-overview.md) is *bármilyen* Rest-vagy SOAP-alapú API-khoz, amelyek elérhetővé teszik az API-kat az Azure-előfizetésében található logikai alkalmazások számára. Ahhoz, hogy az egyéni API Apps vagy összekötők nyilvánosak legyenek az Azure-ban mindenki számára, a [Microsoft minősítéshez összekötőket küldhet](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Az [integrációs szolgáltatási környezetekben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) üzembe helyezett és futtatott logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz. Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.
>
> Az ISE-n belül létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető Azure-beli virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.
>
> A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>További lépések

* A [teljes összekötő listájának](https://docs.microsoft.com/connectors) megtekintése
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Egyéni összekötők létrehozása logikai alkalmazásokhoz](https://docs.microsoft.com/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Azure API Management Service-példány létrehozása az API-k kezeléséhez és közzétételéhez"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai alkalmazások integrálása App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozása csoportokban vagy kötegként"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ugyanazon műveletek végrehajtása egy tömb minden elemén"
[http-doc]: ./connectors-native-http.md "HTTP-vagy HTTPS-végpontok hívása a logikai alkalmazásokból"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérelmek fogadása a logikai alkalmazásokban"
[http-response-doc]: ./connectors-native-reqres.md "Válaszadás a logikai alkalmazásokból érkező HTTP-kérelmekre"
[http-swagger-doc]: ./connectors-native-http-swagger.md "REST-végpontok hívása a logikai alkalmazásokból"
[http-webhook-doc]: ./connectors-native-webhook.md "Várakozás adott eseményekre HTTP-vagy HTTPS-végpontok esetén"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Logikai alkalmazások futtatása ütemterv alapján"
[schedule-delay-doc]: ./connectors-native-delay.md "A következő művelet futásának késleltetése"
[schedule-delay-until-doc]: ./connectors-native-delay.md "A következő művelet futásának késleltetése"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Logikai alkalmazások futtatása ismétlődő ütemterv szerint"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Olyan logikai alkalmazások futtatása, amelyeknek a folytonos adattömbökben kell kezelnie az adatkezelést"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Csoportokba rendezheti a műveleteket, amelyek a csoport műveleteinek befejezése után kapják meg a saját állapotukat"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Olyan esetekre szervezheti a műveleteket, amelyek egyedi értékeket rendelnek hozzájuk. Csak azt az esetet futtassa, amelynek értéke megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Aktívan futó munkafolyamatok leállítása vagy megszakítása a logikai alkalmazáshoz"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Adatműveletek végrehajtása, például tömbök szűrése vagy CSV-és HTML-táblázatok létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek végrehajtása változókkal, például inicializálás, beállítás, növekmény, csökkentés és Hozzáfűzés karakterlánc-vagy Array-változóhoz"

<!--Managed connector doc links-->
[azure-automation-doc]: https://docs.microsoft.com/connectors/azureautomation/ "Automatizálási feladatok létrehozása és kezelése a felhő és a helyszíni infrastruktúra számára"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Kapcsolódás a Azure Cosmos DBhoz a dokumentumok és tárolt eljárások eléréséhez"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a harmadik féltől származó erőforrások változnak"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Kapcsolódjon az Azure Event Hubshoz, hogy fogadja és küldje el az eseményeket a Logic apps és a Event Hubs között"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Kapcsolódjon az Azure Storage-fiókjához, hogy fájlokat lehessen létrehozni, frissíteni, beolvasni és törölni"
[azure-key-vault-doc]: https://docs.microsoft.com/connectors/keyvault/ "Kapcsolódjon a Azure Key Vaulthoz, hogy kezelni tudja a titkait és kulcsait"
[azure-monitor-logs-doc]: https://docs.microsoft.com/connectors/azuremonitorlogs/ "Lekérdezések futtatása Azure Monitor naplókon Log Analytics-munkaterületeken és Application Insights-összetevőkön"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Kapcsolódjon az Azure Storage-fiókjához, hogy várólistákat és üzeneteket lehessen létrehozni és kezelni"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "A Azure SQL Data Warehouse csatlakozhat, így megtekintheti az adatait"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Kapcsolódjon az Azure Storage-fiókjához, így táblákat hozhat létre, frissíthet és lekérdezéseket végezhet"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Kapcsolódjon a BizTalk Serverhoz, hogy a BizTalk-alapú alkalmazások párhuzamosan futtathatók legyenek Azure Logic Apps"
[box-doc]: ./connectors-create-api-box.md "Kapcsolódjon a mezőhöz. A fájlok feltöltése, beolvasása, törlése, listázása és egyebek"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Kapcsolódjon a Dropboxhoz. A fájlok feltöltése, beolvasása, törlése, listázása és egyebek"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Kapcsolódjon a Dynamics CRM Online-hoz, hogy működjön a CRM Online-beli adatkezeléssel"
[facebook-doc]: ./connectors-create-api-facebook.md "Kapcsolódjon a Facebookhoz. Közzététel egy idővonalon, egy oldal hírcsatornájának beolvasása és egyebek"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Kapcsolódik a Google naptárhoz, és kezelheti a naptárat"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Csatlakozhat a GoogleDrive, hogy működjön az adataival"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozhat a Google táblázatokhoz, így módosíthatja a lapokat"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "A Google-feladatokhoz kapcsolódik, hogy kezelni tudja a feladatokat"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Kapcsolódás 3270-alkalmazásokhoz IBM-es nagyszámítógépeken"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Kapcsolódjon az IBM DB2-hez a felhőben vagy a helyszínen. Sor frissítése, tábla beszerzése és egyebek"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Kapcsolódjon az Informix-hez a felhőben vagy a helyszínen. Egy sor beolvasása, a táblák listázása és egyebek"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Kapcsolódás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldéséhez és fogadásához"
[instagram-doc]: ./connectors-create-api-instagram.md "Kapcsolódjon a Instagram. Események elindítása vagy művelet"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Kapcsolódjon a MailChimp-fiókjához. Levelezések kezelése és automatizálása"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Kapcsolódjon a helyszíni MySQL-adatbázishoz, hogy el tudja olvasni és írni az adatait"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Kapcsolódjon az Office 365-fiókjához, így e-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit, és így tovább"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Csatlakozhat a személyes Microsoft-OneDrive, így feltöltheti, törölheti és listázhatja a fájlokat"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Kapcsolódjon vállalata Microsoft-OneDrive, így feltöltheti, törölheti és listázhatja a fájlokat, és így tovább"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, így hozzáadhat, beszúrhat, törölhet sorokat és egyéb műveleteket is"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Kapcsolódjon az Outlook-postaládához, így kezelheti az e-maileket, a naptárakat, a névjegyeket és egyebeket"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Kapcsolódjon a PostgreSQL-adatbázishoz, hogy beolvassa az adatait a táblákból"
[project-online-doc]: ./connectors-create-api-projectonline.md "Kapcsolódjon a Microsoft Project online-hoz, hogy kezelje projektjeit, feladatait, erőforrásait és egyéb szolgáltatásait"
[rss-doc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és beolvasása, aktiválási műveletek, ha új elem kerül közzétételre egy RSS-hírcsatornán"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Kapcsolódjon a Salesforce-fiókjához. Fiókok, érdeklődők, lehetőségek és egyebek kezelése"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Kapcsolódjon a SendGrid. E-mail küldése és a címzettek jegyzékeinek kezelése"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Csatlakozzon az SFTP-fiókjához az SSH használatával. Fájlok feltöltése, beolvasása, törlése és egyebek"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Kapcsolódjon a helyszíni SharePoint-kiszolgálóhoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Kapcsolódjon a SharePoint Online-hoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[slack-doc]: ./connectors-create-api-slack.md "Kapcsolódás a Slackhez és üzenetek közzététele a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Kapcsolódás SMTP-kiszolgálóhoz és mellékletek küldése e-mailben"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Kapcsolódjon Azure SQL Databasehoz vagy SQL Serverhoz. SQL Database-táblázat bejegyzéseinek létrehozása, frissítése, beolvasása és törlése"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Kapcsolódás a Teradata-adatbázishoz a táblák adatainak beolvasásához"
[trello-doc]: ./connectors-create-api-trello.md "Kapcsolódjon a Trello. Kezelheti projektjeit, és bármit megszervezheti bárkivel"
[twilio-doc]: ./connectors-create-api-twilio.md "Kapcsolódjon a Twilio. Üzenetek küldése és lekérése, rendelkezésre álló számok beolvasása, a bejövő telefonszámok kezelése és egyebek"
[twitter-doc]: ./connectors-create-api-twitter.md "Kapcsolódjon a Twitterhez. Ütemtervek, tweetek és egyéb bejegyzések beolvasása"
[yammer-doc]: ./connectors-create-api-yammer.md "Kapcsolódjon a Yammer. Üzenetek közzététele, új üzenetek beolvasása és egyebek"
[youtube-doc]: ./connectors-create-api-youtube.md "Kapcsolódjon a YouTube-hoz. Videók és csatornák kezelése"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Az AS2 protokollt használó üzenetek kódolása és dekódolása"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Az EDIFACT protokollt használó üzenetek kódolása és dekódolása"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Az EDIFACT protokollt használó üzenetek dekódolása"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT protokollt használó üzenetek kódolása"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyszerű fájlról"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Tudnivalók a vállalati integrációs egyszerű fájlról"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Az integrációs fiók összetevőihez tartozó metaadatok kezelése"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "A JSON átalakítása likvid sablonokkal"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Az X12 protokollt használó üzenetek kódolása és dekódolása"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Az X12 protokollt használó üzenetek dekódolása"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 protokollt használó üzenetek kódolása"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-üzenetek átalakítása"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-üzenetek ellenőrzése"

