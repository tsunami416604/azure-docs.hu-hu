---
title: A SendGrid e-mail szolgáltatás (.NET) használata | Microsoft dokumentumok
description: Megtudhatja, hogyan küldhet e-mailt a SendGrid e-mail szolgáltatással az Azure-ban. C# nyelven írt kódminták, és a .NET API használata.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: 33df6b5c8c5c16a6eb896944de05068affc2b407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062203"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Az e-mailek küldése a SendGrid használatával az Azure-ral
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat az Azure-beli SendGrid e-mail szolgáltatással. A minták C\# nyelven vannak megírva, és a .NET Standard 1.3 szabványt támogatják. A tárgyalt forgatókönyvek közé tartozik az e-mailek összeállítása, az e-mailek küldése, a mellékletek hozzáadása, valamint a különböző levelezési és nyomon követési beállítások engedélyezése. A SendGridről és az e-mailek küldéséről a Következő lépések című szakaszban talál további [információt.][Next steps]

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid e-mail szolgáltatás?
A SendGrid egy [felhőalapú e-mail szolgáltatás,] amely megbízható [tranzakciós e-mail kézbesítést,]méretezhetőséget és valós idejű elemzést, valamint rugalmas API-kat biztosít, amelyek megkönnyítik az egyéni integrációt. A SendGrid gyakori használati esetei a következők:

* Nyugták vagy vásárlási visszaigazolások automatikus küldése a vevőknek.
* Terjesztési listák felügyelete az ügyfelek havi szórólapjainak és promócióinak küldéséhez.
* Valós idejű mérőszámok gyűjtése például a blokkolt e-mailekhez és az ügyfelek elköteleződéséhez.
* Vevői lekérdezések továbbítása.
* Bejövő e-mailek feldolgozása.

