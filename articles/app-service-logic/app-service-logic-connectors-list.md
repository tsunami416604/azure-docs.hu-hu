<properties
    pageTitle="A rendelkezésre álló összekötők és API Apps-lista | Microsoft Azure App Service"
    description="Az Azure App Service-ben rendelkezésre álló összekötők és az API Apps megismerése"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>



# A Logic Apps-alkalmazásokban használható összekötők és API-alkalmazások listája
>[AZURE.NOTE] A cikk e verziója a Logic Apps 2014. 12. 01. dátumú előnézeti sémaverziójára vonatkozik. A Logic Apps általánosan elérhető (GA) verziójával kapcsolatban tekintse meg az [új összekötők listáját](../connectors/apis-list.md).

A cikkben leírtak alapján megismerheti a Microsoft által a Logic Apps szolgáltatásban való használatra létrehozott összekötőket és API-alkalmazásokat.

Ha szeretne további tájékoztatáshoz jutni az árakról, illetve kíváncsi rá, hogy mit tartalmaznak az egyes szolgáltatásszintek, látogasson el az [Azure App Service Díjszabás](https://azure.microsoft.com/pricing/details/app-service/) című weblapra.

> [AZURE.NOTE] Ha a Logic Apps-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, lépjen [a Logic Apps kipróbálását](https://tryappservice.azure.com/?appservice=logic) lehetővé tevő oldalra. Itt azonnal létrehozhat egy rövid életű, kezdő szintű logikai alkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.

## Magösszekötők
Az alábbi táblázatban megtalálja az összes, a Microsoft által létrehozott és magösszekötőként felhasználható összekötőt és API-alkalmazást:

Név | Leírás
--- | ---
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Képes üzeneteket küldeni a Service Bus-üzenetsorokból és -témákból, valamint üzeneteket fogadni a Service Bus-üzenetsorok és  -előfizetések felől.
[Bing Fordító](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Szöveg fordítása más nyelvre a Bing segítségével.
[HTTP](app-service-logic-connector-http.md) | A HTTP-figyelő megnyit egy végpontot, amely HTTP-kiszolgálóként üzemel, és figyeli a beérkező HTTP- vagy HTTPS-kéréseket. A HTTP-művelet nem igényel API-alkalmazást, ezt a funkciót a Logic Apps natívan támogatja.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Az Office 365-összekötő képes a felhasználó Office 365-fiókját használva küldeni és fogadni e-maileket, illetve kezelni a naptárt és a névjegyeket.
[Slack](app-service-logic-connector-slack.md) | Csatlakozás a Slackhez, illetve üzenetek közzététele a Slack-csatornákban.


## Vállalati integrációs összekötők
Az alábbi táblázatban megtalálja az összes, a Microsoft által létrehozott és vállalati integrációs összekötőként felhasználható összekötőt és API-alkalmazást:

Név  | Leírás
------------- | -------------
[AS2-összekötő](app-service-logic-connector-as2.md) | Az AS2 átviteli protokollal történő üzenetfogadásra és -küldésre használható. Az adatok átvitele biztonságosan és megbízhatóan, digitális tanúsítványok és titkosítás használatával történik.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | A vállalatközi kommunikációban használt EDIFACT-protokollal fogad és küld üzeneteket.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Együttműködési képességet biztosít a strukturálatlan fájladatok (például Excel- vagy CSV-fájlok) és az XML-adatok között. Ez az API-alkalmazás képes strukturálatlan fájlpéldányokat XML-lé konvertálni és fordítva.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Kódoló és dekóder, amely segít együttműködési képességet biztosítani az alkalmazásnak a JSON- és az XML-adatok között. Képes adott JSON-példányt XML-lé konvertálni  és fordítva.
[BizTalk Rules](app-service-logic-use-biztalk-rules.md) | A BizTalk Rules a vállalaton belüli üzleti logikai meghatározására és vezérlésére használható. Az üzleti szabályzatok újrafordítás és a társított alkalmazások újratelepítése nélkül is frissíthetők.
[BizTalk Trading Partner Management](app-service-logic-connector-tpm.md) | Vállalatközi kapcsolatok meghatározására és fenntartására használható a partnerek, egyezmények, illetve a megállapodásokban használt sémák és tanúsítványok segítségével. Ezeket a kapcsolatokat az AS2, EDIFACT és X12 API-alkalmazások foganatosítják.
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | Egy adott formátumból más formátumba konvertálja az adatokat. Ezenfelül meglévő térkép (.trfm-fájl) feltöltésére, a forrás- és célsémák közötti kapcsolatok megtekintésére, valamint a „Tesztelés” funkciónak a mintaként használt beviteli XML-tartalmakon való lefuttatására is használható. Ezenfelül különféle beépített funkciók is elérhetők, például karakterlánc-feldolgozás, feltételes hozzárendelés stb.
[BizTalk X12](app-service-logic-connector-x12.md) | A vállalatközi kommunikációban használt X12-protokoll segítségével fogad és küld üzeneteket.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | XML-adatoknak az előre definiált XML-sémák alapján történő ellenőrzésére szolgál. Használhat meglévő sémákat, vagy akár saját sémákat is generálható strukturálatlan fájlpéldányok, JSON-példányok vagy meglévő összekötők alapján.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Az Ön által megválasztott XPath alapján megkeresi az XML-tartalmakat, és kinyeri belőlük az adatokat.
[DB2-összekötő](app-service-logic-connector-db2.md) | Csatlakozik egy helyszíni IBM DB2-adatbázishoz és egy Windows operációs rendszeren futó Azure virtuális géphez. Képes a webes API- és OData API-műveleteket Informix strukturált lekérdezésinyelv-parancsokhoz párosítani. <br/><br/>Nincsenek eseményindítók. A műveletek a következők lehetnek: táblázat kiválasztása, beillesztés, frissítés, törlés és egyéni utasítás.<br/><br/>Ez az összekötő tartalmazza a Microsoft DRDA-ügyfelét is, amelynek segítségével TCP/IP-hálózaton keresztül is csatlakozhat az Informix-kiszolgálókhoz.
[Fájl](app-service-logic-connector-file.md) | Ezzel az összekötővel csatlakozhat a helyszíni fájlrendszerhez vagy hálózathoz, és különféle műveleteket hajthat végre, például fájlok feltöltését, törlését, listázását stb.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Ezzel az összekötővel csatlakozhat FTP-/FTPS-kiszolgálókhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlok feltöltését, lekérését, törlését stb.
[Informix](app-service-logic-connector-informix.md) | Egy helyszíni IBM Informix-adatbázishoz és egy Windows operációs rendszeren futó Azure virtuális géphez csatlakozik. Képes a webes API- és OData API-műveleteket Informix strukturált lekérdezésinyelv-parancsokhoz párosítani.<br/><br/>Nincsenek eseményindítók. A műveletek a következők lehetnek: táblázat kiválasztása, beillesztés, frissítés, törlés és egyéni utasítás.<br/><br/>Helyszíni használat esetén a VPN vagy az Azure ExpressRoute is igénybe vehető. Ez az összekötő tartalmazza a Microsoft DRDA-ügyfelét is, amelynek segítségével TCP/IP-hálózaton keresztül is csatlakozhat az Informix-kiszolgálókhoz.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Helyszíni SQL Server és Azure SQL Database összekapcsolására szolgál. Az összekötővel SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet.
MQ | Egy helyszíni, 8-as verziójú IBM WebSphere MQ Server kiszolgálóhoz és egy Windows operációs rendszeren futó Azure virtuális géphez csatlakozik. Helyszíni használat esetén a VPN vagy az Azure ExpressRoute is igénybe vehető. Az összekötő a Microsoft MQ-ügyfelét is tartalmazza.<br/><br/>Nincsenek eseményindítók. Nincsenek műveletek.<br/><br/>**Megjegyzés:** Jelenleg nem használható a Logic Apps szolgáltatásban.

## Eseményindítóként használható összekötők
Számos összekötő eseményindítókat biztosít a Logic Apps szolgáltatásban való használatra. Ezek az eseményindítók két csoportba sorolhatók:

1. Lekérdezéses eseményindítók: ezek az eseményindítók a beállított időközönként új adatokat kereső lekérdezést küldenek a szolgáltatásnak. Ha vannak új adatok, elindul a logikai alkalmazás új példánya, benne az új adatokkal. Ha meg szeretné akadályozni, hogy ugyanazokat az adatokat többször is feldolgozza a rendszer, elérheti, hogy az eseményindító megtisztítsa azokat az adatokat, amelyeket a logikai alkalmazás már beolvasott. Ilyen összekötő például a Fájl, az SQL vagy az Azure Storage.
2. Leküldéses eseményindítók: ezek az eseményindítók azt figyelik, hogy mikor jelennek meg adatok egy végponton, vagy mikor megy végbe egy adott esemény. Ha bekövetkezik, amit vártak, elindítják a logikai alkalmazás egy új példányát. Ilyen összekötő például a HTTP Listener és a Twitter.

## Műveletként használható összekötők
Az összekötők a logikai alkalmazásban műveletként is felhasználhatók. A műveletek segítségével adatokat kereshet a logikai alkalmazásban, amelyeket aztán a program felhasznál a végrehajtáshoz. Előfordulhat például, hogy az ügyfelekkel kapcsolatos további adatokat kell megkeresnie egy SQL-adatbázisban egy rendelés feldolgozása céljából. Vagy előfordulhat, hogy a célhelyen kell adatokat írnia, frissítenie vagy törölnie. Ezt mind megteheti az összekötők által kínált műveletekkel. A műveletek az API-alkalmazásokban szereplő műveletekhez társulnak (a Swagger-metaadatok által meghatározott módon).

## Saját összekötők és API-alkalmazások létrehozása
[Connectors and API Apps Reference (Összekötők és API-alkalmazások referenciája)](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API app triggers (Azure App Service API-alkalmazások eseményindítói)](../app-service-api/app-service-api-dotnet-triggers.md)  
[Logic App Reference (Segédanyagok Logic Apps-alkalmazásokhoz)](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## További információk az összekötőkről és az API-alkalmazásokról
[Mik azok az összekötők és a BizTalk API-alkalmazások?](app-service-logic-what-are-biztalk-api-apps.md)  
[Using the Hybrid Connection Manager in Azure App Service (A Hybrid Connection Manager használata az Azure App Services-ben)](app-service-logic-hybrid-connection-manager.md)  
[Manage and Monitor your built-in API Apps and Connectors (A beépített API-alkalmazások és összekötők kezelése és figyelése)](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Sep16_HO4-->


