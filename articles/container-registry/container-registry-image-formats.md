---
title: Azure Container Registry tartalom formátuma
description: További információ a Azure Container Registry támogatott tartalmi formátumáról.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310677"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry támogatott tartalom-formátumok

A következő tartalom-formátumok egyikének kezeléséhez használjon Azure Container Registry saját tárházát. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibilis tároló lemezképei

A következő Docker-tároló képformátumai támogatottak:

* [Docker-rendszerkép jegyzékfájlja v2, 1. séma](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-rendszerkép jegyzékfájl v2, 2. séma](https://docs.docker.com/registry/spec/manifest-v2-2/) – olyan jegyzékeket tartalmaz, amelyek lehetővé teszik, hogy a beállításjegyzékek többplatformos rendszerképeket tároljanak egyetlen "Image: tag" hivatkozással

## <a name="oci-images"></a>OCI-lemezképek

A Azure Container Registry olyan képeket is támogat, amelyek megfelelnek a [nyílt tároló kezdeményezés (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md)képformátumának specifikációjának. A csomagolási formátumok közé tartozik a [szingularitás képformátuma (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Helm-diagramok

A Azure Container Registry képes a Helm- [diagramok](https://helm.sh/), a Kubernetes alkalmazások gyors kezelésére és üzembe helyezésére használt csomagolási formátum tárolására. A [Helm ügyfélprogram](https://docs.helm.sh/using_helm/#installing-helm) 2.11.0 vagy újabb verziója támogatott.

## <a name="next-steps"></a>További lépések

* Lásd: lemezképek [leküldése és](container-registry-get-started-docker-cli.md) lekérése Azure Container Registry használatával.

* Az [ACR-feladatok](container-registry-tasks-overview.md) használatával készíthet és tesztelheti a tárolók lemezképeit. 

* A [Moby BUILDKIT](https://github.com/moby/buildkit) OCI formátumban hozhat létre és csomagolhat tárolókat.

* Hozzon létre egy Azure Container Registry-ben üzemeltetett [Helm](container-registry-helm-repos.md) -tárházat. 


