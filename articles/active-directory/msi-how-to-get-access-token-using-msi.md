---
title: "Egy Azure virtuális gép felügyelt Szolgáltatásidentitás használata a bejelentkezési és a token beszerzése"
description: "Lépésről lépésre egy Azure virtuális gép MSI kapcsolatos útmutatásért szolgáltatás egyszerű, a bejelentkezési és a hozzáférési token beszerzése."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 168b2ab3676d3f3e2830966f850e14adbe579f85
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használata a bejelentkezési és a token beszerzése 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Egy Azure virtuális gépen az MSI engedélyezését, használhatja az MSI-fájl, a bejelentkezéshez, és egy hozzáférési jogkivonatot. Ez a cikk bemutatja egy olyan MSI Csomaghoz használatának különböző módjai [egyszerű](develop/active-directory-dev-glossary.md#service-principal-object) a bejelentkezéshez, és szerezzen be egy [csak alkalmazás-hozzáférési jogkivonat](develop/active-directory-dev-glossary.md#access-token) egyéb erőforrások eléréséhez, beleértve:

- Néma/felügyelet nélküli bejelentkezés a PowerShell vagy az Azure parancssori felület
- A különböző ügyfelek, beleértve a .NET, PowerShell, Bash/CURL, REST token beszerzése
- Jelentkezzen be az Azure SDK-t, beleértve a .NET, Java, Node.js, Python, Ruby használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, a PowerShell-példák használata ebben a cikkben, ügyeljen arra, hogy telepítse [Azure PowerShell verziója 4.3.1](https://www.powershellgallery.com/packages/AzureRM) vagy nagyobb. Ha tervezi az Azure CLI példák ebben a cikkben, három lehetőség közül választhat:
- Használjon [Azure Cloud rendszerhéj](../cloud-shell/overview.md) Azure-portálról.
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, az Azure parancssori felület kódblokkok jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb), ha szeretné használni a parancssori felület helyi parancssorban. 


> [!IMPORTANT]
> - Minden minta kódot, a parancsfájl a cikkben azt feltételezi, hogy az ügyfél egy MSI-kompatibilis virtuális gépen futó. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vm.md), vagy a variant cikkekben (a PowerShell, CLI, sablon vagy egy Azure SDK használatával). 
> - Hitelesítési hibák megelőzése érdekében (403-as/AuthorizationFailed) a kódot, a parancsfájl példákban a virtuális gép identitásának hozzáférést kell biztosítani "Olvasó" engedélyezi az Azure Resource Manager műveletek a virtuális Gépen a virtuális gép hatókörben. Lásd: [egy felügyelt szolgáltatás identitásának (MSI) hozzáférés hozzárendelése az Azure portál használatával erőforrás](msi-howto-assign-access-portal.md) részleteiről.
> - A következő szakaszok egyikét a folytatás előtt a virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon való távoli csatlakozásának az MSI-kompatibilis virtuális gép.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>A PowerShell vagy a parancssori felület használatával MSI bejelentkezés

Korábban az azt jelentette, hogy a saját identitással parancsprogram futtatása:
- az Azure ad-val bizalmas vagy webes ügyfél alkalmazás regisztrálása
- az alkalmazás ügyfél-azonosító/titkos hitelesítő adataival aláírása

Az MSI-fájl a parancsfájl-ügyfél már nincs szüksége az Azure ad-val regisztrálja, és adjon meg hitelesítő adatokat. A következő példa azt használatát mutatják be a virtuális gép MSI bejelentkezésnél egyszerű.

### <a name="azure-powershell"></a>Azure PowerShell

Az alábbi parancsfájl bemutatja, hogyan:

- Szerezzen be egy MSI-jogkivonatot egy Azure virtuális gép
- a hozzáférési jogkivonat segítségével jelentkezzen be az Azure AD-alatt az megfelelő MSI egyszerű szolgáltatásnév
- az MSI egyszerű szolgáltatás használatával az Azure Resource Manager hívást, a szolgáltatás egyszerű Azonosítójának beszerzése

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

Az alábbi parancsfájl bemutatja, hogyan:

- Jelentkezzen be az Azure AD-alatt a virtuális gép MSI egyszerű szolgáltatásnév
- az MSI egyszerű szolgáltatás használatával az Azure Resource Manager hívást, a szolgáltatás egyszerű Azonosítójának beszerzése

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Az MSI-fájl olyan hozzáférési jogkivonatot beszerzésére

MSI-fájl használatával, az ügyfél alkalmazás, a parancsfájl már nem kell az Azure ad-val regisztrálja, és nem jelent-e az ügyfél-Azonosítót és titkos kulcsot egy hozzáférési jogkivonat beszerzése. Az ügyfél egyszerűen kérje meg a helyi MSI-végpont hozzáférési tokent, az alkalmazás hitelesítő adatok nélkül. Az alkalmazás csak jogkivonat a MSI egyszerű, egy tulajdonosi jogkivonatot a használhatók a kiadott [szolgáltatások közötti hívások igénylő ügyfél hitelesítő adatait](active-directory-protocols-oauth-service-to-service.md).

A következő példa megmutatjuk, hogyan használható a virtuális gép MSI jogkivonat beszerzése.

### <a name="net"></a>.NET

