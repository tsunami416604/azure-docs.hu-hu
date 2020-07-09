---
title: Microsoft Threat Modeling Tool kiadás 02/11/2020 – Azure
description: A veszélyforrások modellezése eszköz kibocsátási megjegyzéseit dokumentálja
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77624838"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool Update kiadás 7.3.00206.1-02/11/2020

A Microsoft Threat Modeling Tool verziójának 7.3.00206.1 (TMT) február 11 2020-én adták ki, és a következő módosításokat tartalmazza:

- Hibajavítások

## <a name="notable-bug-fixes"></a>Jelentős hibajavítások

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>A várt tartományokon kívüli prioritási értékekkel kapcsolatos hibák

Egyes ügyfelek a következő hibaüzenetet kaptak a "Threat Modeling Tool 2016" vagy egyéni sablonokban létrehozott fájlok megnyitásakor:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Ez a probléma ebben a kiadásban lett feloldva.

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió szükséges
  - [.Net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)található, és [az eszköz használatával](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
