---
title: Támogatott tartalomformátumok
description: Ismerje meg az Azure Container Registry által támogatott tartalomformátumokat, beleértve a Docker-kompatibilis tárolórendszerképeket, helm-diagramokat, OCI-rendszerképeket és OCI-összetevőket.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247006"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Az Azure Container Registry által támogatott tartalomformátumok

Az Azure Container Registry privát tárházhasználatával kezelheti az alábbi tartalomformátumok egyikét. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibilis tárolórendszerképek

A következő Docker-tárolórendszerképek támogatottak:

* [Docker-rendszerjegyzék-jegyzék V2, 1.](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Séma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - tartalmazza a manifest listákat, amelyek lehetővé teszik a többplatformos lemezképek tárolását egyetlen "image:tag" hivatkozás alatt

## <a name="oci-images"></a>OCI képek

Az Azure Container Registry támogatja az open [container initiative (OCI) image format specification szolgáltatásnak](https://github.com/opencontainers/image-spec/blob/master/spec.md)megfelelő lemezképeket. A csomagolási formátumok közé tartozik [a Szingularitás képformátum (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI-leletek

Az Azure Container Registry támogatja az [OCI distribution specification,](https://github.com/opencontainers/distribution-spec)a szállító-semleges, felhő-független spec tárolására, megosztására, biztonságos és üzembe helyezési tárolórendszerképek és egyéb tartalomtípusok (összetevők). A specifikáció lehetővé teszi, hogy a rendszerleíró adatbázis a tárolórendszerképek mellett számos műtermék tárolását is lehetővé tegye. A műterméknek megfelelő eszközhasználatot használ a műtermékek leküldése és lekérése. Például lásd: [OCI-összetevő leküldése és lekérése egy Azure-tároló beállításjegyzék használatával.](container-registry-oci-artifacts.md)

Az OCI-összetevőkről az [OCI-beállításjegyzék tárolóként (ORAS)](https://github.com/deislabs/oras) tárházés az [OCI-összetevők](https://github.com/opencontainers/artifacts) tárháza a GitHubon című témakörben olvashat bővebben.

## <a name="helm-charts"></a>Helm térképek

Az Azure Container Registry üzemeltetheti a [Helm-diagramok](https://helm.sh/)adattárak, a csomagolási formátum gyorskezelésére és üzembe helyezésére használt alkalmazások Kubernetes. [Helm ügyfél](https://docs.helm.sh/using_helm/#installing-helm) 2-es verziója (2.11.0 vagy újabb) támogatott.

## <a name="next-steps"></a>További lépések

* Tekintse meg, hogyan [leküldéses és lekéréses](container-registry-get-started-docker-cli.md) lemezképek az Azure Container Registry használatával.

* [Az ACR-feladatok](container-registry-tasks-overview.md) segítségével tárolórendszerképeket hozhat létre és tesztel. 

* A [Moby BuildKit](https://github.com/moby/buildkit) segítségével OCI formátumú tárolókat hozhat létre és csomagol.

* Hozzon létre egy [Helm-tárházat](container-registry-helm-repos.md) az Azure Container Registry üzemelteti. 


