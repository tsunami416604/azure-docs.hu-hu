---
title: Microsoft Power BI Embedded előzetes verzió – Power BI-jelentés beágyazása IFrame használatával
description: Microsoft Power BI Embedded előzetes verzió – Alapvető kódok a jelentések alkalmazásba történő integrálásához; hitelesítés Power BI Embedded alkalmazási jogkivonattal; jelentések lekérése
services: power-bi-embedded
documentationcenter: ''
author: dvana
manager: NA
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 05/16/2016
ms.author: derrickv

---
# Power BI-jelentés beágyazása IFrame segítségével
Ez a cikk azt ismerteti, hogyan lehet egy alapvető kóddal jelentést integrálni vagy beágyazni az alkalmazásba a **Power BI Embedded** REST API, alkalmazási jogkivonatok, IFrame és egyes JavaScript-kódok használatával.

Az [Ismerkedés a Microsoft Power BI Embedded előzetes verziójával](power-bi-embedded-get-started.md) című témakör bemutatja, hogyan lehet a jelentéstartalomhoz egy vagy több **munkaterületet** tartalmazó **munkaterület-csoportot** létrehozni. Az [Ismerkedés a Microsoft Power BI Embedded mintaverziójával](power-bi-embedded-get-started-sample.md) című témakörben pedig megismerheti, hogyan importálhat jelentést egy **munkaterületre**.

