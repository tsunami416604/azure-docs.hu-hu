---
title: Csoport hívása – hős minta
titleSuffix: An Azure Communication Services sample overview
description: Az Azure kommunikációs szolgáltatásokkal való meghívásának áttekintése, amely lehetővé teszi a fejlesztők számára a minta belső működésének megismerését.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947145"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Ismerkedés a csoport meghívása Hero-mintával

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

Az Azure kommunikációs szolgáltatások **csoportos hívása a Hero minta** azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg a webes ügyféloldali kódtárat csoportos hívási élmény létrehozásához.

Ebből a rövid útmutatóból megtudhatja, hogyan működik a minta a minta helyi gépen való futtatása előtt. Ezután üzembe helyezzük a mintát az Azure-ban a saját Azure kommunikációs szolgáltatások erőforrásaival.

> [!IMPORTANT]
> [A minta letöltése a GitHubról](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Áttekintés

A mintában ügyféloldali alkalmazás és kiszolgálóoldali alkalmazás is található. Az **ügyféloldali alkalmazás** egy reagáló/Redux webalkalmazás, amely a Microsoft Fluent felhasználói felületének keretrendszerét használja. Ez az alkalmazás olyan ASP.NET Core **kiszolgálóoldali alkalmazásnak** küld kérelmeket, amely segítséget nyújt az ügyféloldali alkalmazásnak az Azure-hoz való kapcsolódáshoz. 

A minta így néz ki:

:::image type="content" source="./media/calling/landing-page.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

Ha a "hívás indítása" gombra kattint, a webalkalmazás beolvas egy felhasználói hozzáférési jogkivonatot a kiszolgálóoldali alkalmazásból. Ezt a tokent ezután az ügyfélalkalmazás Azure kommunikációs szolgáltatásokhoz való összekapcsolására használják. A jogkivonat lekérése után a rendszer felszólítja a használni kívánt kamera és mikrofon megadására. Az eszközöket letilthatja/engedélyezheti a váltási vezérlőkkel:

:::image type="content" source="./media/calling/pre-call.png" alt-text="A minta alkalmazás előhívási képernyőjét ábrázoló képernyőfelvétel.":::

A megjelenítendő név és az eszközök konfigurálása után csatlakozhat a hívási munkamenethez. Most megjelenik a fő hívási vászon, ahol a legfontosabb hívási élmény él.

:::image type="content" source="./media/calling/main-app.png" alt-text="A minta alkalmazás fő képernyőjét ábrázoló képernyőfelvétel.":::

A fő hívó képernyő összetevői:

- **Media Gallery**: az a fő szakasz, ahol a résztvevők megjelennek. Ha a résztvevő kamerája engedélyezve van, a videó hírcsatornája itt jelenik meg. Minden résztvevő rendelkezik egy egyéni csempével, amely megjeleníti a megjelenítendő nevüket és a video streamet (ha van ilyen)
- **Fejléc**: ez az a hely, ahol az elsődleges hívási vezérlők a beállítások és a résztvevő oldali sáv váltására, a videó bekapcsolására és a kikapcsolására, a megosztási képernyőre és a hívás elhagyása.
- **Oldalsó sáv**: ebben az esetben a rendszer megjeleníti a résztvevők és a beállítások adatait, ha a fejlécben lévő vezérlők használatával váltanak ki. Az összetevőt a jobb felső sarokban található "X" használatával lehet elhagyni. A résztvevők oldali sáv megjeleníti a résztvevők listáját, valamint egy hivatkozást, amely további felhasználókat hívhat meg a csevegéshez. A beállítások oldal sáv lehetővé teszi a mikrofon és a kamera beállításainak konfigurálását.

Az alábbiakban további információkat talál az előfeltételekről, a minta beállításának lépéseiről és a részletes oktatóanyagokról, amelyek segítenek a különböző összetevőinek megismerésében.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért lásd: [fiók létrehozása ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 és újabb)](https://nodejs.org/en/download/)
- [Visual Studio (2019 vagy újabb)](https://visualstudio.microsoft.com/vs/)
- [.Net Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (Ügyeljen arra, hogy a Visual Studio-példánynak megfelelő verziót telepítse, 32 vs 64 bit)
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../quickstarts/create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **kapcsolódási karakterláncot** .

## <a name="locally-deploy-the-service--client-applications"></a>A szolgáltatás helyileg történő üzembe helyezése & ügyfélalkalmazások számára

A Group Calling Sample lényegében két alkalmazás: a ClientApp és a Service.NET alkalmazás.

Ha helyileg szeretnénk üzembe helyezni, mindkét alkalmazást el kell indítani. Amikor a Server alkalmazást meglátogatják a böngészőből, a helyileg telepített ClientApp fogja használni a felhasználói élményhez.

A mintát helyileg tesztelheti úgy, hogy több böngészős munkamenetet nyit meg a hívás URL-címével, amely több felhasználó hívását szimulálja.

### <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. Nyisson meg egy PowerShell-példányt, a Windows Terminalt, a parancssort vagy a megfelelőt, és navigáljon ahhoz a könyvtárhoz, amelyet a minta klónozására szeretne használni.
2. `git clone`
3. Lépjen a **hívás/ClientApp mappára** , és futtassa a parancsot. `npm run setup`
   1. Ha az 1. hibát látja, a kimenetben keresse meg az URL-címet, ahol engedélyezni kell az ügyfelet. (Az URL a következőképpen fog kinézni: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...` ) Miután meglátogatta az URL-címet egy böngészőben, másolja a parancsot a böngészőablakból, és futtassa.
   2. Futtassa `npm run setup-vsts-auth` újra a parancsot az előző lépés befejezése után.
4. Szerezze be a `Connection String` Azure Portal. A kapcsolati karakterláncokkal kapcsolatos további információkért lásd: [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
5. A kapcsolódási karakterlánc lekérése után adja hozzá a kapcsolódási karakterláncot a Service .NET mappában található **hívási/appsetting.js** fájlhoz. Adja meg a kapcsolatok sztringjét a (z) változóban: `ResourceConnectionString` .

### <a name="local-run"></a>Helyi Futtatás

1. Ugrás a hívó mappára
2. A megoldás megnyitása a `Calling.csproj` Visual Studióban
2. A `Calling` projekt futtatása *

* A böngésző ekkor megnyílik `localhost:5000` (ahol a csomópont telepíti az ügyfélalkalmazás alkalmazást). Az alkalmazás nem támogatott az Internet Explorerben.

#### <a name="troubleshooting"></a>Hibaelhárítás

- A megoldás nem épít; hibákat dob a NPM telepítése/készítése során.

   Próbálja meg törölni/újraépíteni a projekteket.

## <a name="publish-the-sample-to-azure"></a>A minta közzététele az Azure-ban

1. Kattintson a jobb gombbal a `Calling` projektre, és válassza a közzététel lehetőséget.
2. Hozzon létre egy új közzétételi profilt, és válassza ki az Azure-előfizetését.
3. A közzététel előtt adja hozzá a (z `Edit App Service Settings` ) és a (z), és adja meg a `ResourceConnectionString` (z) kulcsot, és adja meg a (appsettings.json) értéket a (z) értékként.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a hívó ügyféloldali kódtár használatával](../quickstarts/voice-video-calling/calling-client-samples.md)
- Tudnivalók az [ügyféloldali kódtár képességeinek meghívásáról](../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>További információ

- [Azure Communication Preview](https://github.com/Azure/communication-preview) – további információ a hívó web SDK-ról
- [Redux](https://redux.js.org/) – ügyféloldali állapot kezelése
- [FluentUI](https://developer.microsoft.com/fluentui#/) – Microsoft powered UI Library
- [Reagálás](https://reactjs.org/) – függvénytár a felhasználói felületek létrehozásához
- [ASP.net Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) -keretrendszer webalkalmazások létrehozásához
