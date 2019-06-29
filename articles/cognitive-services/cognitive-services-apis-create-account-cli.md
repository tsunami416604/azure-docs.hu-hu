---
title: Hozzon létre egy Cognitive Services-fiókot az Azure CLI használatával
titlesuffix: Azure Cognitive Services
description: Hogyan hozhat létre az Azure CLI használatával az Azure Cognitive Services API-k-fiókot.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454894"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Az Azure parancssori Interface(CLI) használatával Cognitive Services-fiók létrehozása

Ebben a rövid, megtudhatja, hogyan regisztrálhat az Azure Cognitive Services, és hozzon létre egy fiókot, amely rendelkezik egy – olyan egyetlen szolgáltatást, illetve több szolgáltatási előfizetés használata az [Azure parancssori Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ezek a szolgáltatások szerepelnek az Azure által [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), amelyek lehetővé teszik, hogy kapcsolódjon az Azure Cognitive Services API-k közül legalább egyet.

## <a name="prerequisites"></a>Előfeltételek

* Egy érvényes Azure-előfizetés. [Hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.
* A [Interface(CLI) Azure parancssori felületével](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Az Azure CLI telepítése és bejelentkezés 

Telepítse az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Jelentkezzen be a parancssori felület helyi telepítésen, futtassa a [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index#az-login) parancsot:

```console
az login
```

Is használhatja a zöld **Kipróbálom** gombra kattintva futtassa az alábbi parancsokat a böngészőben.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Hozzon létre egy új Azure Cognitive Services

Az előfizetések, a Cognitive Services, Azure-erőforrások képviseli. Azure-erőforráscsoport összes Cognitive Services-fiók (és a kapcsolódó Azure-erőforrás) kell tartoznia.

### <a name="choose-your-resource-group-location"></a>Válassza ki az erőforráscsoport helye

Erőforrás létrehozása kell egy elérhető az Azure-helyen az előfizetéshez. Lekérheti az elérhető helyek listáját a [az fiók list-locations](/cli/azure/account#az_account_list) parancsot. A legtöbb Cognitive Services több helyről is elérhetők. Válassza ki az Önhöz legközelebbi egy, vagy tekintse meg, melyik helyek elérhetőek a szolgáltatáshoz.

> [!IMPORTANT]
> * Az Azure-beli hely, ne feledje, mert szüksége lesz rá az Azure Cognitive Services hívása során.
> * Néhány kognitív szolgáltatások rendelkezésre állásának régiónként. További információkért lásd: [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Miután az Azure-beli hely, hozzon létre egy új erőforráscsoportot az Azure parancssori felületén a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot.

Az alábbi példában cserélje le az Azure-beli hely `westus2` az előfizetéshez elérhető Azure-helyek egyikével.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>A cognitive Services-szolgáltatás és a tarifacsomag kiválasztása

Új erőforrás létrehozásakor, ismernie kell a "kind" szeretné használni, valamint a szolgáltatás a [tarifacsomag](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy termékváltozat) szeretné. Használni kívánt és más információk paraméterek az erőforrás létrehozásakor.

> [!NOTE]
> Számos, a Cognitive services segítségével próbálja ki a szolgáltatást ingyenesen rendelkezik. Az ingyenes szint használatához `F0` , az erőforrás a termékváltozat.

### <a name="vision"></a>Látás

| Szolgáltatás                    | Kind                      |
|----------------------------|---------------------------|
| Számítógépes látástechnológia            | `ComputerVision`          |
| Egyéni vizuális - előrejelzés | `CustomVision.Prediction` |
| Custom Vision - képzés   | `CustomVision.Training`   |
| Face API                   | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Keresés

| Szolgáltatás            | Kind                  |
|--------------------|-----------------------|
| Bing – Automatikus kiegészítés   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing kereső        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Beszéd

| Szolgáltatás            | Kind                 |
|--------------------|----------------------|
| Beszédszolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

### <a name="language"></a>Nyelv

| Szolgáltatás            | Kind                |
|--------------------|---------------------|
| Képernyő ismertetése | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

### <a name="decision"></a>Döntés

| Szolgáltatás           | Kind               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Tartalommoderátor | `ContentModerator` |
| Personalizer      | `Personalizer`     |

Annak elérhető a Cognitive Services listáját "típusú" az a [az cognitiveservices account list-típusú](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) parancsot:

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Adjon hozzá egy új erőforrást az erőforráscsoport

Hozzon létre, és iratkozzon fel az új Cognitive Services-erőforrás, használja a [az cognitive Services-fiók létrehozása](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) parancsot. Ezzel a paranccsal egy új számlázható erőforrás hozzáadja a korábban létrehozott erőforráscsoportot. Az új erőforrás létrehozásakor, akkor ismernie kell, a tarifacsomag (vagy sku) együtt használni kívánt szolgáltatás "típusú" és a egy Azure-beli hely:

Létrehozhat egy F0 (ingyenes) erőforrás az Anomáliadetektálási detector használatával nevű `anomaly-detector-resource` az alábbi paranccsal.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Az előfizetés-kulcsok beolvasása

Jelentkezzen be a helyi telepítése a parancssori Interface(CLI), használja a [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```console
az login
```

Használja a [az cognitiveservices account kulcsok listája](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) kérnie a kulcsokat a Cognitive Services-szolgáltatás-erőforrás a parancsot.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni, és távolítsa el a Cognitive Services-előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésekor a az erőforráscsoporthoz társított erőforrásokat is törli.

A az group delete parancs használatával távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Lásd még

* [Az Azure Cognitive Services-kérések hitelesítéséhez](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tároló támogatása](cognitive-services-container-support.md)
