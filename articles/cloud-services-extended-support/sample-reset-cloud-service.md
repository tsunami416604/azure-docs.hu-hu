---
title: Azure PowerShell minták – Azure Cloud Services alaphelyzetbe állítása (bővített támogatás)
description: Az Azure Cloud Service (bővített támogatás) üzembe helyezésének alaphelyzetbe állítására szolgáló parancsfájlok
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a2c10e7915b0d293d3193c710885ded26a791f2c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745037"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure Cloud Service alaphelyzetbe állítása (bővített támogatás) 
Ezek a minták a meglévő Azure Cloud Service (bővített támogatás) üzembe helyezésének különböző módjait fedik le.

## <a name="reimage-role-instances-of-cloud-service"></a>Cloud Service-példányok rendszerkép-példányainak átállítása
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Ez a parancs alaphelyzetbe állítja a 2 szerepkör-példányt **\_ \_ 0** -ban, a ContosoCS nevű **ContosoBackEnd pedig \_ \_ 1** ContosoFrontEnd, amely a ContosOrg nevű erőforráscsoporthoz tartozik.

## <a name="reimage-all-roles-of-cloud-service"></a>A Cloud Service összes szerepkörének rendszerképének alaphelyzetbe állítása
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Egy felhőalapú szolgáltatás egyetlen szerepkör-példányának rendszerképének alaphelyzetbe állítása
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Egy felhőalapú szolgáltatás egyetlen szerepkör-példányának újraindítása
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>További lépések

Az Azure Cloud Services (bővített támogatás) szolgáltatással kapcsolatos további információkért lásd: [azure Cloud Services (bővített támogatás) – áttekintés](overview.md).