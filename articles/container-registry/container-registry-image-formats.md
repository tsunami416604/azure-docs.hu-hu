---
title: Az Azure Container Registry tartalom formátumok
description: További információ a támogatott tartalom formátumok az Azure Container Registryben.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634714"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Az Azure Container Registry támogatott tartalom formátumban

Az Azure Container Registry egy privát tárház használatával kezelheti a következő tartalom formátumokba. 

## <a name="docker-compatible-container-images"></a>A tárolólemezképeket a docker-kompatibilis

* [Docker rendszerkép jegyzékfájl V2-séma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-rendszerkép Manifest V2, séma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -jegyzékfájl listákat, amelyek lehetővé teszik a beállításjegyzékek egy egyetlen "image: tag" hivatkozás alatt többplatformos képek tartalmaz

* [Nyissa meg a tároló (OCI) kezdeményezés kép fájlformátum specifikációjának](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Helm-diagramok

Az Azure Container Registry is üzemeltethetnek tárolóhelyekkel [Helm-diagramok](https://helm.sh/), a használt gyorsan üzembe helyezheti és kezelheti a Kubernetes alkalmazások csomagolási formátumot. [Helm-ügyfél](https://docs.helm.sh/using_helm/#installing-helm) 2.11.0 verzió vagy újabb támogatott.

## <a name="next-steps"></a>További lépések

* Lásd: hogyan [lekéréses és küldéses](container-registry-get-started-docker-cli.md) rendszerképek az Azure Container Registryvel.

* Használat [ACR feladatok](container-registry-tasks-overview.md) összeállításához és teszteléséhez a tárolórendszerképeket. 

* Használja a [Moby BuildKit](https://github.com/moby/buildkit) felépítéséhez és becsomagolásához OCI formátumú tárolók.

* Állítsa be a [Helm-adattárhoz](container-registry-helm-repos.md) az Azure Container Registryben tárolt. 


