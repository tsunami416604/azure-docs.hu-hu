---
title: Ügyfél teszteléséhez való használatát a virtuális gépek – az Azure Marketplace-en |} A Microsoft Docs
description: Hogyan teszteléséhez ügyfél létrehozása előre ellenőrzése a virtuálisgép-lemezkép az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: 7afa64ebedb38b4514bbd155bf8f29268d420d18
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745758"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Hozzon létre egy teszteléséhez ügyfél való használatát egy Azure-beli virtuálisgép-lemezkép

Ez a cikk egy útmutató, amely egy ügyfél szolgáltatás létrehozására szolgáló használ fel, a teszteléséhez API-t használja. A helyi tesztelése API segítségével elővalidálási egy virtuális gépet (VM) annak érdekében, hogy megfelelnek-e a legújabb Azure Marketplace közzétételi követelményeknek. Az ügyfél szolgáltatás lehetővé teszi, hogy tesztelje egy virtuális Gépet, mielőtt elküldi az ajánlat a Microsoft általi hitelesítésre.


## <a name="development-and-testing-overview"></a>Fejlesztési és tesztelési áttekintése

A teszteléséhez folyamat részeként a helyi ügyfél, amely csatlakozik egy virtuális Gépet az Azure-előfizetés ellenőrzése az Azure Marketplace-en fogja létrehozni. A virtuális Géphez a Windows vagy Linux operációs rendszert futtathat.

A helyi ügyfél fut egy parancsfájl, amely hitelesíti a teszteléséhez API-val, kapcsolati adatokat küld és kap a vizsgálati eredmények.

A magas szintű lépések teszteléséhez ügyfél létrehozásához a következők:

1. Válassza ki az Azure Active Directory (AD) bérlő az alkalmazás.
2. Az ügyfél alkalmazás regisztrálásához.
3. Hozzon létre egy tokent, az ügyfél Azure AD-alkalmazás.
4. Adja meg a jogkivonatot a teszteléséhez API-t.

Miután létrehozta az ügyfél, tesztelheti a virtuális gép ellen.


### <a name="self-test-client-authorization"></a>Ügyfél-hitelesítés teszteléséhez

Az alábbi ábrán látható, az engedélyezés működése a szolgáltatások közötti hívások ügyfél-hitelesítő adatokkal (közös titkos kulcsot vagy tanúsítvány).

![Ügyfél-hitelesítési folyamat](./media/stclient-dev-process.png)


## <a name="the-self-test-client-api"></a>A helyi tesztelése ügyfél API

A teszteléséhez API tartalmaz egy végpontot, amely támogatja a POST-metódus.  Az alábbi struktúrával rendelkezik.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
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

A következő táblázat ismerteti az API-mezők.


|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
|  Engedélyezés     |  A "Tulajdonosi xxxx-xxxx-xxxx-xxxxx" karakterláncot tartalmazza az Azure Active Directory (AD) ügyfél jogkivonatot, amely PowerShell-lel hozható létre.          |
|  DNSName           |  DNS-név a virtuális gép tesztelése    |
|  Felhasználó              |  A virtuális géppel aláíráshoz felhasználónév         |
|  Jelszó          |  Jelentkezzen be a virtuális gép jelszava          |
|  Operációs rendszer                |  A virtuális gép operációs rendszerének: vagy `Linux` vagy `Windows`          |
|  PortNo            |  Nyissa meg a port számát a virtuális Géphez való csatlakozáshoz. A portszám általában van `22` Linux- és `5986` for Windows.          |
|  |  |


## <a name="consuming-the-api"></a>Az API-t használ

A PowerShell vagy a cURL használatával teszteléséhez API használhatja fel.


### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Az API-t a Linux operációs rendszer használata a PowerShell használatával

Az API meghívása a PowerShellben, kövesse az alábbi lépéseket:

1. Használja a `Invoke-WebRequest` parancsot az API-t.
2. A módszer a következő bejegyzés és tartalomtípus pedig JSON-t, ahogyan az az alábbi kód példa és a képernyő rögzítése.
3. Az üzenettörzs-paraméterek JSON formátumban kell megadni.

