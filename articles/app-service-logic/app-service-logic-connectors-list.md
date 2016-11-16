---
title: "A rendelkezésre álló összekötők és API Apps-lista | Microsoft Docs"
description: "Az Azure App Service-ben rendelkezésre álló összekötők és az API Apps megismerése"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: cgronlun
ms.assetid: 984a425d-ba64-48cc-90dc-bb624411e0f0
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6028fc53661978f63bbac89be6214ffebdc22405


---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>A Logic Apps-alkalmazásokban használható összekötők és API-alkalmazások listája
> [!NOTE]
> A cikk e verziója a Logic Apps 2014. 12. 01. dátumú előnézeti sémaverziójára vonatkozik. A Logic Apps általánosan elérhető (GA) verziójával kapcsolatban tekintse meg az [új összekötők listáját](../connectors/apis-list.md).
> 
> 

A cikkben leírtak alapján megismerheti a Microsoft által a Logic Apps szolgáltatásban való használatra létrehozott összekötőket és API-alkalmazásokat.

Ha szeretne további tájékoztatáshoz jutni az árakról, illetve kíváncsi rá, hogy mit tartalmaznak az egyes szolgáltatásszintek, látogasson el az [Azure App Service Díjszabás](https://azure.microsoft.com/pricing/details/app-service/) című weblapra.

> [!NOTE]
> Ha a Logic Apps-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, lépjen [a Logic Apps kipróbálását](https://tryappservice.azure.com/?appservice=logic) lehetővé tevő oldalra. Itt azonnal létrehozhat egy rövid életű, kezdő szintű logikai alkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="core-connectors"></a>Magösszekötők
Az alábbi táblázatban megtalálja az összes, a Microsoft által létrehozott és magösszekötőként felhasználható összekötőt és API-alkalmazást:

| Név | Leírás |
| --- | --- |
| [Bing Fordító](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |Szöveg fordítása más nyelvre a Bing segítségével. |
| [HTTP](app-service-logic-connector-http.md) |A HTTP-figyelő megnyit egy végpontot, amely HTTP-kiszolgálóként üzemel, és figyeli a beérkező HTTP- vagy HTTPS-kéréseket. A HTTP-művelet nem igényel API-alkalmazást, ezt a funkciót a Logic Apps natívan támogatja. |
| [Slack](app-service-logic-connector-slack.md) |Csatlakozás a Slackhez, illetve üzenetek közzététele a Slack-csatornákban. |

## <a name="enterprise-integration-connectors"></a>Vállalati integrációs összekötők
Az alábbi táblázatban megtalálja az összes, a Microsoft által létrehozott és vállalati integrációs összekötőként felhasználható összekötőt és API-alkalmazást:

| Név | Leírás |
| --- | --- |
| [BizTalk Rules](app-service-logic-use-biztalk-rules.md) |A BizTalk Rules a vállalaton belüli üzleti logikai meghatározására és vezérlésére használható. Az üzleti szabályzatok újrafordítás és a társított alkalmazások újratelepítése nélkül is frissíthetők. |
| [BizTalk XPath Extractor](app-service-logic-xpath-extract.md) |Az Ön által megválasztott XPath alapján megkeresi az XML-tartalmakat, és kinyeri belőlük az adatokat. |
| [DB2-összekötő](app-service-logic-connector-db2.md) |Csatlakozik egy helyszíni IBM DB2-adatbázishoz és egy Windows operációs rendszeren futó Azure virtuális géphez. Képes a webes API- és OData API-műveleteket Informix strukturált lekérdezésinyelv-parancsokhoz párosítani. <br/><br/>Nincsenek eseményindítók. A műveletek a következők lehetnek: táblázat kiválasztása, beillesztés, frissítés, törlés és egyéni utasítás.<br/><br/>Ez az összekötő tartalmazza a Microsoft DRDA-ügyfelét is, amelynek segítségével TCP/IP-hálózaton keresztül is csatlakozhat az Informix-kiszolgálókhoz. |
| [Fájl](app-service-logic-connector-file.md) |Ezzel az összekötővel csatlakozhat a helyszíni fájlrendszerhez vagy hálózathoz, és különféle műveleteket hajthat végre, például fájlok feltöltését, törlését, listázását stb. |
| [Informix](app-service-logic-connector-informix.md) |Egy helyszíni IBM Informix-adatbázishoz és egy Windows operációs rendszeren futó Azure virtuális géphez csatlakozik. Képes a webes API- és OData API-műveleteket Informix strukturált lekérdezésinyelv-parancsokhoz párosítani.<br/><br/>Nincsenek eseményindítók. A műveletek a következők lehetnek: táblázat kiválasztása, beillesztés, frissítés, törlés és egyéni utasítás.<br/><br/>Helyszíni használat esetén a VPN vagy az Azure ExpressRoute is igénybe vehető. Ez az összekötő tartalmazza a Microsoft DRDA-ügyfelét is, amelynek segítségével TCP/IP-hálózaton keresztül is csatlakozhat az Informix-kiszolgálókhoz. |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |Helyszíni SQL Server és Azure SQL Database összekapcsolására szolgál. Az összekötővel SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet. |
| MQ |Egy helyszíni, 8-as verziójú IBM WebSphere MQ Server kiszolgálóhoz és egy Windows operációs rendszeren futó Azure virtuális géphez csatlakozik. Helyszíni használat esetén a VPN vagy az Azure ExpressRoute is igénybe vehető. Az összekötő a Microsoft MQ-ügyfelét is tartalmazza.<br/><br/>Nincsenek eseményindítók. Nincsenek műveletek.<br/><br/>**Megjegyzés:** Jelenleg nem használható a Logic Apps szolgáltatásban. |

## <a name="connectors-as-triggers"></a>Eseményindítóként használható összekötők
Számos összekötő eseményindítókat biztosít a Logic Apps szolgáltatásban való használatra. Ezek az eseményindítók két csoportba sorolhatók:

1. Lekérdezéses eseményindítók: ezek az eseményindítók a beállított időközönként új adatokat kereső lekérdezést küldenek a szolgáltatásnak. Ha vannak új adatok, elindul a logikai alkalmazás új példánya, benne az új adatokkal. Ha meg szeretné akadályozni, hogy ugyanazokat az adatokat többször is feldolgozza a rendszer, elérheti, hogy az eseményindító megtisztítsa azokat az adatokat, amelyeket a logikai alkalmazás már beolvasott. Ilyen összekötő például a Fájl, az SQL vagy az Azure Storage.
2. Leküldéses eseményindítók: ezek az eseményindítók azt figyelik, hogy mikor jelennek meg adatok egy végponton, vagy mikor megy végbe egy adott esemény. Ha bekövetkezik, amit vártak, elindítják a logikai alkalmazás egy új példányát. Ilyen összekötő például a HTTP Listener és a Twitter.

## <a name="connectors-as-actions"></a>Műveletként használható összekötők
Az összekötők a logikai alkalmazásban műveletként is felhasználhatók. A műveletek segítségével adatokat kereshet a logikai alkalmazásban, amelyeket aztán a program felhasznál a végrehajtáshoz. Előfordulhat például, hogy az ügyfelekkel kapcsolatos további adatokat kell megkeresnie egy SQL-adatbázisban egy rendelés feldolgozása céljából. Vagy előfordulhat, hogy a célhelyen kell adatokat írnia, frissítenie vagy törölnie. Ezt mind megteheti az összekötők által kínált műveletekkel. A műveletek az API-alkalmazásokban szereplő műveletekhez társulnak (a Swagger-metaadatok által meghatározott módon).

## <a name="create-your-own-connectors-and-api-apps"></a>Saját összekötők és API-alkalmazások létrehozása
[Connectors and API Apps Reference (Összekötők és API-alkalmazások referenciája)](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API app triggers (Azure App Service API-alkalmazások eseményindítói)](../app-service-api/app-service-api-dotnet-triggers.md)  
[Logic App Reference (Segédanyagok Logic Apps-alkalmazásokhoz)](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>További információk az összekötőkről és az API-alkalmazásokról
[Mik azok az összekötők és a BizTalk API-alkalmazások?](app-service-logic-what-are-biztalk-api-apps.md)  
[Using the Hybrid Connection Manager in Azure App Service (A Hybrid Connection Manager használata az Azure App Services-ben)](app-service-logic-hybrid-connection-manager.md)  
[Manage and Monitor your built-in API Apps and Connectors (A beépített API-alkalmazások és összekötők kezelése és figyelése)](app-service-logic-monitor-your-connectors.md)




<!--HONumber=Nov16_HO2-->


