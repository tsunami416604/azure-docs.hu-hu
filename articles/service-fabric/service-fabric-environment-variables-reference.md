---
title: Az Azure Service Fabric környezeti változói
description: Ismerje meg a környezeti változók az Azure Service Fabric. A változók és azok felhasználásai teljes listájának hivatkozását tartalmazza.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645684"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric környezeti változók

A Service Fabric beépített környezeti változókkal rendelkezik az egyes szolgáltatáspéldányokhoz. A környezeti változók teljes listája a következő:

| Környezeti változó                         | Leírás                                                            | Példa                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Az alkalmazás szövet uri neve                                 | szövet:/MyApplication                                                |
| Fabric_CodePackageName                       | Annak a kódcsomagnak a neve, amelyhez a folyamat tartozik              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | A végpont IP-címe vagy teljes tartományszáma                                 | 10.0.0.1                                                     |
| \_\_*Hálóvégpont-szolgáltatásvégpontneve*              | A végpont portszáma                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Naplómappa                                                             | C:\\\\\\\\\\\\Adat_App\\\\\\\\_Node_0 MyApplicationType_App12 napló      |
| Fabric_Folder_App_Temp                       | Temp mappa                                                            | C:\\\\\\\\MyApplicationType_App12\\\\\\\\\\_Node_0\\adatok _App     |
| Fabric_Folder_App_Work                       | Munkahelyi mappa                                                            | C:\\\\\\\\Az\\\\\\\\adatok\\\\_App _Node_0 MyApplicationType_App12     |
| Fabric_Folder_Application                    | Az alkalmazások kezdőmappája                                           | C:\\\\\\\\Adatok\\\\\\\\_App _Node_0 MyApplicationType_App12             |
| Fabric_IsContainerHost                       | A bool, amely meghatározza, hogy a folyamat egy konténer                   | hamis                                                                |
| Fabric_NodeId                                | A folyamatot futtató csomópont csomópontazonosítója                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | A csomópont IP-címe vagy teljes tartományszáma a fürtjegyzékfájlban megadottak szerint. | localhost vagy 10.0.0.1                                                |
| Fabric_NodeName                              | A folyamatot futtató csomópont csomópontneve                          | _Node_0                                                              |
| Fabric_ServiceName                           | A szolgáltatás fabric uri neve, ha a szolgáltatás kizárólagosfolyamat módban van üzemeltetve. Ez a változóérték csak akkor érhető el, ha a ServicePackageActivationMode ExclusiveProcess szolgáltatással hozza létre a szolgáltatást.  | háló:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | A ServicePackageActivationId                                         | A GUID                                                               |
| Fabric_ServicePackageName                    | Annak a szolgáltatáscsomagnak a neve, amelynek a folyamat                     | Web1Pkg                                                              |

A Service Fabric futásideje által használt belső környezeti változók:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorCím
- FabricPackageFileName
- HostedServiceName (HostedServiceName)
