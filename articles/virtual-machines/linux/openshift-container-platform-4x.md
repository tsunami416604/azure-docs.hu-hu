---
title: OpenShift Container Platform 4.x telepítése az Azure-ban
description: OpenShift Container Platform 4.x üzembe helyezése az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035440"
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