További információkért látogasson [https://sendgrid.com](https://sendgrid.com) el vagy SendGrid [C# könyvtár][sendgrid-csharp] GitHub tár.

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Hivatkozás a SendGrid .NET osztálytárra
A [SendGrid NuGet csomag](https://www.nuget.org/packages/Sendgrid) a legegyszerűbb módja a SendGrid API lehívásának és az alkalmazás konfigurálásának az összes függőséggel. A NuGet a Microsoft Visual Studio 2015-ben és újabb verziókban található Visual Studio bővítmény, amely megkönnyíti a könyvtárak és eszközök telepítését és frissítését.

> [!NOTE]
> Ha a Visual Studio 2015-nél korábbi verzióját szeretné telepíteni a [https://www.nuget.org](https://www.nuget.org)NuGet-et, látogasson el a be látási menügombra, és kattintson a **NuGet telepítése** gombra.
>
>

A SendGrid NuGet csomag alkalmazásba való telepítéséhez tegye a következőket:

1. Kattintson az **Új projekt** elemre, és válasszon egy **sablont.**

   ![Új projekt létrehozása][create-new-project]
2. A **Megoldáskezelőben**kattintson a jobb gombbal **a Hivatkozások**elemre, majd kattintson a **NuGet-csomagok kezelése parancsra.**

   ![SendGrid NuGet csomag][SendGrid-NuGet-package]
3. Keresse meg a **SendGrid elemet,** és jelölje ki a **SendGrid** elemet az eredménylistában.
4. Válassza ki a Nuget csomag legújabb stabil verzióját a verzió legördülő menüből, hogy együtt dolgozhataz objektummodellel és API-kkal, amelyeket ebben a cikkben mutatbe.

   ![SendGrid csomag][sendgrid-package]
5. A telepítés befejezéséhez kattintson a **Telepítés** gombra, majd zárja be ezt a párbeszédpanelt.

A SendGrid .NET osztálykönyvtárának neve **SendGrid**. A következő névtereket tartalmazza:

* **SendGrid** a SendGrid API-jával való kommunikációhoz.
* **SendGrid.Helpers.Mail** a segítő módszerek segítségével könnyen létre SendGridMessage objektumokat, amelyek meghatározzák, hogyan kell e-maileket küldeni.

Adja hozzá a következő kódnévtér deklarációkat bármely C# fájl tetejéhez, amelyben programozott módon szeretné elérni a SendGrid e-mail szolgáltatást.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Hogyan: E-mail létrehozása
E-mail létrehozása a **SendGridMessage** objektummal. Az üzenetobjektum létrehozása után beállíthatja a tulajdonságokat és módszereket, beleértve az e-mail feladóját, az e-mail címzettjét, valamint az e-mail tárgyát és törzsét.

A következő példa bemutatja, hogyan hozhat létre teljesen kitöltött e-mail objektumot:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

A **SendGrid** típus által támogatott összes tulajdonságról és metódusról további információt a [Sendgrid-típus sendgrid-csharp][sendgrid-csharp] című témakörében talál.

## <a name="how-to-send-an-email"></a>Hogyan: E-mail küldése
E-mail létrehozása után elküldheti azt a SendGrid API-jával. Másik lehetőségként használhatja [a használatát. NET beépített könyvtár][NET-library].

Az e-mail küldéséhez meg kell adnia a SendGrid API-kulcsot. Ha az API-kulcsok konfigurálásával kapcsolatos részletekre van szüksége, látogasson el a SendGrid API Keys [dokumentációjára.][documentation]

Ezeket a hitelesítő adatokat az Azure Portalon keresztül tárolhatja az Alkalmazásbeállítások elemre kattintva, és adja hozzá a kulcs-/értékpárokat az Alkalmazásbeállítások csoportban.

 ![Az Azure-alkalmazások beállításai][azure_app_settings]

 Ezután a következőképpen érheti el őket:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Az alábbi példák bemutatják, hogyan küldhet e-mailt a SendGrid Web API-val egy konzolalkalmazással.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Útmutató: E-mail küldése az ASP .NET Core API-ról a MailHelper osztály használatával

Az alábbi példa segítségével egyetlen e-mailt küldhet több személynek az ASP .NET Core API-ból a `MailHelper` névtér osztályhasználatával. `SendGrid.Helpers.Mail` Ebben a példában az ASP .NET Core 1.0-t használjuk. 

Ebben a példában az API-kulcs `appsettings.json` a fájlban van tárolva, amely felülbírálható az Azure Portalon, amint az a fenti példákban látható.

A fájl `appsettings.json` tartalmának a következőkhöz hasonlóan kell kinéznie:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Először is hozzá kell adnunk az alábbi kódot a `Startup.cs` .NET Core API projekt fájljához. Erre azért van szükség, `SENDGRID_API_KEY` hogy `appsettings.json` az API-vezérlőben lévő függőségi injektálás használatával hozzáférhessünk a fájlból. A `IConfiguration` felület lehet beadni a konstruktor `ConfigureServices` a vezérlő hozzáadása után az alábbi módszerrel. A fájl `Startup.cs` tartalma a következőnek tűnik a szükséges kód hozzáadása után:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

A vezérlő, miután `IConfiguration` beadta a felület, tudjuk használni a `CreateSingleEmailToMultipleRecipients` módszert az `MailHelper` osztály, hogy küldjön egy e-mailt több címzett. A metódus egy további logikai `showAllRecipients`paramétert fogad el, melynek neve . Ezzel a paraméterrel szabályozható, hogy az e-mail címzettek láthatják-e egymás e-mail címét az e-mail fejléc Címzett szakaszában. A vezérlő mintakódjának az alábbiakhoz hasonlónak kell lennie 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Útmutató: Melléklet hozzáadása
Az **AddAttachment** metódus hívásával mellékleteket adhat hozzá egy üzenethez, és minimálisan megadhatja a csatolni kívánt fájlnevet és Base64-kódolású tartalmat. Több mellékletet is felvehet, ha ezt a metódust minden csatolni kívánt fájlhoz egyszer meghívja, vagy az **AddAttachments** metódus használatával. A következő példa bemutatja, hogy mellékletet ad-e egy üzenethez:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Útmutató: A levelezési beállítások kal engedélyezheti az élőlábakat, a követést és az elemzést
A SendGrid további e-mail funkciókat biztosít a levelezési beállítások és a nyomon követési beállítások használatával. Ezeket a beállításokat hozzáadhatja egy e-mail üzenethez, hogy lehetővé tegye bizonyos funkciókat, például a kattintáskövetést, a Google Analytics szolgáltatást, az előfizetések nyomon követését és így tovább. Az alkalmazások teljes listáját a [Beállítások dokumentációjában találja.][settings-documentation]

Az alkalmazások a **SendGridMessage** osztály részeként megvalósított módszerekkel alkalmazhatók a **SendGrid** e-mail üzenetekre. A következő példák bemutatják az élőláb- és kattintáskövetési szűrőket:

A következő példák bemutatják az élőláb- és kattintáskövetési szűrőket:

### <a name="footer-settings"></a>Élőláb beállításai
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Kattintáskövetés
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: További SendGrid-szolgáltatások használata
A SendGrid számos API-t és webhookot kínál, amelyek segítségével további funkciókat használhat az Azure-alkalmazáson belül. További információt a [SendGrid API-referencia című témakörben talál.][SendGrid API documentation]

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a SendGrid E-mail szolgáltatás alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* SendGrid\# C könyvtár repo: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API dokumentáció:<https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[felhőalapú e-mail szolgáltatás]: https://sendgrid.com/solutions
[tranzakciós e-mail kézbesítés]: https://sendgrid.com/use-cases/transactional-email

