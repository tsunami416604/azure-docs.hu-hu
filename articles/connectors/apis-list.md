---
title: "Az Azure Logic Apps összekötői | Microsoft Docs"
description: "Válasszon az összes elérhető Microsoft-összekötő közül, ha logikai alkalmazásokat szeretne összeállítani és létrehozni"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.openlocfilehash: 948b91a9fabc3ab3c4d6708968a88cb9d203b171
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="connectors-list"></a>Összekötők listája
Az egyes összekötők Swagger-leírásai és az esetleges összekötőkorlátok által meghatározott eseményindítók és műveletek keresését az [összekötő részletei](/connectors/) rész ismerteti.

Az összekötők a logikai alkalmazások létrehozásának szerves részei. Ezen összekötők használatával kiterjesztheti a helyszíni és felhőbeli alkalmazásokat, így rengeteg mindent kezdhet a létrehozott és már meglévő adataival. Az összekötők beépített műveletekként vagy felügyelt összekötőkként érhetőek el.

**Beépített műveletek**: Maga a Logic Apps-motor biztosít beépített műveleteket a végpontokkal történő kommunikációhoz és a feladatok végrehajtásához. E műveletek alkalmazásával például meghívhat HTTP-végpontokat, Azure Functions-szolgáltatásokat és Azure API Management-műveleteket, valamint az üzeneteket adatműveletekkel és változókkal módosíthatja.

**Felügyelt összekötők**: Különböző szolgáltatások számára biztosít hozzáférést az API-khoz a Logic Apps-szolgáltatás által üzemeltetett és kezelt API-kapcsolatok létrehozásával. A felügyelt összekötők kategóriái:

* **Standard összekötők**: Automatikusan elérhetők és részei a logikai alkalmazásoknak. Néhány példa: Service Bus, Power BI, OneDrive stb.

* **Helyszíni összekötők**: [Helyszíni adatátjáró][gatewaydoc] használatával biztosít kapcsolódási lehetőséget helyszíni kiszolgálói alkalmazásokhoz. A helyszíni összekötők olyan kiszolgálói alkalmazások csatlakoztatását teszik lehetővé, mint például a SharePoint Server, az SQL Server, az Oracle-adatbázis vagy a fájlmegosztások stb.

