---
title: Azure Service Fabric környezeti változók
description: Ismerje meg az Azure Service Fabric környezeti változóit. A változók és azok felhasználási feladatait tartalmazó teljes lista hivatkozását tartalmazza.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645684"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric környezeti változók

Service Fabric beépített környezeti változók vannak beállítva az egyes szolgáltatási példányokhoz. A környezeti változók teljes listája alább látható:

| Környezeti változó                         | Leírás                                                            | Példa                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Az alkalmazás háló URI-neve                                 | háló:/MyApplication                                                |
| Fabric_CodePackageName                       | Annak a kódnak a neve, amelyhez a folyamat tartozik              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | A végpont IP-címe vagy teljes tartományneve                                 | 10.0.0.1                                                     |
| Háló\_végpontjának\_*ServiceEndpointName*              | A végpont portszáma                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Napló mappája                                                             | C:\\\\\\\\\\adat_App\\_Node_0\\MyApplicationType_App12\\napló\\\\      |
| Fabric_Folder_App_Temp                       | Temp mappa                                                            | C:\\\\\\\\\\adat_App\\_Node_0\\MyApplicationType_App12\\Temp\\\\     |
| Fabric_Folder_App_Work                       | Munkahelyi mappa                                                            | C:\\\\az\\\\\\\\adat_App\\_Node_0\\MyApplicationType_App12\\működik\\     |
| Fabric_Folder_Application                    | Az alkalmazások kezdőmappa                                           | C:\\\\\\\\_App\\_Node_0\\az\\adatMyApplicationType_App12\\             |
| Fabric_IsContainerHost                       | Logikai érték, amely azt határozza meg, hogy a folyamat tároló-e                   | hamis                                                                |
| Fabric_NodeId                                | A folyamatot futtató csomóponthoz tartozó csomópont-azonosító                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | A csomópont IP-címe vagy teljes tartományneve a fürt jegyzékfájljában megadott módon. | localhost vagy 10.0.0.1                                                |
| Fabric_NodeName                              | A folyamatot futtató csomópont csomópontjának neve                          | _Node_0                                                              |
| Fabric_ServiceName                           | A szolgáltatás háló URI-neve, ha a szolgáltatás ExclusiveProcess módban van tárolva. Ez a változó csak akkor érhető el, ha a szolgáltatást a ServicePackageActivationMode ExclusiveProcess hozza létre.  | háló:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | A ServicePackageActivationId                                         | EGY GUID                                                               |
| Fabric_ServicePackageName                    | A folyamat részét képező szolgáltatáscsomag neve                     | Web1Pkg                                                              |

Service Fabric futtatókörnyezet által használt belső környezeti változók:

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
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
