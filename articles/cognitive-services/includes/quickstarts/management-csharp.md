---
title: 'Gyors útmutató: Azure Management ügyféloldali kódtár a .NET-hez'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült Azure Management ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607778"
---
[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="create-an-azure-service-principal"></a>Azure egyszerű szolgáltatás létrehozása

Ahhoz, hogy az alkalmazás működjön az Azure-fiókkal, szüksége lesz egy Azure-szolgáltatásra az engedélyek kezeléséhez. Kövesse az [Azure egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)című témakör utasításait.

Egyszerű szolgáltatás létrehozásakor látni fogja, hogy rendelkezik egy titkos értékkel, egy AZONOSÍTÓval és egy alkalmazás-AZONOSÍTÓval. Mentse az alkalmazás AZONOSÍTÓját és a titkos kulcsot egy ideiglenes helyre a későbbi lépések végrehajtásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Cognitive Services erőforrás létrehozása előtt a fióknak rendelkeznie kell egy Azure-erőforráscsoporthoz, amely tartalmazza az erőforrást. Ha még nem rendelkezik erőforráscsoporthoz, a folytatás előtt hozzon létre egyet a [Azure Portal](https://ms.portal.azure.com/) .

## <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást. A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `azure-management-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Azure Management ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

### <a name="import-libraries"></a>Kódtárak importálása

Nyissa meg a *program.cs* , és adja hozzá a következő `using` utasításokat a fájl elejéhez:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá a következő mezőket a *program.cs* gyökeréhez, és töltse fel az értékeit a létrehozott egyszerű szolgáltatásnév és az Azure-fiók adatai alapján.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Ezután a **Main** metódusban használja ezeket az értékeket egy **CognitiveServicesManagementClient** objektum létrehozásához. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Hívás-felügyeleti módszerek

Adja hozzá a következő kódot a **Main** metódushoz a rendelkezésre álló erőforrások listázásához, egy minta erőforrás létrehozásához, a saját tulajdonú erőforrások listázásához, majd a minta erőforrás törléséhez. Ezeket a metódusokat a következő lépésekben fogja meghatározni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. A következő módszer meghívásával megkeresheti a rendelkezésre álló kognitív szolgáltatások listáját a parancsfájlban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

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

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** metódust. Ez a metódus új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő metódus ezeket argumentumként fogadja el, és létrehoz egy erőforrást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Ha az Azure-fiók összes erőforrását (az összes erőforráscsoportot) szeretné megtekinteni, használja a következő metódust:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő metódus törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

## <a name="see-also"></a>Lásd még

* [Az Azure Management SDK dokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Mi az Azure Cognitive Services?](../../Welcome.md)
* [Kérelmek hitelesítése az Azure Cognitive Services](../../authentication.md)
* [Új erőforrás létrehozása az Azure Portal használatával](../../cognitive-services-apis-create-account.md)