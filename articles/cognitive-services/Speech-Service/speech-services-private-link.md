---
title: A Speech Services használata privát végpontokkal
titleSuffix: Azure Cognitive Services
description: Útmutatók a Speech Services használatáról az Azure Private link által biztosított privát végpontokkal
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: c88a7820518d0a73bfb0e93d3b364190207b8f90
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051222"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>A Speech Services használata az Azure Private link által biztosított privát végpontokkal

Az [Azure Private link](../../private-link/private-link-overview.md) segítségével [privát végponton](../../private-link/private-endpoint-overview.md)keresztül kapcsolódhat az Azure-beli különböző Pásti-szolgáltatásokhoz. A privát végpont egy magánhálózati IP-cím egy adott [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) és alhálózaton belül.

Ez a cikk bemutatja, hogyan állíthat be és használhat privát és privát végpontokat az Azure kognitív Speech Services használatával. 

> [!NOTE]
> Ez a cikk ismerteti az Azure kognitív Speech Services szolgáltatással való privát kapcsolat beállításának és használatának sajátosságait. Mielőtt továbblépne, ismerkedjen meg a [virtuális hálózatok Cognitive Services használatával történő használatáról](../cognitive-services-virtual-networks.md)szóló általános cikkel.

A titkos végponti forgatókönyvek beszédfelismerési erőforrásának engedélyezéséhez a következő feladatokat kell végrehajtani:
- [Beszédfelismerési erőforrás egyéni tartománynevének létrehozása](#create-custom-domain-name)
- [Privát végpont (ok) létrehozása és konfigurálása](#enabling-private-endpoints)
- [Meglévő alkalmazások és megoldások módosítása](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Ha később úgy dönt, hogy eltávolítja az összes privát végpontot, de továbbra is használja az erőforrást, a [jelen szakasz](#using-speech-resource-with-custom-domain-name-without-private-endpoints)ismerteti a szükséges műveleteket.

## <a name="create-custom-domain-name"></a>Egyéni tartománynév létrehozása

A privát végpontoknak [Cognitive Services egyéni altartománynevek](../cognitive-services-custom-subdomains.md)használatát igénylik. Az alábbi utasítások segítségével hozzon létre egyet a beszédfelismerési erőforráshoz.

> [!WARNING]
> Egy egyéni tartománynévvel rendelkező beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz más módszert használ. Legvalószínűbb, hogy az alkalmazás kódját is módosítania kell a [privát végpontok](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) számára, és [ **nem** engedélyezett a magánhálózati végpontok engedélyezése](#using-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Az Egyéni tartománynév engedélyezésének művelete [**nem vonható**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)vissza. Az egyetlen lehetőség, hogy visszalépjen a [regionális névre](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) egy új beszédfelismerési erőforrás létrehozásához. 
>
> Különösen azokban az esetekben, amikor a beszédfelismerési erőforrás sok társított egyéni modellt és projektet hozott létre a [Speech Studio](https://speech.microsoft.com/) használatával, **nyomatékosan** javasoljuk a konfiguráció tesztelési erőforrással történő kipróbálását, és csak ezután módosítja az éles környezetben használt beállításokat.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

- Lépjen [Azure Portal](https://portal.azure.com/) , és jelentkezzen be az Azure-fiókjába
- A szükséges beszédfelismerési erőforrás kiválasztása
- *Hálózat* kiválasztása (*erőforrás-felügyeleti* csoport) 
- A *tűzfalak és virtuális hálózatok* lapon (alapértelmezett) kattintson az **Egyéni tartománynév létrehozása** gombra.
- Egy új panel jelenik meg, amely az erőforrás egyedi egyéni altartományának létrehozásához nyújt útmutatást.
> [!WARNING]
> Miután létrehozott egy egyéni tartománynevet, **nem** módosítható. A fenti figyelmeztetésben további információkat talál.
- A művelet befejezése után érdemes kiválasztani a *kulcsok és a végpont* (*erőforrás-felügyeleti* csoport) beállítást, és az erőforrás új végpontjának nevét a következő formátumban kell ellenőrizni: `{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ebben a szakaszban a PowerShell 7. x vagy újabb verziójára van szükség a Azure PowerShell modul 5.1.0 vagy újabb verziójával. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps) című témakört.

Mielőtt folytatná a további futtatást `Connect-AzAccount` , hozzon létre egy-egy kapcsolódást az Azure-ban.

## <a name="verify-custom-domain-name-availability"></a>Egyéni tartománynév rendelkezésre állásának ellenőrzése

Ellenőriznie kell, hogy a használni kívánt egyéni tartomány ingyenes-e. A [tartomány elérhetőségének ellenőrzését](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) a Cognitive Services REST API fogjuk használni. A lépéseket a következő kódrészletben találja: megjegyzések.

> [!TIP]
> Az alábbi kód **nem** fog működni Azure Cloud Shellban.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
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
Ha a kívánt név elérhető, akkor a következőhöz hasonló választ kap:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Ha a név már használatban van, akkor a következő választ fogja kapni:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>Egyéni tartománynév engedélyezése

A kiválasztott beszédfelismerési erőforrás egyéni tartománynevének engedélyezéséhez használja a [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) parancsmagot. A lépéseket a következő kódrészletben találja: megjegyzések.

> [!WARNING]
> Az alábbi kód sikeres végrehajtása után létre kell hoznia egy egyéni tartománynevet a beszédfelismerési erőforráshoz. Ez a név **nem** módosítható. A fenti figyelmeztetésben további információkat talál.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ebben a szakaszban az Azure CLI legújabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="verify-custom-domain-name-availability"></a>Egyéni tartománynév rendelkezésre állásának ellenőrzése

Ellenőriznie kell, hogy a használni kívánt egyéni tartomány ingyenes-e. A [tartomány elérhetőségének ellenőrzését](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) a Cognitive Services REST API fogjuk használni. 

Másolja az alábbi kódrészletet, szúrja be az egyéni tartománynevet, és mentse a fájlt `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Másolja a fájlt a jelenlegi mappájába, vagy töltse fel Azure Cloud Shellre, és hajtsa végre a következő parancsot. (Cserélje le az `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Azure-előfizetés azonosítóját).

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Ha a kívánt név elérhető, akkor a következőhöz hasonló választ kap:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Ha a név már használatban van, akkor a következő választ fogja kapni:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>Egyéni tartománynév engedélyezése

Ha engedélyezni szeretné az egyéni tartománynevet a kiválasztott beszédfelismerési erőforráshoz, az [az cognitiveservices Account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) parancsot használjuk.

Válassza ki a beszédfelismerési erőforrást tartalmazó Azure-előfizetést. Ha az Azure-fiókja csak egy aktív előfizetéssel rendelkezik, akkor kihagyhatja ezt a lépést. (Cserélje le az `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Azure-előfizetés azonosítóját).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Állítsa be az egyéni tartománynevet a kiválasztott erőforrásra. Cserélje le a minták paramétereinek értékét a tényleges értékekre, és hajtsa végre az alábbi parancsot.
> [!WARNING]
> Az alábbi parancs sikeres végrehajtása után létre kell hoznia egy egyéni tartománynevet a beszédfelismerési erőforráshoz. Ez a név **nem** módosítható. A fenti figyelmeztetésben további információkat talál.
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>Privát végpontok engedélyezése

Privát végpont engedélyezése Azure Portal, Azure PowerShell vagy Azure CLI használatával.

Javasoljuk, hogy használja a Virtual Networkhoz csatolt [magánhálózati DNS-zónát](../../dns/private-dns-overview.md) a privát végpontokhoz szükséges frissítésekkel, amelyeket a kiépítési folyamat során alapértelmezés szerint hozunk létre. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban. Lásd: [DNS privát végpontok számára](#dns-for-private-endpoints) szakasz. A legjobb megoldás az, ha a DNS-stratégiával kapcsolatban a privát végpont (ok) kiépítési Beszédi erőforráshoz való kihelyezése *előtt* dönt. Javasoljuk továbbá az előzetes tesztelést is, különösen akkor, ha a saját DNS-kiszolgálóját használja.

A következő cikkek használatával hozhat létre privát végpontokat. A cikkek egy webalkalmazást használnak egy példaként szolgáló erőforrásként, amely lehetővé teszi a magánhálózati végpontok használatát. Ehelyett használja a következő paramétereket:

| Beállítás             | Érték                                    |
|---------------------|------------------------------------------|
| Erőforrás típusa       | **Microsoft. CognitiveServices/fiókok** |
| Erőforrás            | **\<your-speech-resource-name>**         |
| Célzott alerőforrás | **fiókjával**                              |

- [Privát végpont létrehozása az Azure Portal segítségével](../../private-link/create-private-endpoint-portal.md)
- [Privát végpont létrehozása Azure PowerShell használatával](../../private-link/create-private-endpoint-powershell.md)
- [Privát végpont létrehozása az Azure CLI-vel](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>DNS privát végpontokhoz

Ismerkedjen meg a DNS általános alapelveivel a [Cognitive Services erőforrásaiban található magánhálózati végpontokhoz](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Ezután ellenőrizze, hogy a DNS-konfiguráció megfelelően működik-e (lásd a következő alszakaszokat).

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(Kötelező ellenőrzési). A Virtual Network DNS-feloldása

`my-private-link-speech.cognitiveservices.azure.com`Ennek a szakasznak a példában a Speech Resource DNS-nevet fogjuk használni.

Jelentkezzen be a virtuális hálózatban található virtuális gépre, amelyhez csatolta a privát végpontot. Nyissa meg a Windows parancssort vagy a bash shellt, hajtsa végre az "nslookup" parancsot, és győződjön meg róla, hogy sikeresen feloldja az erőforrás egyéni tartománynevét:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Győződjön meg arról, hogy az IP-cím megoldódott a privát végpont címével.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(Nem kötelező ellenőrzési lehetőség). DNS-feloldás más hálózatokból

Ez az ellenőrzési művelet akkor szükséges, ha a privát végponton engedélyezve lévő beszédfelismerési erőforrást "hibrid" módban szeretné használni, azaz az *összes hálózatot* , illetve a *kiválasztott hálózatokat és a magánhálózati végpontok* hozzáférési lehetőséget az erőforrás *hálózatkezelés* szakaszában. Ha úgy tervezi, hogy csak privát végpont használatával szeretné elérni az erőforrást, kihagyhatja ezt a szakaszt.

`my-private-link-speech.cognitiveservices.azure.com`Ennek a szakasznak a példában a Speech Resource DNS-nevet fogjuk használni.

A hálózathoz csatlakoztatott bármely olyan gépen, amelyről engedélyezi az erőforráshoz való hozzáférést, nyissa meg az "nslookup" parancsot, és győződjön meg arról, hogy sikeresen feloldja az erőforrás egyéni tartománynevét:
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

Vegye figyelembe, hogy az IP-cím megoldva egy VNet-proxy végpontra mutat, amely a privát végpont számára engedélyezett Cognitive Services erőforrással való hálózati forgalom elküldésére szolgál. Ez a viselkedés az egyéni tartománynévvel rendelkező erőforrások esetében eltérő lesz, de *nincs* konfigurálva saját végpont. Tekintse meg [ezt a szakaszt](#dns-configuration).

## <a name="adjusting-existing-applications-and-solutions"></a>Meglévő alkalmazások és megoldások módosítása 

Egy egyéni tartománnyal rendelkező beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz más módszert használ. Ez igaz az egyéni tartományon keresztül engedélyezett beszédfelismerési erőforrásokra [, valamint](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) privát végpontok [nélkül és anélkül](#using-speech-resource-with-custom-domain-name-without-private-endpoints) is. Az aktuális szakasz mindkét esetben tartalmazza a szükséges információkat.

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Egyéni tartománynévvel és privát végponttal rendelkező beszédfelismerési erőforrás használata

Egy egyéni tartománynevet és privát végpontot használó beszédfelismerési erőforrás a beszédfelismerési szolgáltatásokkal való kommunikációhoz más módszert használ. Ez a szakasz azt ismerteti, hogyan használható az adott erőforrás a Speech Services REST API és a [SPEECH SDK](speech-sdk.md)használatával.

> [!NOTE]
> Vegye figyelembe, hogy a saját végpontok nélküli beszédfelismerési erőforrás, de az **Egyéni tartománynév** engedélyezve is speciális módszert nyújt a beszédfelismerési szolgáltatásokkal való interakcióra, de ez a módszer eltér a privát végpontok által engedélyezett beszédfelismerési erőforrás forgatókönyvének. Ha van ilyen erőforrása (tegyük fel, hogy rendelkezik egy privát végpontokkal rendelkező erőforrással, de úgy döntött, hogy eltávolítja őket), győződjön meg arról, hogy ismeri a [levelező szakaszt](#using-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végponttal. Használat REST API

`my-private-link-speech.cognitiveservices.azure.com`Ebben a szakaszban a példaként használt Speech Resource DNS-nevet (egyéni tartományt) fogjuk használni.

##### <a name="note-on-speech-services-rest-api"></a>Megjegyzés a Speech Services REST API

A Speech Services REST API a [beszéd-szöveg](rest-speech-to-text.md) és a [szöveg – beszéd kommunikációhoz](rest-text-to-speech.md). Az alábbiakat kell figyelembe venni a privát végpontok számára engedélyezett forgatókönyv esetében.

A beszéd-szöveg a két különböző REST API-val rendelkezik. Az egyes API-k eltérő célokat szolgálnak, különböző végpontokat használnak, és más megközelítést igényelnek, amikor a privát végpontok számára engedélyezett forgatókönyvben használják a Singet.

A beszéd – szöveg REST API-k a következők:
- a [v 1.0](rest-speech-to-text.md) használatban van az online átíráshoz
- a v 3.0 használatos a [Batch átírásához](batch-transcription.md) és [Custom Speech](custom-speech-overview.md). (Lásd a [teljes referenciát](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0))

A beszéd-szöveg 1.0-s és a szöveg-beszéd REST API használata a privát végpont forgatókönyvben a cikk későbbi részében ismertetett [SPEECH SDK-esettel](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) azonos és azzal egyenértékű. 

A beszéd – szöveg REST API v 3.0 különböző végpontokat használ, így a privát végpontok számára engedélyezett forgatókönyvhöz eltérő megközelítést kell alkalmazni.

Mindkét esetet a következő alszakaszokban ismertetjük.


##### <a name="speech-to-text-rest-api-v30"></a>Beszéd – szöveg REST API v 3.0

A beszédfelismerési erőforrások általában [Cognitive Services regionális végpontokat](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) használnak a [beszéd – szöveg REST API v 3.0-](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)val való kommunikációhoz. Ezek az erőforrások a következő elnevezési formátummal rendelkeznek: <p/>`{region}.api.cognitive.microsoft.com`

Ez egy példa a kérelem URL-címére:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Miután engedélyezte az egyéni tartományt egy beszédfelismerési erőforráshoz (amely a privát végpontokhoz szükséges), az adott erőforrás a következő DNS-nevet fogja használni az alapszintű REST API végponthoz: <p/>`{your custom name}.cognitiveservices.azure.com`

Ez azt jelenti, hogy a példában a REST API végpont neve a következő lesz: <p/>`my-private-link-speech.cognitiveservices.azure.com`

A fenti minta-kérelem URL-címét át kell alakítani a következőre:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Ennek az URL-címnek elérhetőnek kell lennie az Virtual Network a csatolt privát végponttal (a [megfelelő DNS-feloldással](#mandatory-check-dns-resolution-from-the-virtual-network)).

Így általában a beszédfelismerési erőforrások egyéni tartománynevének engedélyezése után kell lecserélni az állomásnévt az új egyéni tartománynévvel rendelkező összes kérelem URL-címére. A kérelem összes többi része (például a `/speechtotext/v3.0/transcriptions` fenti példában szereplő útvonal) változatlan marad.

> [!TIP]
> Néhány ügyfél olyan alkalmazásokat fejlesztett ki, amelyek a területi végpont DNS-neve régió részét használják (például a kérés küldéséhez az adott Azure-régióban üzembe helyezett Speech-erőforráshoz).
>
> A beszédfelismerési erőforrás egyéni tartományneve **nem** tartalmaz információt arról a régióról, ahol az erőforrás telepítve van. Így a fent ismertetett alkalmazás-logika **nem** fog működni, és módosítani kell.

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>Beszéd – szöveg REST API 1.0-s és szöveg – beszéd REST API

A [beszédfelismerési REST API 1.0](rest-speech-to-text.md) -s és [szöveg-beszéd REST API](rest-text-to-speech.md) két típusú végpontot használhat:
- [Cognitive Services regionális végpontok](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) , amelyek a Cognitive Services REST API való kommunikációhoz szükségesek az engedélyezési jogkivonat beszerzéséhez
- Speciális végpontok minden egyéb művelethez

A speciális végpontok részletes leírását, valamint azt, hogy az URL-cím Hogyan alakítható át egy privát végponton engedélyezett beszédfelismerési erőforráshoz [, a "](#general-principle) használat a Speech SDK-val" című szakaszban olvasható. Az SDK-val kapcsolatban ismertetett alapelv vonatkozik a beszéd – szöveg 1.0-s verzióra és a szövegről beszédre REST API.

Ismerkedjen meg az előző bekezdésben említett alszakaszban található anyagokkal, és tekintse meg a következő példát. (A példa szöveg-beszéd REST APIt ismertet; a beszédfelismerés és a szöveg közötti 1.0-s REST API használata teljes mértékben egyenértékű)

**Szöveg-beszéd REST API használati példa.**

A Nyugat-európai minta Azure-régióként és `my-private-link-speech.cognitiveservices.azure.com` a beszédfelismerési erőforrás DNS-neve (egyéni tartomány) használatával fogunk használni. A `my-private-link-speech.cognitiveservices.azure.com` példánkban szereplő Egyéni tartománynév a Nyugat-európai régióban létrehozott beszédfelismerési erőforráshoz tartozik.

A régióban támogatott hangok listájának lekéréséhez a következő két műveletet kell végrehajtania:

- Engedélyezési jogkivonat beszerzése a használatával
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- A beszerzett jogkivonat használata a Voices listájának lekérése
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(További részletek a [szöveg és a beszéd REST API dokumentációjában](rest-text-to-speech.md)található lépéseknél)

Ahhoz, hogy a titkos végponton engedélyezve legyen a beszédfelismerési erőforrás, módosítani kell a végpont URL-címeit ugyanahhoz a műveleti sorszámhoz. Ugyanez a folyamat a következőhöz hasonlóan fog kinézni:
- Engedélyezési jogkivonat beszerzése a használatával
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(lásd a fenti, [beszéd – szöveg REST API v 3.0](#speech-to-text-rest-api-v30) alszakaszának részletes magyarázatát)
- A beszerzett jogkivonat használata a Voices listájának lekérése
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(lásd az alábbi részletes magyarázatot: "a Speech SDK használata" című szakasz [általános elv](#general-principle) alszakasza)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Beszédfelismerési erőforrás egyéni tartománynévvel és privát végponttal. Használat a Speech SDK-val

A Speech SDK és az Egyéni tartománynév, valamint a privát végpontok által engedélyezett beszédfelismerési erőforrásokkal az alkalmazás kódjának felülvizsgálata és valószínű módosítása szükséges. A privát végponti forgatókönyvek zökkenőmentes támogatásán dolgozunk.

`my-private-link-speech.cognitiveservices.azure.com`Ebben a szakaszban a példaként használt Speech Resource DNS-nevet (egyéni tartományt) fogjuk használni.

##### <a name="general-principle"></a>Általános elv

Általában SDK-forgatókönyvekben (és a szöveg-beszéd REST API forgatókönyvekben) a beszédfelismerési erőforrások a különböző szolgáltatási ajánlatok speciális regionális végpontját használják. A végpontok DNS-nevének formátuma a következő: </p>`{region}.{speech service offering}.speech.microsoft.com`

Példa: </p>`westeurope.stt.speech.microsoft.com`

A régió összes lehetséges értéke (a DNS-név első eleme) [itt](regions.md) látható az alábbi táblázat a Speech Services-ajánlat lehetséges értékét mutatja (a DNS-név második eleme):

| DNS-név értéke | Speech Services-ajánlat                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Custom Commands](custom-commands.md)                       |
| `convai`       | [Beszélgetés átirata](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [Beszéd – szöveg](speech-to-text.md)                         |
| `tts`          | [Szöveg – beszéd](text-to-speech.md)                         |
| `voice`        | [Egyéni hang](how-to-custom-voice.md)                      |

Így a fenti példa ( `westeurope.stt.speech.microsoft.com` ) a Nyugat-Európában a beszéd-szöveg végpontot jelenti.

A privát végpontok számára engedélyezett végpontok egy speciális proxyn keresztül kommunikálnak a Speech Services szolgáltatással, és **a végponti kapcsolatok URL-címeinek módosítására van szükség**. A rendszer a következő elvet alkalmazza: a "standard" végpont URL-címe az alábbi mintát követi: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Akkor az alábbi módon kell módosítani: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**1. példa.** Az alkalmazás a következő URL-cím használatával kommunikál (a beszédfelismerést az USA angol nyelvű alapmodelljével Nyugat-Európában): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Ha azt szeretné használni a privát végpontok számára engedélyezett forgatókönyvben, ha a beszédfelismerési erőforrás egyéni tartományneve `my-private-link-speech.cognitiveservices.azure.com` ezt az URL-címet kell módosítania:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Nézzük meg közelebbről a következőt:
- `westeurope.stt.speech.microsoft.com`Az állomásnév helyébe az egyéni tartomány állomásneve kerül.`my-private-link-speech.cognitiveservices.azure.com`
- Az eredeti DNS-név () második eleme az `stt` URL elérési útjának első eleme lesz, és megelőzi az eredeti elérési utat, amely az eredeti URL-cím `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` lesz `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**2. példa.** Az alkalmazás a következő URL-cím használatával kommunikál (a hangszintézis a Nyugat-európai egyéni hangmodell használatával): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Ha azt szeretné használni a privát végpontok számára engedélyezett forgatókönyvben, ha a beszédfelismerési erőforrás egyéni tartományneve `my-private-link-speech.cognitiveservices.azure.com` ezt az URL-címet kell módosítania: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Az 1. példával megegyező elv van alkalmazva, de ebben az esetben a legfontosabb elem `voice` .

##### <a name="modifying-applications"></a>Alkalmazások módosítása

Ha alkalmazni szeretné az előző szakaszban leírt elvet az alkalmazás kódjában, két fő dolgot kell tennie:

- Az alkalmazás által használt végpont URL-címének meghatározása
- Módosítsa a végponti URL-címet az előző szakaszban leírtak szerint, és hozza létre az `SpeechConfig` osztály példányát, explicit módon a módosított URL-cím használatával.

###### <a name="determining-application-endpoint-url"></a>Alkalmazás-végpont URL-címének meghatározása

- Az [alkalmazás naplózásának engedélyezése](how-to-use-logging.md) és futtatása a napló létrehozásához
- Keresse meg a fájlt a naplófájlban `SPEECH-ConnectionUrl` . A karakterlánc tartalmaz egy `value` paramétert, amely viszont az alkalmazás által használt teljes URL-címet fogja tartalmazni.

Példa egy naplófájl-sorra a végpont URL-címével:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
Így az alkalmazás által a példában használt URL-cím a következő:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>`SpeechConfig`Példány létrehozása a teljes végpont URL-címével

Módosítsa az előző szakaszban meghatározott végpontot a fenti [általános elv](#general-principle) szerint.

Most módosítania kell a példányának létrehozását `SpeechConfig` . A mai alkalmazása valószínűleg a következőhöz hasonló:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Ez nem fog működni a titkos végponton engedélyezve lévő beszédfelismerési erőforráshoz az előző szakaszokban ismertetett állomásnév és URL-változások miatt. Ha a privát végpontot engedélyező erőforrás kulcsának módosítása nélkül próbálja meg futtatni a meglévő alkalmazást, akkor hitelesítési hibát (401) kap.

Ahhoz, hogy működjön, módosítania kell, hogyan hozza létre `SpeechConfig` az osztályt, és használja a "végpont"/"végpont" inicializálását. Tegyük fel, hogy a következő két változót definiáljuk:
- `subscriptionKey` a privát végpont által engedélyezett beszédfelismerési erőforrás kulcsát tartalmazza
- `endPoint` a teljes **módosított** végpont URL-címének (a levelező programozási nyelv által megkövetelt típusnak a használatával) tartalmazza. A példánkban a változónak tartalmaznia kell
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Ezután a `SpeechConfig` következőhöz hasonló osztályt kell létrehoznia:
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
> A végpont URI azonosítójában megadott lekérdezési paraméterek nem változnak, még akkor sem, ha más API-kkal vannak beállítva. Ha például az azonosító nyelv az URI-ban a "Language = en-US" lekérdezési paraméterként van definiálva, és az "ru-RU" értékre van állítva a levelező tulajdonságon keresztül, az URI nyelvi beállítása elsőbbséget élvez, és a tényleges nyelv az "en-US". A más API-k csak azokat a paramétereket állíthatják be, amelyek nincsenek megadva a végpont URI-ban.

A módosítást követően az alkalmazásnak működnie kell a privát, engedélyezett beszédfelismerési erőforrásokkal. A privát végponti forgatókönyvek zökkenőmentes támogatásán dolgozunk.

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Egyéni tartománynév nélküli beszédfelismerési erőforrás használata privát végpontok nélkül

Ebben a cikkben többször is rámutatunk arra, hogy a beszédfelismerési erőforrások egyéni tartományának engedélyezése **visszafordíthatatlan** , és az ilyen erőforrás a beszédfelismerési szolgáltatásokkal való kommunikáció egy másik módját fogja használni a "szokásos" (azaz a [regionális végpontok nevét](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)használó) kifejezésekkel összehasonlítva.

Ez a szakasz azt ismerteti, hogyan használható a beszédfelismerési erőforrás az engedélyezett egyéni tartománynévvel, de a Speech Services REST API és a [SPEECH SDK](speech-sdk.md)-val rendelkező privát végpontok **nélkül** . Ez lehet egy olyan erőforrás, amely egy privát végponti forgatókönyvben volt használatban, de a privát végpont (ok) törölve lett.

#### <a name="dns-configuration"></a>DNS-konfiguráció

Ne feledje, hogy a magánhálózati végpontok által támogatott beszédfelismerési erőforrások egyéni tartomány DNS-neve Hogyan oldható fel a [nyilvános hálózatokból](#optional-check-dns-resolution-from-other-networks). Ebben az esetben az IP-cím megoldva egy VNet-proxy végpontra mutat, amely a privát végpont számára engedélyezett Cognitive Services erőforrással való hálózati forgalom elküldésére szolgál.

Ha azonban az **összes** erőforrás privát végpontja el van távolítva (vagy közvetlenül az Egyéni tartománynév engedélyezése után), a rendszer újraépíti a beszédfelismerési erőforrás CNAME rekordját, és most a kapcsolattartó [Cognitive Services regionális végpont](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)IP-címére mutat.

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
Hasonlítsa össze a [szakasz](#optional-check-dns-resolution-from-other-networks)kimenetével.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Egyéni tartománynevet tartalmazó beszédfelismerési erőforrás magánhálózati végpontok nélkül. Használat REST API

##### <a name="speech-to-text-rest-api-v30"></a>Beszéd – szöveg REST API v 3.0

A beszéd-szöveg REST API v 3.0 használata teljesen egyenértékű a [privát végpontok által engedélyezett beszédfelismerési erőforrások](#speech-to-text-rest-api-v30)esetében.

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>Beszéd – szöveg REST API 1.0-s és szöveg – beszéd REST API

Ebben az esetben a beszédfelismerés és a szöveg közötti REST API 1.0-s és szöveg-beszéd REST API használata nem tartalmaz eltéréseket az általános esetnek, és a [beszéd – szöveg REST API](rest-speech-to-text.md) 1.0-s és [szöveg-beszéd REST API](rest-text-to-speech.md) dokumentációjában leírt módon kell használni.


#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Egyéni tartománynevet tartalmazó beszédfelismerési erőforrás magánhálózati végpontok nélkül. Használat a Speech SDK-val

Ha egyéni tartománynevet használó Speech SDK-t használ, a saját végpontok **nélküli** beszédfelismerési erőforrásokhoz az alkalmazás kódjának felülvizsgálata és valószínű módosítása szükséges. Vegye figyelembe, hogy ezek a változások **eltérnek** a [privát végpontok által engedélyezett beszédfelismerési erőforrások](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)esetében. A privát végpontok/egyéni tartományok zökkenőmentes támogatásán dolgozunk.

`my-private-link-speech.cognitiveservices.azure.com`Ebben a szakaszban a példaként használt Speech Resource DNS-nevet (egyéni tartományt) fogjuk használni.

A [privát végpontok által engedélyezett beszédfelismerési erőforrás](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) szakasza azt ismerteti, hogyan határozható meg a végponti URL-cím, hogyan módosítható, és a "végpontról"/"végpont" inicializálásával végezze el a munkát `SpeechConfig` .

Ha azonban azt követően próbálja meg futtatni ugyanazt az alkalmazást, hogy az összes privát végpont el lett távolítva (ami egy ideig a levelező DNS-rekord újbóli kiépítésének engedélyezése), akkor a belső szolgáltatási hiba (404) fog megjelenni. Ennek az az oka, hogy a [DNS-rekord](#dns-configuration) most a VNet proxy helyett a regionális Cognitive Services végpontra mutat, és az URL-elérési utak `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` , például nem találhatók ott, ezért a "nem található" hibaüzenet jelenik meg (404).

Ha a "normál" példányra állítja vissza az alkalmazást a következő `SpeechConfig` stílusban:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
az alkalmazás a hitelesítési hibával leáll (401).

##### <a name="modifying-applications"></a>Alkalmazások módosítása

Ha engedélyezni szeretné az alkalmazás számára, hogy a beszédfelismerési erőforráshoz saját végpontok nélkül egyéni tartománynevet adjon, a következőket kell tennie:
- Engedélyezési jogkivonat kérése Cognitive Services REST API használatával
- Osztály példányának a `SpeechConfig` "from engedélyezési jogkivonat"/"engedélyezési jogkivonat használatával" metódusának példányai 

###### <a name="requesting-authorization-token"></a>Engedélyezési jogkivonat kérése

[Ebből a cikkből](../authentication.md#authenticate-with-an-authentication-token) megtudhatja, hogyan kérheti le a tokent a Cognitive Services REST API használatával. 

Használja az egyéni tartománynevet a végpont URL-címében, amely a példánkban az alábbi URL-cím:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Ezt az URL-címet a beszédfelismerési erőforrás *kulcsok és végpont* (*erőforrás-kezelési* csoport) szakaszában találja Azure Portal.

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>`SpeechConfig`Példány létrehozása engedélyezési jogkivonat használatával

Az `SpeechConfig` osztályt az előző szakaszban beszerzett engedélyezési token használatával kell létrehoznia. Tegyük fel, hogy a következő változók vannak definiálva:

- `token` az előző szakaszban beszerzett engedélyezési tokent tartalmazza
- `azureRegion` a beszédfelismerési erőforrás [régiójának](regions.md) nevét tartalmazza (példa: `westeurope` )
- `outError` (csak [Objective C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) esetében)

Ezután a `SpeechConfig` következőhöz hasonló osztályt kell létrehoznia:
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
> A hívónak biztosítania kell, hogy az engedélyezési jogkivonat érvényes legyen. Az engedélyezési jogkivonat lejárta előtt a hívónak frissítenie kell azt egy új érvényes jogkivonat meghívásával. Az új felismerő/szintetizátor létrehozásakor a rendszer a konfigurációs értékeket másolja, az új jogkivonat-érték azonban nem lesz érvényes a már létrehozott felismerők esetében. A korábban létrehozott felismerők/szintetizátorok esetében a token frissítéséhez be kell állítania a megfelelő felismerő/szintetizátor engedélyezési jogkivonatát. Ellenkező esetben a felismerők/szintetizátorok hibákba ütköznek az elismerés/szintézis során.

A módosítást követően az alkalmazásnak az egyéni tartománynévvel rendelkező, privát végpontok nélküli beszédfelismerési erőforrásokkal kell működnie. Az egyéni domain/Private Endpoint forgatókönyvek zökkenőmentes támogatásán dolgozunk.

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure Private linkről](../../private-link/private-link-overview.md)
* További információ a [SPEECH SDK](speech-sdk.md) -ról
* További információ a [beszéd – szöveg REST API](rest-speech-to-text.md)
* További információ a [szöveg – beszéd REST API](rest-text-to-speech.md)
