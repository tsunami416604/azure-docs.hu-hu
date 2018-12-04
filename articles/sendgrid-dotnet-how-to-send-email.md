---
title: A SendGrid e-mail szolgáltatással (.NET) használatával |} A Microsoft Docs
description: Ismerje meg, hogyan e-mail küldése a SendGrid e-mail szolgáltatás az Azure-ban. C# nyelven írt kódmintákat, és a .NET API-val.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 91d28802b4af23da5b8060fa7c8f9a7e843a7dab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840265"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Hogyan lehet E-mail küldése a SendGrid használatával az Azure-ral
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori programozási feladatokat, a SendGrid e-mail szolgáltatással az Azure-ban. A Kódminták C nyelven írt\# és támogatja a .NET Standard 1.3-as. Az ismertetett forgatókönyvek között megtalálható a hozhat létre, amely e-mailt, e-mailt küld, hozzáadása mellékleteket, és különböző mail engedélyezése és követési beállításai. A SendGrid és az e-mailt küld a további információkért lásd: a [további lépések] [ Next steps] szakaszban.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatással?
A SendGrid van egy [e-mail felhő alapú szolgáltatás] , amely megbízható biztosít [tranzakciós e-mail kézbesítési], a méretezhetőség és a valós idejű analitikát és rugalmas API-kat, amelyek elérhetőbbé teszik egyéni integrációs egyszerű. A SendGrid gyakori alkalmazási helyzetek a következők:

* Automatikusan adatokat küldjenek visszaigazolások vagy vásárlást visszaigazolásokhoz az ügyfelek számára.
* Ügyfelek küld el havonta közleményekben és promóciók terjesztési felügyelete sorolja fel.
* Letiltott e-mailek és az ügyfélkapcsolatokért például valós idejű metrikák gyűjtése.
* Továbbítás felhasználói lekérdezések.
* A bejövő e-mailek feldolgozását.

