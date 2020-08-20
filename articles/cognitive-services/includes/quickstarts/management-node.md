---
title: 'Gyors útmutató: Azure Management ügyféloldali kódtár Node.js'
description: Ebben a rövid útmutatóban megismerheti a Node.js Azure Management ügyféloldali függvénytárának első lépéseit.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607788"
---
[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [Node.js](https://nodejs.org/) aktuális verziója

## <a name="create-an-azure-service-principal"></a>Azure egyszerű szolgáltatás létrehozása

Ahhoz, hogy az alkalmazás működjön az Azure-fiókkal, szüksége lesz egy Azure-szolgáltatásra az engedélyek kezeléséhez. Kövesse az [Azure egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)című témakör utasításait.

Egyszerű szolgáltatás létrehozásakor látni fogja, hogy rendelkezik egy titkos értékkel, egy AZONOSÍTÓval és egy alkalmazás-AZONOSÍTÓval. Mentse az alkalmazás AZONOSÍTÓját és a titkos kulcsot egy ideiglenes helyre a későbbi lépések végrehajtásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Cognitive Services erőforrás létrehozása előtt a fióknak rendelkeznie kell egy Azure-erőforráscsoporthoz, amely tartalmazza az erőforrást. Ha még nem rendelkezik erőforráscsoporthoz, hozzon létre egyet a [Azure Portal](https://ms.portal.azure.com/).

## <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

A folytatás előtt hozzon létre egy _index.js_ nevű fájlt.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a következő NPM-csomagokat:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

### <a name="import-libraries"></a>Kódtárak importálása

Nyissa meg _index.js_ -parancsfájlját, és importálja a következő könyvtárakat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá a következő mezőket a parancsfájl gyökeréhez, és töltse ki az értékeket a létrehozott egyszerű szolgáltatás és az Azure-fiók adatai alapján.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Ezután adja hozzá a következő `quickstart` függvényt a program fő munkájának kezeléséhez. A kód első blokkja egy **CognitiveServicesManagementClient** objektumot hoz létre a fent megadott hitelesítőadat-változók használatával. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Hívás-felügyeleti függvények

Adja hozzá a következő kódot a függvény végéhez a `quickstart` rendelkezésre álló erőforrások listázásához, egy minta erőforrás létrehozásához, a saját tulajdonban lévő erőforrások listázásához, majd a minta erőforrás törléséhez. Ezeket a függvényeket a következő lépésekben fogja meghatározni.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. A következő függvény felsorolja az elérhető kognitív szolgáltatások "típusait".

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Tekintse meg az alábbi lista az SKU-ket és a díjszabási információkat. 

#### <a name="multi-service"></a>Több szolgáltatás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/) oldalt.            | `CognitiveServices`     |


#### <a name="vision"></a>Látás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision – előrejelzés | `CustomVision.Prediction` |
| Custom Vision – képzés   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

#### <a name="search"></a>Keresés

| Szolgáltatás            | Altípus                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Szolgáltatás            | Altípus                 |
|--------------------|----------------------|
| Beszédszolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

#### <a name="language"></a>Nyelv

| Szolgáltatás            | Altípus                |
|--------------------|---------------------|
| Űrlap megértése | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

#### <a name="decision"></a>Döntés

| Szolgáltatás           | Altípus               |
|-------------------|--------------------|
| Anomáliadetektor  | `AnomalyDetector`  |
| Tartalommoderátor | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* előre meghatározott számú tranzakció díja. Ennek a számnak a meghaladása esetén a szolgáltatás [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) alapján külön díjat számítunk fel.

> [!NOTE]
> Számos Cognitive Services rendelkezik egy ingyenes szintű lehetőséggel, amellyel kipróbálhatja a szolgáltatást. Az ingyenes szintet az `F0` erőforráshoz tartozó SKU-ként használhatja.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** függvényt. Ez a függvény új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő függvény végrehajtja az összes ilyen argumentumot, és létrehoz egy erőforrást.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Az Azure-fiókban lévő összes erőforrás megtekintéséhez (az összes erőforráscsoport esetében) használja a következő függvényt:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő függvény törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Adja hozzá a következő kódot a parancsfájl aljához, hogy a fő `quickstart` függvényt a hibakezelés során hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Ezután a konzol ablakban futtassa az alkalmazást a `node` paranccsal.

```console
node index.js
```

## <a name="see-also"></a>Lásd még

* [Az Azure Management SDK dokumentációja](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Mi az Azure Cognitive Services?](../../Welcome.md)
* [Kérelmek hitelesítése az Azure Cognitive Services](../../authentication.md)
* [Új erőforrás létrehozása az Azure Portal használatával](../../cognitive-services-apis-create-account.md)