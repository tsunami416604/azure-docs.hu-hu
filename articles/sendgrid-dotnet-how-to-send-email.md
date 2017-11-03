---
title: "A SendGrid e-mail szolgáltatás (.NET) használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan küldjön e-maileket a SendGrid e-mail szolgáltatás az Azure-on. A Kódminták C# nyelven íródtak, és a .NET API-t használja."
services: 
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 14161a0747add43a99e301eacf700ab79c77c767
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Hogyan e-mailek küldése SendGrid az Azure-ral
## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan Azure a SendGrid e-mail szolgáltatás közös programozási feladatok elvégzéséhez. A Kódminták C nyelven íródtak\# és támogatja a .NET-szabvány 1.3. Az ismertetett forgatókönyvek hozhat létre, e-mailek, e-mailek küldéséhez, hozzáadása a mellékleteket, és különböző mail engedélyezése és nyomon követési beállítások közé tartozik. A SendGrid és e-mailek küldéséhez további információkért lásd: a [további lépések] [ Next steps] szakasz.

## <a name="what-is-the-sendgrid-email-service"></a>Mi az a SendGrid E-mail szolgáltatás?
SendGrid van egy [felhőalapú szolgáltatás] biztosít megbízható [tranzakciós e-mailben kézbesítésre], a méretezhetőség és a valós idejű elemzési rugalmas API-k, amelyek egyéni integrációs könnyedén együtt. Közös SendGrid használati esetek a következők:

* Az ügyfelek automatikusan küldése visszaigazolások vagy beszerzési visszaigazolások.
* Az ügyfelek havi szórólapok és előléptetések terjesztési felügyelete sorolja fel.
* Többek között a letiltott e-mailek és az ügyfél engagement valós idejű metrikáját gyűjtése.
* Továbbító ügyfél lekérdezések.
* A bejövő e-mailek feldolgozása.

