---
title: Az Azure Logic Apps összekötői
description: A Azure Logic Apps összekötőit tartalmazó munkafolyamatok automatizálása, beleértve a beépített, felügyelt, helyszíni, integrációs fiókot és vállalati összekötőket
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 642eef8b804a7b71ec16f5834e4989cb7299c408
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789964"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Az összekötők gyors hozzáférést biztosítanak az Azure Logic Appsből más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok eseményeihez, adataihoz és műveleteihez. A logikai alkalmazások összekötőivel kibővítheti a felhőbeli és a helyszíni alkalmazások képességeit annak érdekében, hogy feladatokat hajthassanak végre a létrehozott és már meglévő adatokkal.

Míg Logic Apps [több száz összekötőt](https://docs.microsoft.com/connectors)kínál, ez a cikk a népszerű és leggyakrabban használt összekötőket ismerteti, amelyeket több ezer alkalmazás és több millió végrehajtás is használ az adatok és információk feldolgozásához. Az összekötők és az egyes összekötők (például triggerek, műveletek és korlátok) teljes listájának megkereséséhez tekintse át az összekötők hivatkozási lapjait az [Összekötők áttekintése](https://docs.microsoft.com/connectors)területen. További információ az [Eseményindítók és műveletek](#triggers-actions), a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md)és a [Logic apps díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Ha olyan szolgáltatással vagy API-val szeretne integrálni, amely nem rendelkezik összekötővel, közvetlenül is meghívhatja a szolgáltatást egy protokollon keresztül, például HTTP-n keresztül, vagy létrehozhat egy [Egyéni összekötőt](#custom).

Az összekötők beépített eseményindítók és műveletek vagy felügyelt összekötők számára érhetők el:

<a name="built-in"></a>

* [**Beépített modulok**](#built-ins): ezek a beépített eseményindítók és műveletek "natív" Azure Logic apps, és segítenek az egyéni ütemterveken futó logikai alkalmazások létrehozásában, a más végpontokkal való kommunikációban, a kérelmek fogadásában és megválaszolásában, valamint az Azure functions, az Azure API Apps (Web Apps) és a saját API-k által felügyelt és közzétett Azure-beli API Management és a kérelmeket fogadó beágyazott logikai alkalmazásokban Olyan beépített műveleteket is használhat, amelyek segítségével rendszerezheti és szabályozhatja a logikai alkalmazás munkafolyamatát, és dolgozhat az adataival is.

  > [!NOTE]
  > Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) belüli logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz.
  > Ha ISE-t használ, beépített eseményindítók és műveletek jelennek meg, **amelyek az alapcímkét** a logikai alkalmazásokkal megegyező ISE-ben futtatják. A Logic apps, a beépített eseményindítók és a beépített műveletek, amelyek az ISE-ben futnak, a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak.
  >
  > A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > További információ a díjszabásról: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Felügyelt összekötők**: a Microsoft által központilag telepített és felügyelt, ezek az összekötők a Cloud Services, a helyszíni rendszerek vagy mindkettő, például az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és egyéb szolgáltatások elérésére szolgáló eseményindítókat és műveleteket biztosítanak. Egyes összekötők kifejezetten támogatják a vállalatközi (B2B) kommunikációs forgatókönyveket, és megkövetelik a logikai alkalmazáshoz csatolt [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bizonyos összekötők használata előtt előfordulhat, hogy először a Azure Logic Apps által felügyelt kapcsolatokat kell létrehoznia. 

  Ha például Microsoft-BizTalk Server használ, a logikai alkalmazások a [BizTalk Server helyszíni összekötő](#on-premises-connectors)használatával csatlakozhatnak a BizTalk Serverhoz, és kommunikálhatnak velük. 
  Ezután az [integrációs fiók összekötői](#integration-account-connectors)használatával kiterjesztheti vagy elvégezheti a BizTalk-szerű műveleteket a logikai alkalmazásokban.

  Az összekötők a standard vagy a vállalat kategóriába tartoznak. 
  A [vállalati összekötők](#enterprise-connectors) felár ellenében hozzáférést biztosítanak a nagyvállalati rendszerekhez, például az SAP, az IBM MQ és az IBM 3270 szolgáltatásokhoz. Annak megállapításához, hogy az összekötő szabványos vagy nagyvállalati szintű-e, tekintse meg a technikai részleteket az egyes összekötők hivatkozásait ismertető oldalon az [Összekötők áttekintésében](https://docs.microsoft.com/connectors). 

  Az összekötőket ezen kategóriák használatával is azonosíthatja, bár egyes összekötők több kategóriát is használhatnak. 
  Például az SAP egy vállalati összekötő és egy helyszíni összekötő:

  |   |   |
  |---|---|
  | [**Felügyelt API-összekötők**](#managed-api-connectors) | Az Azure Blob Storage, az Office 365, a Dynamics, a Power BI, a OneDrive, a Salesforce, a SharePoint Online és sok más szolgáltatást használó logikai alkalmazásokat hozhat létre. |
  | [**Helyszíni összekötők**](#on-premises-connectors) | A helyszíni [adatátjáró][gateway-doc]telepítése és beállítása után ezek az összekötők segítenek a logikai alkalmazásoknak olyan helyszíni rendszerek elérésében, mint a SQL Server, a SharePoint Server, a Oracle db, a fájlmegosztás és egyebek. |
  | [**Integrációs fiók összekötői**](#integration-account-connectors) | Akkor érhető el, ha integrációs fiókot hoz létre és fizet ki, ezek az összekötők átalakítja és érvényesíti az XML-t, kódolja és dekódolja a lapos fájlokat, és feldolgozza a vállalatközi (B2B) üzeneteket az AS2, a EDIFACT és a X12 protokollal. |
  |||

  > [!NOTE]
  > Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) belüli logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz. Ha ISE-, standard-és vállalati összekötőket használ, amelyek megjelenítik az **ISE** -címkét, akkor a logikai alkalmazásokkal megegyező ISE-ben futnak. Az ISE-címkét nem megjelenítő összekötők a globális Logic Apps szolgáltatásban futnak.
  >
  > Az Azure-beli virtuális hálózathoz csatlakozó helyszíni rendszerek esetében adja meg az ISE-t a hálózatba, hogy a logikai alkalmazások közvetlenül hozzáférhessenek ezekhez a rendszerekhez egy **ISE** -címkével, egy http-művelettel vagy egy [Egyéni összekötővel](#custom)rendelkező összekötő használatával. Az ISE-ben futó Logic apps és összekötők a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak. 
  >
  > A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > További információ a díjszabásról: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md).

  Az összekötők teljes listáját, valamint az egyes összekötők hivatkozási információit, például a műveleteket és az eseményindítókat, amelyek a OpenAPI (korábban hencegés) leírásában vannak megadva, valamint az összes korlátot, a teljes listát az [Összekötők áttekintése](/connectors/)alatt találja. A díjszabással kapcsolatos információkért tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md), és [Logic apps a díjszabás részleteit](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Beépített modulok

A Logic Apps beépített eseményindítókat és műveleteket biztosít, így ütemezhetők-alapú munkafolyamatokat hozhat létre, segítséget nyújthat a logikai alkalmazásoknak más alkalmazásokkal és szolgáltatásokkal való kommunikációhoz, a munkafolyamat vezérléséhez a logikai alkalmazásokon keresztül, valamint az adatkezeléshez és az adatkezeléshez.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][schedule-icon]<br/>- **ütemterv**][recurrence-doc] | – A logikai alkalmazás futtatása egy adott időpontra, az alapszintű és a összetett ismétlődések között, az **ismétlődési** eseményindítóval. <p>– Szüneteltetheti a logikai alkalmazást egy adott időtartamra a **késleltetési** művelettel. <p>– Szüneteltetheti a logikai alkalmazást, amíg a művelet **nem** lesz a megadott dátummal és időponttal. | [![API-ikon][http-icon]<br/>**http**][http-doc] | Kommunikáció bármely végponttal HTTP-n keresztül, a http, a HTTP + a hencegés és a HTTP + webhook esetében egyaránt. | 
| [![API-ikon][http-request-icon]<br/>**kérelem**][http-request-doc] | – Meghívhatja a logikai alkalmazást más alkalmazásokból vagy szolgáltatásokból, kiválthatja Event Grid erőforrás-eseményeit, vagy aktiválhatja a válaszokat a **kérések** triggerével Azure Security Center riasztásokra. <p>– Válaszok küldése egy alkalmazásnak vagy szolgáltatásnak a **Válasz** művelettel. | [![API-ikon][batch-icon]<br/>**Batch**][batch-doc] | – A Batch **üzenetek** triggerrel rendelkező kötegekben lévő üzenetek feldolgozása. <p>– Olyan logikai alkalmazásokat hívhat meg, amelyek meglévő batch-eseményindítókkal rendelkeznek az **üzenetek küldése kötegelt** művelettel. | 
| [![API-ikon][azure-functions-icon]<br/>**Azure functions**][azure-functions-doc] | Hívja meg az Azure functions szolgáltatást, amely egyéniC# kódrészleteket (vagy Node. js-t) futtat a logikai alkalmazásokból. | [![API-ikon][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Meghívja a saját API-k által definiált eseményindítókat és műveleteket, amelyeket az Azure API Management felügyel és tesz közzé. | 
| [![API-ikon][azure-app-services-icon]<br/>**Azure app Services**][azure-app-services-doc] | Hívja meg az Azure API Apps vagy Web Appst, amely Azure App Serviceon üzemel. Az alkalmazások által definiált eseményindítók és műveletek úgy jelennek meg, mint bármely más, az első osztályú eseményindító és művelet, amikor a kivágás bekerül. | [![API-ikon][azure-logic-apps-icon]<br/>**Azure<br/>Logic apps**][nested-logic-app-doc] | Meghívhat más Logic apps-alkalmazásokat, amelyek egy kérelem-triggerrel kezdődnek. | 
||||| 

### <a name="control-workflow"></a>Vezérlési munkafolyamat

A Logic Apps beépített műveleteket biztosít a logikai alkalmazás munkafolyamatában lévő műveletek rendszerezéséhez és szabályozásához:

|   |   |   |   | 
|---|---|---|---| 
| [![beépített ikon][condition-icon]<br/>**feltétel**][condition-doc] | Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis. | [![beépített **ikon][for-each-icon]</br>** ][for-each-doc] | Hajtsa végre ugyanezeket a műveleteket egy tömb minden elemén. | 
| [![beépített ikon][scope-icon]<br/>**hatókör**][scope-doc] | A *műveletek hatókörbe*való csoportosítása, amelyek a hatókör befejezésének befejezése után kapják meg a saját állapotukat. | [![beépített ikon][switch-icon]</br>**kapcsoló**][switch-doc] | Olyan *esetekre*csoportosíthat műveleteket, amelyek az alapértelmezett eset kivételével egyedi értékeket kapnak. Csak akkor futtassa ezt az esetet, ha a hozzárendelt érték megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet. | 
| [![beépített ikon][terminate-icon]<br/>**megszakítása**][terminate-doc] | Egy aktívan futó logikai alkalmazás munkafolyamatának leállítása. | [![beépített **ikon][until-icon]<br/>a**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Az adatkezelés és-kezelés

A Logic Apps beépített műveleteket biztosít az adatkimenetek és azok formátumának kezeléséhez:  

|   |   | 
|---|---| 
| [![beépített ikon][data-operations-icon]<br/>**adatműveletek**][data-operations-doc] | Műveletek végrehajtása adatokkal: <p>- **összeállítás**: egyetlen kimenet létrehozása több bemenetből különböző típusokkal. <br>- **CSV-táblázat létrehozása**: hozzon létre egy vesszővel tagolt (CSV-) táblázatot egy olyan tömbből, amely JSON-objektumokkal rendelkezik. <br>- **HTML-táblázat létrehozása**: HTML-táblázat létrehozása egy JSON-objektumokkal rendelkező tömbből. <br>- **Filter Array**: hozzon létre egy tömböt egy másik tömb elemeiből, amelyek megfelelnek a feltételeknek. <br>- **Csatlakozás**: hozzon létre egy karakterláncot egy tömb összes eleméről, és válassza el ezeket az elemeket a megadott elválasztóval. <br>- **JSON-elemzés**: hozzon létre felhasználóbarát tokeneket a tulajdonságok és azok értékei a JSON-tartalomban, hogy ezeket a tulajdonságokat használhatja a munkafolyamatban. <br>- **Select**: hozzon létre egy tömböt JSON-objektumokkal egy másik tömb elemeinek vagy értékeinek átalakításával, és rendelje hozzá ezeket az elemeket a megadott tulajdonságokhoz. | 
| ![beépített ikon][date-time-icon]<br/>**Dátum és idő** | Műveletek elvégzése időbélyegekkel: <p>- **Hozzáadás az időponthoz**: adja hozzá a megadott számú egységet egy időbélyeghez. <br>- **időzóna konvertálása**: a forrás időzóna időbélyegének konvertálása a cél időzónára. <br>- **aktuális idő**: az aktuális időbélyeg visszaadása karakterláncként. <br>- a **jövőbeli időt**: az aktuális időbélyeg és a megadott időegységek visszaadása. <br>- a **múltbeli időpontig**: az aktuális időbélyeg visszaadása a megadott időegységek mínusz. <br>- **kivonása az időpontból**: több időegység kivonása egy időbélyegből. |
| [Beépített ikon][variables-icon]<br/>**változók** ![][variables-doc] | Műveletek végrehajtása változókkal: <p>- **Hozzáfűzés a tömb változóhoz**: szúrjon be egy értéket egy változó által tárolt tömb utolsó elemeként. <br>- **Hozzáfűzés a karakterlánc-változóhoz**: szúrjon be egy értéket egy változó által tárolt sztring utolsó karakterének értékére. <br>- **változó csökkentése**: egy változót konstans értékkel csökkentheti. <br>- **növekmény változó**: egy változót konstans értékkel növelheti. <br>- **inicializálási változó**: hozzon létre egy változót, és állapítsa meg az adattípus és a kezdeti érték megadását. <br>- **set változó**: adjon meg egy másik értéket egy meglévő változóhoz. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Felügyelt API-összekötők

A Logic Apps ezeket a népszerű szabványos összekötőket biztosítja a feladatok, folyamatok és munkafolyamatok automatizálásához ezekkel a szolgáltatásokkal vagy rendszerekkel.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet. | [![API-ikon][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Kapcsolódjon SQL Server a felhőben, vagy egy Azure SQL Database a felhőben, így kezelheti a rekordokat, futtathatja a tárolt eljárásokat vagy lekérdezéseket végezhet. | 
| [![API-ikon][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Kapcsolódjon az Office 365 e-mail-fiókjához, így e-maileket, feladatokat, naptári eseményeket és értekezleteket, névjegyeket, kéréseket és egyéb műveleteket hozhat létre és kezelhet. | [![API-ikon][azure-blob-storage-icon]<br/>**Azure Blob<br/>Storage**][azure-blob-storage-doc] | Kapcsolódjon a Storage-fiókjához, hogy blob-tartalmakat hozzon létre és kezeljen. | 
| [![API-ikon][sftp-icon]<br/>**SFTP**][sftp-doc] | Csatlakozhat az interneten elérhető SFTP-kiszolgálókhoz, és elérheti az azokon lévő mappákat és fájlokat. | [![API-ikon][sharepoint-online-icon]<br/>**SharePoint<br/>online**][sharepoint-online-doc] | Kapcsolódjon a SharePoint Online-hoz, így kezelheti a fájlokat, a mellékleteket, a mappákat és egyebeket. | 
| [![API-ikon][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Kapcsolódjon a Dynamics 365-fiókjához, hogy rekordokat, elemeket és egyebeket hozzon létre és kezeljen. | [![API-ikon][ftp-icon]<br/>**FTP**][ftp-doc] | Csatlakozhat az internetről elérhető FTP-kiszolgálókhoz, így a fájlokkal és mappákkal is dolgozhat. | 
| [![API-ikon][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Kapcsolódjon a Salesforce-fiókhoz, így olyan elemeket hozhat létre és kezelhet, mint a rekordok, a feladatok, az objektumok stb. | [![API-ikon][twitter-icon]<br/>**Twitteren**][twitter-doc] | Csatlakozhat a Twitter-fiókjához, így Ön kezelheti a tweeteket, a követőiket, az idővonalat és egyéb műveleteket is. Mentse a tweeteket az SQL, az Excel vagy a SharePoint szolgáltatásba. | 
| [![API-ikon][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Események felhasználása és közzététele az Event hub használatával. Például lekérheti a logikai alkalmazás kimenetét Event Hubs, majd elküldheti a kimenetet egy valós idejű elemzési szolgáltatónak. | [![API-ikon][azure-event-grid-icon]<br/>**Azure Event**</br>**Grid**][azure-event-grid-doc] | Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a külső gyártók erőforrásai változnak. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők 

Íme néhány általánosan használt standard összekötő, amelyet Logic Apps biztosít a helyszíni rendszerekben tárolt adatok és erőforrások eléréséhez. A helyszíni rendszerhez való kapcsolódás előtt először [le kell töltenie, telepítenie és be kell állítania egy helyszíni adatátjárót][gateway-doc]. Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy be kellene állítania a szükséges hálózati infrastruktúrát. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Kiszolgáló** | [![API-ikon][file-system-icon]<br/>**fájl</br> System**][file-system-doc] | [![API-ikon][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API-ikon][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-ikon][oracle-db-icon]<br/>**Oracle db**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-ikon][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![API-ikon][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

A Logic Apps szabványos összekötőket biztosít a vállalatközi (B2B) megoldások létrehozásához és az Azure-beli Enterprise Integration Pack (EIP) keresztül elérhető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)kifizetéséhez. Ezzel a fiókkal B2B-összetevőket hozhat létre és tárolhat, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat stb. Ezeknek az összetevőknek a használatához társítsa a logikai alkalmazásokat az integrációs fiókjával. Ha jelenleg BizTalk Server használ, előfordulhat, hogy ezek az összekötők már ismerősnek tűnnek.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][as2-icon]<br/>**AS2</br> dekódolás**][as2-doc] | [![API-ikon][as2-icon]<br/>**AS2</br> Encoding**][as2-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> dekódolás**][edifact-decode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> kódolás**][edifact-encode-doc] | 
| [![API-ikon][flat-file-decode-icon]<br/>**Flat file</br> dekódolás**][flat-file-decode-doc] | [![API-ikon][flat-file-encode-icon]<br/>a **sima fájl</br> Encoding**][flat-file-encode-doc] | [![API-ikon][integration-account-icon]<br/>**integrációs<br/>fiók**][integration-account-doc] | [![API ikon][liquid-icon]<br/>**folyékony**</br>**átalakítások**][json-liquid-transform-doc] | 
| [![API-ikon][x12-icon]<br/>**X12</br> dekódolás**][x12-decode-doc] | [![API-ikon][x12-icon]<br/>**X12</br> kódolás**][x12-encode-doc] | [![API-ikon][xml-transform-icon]<br/>**XML-** </br>**átalakítások**][xml-transform-doc] | [![API-ikon][xml-validate-icon]<br/>**XML-<br/>érvényesítése**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A Logic Apps a nagyvállalati rendszerekhez (például SAP és IBM MQ) való hozzáféréshez biztosít ilyen vállalati összekötőket:

|   |   |   | 
|---|---|---| 
| [![API-ikon][ibm-3270-icon]<br/>**IBM 3270**][ibm-3270-doc] | [![API-ikon][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![API-ikon][sap-icon]<br/>**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Triggerek és műveletek – további információ

Az összekötők *eseményindítókat*, *műveleteket*vagy mindkettőt adhatnak meg. Az *eseményindító* a logikai alkalmazások első lépése, amely általában megadja az eseményindítót kiváltó eseményt, és elindítja a logikai alkalmazás futtatását. Például az FTP-összekötő olyan triggerrel rendelkezik, amely elindítja a logikai alkalmazást "fájl hozzáadásakor vagy módosításakor". Egyes eseményindítók rendszeresen megkeresik a megadott eseményt vagy az adattípust, majd a megadott esemény vagy az adatgyűjtés észlelése után tüzet fognak. Más eseményindítók várnak, de azonnal megtörténnek egy adott esemény bekövetkezésekor, vagy amikor új adatkapcsolat áll rendelkezésre. Az eseményindítók a logikai alkalmazáshoz is továbbítanak minden szükséges adattal. A logikai alkalmazás az összes munkafolyamatban képes olvasni és felhasználni ezeket az adatait.
Például a Twitter-összekötő egy triggerrel rendelkezik, "új Tweet közzétételekor", amely továbbítja a tweet tartalmát a logikai alkalmazás munkafolyamataiba. 

Egy eseményindító elindítása után Azure Logic Apps létrehozza a logikai alkalmazás egy példányát, és elindítja a logikai alkalmazás munkafolyamatában lévő *műveleteket* . A műveletek a logikai alkalmazás munkafolyamataiban a triggert követő és a feladatokat elvégző lépések. Létrehozhat például egy olyan logikai alkalmazást, amely egy SQL-adatbázisból szerzi be a vásárlói adatait, és feldolgozza azokat a későbbi műveletekben. 

A Azure Logic Apps az alábbi általános típusú eseményindítókat biztosítja:

* *Ismétlődési eseményindító*: ez az eseményindító megadott ütemezésen fut, és nincs szorosan társítva egy adott szolgáltatáshoz vagy rendszerhez.

* *Lekérdezési eseményindító*: ez az eseményindító rendszeresen lekérdezi egy adott szolgáltatást vagy rendszert a megadott ütemterv alapján, új adatellenőrzést végez, vagy adott esemény történt. Ha új adatok állnak rendelkezésre, vagy az adott esemény történt, az eseményindító létrehozza és futtatja a logikai alkalmazás egy új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

* *Leküldéses eseményindító*: ez az eseményindító megvárja az új vagy az esemény megkötését. Ha új adatok állnak rendelkezésre, vagy ha az esemény történik, az eseményindító létrehozza és futtatja a logikai alkalmazás új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

<a name="custom"></a>

## <a name="connector-configuration"></a>Összekötő konfigurációja

Minden összekötő eseményindítói és műveletei saját tulajdonságokat biztosítanak a konfigurálásához. Számos összekötő azt is megköveteli, hogy először létre kell hoznia egy *kapcsolódást* a cél szolgáltatáshoz vagy a rendszerhez, és meg kell adnia a hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt a logikai alkalmazásban eseményindítót vagy műveletet alkalmazhat. Az adatok eléréséhez vagy az Ön nevében történő közzétételhez például engedélyeznie kell egy Twitter-fiókhoz való kapcsolódást. 

Azure Active Directory (Azure AD) OAuth használó összekötők esetén a kapcsolat létrehozása a szolgáltatásba való bejelentkezést jelenti, például az Office 365, a Salesforce vagy a GitHub szolgáltatást, ahol a hozzáférési token [titkosított](../security/fundamentals/encryption-overview.md) , és biztonságosan tárolható egy Azure titkos tárolóban. Más összekötők, például az FTP és az SQL esetében olyan kapcsolatra van szükség, amely konfigurációs adatokat tartalmaz, például a kiszolgáló címe, a Felhasználónév és a jelszó. Ezeket a kapcsolati konfigurációs adatokat is titkosítja és biztonságosan tárolja. További információ az [Azure-beli titkosításról](../security/fundamentals/encryption-overview.md).

A kapcsolatok a szolgáltatás vagy a rendszer engedélyezése esetén is hozzáférhetnek a cél szolgáltatáshoz vagy rendszerhez. Az Azure AD OAuth-kapcsolatokat használó szolgáltatások, például az Office 365 és a Dynamics esetében Azure Logic Apps határozatlan időre frissítik a hozzáférési jogkivonatokat. Előfordulhat, hogy az egyéb szolgáltatások korlátozva vannak, hogy Azure Logic Apps a tokent frissítés nélkül is használhatja. Bizonyos műveletek általában érvénytelenítik az összes hozzáférési jogkivonatot, például a jelszó módosítását.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Az egyéni kódot futtató vagy összekötőként nem elérhető API-k meghívásához [egyéni API apps létrehozásával](../logic-apps/logic-apps-create-api-app.md)kiterjesztheti a Logic apps platformot. [Létrehozhat egyéni összekötőket](../logic-apps/custom-connector-overview.md) is *bármilyen* Rest-vagy SOAP-alapú API-khoz, amelyek elérhetővé teszik az API-kat az Azure-előfizetésében található logikai alkalmazások számára.
Ahhoz, hogy az egyéni API Apps vagy összekötők nyilvánosak legyenek az Azure-ban mindenki számára, a [Microsoft minősítéshez összekötőket küldhet](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) belüli logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz.
> Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.
>
> Az ISE-n belül létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető Azure-beli virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.
>
> A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Következő lépések

* Az [Összekötők teljes listájának](https://docs.microsoft.com/connectors) megkeresése
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Egyéni összekötők létrehozása logikai alkalmazásokhoz](https://docs.microsoft.com/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Azure API Management Service-példány létrehozása az API-k kezeléséhez és közzétételéhez"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai appok integrálása App Service API Apps-alkalmazásokkal"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Üzenetek feldolgozása csoportokban vagy kötegként"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis"
[delay-doc]: ./connectors-native-delay.md "Késleltetett műveletek végrehajtása"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ugyanazon műveletek végrehajtása egy tömb minden elemén"
[http-doc]: ./connectors-native-http.md "HTTP-hívások indítása HTTP-összekötővel"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-response-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-swagger-doc]: ./connectors-native-http-swagger.md "HTTP-hívások indítása HTTP + Swagger összekötővel"
[http-webook-doc]: ./connectors-native-webhook.md "HTTP-webhook-műveletek és-eseményindítók hozzáadása a logikai alkalmazásokhoz"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ./connectors-native-query.md "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[recurrence-doc]:  ./connectors-native-recurrence.md "Ismétlődő műveletek kiváltása logikai alkalmazásokhoz"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Csoportokba rendezheti a műveleteket, amelyek a csoport műveleteinek befejezése után kapják meg a saját állapotukat" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Olyan esetekre szervezheti a műveleteket, amelyek egyedi értékeket rendelnek hozzájuk. Csak azt az esetet futtassa, amelynek értéke megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Aktívan futó munkafolyamatok leállítása vagy megszakítása a logikai alkalmazáshoz"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Adatműveletek végrehajtása, például tömbök szűrése vagy CSV-és HTML-táblázatok létrehozása"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek végrehajtása változókkal, például inicializálás, beállítás, növekmény, csökkentés és Hozzáfűzés karakterlánc-vagy Array-változóhoz"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a harmadik féltől származó erőforrások változnak"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Kapcsolódjon az Azure Event Hubshoz. Események fogadása és küldése a Logic apps és a Event Hubs között"
[box-doc]: ./connectors-create-api-box.md "Kapcsolódjon a mezőhöz. A fájlok feltöltése, beolvasása, törlése, listázása és egyebek"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Kapcsolódjon a Dropboxhoz. A fájlok feltöltése, beolvasása, törlése, listázása és egyebek"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Csatlakozhat a Dynamics CRM Online szolgáltatáshoz, és használhatja a CRM Online adatait"
[facebook-doc]: ./connectors-create-api-facebook.md "Kapcsolódjon a Facebookhoz. Közzététel egy idővonalon, egy oldal hírcsatornájának beolvasása és egyebek"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "A Google Naptárhoz csatlakozhat, és felügyelheti a naptárat."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Csatlakozhat a Google Drive-hoz, és használhatja annak adatait"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozhat a Google táblázatokhoz, így módosíthatja a lapokat"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Csatlakozhat a Google Teendőkhöz, ahol különböző teendőit kezelheti"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Kapcsolódás 3270-alkalmazásokhoz IBM-es nagyszámítógépeken"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Kapcsolódjon az IBM DB2-hez a felhőben vagy a helyszínen. Sor frissítése, tábla beszerzése és egyebek"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Kapcsolódjon az Informix-hez a felhőben vagy a helyszínen. Egy sor beolvasása, a táblák listázása és egyebek"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Kapcsolódás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldéséhez és fogadásához"
[instagram-doc]: ./connectors-create-api-instagram.md "Kapcsolódjon a Instagram. Események elindítása vagy művelet"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Kapcsolódjon a MailChimp-fiókjához. Levelezések kezelése és automatizálása"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Kapcsolódjon a Microsoft translatorhez. Szöveg fordítása, nyelvek felismerése és további" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Kapcsolódjon az Office 365-fiókjához. E-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Videoadatokat, videólistákat és csatornákat kérhet le, valamint Office 365-videók lejátszási URL-címeit"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Kapcsolódjon a személyes Microsoft-OneDrive. Fájlok feltöltése, törlése, listázása és egyéb adatok"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Kapcsolódjon vállalata Microsoft-OneDrive. A fájlok feltöltése, törlése, listázása és egyebek"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, ahol többek között sorokat adhat hozzá, szúrhat be és törölhet"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Kapcsolódjon az Outlook-postaládához. Kezelheti e-mailjeit, naptárait, névjegyeit és egyéb adatait"
[project-online-doc]: ./connectors-create-api-projectonline.md "Kapcsolódjon a Microsoft Project online-hoz. Kezelheti a projektjeit, feladatait, erőforrásait és egyebeket"
[rss-doc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és lekérése, illetve műveletek kiváltása, ha egy új elemet tesznek közzé egy RSS-hírcsatornán."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Kapcsolódjon a Salesforce-fiókjához. Fiókok, érdeklődők, lehetőségek és egyebek kezelése"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Kapcsolódjon a SendGrid. E-mail küldése és a címzettek jegyzékeinek kezelése"
[sftp-doc]: ./connectors-create-api-sftp.md "Kapcsolódjon az SFTP-fiókjához. Fájlok feltöltése, beolvasása, törlése és egyebek"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Kapcsolódjon a helyszíni SharePoint-kiszolgálóhoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Kapcsolódjon a SharePoint Online-hoz. Dokumentumok, listaelemek és egyéb elemek kezelése"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozhat a Slackhez, és üzeneteket tehet közzé a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Kapcsolódás SMTP-kiszolgálóhoz és mellékletek küldése e-mailben"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Kapcsolódjon Azure SQL Databasehoz vagy SQL Serverhoz. Bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet egy SQL Database-táblázatban."
[trello-doc]: ./connectors-create-api-trello.md "Kapcsolódjon a Trello. Kezelheti projektjeit, és bármit megszervezheti bárkivel"
[twilio-doc]: ./connectors-create-api-twilio.md "Kapcsolódjon a Twilio. Üzenetek küldése és lekérése, rendelkezésre álló számok beolvasása, a bejövő telefonszámok kezelése és egyebek"
[twitter-doc]: ./connectors-create-api-twitter.md "Kapcsolódjon a Twitterhez. Ütemtervek, tweetek és egyéb bejegyzések beolvasása"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Kapcsolódjon a wunderlist. Kezelheti a feladatokat és a feladatlistákat, megtarthatja a szinkronizálást, és így tovább"
[yammer-doc]: ./connectors-create-api-yammer.md "Kapcsolódjon a Yammer. Üzenetek közzététele, új üzenetek beolvasása és egyebek"
[youtube-doc]: ./connectors-create-api-youtube.md "Kapcsolódjon a YouTube-hoz. Videók és csatornák kezelése"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Megismerheti a vállalati integrációs AS2-t."
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Megismerheti a vállalati integrációs EDIFACT-dekódolást."
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Megismerheti a vállalati integrációs EDIFACT-kódolást."
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Sémákat, térképeket, partnereket és egyebeket kereshet ki az integrációs fiókjában"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Megismerheti a Liquiddel végzett JSON-átalakításokat"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Megismerheti a vállalati integrációs X12-t."
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Megismerheti a vállalati integrációs X12-dekódolást."
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Megismerheti a vállalati integrációs X12-kódolást."
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Megismerheti a vállalati integrációs átalakításokat."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Megismerheti a vállalati integrációs XML-hitelesítést."

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
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
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
[sftp-icon]: ./media/apis-list/sftp.png
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
[wunderlist-icon]: ./media/apis-list/wunderlist.png
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
