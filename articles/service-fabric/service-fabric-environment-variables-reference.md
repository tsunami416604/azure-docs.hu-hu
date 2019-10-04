---
title: Az Azure Service Fabric környezeti változók |} A Microsoft Docs
description: A környezeti változók a Service Fabric dokumentációja
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946722"
---
# <a name="service-fabric-environment-variables"></a>A Service Fabric környezeti változók

A Service Fabric beépített környezeti változók beállítása minden szolgáltatáspéldány van. A környezeti változók teljes listája alatt van:

| Környezeti változó                         | Leírás                                                            | Példa                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | A fabric URI-t az alkalmazás neve                                 | fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | A kód csomag, amelyhez tartozik a folyamat neve              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Az ip-cím vagy teljes Tartománynevét a végpont                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | A végponti port száma                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Log mappában                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Ideiglenes mappa létrehozása                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Munkahelyi mappák                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Az alkalmazások kezdőmappa                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Adja meg, hogy-e a folyamat egy tároló egy logikai                   | false                                                                |
| Fabric_NodeId                                | A csomópont-azonosító, a csomópont a folyamat futtatása                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | A IP vagy FQDN-jének a csomópontot a fürthöz megadott jegyzékfájl. | localhost vagy 10.0.0.1                                                |
| Fabric_NodeName                              | A csomópont nevét, a csomópont a folyamat futtatása                          | _Node_0                                                              |
| Fabric_ServiceName                           | A szolgáltatást, ha ExclusiveProcess módban lévő üzemeltetett service fabric uri neve. A változó értéke csak akkor használható, ha a szolgáltatás ServicePackageActivationMode ExclusiveProcess hoz létre.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | EGY GUID AZONOSÍTÓT                                                               |
| Fabric_ServicePackageName                    | A folyamat service-csomag neve része                     | Web1Pkg                                                              |

Service Fabric-futtatókörnyezet által használt belső környezeti változókat:

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