További információkért látogasson el a [https://sendgrid.com](https://sendgrid.com) vagy SendGrid tartozó [C# könyvtár] [ sendgrid-csharp] GitHub-tárház.

## <a name="create-a-sendgrid-account"></a>A SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>A SendGrid .NET osztálytár referencia
A [SendGrid NuGet-csomag](https://www.nuget.org/packages/Sendgrid) a legegyszerűbb módja a SendGrid API beszerzésének, és konfigurálja az alkalmazás összes függőségét. NuGet Microsoft Visual Studio 2015-höz mellékelt Visual Studio bővítménye, és fölött, amely megkönnyíti a telepítése és frissítése, kódtárak és eszközök.

> [!NOTE]
> NuGet Ha futtatja a Visual Studio Visual Studio 2015-nél korábbi verziójának telepítéséhez látogasson el a [http://www.nuget.org](http://www.nuget.org), és kattintson a **NuGet telepítése** gombra.
>
>

A SendGrid NuGet-csomagot az alkalmazás telepítéséhez tegye a következőket:

1. Kattintson a **új projekt** válassza ki a **sablon**.

   ![Új projekt létrehozása][create-new-project]
2. A **Megoldáskezelőben**, kattintson a jobb gombbal **hivatkozások**, majd kattintson a **NuGet-csomagok kezelése**.

   ![SendGrid NuGet-csomag][SendGrid-NuGet-package]
3. Keresse meg **SendGrid** válassza ki a **SendGrid** elem az eredménylistában.
4. Válassza ki a Nuget-csomag stabil legújabb verzióját kell lennie a hálózatiobjektum-modellje dolgozni a verzió legördülő listából, és az ebben a cikkben egy API-k.

   ![SendGrid csomag][sendgrid-package]
5. Kattintson a **telepítése** a telepítés befejezéséhez, és zárja be ezt a párbeszédpanelt.

SendGrid tartozó .NET osztálytár nevezik **SendGrid**. A következő névterek tartalmazza:

* **SendGrid** SendGrid tartozó API való kommunikációhoz.
* **SendGrid.Helpers.Mail** segítő módszerek könnyen hozzanak létre az e-mailek küldése megadott SendGridMessage objektumokat.

A következő kód névtér-deklarációk hozzáadása bármely kívánja programon keresztüli eléréséhez a SendGrid e-mail szolgáltatás C#-fájl elejéhez.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Hogyan: hozzon létre egy e-mailt
Használja a **SendGridMessage** objektum e-mailt létrehozni. Az üzenet-objektum létrehozása után beállíthatja a tulajdonságokat és metódusokat, beleértve az e-mailt olyasvalaki, az e-mail címzettjét, és a tárgy és törzs az e-mailek.

A következő példa bemutatja, hogyan hozhat létre teljesen ki van töltve e-mail objektumot:

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

Minden olyan tulajdonságok és módszerek által támogatott további információt a **SendGrid** írja be, lásd: [sendgrid-c Sharp] [ sendgrid-csharp] a Githubon.

## <a name="how-to-send-an-email"></a>Útmutató: az e-mailek küldése
Miután létrehozta az e-mailt, elküldheti azt a SendGrid tartozó API-val. Másik megoldásként használhat [. NET meg a szalagtár beépített][NET-library].

E-mailek küldéséhez, hogy megadnia a SendGrid API-kulcs szükséges. Ha API-kulcsokat konfigurálásával kapcsolatos részletek van szüksége, látogasson el a SendGrid tartozó API-kulcsokat [dokumentáció][documentation].

Az Azure portálon kattintson az alkalmazás beállításait, és vegye fel a kulcs/érték párok az alkalmazásbeállítások tárolhatjuk ezeket a hitelesítő adatokat.

 ![Az Azure alkalmazás beállításai][azure_app_settings]

 Majd hogy a hozzáférés az alábbiak szerint:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

A következő példák bemutatják, hogyan küldhető a webes API-val.

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

## <a name="how-to-add-an-attachment"></a>Hogyan: hozzá mellékletet
Mellékletek meghívásával adhatók hozzá egy üzenetet a **AddAttachment** metódus és minimálisan megadása a fájl nevét és a Base64-kódolású tartalmat kíván csatolni. A metódus hívása után minden fájlt csatolni szeretné vagy segítségével megadhat több mellékletet a **AddAttachments** metódust. A következő példa bemutatja, egy üzenet melléklet hozzáadása:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Hogyan: láblécben, nyomon követési és elemzés engedélyezése levelezési beállítások használatával
SendGrid a levelezési beállításokat és a nyomon követési beállítások további e-mail funkciókat biztosítja. Ezek a beállítások is hozzáadhatók az e-mailt adott funkciónak például kattintson nyomon követése, Google analytics, követési előfizetés, stb. Alkalmazások teljes listáját lásd: a [beállítások dokumentáció][settings-documentation].

Alkalmazások alkalmazhatók **SendGrid** e-mailek részeként megvalósított metódusok használata a **SendGridMessage** osztály. Az alábbi példák bemutatják, a láblécben, majd kattintson a szűrő nyomon követése:

Az alábbi példák bemutatják, a láblécben, majd kattintson a szűrő nyomon követése:

### <a name="footer-settings"></a>Élőláb beállításai
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Kattintson a nyomon követése
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: további SendGrid szolgáltatásokkal
SendGrid több API-k és webhookokkal, amelyek segítségével kihasználhatja az Azure alkalmazáson belül további funkciókat kínál. További részletekért lásd: a [SendGrid API-referencia][SendGrid API documentation].

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, az alábbi hivatkozásokból további.

* SendGrid C\# könyvtár tárház: [sendgrid-c Sharp][sendgrid-csharp]
* SendGrid API dokumentációjának: <https://sendgrid.com/docs>

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

[felhőalapú szolgáltatás]: https://sendgrid.com/solutions
[tranzakciós e-mailben kézbesítésre]: https://sendgrid.com/use-cases/transactional-email

