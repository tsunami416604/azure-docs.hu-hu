---
title: Megoldás az Azure-ajánlások |} A Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: e5011dbaad5e5935f3aa792bd3a3ed2b271f23bc
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632433"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Ajánlott eljárások az eszközkezelési megoldások létrehozása az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez a felügyeleti megoldások létrehozásához az Azure-ban, jelenleg előzetes verzióban érhető el előzetes dokumentációjában talál. Semmilyen sémát, az alábbiakban a változhat.  

Ez a cikk ismerteti az ajánlott eljárások a [hozzon létre egy felügyeleti megoldás fájlt](solutions-solution-file.md) az Azure-ban.  Ezt az információt a további ajánlott eljárások meghatározott frissül.

## <a name="data-sources"></a>Adatforrások
- Adatforrások lehet [Resource Manager-sablonnal konfigurált](../../log-analytics/log-analytics-template-workspace-configuration.md), de azok nem szerepelnie kell a megoldásfájlt.  A hiba oka, hogy adatforrások konfigurálása jelenleg nem idempotens, ami azt jelenti, hogy a megoldás felülírhatja a felhasználó munkaterületét a meglévő konfigurációt.<br><br>A megoldás például szükség lehet az alkalmazások eseménynaplójában a figyelmeztetési és eseményeket.  Ha megadja ezt adatforrásként a megoldásában, azzal kockáztatja információs események eltávolítása, ha a felhasználó a saját munkaterületen konfigurált.  Ha tartalmazza az összes eseményt, majd, előfordulhat, hogy kell események gyűjtése a túlzott információkat a felhasználó munkaterületén.

- Ha a megoldáshoz szükséges a standard szintű forrásokból származó adatokat, majd meg kell határozni a előfeltételként.  Állapot a dokumentációban, hogy az ügyfél önállóan kell állítania az adatforrás.  
- Adjon hozzá egy [Data Flow ellenőrzési](../../azure-monitor/platform/view-designer-tiles.md) meg azokat az adatforrásokat, úgy kell konfigurálni a szükséges adatokat gyűjteni a felhasználót a megoldás nézetekhez üzenet.  Ez az üzenet jelenik meg a nézet a csempe a szükséges adatok nem található.


## <a name="runbooks"></a>Runbookok
- Adjon hozzá egy [Automation ütemezési](../../automation/automation-schedules.md) minden runbook a megoldásban, amelyet futtatása ütemezés szerint.
- Tartalmazza a [IngestionAPI modul](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) írja az adatokat a Log Analytics-adattárban runbookok által használható a megoldásban.  A megoldás konfigurálása arra [referencia](solutions-solution-file.md#solution-resource) így, hogy a megoldás a rendszer eltávolítja, ha ehhez az erőforráshoz.  Ez lehetővé teszi több megoldások megosztása a modult.
- Használat [automatizálási változók](../../automation/automation-schedules.md) megadhatja, hogy a felhasználók később módosítani szeretné a megoldáshoz értékeket.  Akkor is, ha a változó tartalmazza a megoldás van konfigurálva, annak értéke továbbra is módosítható.

## <a name="views"></a>Nézetek
- Az összes megoldáshoz tartalmaznia kell, hogy a felhasználói portálon jelenik meg egyetlen nézetben.  A nézet tartalmazhat több [Vizualizáció részek](../../azure-monitor/platform/view-designer-parts.md) mutatja be az adatokat más-más részhalmazához.
- Adjon hozzá egy [Data Flow ellenőrzési](../../azure-monitor/platform/view-designer-tiles.md) meg azokat az adatforrásokat, úgy kell konfigurálni a szükséges adatokat gyűjteni a felhasználót a megoldás nézetekhez üzenet.
- A megoldás konfigurálása arra [tartalmazhat](solutions-solution-file.md#solution-resource) úgy, hogy a eltávolítja a rendszer, ha a megoldás a rendszer eltávolítja a nézetet.

## <a name="alerts"></a>Riasztások
- A címzettek listája határozza meg a megoldásfájlt a paramétert, a felhasználó megadhatja őket, amikor a megoldás telepítése.
- A megoldás konfigurálása arra [referencia](solutions-solution-file.md#solution-resource) riasztási szabályok, így a felhasználó módosíthatja a konfigurációt.  Szeretnék, hogy a változások, például a címzettlista módosítása, a riasztás a küszöbérték módosítása vagy a riasztási szabály letiltása. 


## <a name="next-steps"></a>További lépések
* Az alapvető folyamatainak végig [megtervezéséért és építéséért felügyeleti megoldás](solutions-creating.md).
* Ismerje meg, hogyan [létrehozza a megoldásfájlt](solutions-solution-file.md).
* [Adja hozzá a mentett keresések és a riasztások](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá az Automation-runbookok és egyéb erőforrások](solutions-resources-automation.md) a felügyeleti megoldáshoz.

