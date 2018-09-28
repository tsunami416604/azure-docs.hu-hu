---
title: Rövid útmutató – az Azure SignalR szolgáltatás REST API-ja | Microsoft Docs
description: Rövid útmutató az Azure SignalR szolgáltatás REST API-jának használatához.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972759"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Rövid útmutató: Valós idejű üzenetek szétküldése konzolalkalmazásból

Az Azure SignalR szolgáltatás [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) biztosításával támogatja a kiszolgáló és ügyfél közötti kommunikációs forgatókönyveket, például a szétküldést. Bármelyik programozási nyelvet választhatja, amely képes REST API-t hívni. Küldhet üzenetet minden csatlakoztatott ügyfélnek, név szerint egy adott ügyfélnek, vagy ügyfelek egy csoportjának.

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet üzeneteket parancssori alkalmazásból a csatlakoztatott ügyfélalkalmazásoknak C#-ban.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.
* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Az Ön által választott szöveg- vagy kódszerkesztő.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kód előkészítésére. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples.git), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Ez a minta egy konzolalkalmazás, amely az Azure SignalR szolgáltatás használatát mutatja be. Kétféle üzemmódot biztosít:

- Kiszolgáló üzemmód: egyszerű parancsok használatával hívja meg az Azure SignalR szolgáltatás REST API-ját.
- Ügyfél üzemmód: csatlakozik az Azure SignalR szolgáltatáshoz, és üzeneteket fogad a kiszolgálótól.

Azt is megtalálhatja, hogyan hozhat létre hozzáférési jogkivonatot az Azure SignalR szolgáltatással való hitelesítéshez.

### <a name="build-the-executable-file"></a>A végrehajtható fájl létrehozása

A példához a MacOS osx.10.13-x64 rendszert használjuk. A más platformokon való létrehozáshoz tekintse meg a [referenciákat](https://docs.microsoft.com/dotnet/core/rid-catalog).
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Indítson el egy ügyfelet

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Indítson el egy kiszolgálót

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Futtassa a mintát közzététel nélkül

A következő parancsot is futtathatja kiszolgálók vagy ügyfelek elindításához

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Használjon titkos felhasználói titkos kulcsokat a Kapcsolati sztring megadásához

A `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` futtatását a minta gyökérkönyvtárában végezheti. Ezután már nem lesz szüksége a `-c "<ConnectionString>"` lehetőségre.

## <a name="usage"></a>Használat

Ha a kiszolgáló elindult, használja a parancsot az üzenetküldéshez

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Több ügyfelet is indíthat különböző ügyfélnevekkel.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
