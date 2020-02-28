---
title: Felügyeleti megoldás az Azure-ban – ajánlott eljárások | Microsoft Docs
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663198"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Ajánlott eljárások a felügyeleti megoldások létrehozásához az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció az Azure-ban jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásához. Az alább ismertetett sémák változhatnak.  

Ez a cikk az Azure-beli [felügyeleti megoldási fájlok létrehozásával](solutions-solution-file.md) kapcsolatos ajánlott eljárásokat ismerteti.  Ezek az információk frissülnek, mivel a rendszer további ajánlott eljárásokat azonosít.

## <a name="data-sources"></a>Adatforrások
- Az adatforrások [Resource Manager-sablonnal konfigurálhatók](../../azure-monitor/platform/template-workspace-configuration.md), de nem szerepelhetnek a megoldási fájlban.  Ennek az az oka, hogy az adatforrások konfigurálása jelenleg nem idempotens, ami azt jelenti, hogy a megoldás felülírhatja a felhasználó munkaterületének meglévő konfigurációját.<br><br>Előfordulhat például, hogy a megoldás figyelmeztetési és hiba eseményeket igényel az alkalmazás eseménynaplójában.  Ha ezt a megoldást adatforrásként adja meg a megoldásban, akkor az információs események eltávolítását kockáztatja, ha a felhasználó ezt a munkaterületen konfigurálta.  Ha az összes eseményt tartalmazza, akkor előfordulhat, hogy a felhasználó munkaterületén túl sok információt gyűjthet.

- Ha a megoldás egy szabványos adatforrásból származó adatokra van szüksége, ezt előfeltételként kell megadnia.  A dokumentációban szereplő állapot, amelyben az ügyfélnek saját maga kell konfigurálnia az adatforrást.  
- Vegyen fel egy adatfolyam- [ellenőrző](../../azure-monitor/platform/view-designer-tiles.md) üzenetet a megoldás bármely nézetére, hogy utasítsa a felhasználót olyan adatforrásokra, amelyeket be kell állítani a szükséges adatok gyűjtéséhez.  Ez az üzenet a nézet csempén jelenik meg, ha a szükséges adatmennyiség nem található.


## <a name="runbooks"></a>Runbookok
- Adjon hozzá egy [automatizálási ütemtervet](../../automation/automation-schedules.md) a megoldás minden olyan runbook, amelynek ütemezett futtatást kell futtatnia.
- Adja meg a [IngestionAPI modult](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) a megoldásban, amelyet a runbookok a log Analytics adattárba írt.  Konfigurálja a megoldást úgy, hogy az erőforrásra [hivatkozzon](solutions-solution-file.md#solution-resource) , hogy az a megoldás eltávolítása után is fennmaradjon.  Ez lehetővé teszi, hogy több megoldás ossza meg a modult.
- Az [Automation-változók](../../automation/automation-schedules.md) használatával megadhatja a megoldásnak azokat a megoldásokat, amelyeket a felhasználók később módosítani kívánnak.  Még ha a megoldás úgy van konfigurálva, hogy tartalmazza a változót, az értéke továbbra is módosítható.

## <a name="views"></a>Nézetek
- Minden megoldásnak tartalmaznia kell egyetlen nézetet, amely megjelenik a felhasználó portálján.  A nézet több [vizualizációs alkatrészt](../../azure-monitor/platform/view-designer-parts.md) is tartalmazhat, amelyek különböző adatkészleteket mutatnak be.
- Vegyen fel egy adatfolyam- [ellenőrző](../../azure-monitor/platform/view-designer-tiles.md) üzenetet a megoldás bármely nézetére, hogy utasítsa a felhasználót olyan adatforrásokra, amelyeket be kell állítani a szükséges adatok gyűjtéséhez.
- Konfigurálja a megoldást úgy, hogy [tartalmazza](solutions-solution-file.md#solution-resource) a nézetet, hogy a rendszer eltávolítsa, ha a megoldás el lett távolítva.

## <a name="alerts"></a>Riasztások
- Adja meg a címzettek listáját paraméterként a megoldás fájljában, így a felhasználó megadhatja azokat a megoldás telepítésekor.
- Konfigurálja a megoldást úgy, hogy a riasztási szabályokra [hivatkozzon](solutions-solution-file.md#solution-resource) , hogy a felhasználó módosíthatja a konfigurációt.  Előfordulhat, hogy olyan módosításokat szeretne végezni, mint például a címzettek listájának módosítása, a riasztás küszöbértékének módosítása vagy a riasztási szabály letiltása. 


## <a name="next-steps"></a>Következő lépések
* Végigvezeti a [felügyeleti megoldások tervezésének és kialakításának](solutions-creating.md)alapvető folyamatán.
* Útmutató [a megoldási fájlok létrehozásához](solutions-solution-file.md).
* [Mentett keresések és riasztások hozzáadása](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* Az [Automation-runbookok és egyéb erőforrásokat is hozzáadhat](solutions-resources-automation.md) a felügyeleti megoldáshoz.

