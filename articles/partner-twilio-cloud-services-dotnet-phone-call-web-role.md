---
title: Hogyan lehet egy telefonhívást Twilio (.NET) | Microsoft dokumentumok
description: Ismerje meg, hogyan kezdeményezhetsz telefonhívást, és hogyan küldhet SMS-üzenetet az Azure-beli Twilio API-szolgáltatással. A .NET nyelven írt kódminták.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636140"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Telefonhívás lehívása a Twilio használatával webes szerepkörben az Azure-ban
Ez az útmutató bemutatja, hogyan használhatja a Twilio hívást az Azure-ban üzemeltetett weboldalról. Az eredményül kapott alkalmazás felszólítja a felhasználót, hogy kezdeményezze a hívást a megadott számmal és üzenettel, ahogy az a következő képernyőképen látható.

![Azure-hívási űrlap a Twilio és a ASP.NET használatával][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Előfeltételek
A témakörben szereplő kód használatához a következőket kell tennie:

1. Twilio-fiók és hitelesítési jogkivonat beszerzése a [Twilio konzolról.][twilio_console] A Twilio ismerkedéséhez regisztráljon a at. [https://www.twilio.com/try-twilio][try_twilio] Az árakat a- és a. [https://www.twilio.com/pricing][twilio_pricing] A Twilio által biztosított API-ról a témakörben talál [https://www.twilio.com/voice/api][twilio_api]további információt.
2. Adja hozzá a *Twilio .NET könyvtárat* a webes szerepkörhöz. Lásd: **A Twilio-tárak hozzáadása a webes szerepkör-projekthez**című témakör későbbi részében.

Ismernie kell az alapvető webes szerepkör létrehozását [az Azure-ban.][azure_webroles_get_started]

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Útmutató: Webes űrlap létrehozása híváshoz
<a id="use_nuget"></a>A Twilio-kódtárak hozzáadása a webes szerepkör-projekthez:

1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal **a Hivatkozások elemre.**
3. Kattintson **a NuGet-csomagok kezelése gombra.**
4. Kattintson **az Online gombra.**
5. A keresés online mezőbe írja be a *twilio*kifejezést.
6. Kattintson a **Telepítés** a Twilio-csomagon.

A következő kód bemutatja, hogyan hozhat létre webes űrlapot a híváshoz való felhasználói adatok lekéréséhez. Ebben a példában egy **twiliocloud** nevű ASP.NET webes szerepkör jön létre.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Útmutató: Hozzon létre egy kódot, hogy a hívás
A következő kód, amelynek neve akkor jön létre, amikor a felhasználó kiírja az űrlapot, létrehozza a hívási üzenetet, és létrehozza a hívást. Ebben a példában a kód az űrlapon lévő gomb onclick eseménykezelőjében fut. (Használja a Twilio-fiókot és a hitelesítési `accountSID` jogkivonatot az alábbi kódhoz `authToken` rendelt helyőrző értékek helyett.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

A hívás történik, és a Twilio-végpont, API-verzió, és a hívás állapota jelenik meg. A következő képernyőképen egy mintafuttatás kimenete látható.

![Azure-hívásválasz a Twilio és ASP.NET használatával][twilio_dotnet_basic_form_output]

További információ a TwiML-ről a található. [https://www.twilio.com/docs/api/twiml][twiml] További információ &lt;&gt; a Say és más Twilio [https://www.twilio.com/docs/api/twiml/say][twilio_say]igékről a.

## <a name="next-steps"></a><a id="nextsteps"></a>További lépések
Ez a kód a Twilio használatával az Azure-beli ASP.NET webes szerepkörben való alapvető funkciók megjelenítéséhez lett biztosítva. Mielőtt éles környezetben üzembe helyezne az Azure-ba, érdemes lehet további hibakezelést vagy egyéb funkciókat hozzáadnia. Példa:

* Webes űrlap használata helyett használhatja az Azure Blob storage-ot vagy egy Azure SQL Database-példányt a telefonszámok tárolására és a hívás szövegének tárolására. A Blobok Azure-ban való használatáról [az Azure Blob storage szolgáltatás használata a .NET-ben][howto_blob_storage_dotnet]című témakörben talál további információt. Az SQL Database használatáról az [Azure SQL Database használata .NET alkalmazásokban][howto_sql_azure_dotnet]című témakörben talál további információt.
* A Twilio-fiók azonosítójának és a hitelesítési jogkivonatnak a központi telepítés konfigurációs beállításaiból való lekéréséhez használhatja, `RoleEnvironment.getConfigurationSettings` ahelyett, hogy az űrlap értékeit kódolna. Az osztályról `RoleEnvironment` további információt a [Microsoft.WindowsAzure.ServiceRuntime Névtér című][azure_runtime_ref_dotnet]témakörben talál.
* Olvassa el a Twilio biztonsági irányelveit a. [https://www.twilio.com/docs/security][twilio_docs_security]
* Tudjon meg többet [https://www.twilio.com/docs][twilio_docs]a Twilio-ról a.

## <a name="see-also"></a><a name="seealso"></a>Lásd még
* [A Twilio használata az Azure-ból származó hang- és SMS-funkciókhoz](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
