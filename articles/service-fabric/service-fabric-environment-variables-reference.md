---
title: "Az Azure Service Fabric környezeti változók |} Microsoft Docs"
description: "A Service Fabric környezeti változók referenciadokumentációt tartalmaz"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: a9faefb43b9d5da81dddef8f326a3867b32842f7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-environment-variables"></a>A Service Fabric környezeti változók

A Service Fabric rendelkezik minden szolgáltatáspéldány beállított beépített környezeti változókat. A teljes listát a környezeti változók nem éri el:

| Környezeti változó                         | Leírás                                                            | Példa                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Az alkalmazás uri háló neve                                 | fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | A kódcsomag, amelyhez tartozik a folyamat neve              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Az IP-cím vagy FQDN-jét a végpont                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | A végpont portszáma                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Naplózási mappájának                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Ideiglenes mappa létrehozása                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Munkahelyi mappák                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Az alkalmazások kezdőmappa                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Egy logikai érték megadása, hogy a folyamat egy olyan tároló                   | false                                                                |
| Fabric_NodeId                                | A csomópont-azonosító, a csomópont a folyamatának futtatása                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Az IP- vagy a csomópont, a fürt megadott FQDN manifest fájlt. | localhost vagy 10.0.0.1                                                |
| Fabric_NodeName                              | A csomópont neve a csomópont a folyamatának futtatása                          | _Node_0                                                              |
| Fabric_ServiceName                           | A szolgáltatás neve a, ha szolgáltatás ExclusiveProcess módban. A változó értéke csak érhető el, ha a szolgáltatás ServicePackageActivationMode ExclusiveProcess hoz létre.  | MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | A GUID                                                               |
| Fabric_ServicePackageName                    | A service-csomag a folyamat neve részét képezi.                     | Web1Pkg                                                              |

Service Fabric-futtatókörnyezet által használt belső környezeti változó:

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