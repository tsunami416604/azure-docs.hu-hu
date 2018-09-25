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
online version: https://docs.microsoft.com/powershell/module/azurerm.frontdoor/new-azurermfrontdoormatchconditionobject
schema: 2.0.0
ms.openlocfilehash: 3a6804abf38126e9296760f5802f75ef35335294
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047496"
---
# Új AzureRmFrontDoorMatchConditionObject
WAF-szabályzat létrehozására szolgáló MatchCondition objektum létrehozása

## SZINTAXIS

```
New-AzureRmFrontDoorMatchConditionObject -MatchVariable <PSMatchVariable>
 -OperatorProperty <PSOperatorProperty> -MatchValue <String[]> [-Selector <String>]
 [-NegateCondition <Boolean>] [-DefaultProfile <IAzureContextContainer>] [<CommonParameters>]
```

## LEÍRÁS
WAF-szabályzat létrehozására szolgáló MatchCondition objektum létrehozása

## PÉLDÁK

### 1. példa
```powershell
PS C:\> New-AzureRmFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Contains -Selector "UserAgent" -MatchValue "Windows"


MatchVariable    : RequestHeader
OperatorProperty : Contains
MatchValue       : {Windows}
Selector         : UserAgent
NegateCondition  : False
```

MatchCondition objektum létrehozása

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

### -MatchValue
A megfelelő értéket.

```yaml
Type: System.String[]
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -MatchVariable
A változó felel meg.
Lehetséges értékek: "RemoteAddr", 'RequestMethod', 'Karakterlánc', "PostArgs", "RequestUri", "RequestHeader", "RequestBody"

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSMatchVariable
Parameter Sets: (All)
Aliases:
Accepted values: RemoteAddr, RequestMethod, QueryString, PostArgs, RequestUri, RequestHeader, RequestBody

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -NegateCondition
Ha ez a negálandó feltétel, vagy nem az alapértelmezett érték FALSE (hamis) nem ismerteti

```yaml
Type: System.Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -OperatorProperty
Egyeztetendő operátor ismerteti.
Lehetséges értékek: "Bármely", "IPMatch", "GeoMatch", "Egyenlő", "Contains", "LessThan", 'GreaterThan', "LessThanOrEqual", "GreaterThanOrEqual", "BeginsWith", "EndsWith''

```yaml
Type: Microsoft.Azure.Commands.FrontDoor.Models.PSOperatorProperty
Parameter Sets: (All)
Aliases:
Accepted values: Any, IPMatch, GeoMatch, Equal, Contains, LessThan, GreaterThan, LessThanOrEqual, GreaterThanOrEqual, BeginsWith, EndsWith

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Választó
Választó RequestHeader vagy RequestBody egyeztetendő neve

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

### CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable. További információk: about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