> [!IMPORTANT]
> Az Azure AD Authentication Library (ADAL) nincs integrálva az MSI-fájl. Egy hozzáférési jogkivonat segítségével MSI beszerzéséhez indítson egy lekérdezést a helyi MSI-végpont a virtuális gépen. További információkért lásd: [MSI – gyakori kérdések és ismert problémákat](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Az Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Mintakérelem:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-műveletet, amely azt jelzi, hogy szeretne lekérdezni a végpont adatait. Ebben az esetben az OAuth hozzáférési tokent. | 
| `http://localhost:50342/oauth2/token` | Az MSI végpont, ahol 50342 az alapértelmezett port és konfigurálható. |
| `resource` | A lekérdezési karakterlánc paraméterként, az App ID URI a célerőforrás jelző. Emellett megjelenik a `aud` (célközönség) jogcím a kiállított jogkivonat. Ebben a példában azt a kért rendelkezik egy App ID URI https://management.azure.com/ az Azure Resource Manager hozzáférési jogkivonatot. |
| `Metadata` | Egy HTTP kérelem fejlécmező, mint a kiszolgáló oldalán kérelem hamisítására (SSRF) támadások elleni megoldás MSI szükséges. Ez az érték "true", az összes kisbetű értékre kell állítani.

Mintaválasz:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elem | Leírás |
| ------- | ----------- |
| `access_token` | A kért hozzáférési jogkivonat. A REST API felület meghívásakor, a rendszer beágyazza a a jogkivonatot a `Authorization` kérelem fejlécmező a "tulajdonos" jogkivonatként, amely lehetővé teszi az API-t a hitelesítéshez a hívó. | 
| `refresh_token` | MSI-fájl nem használja. |
| `expires_in` | A hozzáférési jogkivonat továbbra is érvényes, mielőtt lejár, a kiállítási megjelenésétől másodpercek számát. Kiállítási idején a jogkivonat található `iat` jogcímek. |
| `expires_on` | A timespan, ha a hozzáférési jogkivonat lejár. A dátum jelzi a másodpercek száma "1970-01-01T0:0:0Z UTC" (a token megfelel `exp` jogcím). |
| `not_before` | A timespan, ha a hozzáférési jogkivonat lép érvénybe, és elfogadható. A dátum jelzi a másodpercek száma "1970-01-01T0:0:0Z UTC" (a token megfelel `nbf` jogcím). |
| `resource` | Az erőforrás, a hozzáférési jogkivonat szükséges, mely megfelel a `resource` lekérdezési karakterlánc a kérelem. |
| `token_type` | A jogkivonatot, amely a "Tulajdonos" hozzáférési jogkivonatot, amely azt jelenti, hogy az erőforrás hozzáférést biztosíthat a token tulajdonosi típusa. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>MSI-fájl használata az Azure SDK-könyvtárak

Azure végig több programozási platformot támogat [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek támogatásához jelentkezzen be egy olyan MSI Csomaghoz használatával frissítve lett, és adja meg a megfelelő minták használati bemutatásához. Ez a lista frissül a további támogatja:

| SDK | Minta |
| --- | ------ | 
| .NET | [Az ARM-sablont a felügyelt szolgáltatás identitás használatával Windows virtuális gép telepítése](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [Azure-szolgáltatások hívja Service identitásával felügyelt Linux virtuális gép](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [Felügyelt identitás-erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [MSI hitelesítéshez használni kívánt egyszerűen a egy virtuális Gépen belül](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Az MSI-kompatibilis virtuális erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>További információ

### <a name="token-expiration"></a>Jogkivonat lejáratáról

A helyi MSI alrendszer gyorsítótárazza a jogkivonatokat. Ezért hívása, amilyen gyakran csak van lehetősége, és az Azure AD egy a tömörített hívás eredménye csak akkor, ha:
- a gyorsítótár-tévesztései miatt nem jogkivonat a gyorsítótárban történik
- a jogkivonat érvényessége lejárt

Ha a jogkivonat gyorsítótárba helyezi a kódban, helyzetek kezelésére, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt felkészültnek kell lennie.

### <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítók az Azure-szolgáltatások

Lásd: [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](msi-overview.md#azure-services-that-support-azure-ad-authentication) MSI-fájl, és a megfelelő erőforrás-azonosítók támogató szolgáltatások listáját.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="sign-in-or-token-acquisition-fails"></a>Sikertelen bejelentkezés vagy a token beszerzése

Győződjön meg arról, hogy az MSI-fájl helytelenül engedélyezett. Lépjen vissza az Azure virtuális gép a [Azure-portálon](https://portal.azure.com) és:

- Nézze meg a "Konfiguráció" lapot, és ellenőrizze az MSI engedélyezve = "Yes".
- Nézze meg a "Kiterjesztésekkel" lapot, és ellenőrizze az MSI-bővítmény sikeresen telepítve.

Vagy nem megfelelő, ha szeretne újra telepíteni a erőforráson MSI, vagy a központi telepítési hiba elhárítása.

### <a name="http-request-error-responses"></a>HTTP-kérelem hibaválaszok

- *bad_request_102: nincs megadva a szükséges metaadat-fejléccel*

  Vagy a `Metadata` kérelem mező hiányzik a kérelemből, vagy helytelenül van formázva. Az értéket kell megadni, `true`, az összes kisbetű. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát.

- *Ismeretlen: nem sikerült beolvasni a jogkivonatot az Active Directory címtárban. További információ: a naplók \<fájl elérési útja\>*

  Győződjön meg arról, hogy a HTTP GET kérés URI formátuma megfelelő, különösen az erőforrás URI megadva a lekérdezésben. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát, vagy [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](msi-overview.md#azure-services-that-support-azure-ad-authentication) szolgáltatások és a megfelelő erőforrás-azonosítók listáját.

- *unknown_source: az ismeretlen forrásból \<URI\>*

  Győződjön meg arról, hogy a HTTP GET kérelem URI-azonosítója helytelenül van formázva. A `scheme:host/resource-path` részét kell megadni, `http://localhost:50342/oauth2/token`. A "kérelemmintát" című része a [REST szakasz megelőző](#rest) példát.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálása az Azure virtuális gép felügyelt szolgáltatás Identity (MSI) PowerShell használatával](msi-qs-configure-powershell-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

