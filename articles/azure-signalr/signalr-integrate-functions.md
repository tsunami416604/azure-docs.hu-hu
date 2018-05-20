---
title: Oktatóanyag az Azure Functions Azure SignalR Service szolgáltatással való integrálásához | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure Functions az Azure SignalR Service szolgáltatással
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Oktatóanyag: Az Azure Functions integrálása az Azure SignalR Service szolgáltatással

Ez az oktatóanyag a korábbi oktatóanyagokban létrehozott csevegőszoba-alkalmazásra épít. Ha még nem végezte el a [Csevegőszoba létrehozása SignalR Service szolgáltatással](signalr-quickstart-dotnet-core.md) és az [Azure SignalR Service – hitelesítés](./signalr-authenticate-oauth.md) gyakorlatokat, először végezze el őket. 

A valós idejű alkalmazások esetén gyakori forgatókönyv, hogy a tartalomfrissítések egy olyan kiszolgálótól származnak, amely webes ügyfeleken lesz közzétéve. Az [Azure Functions](../azure-functions/functions-overview.md) kiválóan alkalmas az ilyen tartalomfrissítések létrehozására. Az Azure Functions használatának egyik legfontosabb előnye, hogy igény szerint futtathatja a kódot, és nem kell egy egész alkalmazás architektúrájával vagy a futtató infrastruktúrával bajlódnia. Emellett csak azért az időért fizet, amikor a kód ténylegesen fut.  

Normál esetben ez a forgatókönyv problémát jelentene a SignalR használatakor, mivel a SignalR kapcsolatot próbál fenntartani az ügyfél és a kiszolgáló között a tartalomfrissítések küldéséhez. Mivel a kód csak igény szerint fut, nem lehet fenntartani a kapcsolatot. Az Azure SignalR Service azonban támogatni tudja ezt a forgatókönyvet, mivel futásidőben kezeli a kapcsolatokat.

