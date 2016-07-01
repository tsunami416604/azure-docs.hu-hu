<properties 
    pageTitle="Mik azok az összekötők és a BizTalk API-alkalmazások?" 
    description="Az API-alkalmazások, az összekötők és a BizTalk API-alkalmazások megismerése" 
    services="app-service\logic" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="04/20/2016" 
    ms.author="mandia"/>

# Mik azok az összekötők és a BizTalk API-alkalmazások?

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Az Azure App Services szolgáltatás a bővíthetőség és a széles körű összekapcsolhatóság elveire épült. Ezeket a funkciókat az API-alkalmazások biztosítják. Az *összekötők* olyan API-alkalmazások, amelyek az összekapcsolhatóságra fókuszálnak. Az összekötők a többi API-alkalmazáshoz hasonlóan a Web Apps, a Mobile Apps és a Logic Apps szolgáltatáson keresztül érhetők el. Az összekötők segítenek a meglévő szolgáltatásokhoz való kapcsolódásban, illetve a hitelesítés kezelésében, ezenfelül figyelést, elemzési lehetőségeket és más hasznos funkciókat is biztosítanak.

Bármelyik fejlesztő megalkothatja saját API-alkalmazását, amelyet aztán bevezethet saját rendszerében. A jövőben a fejlesztők megoszthatják saját API-alkalmazásaikat a piactéren, és profitálhatnak is belőlük. 

![API-alkalmazások piactere](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

Hogy a fejlesztők gyorsabban alkothassák meg Azure App Service-alapú megoldásaikat, az Azure-csapat új összekötőket adott a piactérhez, amelyek számos gyakori alkalmazási helyzet kezelésére alkalmasak. Ezenfelül több Premium és BizTalk funkciót is elérhetővé tettünk, hogy az App Service összetett és speciális integrálási helyzetek kezelésére is alkalmas legyen.

Az Azure App Service-ben több különböző szolgáltatáscsomag érhető el. Minden csomagban elérhető az összes összekötő és API-alkalmazás, illetve ezek minden funkciója.  

Az [App Service szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/app-service/) oldalon megtalálja ezeket a csomagokat, és azt, hogy mit kínálnak az egyes csomagok. Az alábbi bekezdésekben a BizTalk API-alkalmazások és összekötők különböző kategóriáit ismertetjük.


## Hibrid összekötők 
A hibrid összekötők révén az App Services szolgáltatás mélyebb vállalati szinteken is bevethető, mivel ezen összekötők segítségével kapcsolat létesíthető [SAP-](app-service-logic-connector-sap.md), [Oracle-](app-service-logic-connector-oracle.md), [DB2-](app-service-logic-connector-db2.md), [Informix-](app-service-logic-connector-informix.md) és WebSphere MQ-rendszerekkel. 

## EAI- és EDI-szolgáltatások
A vállalat szempontjából létfontosságú alkalmazások megalkotásához nem csupán a kapcsolat kialakításának lehetősége szükséges. A BizTalk Serveren, a Microsoft piacvezető integrációs platformján alapuló BizTalk API-alkalmazások fejlett integrációs funkciókat tesznek elérhetővé, amelyek könnyedén beilleszthetők webes, mobil- és logikai alkalmazásaiba. Az integrációs funkciók között a következőket találjuk: [Érvényesítés](app-service-logic-xml-validator.md), [Kinyerés](app-service-logic-xpath-extract.md), [Átalakítás](app-service-logic-transform-xml-documents.md), [Kódolók](app-service-logic-connector-jsonencoder.md), [Kereskedelmipartner-kezelés](app-service-logic-connector-tpm.md) és EDI-formátumok ([X12](app-service-logic-connector-x12.md), [EDIFACT](app-service-logic-connector-edifact.md) és [AS2](app-service-logic-connector-as2.md)) támogatása.

További források: [Business-to-business connectors and API apps](app-service-logic-b2b-connectors.md) (Vállalatközi összekötők és API-alkalmazások)  
[Create a B2B process (Vállalatközi folyamat létrehozása)](app-service-logic-create-a-b2b-process.md)  
[Kereskedelmipartner-egyezmény létrehozása](app-service-logic-create-a-trading-partner-agreement.md)  
[Track your B2B messages (Vállalatközi üzenetek nyomon követése)](app-service-logic-track-b2b-messages.md)  


## Szabályok
Az üzleti szabályok tartalmazzák az üzleti folyamatokat vezérlő szabályzatokat és döntéseket. Ezek a szabályok általában dinamikusan változnak az idők során, követve az üzleti terveket, a szabályozókat és az egyéb releváns körülményeket. Az [App Services szolgáltatásban elérhető BizTalk-szabályok](app-service-logic-use-biztalk-rules.md) segítségével leválaszthatja a szabályokat az alkalmazás kódjáról, ami jelentősen leegyszerűsíti és felgyorsítja a módosítás folyamatát.

## Az összekötők és API-alkalmazások listája
Az egyes kategóriákban szereplő összekötők és API-alkalmazások (köztük a Standard összekötők, a BizTalk EAI-összekötők, a Premium összekötők stb.) teljes listáját az [Connectors and API Apps List](app-service-logic-connectors-list.md) (Összekötők és API-alkalmazások listája) című cikket.
 



<!--HONumber=Jun16_HO2-->


