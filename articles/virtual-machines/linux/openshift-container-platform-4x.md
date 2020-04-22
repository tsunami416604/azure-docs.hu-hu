---
title: OpenShift Container Platform 4.x telepítése az Azure-ban
description: OpenShift Container Platform 4.x üzembe helyezése az Azure-ban.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759493"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>OpenShift Container Platform 4.x telepítése az Azure-ban

Az OpenShift Container Platform (OCP) 4.2 üzembe helyezését az Azure mostantól támogatja a telepítővel kiépített infrastruktúra (IPI) modellen keresztül.  Az OpenShift 4 kipróbálására szolgáló céloldal [try.openshift.com](https://try.openshift.com/). Az OCP 4.2 azure-beli telepítéséhez látogasson el a [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) lapra.  A red hat hitelesítő adatok szükségesek a webhely eléréséhez.


## <a name="notes"></a>Megjegyzések 

 - Az OCP 4.x azure-ban való telepítéséhez és futtatásához szükség van egy Azure Active Directory (AAD) egyszerű szolgáltatás (SP) telepítéséhez és futtatásához
     - Az SP-nek meg kell adni az **Application.ReadWrite.OwnedBy** API-engedélyét az Azure Active Directory Graph-hoz
     - Az AAD-bérlői rendszergazdának rendszergazdai hozzájárulást kell adnia ehhez az API-engedélyhez a hatálybalépéshez.
     - Az SP-nek **közreműködői** és **felhasználói hozzáférés-rendszergazdai** szerepkört kell kapnia az előfizetéshez
 - Az OCP 4.x telepítési modellje eltér a 3.x-től, és nincselérhető Azure Resource Manager-sablon az OCP 4.x azure-ban való üzembe helyezéséhez az Azure-ban
 - Ha a telepítési folyamat során problémák merülnek fel, forduljon a megfelelő vállalathoz (Microsoft vagy Red Hat)

| Probléma leírása | Kapcsolattartó pont |
|-------------------|---------------|
| Az Azure-ral kapcsolatos konkrét problémák (AAD, SP, Azure-előfizetés stb.)                              | Microsoft |
| OpenShift-specifikus problémák (telepítési hibák / hibák, Red Hat előfizetés stb.) |  Red Hat  |




## <a name="next-steps"></a>További lépések

- [Az OpenShift container platform első lépései](https://docs.openshift.com)
