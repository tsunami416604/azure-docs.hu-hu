---
title: ACR-feladatminták
description: Minta Azure Container Registry Feladatok (ACR-feladatok) létrehozásához, futtatásához és patch tárolórendszerképek
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456082"
---
# <a name="acr-tasks-samples"></a>ACR-feladatok mintái

Ez a cikk `task.yaml` a példafájlokra és a társított Docker-fájlokra mutat több [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) forgatókönyvekhez. 

További példák: az [Azure-minták][task-examples] tártára.

## <a name="scenarios"></a>Forgatókönyvek

* **YAML lemezkép** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Docker-fájl](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) létrehozása

* **YAML tároló** - [futtatása](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Build and push image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) lemezkép létrehozása és leküldése

* **Build and run image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml)lemezkép létrehozása és futtatása , [Docker-fájl](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Több kép** -  létrehozása és leküldése[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Képek létrehozása és tesztelése párhuzamos** -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml) [Dockerfile fájlban](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Képek létrehozása és leküldése több** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>További lépések

További információ az ACR-feladatokról:

* [Többlépéses feladatok](container-registry-tasks-multi-step.md) – ACR-feladatalapú munkafolyamatok tárolóképek létrehozásához, teszteléséhez és javításához a felhőben.
* [Feladathivatkozás](container-registry-tasks-reference-yaml.md) – Tevékenységlépés-típusok, tulajdonságaik és használatuk.
* [Cmd repo](https://github.com/AzureCR/cmd) – tárolók gyűjteménye az ACR-feladatok parancsaiként.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
