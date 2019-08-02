---
title: Azure Logic Apps-összekötők
description: A Azure Logic Apps összekötőit tartalmazó munkafolyamatok automatizálása, beleértve a beépített, felügyelt, helyszíni, integrációs fiókot és vállalati összekötőket
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 126c3fb348b1d53769a818ae4b21fcdbbef65615
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517238"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps-összekötők

Az összekötők gyors hozzáférést biztosítanak az Azure Logic Appsből más alkalmazások, szolgáltatások, rendszerek, protokollok és platformok eseményeihez, adataihoz és műveleteihez. A logikai alkalmazások összekötőivel kibővítheti a felhőbeli és a helyszíni alkalmazások képességeit annak érdekében, hogy feladatokat hajthassanak végre a létrehozott és már meglévő adatokkal.

Míg Logic Apps [több száz összekötőt](https://docs.microsoft.com/connectors)kínál, ez a cikk a népszerű és leggyakrabban használt összekötőket ismerteti, amelyeket több ezer alkalmazás és több millió végrehajtás is használ az adatok és információk feldolgozásához. Az összekötők és az egyes összekötők (például triggerek, műveletek és korlátok) teljes listájának megkereséséhez tekintse át az összekötők hivatkozási lapjait az [Összekötők áttekintése](https://docs.microsoft.com/connectors)területen. További információ az eseményindítók [és műveletek](#triggers-actions), a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md)és a [Logic apps díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Ha olyan szolgáltatással vagy API-val szeretne integrálni, amely nem rendelkezik összekötővel, közvetlenül is meghívhatja a szolgáltatást egy protokollon keresztül, például HTTP-n keresztül, vagy létrehozhat egy [Egyéni összekötőt](#custom).

Az összekötők beépített eseményindítók és műveletek vagy felügyelt összekötők számára érhetők el:

<a name="built-in"></a>

* [**Beépített modulok**](#built-ins): Ezek a beépített eseményindítók és műveletek "natív" Azure Logic Apps, és segítenek az egyéni ütemterveken futó logikai alkalmazások létrehozásában, a más végpontokkal való kommunikációban, a kérelmek fogadásában és megválaszolásában, valamint az Azure functions, az Azure API Apps (Web Apps) és a saját API-k meghívásában. felügyelt és közzétett Azure API Management és beágyazott logikai alkalmazások, amelyek fogadhatnak kérelmeket. Olyan beépített műveleteket is használhat, amelyek segítségével rendszerezheti és szabályozhatja a logikai alkalmazás munkafolyamatát, és dolgozhat az adataival is.

  > [!NOTE]
  > Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) belüli logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz.
  > Ha ISE-t használ, beépített eseményindítók és műveletek jelennek meg, amelyek az **alapcímkét** a logikai alkalmazásokkal megegyező ISE-ben futtatják. A Logic apps, a beépített eseményindítók és a beépített műveletek, amelyek az ISE-ben futnak, a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak.
  >
  > A ISEs létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > További információ a díjszabásról: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Felügyelt összekötők**: A Microsoft által központilag telepített és felügyelt, ezek az összekötők a Cloud Services, a helyszíni rendszerek vagy mindkettő, például az Office 365, az Azure Blob Storage, a SQL Server, a Dynamics, a Salesforce, a SharePoint és más szolgáltatások elérésére szolgáló eseményindítókat és műveleteket biztosítanak. Egyes összekötők kifejezetten támogatják a vállalatközi (B2B) kommunikációs forgatókönyveket, és megkövetelik a logikai alkalmazáshoz csatolt [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bizonyos összekötők használata előtt előfordulhat, hogy először a Azure Logic Apps által felügyelt kapcsolatokat kell létrehoznia. 

  Ha például Microsoft-BizTalk Server használ, a logikai alkalmazások a [BizTalk Server helyszíni összekötő](#on-premises-connectors)használatával csatlakozhatnak a BizTalk Serverhoz, és kommunikálhatnak velük. 
  Ezután az [integrációs fiók összekötői](#integration-account-connectors)használatával kiterjesztheti vagy elvégezheti a BizTalk-szerű műveleteket a logikai alkalmazásokban.

  Az összekötők a standard vagy a vállalat kategóriába tartoznak. 
  A [vállalati összekötők](#enterprise-connectors) felár ellenében hozzáférést biztosítanak a nagyvállalati rendszerekhez, például az SAP, az IBM MQ és az IBM 3270 szolgáltatásokhoz. Annak megállapításához, hogy az összekötő szabványos vagy nagyvállalati szintű-e, tekintse meg a technikai részleteket az egyes összekötők hivatkozásait [ismertető](https://docs.microsoft.com/connectors)oldalon az összekötők áttekintésében. 

  Az összekötőket ezen kategóriák használatával is azonosíthatja, bár egyes összekötők több kategóriát is használhatnak. 
  Például az SAP egy vállalati összekötő és egy helyszíni összekötő:

  |   |   |
  |---|---|
  | [**Felügyelt API-összekötők**](#managed-api-connectors) | Az Azure Blob Storage, az Office 365, a Dynamics, a Power BI, a OneDrive, a Salesforce, a SharePoint Online és sok más szolgáltatást használó logikai alkalmazásokat hozhat létre. |
  | [**Helyszíni összekötők**](#on-premises-connectors) | A helyszíni adatátjáró telepítése és beállítása [][gateway-doc]után ezek az összekötők segítenek a logikai alkalmazásoknak olyan helyszíni rendszerek elérésében, mint a SQL Server, a SharePoint Server, a Oracle db, a fájlmegosztás és egyebek. |
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
| [![API-][schedule-icon]<br/>ikon**ütemterve**][recurrence-doc] | – A logikai alkalmazás futtatása egy adott időpontra, az alapszintű és a összetett ismétlődések között , az ismétlődési eseményindítóval. <p>– Szüneteltetheti a logikai alkalmazást egy adott időtartamra a **késleltetési** művelettel. <p>– Szüneteltetheti a logikai alkalmazást, amíg a művelet **nem** lesz a megadott dátummal és időponttal. | [![API-][http-icon]<br/>ikon**http**][http-doc] | Kommunikáció bármely végponttal HTTP-n keresztül, a http, a HTTP + a hencegés és a HTTP + webhook esetében egyaránt. | 
| [![API-][http-request-icon]<br/>ikon**kérése**][http-request-doc] | – Meghívhatja a logikai alkalmazást más alkalmazásokból vagy szolgáltatásokból, kiválthatja Event Grid erőforrás-eseményeit, vagy aktiválhatja a válaszokat a kérések triggerével Azure Security Center riasztásokra. <p>– Válaszok küldése egy alkalmazásnak vagy szolgáltatásnak a **Válasz** művelettel. | [![API-][batch-icon]<br/>ikon**köteg**][batch-doc] | – A Batch **üzenetek** triggerrel rendelkező kötegekben lévő üzenetek feldolgozása. <p>– Olyan logikai alkalmazásokat hívhat meg, amelyek meglévő batch-eseményindítókkal rendelkeznek az **üzenetek küldése kötegelt** művelettel. | 
| [![API-][azure-functions-icon]<br/>ikon**Azure functions**][azure-functions-doc] | Hívja meg az Azure functions szolgáltatást, amely egyéniC# kódrészleteket (vagy Node. js-t) futtat a logikai alkalmazásokból. | [![API-][azure-api-management-icon]</br>ikon**Azure API Management**][azure-api-management-doc] | Meghívja a saját API-k által definiált eseményindítókat és műveleteket, amelyeket az Azure API Management felügyel és tesz közzé. | 
| [![API-][azure-app-services-icon]<br/>ikon**Azure app Services**][azure-app-services-doc] | Hívja meg az Azure API Apps vagy Web Appst, amely Azure App Serviceon üzemel. Az alkalmazások által definiált eseményindítók és műveletek úgy jelennek meg, mint bármely más, az első osztályú eseményindító és művelet, amikor a kivágás bekerül. | [![API-][azure-logic-apps-icon]<br/>ikon**Azure<br/>Logic apps**][nested-logic-app-doc] | Meghívhat más Logic apps-alkalmazásokat, amelyek egy kérelem-triggerrel kezdődnek. | 
||||| 

### <a name="control-workflow"></a>Vezérlési munkafolyamat

A Logic Apps beépített műveleteket biztosít a logikai alkalmazás munkafolyamatában lévő műveletek rendszerezéséhez és szabályozásához:

|   |   |   |   | 
|---|---|---|---| 
| [![Beépített ikon][condition-icon]<br/>feltétele][condition-doc] | Értékelje ki a feltételt, és futtasson különböző műveleteket attól függően, hogy a feltétel igaz vagy hamis. | [![Beépített][for-each-icon]</br>**ikon**][for-each-doc] | Hajtsa végre ugyanezeket a műveleteket egy tömb minden elemén. | 
| [![Beépített ikon][scope-icon]<br/>**hatóköre**][scope-doc] | A műveletek hatókörbe való csoportosítása *, amelyek*a hatókör befejezésének befejezése után kapják meg a saját állapotukat. | [![Beépített ikon][switch-icon]</br>**kapcsoló**][switch-doc] | Olyan esetekrecsoportosíthat műveleteket, amelyek az alapértelmezett eset kivételével egyedi értékeket kapnak. Csak akkor futtassa ezt az esetet, ha a hozzárendelt érték megegyezik egy kifejezés, objektum vagy token eredményével. Ha nem létezik egyezés, futtassa az alapértelmezett esetet. | 
| [![Beépített ikon][terminate-icon]<br/>**leállítása**][terminate-doc] | Egy aktívan futó logikai alkalmazás munkafolyamatának leállítása. | [![Beépített ikon][until-icon]<br/>,**amíg**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel igaz vagy valamilyen állapot megváltozott. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Az adatkezelés és-kezelés

A Logic Apps beépített műveleteket biztosít az adatkimenetek és azok formátumának kezeléséhez:  

|   |   | 
|---|---| 
| [![Beépített ikon][data-operations-icon]<br/>adatműveletek][data-operations-doc] | Műveletek végrehajtása adatokkal: <p>- **Összeállítás**: Hozzon létre egyetlen kimenetet több bemenetből különböző típusokkal. <br>- **CSV-tábla létrehozása**: Vesszővel tagolt (CSV) tábla létrehozása egy JSON-objektumokkal rendelkező tömbből. <br>- **HTML-tábla létrehozása**: Hozzon létre egy HTML-táblázatot egy JSON-objektumokat tartalmazó tömbből. <br>- **Tömb szűrése**: Tömb létrehozása egy másik tömb elemeiből, amelyek megfelelnek a feltételeknek. <br>- **Csatlakozás**: Hozzon létre egy karakterláncot egy tömb összes eleméről, és válassza el ezeket az elemeket a megadott elválasztóval. <br>- **JSON-elemzés**: Felhasználóbarát tokeneket hozhat létre a tulajdonságok és azok értékei JSON-tartalomban való létrehozásához, hogy ezeket a tulajdonságokat a munkafolyamatban is használhassa. <br>- **Válassza**a következőket: Hozzon létre egy tömböt JSON-objektumokkal egy másik tömb elemeinek vagy értékeinek átalakításával, és rendelje hozzá ezeket az elemeket a megadott tulajdonságokhoz. | 
| ![Beépített ikon][date-time-icon]<br/>**Dátum és idő** | Műveletek elvégzése időbélyegekkel: <p>- **Hozzáadás az**időponthoz: Adja hozzá a megadott számú egységet egy időbélyeghez. <br>- **Időzóna konvertálása**: A forrás időzóna időbélyegének konvertálása a cél időzónára. <br>- **Aktuális idő**: Az aktuális időbélyeg visszaadása karakterláncként. <br>- **Jövőbeli idő**beolvasása: Az aktuális időbélyeg és a megadott időegységek visszaadása. <br>- **Múltbeli időpont**beolvasása: Az aktuális időbélyeg visszaadása a megadott időegységek mínusz. <br>- **Kivonás az**időpontból: Több időegység kivonása egy időbélyegből. |
| [![Beépített ikon][variables-icon]<br/>**változók**][variables-doc] | Műveletek végrehajtása változókkal: <p>- **Hozzáfűzés a tömb változóhoz**: Érték beszúrása egy változó által tárolt tömb utolsó elemeként. <br>- **Hozzáfűzés a karakterlánc-változóhoz**: Érték beszúrása egy változó által tárolt karakterlánc utolsó karaktere. <br>- **Változó csökkentése**: Változó csökkentése állandó értékkel. <br>- **Növekmény változó**: A változókat állandó értékkel növelheti. <br>- **Változó inicializálása**: Hozzon létre egy változót, és állapítsa meg az adattípusát és a kezdeti értékét. <br>- **Változó beállítása**: Rendeljen egy másik értéket egy meglévő változóhoz. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Felügyelt API-összekötők

A Logic Apps ezeket a népszerű szabványos összekötőket biztosítja a feladatok, folyamatok és munkafolyamatok automatizálásához ezekkel a szolgáltatásokkal vagy rendszerekkel.

|   |   |   |   | 
|---|---|---|---| 
| [![API-][azure-service-bus-icon]<br/>ikon**Azure Service Bus**][azure-service-bus-doc] | A Logic Apps leggyakrabban használt összekötőjével aszinkron üzeneteket, munkameneteket és témakör-előfizetéseket kezelhet. | [![API-][sql-server-icon]<br/>ikon**SQL Server**][sql-server-doc] | Kapcsolódjon SQL Server a felhőben, vagy egy Azure SQL Database a felhőben, így kezelheti a rekordokat, futtathatja a tárolt eljárásokat vagy lekérdezéseket végezhet. | 
| [![API-][office-365-outlook-icon]<br/>ikon**Office<br/>365 Outlook**][office-365-outlook-doc] | Kapcsolódjon az Office 365 e-mail-fiókjához, így e-maileket, feladatokat, naptári eseményeket és értekezleteket, névjegyeket, kéréseket és egyéb műveleteket hozhat létre és kezelhet. | [![API-][azure-blob-storage-icon]<br/>ikon**Azure<br/>blob Storage**][azure-blob-storage-doc] | Kapcsolódjon a Storage-fiókjához, hogy blob-tartalmakat hozzon létre és kezeljen. | 
| [![API-][sftp-icon]<br/>ikon**SFTP**][sftp-doc] | Csatlakozhat az interneten elérhető SFTP-kiszolgálókhoz, és elérheti az azokon lévő mappákat és fájlokat. | [![API-][sharepoint-online-icon]<br/>ikon**SharePoint<br/>online**][sharepoint-online-doc] | Kapcsolódjon a SharePoint Online-hoz, így kezelheti a fájlokat, a mellékleteket, a mappákat és egyebeket. | 
| [![API-][dynamics-365-icon]<br/>ikon**Dynamics<br/>365 CRM Online**][dynamics-365-doc] | Kapcsolódjon a Dynamics 365-fiókjához, hogy rekordokat, elemeket és egyebeket hozzon létre és kezeljen. | [![API-][ftp-icon]<br/>ikon**FTP**][ftp-doc] | Csatlakozhat az internetről elérhető FTP-kiszolgálókhoz, így a fájlokkal és mappákkal is dolgozhat. | 
| [![API-][salesforce-icon]<br/>ikon**Salesforce**][salesforce-doc] | Kapcsolódjon a Salesforce-fiókhoz, így olyan elemeket hozhat létre és kezelhet, mint a rekordok, a feladatok, az objektumok stb. | [![API-][twitter-icon]<br/>ikon**Twitter**][twitter-doc] | Csatlakozhat a Twitter-fiókjához, így Ön kezelheti a tweeteket, a követőiket, az idővonalat és egyéb műveleteket is. Mentse a tweeteket az SQL, az Excel vagy a SharePoint szolgáltatásba. | 
| [![API-][azure-event-hubs-icon]<br/>ikon**Azure Event Hubs**][azure-event-hubs-doc] | Események felhasználása és közzététele az Event hub használatával. Például lekérheti a logikai alkalmazás kimenetét Event Hubs, majd elküldheti a kimenetet egy valós idejű elemzési szolgáltatónak. | [![API-][azure-event-grid-icon]<br/>ikon**Azure Event**</br>**Grid**][azure-event-grid-doc] | Egy Event Grid által közzétett események figyelése, például amikor az Azure-erőforrások vagy a külső gyártók erőforrásai változnak. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők 

Íme néhány általánosan használt standard összekötő, amelyet Logic Apps biztosít a helyszíni rendszerekben tárolt adatok és erőforrások eléréséhez. A helyszíni rendszerhez való kapcsolódás előtt először [le kell töltenie, telepítenie és be kell állítania egy][gateway-doc]helyszíni adatátjárót. Ez az átjáró biztonságos kommunikációs csatornát biztosít anélkül, hogy be kellene állítania a szükséges hálózati infrastruktúrát. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![API-][file-system-icon]<br/>ikon **</br> fájlrendszere**][file-system-doc] | [![API-][ibm-db2-icon]<br/>ikon**IBM DB2**][ibm-db2-doc] | [![API-][ibm-informix-icon]<br/>ikon**IBM** </br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-][oracle-db-icon]<br/>ikon**Oracle db**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-][sharepoint-server-icon]<br/>ikon**SharePoint</br> -kiszolgáló**][sharepoint-server-doc] | [![API-][sql-server-icon]<br/>ikon**SQL</br> Server**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői

A Logic Apps szabványos összekötőket biztosít a vállalatközi (B2B) megoldások létrehozásához és az Azure-beli Enterprise Integration Pack (EIP) keresztül elérhető [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)kifizetéséhez. Ezzel a fiókkal B2B-összetevőket hozhat létre és tárolhat, például kereskedelmi partnereket, szerződéseket, térképeket, sémákat, tanúsítványokat stb. Ezeknek az összetevőknek a használatához társítsa a logikai alkalmazásokat az integrációs fiókjával. Ha jelenleg BizTalk Server használ, előfordulhat, hogy ezek az összekötők már ismerősnek tűnnek.

|   |   |   |   | 
|---|---|---|---| 
| [![API-][as2-icon]<br/>ikon**AS2</br> -dekódolás**][as2-doc] | [![API-][as2-icon]<br/>ikon**AS2</br> -kódolás**][as2-doc] | [![API-][edifact-icon]<br/>ikon**EDIFACT</br>**  dekódolása][edifact-decode-doc] | [![API-][edifact-icon]<br/>ikon**EDIFACT</br> kódolása**][edifact-encode-doc] | 
| [![API-][flat-file-decode-icon]<br/>ikon – **</br> lapos fájl** dekódolása][flat-file-decode-doc] | [![API-][flat-file-encode-icon]<br/>ikon – **</br> lapos fájl kódolása**][flat-file-encode-doc] | [![API-][integration-account-icon]<br/>ikon**integrációs<br/>fiókja**][integration-account-doc] | [![API-][liquid-icon]<br/>ikon**folyékony**</br>**átalakítások**][json-liquid-transform-doc] | 
| [![API-][x12-icon]<br/>ikon**X12</br>**  dekódolása][x12-decode-doc] | [![API-][x12-icon]<br/>ikon**X12</br> kódolása**][x12-encode-doc] | [![API-][xml-transform-icon]<br/>ikon**XML-** </br>**átalakítások**][xml-transform-doc] | [![API-][xml-validate-icon]<br/>ikon **<br/>XML-ellenőrzése**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A Logic Apps a nagyvállalati rendszerekhez (például SAP és IBM MQ) való hozzáféréshez biztosít ilyen vállalati összekötőket:

|   |   |   | 
|---|---|---| 
| [![API-][ibm-3270-icon]<br/>ikon**IBM 3270**][ibm-3270-doc] | [![API-][ibm-mq-icon]<br/>ikon**IBM MQ**][ibm-mq-doc] | [![API-][sap-icon]<br/>ikon**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Triggerek és műveletek – további információ

Az összekötők *eseményindítókat*, *műveleteket*vagy mindkettőt adhatnak meg. Az *eseményindító* a logikai alkalmazások első lépése, amely általában megadja az eseményindítót kiváltó eseményt, és elindítja a logikai alkalmazás futtatását. Például az FTP-összekötő olyan triggerrel rendelkezik, amely elindítja a logikai alkalmazást "fájl hozzáadásakor vagy módosításakor". Egyes eseményindítók rendszeresen megkeresik a megadott eseményt vagy az adattípust, majd a megadott esemény vagy az adatgyűjtés észlelése után tüzet fognak. Más eseményindítók várnak, de azonnal megtörténnek egy adott esemény bekövetkezésekor, vagy amikor új adatkapcsolat áll rendelkezésre. Az eseményindítók a logikai alkalmazáshoz is továbbítanak minden szükséges adattal. A logikai alkalmazás az összes munkafolyamatban képes olvasni és felhasználni ezeket az adatait.
Például a Twitter-összekötő egy triggerrel rendelkezik, "új Tweet közzétételekor", amely továbbítja a tweet tartalmát a logikai alkalmazás munkafolyamataiba. 

Egy eseményindító elindítása után Azure Logic Apps létrehozza a logikai alkalmazás egy példányát, és elindítja a logikai alkalmazás munkafolyamatában lévő *műveleteket* . A műveletek a logikai alkalmazás munkafolyamataiban a triggert követő és a feladatokat elvégző lépések. Létrehozhat például egy olyan logikai alkalmazást, amely egy SQL-adatbázisból szerzi be a vásárlói adatait, és feldolgozza azokat a későbbi műveletekben. 

A Azure Logic Apps az alábbi általános típusú eseményindítókat biztosítja:

* *Ismétlődési eseményindító*: Ez az aktiválás megadott ütemterv szerint fut, és nincs szorosan társítva egy adott szolgáltatáshoz vagy rendszerhez.

* *Lekérdezési trigger*: Ez az eseményindító rendszeresen lekérdezi egy adott szolgáltatást vagy rendszert a megadott ütemterv alapján, új adatellenőrzést végez, vagy adott esemény történt. Ha új adatok állnak rendelkezésre, vagy az adott esemény történt, az eseményindító létrehozza és futtatja a logikai alkalmazás egy új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

* *Leküldéses trigger*: Ez az eseményindító megvárja és figyeli az új adatgyűjtést, vagy egy esemény bekövetkeztekor. Ha új adatok állnak rendelkezésre, vagy ha az esemény történik, az eseményindító létrehozza és futtatja a logikai alkalmazás új példányát, amely mostantól a bemenetként továbbított adatokat is használhatja.

<a name="custom"></a>

## <a name="connector-configuration"></a>Összekötő konfigurációja

Minden összekötő eseményindítói és műveletei saját tulajdonságokat biztosítanak a konfigurálásához. Számos összekötő azt is megköveteli, hogy először létre kell hoznia egy kapcsolódást a cél szolgáltatáshoz vagy a rendszerhez, és meg kell adnia a hitelesítő adatokat vagy más konfigurációs adatokat, mielőtt a logikai alkalmazásban eseményindítót vagy műveletet alkalmazhat. Az adatok eléréséhez vagy az Ön nevében történő közzétételhez például engedélyeznie kell egy Twitter-fiókhoz való kapcsolódást. 

A OAuth-t használó összekötők esetében a kapcsolat létrehozása a szolgáltatásba való bejelentkezést jelenti, például az Office 365, az Salesforce vagy a GitHub szolgáltatást, ahol a hozzáférési token titkosított és biztonságos módon tárolódik egy Azure titkos tárolóban. Más összekötők, például az FTP és az SQL esetében olyan kapcsolatra van szükség, amely konfigurációs adatokat tartalmaz, például a kiszolgáló címe, a Felhasználónév és a jelszó. Ezeket a kapcsolati konfigurációs adatokat is titkosítja és biztonságosan tárolja. 

A kapcsolatok a szolgáltatás vagy a rendszer engedélyezése esetén is hozzáférhetnek a cél szolgáltatáshoz vagy rendszerhez. Az Azure Active Directory (AD) OAuth-kapcsolatokat (például az Office 365-et és a Dynamics-t) használó szolgáltatások esetében a Azure Logic Apps határozatlan időre frissíti a hozzáférési jogkivonatokat. Előfordulhat, hogy az egyéb szolgáltatások korlátozva vannak, hogy Azure Logic Apps a tokent frissítés nélkül is használhatja. Bizonyos műveletek általában érvénytelenítik az összes hozzáférési jogkivonatot, például a jelszó módosítását.

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

## <a name="next-steps"></a>További lépések

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
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Csatlakozhat az Azure Event Hubs szolgáltatáshoz. Eseményeket fogadhat és küldhet a logikai alkalmazások és az Event Hubs között"
[box-doc]: ./connectors-create-api-box.md "Csatlakozhat a Boxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Csatlakozhat a Dropboxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Csatlakozhat a Dynamics CRM Online szolgáltatáshoz, és használhatja a CRM Online adatait"
[facebook-doc]: ./connectors-create-api-facebook.md "Csatlakozhat a Facebookhoz. Bejegyzéseket tehet közzé az idővonalon, laptartalmakat kérhet le, és egyéb műveleteket is végrehajthat"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftp-doc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[github-doc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "A Google Naptárhoz csatlakozhat, és felügyelheti a naptárat."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Csatlakozhat a Google Drive-hoz, és használhatja annak adatait"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Csatlakozhat a Google táblázatokhoz, így módosíthatja a lapokat"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Csatlakozhat a Google Teendőkhöz, ahol különböző teendőit kezelheti"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Kapcsolódás 3270-alkalmazásokhoz IBM-es nagyszámítógépeken"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Csatlakozhat az IBM DB2-höz a felhőben vagy a helyszínen. Sorokat frissíthet, táblákat kérhet le, és egyéb műveleteket is végrehajthat"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Csatlakozhat az Informixhez a felhőben vagy a helyszínen. Sorokat olvashat el, táblákat listázhat, és egyéb műveleteket is végrehajthat"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Kapcsolódás az IBM MQ-hoz a helyszínen vagy az Azure-ban üzenetek küldéséhez és fogadásához"
[instagram-doc]: ./connectors-create-api-instagram.md "Csatlakozhat az Instagramhoz. Eseményeket indíthat vagy reagálhat rájuk"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Csatlakozhat MailChimp-fiókjához. Kezelheti és automatizálhatja e-mailjeit"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Csatlakozhat a Microsoft Translatorhoz. Szövegeket fordíthat, nyelveket ismerhet fel, és egyéb műveleteket is végrehajthat" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Csatlakozhat Office 365-fiókjához. E-maileket küldhet és fogadhat, kezelheti a naptárát és névjegyeit, és egyéb műveleteket is végrehajthat"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Videoadatokat, videólistákat és csatornákat kérhet le, valamint Office 365-videók lejátszási URL-címeit"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Csatlakozhat személyes Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Csatlakozhat vállalati Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, ahol többek között sorokat adhat hozzá, szúrhat be és törölhet"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Csatlakozhat Outlook-postaládájához. Kezelheti az e-mailjeit, naptárait, névjegyeit és egyéb tartalmait"
[project-online-doc]: ./connectors-create-api-projectonline.md "Csatlakozhat a Microsoft Project Online-hoz. Kezelheti a projektjeit, feladatait, erőforrásait és egyéb tartalmait"
[rss-doc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és lekérése, illetve műveletek kiváltása, ha egy új elemet tesznek közzé egy RSS-hírcsatornán."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Csatlakozhat Salesforce-fiókjához. Kezelheti fiókjait, az érdeklődőket, üzleti lehetőségeit és egyéb elemeket"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Csatlakozhat a SendGridhez. E-mail küldése és a címzettek jegyzékeinek kezelése"
[sftp-doc]: ./connectors-create-api-sftp.md "Csatlakozhat SFTP-fiókjához. Fájlokat tölthet fel, kérhet le, törölhet, és egyéb műveleteket is végrehajthat"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Csatlakozhat a helyszíni SharePoint-kiszolgálóhoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Csatlakozhat a SharePoint Online-hoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozhat a Slackhez, és üzeneteket tehet közzé a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Kapcsolódás SMTP-kiszolgálóhoz és mellékletek küldése e-mailben"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Kapcsolódjon Azure SQL Databasehoz vagy SQL Serverhoz. SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet."
[trello-doc]: ./connectors-create-api-trello.md "Csatlakozhat a Trellóhoz. Kezelheti a projektjeit és bármit és bárkivel megszervezhet"
[twilio-doc]: ./connectors-create-api-twilio.md "Csatlakozhat a Twilióhoz. Üzeneteket küldhet, lekérheti az elérhető számokat, kezelheti a bejövő hívások telefonszámait, és egyéb műveleteket is végrehajthat"
[twitter-doc]: ./connectors-create-api-twitter.md "Csatlakozhat a Twitterhez. Idővonal-tartalmakat fogadhat, tweeteket tehet közzé, és egyéb műveleteket is végrehajthat"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Csatlakozhat a Wunderlisthez. Kezelheti a feladatait és teendőlistáit, szinkronizálhatja különböző tartalmait, és egyéb műveleteket is végrehajthat"
[yammer-doc]: ./connectors-create-api-yammer.md "Csatlakozhat a Yammerhez. Üzenetek tehet közzé, új üzeneteket fogadhat, és egyéb műveleteket is végrehajthat"
[youtube-doc]: ./connectors-create-api-youtube.md "Csatlakozhat a YouTube-hoz. Kezelheti a videóit és csatornáit"

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
