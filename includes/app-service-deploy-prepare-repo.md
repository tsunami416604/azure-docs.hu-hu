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
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850810"
---
## <a name="prepare-your-repository"></a>A tárház előkészítése

Ahhoz, hogy automatikus buildek az App Service Kudu build kiszolgálóról, győződjön meg arról, hogy az adattár gyökérkönyvtárában a megfelelő fájlokat a projektben.

| Futtatókörnyezet | Legfelső szintű directory fájlok |
|-|-|
| ASP.NET (csak Windows) | _*.sln_, _*.csproj_, vagy _default.aspx_ |
| ASP.NET-mag | _*.sln_ vagy _*.csproj_ |
| PHP | _index.php_ |
| Ruby (csak Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, vagy _package.json_ egy indítási parancsfájl |
| Python (csak Windows) | _\*.PY_, _requirements.txt_, vagy _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.HTML_, _index.html_, vagy  _Iisstart.htm_ |
| WebJobs | _\<job_name > / futtatásához. \<bővítmény >_ alatt _App\_adatok/feladatok/folyamatos_ (a folyamatos webjobs-feladatok) vagy _App\_adatok/feladatok/indított_ (az akkor kiváltott Webjobs-feladatok). További információkért lásd: [Kudu WebJobs-dokumentáció](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Functions | Lásd: [folyamatos üzembe helyezés az Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

A telepítés testreszabásához közé tartozik egy _.deployment_ fájlt az adattár gyökérkönyvtárában. További információkért lásd: [telepítéseinek testreszabásához](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) és [egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Ha a Visual Studio fejlesztése, [Visual Studio egy tárház létrehozása az Ön](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). A projekt azonnal készen áll a Git használatával telepíthetők.
>
>

