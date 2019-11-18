---
title: Azure Container Registry feladatok mintái
description: Minta Azure Container Registry feladatok (ACR-feladatok) a tároló-lemezképek létrehozásához, futtatásához és javításához
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152852"
---
# <a name="acr-tasks-samples"></a>ACR-feladatok mintái

Ez a cikk több [Azure Container Registry feladathoz](container-registry-tasks-overview.md) (ACR-feladatok) kapcsolódó forgatókönyvek `task.yaml` fájlokra és társított Dockerfiles mutató hivatkozásokat tartalmaz. 

További példákért tekintse meg az [Azure Samples][task-examples] tárházat.

## <a name="scenarios"></a>Alkalmazási helyzetek

* **Rendszerkép létrehozása** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tároló** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) futtatása

* **Rendszerkép létrehozása és leküldése** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Rendszerkép létrehozása és futtatása** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Több rendszerkép kiépítése és leküldése** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Lemezképek létrehozása és tesztelése párhuzamosan** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Képek létrehozása és leküldése több** beállításjegyzékbe - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Docker](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>További lépések

További információ az ACR-feladatokról:

* [Többlépéses feladatok](container-registry-tasks-multi-step.md) – az ACR-alapú munkafolyamatok a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához.
* [Feladat leírása](container-registry-tasks-reference-yaml.md) – a feladat lépésének típusai, tulajdonságai és használata.
* [Cmd](https://github.com/AzureCR/cmd) -tárház – a tárolók gyűjteménye az ACR-feladatok parancsaiként.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