* **Integrációsfiók-összekötők**: Akkor érhetők el, ha integrációs fiókot vásárol. Ezen összekötők használatával átalakíthatja és érvényesítheti az XML-fájlokat, vállalatok közötti üzeneteket dolgozhat fel az AS2/X12/EDIFACT használatával, illetve egybesimított fájlokat kódolhat és dekódolhat. Ha BizTalk Serverrel dolgozik, akkor ezek az összekötők a BizTalk-munkafolyamatok Azure-ba történő kibontásakor kifejezetten hasznosak.  

    A BizTalk Server rendelkezik [Logic Apps-adapterrel](https://msdn.microsoft.com/library/mt787163.aspx) is, amely elérhetővé teszi a logikai alkalmazással folytatott kétoldalú kommunikációt.

* **Vállalati összekötők**: Tartalmazza az MQ-t és az SAP-t. További költségekkel érhető el. 

További információk a költségekről: [Díjszabás részletei](https://azure.microsoft.com/pricing/details/logic-apps/), a Logic Apps [díjszabási modellje](../logic-apps/logic-apps-pricing.md). 

## <a name="popular-connectors"></a>Népszerű összekötők
Több ezer olyan alkalmazás és több millió olyan végrehajtás létezik, amely ezekkel az összekötőkkel dolgozott fel sikeresen adatokat és információkat. 

### <a name="built-in-actions"></a>Beépített műveletek
A Logic Apps-motor által biztosított műveletek segítségével adatokat módosíthat, HTTP-kommunikációt valósíthat meg, és irányíthatja a Logic App-alkalmazások meghatározásának folyamatát. Példák ilyen műveletekre:

| |  |  |  |
| --- | --- | --- | --- |
| [![API-ikon][HTTPicon]<br/>**HTTP**][httpdoc] | A logikai alkalmazások segítségével HTTP protokollon keresztül bármilyen végponttal kommunikálhat.| [![API-ikon][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Létrehozhat C#- vagy node.js-környezetben készített, egyedi kódrészleteket futtatni képes függvényeket, majd logikai alkalmazásaiban felhasználhatja ezeket.  |
| [![API-ikon][HTTP-Requesticon]<br/>**Kérés**][HTTP-Requestdoc] | Hívható HTTPS URL-címet biztosít, amely jellemzően más alkalmazások webhookjaként használható. A logikai alkalmazás elindul, amikor kérést kap erre az URL-címre. | [![API-ikon][Recurrenceicon]<br/>**Ütemezés**][recurrencedoc] | A logikai alkalmazások indítása egyszerű vagy összetett ismétlődésütemezések alapján történhet. Létrehozhatunk például ütemezéseket az egyszerű napi ismétlődésűektől kezdve egészen a minden hónap utolsó péntekjén 9:00 és 17:00 között óránként ismétlődő eseményekig. |
| [![API-ikon][CallLogicApp-icon]<br/>**Logikai alkalmazás<br/>meghívása**][nested-logic-appdoc] | Meghív egy beágyazott logikai alkalmazást. A kérelem-eseményindítóval rendelkező logikai alkalmazások beágyazott logikai alkalmazásokként hívhatóak meg.| [![API-ikon][API/Web-Appicon]<br/>**API-alkalmazás**][api/web-appdoc] | Meghív egy App Service API-alkalmazást. A swaggerrel rendelkező API Apps-alkalmazások renderelése ugyanúgy történik, mint a többi elsőrangú művelet esetében.|

### <a name="standard-connectors"></a>Szokványos összekötők
Az alábbi táblázat tartalmazza a felhasználóink körében legnépszerűbb összekötőket:

| |  |  |  |
| --- | --- | --- | --- |
| [![API-ikon][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | Ha szeretné bármely feladatot a tárfiókjával automatizálni, akkor tekintse meg ezt az összekötőt. Támogatja a CRUD (create – létrehozás, read – olvasás, update – frissítés, delete – törlés) műveleteket. | [![API-ikon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Jelenleg ez az egyik legkeresettebb összekötő. Rendelkezik eseményindítókkal és műveletekkel, amelyek segítenek az érdeklődőkhöz kapcsolódó munkafolyamatok automatizálásában és számos egyébben. |
| [![API-ikon][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Az Event Hubon eseményeket használhat fel és tehet közzé. Az Event Hubs használatával például lekérheti a logikai alkalmazás kimenetét, majd elküldheti a kimenetet valamely valós idejű elemzési szolgáltatónak. | [![API-ikon][FTPicon]<br/>**FTP**][FTPdoc] | Ha az FTP-kiszolgálója elérhető az interneten keresztül, akkor automatizálhatja a fájlokkal és mappákkal végzett munkameneteket. <br/><br/>Az SFTP szintén elérhető az SFTP-összekötővel. |
| [![API-ikon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Rengeteg eseményindító és még több művelet, amelyekkel az Office 365 e-mailjeit és eseményeit a munkafolyamatain belül használhatja. <br/><br/>Ez az összekötő tartalmaz egy *jóváhagyó e-mailes* műveletet, amellyel jóváhagyhatja a szabadságkérelmeket, a kiadásokról szóló jelentéseket stb. <br/><br/>Az Office 365-felhasználók is elérhetők az Office 365 Users összekötővel.| [![API-ikon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Könnyen bejelentkezhet a Salesforce-fiókjával, és objektumokhoz – például érdeklődőkhöz – és sok egyébhez férhet hozzá. | 
| [![API-ikon][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | A logikai alkalmazásokon belüli legnépszerűbb összekötő. Olyan eseményindítókat és műveleteket tartalmaz, amelyekkel aszinkron üzenetkezelést végezhet, valamint közzétehet üzenetsorokat, előfizetéseket és témaköröket, illetve feliratkozhat rájuk. |  [![API-ikon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Ha olyan jellegű munkát végez a SharePointtal, amely során hasznos lenne az automatizálás, javasoljuk, hogy tekintse meg ezt az összekötőt. A helyszíni SharePointtal és a SharePoint Online-nal is használható. |
| [![API-ikon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Az egyik leggyakrabban használt összekötő, amely egy helyszíni SQL Server és egy Azure SQL Database összekapcsolására szolgál. | [![API-ikon][Twittericon]<br/>**Twitter**][Twitterdoc] | Könnyedén bejelentkezhet Twitter-fiók használatával, majd minden új Twitter-üzenet közzététele után elindíthat egy munkafolyamatot. Végül mentheti ezeket a Twitter-üzeneteket egy SQL-adatbázisba vagy SharePoint-listába. | 

### <a name="on-premises-connectors"></a>Helyszíni összekötők 

A helyszíni összekötők a helyszíni kiszolgálókon lévő adatokhoz biztosítanak hozzáférést.  A helyszíni kiszolgálóhoz történő kapcsolat létrehozásához olyan [helyszíni adatátjáróra][gatewaydoc] van szükség, amely biztonságos kommunikációs csatornát biztosít anélkül, hogy a hálózati infrastruktúrát konfigurálni kellene.  Példák ilyen összekötőkre:

|  |  |  |  |
| --- | --- | --- | --- |
| [![API-ikon][db2icon]<br/>**DB2**][db2doc] | [![API-ikon][oracle-DB-icon]<br/>**Oracle-adatbázis**][oracle-db-doc] | [![API-ikon][sharepointicon]<br/>**SharePoint</br> Server**][sharepointserver] | [![API-ikon][filesystem-icon]<br/>**Fájl-</br>rendszer**][filesystemdoc] |
[![API-ikon][sql-servericon]<br/>**SQL</br> Server**][sql-serverdoc] | ![API-ikon][Biztalk-Servericon]<br/>**BizTalk</br> Server**| |

### <a name="integration-account-connectors"></a>Integrációs fiókok összekötői 

Az Enterprise Integration Pack (EIP) olyan összekötőket tartalmaz, amelyeket a BizTalk Server közössége már régóta jól ismer. Amikor [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vásárol, a következő összekötőket is megkapja: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API-ikon][as2icon]<br/>**AS2-</br>dekódolás**][as2decode] | [![API-ikon][as2icon]<br/>**AS2-</br>kódolás**][as2encode] | [![API-ikon][x12icon]<br/>**EDIFACT-</br>dekódolás**][EDIFACTdecode] | [![API-ikon][x12icon]<br/>**EDIFACT-</br>kódolás**][EDIFACTencode] |
[![API-ikon][flatfileicon]<br/>**Egybesimított fájl</br> kódolása**][flatfiledoc] | [![API-ikon][flatfiledecodeicon]<br/>**Egybesimított fájl</br> dekódolása**][flatfiledecodedoc] | [![API-ikon][integrationaccounticon]<br/>**Integrációs<br/>fiók**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**XML-<br/>átalakítás**][xmltransformdoc] |
| [![API-ikon][x12icon]<br/>**X12-</br>dekódolás**][x12decode] | [![API-ikon][x12icon]<br/>**X12-</br>kódolás**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**XML-<br/>érvényesítés**][xmlvalidatedoc] | |

### <a name="enterprise-connectors"></a>Vállalati összekötők

Logikai alkalmazásain belül csatlakozhat vállalati alkalmazásaihoz.

|  |  |
| --- | --- |
|[![API-ikon][MQicon]<br/>**MQ**][mqdoc]|[![API-ikon][SAPicon]<br/>**SAP**][sapconnector]|

> [!TIP]
> Ha az Azure Logic Appst az Azure-fiók regisztrálása előtt szeretné kipróbálni, folytassa [a Logic Apps kipróbálásával](https://tryappservice.azure.com/?appservice=logic). Azonnal létrehozhat egy rövid életű, kezdő szintű logikai alkalmazást. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.

## <a name="connectors-as-triggers-and-actions"></a>Eseményindítóként és műveletként használható összekötők

Egy **eseményindító** elindul, vagy lefuttatja a logikai alkalmazása egy példányát. Egyes összekötők eseményindítókat biztosítanak, amelyek adott események bekövetkezése esetén értesítik az alkalmazást. Az FTP-összekötő például az `OnUpdatedFile` eseményindítót tartalmazza, amely egy fájl frissítésekor elindítja a logikai alkalmazást. 

A logikai alkalmazások a következő eseményindító-típusokat tartalmazzák:  

* *Lekérdezéses eseményindítók*: ezek az eseményindítók a beállított időközönként új adatokat kereső lekérdezést küldenek a szolgáltatásnak. 

    Ha vannak új adatok, elindul a logikai alkalmazás egy új példánya, benne az új adatokkal. 

* *Leküldéses eseményindítók*: ezek az eseményindítók azt figyelik, hogy mikor jelennek meg adatok egy végponton, vagy mikor megy végbe egy adott esemény, majd elindítják a logikai alkalmazás egy új példányát.

* *Ismétlődés eseményindító*: ez az eseményindító előre meghatározott ütemezés szerint létrehozza a logikai alkalmazás egy példányát.

Az összekötők **műveleteket** is biztosítanak, amelyek felhasználhatók a munkafolyamatban. A logikai alkalmazás például adatokat kereshet ki, amelyeket Ön később felhasználhat a logikai alkalmazásban. Pontosabban ügyféladatokat kereshet például egy SQL-adatbázisban, majd az ügyféladatok felhasználásával létrehozhatja a munkafolyamatot. 

> [!TIP]
> Az [összekötők áttekintését](connectors-overview.md) nyújtó témakörben további részleteket olvashat az eseményindítókról és a műveletekről. 


## <a name="message-manipulation-actions"></a>Üzenetkezelési műveletek

A logikai alkalmazások beépített műveleteket tartalmaznak, amelyek a hasznos adatok módosítására vagy kezelésére szolgálnak. A beépített **Adatműveletek** összekötő az alábbi műveleteket tartalmazza: 

| | |
|---|---|
| **Összeállítás** | Létrehozhat vagy előállíthat értékeket vagy objektumokat, amelyeket később, vagy akár a munkafolyamat létrehozása alatt is felhasználhat. Létrehozhat például egy JSON-objektumot több lépésből származó értékekkel, vagy kiszámíthat egy állandó értéket, amelyre később, a logikai alkalmazás futása során hivatkozhat. |
| **CSV-táblázat létrehozása**<br/>**HTML-táblázat létrehozása** | Egy tömb eredménykészletét átviheti egy CSV- vagy HTML-táblázatba. Hozzáadhatja például a CRM „Rekordok listába rendezése” műveletét, majd szűrheti a bejegyzéseket az adott napon írtakra. Végül elküldheti a végeredményt HTML-táblázatként egy e-mailben. |
| **Tömb szűrése** (lekérdezés) | Szűrhet az Önt érdeklő bejegyzések eredménykészletére. Kereshet például az `#Azure` kifejezést tartalmazó Twitter-üzenetek között, majd a kapott üzeneteket „szűrheti” úgy, hogy csak a `Tweeted_by_followers > 50` feltételnek megfelelő eredményeket adja ki. |
| **Csatlakozás** | Tömböt csatlakoztathat valamilyen elválasztóval. A Kulcskifejezések észlelése művelet például egy kulcskifejezésekből álló tömböt ad vissza. Ezeket „csatlakoztathatja” egy `,` vagy hasonló karakter segítségével. Tehát `["Some", "Phrase"]` helyett `"Some, Phrase"` értékkel rendelkezik majd. |
| **JSON elemzése** | Elérheti és elemezheti a tervezőben található JSON-objektumok értékeit. Ha az Azure Function például hasznos JSON-adatokat ad vissza, akkor elemezheti azokat, hogy hozzáférhessen a JSON tulajdonságaihoz egy későbbi lépésben. Ez a művelet azt is ellenőrzi, hogy a JSON egyezik-e a futásidő során meghatározott sémával. | 
| **Kiválasztás** | Kiválaszthatja egy tömb bizonyos tulajdonságait további feldolgozás céljából. Ha az SQL-ben a „Rekordok listába rendezése” parancs 15 oszlopot ad vissza, akkor kiválaszthat ezek közül néhányat a további feldolgozás érdekében. A kimenet olyan tömb, amely csak a kiválasztott tulajdonságokat tartalmazza. |

## <a name="custom-connectors-and-azure-certification"></a>Egyéni összekötők és Azure-hitelesítés 

Ha szeretne hívásokat indítani olyan API-kba, amelyek egyéni kóddal rendelkeznek, vagy nem érhetők el beépített összekötőkként, kiterjesztheti a Logic Apps-platformot [REST-alapú API-alkalmazások létrehozásával](../logic-apps/logic-apps-create-api-app.md). Létrehozhatja saját [egyéni összekötőit](../logic-apps/custom-connector-overview.md) is, amelyeket az előfizetéshez tartozó összes logikai alkalmazás számára elérhetővé tehet.

Ha szeretné, hogy egyéni API-alkalmazása nyilvános és az Azure-ban elérhető legyen, akkor [küldje be összekötőit hitelesítésre a Microsofthoz](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre a [Logic Apps felhasználói visszajelzéseinek oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

Nem talál valamilyen összekötővel kapcsolatos témakört, illetve fontosnak tartott részletet? Ha nem, akkor meglévő témaköreinket kiegészítve segíthet nekünk, illetve sajátot is készíthet. Dokumentációnk nyílt forrás, amelyet a GitHubon találhat. Első lépések a [GitHub-adattárban](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>További lépések
* [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Egyéni API-k létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-create-api-app.md)
* [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Kapcsolódás helyszíni adatforrásokhoz a helyszíni adatátjáróval rendelkező logikai alkalmazásokból"
[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Logikai appok integrálása App Service API Apps-alkalmazásokkal"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "A blobtárolókban található fájlok kezelése az Azure Blob Storage-összekötővel"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Logikai alkalmazások integrálása Azure Functions-függvényekkel"
[db2doc]: ./connectors-create-api-db2.md "Csatlakozhat az IBM DB2-höz a felhőben vagy a helyszínen. Sorokat frissíthet, táblákat kérhet le, és egyéb műveleteket is végrehajthat"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Csatlakozhat a Dynamics CRM Online szolgáltatáshoz, és használhatja a CRM Online adatait"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Csatlakozhat az Azure Event Hubs szolgáltatáshoz. Eseményeket fogadhat és küldhet a logikai alkalmazások és az Event Hubs között"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Csatlakozhat egy helyszíni fájlrendszerhez"
[ftpdoc]: ./connectors-create-api-ftp.md "Csatlakozhat egy FTP-/FTPS-kiszolgálóhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlokat törölhet, tölthet fel vagy kérhet le"
[httpdoc]: ./connectors-native-http.md "HTTP-hívások indítása HTTP-összekötővel"
[http-requestdoc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "HTTP-hívások indítása HTTP + Swagger összekötővel"
[informixdoc]: ./connectors-create-api-informix.md "Csatlakozhat az Informixhez a felhőben vagy a helyszínen. Sorokat olvashat el, táblákat listázhat, és egyéb műveleteket is végrehajthat"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Logikai alkalmazások integrálása beágyazott munkafolyamatokkal"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Csatlakozhat Office 365-fiókjához. E-maileket küldhet és fogadhat, kezelheti a naptárát és névjegyeit, és egyéb műveleteket is végrehajthat"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Csatlakozhat egy Oracle-adatbázishoz, ahol többek között sorokat adhat hozzá, szúrhat be és törölhet"
[mqdoc]: ./connectors-create-api-mq.md "Csatlakozás az MQ-hoz a helyszínen vagy az Azure-on keresztül, és üzenetek küldése és fogadása"
[recurrencedoc]:  ./connectors-native-recurrence.md "Ismétlődő műveletek kiváltása logikai alkalmazásokhoz"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Csatlakozhat Salesforce-fiókjához. Kezelheti fiókjait, az érdeklődőket, üzleti lehetőségeit és egyéb elemeket"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Csatlakozhat egy helyszíni SAP-rendszerhez"
[service-busdoc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témakörökből, valamint fogadhatja a Service Bus-üzenetsorok és -előfizetések üzeneteit."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Csatlakozhat a SharePoint Online-hoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Csatlakozhat a helyszíni SharePoint-kiszolgálóhoz. Dokumentumokat kezelhet, elemeket listázhat és egyéb műveleteket is végrehajthat"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Csatlakozhat az Azure SQL Database-hez vagy az SQL Serverhez. SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet."
[twitterdoc]: ./connectors-create-api-twitter.md "Csatlakozhat a Twitterhez. Idővonal-tartalmakat fogadhat, tweeteket tehet közzé, és egyéb műveleteket is végrehajthat"
[webhookdoc]: ./connectors-native-webhook.md "Webhook-műveletek és -eseményindítók hozzáadása a logikai alkalmazásokhoz"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Megismerheti a vállalati integrációs AS2-t."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Megismerheti a vállalati integrációs X12-t."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Megismerheti a vállalati integrációs egybesimított fájlt."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Megismerheti a vállalati integrációs XML-hitelesítést."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Megismerheti a vállalati integrációs átalakításokat."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Megismerheti a vállalati integrációs AS2-dekódolást."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Megismerheti a vállalati integrációs AS2-kódolást."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Megismerheti a vállalati integrációs X12-dekódolást."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Megismerheti a vállalati integrációs X12-kódolást."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Megismerheti a vállalati integrációs EDIFACT-dekódolást."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Megismerheti a vállalati integrációs EDIFACT-kódolást."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Sémákat, térképeket, partnereket és egyebeket kereshet ki az integrációs fiókjában"


[boxDoc]: ./connectors-create-api-box.md "Csatlakozhat a Boxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[delaydoc]: ./connectors-native-delay.md "Késleltetett műveletek végrehajtása"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Csatlakozhat a Dropboxhoz. Fájlokat tölthet fel, kérhet le, törölhet, listázhat, és egyéb műveleteket is végrehajthat"
[facebookdoc]: ./connectors-create-api-facebook.md "Csatlakozhat a Facebookhoz. Bejegyzéseket tehet közzé az idővonalon, laptartalmakat kérhet le, és egyéb műveleteket is végrehajthat"
[githubdoc]: ./connectors-create-api-github.md "Csatlakozhat a GitHubhoz, és különböző problémákat követhet nyomon"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Csatlakozhat a Google Drive-hoz, és használhatja annak adatait"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Csatlakozhat a Google Táblázatokhoz, és módosíthatja a táblázatait"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Csatlakozhat a Google Teendőkhöz, ahol különböző teendőit kezelheti"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "A Google Naptárhoz csatlakozhat, és felügyelheti a naptárat."
[http-responsedoc]: ./connectors-native-reqres.md "HTTP-kérésekre és -válaszokra vonatkozó műveletek hozzáadása logikai alkalmazásaihoz"
[instagramdoc]: ./connectors-create-api-instagram.md "Csatlakozhat az Instagramhoz. Eseményeket indíthat vagy reagálhat rájuk"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Csatlakozhat MailChimp-fiókjához. Kezelheti és automatizálhatja e-mailjeit"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Csatlakozhat a Mandrillhoz, ha azon keresztül szeretne kommunikálni"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Csatlakozhat a Microsoft Translatorhoz. Szövegeket fordíthat, nyelveket ismerhet fel, és egyéb műveleteket is végrehajthat" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Videoadatokat, videólistákat és csatornákat kérhet le, valamint Office 365-videók lejátszási URL-címeit"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Csatlakozhat személyes Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Csatlakozhat vállalati Microsoft OneDrive-fiókjához. Fájlokat tölthet fel, törölhet, listázhat, és egyéb műveleteket is elvégezhet"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Csatlakozhat Outlook-postaládájához. Kezelheti az e-mailjeit, naptárait, névjegyeit és egyéb tartalmait"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Csatlakozhat a Microsoft Project Online-hoz. Kezelheti a projektjeit, feladatait, erőforrásait és egyéb tartalmait"
[querydoc]: ./connectors-native-query.md "Tömbök kiválasztása és szűrése a Lekérdezés művelettel"
[rssdoc]: ./connectors-create-api-rss.md "Hírcsatorna-elemek közzététele és lekérése, illetve műveletek kiváltása, ha egy új elemet tesznek közzé egy RSS-hírcsatornán."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Csatlakozhat a SendGridhez. E-maileket küldhet, és kezelheti a címzettek listáit"
[sftpdoc]: ./connectors-create-api-sftp.md "Csatlakozhat SFTP-fiókjához. Fájlokat tölthet fel, kérhet le, törölhet, és egyéb műveleteket is végrehajthat"
[slackdoc]: ./connectors-create-api-slack.md "Csatlakozhat a Slackhez, és üzeneteket tehet közzé a Slack-csatornákon"
[smtpdoc]: ./connectors-create-api-smtp.md "Csatlakozhat egy SMTP-kiszolgálóhoz, és mellékleteket tartalmazó e-maileket küldhet"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Csatlakozhat a SparkPosthoz, ha azon keresztül szeretne kommunikálni"
[trellodoc]: ./connectors-create-api-trello.md "Csatlakozhat a Trellóhoz. Kezelheti a projektjeit és bármit és bárkivel megszervezhet"
[twiliodoc]: ./connectors-create-api-twilio.md "Csatlakozhat a Twilióhoz. Üzeneteket küldhet, lekérheti az elérhető számokat, kezelheti a bejövő hívások telefonszámait, és egyéb műveleteket is végrehajthat"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Csatlakozhat a Wunderlisthez. Kezelheti a feladatait és teendőlistáit, szinkronizálhatja különböző tartalmait, és egyéb műveleteket is végrehajthat"
[yammerdoc]: ./connectors-create-api-yammer.md "Csatlakozhat a Yammerhez. Üzenetek tehet közzé, új üzeneteket fogadhat, és egyéb műveleteket is végrehajthat"
[youtubedoc]: ./connectors-create-api-youtube.md "Csatlakozhat a YouTube-hoz. Kezelheti a videóit és csatornáit"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