További információért látogasson el [ https://sendgrid.com ](https://sendgrid.com) vagy SendGrid [C# könyvtár] [ sendgrid-csharp] GitHub-adattárban.

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referencia a SendGrid .NET osztálytár
A [SendGrid NuGet-csomag](https://www.nuget.org/packages/Sendgrid) legegyszerűbb módja a SendGrid API-t és az alkalmazás konfigurálása az összes függőség. NuGet egy Visual Studio-bővítmény a Microsoft Visual Studio 2015-höz mellékelt és fölött, amely megkönnyíti a telepíthet és frissíthet, könyvtárak és eszközök.

> [!NOTE]
> Telepítse a NuGet, ha a Visual Studio Visual Studio 2015-nél korábbi verzióját futtatja, a Microsoft [ https://www.nuget.org ](https://www.nuget.org), kattintson a **NuGet telepítése** gombra.
>
>

A SendGrid NuGet-csomag telepítése az alkalmazásban, tegye a következőket:

1. Kattintson a **új projekt** , és válassza ki a **sablon**.

   ![Új projekt létrehozása][create-new-project]
2. A **Megoldáskezelőben**, kattintson a jobb gombbal **hivatkozások**, majd kattintson a **NuGet-csomagok kezelése**.

   ![A SendGrid NuGet-csomag][SendGrid-NuGet-package]
3. Keresse meg **SendGrid** , és válassza ki a **SendGrid** elemet a találatok listájában.
4. Válassza ki a Nuget-csomag legújabb stabil verzióját a verzió képesek együttműködni a hálózatiobjektum-modellt a legördülő menüből, és ebben a cikkben bemutatott API-k.

   ![A SendGrid-csomagot][sendgrid-package]
5. Kattintson a **telepítése** a telepítés befejezéséhez, és zárja be ezt a párbeszédpanelt.

.NET-osztálytár SendGrid nevezzük **SendGrid**. A következő névterek tartalmazza:

* **A SendGrid** SendGrid API-val való kommunikációhoz.
* **SendGrid.Helpers.Mail** segédmetódusokat egyszerűen SendGridMessage objektumainak létrehozásához, amelyek adja meg az e-mailek küldése a.

Adja hozzá a következő kód névtér-deklarációk bármely, amelyben meg szeretné programozott módon érheti el a SendGrid e-mail szolgáltatás C#-fájl elejéhez.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>How to: hozzon létre egy e-mailt
Használja a **SendGridMessage** objektumot hozzon létre egy e-mail-üzenetet. Az üzenet-objektum létrehozása után beállíthatja a tulajdonságok és metódusok, beleértve az e-mailt küldő, az e-mail címzettje és a tulajdonos és az e-mail törzse.

Az alábbi példa bemutatja, hogyan hozhat létre teljes mértékben ki van töltve e-mail objektumot:

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

További információ az összes tulajdonságok és metódusok által támogatott a **SendGrid** írja be, lásd: [sendgrid-csharp] [ sendgrid-csharp] a Githubon.

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése
Miután létrehozott egy e-mail-üzenetet, elküldheti azt a SendGrid API-val. Másik megoldásként használhat [. NET a beépített könyvtár][NET-library].

E-mail-küldési kell írnia a SendGrid API-kulcsát. Ha az API-kulcsok konfigurálásával kapcsolatos részletes van szüksége, látogasson el a SendGrid API-kulcsok [dokumentáció][documentation].

Alkalmazásbeállítások kattint, és vegye fel a kulcs/érték párok az alkalmazásbeállításoknál tárolhatja ezeket a hitelesítő adatokat az Azure Portalon keresztül.

 ![Az Azure app beállításai][azure_app_settings]

 Ezt követően előfordulhat, hogy férhet hozzájuk a következő:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Az alábbi példák bemutatják, hogyan küldése a SendGrid webes API-val egy olyan konzolalkalmazást, e-mailbe.

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
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Útmutató: e-mail küldéséhez az ASP .NET Core API MailHelper osztály használatával

Az alábbi példában is használható az ASP .NET Core API használatával több személy egyetlen e-mail küldéséhez a `MailHelper` osztályát `SendGrid.Helpers.Mail` névtér. Ebben a példában az ASP .NET Core 1.0-t használjuk. 

Ebben a példában az API-kulcs nem menteni az a `appsettings.json` bírálható felül az Azure Portalról, ahogyan az a fenti példa fájlt.

A tartalmát `appsettings.json` fájl hasonlóan kell kinéznie:

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

Először hozzá kell adnunk a az alábbi kódot a `Startup.cs` fájlt a .NET Core API-projekt. Ez azért szükséges, hogy elérheti a `SENDGRID_API_KEY` származó a `appsettings.json` függőségi beszúrást használatával az API-vezérlő fájlt. A `IConfiguration` felület, a vezérlő konstruktorának juttatható a hozzáadása után a `ConfigureServices` az alábbi metódust. Tartalmának `Startup.cs` fájlt a következőhöz hasonló a szükséges kód hozzáadása után:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

A vezérlő betöltése után, a `IConfiguration` felületen, használhatjuk a `CreateSingleEmailToMultipleRecipients` módszere a `MailHelper` osztály egyetlen e-mail küldés több címzettnek. A metódus elfogad egy további logikai paraméter nevű `showAllRecipients`. Ez a paraméter segítségével ellenőrző e-mailek címzettjeinek megtekinthető lesz-e minden más e-mail-cím az e-mail adott fejléce To szakaszában. A mintakód vezérlő alatt például kell lennie 

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
    
## <a name="how-to-add-an-attachment"></a>Útmutató: a melléklet hozzáadása
A mellékletek meghívásával is hozzáadhatók egy üzenetet a **AddAttachment** metódust, és minimálisan megadása a fájl nevét és a Base64-kódolású tartalmak kíván csatolni. A metódus meghívásának hatására, miután az összes fájl esetében a csatlakoztatni kívánt és használatával több mellékleteket tartalmazhatnak a **AddAttachments** metódust. A következő példa bemutatja egy melléklet hozzáadása:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Hogyan: élőlábak, nyomon követés és az analytics engedélyezése a levelezési beállítások használatával
A SendGrid levelezési beállítások és a nyomon követési beállítások e-mail további funkciókat biztosít. Ezek a beállítások is hozzáadhatók egy e-mail üzenethez ahhoz, hogy bizonyos funkciók, például a kampányban, a Google analytics, előfizetés nyomon követése és így tovább. Alkalmazások teljes listáját lásd: a [beállítások dokumentáció][settings-documentation].

Alkalmazások alkalmazhatók **SendGrid** e-mailek részeként megvalósított metódusok használata a **SendGridMessage** osztály. Az alábbi példák bemutatják, az élőláb, majd kattintson a szűrők követés:

Az alábbi példák bemutatják, az élőláb, majd kattintson a szűrők követés:

### <a name="footer-settings"></a>Lábléc beállításai
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Kattintson a nyomon követés
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>How to: további SendGrid-szolgáltatások használata
A SendGrid több API-kat és webhookokat használatával kihasználhatja az Azure alkalmazásban további funkciókat kínál. További részletekért tekintse meg a [SendGrid API-referencia][SendGrid API documentation].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, a SendGrid E-mail szolgáltatás, kövesse az alábbi hivatkozások további.

* A SendGrid C\# könyvtár tárház: [sendgrid-csharp][sendgrid-csharp]
* A SendGrid API-dokumentáció: <https://sendgrid.com/docs>

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

[e-mail felhő alapú szolgáltatás]: https://sendgrid.com/solutions
[tranzakciós e-mail kézbesítési]: https://sendgrid.com/use-cases/transactional-email

