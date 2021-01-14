---
title: Privát végpontok használata a Speech Services használatával
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a Speech Services szolgáltatást az Azure Private link által biztosított privát végpontokkal
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 61be4b45df94c902c0473b94a6dd83237c72da3c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196119"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>A Speech Services használata privát végponton keresztül

Az [Azure Private link](../../private-link/private-link-overview.md) használatával [privát végponton](../../private-link/private-endpoint-overview.md)keresztül kapcsolódhat a szolgáltatásokhoz az Azure-ban. A privát végpontok olyan magánhálózati IP-címek, amelyek csak egy adott [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) és alhálózaton belül érhetők el.

Ez a cikk bemutatja, hogyan állíthatja be és használhatja a privát és a privát végpontokat az Azure Cognitive Services Speech Services szolgáltatásával.

> [!NOTE]
> A folytatás előtt tekintse át [a virtuális hálózatok Cognitive Services használatával történő használatát ismertető témakört](../cognitive-services-virtual-networks.md).

Ez a cikk azt is ismerteti [, Hogyan távolítható el később a privát végpontok, de továbbra is használhatja a beszédfelismerési erőforrást](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Egyéni tartománynév létrehozása

A magánhálózati végpontok [Egyéni altartománynevet igényelnek a Cognitive Serviceshoz](../cognitive-services-custom-subdomains.md). Az alábbi útmutatást követve hozzon létre egyet a beszédfelismerési erőforráshoz.

> [!WARNING]
> Egy egyéni tartománynévvel rendelkező beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz más módszert használ. Előfordulhat, hogy mindkét forgatókönyv esetében módosítania kell az alkalmazás kódját: a [privát végpont engedélyezve](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) van, és [ *nem* engedélyezett a magánhálózati végpont](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Ha egyéni tartománynevet engedélyez, a művelet [nem vonható](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)vissza. Az egyetlen lehetőség, hogy visszalépjen a [regionális névre](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) egy új beszédfelismerési erőforrás létrehozásához.
>
> Ha a beszédfelismerési erőforrás sok társított egyéni modellt és projektet tartalmaz a [Speech Studio](https://speech.microsoft.com/)használatával, javasoljuk, hogy próbálja meg a konfigurációt tesztelési erőforrással kipróbálni, mielőtt módosítaná az éles környezetben használt erőforrást.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha egyéni tartománynevet szeretne létrehozni a Azure Portal használatával, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be Azure-fiókjába.
1. Válassza ki a szükséges beszédfelismerési erőforrást.
1. A bal oldali ablaktábla **Erőforrás-kezelés** csoportjában válassza a **hálózatkezelés** lehetőséget.
1. A **tűzfalak és virtuális hálózatok** lapon válassza az **Egyéni tartománynév létrehozása** lehetőséget. Megjelenik egy új jobb oldali panel, amely útmutatást ad az erőforrás egyedi egyéni altartományának létrehozásához.
1. Az **Egyéni tartománynév létrehozása** panelen adjon meg egy egyéni tartománynevet. A teljes egyéni tartomány a következőképpen fog kinézni: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Ne feledje, hogy az Egyéni tartománynév létrehozása után _nem_ módosítható.
    
    Miután megadta az egyéni tartománynevet, válassza a **Mentés** lehetőséget.
1. A művelet befejezése után az **Erőforrás-kezelés** csoportban válassza a **kulcsok és végpont** lehetőséget. Győződjön meg arról, hogy az erőforrás új végpontjának neve így kezdődik: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha egyéni tartománynevet szeretne létrehozni a PowerShell használatával, ellenőrizze, hogy a számítógépen a PowerShell 7. x vagy újabb verziója van-e a Azure PowerShell modul 5.1.0 vagy újabb verziójával. Az eszközök verzióinak megtekintéséhez kövesse az alábbi lépéseket:

1. A PowerShell-ablakban adja meg a következőket:

    `$PSVersionTable`

    Győződjön meg arról, hogy az `PSVersion` érték 7. x vagy újabb. A PowerShell frissítéséhez kövesse a [PowerShell különböző verzióinak telepítésével](/powershell/scripting/install/installing-powershell)kapcsolatos utasításokat.

1. A PowerShell-ablakban adja meg a következőket:

    `Get-Module -ListAvailable Az`

    Ha semmi sem jelenik meg, vagy ha a Azure PowerShell modul adott verziója korábbi, mint a 5.1.0, kövesse az [Azure PowerShell modul telepítése a](/powershell/azure/install-Az-ps) frissítéshez című témakör útmutatását.

A folytatás előtt futtassa a parancsot `Connect-AzAccount` Az Azure-beli kapcsolatok létrehozásához.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Ellenőrizze, hogy van-e elérhető Egyéni tartománynév

Győződjön meg arról, hogy a használni kívánt egyéni tartomány elérhető. A következő kód ellenőrzi, hogy a tartomány elérhető-e a Cognitive Services REST API a [tartomány elérhetőségének ellenőrzése](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) művelettel.

> [!TIP]
> A következő kód *nem* fog működni Azure Cloud Shellban.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Ha a kívánt név elérhető, a következőhöz hasonló választ fog látni:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Ha a név már használatban van, akkor a következő választ fogja látni:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Egyéni tartománynév létrehozása

Ha egyéni tartománynevet szeretne engedélyezni a kiválasztott beszédfelismerési erőforráshoz, használja a [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) parancsmagot.

> [!WARNING]
> A következő kód sikeres futtatása után létre kell hoznia egy egyéni tartománynevet a beszédfelismerési erőforráshoz. Ne feledje, hogy ez a név *nem* módosítható.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Ebben a szakaszban az Azure CLI legújabb verziójára van szükség. Ha Azure Cloud Shell használ, a legújabb verzió már telepítve van.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Ellenőrizze, hogy az Egyéni tartománynév elérhető-e

Győződjön meg arról, hogy a használni kívánt egyéni tartomány ingyenes. Használja a [tartomány elérhetőségének ellenõrzése](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) módszert a Cognitive Services REST API.

Másolja a következő kódrészletet, szúrja be a kívánt egyéni tartománynevet, és mentse a fájlt `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Másolja a fájlt a jelenlegi mappájába, vagy töltse fel Azure Cloud Shellba, és futtassa a következő parancsot. Cserélje le a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` értékét a saját Azure-előfizetése azonosítójára.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Ha a kívánt név elérhető, a következőhöz hasonló választ fog látni:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Ha a név már használatban van, akkor a következő választ fogja látni:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Egyéni tartománynév engedélyezése

Ha egyéni tartománynevet szeretne engedélyezni a kiválasztott beszédfelismerési erőforráshoz, használja az az [cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) parancsot.

Válassza ki a beszédfelismerési erőforrást tartalmazó Azure-előfizetést. Ha az Azure-fiókja csak egy aktív előfizetéssel rendelkezik, akkor kihagyhatja ezt a lépést. Cserélje le a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` értékét a saját Azure-előfizetése azonosítójára.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Állítsa be az egyéni tartománynevet a kiválasztott erőforrásra. Cserélje le a mintául szolgáló paramétereket a tényleges értékekre, és futtassa a következő parancsot.

> [!WARNING]
> A következő parancs sikeres végrehajtása után létre kell hoznia egy egyéni tartománynevet a beszédfelismerési erőforráshoz. Ne feledje, hogy ez a név *nem* módosítható.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Privát végpontok engedélyezése

Javasoljuk, hogy a virtuális hálózathoz csatolt [magánhálózati DNS-zónát](../../dns/private-dns-overview.md) a magánhálózati végpontokhoz szükséges frissítésekkel használja. A kiépítési folyamat során alapértelmezés szerint létre kell hoznia egy privát DNS-zónát. Ha saját DNS-kiszolgálót használ, lehetséges, hogy módosítania kell a DNS-konfigurációt is. 

Hozzon létre egy DNS-stratégiát, *mielőtt* kiépíti a saját végpontokat egy éles beszédfelismerési erőforráshoz. És tesztelje a DNS-módosításokat, különösen akkor, ha a saját DNS-kiszolgálóját használja.

Privát végpontok létrehozásához használja az alábbi cikkek egyikét. Ezek a cikkek egy webalkalmazást használnak példaként, amely lehetővé teszi a magánhálózati végpontok használatát.

- [Hozzon létre egy privát végpontot a Azure Portal használatával](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)
- [Privát végpont létrehozása az Azure CLI használatával](../../private-link/create-private-endpoint-cli.md)

Ezeket a paramétereket a kiválasztott cikkben szereplő paraméterek helyett használja:

| Beállítás             | Érték                                    |
|---------------------|------------------------------------------|
| Erőforrás típusa       | **Microsoft. CognitiveServices/fiókok** |
| Erőforrás            | **\<your-speech-resource-name>**         |
| Célzott alerőforrás | **fiókjával**                              |

**DNS privát végpontokhoz:** Tekintse át a [Cognitive Services-erőforrásokban lévő magánhálózati végpontok](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)általános alapelveit. Ezután ellenőrizze, hogy a DNS-konfiguráció megfelelően működik-e az alábbi szakaszokban ismertetett ellenőrzések elvégzésével.

### <a name="resolve-dns-from-the-virtual-network"></a>DNS feloldása a virtuális hálózatról

Ezt az ellenőrzési *követelményt kötelező megadni*.

Az alábbi lépéseket követve tesztelheti az egyéni DNS-bejegyzést a virtuális hálózatról:

1. Jelentkezzen be a virtuális hálózatban található virtuális gépre, amelyhez csatolta a privát végpontot. 
1. Nyisson meg egy Windows-parancssort vagy egy bash-rendszerhéjat, futtassa a parancsot `nslookup` , és győződjön meg róla, hogy sikeresen feloldotta az erőforrás egyéni tartománynevét.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Győződjön meg arról, hogy az IP-cím megegyezik a saját végpontjának IP-címével.

### <a name="resolve-dns-from-other-networks"></a>DNS feloldása más hálózatokból

Ezt az ellenőrzés csak akkor hajtsa végre, ha az erőforrás **hálózatkezelés** szakaszában engedélyezte a **minden hálózat** vagy a **kiválasztott hálózatok és a magánhálózati végpontok** hozzáférési lehetőséget. 

Ha azt tervezi, hogy csak privát végpont használatával szeretné elérni az erőforrást, akkor kihagyhatja ezt a szakaszt.

1. Jelentkezzen be egy olyan számítógépre, amely egy olyan hálózathoz csatlakozik, amely jogosult az erőforrás elérésére.
2. Nyisson meg egy Windows-parancssort vagy bash-rendszerhéjt, futtassa a parancsot `nslookup` , és győződjön meg róla, hogy sikeresen feloldotta az erőforrás egyéni tartománynevét.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

3. Győződjön meg arról, hogy az IP-cím megegyezik a saját végpontjának IP-címével.

> [!NOTE]
> A feloldott IP-cím egy virtuális hálózati proxy végpontra mutat, amely a Cognitive Services erőforráshoz tartozó privát végpont felé irányuló hálózati forgalmat küldi el. A viselkedés eltérő lesz egy egyéni tartománynévvel rendelkező erőforrás esetében, de privát végpontok *nélkül* . További részletekért tekintse meg [ezt a szakaszt](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Meglévő alkalmazások és megoldások módosítása

Egy egyéni tartománnyal rendelkező beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz más módszert használ. Ez egy egyéni tartományon alapuló beszédfelismerési erőforrás esetében érvényes, amely magán-végpontokkal és anélkül is használható. Az ebben a szakaszban szereplő információk mindkét forgatókönyvre érvényesek.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Egyéni tartománynévvel és egy privát végponttal rendelkező beszédfelismerési erőforrás használata

Egy egyéni tartománynévvel és egy privát végponttal rendelkező beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz eltérő módon működik. Ez a szakasz azt ismerteti, hogyan használható egy ilyen erőforrás a Speech Services REST API-kkal és a [SPEECH SDK](speech-sdk.md)-val.

> [!NOTE]
> A saját végpontok nélküli beszédfelismerési erőforrás, de az Egyéni tartománynév engedélyezve is különleges módszert nyújt a beszédfelismerési szolgáltatásokkal való interakcióra. Ez a módszer különbözik a privát végpontok által engedélyezett beszédfelismerési erőforrás forgatókönyvtől. Ha van ilyen erőforrása (például ha van egy olyan erőforrása, amely privát végpontokkal rendelkezik, de azután úgy döntött, hogy eltávolítja őket), tekintse meg a [beszédfelismerési erőforrás használata egyéni tartománynévvel és privát végpontok nélkül](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)című szakaszt.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végponttal: használat a REST API-kkal

`my-private-link-speech.cognitiveservices.azure.com`Ehhez a szakaszhoz példaként használjuk a beszédfelismerési erőforrás DNS-nevét (egyéni tartomány).

A beszédfelismerési szolgáltatások REST API-kkal rendelkeznek a [beszédfelismerés](rest-speech-to-text.md) és a [szöveg](rest-text-to-speech.md)közötti kommunikációhoz. Vegye figyelembe az alábbi információkat a privát végpontok számára engedélyezett forgatókönyvhöz.

A beszédfelismerési szöveg két REST API-val rendelkezik. Az egyes API-k eltérő célokat szolgálnak, különböző végpontokat használnak, és más megközelítést igényelnek, ha a privát végpontok használatára képes forgatókönyvben használják.

A beszéd – szöveg REST API-k a következők:
- [Beszéd – szöveg REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), amely [kötegelt átíráshoz](batch-transcription.md) és [Custom Speechhoz](custom-speech-overview.md)használható. a v 3.0 a [v 2.0 utódja](/azure/cognitive-services/speech-service/migrate-v2-to-v3)
- [Beszéd-szöveg REST API rövid hanghoz](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), amely online átíráshoz használható 

A beszéd-szöveg REST API rövid hangra való használata, valamint a privát végpont forgatókönyvben szereplő szöveg-beszéd REST API megegyeznek. Ez megegyezik a cikk későbbi részében ismertetett [SPEECH SDK-esettel](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) . 

A beszéd – szöveg REST API a v 3.0 különböző végpontokat használ, ezért eltérő megközelítést igényel a privát végpontok számára engedélyezett forgatókönyvhöz.

A következő alszakaszok mindkét esetet leírják.

##### <a name="speech-to-text-rest-api-v30"></a>Beszéd – szöveg REST API v 3.0

A beszédfelismerési erőforrások általában [Cognitive Services regionális végpontokat](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) használnak a [beszéd – szöveg REST API v 3.0-](rest-speech-to-text.md#speech-to-text-rest-api-v30)val való kommunikációhoz. Ezek az erőforrások a következő elnevezési formátummal rendelkeznek: <p/>`{region}.api.cognitive.microsoft.com`.

Ez egy példa a kérelem URL-címére:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Miután engedélyezte az egyéni tartományt egy beszédfelismerési erőforráshoz (amely a privát végpontokhoz szükséges), az adott erőforrás a következő DNS-nevet fogja használni az alapszintű REST API végponthoz: <p/>`{your custom name}.cognitiveservices.azure.com`.

Ez azt jelenti, hogy a példában a REST API végpont neve a következő lesz: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

A minta kérés URL-címét át kell alakítani a következőre:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Az URL-címnek elérhetőnek kell lennie a virtuális hálózatról a csatolt privát végponttal (a [megfelelő DNS-feloldással](#resolve-dns-from-the-virtual-network)).

Miután engedélyezte a beszédfelismerési erőforrás egyéni tartománynevét, a rendszer általában az összes kérelem URL-címére cseréli az állomásnevet az új egyéni tartományi állomásnévvel. A kérelem összes többi része (például a `/speechtotext/v3.0/transcriptions` korábbi példában található elérési út) változatlan marad.

> [!TIP]
> Egyes ügyfelek olyan alkalmazásokat fejlesztenek, amelyek a regionális végpont DNS-neve régió részét használják (például a kérés küldéséhez az adott Azure-régióban üzembe helyezett Speech-erőforráshoz).
>
> A beszédfelismerési erőforrások egyéni tartománya *nem* tartalmaz információt arról a régióról, ahol az erőforrás telepítve van. Így a korábban ismertetett alkalmazás-logika *nem* fog működni, és módosítani kell.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Beszéd-szöveg REST API rövid hang-és szöveg-beszéd REST API

A [rövid hanganyaghoz](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) és a [szöveg-beszéd Rest APIhoz](rest-text-to-speech.md) REST API két típusú végpontot használhat:
- [Cognitive Services regionális végpontok](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) , amelyek a Cognitive Services REST API való kommunikációhoz szükségesek az engedélyezési jogkivonat beszerzéséhez
- Speciális végpontok minden egyéb művelethez

A speciális végpontok részletes leírását, valamint azt, hogy az URL-cím Hogyan alakítható át egy privát végponttal rendelkező Speech-erőforrásra, a Speech SDK használatáról szóló [szakasz](#general-principles) tartalmazza. Az SDK-val kapcsolatban ismertetett alapelv vonatkozik a beszéd – szöveg REST API v 1.0 és a szöveg – beszéd REST APIra.

Ismerkedjen meg az előző bekezdésben említett alszakaszban található anyagokkal, és tekintse meg a következő példát. A példa a szöveg-beszéd REST API ismerteti. A rövid hanghoz tartozó beszéd-szöveg REST API használata teljesen egyenértékű.

> [!NOTE]
> Ha a rövid hanghoz tartozó beszéd-szöveg REST API a privát végponti forgatókönyvekben, használjon a fejlécen [átadott](rest-speech-to-text.md#request-headers) engedélyezési jogkivonatot `Authorization` [](rest-speech-to-text.md#request-headers). A beszéd előfizetési kulcsának a fejlécen keresztüli speciális végpontra való átadása `Ocp-Apim-Subscription-Key` *nem* fog működni, és a 401-es hibát állítja elő.

**Szöveg-beszéd REST API használati példa**

Nyugat-Európát példaként használjuk Azure-régióként és példaként `my-private-link-speech.cognitiveservices.azure.com` a beszédfelismerési erőforrás DNS-neveként (egyéni tartomány). A példánkban szereplő Egyéni tartománynév `my-private-link-speech.cognitiveservices.azure.com` a Nyugat-európai régióban létrehozott beszédfelismerési erőforráshoz tartozik.

A régióban támogatott hangok listájának beszerzéséhez hajtsa végre a következő két műveletet:

- Engedélyezési jogkivonat beszerzése:
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- A token használatával szerezze be a hangok listáját:
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
A [szöveg-beszéd REST API dokumentációjának](rest-text-to-speech.md)előző lépéseivel kapcsolatban lásd a további részleteket.

A magánhálózati végpontok számára engedélyezett beszédfelismerési erőforrás esetében módosítani kell az ugyanahhoz a műveleti sorozathoz tartozó végponti URL-címeket. Ugyanez a folyamat a következőhöz hasonlóan fog kinézni:

- Engedélyezési jogkivonat beszerzése:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  Tekintse meg a korábbi [beszéd – szöveg REST API v 3.0](#speech-to-text-rest-api-v30) alszakasz részletes magyarázatát.

- A beszerzett jogkivonat használatával szerezze be a hangok listáját:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  Tekintse meg a Speech SDK [általános alapelvek](#general-principles) alszakaszának részletes ismertetését.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végponttal: használat a Speech SDK-val

Ha a Speech SDK-t egyéni tartománynévvel és privát végponttal rendelkező beszédfelismerési erőforrásokkal szeretné használni, érdemes áttekintenie és valószínűleg módosítania az alkalmazás kódját.

`my-private-link-speech.cognitiveservices.azure.com`Ehhez a szakaszhoz példaként használjuk a beszédfelismerési erőforrás DNS-nevét (egyéni tartomány).

##### <a name="general-principles"></a>Általános alapelvek

Általában SDK-forgatókönyvekben (valamint a szöveg-beszéd REST API forgatókönyvekben) a beszédfelismerési erőforrások a különböző szolgáltatási ajánlatok dedikált regionális végpontját használják. A végpontok DNS-nevének formátuma a következő:

`{region}.{speech service offering}.speech.microsoft.com`

Példa a DNS-névre:

`westeurope.stt.speech.microsoft.com`

A régió összes lehetséges értéke (a DNS-név első eleme) szerepel a [beszédfelismerési szolgáltatás által támogatott régiókban](regions.md). A következő táblázat a Speech Services-ajánlat lehetséges értékeit mutatja be (a DNS-név második eleme):

| DNS-név értéke | Beszédfelismerési szolgáltatás ajánlata                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Custom Commands](custom-commands.md)                       |
| `convai`       | [Beszélgetés átirata](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [Beszéd – szöveg](speech-to-text.md)                         |
| `tts`          | [Szöveg – beszéd](text-to-speech.md)                         |
| `voice`        | [Egyéni hang](how-to-custom-voice.md)                      |

Így a korábbi példa ( `westeurope.stt.speech.microsoft.com` ) a Nyugat-Európában található beszéd-szöveg végpontot jelenti.

A privát végpontok számára engedélyezett végpontok egy speciális proxyn keresztül kommunikálnak a Speech Services szolgáltatással. Emiatt *módosítania kell a végponti kapcsolatok URL-címeit*. 

A "standard" végpont URL-címe A következőre hasonlít: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

A privát végpont URL-címe A következőre hasonlít: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**1. példa.** Egy alkalmazás a következő URL-cím használatával kommunikál (a beszédfelismerés az USA angol nyelvű alapmodelljével Nyugat-Európában):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Ha a beszédfelismerési erőforrás egyéni tartománynevét használja a magánhálózati végponttal kompatibilis forgatókönyvben `my-private-link-speech.cognitiveservices.azure.com` , a következőhöz hasonló URL-címet kell módosítania:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Figyelje meg a részleteket:

- Az állomásnév `westeurope.stt.speech.microsoft.com` helyére az egyéni tartomány állomásneve kerül `my-private-link-speech.cognitiveservices.azure.com` .
- Az eredeti DNS-név () második eleme az `stt` URL elérési útjának első eleme lesz, és megelőzi az eredeti elérési utat. Így az eredeti URL-cím `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` lesz `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**2. példa.** Egy alkalmazás a következő URL-címet használja a beszéd megszintéziséhez Nyugat-Európában egyéni hangmodell használatával:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

A következő egyenértékű URL-cím egy olyan magánhálózati végpontot használ, amelyen engedélyezve van a beszédfelismerési erőforrás egyéni tartományneve `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Az 1. példában szereplő elv ugyanezt az elvet alkalmazza, de ebben az esetben a legfontosabb elem `voice` .

##### <a name="modifying-applications"></a>Alkalmazások módosítása

A kód módosításához kövesse az alábbi lépéseket:

1. Az alkalmazás-végpont URL-címének meghatározása:

   - [Engedélyezze a naplózást az alkalmazáshoz](how-to-use-logging.md) , és futtassa azt a naplózási tevékenységhez.
   - A naplófájlban keresse meg a következőt: `SPEECH-ConnectionUrl` . Az egyező sorokban a `value` paraméter tartalmazza az alkalmazás által a Speech Services eléréséhez használt teljes URL-címet.

   Példa:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Így a példában az alkalmazás által használt URL-cím a következő:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Példány létrehozása `SpeechConfig` teljes végponti URL-cím használatával:

   1. Módosítsa az imént meghatározott végpontot a korábbi [általános alapelvek](#general-principles) című szakaszban leírtak szerint.

   1. Módosítsa, hogyan hozza létre a példányát `SpeechConfig` . Legvalószínűbb, hogy az alkalmazás a következőhöz hasonlót használ:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Ez nem fog működni egy privát végponttal rendelkező beszédfelismerési erőforrás esetében, mert az előző szakaszokban leírt állomásnév és URL-cím megváltozik. Ha a meglévő alkalmazást a privát végpontok számára engedélyezett erőforrás kulcsának módosításával próbálja meg futtatni, akkor hitelesítési hibaüzenetet kap (401).

      Annak érdekében, hogy működjön, módosítsa az osztály létrehozási módját, `SpeechConfig` és használja a "from Endpoint"/"végpont" inicializálását. Tegyük fel, hogy a következő két változót definiáljuk:
      - `subscriptionKey` a magánhálózati végpontok számára engedélyezett beszédfelismerési erőforrás kulcsát tartalmazza.
      - `endPoint` a teljes *módosított* végpont URL-címét tartalmazza (a megfelelő programozási nyelv által megkövetelt típus használatával). A példánkban a következő változónak kell szerepelnie:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Példány létrehozása `SpeechConfig` :
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> A végpont URI-azonosítójában megadott lekérdezési paraméterek nem változnak, még akkor is, ha más API-k vannak beállítva. Ha például az azonosító nyelv az URI-ban lekérdezési paraméterként van definiálva `language=en-US` , és `ru-RU` a megfelelő tulajdonságon keresztül is be van állítva, akkor az URI nyelvi beállítása is használatban van. Ezt követően az érvényes nyelv `en-US` .
>
> A végpont URI-azonosítójában beállított paraméterek mindig elsőbbséget élveznek. Más API-k csak azokat a paramétereket bírálják felül, amelyek nincsenek meghatározva a végpont URI-ban.

A módosítás után az alkalmazásnak működnie kell a privát végpontok számára engedélyezett beszédfelismerési erőforrásokkal. A privát végponti forgatókönyvek zökkenőmentes támogatásán dolgozunk.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Egyéni tartománynévvel rendelkező beszédfelismerési erőforrás használata privát végpontok nélkül

Ebben a cikkben többször is kimutatjuk, hogy a beszédfelismerési erőforrások egyéni tartományának engedélyezése *visszafordíthatatlan*. Egy ilyen erőforrás a beszédfelismerési szolgáltatásokkal való kommunikáció különböző módját fogja használni a [regionális végpontok neveit](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)használókkal szemben.

Ez a szakasz azt ismerteti, hogyan használható a beszédfelismerési erőforrás egy engedélyezett egyéni tartománynévvel, de a Speech Services REST API-kkal és a [SPEECH SDK](speech-sdk.md)-val rendelkező privát végpontok *nélkül* . Ez lehet egy olyan erőforrás, amely egy privát végponti forgatókönyvben volt használatban, de a privát végpontok törölve lettek.

#### <a name="dns-configuration"></a>DNS-konfiguráció

Ne feledje, hogyan oldható fel a [nyilvános hálózatokból](#resolve-dns-from-other-networks)az egyéni tartomány DNS-neve a privát végpontok számára engedélyezett beszédfelismerési erőforrásokhoz. Ebben az esetben a megoldott IP-cím egy virtuális hálózat proxy-végpontját mutat. Ezt a végpontot használja a rendszer a privát végpontok számára engedélyezett Cognitive Services erőforrás hálózati forgalmának elküldésére.

Ha azonban az *összes* erőforrás privát végpontja el van távolítva (vagy közvetlenül az Egyéni tartománynév engedélyezése után), a beszédfelismerési erőforrás CNAME rekordja újra lesz kiépítve. Most a megfelelő [Cognitive Services regionális végpont](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)IP-címére mutat.

Így a parancs kimenete a `nslookup` következőképpen fog kinézni:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Hasonlítsa össze a [szakasz](#resolve-dns-from-other-networks)kimenetével.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végpontok nélkül: használat a REST API-kkal

##### <a name="speech-to-text-rest-api-v30"></a>Beszéd – szöveg REST API v 3.0

A beszéd és a szöveg közötti REST API v 3.0 használata teljes mértékben egyenértékű a [saját végpontok számára engedélyezett beszédfelismerési erőforrások](#speech-to-text-rest-api-v30)esetében.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Beszéd-szöveg REST API rövid hang-és szöveg-beszéd REST API

Ebben az esetben a beszéd és a szöveg közötti REST API a rövid hangra és a szöveg-beszéd REST API használatára nem vonatkoznak eltérések az általános esettől, egyetlen kivételt jelent a rövid hangra irányuló beszéd-szöveg REST API. (Lásd a következő megjegyzést.) A rövid hang-és [szöveg-beszéd REST API](rest-text-to-speech.md) dokumentációhoz mindkét API-t használhatja a [beszéd-szöveg REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) .

> [!NOTE]
> Ha a rövid hanghoz tartozó beszéd-szöveg REST API egyéni tartománybeli forgatókönyvekben használja, használjon egy fejlécen [átadott](rest-speech-to-text.md#request-headers) engedélyezési jogkivonatot `Authorization` [](rest-speech-to-text.md#request-headers). A beszéd előfizetési kulcsának a fejlécen keresztüli speciális végpontra való átadása `Ocp-Apim-Subscription-Key` *nem* fog működni, és a 401-es hibát állítja elő.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végpontok nélkül: használat a Speech SDK-val

Ha egyéni végpontok *nélkül* kívánja használni a Speech SDK-t az egyéni tartományhoz tartozó beszédfelismerési erőforrásokkal, meg kell vizsgálnia, és valószínűleg módosítania kell az alkalmazás kódját. Vegye figyelembe, hogy ezek a változások eltérnek a [privát végpontok számára engedélyezett beszédfelismerési erőforrások](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)esetében. A privát végpontok és az egyéni tartományi forgatókönyvek zökkenőmentes támogatásán dolgozunk.

`my-private-link-speech.cognitiveservices.azure.com`Ehhez a szakaszhoz példaként használjuk a beszédfelismerési erőforrás DNS-nevét (egyéni tartomány).

A [belső végpontok használatára képes beszédfelismerési erőforrások](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)című szakaszban azt ismertetjük, hogyan határozható meg a végpont URL-címe, hogyan módosítható, és a "végpontról"/"végpont" inicializálásával végezze el a munkát `SpeechConfig` .

Ha azonban azt követően próbálja meg futtatni ugyanazt az alkalmazást, hogy az összes privát végpont el lett távolítva (ami a DNS-rekordok újbóli kiépítését is lehetővé teszi), belső szolgáltatási hibaüzenetet kap (404). Ennek az az oka, hogy a [DNS-rekord](#dns-configuration) most a regionális Cognitive Services végpontra mutat a virtuális hálózati proxy helyett, és az URL-elérési utak, például `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` nem találhatók.

Ha az alkalmazást az alábbi kód stílusa alapján állítja vissza az alkalmazásból `SpeechConfig` , az alkalmazás a hitelesítési hibával leáll (401):

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>Alkalmazások módosítása

Az alábbi lépéseket követve engedélyezheti, hogy az alkalmazás egy egyéni tartománynévvel és privát végpontok nélkül használja a beszédfelismerési erőforrásokat:

1. Igényeljen engedélyezési jogkivonatot a Cognitive Services REST API. [Ez a cikk](../authentication.md#authenticate-with-an-authentication-token) bemutatja, hogyan kérheti le a tokent.

   Használja az egyéni tartománynevet a végpont URL-címében. A példánkban ez az URL-cím a következő:
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > Ez az URL-cím a Azure Portalban található. A beszédfelismerési erőforrás lapon az erőforrás- **kezelés** csoportban válassza a **kulcsok és végpont** lehetőséget.

1. Hozzon létre egy `SpeechConfig` példányt az előző szakaszban beszerzett engedélyezési jogkivonat használatával. Tegyük fel, hogy a következő változók vannak definiálva:

   - `token`: az előző szakaszban beszerzett engedélyezési jogkivonat
   - `azureRegion`: a Speech erőforrás- [régió](regions.md) neve (példa: `westeurope` )
   - `outError`: (csak a [Objective C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) esetében)

   Hozzon létre egy `SpeechConfig` példányt a következőhöz hasonló módon:

   ```csharp
   var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
   ```
   ```cpp
   auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
   ```
   ```java
   SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
   ```
   ```python
   import azure.cognitiveservices.speech as speechsdk
   speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
   ```
   ```objectivec
   SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
   ```
> [!NOTE]
> A hívónak biztosítania kell, hogy az engedélyezési jogkivonat érvényes legyen. Az engedélyezési jogkivonat lejárta előtt a hívónak frissítenie kell azt egy új érvényes jogkivonat meghívásával. Mivel a konfigurációs értékek másolása új felismerő vagy szintetizátor létrehozásakor történik, az új jogkivonat értéke nem vonatkozik a már létrehozott felismerők vagy szintetizátorokra.
>
> Ezeknél állítsa be a megfelelő felismerő vagy szintetizátor engedélyezési jogkivonatát a jogkivonat frissítéséhez. Ha nem frissíti a jogkivonatot, a felismerő vagy a szintetizátor hibákba ütközik a működés közben.

A módosítás után az alkalmazásnak olyan beszédfelismerési erőforrásokkal kell működnie, amelyek egyéni tartománynevet használnak privát végpontok nélkül.

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Tudjon meg többet

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Beszéd SDK](speech-sdk.md)
* [Beszéd – szöveg REST API](rest-speech-to-text.md)
* [Szöveg – beszéd REST API](rest-text-to-speech.md)
