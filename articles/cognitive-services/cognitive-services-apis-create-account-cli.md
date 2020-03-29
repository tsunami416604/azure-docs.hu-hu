---
title: Cognitive Services-erőforrás létrehozása az Azure CLI használatával
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services használatának megkezdéséhez hozzon létre és előfizetjen egy erőforrásra az Azure parancssori felületén.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 72b00d78d19ed0e963b4dad01b82033c659e1efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219609"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Cognitive Services-erőforrás létrehozása az Azure parancssori felületével (CLI)

Ezzel a rövid útmutatóval ismerkedjen meg az Azure Cognitive Services szolgáltatással az [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)használatával. A Cognitive Servicest az Azure-előfizetésben létrehozott [Azure-erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) képviselik. Az erőforrás létrehozása után használja az alkalmazások hitelesítéséhez létrehozott kulcsokat és végpontot. 


Ebben a rövid útmutatóban megtudhatja, hogyan regisztrálhat az Azure Cognitive Services szolgáltatásra, és hogyan hozhat létre egy fiókot, amely egyszolgáltatásos vagy többszolgáltatásos előfizetéssel rendelkezik: Az [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)használata. Ezeket a szolgáltatásokat az [Azure-erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)képviselik, amelyek lehetővé teszik, hogy az Azure Cognitive Services API-k közül egy vagy többhez csatlakozzon.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [Hozzon létre egyet](https://azure.microsoft.com/free/) ingyen.
* Az [Azure parancssori felülete(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Telepítse az Azure CLI-t, és jelentkezzen be 

Telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). A CLI helyi telepítéséhez futtassa az [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) parancsot:

```azurecli-interactive
az login
```

A zöld Try **It** gombbal is futtathatja ezeket a parancsokat a böngészőben.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Új Azure Cognitive Services-erőforráscsoport létrehozása

A Cognitive Services-erőforrás létrehozása előtt rendelkeznie kell egy Azure-erőforráscsoport az erőforrás. Új erőforrás létrehozásakor létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre új erőforráscsoportot.

### <a name="choose-your-resource-group-location"></a>Az erőforráscsoport helyének kiválasztása

Erőforrás létrehozásához szüksége lesz az előfizetéséhez elérhető egyik Azure-helyre. Az [az fióklista-helyek](/cli/azure/account#az-account-list-locations) paranccsal lekérheti az elérhető helyek listáját. A legtöbb Cognitive Services több helyről is elérhető. Válassza ki az Önhöz legközelebb állót, vagy nézze meg, hogy mely helyek érhetők el a szolgáltatáshoz.

> [!IMPORTANT]
> * Ne feledje az Azure-beli helyét, mivel szüksége lesz rá az Azure Cognitive Services hívásakor.
> * Egyes Cognitive Services-szolgáltatások elérhetősége régiónként változhat. További információ: [Azure-termékek régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Miután az azure-hely, hozzon létre egy új erőforráscsoportot az Azure CLI az [az csoport létrehozása](/cli/azure/group#az-group-create) parancs használatával.

Az alábbi példában cserélje `westus2` le az azure-hely az előfizetéshez elérhető Azure-helyek egyikére.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Válasszon kognitív szolgáltatási és tarifacsomagot

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás "fajtáját", valamint a kívánt [tarifacsomagot](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy termékváltozatot). Ezt és más információkat paraméterként fogja használni az erőforrás létrehozásakor.

### <a name="multi-service"></a>Többszolgáltatásos

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További részletekért tekintse meg az [árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/) oldalt.            | `CognitiveServices`     |


> [!NOTE]
> Az alábbi Cognitive Services számos ingyenes szinttel rendelkezik, amelyekkel kipróbálhatja a szolgáltatást. Az ingyenes szint használatához `F0` használja az erőforrás termékváltozataként.

### <a name="vision"></a>Vizuális elemek

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Számítógépes látástechnológia            | `ComputerVision`          |
| Egyéni Vision - Előrejelzés | `CustomVision.Prediction` |
| Egyéni Vision - Képzés   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Keresés

| Szolgáltatás            | Altípus                  |
|--------------------|-----------------------|
| Bing – Automatikus kiegészítés   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing kereső        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Beszéd

| Szolgáltatás            | Altípus                 |
|--------------------|----------------------|
| Beszédszolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

### <a name="language"></a>Nyelv

| Szolgáltatás            | Altípus                |
|--------------------|---------------------|
| Űrlap ismertetése | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

### <a name="decision"></a>Döntés

| Szolgáltatás           | Altípus               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Tartalommoderátor | `ContentModerator` |
| Personalizer      | `Personalizer`     |

Az [az cognitiveservices fióklista-típusokkal](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) az elérhető Cognitive Service "fajtáinak" listáját megtalálja:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Új erőforrás hozzáadása az erőforráscsoporthoz

Új Cognitive Services-erőforrás létrehozásához és előfizetéséhez használja az [az cognitiveservices fiók létrehozási](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) parancsát. Ez a parancs új számlázható erőforrást ad hozzá a korábban létrehozott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás "fajtáját", valamint a tarifacsomagot (vagy termékváltozatot) és az Azure-helyet:

Létrehozhat egy F0 (ingyenes) erőforrást az anomáliadetektorhoz, amelyet az alábbi paranccsal neveztek el. `anomaly-detector-resource`

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beszereznie

A parancssori csatoló (CLI) helyi telepítésébe való bejelentkezéshez használja az [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli-interactive
az login
```

Az [az cognitiveservices fiókkulcsok listájának parancs](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) használatával a Cognitive Service-erőforrás kulcsainak lekérni.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Díjszabási szintek és számlázás

A tarifacsomagok (és a számlázott összeg) a hitelesítési adatok alapján küldött tranzakciók számán alapulnak. Minden tarifacsomag a következőket határozza meg:
* engedélyezett tranzakciók maximális száma másodpercenként (TPS).
* a tarifacsomagon belül engedélyezett szolgáltatási funkciók.
* A tranzakciók előre meghatározott összegének költsége. Ha túllépi ezt az összeget, a szolgáltatás [árazási adatai](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) ban meghatározott többletköltség jár.

## <a name="get-current-quota-usage-for-your-resource"></a>Az erőforrás aktuális kvótahasználatának beszereznie

Az [az cognitiveservices-fióklista-használati](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) parancs használatával a kognitív szolgáltatás erőforrás használatának lekérni.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy törölje, és távolítsa el a Cognitive Services-erőforrás, törölheti azt, vagy az erőforráscsoport. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához használja az az csoport törlés parancsát.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Lásd még

* [Az Azure Cognitive Services kéréseinek hitelesítése](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tároló támogatása](cognitive-services-container-support.md)