Az alábbi példakód bemutatja a PowerShell az API meghívásához.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers; 
$Content = $res | ConvertFrom-Json
```
Az alábbi képernyőfelvételen látható az API hívásakor a PowerShellben.

![A PowerShell-lel API hívása a Linux operációs rendszer](./media/stclient-call-api-ps-linuxvm.png)

Az előző példát, beolvashatja a JSON és elemezni, hogy a következő adatokat:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Az alábbi képernyőfelvétel, amelyen `$res.Content`, a műveletről a vizsgálati eredmények JSON formátumban.

![Linux PowerShell hívásából származó JSON-eredmények](./media/stclient-pslinux-rescontent-json.png)

Az alábbi képernyőfelvétel-készítés JSON terhelésiteszt-eredményei egy online JSON-megjelenítő megtekinteni egy példát mutat be (például [kód Beautify](https://codebeautify.org/jsonviewer) vagy [JSON-megjelenítő](https://jsonformatter.org/json-viewer)).

![Linux rendszerű virtuális gép PowerShell hívásából származó JSON-eredmények](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Felhasználása az API-t a Windows operációs rendszereken a PowerShell használatával

Az API meghívása a PowerShellben, kövesse az alábbi lépéseket:

1. Használja a `Invoke-WebRequest` parancsot az API-t.
2. A módszer a következő bejegyzés és tartalomtípus pedig JSON-t, ahogyan az az alábbi kód példa és a képernyő rögzítése.
3. Hozzon létre az üzenettörzs-paraméterek JSON formátumban.

Az alábbi példakód bemutatja a PowerShell az API meghívásához.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Az alábbi képernyőfelvételen látható az API hívásakor a PowerShellben.

![A PowerShell-lel API hívása Windows virtuális gép számára](./media/stclient-call-api-ps-windowsvm.png)

Az előző példát, beolvashatja a JSON és elemezni, hogy a következő adatokat:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Az alábbi képernyőfelvétel, amelyen `$res.Content`, a műveletről a vizsgálati eredmények JSON formátumban.

![Windows PowerShell JSON eredményeinek hívása](./media/stclient-pswindows-rescontent-json.png)

Az alábbi képernyőfelvétel-készítés képe egy online JSON-megjelenítő a teszt eredménye látható.
(például [kód Beautify](https://codebeautify.org/jsonviewer), [JSON-megjelenítő](https://jsonformatter.org/json-viewer))

![Windows virtuális gép PowerShell hívásából származó JSON-eredmények](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Az API-t a Linux operációs rendszer által a cURL használatával

A curl használatával az API meghívása, kövesse az alábbi lépéseket:

1. A curl-parancs segítségével az API-t.
2. A módszer a következő bejegyzés és tartalomtípus pedig JSON-t, az alábbi kódrészletben látható módon.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
A következő képernyő egy példa az API-t a curl használatával mutatja.

![A curl-paranccsal API meghívása](./media/stclient-consume-api-curl.png)

Az alábbi képernyőfelvételen látható a JSON a curl-hívás eredményei.

![A curl-hívás eredményei JSON](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Válassza ki az alkalmazás Azure AD-bérlő

Használatával a következő lépéseket az Azure AD-bérlőjében válassza ki kívánja az alkalmazás létrehozása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A felső menüsávon válassza ki a fiókját, és a Directory listája alatt válassza ki az Active Directory-bérlővel, ahol regisztrálja az alkalmazást szeretné. Vagy válassza ki a **címtár és előfizetés** ikonra kattintva megtekintheti a globális előfizetés-szűrőt. Az alábbi képernyőfelvétel-készítés Ez a szűrő egy példát mutat be.

   ![Válassza ki az előfizetés-szűrő](./media/stclient-subscription-filter.png)

3. A bal oldali navigációs sávján válassza **minden szolgáltatás** majd **Azure Active Directory**.

   A következő lépésekben szükség lehet a bérlő nevét (vagy a könyvtár neve), vagy a bérlő azonosítója (vagy a címtár-azonosító).

   **Bérlő információk lekéréséhez:**
  
   A **Azure Active Directory áttekintése**, keressen a "Tulajdonságok", majd **tulajdonságok**. Az alábbi képernyőfelvétel-készítés példaként használva:

   - **Név** – a bérlő nevét vagy a könyvtár neve
   - **Címtár-azonosító** – a bérlői azonosító vagy a címtár-azonosító vagy használja a görgetősáv tulajdonságainak megkereséséhez.

   ![Az Azure Active Directory-tulajdonságok lap](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Az ügyféloldali alkalmazás regisztrálása

A következő lépések segítségével regisztrálja az ügyfélalkalmazás.

1. A bal oldali navigációs sávján válassza **minden szolgáltatás** majd **alkalmazásregisztrációk**.
2. A **alkalmazásregisztrációk**válassza **+ új alkalmazásregisztráció**.
3. A **létrehozás**, adja meg a következő mezőket a szükséges adatokat:

   - **Név** – adjon egy rövid nevet az alkalmazáshoz. Például "SelfTestClient."
   - **Az alkalmazástípus** : Adja meg, **Web App és az API**
   - **Bejelentkezés URL-cím** – típusa "https://isvapp.azurewebsites.net/selftest-vm"

4. Kattintson a **Létrehozás** gombra.
5. A **alkalmazásregisztrációk** vagy **regisztrált alkalmazás**, másolatot a **Alkalmazásazonosító**.

   ![Az Alkalmazásazonosító beszerzése](./media/stclient-app-id.png)

6. Válassza ki a regisztrált alkalmazás eszköztár **beállítások**.
7. Válassza ki **szükséges engedélyek** az alkalmazás engedélyeit.
8. A **szükséges engedélyek**válassza **+ Hozzáadás**.
9. A **API-hozzáférés hozzáadása**, válasszon **API kiválasztása**.
10. A **API kiválasztása**, írja be a "Windows klasszikus Azure üzemi modellből", keresse meg az API-t.
11. A keresési eredmények között, válassza ki a **Windows Azure klasszikus üzemi modellben** majd **kiválasztása**.

    ![Több-bérlős alkalmazás konfigurálása](./media/stclient-select-api.png)

12. A **API-hozzáférés hozzáadása**, válasszon **engedélyek kiválasztása**.
13. Válassza ki **"Windows Azure Service Management API-t" eléréséhez**.

    ![Alkalmazás API-hozzáférés engedélyezése](./media/stclient-enable-api-access.png)

14. Kattintson a **Kiválasztás** gombra.
15. Válassza a **Done** (Kész) lehetőséget.
16. A **beállítások**válassza **tulajdonságok**.
17. A **tulajdonságok**, görgessen le a **több-bérlős**. Válassza ki **Igen**.  

    ![Több-bérlős alkalmazás konfigurálása](./media/stclient-yes-multitenant.png)

18. Kattintson a **Mentés** gombra.
19. A **beállítások**válassza **kulcsok**.
20. Hozzon létre egy titkos kulcsot a kulcs kiválasztásával **leírás** szövegmezőbe. Adja meg a következő mezőket:

    - Írja be a kulcs nevét. Ha például a "selftestclient"
    - Az a **LEJÁRAT** legördülő listában válassza a "az 1 év".
    - Válassza ki **mentése** a kulcs létrehozásához.
    - A **érték**, másolja a kulcsot.

     >[!Important]
     >Nem láthatja a kulcs értékét, miután kilépett a **kulcsok** űrlap.

    ![Kulcsérték űrlap](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>A jogkivonatot az ügyfél-alkalmazás létrehozása

A következő programok bármelyikét használhatja hozhat létre, és az OAuth-REST API-val egy token beszerzéséhez:

- Postman
- a Linux cURL
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Hozhat létre, és a Postman segítségével egy token beszerzése

 Kérje meg az Auth0 bármely az engedélyezett alkalmazások jogkivonatokat, hajtsa végre a POST műveletnek a [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) koncového bodu egy hasznos adat a következő formátumban:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
A kérelem törzsében adja át a következő paraméterekkel:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

A kérelem fejlécében adja át a következő paraméterekkel:

```
Content-Type: application/x-www-form-urlencoded
```

A következő képernyőfelvétel egy jogkivonat beszerzéséhez a postmannel példát mutat be.

![Szerezze be a tokent a postman használatával](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Hozhat létre, és a egy Linux a cURL használatával token beszerzése

Kérje meg az Auth0 bármely az engedélyezett alkalmazások jogkivonatokat, hajtsa végre a POST műveletnek a [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) koncového bodu egy hasznos adat a következő formátumban:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Az alábbi képernyőfelvétel-készítés mutat példát a curl-parancs használatával a jogkivonat beszerzéséhez.

![A curl paranccsal token beszerzése](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Hozhat létre, és a C használatával egy token beszerzése&#35;

Kérje meg az Auth0 bármely az engedélyezett alkalmazások jogkivonatokat, hajtsa végre a POST műveletnek a [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) koncového bodu egy hasznos adat a következő formátumban:

```
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

