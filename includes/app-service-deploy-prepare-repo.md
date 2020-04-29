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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836795"
---
## <a name="prepare-your-repository"></a>A tárház előkészítése

Az Azure App Service kudu Build Serverről származó automatikus buildek lekéréséhez győződjön meg arról, hogy a tárház gyökerében a megfelelő fájlok vannak a projektben.

| Futtatókörnyezet | Gyökérkönyvtár fájljai |
|-|-|
| ASP.NET (csak Windows) | _*. SLN_, _*. csproj_vagy _default. aspx_ |
| ASP.NET-mag | _*. SLN_ vagy _*. csproj_ |
| PHP | _index. php_ |
| Ruby (csak Linux) | _Gemfile_ |
| Node.js | _Server. js_, _app. js_vagy _Package. JSON_ egy indítási parancsfájllal |
| Python | . a., a _követelmények. txt_vagy a _Runtime. txt fájl_ _ \*_ |
| HTML | _alapértelmezett. htm_, _alapértelmezett. html_, _default. asp_, _index. htm_, _index. html_vagy _Iisstart. htm_ |
| WebJobs | _\<job_name>/Run. bővítmény \<>_ az _\_alkalmazásadatok/feladatok/folyamatos_ a folyamatos webjobs-feladatok, vagy az _\_alkalmazásadatok/feladatok_ , illetve az aktivált webjobs-feladatok esetében. További információ: [kudu webjobs-dokumentáció](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Lásd: [Azure functions folyamatos üzembe helyezése](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

A központi telepítés testreszabásához vegyen fel egy *. Deployment* fájlt az adattár gyökerébe. További információ: a [központi telepítések](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) és az [egyéni telepítési parancsfájlok](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)testreszabása.

> [!NOTE]
> Ha a Visual Studióban fejleszti a fejlesztést, [a Visual Studio létrehoz egy tárházat](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). A projekt azonnal készen áll a git használatával történő üzembe helyezésre.
>

