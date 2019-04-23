---
title: Az Azure Container Registry tartalom formátumok
description: További információ a támogatott tartalom formátumok az Azure Container Registryben.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997070"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Az Azure Container Registry támogatott tartalom formátumban

Az Azure Container Registry egy privát tárház használatával kezelheti a következő tartalom formátumokba. 

## <a name="docker-compatible-container-images"></a>A tárolólemezképeket a docker-kompatibilis

A következő Docker-tároló lemezképet formátumok támogatják:

* [Docker rendszerkép jegyzékfájl V2-séma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-rendszerkép Manifest V2, séma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -jegyzékfájl listákat, amelyek lehetővé teszik a beállításjegyzékek egy egyetlen "image: tag" hivatkozás alatt többplatformos képek tartalmaz

## <a name="oci-images"></a>OCI képek

Az Azure Container Registry is támogatja a lemezképek, amelyek megfelelnek a [Open Container kezdeményezés (OCI) rendszerkép fájlformátum specifikációjának](https://github.com/opencontainers/image-spec/blob/master/spec.md). Csomagolási formátumnak tartalmaznia kell [Singularity lemezkép formátumú (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Helm-diagramok

Az Azure Container Registry üzemeltethet tárolóhelyekkel [Helm-diagramok](https://helm.sh/), a használt gyorsan üzembe helyezheti és kezelheti a Kubernetes alkalmazások csomagolási formátumot. [Helm-ügyfél](https://docs.helm.sh/using_helm/#installing-helm) 2.11.0 verzió vagy újabb támogatott.

## <a name="next-steps"></a>További lépések

* Lásd: hogyan [lekéréses és küldéses](container-registry-get-started-docker-cli.md) rendszerképek az Azure Container Registryvel.

* Használat [ACR feladatok](container-registry-tasks-overview.md) összeállításához és teszteléséhez a tárolórendszerképeket. 

* Használja a [Moby BuildKit](https://github.com/moby/buildkit) felépítéséhez és becsomagolásához OCI formátumú tárolók.

* Állítsa be a [Helm-adattárhoz](container-registry-helm-repos.md) az Azure Container Registryben tárolt. 