### <a name="to-create-and-get-a-token-using-powershell"></a>Hozhat létre, és a egy PowerShell-lel token beszerzése

Kérje meg az Auth0 bármely az engedélyezett alkalmazások jogkivonatokat, hajtsa végre a POST műveletnek a [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) koncového bodu egy hasznos adat a következő formátumban:

```
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
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

## <a name="pass-the-client-app-token-to-the-api"></a>Alkalmazás-jogkivonatára az ügyfél át az API-hoz

Adja át a jogkivonatot a helyi tesztelése API, az engedélyezési fejléc a következő kód használatával:

```
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

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
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>A helyi tesztelése ügyfél tesztelése

Ha tesztelni szeretné az ügyfelet, kövesse az alábbi lépéseket:

1. A vizsgálni kívánt virtuális gép üzembe helyezése.
2. Hívja meg az teszteléséhez API-t az ügyféloldali alkalmazás-jogkivonatára használ a hitelesítéshez.
3. A vizsgálati eredmények lekérése JSON formátumban.

### <a name="test-result-examples"></a>Példák a teszt eredménye

Az alábbi kódrészletek megjelenítése a vizsgálati eredmények JSON formátumban.

**Vizsgálati eredmények egy Windows virtuális gép számára:**

```
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

**Vizsgálati eredmények Linux rendszerű virtuális gép számára:**

```
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

Miután az Azure virtuális gép sikeresen tesztelését, [az ajánlat közzététele](./cpp-publish-offer.md).
