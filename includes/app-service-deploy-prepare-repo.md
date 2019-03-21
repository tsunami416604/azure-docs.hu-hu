---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df987d1e13cb5330842fbab41dae96b24b581ddb
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227269"
---
## <a name="prepare-your-repository"></a>A tárház előkészítése

A Azure App Service Kudu-buildelési kiszolgáló automatikus buildek lekéréséhez ellenőrizze, hogy az adattár gyökérkönyvtárában van-e a megfelelő fájlokat a projekt.

| Futtatókörnyezet | Legfelső szintű könyvtár fájlok |
|-|-|
| ASP.NET (csak Windows) | _*.sln_, _*.csproj_, vagy _default.aspx_ |
| ASP.NET-mag | _*.sln_ vagy _*.csproj_ |
| PHP | _index.php_ |
| Ruby (csak Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, vagy _package.json_ egy indítási szkriptet a |
| Python (csak Windows) | _\*.PY_, _requirements.txt_, vagy _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, vagy  _Iisstart.htm_ |
| WebJobs | _\<job_name > / run. \<bővítmény >_ alatt _alkalmazás\_Data/feladatok/folyamatos_ (a folyamatos webjobs-feladatok) vagy _alkalmazás\_Data/feladatok/triggered_ (az aktivált Webjobs-feladatok). További információkért lásd: [Kudu WebJobs-dokumentáció](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Lásd: [Azure Functions – folyamatos üzembe helyezés](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Az üzembe helyezés testreszabásához, például egy _.deployment_ fájlt az adattár gyökérkönyvtárában. További információkért lásd: [testre szabhatja a központi telepítések](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) és [egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Ha a Visual Studio fejlesztése, [Visual Studio-tárház létrehozásához,](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). A projekt azonnal készen áll a Git használatával üzembe helyezhetők.
>
>

