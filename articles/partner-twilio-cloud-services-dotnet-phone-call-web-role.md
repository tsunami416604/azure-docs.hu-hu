<properties urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf"></tags>

# How to make a phone call using Twilio in a web role on Azure

This guide demonstrates how to use Twilio to make a call from a web page hosted in Azure. The resulting application prompts the user for phone call values, as shown in the following screenshot.

![Azure call form using Twilio and ASP.NET][Azure call form using Twilio and ASP.NET]

## Table of Contents

-   [Prerequisites][Prerequisites]
-   [How to: Create a web form for making a call][How to: Create a web form for making a call]
-   [How to: Create the code to make the call][How to: Create the code to make the call]
-   [Next steps][Next steps]
-   [See also][See also]

## <a name="twilio-prereqs"></a>Prerequisites

You will need to do the following to use the code in this topic:

1.  Acquire a Twilio account and authentication token. To get started with Twilio, sign up at [][]<https://www.twilio.com/try-twilio></a>. You can evaluate pricing at [][1]<http://www.twilio.com/pricing></a>. For information about the API provided by Twilio, see [][2]<http://www.twilio.com/voice/api></a>.
2.  Verify your phone number with Twilio. For information on how to verify your phone number, see [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. As an alternative to using an existing number, you can purchase a Twilio phone number.
    For the purposes of this example you will use the Twilio sandbox phone number to send a message to the verified phone number. You can only use the sandbox phone number to send to verified phone numbers.
3.  Add the Twilio .NET libary to your web role. See "To add the Twilio libraries to your web role project," later in this topic.

You should be familiar with creating a basic web role on Azure.

## <a name="howtocreateform"></a>How to: Create a web form for making a call

### <span id="use_nuget"></span></a>To add the Twilio libraries to your web role project:

1.  Open your solution in Visual Studio.
2.  Right-click **References**.
3.  Click **Manage NuGet Packages**.
4.  Click **Online**.
5.  In the search online box, type *twilio*.
6.  Click **Install** on the Twilio package.

The following code shows how to create a web form to retrieve user data for making a call. In this example, an ASP.NET web role named **TwilioCloud** is created.

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

## <span id="howtocreatecode"></span></a>How to: Create the code to make the call

The following code, which is called when the user completes the form, creates the call message and generates the call. In this example, the code is run in the onclick event handler of the button on the form. (Use your Twilio account and authentication token instead of the placeholder values assigned to **accountSID** and **authToken** in the code below.)

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

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
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

The call is made, and the Twilio endpoint, API version, and the call status are displayed. The following screenshot shows output from a sample run.

![Azure call response using Twilio and ASP.NET][Azure call response using Twilio and ASP.NET]

More information about TwiML can be found at [][4]<http://www.twilio.com/docs/api/twiml></a>. More information about \<Say\> and other Twilio verbs can be found at [][5]<http://www.twilio.com/docs/api/twiml/say></a>.

## <span id="nextsteps"></span></a>Next steps

This code was provided to show you basic functionality using Twilio in an ASP.NET web role on Azure. Before deploying to Azure in production, you may want to add more error handling or other features. For example:

-   Instead of using a web form, you could use Azure Blob storage or an Azure SQL Database instance to store phone numbers and call text. For information about using blobs in Azure, see [How to use the Azure Blob storage service in .NET][How to use the Azure Blob storage service in .NET]. For information about using SQL Database, see [How to use Azure SQL Database in .NET applications][How to use Azure SQL Database in .NET applications].
-   You could use RoleEnvironment.getConfigurationSettings to retrieve the Twilio account ID and authentication token from your deployment's configuration settings, instead of hard-coding the values in your form. For information about the RoleEnvironment class, see [Microsoft.WindowsAzure.ServiceRuntime Namespace][Microsoft.WindowsAzure.ServiceRuntime Namespace].
-   Read the Twilio security guidelines at [][6]<https://www.twilio.com/docs/security></a>.
-   Learn more about Twilio at [][7]<https://www.twilio.com/docs></a>.

## <a name="seealso"></a>See also

-   [How to use Twilio for voice and SMS capabilities from Azure][How to use Twilio for voice and SMS capabilities from Azure]

  [Azure call form using Twilio and ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Prerequisites]: #twilio-prereqs
  [How to: Create a web form for making a call]: #howtocreateform
  [How to: Create the code to make the call]: #howtocreatecode
  [Next steps]: #nextsteps
  [See also]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Azure call response using Twilio and ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [How to use the Azure Blob storage service in .NET]: https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/
  [How to use Azure SQL Database in .NET applications]: https://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/
  [Microsoft.WindowsAzure.ServiceRuntime Namespace]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [How to use Twilio for voice and SMS capabilities from Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
