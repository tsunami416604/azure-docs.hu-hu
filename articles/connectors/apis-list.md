---
title: Az Azure Logic Apps összekötői | Microsoft Docs
description: Munkafolyamatainak automatizálásához beépített, felügyelt API-t a helyszíni, integrációs fiókot és az Azure Logic Apps enterprise összekötők
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 06/29/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bb3e2ce29037372395aa0b30e9f76f3e712667d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096611"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötők

Összekötők fontos szerepet játszanak az Azure Logic Apps automatizált munkafolyamatok létrehozásakor. A logic apps összekötők használatával funkcióinak kiterjesztése a helyszíni és felhőalapú alkalmazások létrehozása, és már rendelkezik adatai feladatok elvégzéséhez. Miközben a Logic Apps ajánlatok ~ 200 + összekötők, ez a cikk ismerteti a népszerű és gyakrabban használt összekötők sikeresen használt alkalmazások ezer és végrehajtások több millió az adatok és információk feldolgozásra.
Összekötők built-ins vagy a felügyelt összekötők érhetők el. 

* [**Built-ins**](#built-ins): A beépített műveletek és eseményindítók súgó hoz létre a logic apps egyéni ütemezések futó kommunikálni végpontja, fogadására és kérésekre válaszol, és hívja meg Azure feladatokat az Azure API Apps (webalkalmazások), a saját API-k kezelhetők, és az Azure API Management és a beágyazott logika közzétett alkalmazások fogadhassanak kérelmeket. Is használhatja a beépített műveletek, amelyek segítenek rendszerezése és a Logic Apps alkalmazást munkafolyamat vezérlését és adatokkal is működik.

* **Felügyelt összekötők**: Adja meg az összekötők, eseményindítók és műveletek más szolgáltatások és rendszerek eléréséhez. Összekötők szükséges, hogy először létre kell hoznia Azure Logic Apps által kezelt csatlakozások. A felügyelt összekötők ezek a csoportok szerint vannak csoportosítva:

  |   |   |
  |---|---|
  | [**A felügyelt API-összekötők**](#managed-api-connectors) | Logic Apps alkalmazások, például az Azure Blob Storage tárolóban, Office 365, Dynamics, a Power BI, OneDrive, Salesforce, SharePoint Online és sok más szolgáltatásokat használó létrehozása. | 
  | [**A helyszíni összekötő**](#on-premises-connectors) | Telepítése és beállítása után a [helyszíni adatátjáró][gateway-doc], ezek összekötők súgó a logic apps hozzáférés a helyszíni rendszerek – például az SQL Server, SharePoint Server, Oracle DB, fájlmegosztásokat és mások számára. | 
  | [**Integrációs fiók összekötők**](#integration-account-connectors) | Rendelkezésre álló létrehozásakor és integrációs-fiók esetén ezek összekötők átalakítás után kell fizetnie és XML érvényesítése, kódolása és egybesimított fájlok, és vállalatok feldolgozni (B2B) üzenetek AS2, EDIFACT és X12 protokollokat. | 
  | [**Vállalati összekötők**](#enterprise-connectors) | A további költségek a vállalati rendszerek például SAP és IBM MQ hozzáférést biztosítanak. |
  ||| 

  Például Microsoft BizTalk Server használata, a logic apps csatlakozhat, és a BizTalk Server használatával kommunikálnak a [BizTalk Server-összekötő](#on-premises-connectors). 
  Majd kiterjeszthetik vagy a logic apps a BizTalk-szerű műveleteinek elvégzéséhez a [integrációs fiók összekötők](#integration-account-connectors). 

Minden egyes összekötőt eseményindítók és műveletek, Swagger leírását, valamint bármely korlátai által meghatározott műszaki információt [Connector részleteket](/connectors/). Költség információkért lásd: [díjszabása Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) és [fizetési modell Logic Apps](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Beépített

A Logic Apps beépített eseményindítókat biztosít, és műveletek ütemezésalapú munkafolyamatokat hozhat létre, ezzel a logic apps egyéb alkalmazások és szolgáltatások, a munkafolyamat a logic apps segítségével vezérlő kommunikálni és kezelése vagy kezelhetők adatok. 

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][schedule-icon]<br/>**ütemezése**][recurrence-doc] | -A Logic Apps alkalmazást futtatnak kezdve alapvető összetett ismétlődések, a megadott ütemterv a **ismétlődési** eseményindító. <p>-A Logic Apps alkalmazást felfüggeszti a megadott időtartamban a **késleltetés** művelet. <p>– A Logic Apps alkalmazást szünet a megadott dátum és az idő a **addig elhalasztani** művelet. | [![API-ikon][http-icon]<br/>**HTTP**][http-doc] | Eseményindítók és műveletek a HTTP, a HTTP + a Swagger, HTTP és a HTTP + a Webhook keresztül kommunikál a tetszőleges végpontot. | 
| [![API-ikon][http-request-icon]<br/>**kérése**][http-request-doc] | -Ellenőrizze a logikai alkalmazás hívható más alkalmazások vagy szolgáltatások, eseményindító esemény rács erőforrás események vagy eseményindító a válaszokat ad az Azure Security Center riasztásait az **kérelem** eseményindító. <p>-Visszajelzéseket az alkalmazásba vagy szolgáltatásba a **válasz** művelet. | [![API-ikon][batch-icon]<br/>**kötegelt**][batch-doc] | -Kötegek üzeneteinek feldolgozását a **Batch-üzenetek** eseményindító. <p>-A batch hívás a logic apps, amelyeken a meglévő, eseményindító a **üzenetek küldését a batch-** művelet. | 
| [![API-ikon][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Az Azure functions a logic apps egyéni kódtöredékek (C# vagy Node.js) futó hívja. | [![API-ikon][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Eseményindítók és műveletek határozzák meg a saját API-t kezelni és közzétenni az Azure API Management hívni. | 
| [![API-ikon][azure-app-services-icon]<br/>**Azure App Service szolgáltatások**][azure-app-services-doc] | Azure API-alkalmazások vagy webes alkalmazásokhoz, Azure App Service szolgáltatásban üzemeltetett hívja. Az eseményindítók és ezek az alkalmazások által definiált műveletek jelennek meg például első osztályú eseményindítók és műveletek Swagger tartalmazza. | [![API-ikon][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Hívja meg más logikai alkalmazások kérelem eseményindító kezdődik. | 
||||| 

### <a name="control-workflow"></a>Vezérlési munkafolyamat

Az alábbiakban a beépített műveletek szerkezetének kialakítása és a logikai alkalmazás munkafolyamat műveletek szabályozása:

|   |   |   |   | 
|---|---|---|---| 
| [![Beépített ikon][condition-icon]<br/>**feltétel**][condition-doc] | Olyan feltétel értékelése, és hogy alapján futtatási különféle műveletek az feltétele igaz vagy hamis. | [![Beépített ikon][for-each-icon]</br>**minden**][for-each-doc] | A tömb minden eleme ugyanazok a műveletek elvégezhetők. | 
| [![Beépített ikon][scope-icon]<br/>**hatókör**][scope-doc] | A műveletek csoport *hatókörök*, amelyek a saját állapotának beolvasása, miután hatókörében műveletek futása befejeződik. | [![Beépített ikon][switch-icon]</br>**kapcsoló**][switch-doc] | A műveletek csoport *esetekben*, amely rendelt egyedi értékeket az alapértelmezett eset kivételével. Futtatás csak az adott eset, amelynek hozzárendelt értéke megegyezik egy kifejezés, az objektumot, vagy a token kapott eredmény. Ha nincs találat, az alapértelmezett eset futtatása. | 
| [![Beépített ikon][terminate-icon]<br/>**leáll**][terminate-doc] | Állítsa le az aktívan futó logic app munkafolyamat. | [![Beépített ikon][until-icon]<br/>**amíg**][until-doc] | Ismételje meg a műveleteket, amíg a megadott feltétel teljesül, vagy bizonyos állapota megváltozott. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Kezelése vagy kezelhetők adatok

Az alábbiakban a beépített műveletek adatok kimenetek és formátumukban:  

|   |   | 
|---|---| 
| ![Beépített ikon][data-operations-icon]<br/>**Adatok műveletek** | Adatokkal műveleteket: <p>- **Összeállítása**: hozzon létre egy kimeneti a különböző típusú több bemenetei. <br>- **Hozzon létre CSV táblát**: hozzon létre egy vesszővel tagolt (CSV) táblát a JSON-objektumok tömbben. <br>- **HTML-táblázat létrehozása**: hozzon létre egy HTML-tábla a JSON-objektumok tömbjét. <br>- **Szűrő tömb**: tömb a megadott feltételeket teljesítő egy másik tömb elemeinek létrehozása. <br>- **Csatlakozás**: karakterlánc létrehozása a tömbben szereplő összes és a megadott elválasztó való elválasztása a elemeket. <br>- **Elemzése JSON**: tartalom hozható létre felhasználóbarát jogkivonatokat a tulajdonságok és azok értékei a JSON-ban, hogy ezek a Tulajdonságok használhassa a munkafolyamatban. <br>- **Válassza ki**: hozzon létre egy tömb JSON-objektumok átalakítása elemek vagy egy másik tömbben szereplő értékeket, és azokat a cikkeket megadott tulajdonságokkal. | 
| ![Beépített ikon][date-time-icon]<br/>**Dátum idő** | Timestamp műveleteket: <p>- **Adja hozzá a időre**: a megadott számú egység hozzáadása egy időbélyegző. <br>- **Konvertálja az időzóna**: időbélyeg a forrás időzóna átalakítása a célként megadott időzóna. <br>- **Aktuális idő**: az aktuális időbélyeg karakterláncként adja vissza. <br>- **Jövőbeli időpont beolvasása**: az aktuális időbélyeg és a megadott időegységek vissza. <br>- **Első időn túli**: térjen vissza a megadott időegységek mínusz aktuális időbélyeg. <br>- **Idő értékekből kivonni**: időbélyeg idő egységei számos kivonás. |
| [![Beépített ikon][variables-icon]<br/>**változók**][variables-doc] | Műveletek elvégzését a változók: <p>- **Tömbváltozó hozzáfűzése**: Insert értéket egy változó tárolja tömb utolsó elemként. <br>- **Hozzáfűzendő karakterlánc-változóvá**: Insert értéket egy változó tárolja karakterláncban szereplő utolsó karakterként. <br>- **Csökkentést változó**: csökkentéséhez egy változó egy állandó értékkel. <br>- **Növekvő változó**: növelje a változót egy állandó értékkel. <br>- **Változó inicializálására**: hozzon létre egy változót, és az adattípus és a kezdeti érték deklarálható. <br>- **Új érték**: egy létező változó egy másik értéket hozzárendelni. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>A felügyelt API-összekötők

Az alábbiakban a népszerűbbnek összekötők automatizálhatja a feladatokat, folyamatok és munkafolyamatok ezeket a szolgáltatásokat vagy rendszerek:

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Aszinkron üzenetek, munkamenetek és a leggyakrabban használt összekötőt a Logic Apps témakör előfizetésekre kezelése. | [![API-ikon][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Csatlakozzon az SQL Server, a helyszíni vagy egy Azure SQL-adatbázis a felhőben, rekordok kezelése, tárolt eljárások futtatása, vagy hajtsa végre a lekérdezéseket. | 
| [![API-ikon][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Csatlakozni az Office 365 e-mail fiókja, így hozhat létre és e-mailek, feladatok, naptár események és értekezletek, névjegyek, kérelmek és több kezelése. | [![API-ikon][azure-blob-storage-icon]<br/>**Azure Blob<br/>tároló**][azure-blob-storage-doc] | Csatlakozni a tárfiókhoz, így hozhat létre és kezeli a blob tartalmát. | 
| [![API-ikon][sftp-icon]<br/>**SFTP**][sftp-doc] | Csatlakozni próbál hozzáférni az internetről, akivel együttműködik a fájlok és mappák SFTP-kiszolgálók. | [![API-ikon][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Csatlakozzon a SharePoint online, a fájlokat mellékletek, mappák és több kezelheti. | 
| [![API-ikon][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online-hoz**][dynamics-365-doc] | Csatlakozzon a Dynamics 365 fiókját, hozhat létre és kezelheti a rekordok, elemek és egyebek. | [![API-ikon][ftp-icon]<br/>**FTP**][ftp-doc] | FTP-kiszolgálók próbál hozzáférni az internetről, akivel együttműködik a fájlok és mappák csatlakozni. | 
| [![API-ikon][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Csatlakozzon a Salesforce-fiókhoz, hozhat létre és kezelheti a rekordokat, a feladatok, az objektumok és több. | [![API-ikon][twitter-icon]<br/>**Twitter**][twitter-doc] | Csatlakozzon a Twitter-fiók, kezelheti a Twitter-üzeneteket, followers, az ütemterv és egyebek. Mentse a Twitter-üzeneteket SQL, az Excel vagy SharePoint. | 
| [![API-ikon][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Felhasználását, és tegye közzé az Eseményközpontok keresztül. Kimeneti például beszerezni az Event Hubs a Logic Apps alkalmazást, és elküldheti-e, amelyek kimenete egy valós idejű elemzési szolgáltató. | [![API-ikon][azure-event-grid-icon]<br/>**Azure esemény**</br>**rács**][azure-event-grid-doc] | Egy esemény rács például által közzétett, amikor Azure-erőforrások vagy a külső erőforrások módosítása események figyelése | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Helyszíni összekötők 

Íme néhány gyakran használt adatokhoz és erőforrásokhoz való hozzáférés a helyszíni rendszerekben biztosított összekötők. Kapcsolatot létesíthet egy a helyszíni rendszer hozhat létre, előtt először [letöltése, telepítése, és állítson be egy helyszíni adatátjárót][gateway-doc]. Ezt az átjárót egy biztonságos kommunikációs csatornát biztosít anélkül, hogy a szükséges hálózati infrastruktúra beállításához. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Kiszolgáló** | [![API-ikon][file-system-icon]<br/>**fájl</br> rendszer**][file-system-doc] | [![API-ikon][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API-ikon][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-ikon][oracle-db-icon]<br/>**Oracle-adatbázis**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-ikon][sharepoint-server-icon]<br/>**SharePoint</br> kiszolgáló**][sharepoint-server-doc] | [![API-ikon][sql-server-icon]<br/>**SQL</br> kiszolgáló**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Integrációs fiókok összekötői 

Összekötők használatával történő megtervezésével üzleti vállalatközi (B2B) megoldások a logic Apps alkalmazások létrehozásakor és kell fizetnie az alábbiakban egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), elérhető keresztül a vállalati Integration Pack (EIP-t) az Azure-ban. Ezt a fiókot hozhat létre és tárolja a B2B összetevők, például kereskedelmi partnerek, egyezmények, maps, sémák, tanúsítványok és stb. Ezen összetevők használatához társítsa a logic apps integrációs fiókját. BizTalk Server jelenleg használ, ha az összekötők tűnhet ismerős már.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][as2-icon]<br/>**AS2</br> dekódolása**][as2-decode-doc] | [![API-ikon][as2-icon]<br/>**AS2</br> kódolás**][as2-encode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> dekódolása**][edifact-decode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> kódolás**][edifact-encode-doc] | 
| [![API-ikon][flat-file-decode-icon]<br/>**Egybesimított fájl</br> dekódolása**][flat-file-decode-doc] | [![API-ikon][flat-file-encode-icon]<br/>**Egybesimított fájl</br> kódolás**][flat-file-encode-doc] | [![API-ikon][integration-account-icon]<br/>**integrációs<br/>fiók**][integration-account-doc] | [![API-ikon][liquid-icon]<br/>**folyékony**</br>**átalakítja**][json-liquid-transform-doc] | 
| [![API-ikon][x12-icon]<br/>**X12</br> dekódolása**][x12-decode-doc] | [![API-ikon][x12-icon]<br/>**X12</br> kódolás**][x12-encode-doc] | [![API-ikon][xml-transform-icon]<br/>**XML**</br>**átalakítja**][xml-transform-doc] | [![API-ikon][xml-validate-icon]<br/>**XML <br/>érvényesítése**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Vállalati összekötők

A logic Apps alkalmazások férhetnek hozzá a vállalati rendszerek, például SAP és IBM MQ:

|   |   | 
|---|---| 
| [![API-ikon][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![API-ikon][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>További információ a eseményindítók és műveletek

Néhány összekötő is biztosít *eseményindítók* a Logic Apps alkalmazást, amely értesítése, ha a meghatározott események következnek be. Ezért ezeket az eseményeket fordulhat elő, amikor az eseményindító hoz létre és fut a Logic Apps alkalmazást példánya. Az FTP-összekötő például a logikai alkalmazás kezdődik, amikor egy fájl frissül a "Ha a fájl hozzáadása vagy módosítása" eseményindítót tartalmaz. 

A Logic Apps biztosít az ilyen típusú eseményindítók:  

* *Lekérdezési eseményindítók*: ezek az eseményindítók kérdezze le a szolgáltatás egy adott gyakorisággal és ellenőrzi az új adatokat. 

  Új adatok nem érhető el, a logikai alkalmazás új példánya jön létre, és fut, az adatok átadott bemeneti adatként. 

* *Leküldéses eseményindítók*: ezek az eseményindítók figyelésére új adatokat a végpont vagy egy adott esemény fordulhat elő, amely létrehozza, és a logikai alkalmazás új példányát futtatja.

* *Ismétlődés eseményindító*: ehhez az eseményindítóhoz hoz létre, és futtatja a Logic Apps alkalmazást, a megadott ütemezés szerint egy példányát.

Összekötők is biztosítanak *műveletek* feladatokat, amelyek a Logic Apps alkalmazást a munkafolyamatban. Például a logikai alkalmazás-adatok olvasása és használja ezeket az adatokat későbbi lépéseiben a Logic Apps alkalmazást. Pontosabban a Logic Apps alkalmazást kereshet ügyféladatok egy SQL-adatbázisból, és ezeket az adatokat később a Logic Apps alkalmazást munkafolyamatban feldolgozni. 

Eseményindítók és műveletek kapcsolatban bővebben lásd: a [összekötők áttekintése](connectors-overview.md). 

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők 

API-t egyéni kódra, vagy nem érhetők el összekötők meghívásához kiterjesztheti a Logic Apps platform által [egyéni API-alkalmazások létrehozása](../logic-apps/logic-apps-create-api-app.md). Emellett [hozzon létre egyéni összekötők](../logic-apps/custom-connector-overview.md) a *bármely* REST vagy a SOAP-alapú API-k, amelyek ezen API-k számára elérhetővé tenni a logikai alkalmazást az Azure-előfizetése.
Ahhoz, hogy egyéni API-alkalmazások és összekötők nyilvános mindenki számára használja az Azure-ban, akkor [küldje el a Microsoft hitelesítő összekötők](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Küldje el, vagy az Azure Logic Apps alkalmazások és összekötők ötleteket szavazhatnak, látogasson el a [Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

* Azok a dokumentumok hiányzó cikkek, vagy úgy véli, hogy részletek fontosak? Ha igen, a meglévő cikkek ad hozzá, vagy saját írásával segítségével. A dokumentáció nyílt forráskódú, és a Githubon található. Ismerkedés az Azure dokumentációja, [GitHub-tárházban](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>További lépések

* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Hozzon létre egyéni csatlakozók a logic Apps alkalmazások](https://docs.microsoft.com/connectors/custom-connectors/)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Hozzon létre egy Azure API Management szolgáltatás példányt kezelése, és az API-kat közzététele"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai appok integrálása App Service API Apps-alkalmazásokkal"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Folyamat üzenetek csoportok, vagy kötegek"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Olyan feltétel értékelése, és hogy alapján futtatási különféle műveletek a feltétel igaz vagy hamis"
[delay-doc]: ./connectors-native-delay.md "Késleltetett műveletek végrehajtása"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Ugyanazok a műveletek elvégezhetők a tömb minden elemhez"
[http-doc]: ./connectors-native-http.md "HTTP-hívások indítása HTTP-összekötővel"
[http-request-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-response-doc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-swagger-doc]: ./connectors-native-http-swagger.md "HTTP-hívások indítása HTTP + Swagger összekötővel"
[http-webook-doc]: ./connectors-native-webhook.md "A logic apps HTTP webhookműveletek és eseményindítók hozzáadása"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[query-doc]: ./connectors-native-query.md "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[recurrence-doc]:  ./connectors-native-recurrence.md "Ismétlődő műveletek kiváltása logikai alkalmazásokhoz"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Műveletek után a csoport műveletek futása befejeződik, a saját állapotának beolvasása csoportokba rendezheti" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Az esetekről, melyek rendelt egyedi értékeket műveletek rendezhetők. Futtatás csak a helyzet, amelynek értéke megegyezik egy kifejezés, az objektumot, vagy a token kapott eredmény. Ha nincs találat, az alapértelmezett eset futtatása"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Állítsa le, vagy szakítsa meg a logikai alkalmazásnak egy aktívan futó munkafolyamathoz"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ismételje meg a műveleteket, amíg a megadott feltétel teljesül, vagy bizonyos állapota megváltozott"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Műveletek elvégzését a változókat, például az inicializálási, set, növekvő, csökkentést, és a karakterlánc vagy tömb változó hozzáfűzése"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Egy esemény rács például által közzétett, amikor Azure-erőforrások vagy a külső erőforrások módosítása események figyelése"
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
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google-lapok kapcsolódni, így módosíthatja a smartsheetet"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Csatlakozhat a Google Teendőkhöz, ahol különböző teendőit kezelheti"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Csatlakozhat az IBM DB2-höz a felhőben vagy a helyszínen. Sorokat frissíthet, táblákat kérhet le, és egyéb műveleteket is végrehajthat"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Csatlakozhat az Informixhez a felhőben vagy a helyszínen. Sorokat olvashat el, táblákat listázhat, és egyéb műveleteket is végrehajthat"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Csatlakozás helyszíni IBM MQ vagy az Azure szolgáltatásban az üzenetek küldése és fogadása"
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
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Csatlakozhat a SendGridhez. E-mailek küldése és a címzett listák kezelése"
[sftp-doc]: ./connectors-create-api-sftp.md "Csatlakozhat SFTP-fiókjához. Fájlokat tölthet fel, kérhet le, törölhet, és egyéb műveleteket is végrehajthat"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Csatlakozhat a helyszíni SharePoint-kiszolgálóhoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Csatlakozhat a SharePoint Online-hoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[slack-doc]: ./connectors-create-api-slack.md "Csatlakozhat a Slackhez, és üzeneteket tehet közzé a Slack-csatornákon"
[smtp-doc]: ./connectors-create-api-smtp.md "Egy SMTP-kiszolgálóhoz csatlakozhat, és mellékleteket tartalmazó e-mailek küldése"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Csatlakozás az Azure SQL Database vagy az SQL Server. SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet."
[trello-doc]: ./connectors-create-api-trello.md "Csatlakozhat a Trellóhoz. Kezelheti a projektjeit és bármit és bárkivel megszervezhet"
[twilio-doc]: ./connectors-create-api-twilio.md "Csatlakozhat a Twilióhoz. Üzeneteket küldhet, lekérheti az elérhető számokat, kezelheti a bejövő hívások telefonszámait, és egyéb műveleteket is végrehajthat"
[twitter-doc]: ./connectors-create-api-twitter.md "Csatlakozhat a Twitterhez. Idővonal-tartalmakat fogadhat, tweeteket tehet közzé, és egyéb műveleteket is végrehajthat"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Csatlakozhat a Wunderlisthez. Kezelheti a feladatait és teendőlistáit, szinkronizálhatja különböző tartalmait, és egyéb műveleteket is végrehajthat"
[yammer-doc]: ./connectors-create-api-yammer.md "Csatlakozhat a Yammerhez. Üzenetek tehet közzé, új üzeneteket fogadhat, és egyéb műveleteket is végrehajthat"
[youtube-doc]: ./connectors-create-api-youtube.md "Csatlakozhat a YouTube-hoz. Kezelheti a videóit és csatornáit"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Megismerheti a vállalati integrációs AS2-t."
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Megismerheti a vállalati integrációs AS2-dekódolást."
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Megismerheti a vállalati integrációs AS2-kódolást."
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