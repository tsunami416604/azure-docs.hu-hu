---
title: A Container-ajánlatok közzétételi útmutatója az Azure Marketplace-en
description: Ez a cikk a tároló-ajánlatok Azure Marketplace-en való közzétételének követelményeit ismerteti.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 99aecee930e5d77302ad54babd927588519e33fd
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160460"
---
# <a name="publishing-guide-for-container-offers"></a>A Container-ajánlatok közzétételi útmutatója

A Container segítségével közzéteheti a tárolók rendszerképét az Azure Marketplace-en. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

A tárolók az Azure Marketplace-en üzembe helyezett és számlázható tranzakciós ajánlatok. A felhasználó által megjelenő művelet hívása "Letöltés most".

Ha a megoldás egy olyan Docker-tároló-rendszerkép, amely Kubernetes-alapú Azure Container Service-példányként van beállítva, használja a *Container* ajánlat típusát. 

> [!NOTE]
> Kubernetes-alapú Azure Container Service-példányok például az Azure Kubernetes Service vagy a Azure Container Instances, a Kubernetes-alapú tároló-futtatókörnyezethez választható Azure-ügyfelek.  

A Microsoft jelenleg támogatja az ingyenes és a saját licencű (BYOL) licencelési modelleket.

## <a name="container-offer-requirements"></a>A Container-ajánlat követelményei

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Az ingyenes vagy a BYOL számlázási modellt is támogatja.<br><br> |  
| Docker létrehozott rendszerkép | A tároló lemezképeit a Docker-rendszerkép specifikációja alapján kell létrehozni, és egy Docker kell létrehozni.<br> <br>A Docker-rendszerképek létrehozásával kapcsolatos további információkért tekintse meg a [Docker dokumentációjának](https://docs.docker.com/engine/reference/builder/#usage)"használat" szakaszát.<br><br> |  
| Üzemeltetés Azure Container Registry adattárban | A tároló lemezképeit egy Azure Container Registry adattárban kell tárolni.<br> <br>A Azure Container Registry használatáról további információt a rövid útmutató [: Private Container Registry létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)című témakörben talál.<br><br> |  
| Képcímkézés | A tároló lemezképének legalább egy címkét tartalmaznia kell (címkék maximális száma: 16).<br><br>A képek címkézésével kapcsolatos további információkért tekintse meg `docker tag` a lapot a [Docker dokumentációs](https://docs.docker.com/engine/reference/commandline/tag) webhelyén.<br><br> |  

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, Ismerje meg, hogyan [növelheti Felhőbeli üzletét az Azure Marketplace-szel](https://azuremarketplace.microsoft.com/sell).

A partner Centerben való regisztráció és a használat megkezdése:

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információt az [Azure Container-ajánlat létrehozása](./partner-center-portal/create-azure-container-offer.md) című témakörben talál.
