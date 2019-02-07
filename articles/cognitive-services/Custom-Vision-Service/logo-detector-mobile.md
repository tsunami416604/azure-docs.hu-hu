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
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773140"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Oktatóanyag: Ismeri fel az Azure-szolgáltatás emblémát a kamera képek

Ebben az oktatóanyagban egy mintaalkalmazást, amely nagyobb a forgatókönyv részét képező Azure Custom Vision fog elvégezhető. A mesterséges Intelligencia vizuális kiépítése alkalmazást, a Xamarin.Forms-alkalmazás mobil platformokhoz elemzi az Azure-szolgáltatás emblémák kamera készíthessenek, és majd üzembe helyezi a tényleges szolgáltatások a felhasználó Azure-fiókjába. Itt megtudhatja, hogyan használja Custom Vision együtt más összetevőket egy hasznos, teljes körű alkalmazás biztosításához. Előfordulhat, hogy szeretné futtatni a teljes alkalmazás saját maga, vagy egyszerűen a Custom Vision része a telepítés befejezéséhez, és ismerje meg, hogy az alkalmazás már használja azt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - Hozzon létre egy egyéni objektum detector használatával Azure-szolgáltatás emblémák felismerésére
> - Az alkalmazás csatlakoztatása az Azure Computer Vision, és egyéni vizuális
> - Hozzon létre egy Azure egyszerű szolgáltatásfiókot az alkalmazás Azure-szolgáltatások üzembe helyezéséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- A Visual Studióhoz készült Xamarin munkaterhelés (lásd: [Xamarin telepítése](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Az iOS vagy Android-emulátorban a Visual Studióhoz
- [Az Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (nem kötelező)

## <a name="get-the-source-code"></a>A forrás-kód kérése

Ha szeretné használni a megadott web app, klónozza, vagy töltse le az alkalmazás forráskódjának a [mesterséges Intelligencia vizuális kiépítése](https://github.com/Microsoft/AIVisualProvision) tárházban a Githubon. Nyissa meg a *Source/VisualProvision.sln* fájlt a Visual Studióban. Később a fogja elérhetővé tenni a szükséges módosításokat a projektfájlok némelyike ahhoz, hogy az alkalmazás futtatásához.

## <a name="create-an-object-detector"></a>Hozzon létre egy objektumot detector használatával

Jelentkezzen be a [Custom Vision webhely](https://customvision.ai/) , és hozzon létre egy új projektet. Adja meg az Objektumfelismerés projektek és embléma a tartományt; Ez lehetővé teszi a szolgáltatás egy optimalizált embléma észlelési algoritmus használatára. 

![Új projekt párbeszédpanelen ablak Chrome böngészőben a Custom Vision webhelyen](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ezután szüksége lesz az embléma észlelési algoritmus betanítása rendszerképek az Azure-szolgáltatás emblémák feltöltése és címkézés őket manuálisan. A AIVisualProvision tárház tartalmaz egy betanító kép, amelyet használhat. A webhelyen válassza ki a **lemezképeket** gombra a **betanító kép** lapra, és navigáljon arra a **dokumentumok/képek/Training_DataSet** mappában található az adattárban. Kell manuálisan címkézése a emblémák az egyes rendszerképek, így csak kipróbálni a projekthez, ha érdemes csak egy részét a képek feltöltése. Legalább 15 példányait tervezi használni minden címke fel kell tölteni.

Miután feltöltötte a betanító kép, válassza ki a jelenik az elsőt. A címkézési ablakban megjelenik. Állít be, és rendelje hozzá a címkéket az egyes rendszerképek mindkét emblémának. 

![a Custom Vision webhelyen alkalmazott címkék emblémák képe](media/azure-logo-tutorial/tag-logos.png)

Az alkalmazás konfigurálva van az adott címkével karakterláncok; Tekintse meg a definíciókat a *Source\VisualProvision\Services\Recognition\RecognitionService.cs* fájlt:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Kép címkéket, keresse meg a jogot a következő címke. Ha elkészült, lépjen ki a címkézési ablak.

## <a name="train-the-object-detector"></a>Az objektum érzékelő betanítása

A bal oldali panelen állítsa be a **címkék** váltson **címkézett**, és minden, a képek kell megjelennie. Kattintson a zöld gombot a modell betanításához az oldal tetején. Ez hogyan az algoritmust, hogy ismeri fel az új képek ugyanazt a címkét. Azt is teszteli a modellt a meglévő rendszerképek pontossága pontszámok létrehozásához néhány.

![a Custom Vision webhely betanító kép lapján; szerint alakul a vonat gomb](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Az előrejelzési URL-Címének lekéréséhez

Ha a modell tanítása, készen áll integrálnia kell az alkalmazást. Ehhez be kell szereznie, a végpont URL-címe (a modellt, amely lekérdezi az alkalmazás címe) és az előrejelzési kulcs (a hozzáférést az alkalmazás számára előrejelzési kérések). Az a **teljesítmény** lapra, majd a **előrejelzési URL-cím** gombra a lap tetején.

![egy előrejelzési API képernyő, egy URL-cím megjelenítése a Custom Vision webhely címét és api-kulcsát](media/azure-logo-tutorial/cusvis-endpoint.png)

Másolja ki a fájl URL-címe és a `Prediction-key` értéket a megfelelő mezőket a *Source\VisualProvision\AppSettings.cs* fájlt:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Vizsgálja meg a Custom Vision használat

Nyissa meg a *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* fájlra kattintva látható, a Custom Vision kulcsának és végpontjának URL-cím használata az alkalmazásban. A **PredictImageContentsAsync** metódus lemondás mellett képfájl bájt adatfolyam token (az aszinkron feladat management) vesz igénybe, egyéni vizuális előrejelzési API-hívások és az előrejelzés eredményét adja vissza. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Ez az eredmény formájában történik egy **PredictionResult** példányt, amely maga is megtalálhatja az **előrejelzési** példányok. A **előrejelzési** észlelt címke és a határoló box helyét a rendszerkép tartalmazza.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Ha szeretne további információ az alkalmazás hogyan kezeli az ezeket az adatokat, kezdőpont a **GetResourcesAsync** meghatározott metódus a *Source/VisualProvision/Services/Recognition/RecognitionService.cs* fájlt. 

## <a name="add-computer-vision"></a>Adja hozzá a Computer Vision

Az oktatóanyag a Custom Vision része befejeződött, de ha futtatja az alkalmazást, szüksége lesz a Computer Vision service, valamint integrálhatja. Az alkalmazás számítógépes Látástechnológiai szöveg felismerése funkciót használja, példányába embléma észlelési; egy Azure-embléma felismeri a megjelenését _vagy_ telefonközelben nyomtatott szöveg. Egyéni Látástechnológiai modelljeit, ellentétben a Computer Vision hajtson végre bizonyos műveleteket, a képek és videók előre betanított.

Egyszerűen fizessen elő a Computer Vision service, kulcsának és végpontjának URL-Címének lekéréséhez. Lásd: [előfizetői azonosítók beszerzése](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) Ha ehhez a lépéshez segítségre van szüksége.

![az Azure Portalon, a gyors üzembe helyezési menüben kiválasztott; a Computer Vision service kulcsok hivatkozás ki van jelölve, mivel az API-végpont URL-címe](media/azure-logo-tutorial/comvis-keys.png)

Ezután nyissa meg a *Source\VisualProvision\AppSettings.cs* fájlt, és töltse fel a `ComputerVisionEndpoint` és `ComputerVisionKey` változók a megfelelő értékekkel.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazás egy Azure egyszerű szolgáltatásfiókot szolgáltatások történő üzembe helyezéséhez az Azure-előfizetés szükséges. Egyszerű szolgáltatás lehetővé teszi egy alkalmazás szerepkörön alapuló hozzáférés-vezérlés használatával konkrét engedélyeket delegálhatnak. Tekintse meg a [végigvezeti a szolgáltatásnevek](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) Ha többet szeretne.

Létrehozhat egy egyszerű szolgáltatást az Azure Cloud Shell vagy az Azure CLI használatával (következő). Először jelentkezzen be, és válassza ki a használni kívánt előfizetést.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Ezután hozzon létre az egyszerű szolgáltatásnév (vegye figyelembe, hogy eltarthat egy ideig végrehajtásához).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Sikeres telepítést megtekintheti a következő JSON-kimenetét, amely tartalmazza a szükséges hitelesítő adatokat.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Jegyezze fel a `clientId`, és `tenantId` értékeket. Hozzáadhatja őket a megfelelő mezőket a *Source\VisualProvision\AppSettings.cs* fájlt.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezen a ponton az alkalmazások hozzáférése az adott:
* Egyéni vizuális betanított modell
* a Computer Vision service
* egy egyszerű szolgáltatásfiókot 

Kövesse az alábbi lépéseket az alkalmazás futtatásához:

1. Válassza a VisualProvision.Android vagy VisualProvision.iOS projekt a Visual Studio Solution Explorerben és a egy megfelelő emulátort vagy egy csatlakoztatott mobileszköz válassza a legördülő menüből a fő eszközsáv (vegye figyelembe, hogy kell-e a MacOS-eszköz futtatásához egy iOS-es emulátor). Ezután futtassa az alkalmazást.

1. Az első képernyő, amely betölti adja meg a szolgáltatáspéldány ügyfél-azonosítója, bérlői azonosító és jelszó. Kattintson a **bejelentkezési** gombra.

    > [!NOTE]
    > Az egyes emulátory systému a **bejelentkezési** gomb nem bármikor aktiválhatja ezt a lépést. Ha ez történik, állítsa le az alkalmazást, nyissa meg a _Source/VisualProvision/Pages/LoginPage.xaml_ fájlt és keresse meg a `Button` elem címkézett bejelentkezési GOMBOT, és a következő sor eltávolításához:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Ezután futtassa újra az alkalmazást.

    ![az alkalmazás képernyőjéről, az egyszerű szolgáltatás hitelesítő adatai a mezők](media/azure-logo-tutorial/app-credentials.png)

1. A következő képernyőn válassza ki a legördülő menüből (tartalmaznia kell az előfizetést, amelyhez hozzáféréssel rendelkezik az egyszerű szolgáltatást az összes) az Azure-előfizetés. Kattintson a **Folytatás** gombra.

    ![az alkalmazás képernyőjéről cél Azure-előfizetéshez tartozó legördülő mező](media/azure-logo-tutorial/app-az-subscription.png)

    Ezen a ponton az alkalmazás kérhet hozzáférést a kamerához és a fotótárhoz tárolási kívánt eszközöket. Fogadja el ezeket az engedélyeket.

1. Ezután az eszközön a kamera aktiválja. Fényképet is az Azure-szolgáltatás emblémák, betanított egyikét. A központi telepítési párbeszéd kell kérnie, hogy az új szolgáltatások régiók és -erőforrások csoport kijelölése (mint ha voltak üzembe őket az Azure Portalról). 

    ![a két tanulmány cutouts Azure logók nézetben smartphone kamera képernyő](media/azure-logo-tutorial/app-camera-capture.png)

    ![az üzembe helyezési régiók és -erőforrások a csoport bejegyzés mezőit képernyő](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha követte a lépéseket, az ebben a forgatókönyvben, és az alkalmazás üzembe helyezése az Azure-szolgáltatásokhoz a fiókjához használt, ügyeljen arra, hogy nyissa meg a [az Azure portal](https://ms.portal.azure.com/) Ha ezzel végzett, és megszakítja a szolgáltatások nem kívánja azt használni.

Is ha azt tervezi, a jövőben a Custom Vision hozzon létre saját objektum észlelési projektet, előfordulhat, hogy törölni kívánt embléma észlelési ebben az oktatóanyagban létrehozott projekt. Két projekt lehetővé teszi a Custom Vision, ingyenes próbaverziójára. Az a [Custom Vision webhely](https://customvision.ai), navigáljon a **projektek** , és válassza a Kuka alatt **saját új projekt**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállítása, és megismerkedett a teljes körű Xamarin.Forms-alkalmazás, amely a Custom Vision service mobil fényképek emblémák észleléséhez használja. Az ajánlott eljárást létrehozásához egy egyéni Látástechnológiai modellje ezt követően megtudhatja, hogy egy saját-alkalmazás létrehozásakor férhessenek hozzá hatékony és pontos.

> [!div class="nextstepaction"]
> [Tartalombesoroló teljesítményének hogyan](getting-started-improving-your-classifier.md)