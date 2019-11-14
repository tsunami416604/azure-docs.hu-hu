---
title: A OpenShift Container platform 4. x telepítése az Azure-ban
description: Telepítse az OpenShift Container platform 4. x verzióját az Azure-ban.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035440"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>A OpenShift Container platform 4. x telepítése az Azure-ban

A OpenShift Container platform (OCP) 4,2 üzembe helyezése mostantól támogatott az Azure-ban a telepítő által kiépített infrastruktúra (IPI) modell használatával.  A OpenShift 4 kipróbálására szolgáló Kezdőlap a [Try.openshift.com](https://try.openshift.com/). A OCP 4,2 Az Azure-ban való telepítéséhez látogasson el a [Red Hat OpenShift cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) oldalára.  A hely eléréséhez Red Hat hitelesítő adatok szükségesek.


## <a name="notes"></a>Megjegyzések 

 - A OCP 4. x telepítéséhez és futtatásához az Azure-ban egy Azure Active Directory (HRE) egyszerű szolgáltatásnév (SP) szükséges.
     - Az SP-nek meg kell adni az **Application. ReadWrite. OWNEDBY** API-engedélyét Azure Active Directory gráfhoz
     - Egy HRE-Bérlői rendszergazdanak rendszergazdai jóváhagyást kell adnia ehhez az API-engedélyhez az érvénybe léptetéséhez
     - Az SP-nek **közreműködői** és **felhasználói hozzáférési rendszergazdai** szerepköröket kell megadnia az előfizetéshez
 - A 4. x OCP telepítési modellje eltér a 3. x verziótól, és nincsenek elérhető Azure Resource Manager sablonok az Azure-ban a 4. x OCP telepítéséhez
 - Ha a telepítési folyamat során problémák merülnek fel, lépjen kapcsolatba a megfelelő céggel (Microsoft vagy Red Hat)

| Probléma leírása | Kapcsolattartási pont |
|-------------------|---------------|
| Azure-specifikus problémák (HRE, SP, Azure-előfizetés stb.)                              | Microsoft |
| OpenShift-specifikus problémák (telepítési hibák/hibák, Red Hat előfizetés stb.) |  Red Hat  |




## <a name="next-steps"></a>Következő lépések

- [OpenShift-tároló platform – első lépések](https://docs.openshift.com)