Ez a cikk azt mutatja be, hogyan lehetséges jelentéseket beágyazni az alkalmazásba. Az alábbi lépések követése érdekében töltse le a [Jelentés integrálása IFrame használatával](https://github.com/Azure-Samples/power-bi-embedded-iframe) mintát a GitHubon. Ez a minta valójában egy egyszerű ASP.NET webes űrlapalkalmazás, melyet arra terveztek, hogy bemutassa a jelentések integrálásához szükséges C#- és JavaScript-kódokat. Ha a jelentés integrálásához szívesebben választana egy összetettebb, MVC (Model-View-Controller) szerkezetet használó mintát, keresse meg a [Minta irányítópult webalkalmazás](http://go.microsoft.com/fwlink/?LinkId=761493) mintát a GitHubon.

A következőkben bemutatjuk, hogyan lehet **Power BI Embedded**-jelentéseket integrálni egy alkalmazásba.

A jelentések integrálásának lépései a következők:

* 1. lépés: [Jelentés lekérése a munkaterületen](#GetReport). Ebben a lépésben egy alkalmazás jogkivonat-folyamata segítségével be kell szereznie egy hozzáférési jogkivonatot, amellyel meghívhatja a [Jelentések lekérése](https://msdn.microsoft.com/library/mt711510.aspx) REST-műveletet. Miután a **Jelentések lekérése** listából lekérte a jelentést, **IFrame**-elemmel ágyazza be azt egy alkalmazásba.
* 1. lépés: [Jelentés beágyazása az alkalmazásba](#EmbedReport). Ebben a lépésben egy jelentés beágyazási jogkivonata, JavaScript és egy IFrame-keret segítségével integrálja vagy beágyazza a jelentést egy webalkalmazásba.

Ha futtatni szeretné a jelentés integrálását bemutató mintát, töltse le a [Jelentés integrálása IFrame használatával](https://github.com/Azure-Samples/power-bi-embedded-iframe) mintát a GitHubon, és konfigurálja a következő három Web.Config beállítást:

* **AccessKey**: Az **AccessKey** beállítással egy JSON webes jogkivonat (JWT) hozható létre, amely a jelentések lekérésére és beágyazására egyaránt használható. Az **AccessKey** beszerzéséről további információt az [Ismerkedés a Microsoft Power BI Embedded előzetes verziójával](power-bi-embedded-get-started.md) című témakörben talál.
* **WorkspaceName**: A **WorkspaceName** beszerzéséről további információt az [Ismerkedés a Microsoft Power BI Embedded előzetes verziójával](power-bi-embedded-get-started.md) című témakörben talál.
* **WorkspaceId**: A **WorkspaceId** beszerzéséről további információt az [Ismerkedés a Microsoft Power BI Embedded előzetes verziójával](power-bi-embedded-get-started.md) című témakörben talál.

A következő szakasz a jelentés integrálásához szükséges kódot mutatja be.

<a name="GetReport"/>

## Jelentés lekérése a munkaterületen
A jelentés integrálásához szüksége lesz egy jelentés-**azonosítóra** és egy **embedUrl**-címre. A jelentés-**azonosító** és az **embedUrl**-cím beszerzéséhez hívja meg a [Jelentések lekérése](https://msdn.microsoft.com/library/mt711510.aspx) REST-műveletet, majd a JSON-listából válassza ki a kívánt jelentést. A [Jelentés beágyazása alkalmazásba](#EmbedReport) szakaszban ezzel a jelentés-**azonosítóval** és **embedUrl**-címmel ágyazhatja be a jelentést az alkalmazásba.

### JSON-válasz lekérése a jelentésekhez
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

A [Jelentések lekérése](https://msdn.microsoft.com/library/mt711510.aspx) REST-művelet meghívásához használjon egy alkalmazási jogkivonatot. Az alkalmazások jogkivonat-folyamatáról további információt [Az alkalmazások jogkivonat-folyamatának ismertetése a Power BI Embeddedben](power-bi-embedded-app-token-flow.md) című témakörben talál. A következő kód bemutatja, hogyan kérhetők le a JSON-jelentéslisták. A jelentések beágyazásáról további információt a [Jelentés beágyazása egy alkalmazásba](#EmbedReport) című szakaszban talál.

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>

## Jelentés beágyazása egy alkalmazásba
Mielőtt beágyazhatná a kiválasztott jelentést az alkalmazásba, be kell szereznie a jelentéshez tartozó beágyazási jogkivonatot. Ez a jogkivonat nagyon hasonló a **Power BI Embedded** REST-műveletek meghívásához használt alkalmazási jogkivonathoz, azonban a REST-erőforrás helyett a jelentés erőforrásához jön létre. A következő kód segítségével kérheti le a jelentés alkalmazási jogkivonatát. A jelentés alkalmazási jogkivonatának használatáról további információt a [Jelentés beágyazása egy alkalmazásba](#EmbedReportJS) című szakaszban talál.

<a name="EmbedReportToken"/>

### A jelentés alkalmazási jogkivonatának lekérése
```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>

### Jelentés beágyazása saját alkalmazásba
A **Power BI**-jelentést IFrame- és néhány JavaScript-kód segítségével ágyazhatja be az alkalmazásba. A következő példa a jelentés beágyazásához szükséges IFrame- és JavaScript-kódot mutatja be. A jelentések beágyazásához szükséges összes mintakódot a [Jelentés integrálása IFrame használatával](https://github.com/Azure-Samples/power-bi-embedded-iframe) mintában találja a GitHubon.

![Iframe](media\\power-bi-embedded-integrate-report\\Iframe.png)

```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Miután beágyazta a jelentést az alkalmazásba, a jelentést szűrheti is. A következő szakasz bemutatja, hogyan használható egy URL-cím szintaxisa a jelentések szűréséhez.

## Jelentés szűrése
A beágyazott jelentések szűrését elvégezheti egy URL-cím szintaxisával. Ehhez egy lekérdezési karakterlánc paramétert kell adnia a meghatározott szűrőt tartalmazó iFrame src url-címhez. Végezhet **érték szerinti szűrést**, illetve **elrejtheti a keresőablakot**.

**Szűrés érték szerint**

Érték szerinti szűréshez használjon egy **eq** operátort tartalmazó **$filter** lekérdezési szintaxist az alábbiak szerint:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Végezhet például olyan szűrést, ahol a Store Chain (Üzletlánc) értéke a „Lindseys”. Az URL-cím szűrő része a következőképpen néz ki:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [!NOTE]
> A {tableName/fieldName} nem tartalmazhat szóközt és speciális karaktereket. A {fieldValue} egyetlen kategorikus értéket enged.
> 
> 

**A keresőablak elrejtése**

A **keresőablak** elrejtéséhez be kell építenie a **filterPaneEnabled** értéket a jelentés lekérdezési karakterláncába az alábbiak szerint:

```
&filterPaneEnabled=false
```

## Összegzés
A fenti cikk a **Power BI**-jelentések alkalmazásokba történő beágyazásához szükséges kódot mutatta be. Ha gyorsan meg szeretne ismerkedni azzal, hogyan lehet jelentéseket integrálni egy alkalmazásba, töltse le a GitHubról a következő mintákat:

* [Jelentés integrálása minta IFrame használatával](https://github.com/Azure-Samples/power-bi-embedded-iframe)
* [Minta irányítópult webalkalmazás](http://go.microsoft.com/fwlink/?LinkId=761493)

## Lásd még:
* [Ismerkedés a Microsoft Power BI Embedded előzetes verziójával](power-bi-embedded-get-started.md)
* [Bevezetés a minta használatába](power-bi-embedded-get-started-sample.md)
* [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
* [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
* [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
* [Jelentések lekérése](https://msdn.microsoft.com/library/mt711510.aspx)

<!--HONumber=Jun16_HO2--->


