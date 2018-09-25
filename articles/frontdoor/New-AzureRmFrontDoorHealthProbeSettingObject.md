---
title: Azure bejárati ajtajának szolgáltatás – PowerShell-referencia |} A Microsoft Docs
description: Ez az útmutató részletesen a különböző Azure bejárati ajtajának szolgáltatás támogatott PowerShell-parancsmagok
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2018
ms.author: sharadag
Module Name: AzureRM.FrontDoor
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoorhealthprobesettingobject
schema: 2.0.0
ms.openlocfilehash: 23a5b3a56ecdacbf923db9131a89942bba7de276
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047615"
---
# Új AzureRmFrontDoorHealthProbeSettingObject

A bejárati ajtajának létrehozásához PSHealthProbeSetting objektum létrehozása

## SZINTAXIS

```
New-AzureRmFrontDoorHealthProbeSettingObject -Name <String> [-Path <String>] [-Protocol <PSProtocol>]
 [-IntervalInSeconds <Int32>] [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## LEÍRÁS
A bejárati ajtajának létrehozásához PSHealthProbeSetting objektum létrehozása

## PÉLDÁK

### 1. példa
```powershell
PS C:\>  New-AzureRmFrontDoorHealthProbeSettingObject -Name "healthProbeSetting1"


Path              : /
Protocol          : Http
IntervalInSeconds : 30
ResourceState     :
Id                :
Name              : healthProbeSetting1
Type              :
```

A bejárati ajtajának létrehozásához PSHealthProbeSetting objektum létrehozása

## PARAMÉTEREK

### -DefaultProfile
A hitelesítő adatokat, fiók, bérlői és az Azure-ral való kommunikációhoz használt előfizetés.

```yaml
Type: Microsoft.Azure.Commands.Common.Authentication.Abstractions.IAzureContextContainer
Parameter Sets: (All)
Aliases: AzureRmContext, AzureCredential

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -IntervalInSeconds
Mintavételek egészségügyi között eltelt másodpercek száma.
Alapértelmezett értéke 30

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Név
egészségügyi mintavételi beállítás neve.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Path
Használja az állapotmintához elérési útja.
Alapértelmezett érték a /

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Protocol
Protokoll sémát, és ez a Hálózatfigyelő alapértelmezett értéket használja a HTTP

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSProtocol
Parameter Sets: (All)
Aliases:
Accepted values: Http, Https

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable. További információk: about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
