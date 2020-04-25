---
title: Önteszt ügyfél a virtuális gép előzetes érvényesítéséhez | Azure piactér
description: Önteszt ügyfél létrehozása az Azure Marketplace-hez készült virtuálisgép-rendszerkép előzetes érvényesítéséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: 9f16d26fa95254282e453cd7bf35d85f8b81ed73
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143197"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Önteszt ügyfél létrehozása Azure-beli virtuális gépek rendszerképének előzetes érvényesítéséhez

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [Azure VM-rendszerkép minősítésének](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) utasításait.

Ez a cikk útmutatóként szolgál az önteszt API-t használó ügyfélszolgáltatás létrehozásához. Az önteszt API-val előre érvényesítheti a virtuális gépet (VM), így biztosítva, hogy az megfeleljen az Azure Marketplace legújabb közzétételi követelményeinek. Ez az ügyfélszolgáltatás lehetővé teszi a virtuális gépek tesztelését, mielőtt elküldi a Microsoft minősítési ajánlatát.

## <a name="development-and-testing-overview"></a>Fejlesztés és tesztelés áttekintése

Az önteszt folyamat részeként létre fog hozni egy helyi ügyfelet, amely csatlakozik az Azure Marketplace-hez az Azure-előfizetésében futó virtuális gépek érvényesítéséhez. A virtuális gép a Windows vagy Linux operációs rendszert is futtathatja.

A helyi ügyfél egy olyan parancsfájlt futtat, amely az önteszt API-val hitelesít, a kapcsolati adatokat küld, és a teszt eredményeit fogadja.

Az önteszt ügyfelek létrehozásának magas szintű lépései a következők:

1. Válassza ki az alkalmazás Azure Active Directory (AD) bérlőjét.
2. Az ügyfélalkalmazás regisztrálása.
3. Hozzon létre egy tokent az ügyfél Azure AD-alkalmazáshoz.
4. Adja át a tokent az önteszt API-nak.

Az ügyfél létrehozása után tesztelheti a virtuális gépre.

### <a name="self-test-client-authorization"></a>Ügyfél-hitelesítés önellenőrzése

Az alábbi ábra azt mutatja be, hogyan működik a hitelesítés a szolgáltatás és az ügyfél hitelesítő adatai (közös titok vagy tanúsítvány) használatával.