Az oktatóanyag során üzeneteket fog létrehozni az Azure Functions szolgáltatással, amelyhez minden perc elején egy időzítő függvényt fog használni. A függvény közzé fogja tenni az üzenteket a korábbi oktatóanyagokban létrehozott csevegőszoba összes ügyfele számára. Az időzítő függvényekkel kapcsolatos további információkért lásd az [időzítő függvény](../azure-functions/functions-create-scheduled-function.md) leírását.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Csevegőalkalmazás kiszolgálói üzenetekkel](./media/signalr-integrate-functions/signalr-functions-complete.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új időzítő függvény létrehozása az Azure Functions szolgáltatással, az Azure CLI használatával.
> * Az időzítő függvény konfigurálása a helyi Git-adattár üzemelő példánya számára.
> * Az időzítő csatlakoztatása a SignalR Service szolgáltatáshoz a frissítések percenkénti küldése érdekében

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbi előfeltételekkel kell rendelkeznie:

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Konfigurált Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Töltse le vagy klónozza az [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub-adattárat.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A függvények végrehajtási környezetének meghatározásához létre kell hoznia egy függvényalkalmazást. A függvényalkalmazás azt is lehetővé teszi, hogy logikai egységbe csoportosítson több függvényt az egyszerűbb felügyelet, telepítés és erőforrás-megosztás érdekében. További információkért lásd: [Az első függvény létrehozása az Azure CLI használatával](../azure-functions/functions-create-first-azure-function-azure-cli.md).

Ebben a szakaszban az Azure Cloud Shellt fogja használni egy helyi Git-adattárból való üzembe helyezésre konfigurált új Azure-függvényalkalmazás létrehozásához. 

A függvényalkalmazás-erőforrások létrehozásakor ugyanabban az erőforráscsoportban hozza őket létre, mint amelyet a korábbi oktatóanyagokban használt. Ez a módszer megkönnyíti az oktatóanyag-erőforrások kezelését.

Másolja be az alábbi szkriptet a szövegszerkesztőbe, és cserélje le a változóparaméterek értékeit a saját erőforrásainak megfelelő értékekre. Másolja és illessze be a frissített szkriptet az Azure Cloud Shellbe, majd nyomja le az **Enter** billentyűt egy tárfiók és egy függvényalkalmazás létrehozásához.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Paraméter | Leírás |
| -------------------- | --------------- |
| ResourceGroupName | Ezt az erőforráscsoport-nevet a korábbi oktatóanyagokban javasoltuk. Az oktatóanyag-erőforrásokat érdemes egy csoportban tárolni. Használja ugyanazt az erőforráscsoportot, mint amelyet a korábbi oktatóanyagokban használt. | 
| location | Frissítse ezt a változót ugyanarra a helyre, mint amelyet a korábbi oktatóanyagok során az erőforráscsoport létrehozásához használt. | 
| functionappName | Frissítse ezt a változót az új függvényalkalmazás egyedi nevére. Például: signalrfunctionapp22665120. | 
| storageAccountName | Adjon nevet az új tárfióknak, amely a függvényalkalmazás kódját és beállításait tárolja. | 



## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

Ebben a szakaszban konfigurálni fogja a függvényalkalmazást az Azure SignalR Service-erőforrás kapcsolati karakterláncát tartalmazó alkalmazásbeállítással. A függvénykód ezt a beállítást fogja használni a csevegőszobához való csatlakozáshoz és az üzenetek közzétételéhez. A függvényalkalmazást továbbá a helyi Git-adattárból való üzembe helyezéshez is konfigurálni fogja.

Másolja az alábbi szkriptet, és cserélje le a paraméterek értékeit. Illessze be a frissített szkriptet az Azure Cloud Shellbe, majd nyomja le az **Enter** billentyűt.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Paraméter | Leírás |
| -------------------- | --------------- |
| ResourceGroupName | Ezt az erőforráscsoport-nevet a korábbi oktatóanyagokban javasoltuk. Az oktatóanyag-erőforrásokat érdemes egy csoportban tárolni. Használja ugyanazt az erőforráscsoportot, mint amelyet a korábbi oktatóanyagokban használt. | 
| functionappName | Frissítse ezt a változót az új függvényalkalmazás egyedi nevére. Például: signalrfunctionapp22665120. | 
| connstring | Adjon meg egy kapcsolati karakterláncot a SignalR Service-erőforrás számára. Ezt a kapcsolati karakterláncot a SignalR Service-erőforrás oldaláról is lekérheti az Azure Portalon, ha a **BEÁLLÍTÁSOK** alatt a **Kulcsok** elemre kattint. | 



Jegyezze fel a Git üzemelő példány utolsó parancs által visszaadott URL-címét. Ezt az URL-címet fogja használni a függvénykód üzembe helyezéséhez.


## <a name="the-timer-function"></a>Az időzítő függvény

Az időzítő függvény mintája a letöltés */samples/Timer* mappájában vagy az [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) GitHub-adattárjának klónjában található. Az időzítő függvény kódja a *TimerFunction.cs* fájlban található. A kód az alapértelmezett konfigurációs kulccsal (*Azure:SignalR:ConnectionString*) együtt tárolt kapcsolati karakterláncot használja ahhoz, hogy létrehozzon egy proxyt a központhoz. Mivel a függvénykód a kiszolgálóoldalon fut, nem lehet elvárni tőle, hogy normál ügyfélként hitelesítsen. A kód megbízható a kapcsolati karakterlánc használatához. Ha ezt a központi proxyt használja, a függvénykód meg tudja hívni a központban meghatározott metódusok bármelyikét. A kód meghívja a `BroadcastMessage` metódust, hogy tegyen közzé egy, az aktuális időt tartalmazó üzenetet, amikor az eseményindító aktiválódik.

A függvénykód eseményindítója egy *timerTrigger*, amely a *TimerFunction/function.json* kötéseiben van meghatározva. Ez egy [CRON-kifejezést](https://wikipedia.org/wiki/Cron#CRON_expression) tartalmaz, amely beállítja, hogy az időzített eseményindító minden perc elején aktiválódjon.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Az időzítő függvény buildelése

Használja a [.NET Core parancssori felületet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) a következő lépésekben a függvény buildeléséhez és üzembe helyezésre való előkészítéséhez:

1. Keresse meg a letöltés */samples/Timer* alkönyvtárát, vagy az [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub-adattárjának klónját.

2. Állítsa vissza a NuGet-csomagokat a következő paranccsal:

        dotnet restore

3. Buildelje a *Timer* függvényalkalmazást a következő paranccsal:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>A helyi Git-adattár létrehozása és üzembe helyezése

1. Egy Git rendszerhéjban lépjen a */samples/Timer/bin/Release/net461* buildelési alkönyvtárra.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Inicializálja a könyvtárat új Git-adattárként a következő paranccsal:

        git init

3. Adjon hozzá egy új véglegesítést a build könyvtárban lévő fájlokhoz.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Adjon hozzá egy távoli végpontot a Git üzemelő példány függvényalkalmazás konfigurálásakor feljegyzett URL-címéhez:

        git remote add Azure <enter your Git deployment URL>

5. A függvényalkalmazás üzembe helyezése

        git push Azure master

   A kód üzembe helyezését követően az időzítő percenként aktiválódik, hogy végrehajtsa a kódot.

## <a name="test-the-chat-app"></a>A csevegőalkalmazás tesztelése

Nyissa meg a csevegőalkalmazást. Az imént létrehozott időzítő függvény minden perc elején jelenteni fogja az időt.

![Csevegőalkalmazás kiszolgálói üzenetekkel](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné az alkalmazás tesztelését, megtarthatja a létrehozott erőforrásokat.

Ha viszont befejezte a mintaalkalmazás használatát, a költségek elkerülése érdekében törölheti az Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
> 
> 

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *SignalRTestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** lehetőségre.

   
![Törlés](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.
   
A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az Azure Function szolgáltatással kialakított integrációt, amely az ügyfeleknek szánt frissítések Azure Function eseményindítók alapján történő küldését végzi. Ha többet szeretne megtudni az Azure SignalR Server használatáról, lépjen tovább a SignalR Service Azure CLI-mintáira.

> [!div class="nextstepaction"]
> [Azure SignalR CLI-minták](./signalr-cli-samples.md)



