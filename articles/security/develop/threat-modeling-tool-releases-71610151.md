---
title: A Microsoft Threat Modeling Tool 10/16/2019 kiadása – Azure
description: A fenyegetésmodellező eszköz kiadási megjegyzésekének dokumentálása
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552049"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>A Threat Modeling Tool frissített kiadása: 7.1.61015.1 – 2019. 10. 16.

2019. október 16-án jelent meg a Microsoft Threat Modeling Tool (TMT) 7.1.61015.1-es verziója, amely a következő módosításokat tartalmazza:

- Javított kisegítő lehetőségek
- Hibajavítások
- Új rajzsablonok az Azure Logic Apps és az Azure Data Explorer számára

## <a name="notable-bug-fixes"></a>Figyelemre méltó hibajavítások

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Továbbfejlesztett visszamenőleges kompatibilitás a "Fenyegetésmodellező eszköz 2016" eszközben létrehozott fájlokkal

A "Threat Modeling Tool 2016" -ban létrehozott fenyegetésmodell-fájlok megnyitásával vagy megjelenítésével kapcsolatos számos hibát kijavítottak.

## <a name="feature-enhancements"></a>Funkciófejlesztések

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Új rajzsablonok az Azure Logic Apps és az Azure Data Explorer számára

Az Azure Logic Apps és az Azure Data Explorer új rajzsablonjai az Azure Stencilhez kerültek a kapcsolódó fenyegetések és megoldások mellett.

![Az Azure Logic Apps és az Azure Data Explorer stencil](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>A várt tartományokon kívüli prioritási értékekkel kapcsolatos hibák

Egyes ügyfelek arról számoltak be, hogy a következő hibaüzenetet kapták a "Fenyegetésmodellező eszköz 2016" vagy egyéni sablonokban létrehozott fájlok megnyitásakor:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Ez a kérdés vizsgálat alatt áll

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET verzió szükséges
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A fenyegetésmodellező eszköz dokumentációja [a docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)található, és információkat tartalmaz [az eszköz használatával kapcsolatban.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
