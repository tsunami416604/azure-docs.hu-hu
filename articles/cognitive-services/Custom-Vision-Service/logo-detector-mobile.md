---
title: 'Oktatóanyag: Egyéni billentyű detector használatával használatával ismerik fel az Azure-szolgáltatások – Custom Vision'
titlesuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy mintaalkalmazást, amely használja az Azure Custom Vision embléma észlelési forgatókönyv részeként lesz elvégezhető. Ismerje meg, hogyan Custom Vision segítségével más összetevőkkel egy teljes körű alkalmazás biztosításához.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 8866dc44c8e7ec3e0f3ed23178741535006855cd
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650326"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Oktatóanyag: Ismeri fel az Azure-szolgáltatás emblémát a kamera képek

Ebben az oktatóanyagban azt vizsgáljuk meg egy mintaalkalmazást, amely nagyobb a forgatókönyv részét képező Azure Custom Vision használja. A mesterséges Intelligencia vizuális kiépítése alkalmazást, a Xamarin.Forms-alkalmazás mobil platformokhoz elemzi az Azure-szolgáltatás emblémák kamera készíthessenek, és majd üzembe helyezi a tényleges szolgáltatások a felhasználó Azure-fiókjába. Itt megtudhatja, hogyan használja Custom Vision együtt más összetevőket egy hasznos, teljes körű alkalmazás biztosításához. Futtatni teljes alkalmazás saját maga, vagy egyszerűen a Custom Vision része a telepítés befejezéséhez, és ismerje meg, hogy az alkalmazás használ, azt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Hozzon létre egy egyéni objektum detector használatával Azure-szolgáltatás emblémák felismerni.
> - Az alkalmazás csatlakoztatása az Azure Computer Vision, és egyéni vizuális.
> - Hozzon létre egy Azure egyszerű szolgáltatásfiókot az alkalmazás Azure-szolgáltatások üzembe helyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- A Visual Studióhoz készült Xamarin munkaterhelés (lásd: [Xamarin telepítése](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Az iOS vagy Android-emulátorban a Visual Studióhoz
- A [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (nem kötelező)

## <a name="get-the-source-code"></a>A forrás-kód kérése

Ha a megadott webalkalmazás használni kívánt, klónozza, vagy töltse le az alkalmazás forráskódjának a [mesterséges Intelligencia vizuális kiépítése](https://github.com/Microsoft/AIVisualProvision) tárházban a Githubon. Nyissa meg a *Source/VisualProvision.sln* fájlt a Visual Studióban. Később fogja módosíthatja a projektfájlok némelyike ezért is futtathatja az alkalmazást.

## <a name="create-an-object-detector"></a>Hozzon létre egy objektumot detector használatával

Jelentkezzen be a [Custom Vision webhely](https://customvision.ai/) , és hozzon létre egy új projektet. Adja meg az Objektumfelismerés projektek és embléma a tartományt; Ez lehetővé teszi a szolgáltatás egy optimalizált embléma észlelési algoritmus használatát. 

![Új projekt ablakának a Custom Vision webhelyén, a Chrome böngészőben](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ezután az embléma észlelési algoritmus betanítás rendszerképek az Azure-szolgáltatás emblémák feltöltése és címkézés őket manuálisan. A AIVisualProvision tárház tartalmaz egy betanító kép, amelyet használhat. A webhelyen válassza ki a **lemezképeket** gombot a **betanító kép** fülre. Ezután nyissa meg a **dokumentumok/képek/Training_DataSet** mappában található az adattárban. Kell manuálisan címkézése a emblémák az egyes rendszerképek, így csak tesztelt ki a projektet, előfordulhat, hogy szeretné-e a lemezképek csak egy részhalmazát feltölteni. Minden címke használatához legalább 15 példányok feltöltésére.

Miután feltölti a betanító kép, válassza ki a jelenik az elsőt. A címkézési ablakban megjelenik. Állít be, és rendelje hozzá a címkéket az egyes rendszerképek mindkét emblémának. 

![A Custom Vision webhelyen címkézés embléma](media/azure-logo-tutorial/tag-logos.png)

Az alkalmazás konfigurálva van az adott címkével karakterláncokat. Látni fogja a definíciókat a *Source\VisualProvision\Services\Recognition\RecognitionService.cs* fájlt:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Követően egy képet, nyissa meg a következő címke jobb. Amikor végzett, zárja be a címkézési ablakot.

## <a name="train-the-object-detector"></a>Az objektum érzékelő betanítása

A bal oldali panelen állítsa be a **címkék** váltson **címkézett** a kép megjelenítéséhez. Ezután válassza ki a modell betanításához az oldal tetején lévő zöld gombot. Ez hogyan az algoritmust, hogy ismeri fel az új képek ugyanazt a címkét. Azt is teszteli a modellt a meglévő rendszerképek pontossága pontszámok létrehozásához néhány.

![A Custom Vision webhely betanító kép lapján. Az ezen a képernyőfelvételen látható szerint alakul a vonat gomb](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Az előrejelzési URL-Címének lekéréséhez

Miután a modell tanítása, készen áll integrálnia kell az alkalmazást. Ehhez be kell szereznie, a végpont URL-címe (a modellt, amely lekérdezi az alkalmazás címe) és az előrejelzési kulcs (a hozzáférést az alkalmazás számára előrejelzési kérések). Az a **teljesítmény** lapon jelölje be a **előrejelzési URL-cím** gombra a lap tetején.

![A Custom Vision webhelyre, és egy URL-címét és API-kulcs megjelenítő előrejelzési API ablak megjelenítése](media/azure-logo-tutorial/cusvis-endpoint.png)

Másolja ki a fájl URL-címe és a **előrejelzés-kulcs** értéket a megfelelő mezőket a *Source\VisualProvision\AppSettings.cs* fájlt:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Vizsgálja meg a Custom Vision használat

Nyissa meg a *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* fájlra kattintva látható, hogy az alkalmazás által használt a Custom Vision kulcsának és végpontjának URL-CÍMÉT. A **PredictImageContentsAsync** metódus lemondás mellett képfájl bájt adatfolyam token (az aszinkron feladat management) vesz igénybe, egyéni vizuális előrejelzési API-hívások és az előrejelzés eredményét adja vissza. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Ez az eredmény formájában történik egy **PredictionResult** példányt, amely maga is megtalálhatja az **előrejelzési** példányok. A **előrejelzési** észlelt címke és a határoló box helyét a rendszerkép tartalmazza.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

További információ az alkalmazás hogyan kezeli az ezeket az adatokat, kezdje a **GetResourcesAsync** metódust. Ez a módszer van definiálva a *Source/VisualProvision/Services/Recognition/RecognitionService.cs* fájlt.  

## <a name="add-computer-vision"></a>Adja hozzá a Computer Vision

Az oktatóanyag a Custom Vision része befejeződött. Ha szeretné futtatni az alkalmazást, szüksége lesz a Computer Vision service, valamint integrálhatja. Az alkalmazás használ a Computer Vision szöveg szolgáltatás embléma észlelési példányába. Egy Azure-embléma felismeri a megjelenését *vagy* telefonközelben nyomtatott szöveg. Egyéni Látástechnológiai modelljeit, ellentétben a Computer Vision imagenet van bizonyos műveletek végrehajtása a képek és videók.

Fizessen elő a Computer Vision service, kulcsának és végpontjának URL-Címének lekéréséhez. Ez a lépés segítséget, lásd: [előfizetői azonosítók beszerzése](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![A Computer Vision service az Azure Portalon, a kiválasztott gyors üzembe helyezési menü. Egy hivatkozást a kulcsok vázolt, mivel az API-végpont URL-címe](media/azure-logo-tutorial/comvis-keys.png)

Ezután nyissa meg a *Source\VisualProvision\AppSettings.cs* fájlt, és töltse fel a `ComputerVisionEndpoint` és `ComputerVisionKey` változók a megfelelő értékekkel.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazás egy Azure egyszerű szolgáltatásfiókot szolgáltatások történő üzembe helyezéséhez az Azure-előfizetés szükséges. Egyszerű szolgáltatás lehetővé teszi egy alkalmazás szerepkörön alapuló hozzáférés-vezérlés használatával konkrét engedélyeket delegálhatnak. További tudnivalókért tekintse meg a [végigvezeti a szolgáltatásnevek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Akkor is egy egyszerű szolgáltatás létrehozása az Azure Cloud Shell vagy az Azure CLI használatával itt látható módon. Első lépésként jelentkezzen be, és válassza ki a használni kívánt előfizetést.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Ezután hozzon létre a szolgáltatásnevet. (Ez a folyamat eltarthat egy ideig, a befejezéshez.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

A sikeres befejezését követően megjelenik az alábbi JSON-kimenetet, beleértve a szükséges hitelesítő adatokat.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Jegyezze fel a `clientId` és `tenantId` értékeket. Hozzáadhatja őket a megfelelő mezőket a *Source\VisualProvision\AppSettings.cs* fájlt.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezen a ponton az alkalmazások hozzáférése az adott:
* Egyéni vizuális betanított modell
* a Computer Vision service
* egy egyszerű szolgáltatásfiókot 

Kövesse az alábbi lépéseket az alkalmazás futtatásához:

1. A Visual Studio Megoldáskezelőjében közül választhat a **VisualProvision.Android** projekt vagy a **VisualProvision.iOS** projekt. Válassza ki a megfelelő emulátor, vagy a csatlakoztatott mobil eszköz a legördülő menüből, kattintson az eszköztáron. Ezután futtassa az alkalmazást.

    > [!NOTE]
    > Szüksége lesz egy MacOS-eszköz futtatásához egy iOS-emulátoron.

1. Az első képernyőn írja be a szolgáltatáspéldány ügyfél-azonosítója, bérlői azonosító és jelszó. Válassza ki a **bejelentkezési** gombra.

    > [!NOTE]
    > Az egyes emulátory systému a **bejelentkezési** gomb ebben a lépésben nem aktiválódik. Ha ez történik, állítsa le az alkalmazást, nyissa meg a *Source/VisualProvision/Pages/LoginPage.xaml* fájlt és keresse meg a `Button` feliratú elem **bejelentkezési GOMB**, távolítsa el a következő sort, és futtassa az alkalmazást újra.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Az alkalmazás képernyőjéről, az egyszerű szolgáltatás hitelesítő adatai mezők](media/azure-logo-tutorial/app-credentials.png)

1. A következő képernyőn válassza ki a legördülő menüből az Azure-előfizetésében. (Ebben a menüben tartalmaznia kell az előfizetést, amelyhez hozzáféréssel rendelkezik az egyszerű szolgáltatás összes.) Válassza ki a **Folytatás** gombra. Ezen a ponton az alkalmazás kérheti, hogy az eszköz kamerájának és a fényképe tárolási hozzáférést. A hozzáférési engedélyeket.

    ![Az alkalmazás képernyőjéről, a cél Azure-előfizetéshez egy legördülő mező megjelenítése](media/azure-logo-tutorial/app-az-subscription.png)

    

1. Az eszközön a kamera aktív lesz. Fényképet is az Azure-szolgáltatás emblémák, amely, betanított egyikét. Egy üzembe helyezési időszakot kell kérnie, hogy az új szolgáltatások régiók és -erőforrások csoport kijelölése (mint ha voltak üzembe őket az Azure Portalról). 

    ![Smartphone kamera képernyő két tanulmány cutouts Azure logók összpontosít.](media/azure-logo-tutorial/app-camera-capture.png)

    ![Egy alkalmazás képernyőjéről, az üzembe helyezési régiók és -erőforrások csoport mezők](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha követte a lépéseket, az ebben a forgatókönyvben, és az alkalmazás üzembe helyezése az Azure-szolgáltatásokhoz a fiókjához használt, nyissa meg a [az Azure portal](https://ms.portal.azure.com/). A szolgáltatások nem szeretné használni, megszakítása

Ha azt tervezi, a saját objektum észlelési projekt létrehozása a Custom Vision, előfordulhat, hogy törölni kívánja a embléma észlelési ebben az oktatóanyagban létrehozott projekt. A Custom Vision, ingyenes próbaverziójára lehetővé teszi, hogy csak két projektekhez. Az embléma észlelési projekt törlése a a [Custom Vision webhely](https://customvision.ai), nyissa meg **projektek** majd válassza a Kuka ikonra a **saját új projekt**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállítása, és megvizsgálta a Custom Vision service segítségével észlelheti a mobil fényképek emblémák teljes körű Xamarin.Forms-alkalmazás. Ezután egy egyéni Látástechnológiai modell létrehozásához, hogy egy saját-alkalmazás létrehozásakor férhessenek hozzá hatékony és pontos ajánlott eljárások megismerése.

> [!div class="nextstepaction"]
> [Tartalombesoroló teljesítményének hogyan](getting-started-improving-your-classifier.md)