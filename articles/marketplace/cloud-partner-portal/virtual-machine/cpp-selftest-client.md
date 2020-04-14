---
title: Önteszt-ügyfél a virtuális gép előzetes érvényesítéséhez | Azure Piactér
description: Önteszt-ügyfél létrehozása az Azure Piactér virtuálisgép-lemezképének előzetes érvényesítéséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: af42476f9d04f7f2bfc275c731b02aa5a9b8ecf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273154"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Önteszt-ügyfél létrehozása az Azure virtuálisgép-lemezkép előzetes érvényesítéséhez

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure VM-lemezkép-minősítés](https://aks.ms/CertifyVMimage) utasításait az áttelepített ajánlatok kezeléséhez.

Használja ezt a cikket útmutatóként egy ügyfélszolgáltatás létrehozásához, amely felhasználja az önteszt API-t. Az önteszt API-val előzetesen érvényesíthet egy virtuális gépet (VM) annak érdekében, hogy az megfeleljen az Azure Marketplace legújabb közzétételi követelményeinek. Ez az ügyfélszolgáltatás lehetővé teszi a virtuális gép tesztelését a Microsoft-minősítési ajánlat elküldése előtt.

## <a name="development-and-testing-overview"></a>Fejlesztési és tesztelési áttekintés

Az öntesztelési folyamat részeként létre fog hozni egy helyi ügyfelet, amely csatlakozik az Azure Piactérhez az Azure-előfizetésben futó virtuális gép érvényesítéséhez. A virtuális gép futtathatja a Windows vagy Linux operációs rendszert.

A helyi ügyfél olyan parancsfájlt futtat, amely hitelesíti magát az önteszt API-val, kapcsolati adatokat küld, és megkapja a teszteredményeket.

Az öntesztügyfél létrehozásának magas szintű lépései a következők:

1. Válassza ki az Azure Active Directory (AD) bérlőaz alkalmazáshoz.
2. Regisztrálja az ügyfélalkalmazást.
3. Hozzon létre egy jogkivonatot az ügyfél Azure AD alkalmazáshoz.
4. Adja át a jogkivonatot az önteszt API-nak.

Miután létrehozta az ügyfelet, tesztelheti azt a virtuális gépével szemben.

### <a name="self-test-client-authorization"></a>Önteszt ügyfél engedélyezése

Az alábbi ábra bemutatja, hogyan működik az ügyfélhitelesítő adatok (megosztott titok vagy tanúsítvány) használatával érkező szolgáltatás-szolgáltatáshívások engedélyezése.

![Ügyfél engedélyezési folyamata](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Az önteszt ügyfél API-ja

Az önteszt API egyetlen végpontot tartalmaz, amely csak a POST metódust támogatja.  Ez a következő struktúrát.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

Az alábbi táblázat az API-mezőket ismerteti.


|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
|  Engedélyezés     |  A "Bearer xxxx-xxxx-xxxx-xxxxx" karakterlánc tartalmazza az Azure Active Directory (AD) ügyféljogkivonatot, amely a PowerShell használatával hozható létre.          |
|  DNS-név           |  A tesztelendő virtuális gép DNS-neve    |
|  Felhasználó              |  Felhasználónév a virtuális gépbe való bejelentkezéshez         |
|  Jelszó          |  Jelszó a virtuális gépbe való bejelentkezéshez          |
|  Operációs rendszer                |  A virtuális gép operációs `Linux` rendszere: vagy`Windows`          |
|  PortNo között            |  Nyissa meg a portszámát a virtuális géphez való csatlakozáshoz. A portszám általában `22` Linux `5986` és Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Az API fogyasztása

Az önteszt API-t a PowerShell vagy a cURL használatával használhatja fel.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Az API-használat a Linux operációs rendszeren a PowerShell használatával

Az API-k hívásához hajtsa végre az alábbi lépéseket:

1. Használja `Invoke-WebRequest` a parancsot az API-híváshoz.
2. A módszer posta és a tartalom típusa JSON, amint az a következő kód példa és képernyő elfog.
3. Adja meg a törzs paramétereit JSON formátumban.

A következő kód példa egy PowerShell-hívást mutat be az API-hoz.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
A következő képernyőfelvétel egy példát mutat be az API-k PowerShellben való hívására.

![API-hívás Linuxos operációs rendszerhez a PowerShell segítségével](./media/stclient-call-api-ps-linuxvm.png)

Az előző példában lekérheti a JSON-t, és elemezheti a következő részleteket:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A következő képernyőfelvétel, `$res.Content`amely azt mutatja, hogy a részleteket a vizsgálati eredmények JSON formátumban.

![JSON-eredmények a PowerShell Linux-hívásából](./media/stclient-pslinux-rescontent-json.png)

A következő képernyőfelvétel egy példát mutat be az online JSON-megjelenítőben megtekintett JSON-teszteredményekre (például [Code Beautify](https://codebeautify.org/jsonviewer) vagy [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON-eredmények a PowerShell linuxos virtuális gépre hívásából](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Az API használata a Windows operációs rendszeren a PowerShell használatával

Az API-k hívásához hajtsa végre az alábbi lépéseket:

1. Használja `Invoke-WebRequest` a parancsot az API-híváshoz.
2. A módszer posta és a tartalom típusa JSON, amint az a következő kód példa és képernyő elfog.
3. A Body paraméterek létrehozása JSON formátumban.

A következő kód példa egy PowerShell-hívást mutat be az API-hoz.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

A következő képernyőfelvétel egy példát mutat be az API-k PowerShellben való hívására.

![API-hívás a Windows VM PowerShell szolgáltatásával](./media/stclient-call-api-ps-windowsvm.png)

Az előző példában lekérheti a JSON-t, és elemezheti a következő részleteket:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A következő képernyőfelvétel, `$res.Content`amely azt mutatja, hogy a részleteket a vizsgálati eredmények JSON formátumban.

![JSON-eredmények a Windows PowerShell-hívásából](./media/stclient-pswindows-rescontent-json.png)

A következő képernyőfelvétel egy online JSON-megjelenítőben megtekintett teszteredményeket jelenít meg.
(például [Code Szimány](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer))

![JSON-eredmények a Windows virtuális gép PowerShell-hívásából](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>A cURL használata az API-használathoz a Linux operációs rendszeren

Az API cURL-lel történő hívásához kövesse az alábbi lépéseket:

1. Használja a curl parancsot az API-híváshoz.
2. A módszer posta és tartalomtípus JSON, ahogy az a következő kódrészlet.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

A következő képernyőn egy példa a curl az API hívásához.

![API hívása a Curl paranccsal](./media/stclient-consume-api-curl.png)

A következő képernyőfelvétel en látható a JSON-találat a curl hívásból.

![JSON eredmények curl hívás](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Válassza ki az Azure AD-bérlőt az alkalmazáshoz

A következő lépésekkel válassza ki az Azure AD-bérlőt, ahol létre szeretné hozni az alkalmazást.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A felső menüsorban válassza ki a fiókját, és a Címtár listában válassza ki azt az Active Directory-bérlőt, ahol regisztrálni szeretné az alkalmazást. Vagy válassza ki a **Könyvtár + Előfizetés** ikont a Globális előfizetésszűrő megtekintéséhez. A következő képernyőfelvétel en látható egy példa erre a szűrőre.

   ![Az előfizetésszűrő kiválasztása](./media/stclient-subscription-filter.png)

3. A bal oldali navigációs sávon válassza a **Minden szolgáltatás** lehetőséget, majd az **Azure Active Directory**lehetőséget.

   A következő lépésekben szükség lehet a bérlő nevére (vagy könyvtárnevére) vagy a bérlői azonosítóra (vagy könyvtárazonosítóra).

   **Bérlői adatok beszerezése:**

   Az **Azure Active Directory áttekintése**párbeszédpanelen keressen rá a "Tulajdonságok" kifejezésre, és válassza a **Tulajdonságok**lehetőséget. A következő képernyőfelvétel használata példaként:

   - **Név** - A bérlő neve vagy könyvtárneve
   - **Címtárazonosító** – A bérlőazonosító vagy a könyvtárazonosító, vagy a görgetősáv segítségével keresse meg a Tulajdonságokat.

   ![Az Azure Active Directory tulajdonságai lap](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Az ügyfélalkalmazás regisztrálása

Az ügyfélalkalmazás regisztrálásához kövesse az alábbi lépéseket.

1. A bal oldali navigációs sávon válassza a **Minden szolgáltatás** lehetőséget, majd válassza **az Alkalmazásregisztrációk**lehetőséget.
2. Az **Alkalmazásregisztrációk csoportban**válassza a **+ Új alkalmazásregisztráció**lehetőséget.
3. A **Létrehozás**csoportban adja meg a következő mezőkhöz szükséges adatokat:

   - **Név** – Adja meg az alkalmazás rövid nevét. Például a "SelfTestClient".
   - **Alkalmazás típusa** – **Webalkalmazás/API** kiválasztása
   - **Bejelentkezési URL -** Írja\/be a "https: /isvapp.azurewebsites.net/selftest-vm" kifejezést

4. Kattintson a **Létrehozás** gombra.
5. Az **Alkalmazásregisztrációk** vagy **a Regisztrált alkalmazás csoportban**másolja az **alkalmazásazonosítót.**

   ![Az alkalmazásazonosító beszereznie](./media/stclient-app-id.png)

6. A regisztrált alkalmazáseszköztáron válassza a **Beállítások lehetőséget.**
7. Válassza a **Szükséges engedélyek lehetőséget** az alkalmazás engedélyeinek konfigurálásához.
8. A **Szükséges engedélyek csoportban**válassza a **+ Hozzáadás**lehetőséget.
9. Az **API-hozzáférés hozzáadása**csoportban válassza **az API kiválasztása**lehetőséget.
10. Az **API kiválasztása csoportban**írja be a "Klasszikus Windows Azure központi telepítési modell" kifejezést az API kereséséhez.
11. A keresési eredmények között válassza a **klasszikus Windows Azure központi telepítési modellt,** majd kattintson a Kijelölés **gombra.**

    ![Több-bérlős konfigurálás az alkalmazáshoz](./media/stclient-select-api.png)

12. Az **API-hozzáférés hozzáadása csoportban**válassza **az Engedélyek kiválasztása**lehetőséget.
13. Válassza **az Access "Windows Azure Service Management API" (Hozzáférés a Windows Azure Service Management API) lehetőséget.**

    ![API-hozzáférés engedélyezése az alkalmazáshoz](./media/stclient-enable-api-access.png)

14. Kattintson a **Kiválasztás** gombra.
15. Válassza a **Done** (Kész) lehetőséget.
16. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
17. A **Tulajdonságok csoportban**görgessen le a **Több-bérlős lehetőségig.** Válassza az **Igen** lehetőséget.

    ![Több-bérlős konfigurálás az alkalmazáshoz](./media/stclient-yes-multitenant.png)

18. Kattintson a **Mentés** gombra.
19. A **Beállítások csoportban**válassza a **Billentyűk lehetőséget.**
20. Titkos kulcs létrehozása a Kulcs **leírása** szövegmező kiválasztásával. Konfigurálja a következő mezőket:

    - Írjon be egy kulcsnevet. Például a "selftestclient"
    - A **EXPIRES** legördülő listában válassza az "Egy év múlva" lehetőséget.
    - A kulcs létrehozásához válassza a **Mentés** lehetőséget.
    - Az **ÉRTÉK csoportban**másolja a kulcsot.

      >[!Important]
      >A **Kulcsok** űrlapból való kilépés után nem fogja látni a kulcs értékét.

    ![Kulcsérték-űrlap](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>A jogkivonat létrehozása az ügyfélalkalmazáshoz

Az alábbi programok bármelyikével létrehozhat és beszerezhet egy jogkivonatot az OAuth REST API használatával:

- Postman
- cURL Linux alatt
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Token létrehozása és bekéselése a Postman használatával

 Ha az Auth0-tól jogkivonatokat szeretne kérni bármelyik engedélyezett [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) alkalmazáshoz, hajtson végre egy POST-műveletet a végponton, amelynek hasznos adatai a következő formátumban vannak:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Adja át a következő paramétereket a Kérelem törzsében:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Adja át a következő paramétereket a Kérelem fejlécében:

```
Content-Type: application/x-www-form-urlencoded
```

A következő képernyőfelvétel egy példát mutat be a Postman segítségével a token beírására.

![Token beszereznie a Postmansegítségével](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Token létrehozása és bekapása cURL használatával Linux alatt

Ha az Auth0-tól jogkivonatokat szeretne kérni bármelyik engedélyezett [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) alkalmazáshoz, hajtson végre egy POST-műveletet a végponton, amelynek hasznos adatai a következő formátumban vannak:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

A következő képernyőfelvétel egy példát mutat be a curl paranccsal egy token beírására.

![Token beszereznie a curl paranccsal](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Token létrehozása és bekésezése a C&#35; használatával

Ha az Auth0-tól jogkivonatokat szeretne kérni bármelyik engedélyezett alkalmazáshoz, hajtson végre egy POST-műveletet a\/https:/soamtenant.auth0.com/oauth/token végponton, amelynek hasznos adatai a következő formátumban vannak:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Jogkivonat létrehozása és lekérésa a PowerShell használatával

Ha az Auth0-tól jogkivonatokat szeretne kérni bármelyik engedélyezett alkalmazáshoz, hajtson végre egy POST-műveletet a\/https:/soamtenant.auth0.com/oauth/token végponton, amelynek hasznos adatai a következő formátumban vannak:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Az ügyfélalkalmazás tokenjének áthárítása az API-nak

Adja át a jogkivonatot az önteszt API-nak az engedélyezési fejlécben található következő kód használatával:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Tesztelje az önteszt kliens

Az ügyfél teszteléséhez kövesse az alábbi lépéseket:

1. Telepítse a tesztelni kívánt virtuális gép.
2. Hívja meg az önteszt API-t az ügyfélalkalmazás tokenje használatával az engedélyezéshez.
3. Get a vizsgálati eredmények JSON formátumban.

### <a name="test-result-examples"></a>Példák a teszteredményre

A következő kódrészletek JSON formátumban mutatják be a vizsgálati eredményeket.

**Windows virtuális gép teszteredményei:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux os virtuális gép teszteredményei:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>További lépések

Miután sikeresen tesztelte az Azure virtuális gépét, [közzéteheti az ajánlatot.](./cpp-publish-offer.md)
