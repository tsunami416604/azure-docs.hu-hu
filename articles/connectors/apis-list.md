<properties
    pageTitle="A Microsoft Azure Logic Apps alkalmazásokban használható, Microsoft által felügyelt összekötők listája | Microsoft Azure App Service"
    description="A Microsoft által felügyelt összekötők teljes listája, amelyek használatával Logic Apps alkalmazásokat készíthet az Azure App Service szolgáltatásban"
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="deonhe"/>

# A felügyelt összekötők listája

>[AZURE.NOTE] A cikk e verziója a Logic Apps 2015. 08. 01. dátumú előnézeti sémaverziójára vonatkozik. A 2014. 12. 01. dátumú előzetes sémaverzióért kattintson az [összekötők listájára](../app-service-logic/app-service-logic-connectors-list.md). 

Ha szeretne további tájékoztatáshoz jutni az árakról, illetve kíváncsi rá, hogy mit tartalmaznak az egyes szolgáltatásszintek, látogasson el az [Azure App Service Díjszabás](https://azure.microsoft.com/pricing/details/app-service/) című weblapra.

> [AZURE.NOTE] Ha az Azure Logic Apps-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, lépjen [Az Azure App Service kipróbálása](https://tryappservice.azure.com/?appservice=logic) oldalra. Itt azonnal létrehozhat egy rövid életű, kezdő szintű logikai alkalmazást az App Service szolgáltatásban. Nincs szükség bankkártyára, és nem jár semmiféle kötelezettséggel.

Válasszon egy ikont, ha szeretné megtudni, hogyan használhatja ezeket az összekötőket olyan alkalmazások készítéséhez, amelyek ezen szolgáltatásokat hívják meg. Az összekötők Logic Apps, PowerApps és Flow-alkalmazások készítéséhez használhatók.

|Összekötők||||
|-----------|-----------|-----------|-----------|
|[![API Icon][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API Icon][boxicon]<br/>**Box**][boxDoc]|[![API Icon][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![API Icon][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![API Icon][facebookicon]<br/>**Facebook**][facebookdoc]|[![API Icon][ftpicon]<br/>**FTP**][ftpdoc]|[![API Icon][githubicon]<br/>**GitHub**][githubdoc]|[![API Icon][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![API Icon][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![API Icon][microsofttranslatoricon]<br/>**Fordító**][microsofttranslatordoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Users**][office365usersdoc]|
|[![API Icon][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|[![API Icon][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API Icon][onedriveicon]<br/>**OneDrive<br/>Vállalati verzió**][onedriveforbusinessdoc]|[![API Icon][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![API Icon][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![API Icon][rssicon]<br/>**RSS**][rssdoc]|[![API Icon][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API Icon][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![API Icon][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API Icon][sftpicon]<br/>**SFTP**][sftpdoc]|[![API Icon][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![API Icon][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![API Icon][smtpicon]<br/>**SMTP**][smtpdoc]|[![API Icon][sqlicon]<br/>**SQL Azure**][sqldoc]|[![API Icon][trelloicon]<br/>**Trello**][trellodoc]|[![API Icon][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![API Icon][twittericon]<br/>**Twitter**][twitterdoc]|[![API Icon][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![API Icon][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Amennyiben a 2014. 12. 01. dátumú előnézeti séma használatával készített Logic Apps alkalmazásokat, észrevehette, hogy a vállalati integrációs összekötők, például a BizTalk összekötői nem láthatók fent. Tisztában vagyunk vele, hogy ezek fontosak, és minden erőfeszítést megteszünk azért, hogy mihamarabb használhassa őket. Jóllehet még nem tudjuk a pontos dátumot, hogy mikor lesznek elérhetők az összekötők, szeretnénk, ha tudná, hogy a biztosításuk a legfontosabb prioritásaink közé tartozik. Addig is az [1-es verziójú API-kat és a BizTalk API-kat elérheti a Logic Apps alkalmazásokból](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Megértését köszönjük. Kövessen bennünket.


### Az összekötők lehetnek eseményindítók
Több összekötő is biztosít eseményindítókat, amelyek adott események bekövetkezése esetén értesíthetik az alkalmazást. Az FTP-összekötő például az OnUpdatedFile eseményindítót tartalmazza. Készíthet olyan Logic App, PowerApp vagy Flow-alkalmazást, amely figyel erre az eseményindítóra, és végrehajt egy műveletet, amint az eseményindító aktiválódik.

Két eseményindító-típus létezik:  

* Lekérdezéses eseményindítók: ezek az eseményindítók a beállított időközönként új adatokat kereső lekérdezést küldenek a szolgáltatásnak. Amikor új adatok válnak elérhetővé, az alkalmazás új példánya bemeneti adatokként fogja azokat használni a futása során. Ha meg szeretné akadályozni, hogy ugyanazokat az adatokat többször is feldolgozza a rendszer, elérheti, hogy az eseményindító megtisztítsa azokat az adatokat, amelyeket az alkalmazása már beolvasott.
* Leküldéses eseményindítók: ezek az eseményindítók azt figyelik, hogy mikor jelennek meg adatok egy végponton, vagy mikor megy végbe egy adott esemény. Ezután elindítják az alkalmazás egy új példányát. Ilyen például a Twitter-összekötő.


### Az összekötők lehetnek műveletek
Az összekötők használhatók műveletekként is az alkalmazásokban. A műveletek olyan adatok gyűjtéséhez lehetnek hasznosak, amelyeket azután az alkalmazás végrehajtása során használ. Előfordulhat például, hogy ügyféladatokat kell keresnie egy SQL-adatbázisban egy megrendelés feldolgozásához. Vagy előfordulhat, hogy a céltáblában kell adatokat írnia, frissítenie vagy törölnie. Ezt mind megteheti az összekötők által kínált műveletekkel. Ezek a műveletek leképezhetőek a Swagger-metaadatokban meghatározott műveletekké.


[Újdonságok](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Hozzon létre egy Logic App alkalmazást](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Bevezetés a PowerApps használatába](../power-apps/powerapps-get-started-azure-portal.md)  
[Meglévő Logic Apps alkalmazások áttelepítése a legújabb sémaverzióra](connectors-schema-migration.md) 

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Az Azure-blobhoz csatlakozhat a blobtárolókban lévő fájlok kezeléséhez."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "A Bing használatával kereshet webes tartalmakat, képeket, híreket és videókat."
[boxDoc]: ./connectors-create-api-box.md "A Box szolgáltatáshoz csatlakozhat, és fájlokat tölthet fel, kérhet le, törölhet, listázhat stb."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "A Dynamics CRM Online szolgáltatáshoz csatlakozhat, és hatékonyabban használhatja CRM Online-adatait."
[dropboxdoc]: ./connectors-create-api-dropbox.md "A Dropbox szolgáltatáshoz csatlakozhat, és fájlokat tölthet fel, kérhet le, törölhet, listázhat stb."
[exceldoc]: ./connectors-create-api-excel.md "Az Excelhez csatlakozhat."
[facebookdoc]: ./connectors-create-api-facebook.md "A Facebookhoz csatlakozhat, és bejegyzéseket tehet közzé az idővonalon, laptartalmakat kérhet le, és egyéb műveleteket végezhet."
[ftpdoc]: ./connectors-create-api-ftp.md "Ezzel az összekötővel csatlakozhat FTP-/FTPS-kiszolgálókhoz, és ott különféle FTP-műveleteket hajthat végre, például fájlok feltöltését, lekérését, törlését stb."
[googledrivedoc]: ./connectors-create-api-googledrive.md "A Google Drive-hoz csatlakozhat, és kezelheti adatait."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Az Office 365-összekötővel e-maileket küldhet és fogadhat, kezelheti naptárát és névjegyeit Office 365-fiókjában."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Személyes Microsoft OneDrive-fiókjához csatlakozhat, és fájlokat tölthet fel, törölhet, listázhat stb."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Vállalati Microsoft OneDrive-fiókjához csatlakozhat, és fájlokat tölthet fel, törölhet, listázhat stb."
[outlookdoc]: ./connectors-create-api-outlook.md "Outlook-postaládájához csatlakozhat, amelyben elérheti e-mailjeit, és egyéb műveleteket végezhet."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "A Microsoft Project Online-hoz csatlakozhat."
[rssdoc]: ./connectors-create-api-rss.md "Az RSS-összekötő használatával a felhasználók közzétehetnek és lekérhetnek hírcsatornaelemeket. Az összekötő emellett lehetővé teszi, hogy a felhasználók műveleteket indítsanak, ha egy új elemet tesznek közzé a hírcsatornán."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce-fiókjához csatlakozhat, és kezelheti ügyfeleit, üzleti lehetőségeit és egyebeket."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "A Microsoft Project Online-hoz csatlakozhat."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Üzeneteket küldhet a Service Bus-üzenetsorokból és -témákból, illetve üzeneteket fogadhat a Service Bus-üzenetsorokból és -előfizetésekből."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "A SharePoint Online-hoz csatlakozhat a dokumentumok kezeléséhez és az elemek listázásához."
[slackdoc]: ./connectors-create-api-slack.md "A Slackhez csatlakozhat, és üzeneteket tehet közzé a Slack-csatornákon."
[sftpdoc]: ./connectors-create-api-sftp.md "Az SFTP szolgáltatáshoz csatlakozhat, és fájlokat tölthet fel, kérhet le, törölhet stb."
[githubdoc]: ./connectors-create-api-github.md "A GitHubhoz csatlakozhat, és nyomon követheti a problémákat."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Jobb e-maileket küldhet."
[smtpdoc]: ./connectors-create-api-smtp.md "Egy SMTP-kiszolgálóhoz csatlakozhat, és mellékleteket tartalmazó e-maileket küldhet."
[sqldoc]: ./connectors-create-api-sqlazure.md "Az SQL Azure-adatbázishoz csatlakozhat. Az összekötővel SQL-adatbázistáblában szereplő bejegyzéseket hozhat létre, frissíthet, kérhet le és törölhet."
[trellodoc]: ./connectors-create-api-trello.md "A Trello használatával ingyenesen, rugalmasan és látványosan szervezhet meg bármit bárkivel."
[twiliodoc]: ./connectors-create-api-twilio.md "A Twilio szolgáltatáshoz csatlakozhat, amelyben üzeneteket küldhet és fogadhat, lekérheti az elérhető számokat, kezelheti a bejövő telefonszámokat, és egyéb műveleteket végezhet."
[twitterdoc]: ./connectors-create-api-twitter.md "A Twitterhez csatlakozhat, és idővonal-tartalmakat fogadhat, tweeteket tehet közzé, és egyéb műveleteket végezhet."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Szervezetten élheti életét."
[yammerdoc]: ./connectors-create-api-yammer.md "A Yammerhez csatlakozhat, és üzeneteket tehet közzé vagy fogadhat újakat."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png



<!--HONumber=Jun16_HO2-->


