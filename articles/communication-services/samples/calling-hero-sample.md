---
title: Csoportos hívás kiemelt mintája
titleSuffix: An Azure Communication Services sample overview
description: Az Azure kommunikációs szolgáltatásokkal való meghívásának áttekintése, amely lehetővé teszi a fejlesztők számára a minta belső működésének megismerését.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c872c2a2799eee3ae9e39653b0046b70ca5d7e8b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887535"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Ismerkedés a csoport meghívása Hero-mintával

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [Ez a minta a GitHubon érhető el.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Az Azure kommunikációs szolgáltatások **csoportos hívása a Hero minta** azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg a webes ügyféloldali kódtárat csoportos hívási élmény létrehozásához.

Ebből a rövid útmutatóból megtudhatja, hogyan működik a minta a minta helyi gépen való futtatása előtt. Ezután üzembe helyezzük a mintát az Azure-ban a saját Azure kommunikációs szolgáltatások erőforrásaival.

## <a name="overview"></a>Áttekintés

A mintában ügyféloldali alkalmazás és kiszolgálóoldali alkalmazás is található. Az **ügyféloldali alkalmazás** egy reagáló/Redux webalkalmazás, amely a Microsoft Fluent felhasználói felületének keretrendszerét használja. Ez az alkalmazás olyan ASP.NET Core **kiszolgálóoldali alkalmazásnak** küld kérelmeket, amely segítséget nyújt az ügyféloldali alkalmazásnak az Azure-hoz való kapcsolódáshoz. 

A minta így néz ki:

:::image type="content" source="./media/calling/landing-page.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

Ha a "hívás indítása" gombra kattint, a webalkalmazás beolvas egy felhasználói hozzáférési jogkivonatot a kiszolgálóoldali alkalmazásból. Ezt a tokent ezután az ügyfélalkalmazás Azure kommunikációs szolgáltatásokhoz való összekapcsolására használják. A jogkivonat lekérése után a rendszer felszólítja a használni kívánt kamera és mikrofon megadására. Az eszközöket letilthatja/engedélyezheti a váltási vezérlőkkel:

:::image type="content" source="./media/calling/pre-call.png" alt-text="A minta alkalmazás előhívási képernyőjét ábrázoló képernyőfelvétel.":::

A megjelenítendő név és az eszközök konfigurálása után csatlakozhat a hívási munkamenethez. Most megjelenik a fő hívási vászon, ahol a legfontosabb hívási élmény él.

:::image type="content" source="./media/calling/main-app.png" alt-text="A minta alkalmazás fő képernyőjét ábrázoló képernyőfelvétel.":::

A fő hívó képernyő összetevői:

1. **Media Gallery**: az a fő szakasz, ahol a résztvevők megjelennek. Ha a résztvevő kamerája engedélyezve van, a videó hírcsatornája itt jelenik meg. Minden résztvevő rendelkezik egy egyéni csempével, amely megjeleníti a megjelenítendő nevüket és a video streamet (ha van ilyen)
2. **Fejléc**: ez az a hely, ahol az elsődleges hívási vezérlők a beállítások és a résztvevő oldali sáv váltására, a videó bekapcsolására és a kikapcsolására, a megosztási képernyőre és a hívás elhagyása.
3. **Oldalsó sáv**: ebben az esetben a rendszer megjeleníti a résztvevők és a beállítások adatait, ha a fejlécben lévő vezérlők használatával váltanak ki. Az összetevőt a jobb felső sarokban található "X" használatával lehet elhagyni. A résztvevők oldali sáv megjeleníti a résztvevők listáját, valamint egy hivatkozást, amely további felhasználókat hívhat meg a csevegéshez. A beállítások oldal sáv lehetővé teszi a mikrofon és a kamera beállításainak konfigurálását.

Az alábbiakban további információkat talál a minta beállításához szükséges előfeltételekről és lépésekről.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért lásd: [fiók létrehozása ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 és újabb)](https://nodejs.org/en/download/)
- [Visual Studio (2019 vagy újabb)](https://visualstudio.microsoft.com/vs/)
- [.Net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Ügyeljen arra, hogy a Visual Studio-példánynak megfelelő verziót telepítse, 32 vs 64 bit)
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../quickstarts/create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **kapcsolódási karakterláncot** .

## <a name="locally-deploy-the-service--client-applications"></a>A szolgáltatás helyileg történő üzembe helyezése & ügyfélalkalmazások számára

A Group Calling Sample lényegében két alkalmazás: a ClientApp és a Service.NET alkalmazás.

Ha helyileg szeretnénk üzembe helyezni, mindkét alkalmazást el kell indítani. Amikor a Server alkalmazást meglátogatják a böngészőből, a helyileg telepített ClientApp fogja használni a felhasználói élményhez.

A mintát helyileg tesztelheti úgy, hogy több böngészős munkamenetet nyit meg a hívás URL-címével, amely több felhasználó hívását szimulálja.

## <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. Nyisson meg egy PowerShell-példányt, a Windows Terminalt, a parancssort vagy a megfelelőt, és navigáljon ahhoz a könyvtárhoz, amelyet a minta klónozására szeretne használni.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Szerezze be a `Connection String` Azure Portal. A kapcsolati karakterláncokkal kapcsolatos további információkért lásd: [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
4. A `Connection String` hozzáadása után adja hozzá a kapcsolódási karakterláncot a Service .net mappában található **hívási/appsetting.js** fájlhoz. Adja meg a kapcsolatok sztringjét a (z) változóban: `ResourceConnectionString` .

### <a name="local-run"></a>Helyi Futtatás

1. A Visual Studióban nyissa meg a hívó mappát és a megoldás megnyitása lehetőséget. `Calling.csproj`
2. `Calling`Projekt futtatása. A böngésző ekkor megnyílik a localhost címen: 5001

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

>[!div class="nextstepaction"] 
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-web-calling-hero)

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a hívó ügyféloldali kódtár használatával](../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
- [Redux](https://redux.js.org/) – ügyféloldali állapot kezelése
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft powered UI Library
- [Reagálás](https://reactjs.org/) – függvénytár a felhasználói felületek létrehozásához
- [ASP.net Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) -keretrendszer webalkalmazások létrehozásához