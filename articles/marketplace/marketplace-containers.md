---
title: A tárolók közzétételi útmutatót kínálnak az Azure Piactérszámára
description: Ez a cikk ismerteti a tárolók közzétételének követelményeit a Marketplace-en
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277306"
---
# <a name="containers-offer-publishing-guide"></a>A tárolók közzétételi útmutatót kínálnak

A tárolóajánlatok segítségével közzéteheti a tárolólemezképet az Azure Piactéren. Ebben az útmutatóban megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek a Marketplace-en keresztül vannak telepítve és számlázva. A felhasználó által látott cselekvésre való felhívás a "Get It Now" (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now)

Használja a Container-ajánlat típusát, ha a megoldás egy Kubernetes-alapú Azure-tárolószolgáltatásként kiépített Docker-tárolórendszerkép.

>[!NOTE]
>Például egy Kubernetes-alapú Azure-tároló szolgáltatás, például az Azure Kubernetes-szolgáltatás vagy az Azure Container Instances, az Azure-ügyfelek kiválasztása egy Kubernetes-alapú tároló futásidejű.  

A Microsoft jelenleg támogatja az ingyenes és bring-your-own-license (BYOL) licencelési modelleket.

## <a name="containers-offer"></a>Konténerajánlat

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Támogassa az ingyenes vagy byol számlázási modellt. |  
| A Dockerfile-ból készült lemezkép | A tárolórendszerképeknek a Docker-lemezkép specifikációján kell alapulniuk, és egy Docker-fájlból kell épülniük.<ul> <li>A docker-lemezképek létrehozásáról a [használat](https://docs.docker.com/engine/reference/builder/#usage)docs.docker.com/engine/reference/builder/#usage című szakaszban talál további információt.</li> </ul> |  
| Tárhely az ACR-ben | A tárolórendszerképeket egy Azure Container Registry (ACR) tárházban kell üzemeltetni.<ul> <li>Az ACR használatáról további információt a rövid útmutató: Tároló beállításjegyzék létrehozása az [Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)Portal docs.microsoft.com/azure/container-registry/container-registry-get-started-portal.</li> </ul> |  
| Képcímkézés | A tárolóképeknek legalább 1 címkét kell tartalmazniuk (maximális címkék: 16).<ul> <li>A kép címkézésével kapcsolatos további információkért látogasson el a [docker](https://docs.docker.com/engine/reference/commandline/tag)címke oldalára, amely a docs.docker.com/engine/reference/commandline/tag .</li> </ul> |  

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piacon.

Ha regisztrált, és új ajánlatot hoz létre, vagy egy meglévőn dolgozik,

- [Jelentkezzen be a Cloud Partner Portalszolgáltatásba](https://cloudpartner.azure.com) az ajánlat létrehozásához vagy teljesítéséhez.
- További információt a [Tárolók](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) című témakörben talál.
