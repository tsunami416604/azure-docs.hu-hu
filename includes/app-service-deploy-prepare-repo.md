---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836795"
---
## <a name="prepare-your-repository"></a>A tárház előkészítése

Ha automatikus buildeket szeretne beszerezni az Azure App Service Kudu buildkiszolgálóról, győződjön meg arról, hogy a tárház gyökér a megfelelő fájlokkal rendelkezik a projektben.

| Futtatókörnyezet | Gyökérkönyvtár-fájlok |
|-|-|
| ASP.NET (csak Windows) | _*.sln_, _*.csproj_vagy _default.aspx_ |
| ASP.NET-mag | _*.sln_ vagy _*.csproj_ |
| PHP | _Index.php_ |
| Ruby (csak Linux) | _Gemfile (Gemfile)_ |
| Node.js | _server.js_, _app.js_vagy _package.json_ kezdő parancsfájllal |
| Python | .py , _requirements.txt_vagy _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_vagy _iisstart.htm_ |
| WebJobs | _\<job_name>/futtatás. bővítmény \<>_ az _\_Alkalmazásadatok/feladatok/folyamatos_ folyamatos folyamatos webfeladatok esetén, illetve az aktivált WebJobs _alkalmazásadatok/feladatok/aktivált\_alkalmazásadatok/feladatok_ alatt. További információt a [Kudu WebJobs dokumentációjában](https://github.com/projectkudu/kudu/wiki/WebJobs)talál. |
| Functions | Lásd: [Folyamatos üzembe helyezés az Azure Functions hez.](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment) |

A központi telepítés testreszabásához vegyen fel egy *.deployment* fájlt a tárház gyökérfájljába. További információt a [Központi telepítések](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) és az Egyéni központi telepítési parancsfájl testreszabása [című témakörben talál.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)

> [!NOTE]
> Ha a Visual Studióban fejleszt, hagyja, hogy [a Visual Studio hozzon létre egy adattárat.](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) A projekt azonnal készen áll a Git használatával történő üzembe helyezésre.
>

