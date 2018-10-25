---
title: Telefonhívás indítása Twilióról (.NET) biztosítása |} A Microsoft Docs
description: Útmutató a telefonhívás, és a Twilio API-szolgáltatással egy SMS-üzenet küldése az Azure-ban. .NET-keretrendszerben írt kódmintákat.
services: ''
documentationcenter: .net
author: devinrader
manager: timlt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: jeconnoc
ms.openlocfilehash: 9433cbaba774237bc739106af410dd8d3dd7308f
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025710"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>A Twilio használata az Azure-beli webes szerepkör telefonhívás módját
Ez az útmutató bemutatja, hogyan a Twilio használata az Azure-ban üzemeltetett weboldalakról irányuló hívások indításához. Az eredményül kapott alkalmazás kéri a felhasználót, hogy a megadott számot, egy üzenet, a hívás, az alábbi képernyőképen látható módon.

![Az Azure hívás űrlap Twilio és az ASP.NET használatával][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Előfeltételek
Szüksége lesz a kód használata ebben a témakörben a következőket:

1. Szerezzen egy Twilio-fiók és egy hitelesítési tokent a [Twilio konzol][twilio_console]. Első lépések a Twilio, regisztráció: [ https://www.twilio.com/try-twilio ] [ try_twilio]. Díjszabás kiértékelheti [ http://www.twilio.com/pricing ] [ twilio_pricing]. A Twilio által biztosított API-val kapcsolatos további információkért lásd: [ http://www.twilio.com/voice/api ] [ twilio_api].
2. Adja hozzá a *Twilio .NET-kódtár* , a webes szerepkör. Lásd: **a Twilio-kódtárak a webes szerepkör projekt hozzáadása**, jelen témakör későbbi részében.

Meg kell ismernie az alapszintű létrehozása [Azure-beli webes szerepkör][azure_webroles_get_started].

## <a name="howtocreateform"></a>Útmutató: webes űrlap egy hívás létrehozása
<a id="use_nuget"></a>A Twilio-kódtárak a webes szerepkör projekt hozzáadása:

1. Nyissa meg a megoldást a Visual Studióban.
2. Kattintson a jobb gombbal **hivatkozások**.
3. Kattintson a **NuGet-csomagok kezelése**.
4. Kattintson a **Online**.
5. Az online be a keresőmezőbe írja be a *twilio*.
6. Kattintson a **telepítése** a Twilio-csomag.

A következő kód bemutatja, hogyan hozhat létre egy webes űrlap, hogy a hívás felhasználói adatok lekéréséhez. Ebben a példában egy ASP.NET webes szerepkör nevű **TwilioCloud** jön létre.

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

## <a id="howtocreatecode"></a>Útmutató: a hívást a kód létrehozása
A következő kódra, amely nevezzük, amikor a felhasználó befejezi az űrlapot, a hívás üzenetet hoz létre, és állít elő, a hívást. Ebben a példában a kódot az űrlap a gomb onclick eseménykezelő futtassa. (Használja a Twilio-fiók és a hitelesítési token helyett a helyőrző értékeket rendelt `accountSID` és `authToken` az alábbi kódban.)

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
            // Call porcessing happens here.

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
                var url = $"http://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
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

A kérés érkezett, és a Twilio-végpont, API-verzió és az állapot jelenik meg. Az alábbi képernyőképen látható egy példa Futtatás kimenete.

![Az Azure hívás válasz Twilio és az ASP.NET használatával][twilio_dotnet_basic_form_output]

További információ a TwiML található [ http://www.twilio.com/docs/api/twiml ] [ twiml]. További információ a &lt;Say&gt; és egyéb Twilio művelet található [ http://www.twilio.com/docs/api/twiml/say ] [ twilio_say].

## <a id="nextsteps"></a>Következő lépések
Ez a kód mutatni, alapvető funkció a Twilio használatával az Azure-ban egy ASP.NET webes szerepkörben lett megadva. Mielőtt üzembe helyezni az Azure éles környezetben, érdemes lehet további hibakezelés vagy más szolgáltatások hozzáadása. Példa:

* Egy webes űrlap használata helyett használhatja az Azure Blob storage- vagy Azure SQL Database-példány telefonszámok tárolására, és hívja a szöveget. További információ a Blobs használata az Azure-ban: [az Azure Blob storage szolgáltatás használata .NET-keretrendszerben][howto_blob_storage_dotnet]. Az SQL Database használatával kapcsolatos információkért lásd: [Azure SQL Database használata .NET-alkalmazásokban][howto_sql_azure_dotnet].
* Használhat `RoleEnvironment.getConfigurationSettings` beolvasni a Twilio-fiókja Azonosítóját és hitelesítési tokent a telepítési konfigurációt, fix kódolása az űrlap értékei helyett. További információ a `RoleEnvironment` osztály, lásd: [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Olvassa el a Twilio biztonsági irányelveknek, [ https://www.twilio.com/docs/security ] [ twilio_docs_security].
* További információ a Twilio [ https://www.twilio.com/docs ] [ twilio_docs].

## <a name="seealso"></a>Lásd még:
* [Hogyan lehet a Twilio használata Hanghívási és SMS-funkciókat az Azure-ból](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
