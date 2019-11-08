---
title: A tárolók az Azure Marketplace-hez készült közzétételi útmutatót nyújtanak
description: Ez a cikk a tárolók a piactéren való közzétételének követelményeit ismerteti
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822998"
---
# <a name="containers-offer-publishing-guide"></a>A tárolók közzétételi útmutatót biztosítanak

A Container segítségével közzéteheti a tároló-rendszerképet az Azure Marketplace-en. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek üzembe helyezése és számlázása a piactéren történik. A felhasználó által megjelenő művelet hívása "Letöltés most".

Ha a megoldás egy, a Kubernetes-alapú Azure Container Service-ben kiépített Docker-tároló-rendszerkép, akkor használja a Container ajánlat típusát.

>[!NOTE]
>Például egy Kubernetes-alapú Azure Container szolgáltatás, például az Azure Kubernetes Service vagy a Azure Container Instances, az Azure-ügyfelek választhatnak a Kubernetes-alapú tárolók futtatásához.  

A Microsoft jelenleg támogatja az ingyenes és a saját licencű (BYOL) licencelési modelleket.

## <a name="containers-offer"></a>Tárolók ajánlat

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Az ingyenes vagy a BYOL számlázási modellt is támogatja. |  
| Docker-ből készített rendszerkép | A tároló lemezképeit a Docker-rendszerkép specifikációja alapján kell létrehozni, és a Docker kell létrehozni.<ul> <li>A Docker-rendszerképek létrehozásával kapcsolatos további információkért tekintse meg a használati szakaszt, amely a következő helyen található: [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Üzemeltetés az ACR-ben | A tároló lemezképeit Azure Container Registry (ACR) tárházban kell tárolni.<ul> <li>Az ACR használatával kapcsolatos további információkért látogasson el a rövid útmutató: tároló-beállításjegyzék létrehozása a [docs.microsoft.com/Azure/Container-Registry/Container-Registry-Get-Started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)címen található Azure Portal oldalon.</li> </ul> |  
| Képek címkézése | A tárolók képeinek legalább 1 címkét kell tartalmazniuk (maximális Címkék: 16).<ul> <li>A képek címkézésével kapcsolatos további információkért látogasson el a Docker tag oldalára, amely a következő címen található: [docs.Docker.com/Engine/Reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piactéren.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy dolgozik egy meglévőn,

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a Cloud Partner Portalba](https://cloudpartner.azure.com) .
- További információkért lásd: [tárolók](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) .
