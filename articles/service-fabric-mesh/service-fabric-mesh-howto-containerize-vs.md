---
title: A Service Fabric-háló egy meglévő .NET-alkalmazás tárolóalapúvá |} A Microsoft Docs
description: Háló-támogatás hozzáadása egy meglévő .NET-alkalmazás
services: service-fabric-mesh
keywords: service fabric háló tárolóba
author: tylermsft
ms.author: twhitney
ms.date: 11/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 6f71f45d435b6be3358f79d8b6e72e4636d92ab6
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893503"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>A Service Fabric-háló egy meglévő .NET-alkalmazás tárolóalapúvá

Ez a cikk bemutatja, hogyan Service Fabric-háló tárolótámogatás vezénylési hozzáadása egy meglévő .NET-alkalmazást.

A Visual Studio 2017 a teljes .NET-keretrendszert használó ASP.NET és a konzol projektek bontás támogatást adhat hozzá.

> [!NOTE]
> .NET **core** projektek jelenleg nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [a fejlesztési környezet beállítása](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Ebbe beletartozik a Service Fabric-futtatókörnyezet, SDK-t, a Docker, a Visual Studio 2017 telepítése és a egy helyi fürtöt hoz létre.

## <a name="open-an-existing-net-app"></a>Nyissa meg a meglévő .NET-alkalmazások

Nyissa meg a vezénylési tárolótámogatás hozzáadni kívánt alkalmazást.

Ha szeretné próbálja példaként, használhatja a [eShop](https://github.com/MikkelHegn/ContainersSFLab) kódmintát. Ez a cikk többi része feltételezi, hogy használjuk-e erre a projektre, bár ezeket a lépéseket a saját project is alkalmazhat.

Egy példánya a **eShop** projekt:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Amely rendelkezik a letöltést követően a Visual Studio 2017 nyílt **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adja hozzá a tároló támogatása
 
Vezénylési tárolótámogatás hozzáadása egy meglévő ASP.NET- vagy konzol projekt használatával a Service Fabric-háló eszközöket az alábbiak szerint:

A Visual Studio megoldáskezelőben kattintson a jobb gombbal a projekt neve (a példában **eShopLegacyWebForms**) majd **Hozzáadás** > **tároló az Orchestrator-támogatás** .
A **adja hozzá az Orchestrator Tárolótámogatás** párbeszédpanel jelenik meg.

![A Visual Studio hozzáadása tároló orchestrator párbeszédpanel](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Válasszon **Service Fabric-háló** a legördülő listából és kattintson a **OK**.

Az eszköz ezután ellenőrzi, hogy a Docker telepítve van, egy docker-fájlban ad hozzá a projekthez és lekéri a docker-rendszerkép a projekthez.  
Egy Service Fabric-háló-projektet a megoldáshoz kerül. A háló tartalmaz közzétételi profilok és konfigurációs fájlok. A projekt neve megegyezik a projekt nevére, például az utolsó összefűzött alkalmazással **eShopLegacyWebFormsApplication**. 

Az új háló projekt látni fogja a két, célszerű tisztában lennie a mappák:
- **Alkalmazás-erőforrások** további háló-erőforrások, például a hálózati leíró YAML-fájlokat tartalmazó.
- **Szolgáltatás-erőforrások** amely tartalmaz egy service.yaml fájlt, amely azt ismerteti, hogyan kell futnia az alkalmazás telepítésekor.

Miután vezénylési tárolótámogatás van adva az alkalmazáshoz, nyomja le **F5** hibakeresése a helyi Service Fabric-háló fürtön lévő .NET-alkalmazásait. A Service Fabric-háló fürtön futó eShop ASP.NET-alkalmazás a következő: 

![eShop alkalmazás](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Az alkalmazás közzététele az Azure Service Fabric-háló most.

## <a name="next-steps"></a>További lépések

Alkalmazások közzététele a Service Fabric-háló való használatáról: [oktatóanyag üzembe helyezése egy Service Fabric-háló-alkalmazás](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)