![Ügyfél-engedélyezési folyamat](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Az önellenőrzés ügyfél API-ját

Az önteszt API egyetlen végpontot tartalmaz, amely csak a POST metódust támogatja.  A következő szerkezettel rendelkezik.

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
|  Engedélyezés     |  A "Bearer xxxx-xxxx-xxxx-xxxxx" karakterlánc tartalmazza a Azure Active Directory (AD) ügyfél-jogkivonatot, amely a PowerShell használatával hozható létre.          |
|  DNSName           |  A tesztelni kívánt virtuális gép DNS-neve    |
|  Felhasználó              |  A virtuális gépre való bejelentkezéshez használt Felhasználónév         |
|  Jelszó          |  A virtuális gépre való bejelentkezéshez használt jelszó          |
|  Operációs rendszer                |  A virtuális gép operációs rendszere: `Linux` vagy`Windows`          |
|  PortNo            |  Nyissa meg a virtuális géphez való csatlakozáshoz szükséges portszámot. A portszám általában `22` Linux és `5986` Windows rendszerekhez használható.          |
|  |  |

## <a name="consuming-the-api"></a>Az API fogyasztása

Az önteszt API-t használhatja a PowerShell vagy a cURL használatával.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Az API használata a Linux operációs rendszeren a PowerShell használatával

Az API PowerShellben való meghívásához kövesse az alábbi lépéseket:

1. Az API `Invoke-WebRequest` meghívásához használja az parancsot.
2. A metódus a post és a Content típus JSON, ahogy az alábbi kódrészletben látható.
3. A szövegtörzs paramétereit JSON formátumban kell megadni.

Az alábbi kódrészlet egy PowerShell-hívást mutat be az API-nak.

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
Az alábbi képernyőfelvételen egy példa látható az API PowerShellben való meghívására.

![API meghívása Linux operációs rendszerhez készült PowerShell-lel](./media/stclient-call-api-ps-linuxvm.png)

Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

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

A következő képernyőfelvétel, amely bemutatja `$res.Content`, a teszt eredményét JSON formátumban adja meg.

![JSON-eredmények a PowerShell-hívástól a Linuxra](./media/stclient-pslinux-rescontent-json.png)

Az alábbi képernyőfelvételen egy példa látható a JSON-teszt eredményeire egy online JSON-megjelenítőben (például [Code szépít](https://codebeautify.org/jsonviewer) vagy [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON-eredmények a PowerShell-hívástól a linuxos virtuális géphez](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Az API használata a Windows operációs rendszeren a PowerShell használatával

Az API PowerShellben való meghívásához kövesse az alábbi lépéseket:

1. Az API `Invoke-WebRequest` meghívásához használja az parancsot.
2. A metódus a post és a Content típus JSON, ahogy az alábbi kódrészletben látható.
3. Hozza létre a törzs paramétereit JSON formátumban.

Az alábbi kódrészlet egy PowerShell-hívást mutat be az API-nak.

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

Az alábbi képernyőfelvételen egy példa látható az API PowerShellben való meghívására.

![API meghívása a PowerShell-lel Windows rendszerű virtuális géphez](./media/stclient-call-api-ps-windowsvm.png)

Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

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

A következő képernyőfelvétel, amely bemutatja `$res.Content`, a teszt eredményét JSON formátumban adja meg.

![JSON-eredmények a Windows PowerShell-hívásból](./media/stclient-pswindows-rescontent-json.png)

Az alábbi képernyőfelvételen egy online JSON-megjelenítőben megtekintett teszt eredményei láthatók.
(például: [Code szépít](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer))

![JSON-eredmények a PowerShell-hívásról a Windows rendszerű virtuális gépre](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Az API használata a Linux operációs rendszeren a cURL használatával

Az API fürtön való meghívásához kövesse az alábbi lépéseket:

1. Hívja meg az API-t a curl parancs használatával.
2. A metódus a post és a Content típus JSON, ahogy az a következő kódrészletben látható.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Az alábbi képernyőn egy példa látható a curl használatára az API meghívásához.

![API meghívása a curl paranccsal](./media/stclient-consume-api-curl.png)

Az alábbi képernyőfelvételen a curl-hívásból származó JSON-eredmények láthatók.

![JSON-eredmények a curl-hívásból](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Válassza ki az alkalmazáshoz tartozó Azure AD-bérlőt

A következő lépésekkel kiválaszthatja azt az Azure AD-bérlőt, amelyben létre szeretné hozni az alkalmazást.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A felső menüsorban válassza ki a fiókját, és a címtár listáról válassza ki azt a Active Directory bérlőt, ahová regisztrálni kívánja az alkalmazást. Vagy válassza a **címtár + előfizetés** ikont a globális előfizetés-szűrő megjelenítéséhez. Az alábbi képernyőfelvételen egy példa látható erre a szűrőre.

   ![Előfizetés-szűrő kiválasztása](./media/stclient-subscription-filter.png)

3. A bal oldali navigációs sávon válassza a **minden szolgáltatás** lehetőséget, majd válassza a **Azure Active Directory**lehetőséget.

   A következő lépésekben szükség lehet a bérlő nevére (vagy a könyvtár nevére) vagy a bérlői AZONOSÍTÓra (vagy a címtár-AZONOSÍTÓra).

   **Bérlői információk beszerzése:**

   **Azure Active Directory áttekintés**lapon keressen rá a "tulajdonságok" kifejezésre, majd válassza a **Tulajdonságok**lehetőséget. A következő képernyőfelvétel használata példaként:

   - **Név** – a bérlő neve vagy a könyvtár neve
   - **Címtár-azonosító** – a bérlő azonosítója vagy könyvtára, vagy használja a görgetősávot a tulajdonságok megkereséséhez.

   ![Azure Active Directory Tulajdonságok lap](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Az ügyfélalkalmazás regisztrálása

Az ügyfélalkalmazás regisztrálásához kövesse az alábbi lépéseket.

1. A bal oldali navigációs sávon válassza a **minden szolgáltatás** lehetőséget, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
2. A **Alkalmazásregisztrációk**területen válassza az **+ új alkalmazás regisztrálása**lehetőséget.
3. A **Létrehozás**területen adja meg a következő mezőkhöz szükséges adatokat:

   - **Név** – adjon meg egy rövid nevet az alkalmazás számára. Például: "SelfTestClient".
   - **Alkalmazás típusa** – **Web App/API** kiválasztása
   - **Bejelentkezési URL-cím** – típus: "https\/:/isvapp.azurewebsites.net/selftest-VM"

4. Kattintson a **Létrehozás** gombra.
5. Az **Alkalmazásregisztrációk** vagy a **regisztrált alkalmazás**területen másolja az **alkalmazás azonosítóját**.

   ![Az alkalmazás AZONOSÍTÓjának beolvasása](./media/stclient-app-id.png)

6. A regisztrált alkalmazás eszköztárán válassza a **Beállítások**lehetőséget.
7. Az alkalmazás engedélyeinek konfigurálásához válassza a **szükséges engedélyek** lehetőséget.
8. A **szükséges engedélyek**területen válassza a **+ Hozzáadás**lehetőséget.
9. Az **API-hozzáférés hozzáadása**területen **válassza ki az API**-t.
10. Az API **kiválasztása**területen írja be a "Windows Azure klasszikus üzembehelyezési modellje" kifejezést az API megkereséséhez.
11. A keresési eredmények között válassza a **klasszikus Windows Azure üzembehelyezési modell** lehetőséget, majd kattintson a **kiválasztás**elemre.

    ![Több-bérlős alkalmazás konfigurálása](./media/stclient-select-api.png)

12. Az **API-hozzáférés hozzáadása**területen **válassza az engedélyek kiválasztása lehetőséget**.
13. Válassza a **hozzáférés a "Windows Azure Service Management API"** lehetőséget.

    ![API-hozzáférés engedélyezése az alkalmazáshoz](./media/stclient-enable-api-access.png)

14. Kattintson a **Kiválasztás** gombra.
15. Válassza a **Done** (Kész) lehetőséget.
16. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
17. A **Tulajdonságok**területen görgessen le a **több-bérlős**elemre. Válassza az **Igen** lehetőséget.

    ![Több-bérlős alkalmazás konfigurálása](./media/stclient-yes-multitenant.png)

18. Kattintson a **Mentés** gombra.
19. A **Beállítások**területen válassza a **kulcsok**elemet.
20. Hozzon létre egy titkos kulcsot a Key **description** (kulcs leírása) szövegmező kiválasztásával. Konfigurálja a következő mezőket:

    - Írja be a kulcs nevét. Például: "selftestclient"
    - A **lejárati** legördülő listában válassza az "1 év" lehetőséget.
    - A kulcs létrehozásához válassza a **Mentés** lehetőséget.
    - Az **érték**alatt másolja a kulcsot.

      >[!Important]
      >A **kulcsok** űrlapból való kilépés után nem fogja tudni megtekinteni a kulcs értékét.

    ![Kulcs értékének űrlapja](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Az ügyfélalkalmazás jogkivonatának létrehozása

A következő programok bármelyikével létrehozhat és lekérhet jogkivonatot a OAuth REST API használatával:

- Postman
- cURL Linuxon
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Token létrehozása és lekérése a Poster használatával

 Ahhoz, hogy Auth0 kérdezzen a jogkivonatok bármelyikének a jogosult alkalmazásaihoz, a következő [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) formátumban hajtson végre egy post műveletet a végponton egy adattartalommal:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Adja át a következő paramétereket a kérelem törzsében:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Adja át a következő paramétereket a kérelem fejlécében:

```
Content-Type: application/x-www-form-urlencoded
```

Az alábbi képernyőfelvételen egy példa látható a Poster-token beszerzésére.

![Token beszerzése a Poster-vel](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Token létrehozása és lekérése a cURL használatával Linuxon

Ahhoz, hogy Auth0 kérdezzen a jogkivonatok bármelyikének a jogosult alkalmazásaihoz, a következő [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) formátumban hajtson végre egy post műveletet a végponton egy adattartalommal:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Az alábbi képernyőfelvételen egy példa látható a curl parancs használatára a jogkivonat lekéréséhez.

![Token beolvasása a curl paranccsal](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Token létrehozása és lekérése C&#35; használatával

Ahhoz, hogy Auth0 Kérjen a jogkivonatok bármelyikét a jogosult alkalmazásaihoz, hajtson végre egy POST\/műveletet a https:/soamtenant.auth0.com/OAuth/token végponton egy hasznos adattartalommal a következő formátumban:

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

### <a name="to-create-and-get-a-token-using-powershell"></a>Token létrehozása és lekérése a PowerShell használatával

Ahhoz, hogy Auth0 Kérjen a jogkivonatok bármelyikét a jogosult alkalmazásaihoz, hajtson végre egy POST\/műveletet a https:/soamtenant.auth0.com/OAuth/token végponton egy hasznos adattartalommal a következő formátumban:

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

## <a name="pass-the-client-app-token-to-the-api"></a>Adja át az ügyfélalkalmazás tokenjét az API-nak

Adja át a jogkivonatot az önteszt API-nak az alábbi kód használatával az engedélyezési fejlécben:

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

## <a name="test-your-self-test-client"></a>Az önteszt ügyfél tesztelése

Az ügyfél teszteléséhez kövesse az alábbi lépéseket:

1. Telepítse a tesztelni kívánt virtuális gépet.
2. Az ügyfél-alkalmazási jogkivonat használatával hívja meg az önteszt API-t az engedélyezéshez.
3. A teszt eredményének lekérdezése JSON formátumban.

### <a name="test-result-examples"></a>Példa tesztelési eredményekre

A következő kódrészletek a teszt eredményét JSON formátumban jelenítik meg.

**Windows rendszerű virtuális gép tesztelési eredményei:**

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

**A Linux rendszerű virtuális gépek tesztelési eredményei:**

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

Az Azure-beli virtuális gép sikeres tesztelése után közzé teheti [az ajánlatot](./cpp-publish-offer.md).
