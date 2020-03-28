---
title: 'Oktatóanyag: Egyéni embléma-detektor használata az Azure-szolgáltatások felismeréséhez – Custom Vision'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy mintaalkalmazáson keresztül, amely a Custom Vision-t egy embléma-észlelési forgatókönyv részeként használja. Ismerje meg, hogyan használja a Custom Vision más összetevőkkel egy végpontok között történő alkalmazás biztosítását.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 51fa6d4859eb4b7f059b499ba73d84d9fc65e6f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78398989"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Oktatóanyag: Az Azure-szolgáltatás emblémáinak felismerése kameraképekben

Ebben az oktatóanyagban egy mintaalkalmazást fedezhet fel, amely egy nagyobb forgatókönyv részeként használja a Custom Vision t. Az AI Visual Provision alkalmazás, a Xamarin.Forms alkalmazás mobil platformokra, elemzi az Azure szolgáltatásemblémák kameraképeit, majd telepíti a tényleges szolgáltatásokat a felhasználó Azure-fiókjába. Itt megtudhatja, hogyan használja a Custom Vision-t más összetevőkkel együttműködve egy hasznos végpontok közötti alkalmazás biztosításához. Futtathatja a teljes alkalmazásforgatókönyvet saját maga, vagy csak a beállítás Egyéni látás részét futtathatja, és feltárhatja, hogyan használja az alkalmazás.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Hozzon létre egy egyéni objektum-detektort az Azure szolgáltatásemblémák felismeréséhez.
> - Csatlakoztassa alkalmazását az Azure Computer Vision és a Custom Vision alkalmazáshoz.
> - Hozzon létre egy egyszerű Azure-szolgáltatásfiókot az Azure-szolgáltatások alkalmazásból történő üzembe helyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)
- A Visual Studio Xamarin-munkaterhelése [(lásd: Xamarin telepítése](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- IOS vagy Android emulátor visual studio
- Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (nem kötelező)

## <a name="get-the-source-code"></a>A forráskód lekérése

Ha a megadott webalkalmazást szeretné használni, klónozza vagy töltse le az alkalmazás forráskódját a GitHub [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) tárházból. Nyissa meg a *Forrás/VisualProvision.sln* fájlt a Visual Studióban. Később szerkesztheti a projektfájlok egy részét, hogy futtathassa az alkalmazást.

## <a name="create-an-object-detector"></a>Objektumdetektor létrehozása

Jelentkezzen be a [Custom Vision webhelyre,](https://customvision.ai/) és hozzon létre egy új projektet. Adjon meg egy objektumészlelési projektet, és használja az Embléma tartományt; ez lehetővé teszi, hogy a szolgáltatás egy emblémaészlelésre optimalizált algoritmust használjon. 

![Új projektablak a Custom Vision webhelyen a Chrome böngészőben](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ezután tanítsa be az emblémaészlelési algoritmust az Azure-szolgáltatásemblémák képeinek feltöltésével és manuális címkézésével. Az AIVisualProvision tárház egy betanítási lemezképek, amelyek segítségével egy készlet. A webhelyen válassza a **Képek hozzáadása** gombot a **Betanítási képek** lapon. Ezután lépjen a tárház **Dokumentumok/képek/Training_DataSet** mappájába. Az egyes képeken manuálisan kell címkéznie az emblémákat, ezért ha csak ezt a projektet teszteli, érdemes lehet a képeknek csak egy részét feltölteni. Töltsön fel legalább 15 példányt minden használni kívánt címkéből.

Az oktatóképek feltöltése után válassza ki az elsőt a kijelzőn. Megjelenik a címkézési ablak. Rajzoljon dobozokat, és rendeljen címkéket az egyes emblémákhoz az egyes képeken. 

![Embléma címkézése a Custom Vision webhelyen](media/azure-logo-tutorial/tag-logos.png)

Az alkalmazás úgy van beállítva, hogy adott címkekarakterláncokkal működjön. A definíciók a *Forrás\VisualProvision\Services\Recognition\RecognitionService.cs* fájlban találhatók:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

Miután megcímkézett egy képet, jobbra a következő t. Zárja be a címkézési ablakot, amikor végzett.

## <a name="train-the-object-detector"></a>Az objektumdetektor betanítása

A bal oldali ablaktáblában állítsa a **Címkék** kapcsolót **Címkézve** beállításra a képek megjelenítéséhez. Ezután válassza ki a lap tetején található zöld gombot a modell betanításához. Az algoritmus betanítja ugyanazokat a címkéket az új képeken. Azt is teszteli a modell néhány meglévő képeket, hogy hozzon létre pontossági pontszámok.

![A Custom Vision weboldala a Képzési képek lapon. Ezen a képernyőképen a Vonat gomb](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Az előrejelzési URL-cím beszerezni

A modell betanítása után készen áll arra, hogy integrálja azt az alkalmazásba. Be kell szereznie a végpont URL-címét (a modell címét, amelyet az alkalmazás lekérdez) és az előrejelzési kulcsot (az alkalmazás hozzáférésének biztosításához az előrejelzési kérelmekhez). A **Teljesítmény** lapon kattintson az **előrejelzési URL-cím** gombra a lap tetején.

![A Custom Vision webhely, amely egy előrejelzési API-ablakot jelenít meg, amely url-címet és API-kulcsot jelenít meg](media/azure-logo-tutorial/cusvis-endpoint.png)

Másolja a végpont URL-címét és az **Előrejelzési kulcs** értéket a *Forrás\VisualProvision\AppSettings.cs* fájl megfelelő mezőibe:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Az egyéni látás használatának vizsgálata

Nyissa meg a *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* fájlt, és nézze meg, hogyan használja az alkalmazás az Egyéni vision kulcsot és a végpont URL-címét. A **PredictImageContentsAsync** metódus egy képfájl bájtfolyamát veszi igénybe egy megszakítási jogkivonattal együtt (aszinkron feladatkezelés esetén), meghívja a Custom Vision előrejelzési API-t, és visszaadja az előrejelzés eredményét. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

Ez az eredmény egy **PredictionResult** példány formáját ölti, amely maga is tartalmazza az **előrejelzési** példányok listáját. Az **előrejelzés** egy észlelt címkét és annak határolókereti helyét tartalmazza a képen.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

Ha többet szeretne megtudni arról, hogy az alkalmazás hogyan kezeli ezeket az adatokat, kezdje a **GetResourcesAsync** metódussal. Ez a módszer a *Forrás/VisualProvision/Services/Recognition/RecognitionService.cs* fájlban van definiálva.  

## <a name="add-computer-vision"></a>Számítógép-látás hozzáadása

Az oktatóanyag Egyéni látás része befejeződött. Ha futtatni szeretné az alkalmazást, integrálnia kell a Computer Vision szolgáltatást is. Az alkalmazás a Computer Vision szövegfelismerő funkciót használja az embléma észlelési folyamatának kiegészítésére. Az Azure-embléma a megjelenése *vagy* a hozzá nyomtatott szöveg alapján ismerhető fel. A Custom Vision modellekkel ellentétben a Computer Vision előre ki van képezve bizonyos műveletek elvégzésére képeken vagy videókon.

Iratkozzon fel a Computer Vision szolgáltatásra a kulcs- és végpont URL-címének leéséhez. Ha segítségre van szüksége ebben a lépésben, olvassa [el az Előfizetési kulcsok beszerzése](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![A Computer Vision szolgáltatás az Azure Portalon, a gyorsindítás menü kiválasztva. A kulcsok hivatkozása körvonalazva van, csakúgy, mint az API végpontURL-címe](media/azure-logo-tutorial/comvis-keys.png)

Ezután nyissa meg a *Source\VisualProvision\AppSettings.cs* `ComputerVisionEndpoint` fájlt, és a megfelelő értékekkel feltöltse a változókat. `ComputerVisionKey`

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazás hoz egy Azure-szolgáltatás egyszerű fiók üzembe helyezése szolgáltatások az Azure-előfizetés. Az egyszerű szolgáltatás lehetővé teszi, hogy adott engedélyeket delegáljon egy alkalmazáshoz szerepköralapú hozzáférés-vezérlés használatával. További információ: [a szolgáltatástagok útmutatója](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Az Azure Cloud Shell vagy az Azure CLI használatával létrehozhat egy egyszerű szolgáltatást, ahogy az itt látható. A kezdéshez jelentkezzen be, és válassza ki a használni kívánt előfizetést.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Ezután hozza létre a szolgáltatásnév. (Ez a folyamat eltarthat egy ideig.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Sikeres befejezése után a következő JSON-kimenet, beleértve a szükséges hitelesítő adatokat.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Vegye figyelembe `clientId` a `tenantId` és az értékeket. Adja hozzá őket a *Forrás\VisualProvision\AppSettings.cs* fájl megfelelő mezőihez.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezen a ponton hozzáférést adott az alkalmazásnak a következőkhöz:

- Betanított Egyéni vision modell
- A Computer Vision szolgáltatás
- Egyszerű szolgáltatásfiók

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. A Visual Studio Solution Explorer ben válassza a **VisualProvision.Android** projektet vagy a **VisualProvision.iOS** projektet. Válasszon egy megfelelő emulátort vagy csatlakoztatott mobileszközt a fő eszköztár legördülő menüjéből. Ezután futtassa az alkalmazást.

    > [!NOTE]
    > Az iOS-emulátor futtatásához MacOS-eszközre lesz szükség.

1. Az első képernyőn adja meg a szolgáltatás első ügyfélazonosítóját, a bérlőazonosítóját és a jelszót. Válassza a **Bejelentkezés** gombot.

    > [!NOTE]
    > Egyes emulátorokon előfordulhat, hogy a **Bejelentkezés** gomb nem aktiválódik ebben a lépésben. Ha ez történik, állítsa le az alkalmazást, nyissa meg a *Source/VisualProvision/Pages/LoginPage.xaml* fájlt, keresse meg a `Button` **LOGIN BUTTON**feliratú elemet, távolítsa el a következő sort, majd futtassa újra az alkalmazást.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Az alkalmazás képernyője, amely az egyszerű szolgáltatás hitelesítő adatainak mezőit jeleníti meg](media/azure-logo-tutorial/app-credentials.png)

1. A következő képernyőn válassza ki az Azure-előfizetést a legördülő menüből. (Ennek a menünek tartalmaznia kell az összes olyan előfizetést, amelyhez a szolgáltatásnév hozzáfér.) Válassza a **Folytatás** gombot. Ezen a ponton az alkalmazás kérheti, hogy adjon hozzáférést az eszköz kamerájához és fényképes tárhelyéhez. Adja meg a hozzáférési engedélyeket.

    ![Az alkalmazás képernyője, amely a Target Azure-előfizetés legördülő mezőjét jeleníti meg](media/azure-logo-tutorial/app-az-subscription.png)


1. A készülék kamerája aktiválódik. Fényképezésaz Azure-szolgáltatás egyik betanított emblémájáról. A központi telepítési ablakban meg kell kérni, hogy válasszon ki egy régiót és erőforráscsoportot az új szolgáltatásokhoz (ahogy azt tenné, ha az Azure Portalon telepíti őket). 

    ![Az okostelefon kamerájának képernyője az Azure-emblémák két papírkivágására fókuszál](media/azure-logo-tutorial/app-camera-capture.png)

    ![Alkalmazásképernyő a telepítési régió és az erőforráscsoport mezőivel](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha követte a forgatókönyv összes lépését, és az alkalmazás használatával üzembe helyezte az Azure-szolgáltatásokat a fiókjában, nyissa meg az [Azure Portalt.](https://ms.portal.azure.com/) Ott mondja le azon szolgáltatásokat, amelyeket nem kíván használni.

Ha azt tervezi, hogy saját objektumészlelési projektet hoz létre a Custom Vision segítségével, érdemes törölnie az ebben az oktatóanyagban létrehozott emblémaészlelési projektet. A Custom Vision ingyenes próbaverziója csak két projektet tesz lehetővé. Az emblémaészlelési projekt törléséhez nyissa meg a **Projektek** [webhelyet,](https://customvision.ai)és válassza a Kukák ikont az **Új projekt csoportban.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy teljes funkcionalitású Xamarin.Forms alkalmazást állított be és fedezett fel, amely a Custom Vision szolgáltatást használja a mobil kameraképek emblémáinak észlelésére. Ezután ismerje meg a Custom Vision modell létrehozásának bevált módszereit, hogy amikor saját alkalmazásához hoz létre egyet, hatékonysá és pontossá teheti azt.

> [!div class="nextstepaction"]
> [Hogyan lehet javítani az osztályozó](getting-started-improving-your-classifier.md)
