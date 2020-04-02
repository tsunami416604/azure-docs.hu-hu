---
title: Felügyeleti megoldás az Azure bevált módszereiben
description: Ebben a cikkben ismerése a felügyeleti megoldásfájl létrehozásához. Megtudhatja, hogyan dolgozhat adatforrásokkal, runbookokkal, nézetekkel és figyelmeztetésekkel.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548170"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Gyakorlati tanácsok felügyeleti megoldások létrehozásához az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció felügyeleti megoldások létrehozásához az Azure-ban, amelyek jelenleg előzetes verzióban. Az alábbiakban ismertetett sémák változhatnak.  

Ez a cikk gyakorlati tanácsok egy [felügyeleti megoldás fájl azure-ban létrehozásához.](solutions-solution-file.md)  Ezeket az információkat a további bevált gyakorlatok azonosítása után frissítjük.

## <a name="data-sources"></a>Adatforrások
- Az adatforrások [konfigurálhatók Erőforrás-kezelő sablonnal](../../azure-monitor/platform/template-workspace-configuration.md), de nem szerepelhetnek a megoldásfájlban.  Ennek az az oka, hogy az adatforrások konfigurálása jelenleg nem idempotens, ami azt jelenti, hogy a megoldás felülírhatja a felhasználó munkaterületének meglévő konfigurációját.<br><br>A megoldás például figyelmeztető és hiba eseményeket igényelhet az alkalmazás eseménynaplójából.  Ha ezt adatforrásként adja meg a megoldásban, azzal a kockázattal jár, hogy eltávolítja az információs eseményeket, ha a felhasználó ezt konfigurálta a munkaterületen.  Ha az összes eseményt felvette, akkor előfordulhat, hogy túlzott információeseményeket gyűjt a felhasználó munkaterületén.

- Ha a megoldás a szabványos adatforrások egyikéből származó adatokat igényel, akkor ezt előfeltételként kell meghatároznia.  A dokumentációban adja meg, hogy az ügyfélnek saját maga kell konfigurálnia az adatforrást.  
- Adjon hozzá egy [adatfolyam-ellenőrzési](../../azure-monitor/platform/view-designer-tiles.md) üzenetet a megoldás bármely nézetéhez, hogy tájékoztassa a felhasználót azadatforrásokról, amelyeket be kell állítani a szükséges adatok gyűjtéséhez.  Ez az üzenet akkor jelenik meg a nézet csempéjén, ha a szükséges adatok nem találhatók.


## <a name="runbooks"></a>Runbookok
- Adjon hozzá egy [Automation-ütemezést](../../automation/automation-schedules.md) a megoldás minden olyan runbookjához, amelynek ütemezés szerint kell futnia.
- Az [IngestionAPI modult](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) is bekell foglalni a megoldásba, amelyet az adatokat a Log Analytics-tárházba író runbookok használnak.  Konfigurálja úgy a megoldást, hogy [az erőforrásra hivatkozzon,](solutions-solution-file.md#solution-resource) hogy az a megoldás eltávolítása esetén is maradjon.  Ez lehetővé teszi, hogy több megoldás is megossza a modult.
- [Az Automation változók](../../automation/automation-schedules.md) segítségével olyan értékeket adhat meg a megoldásnak, amelyet a felhasználók később módosítani szeretnének.  Még akkor is, ha a megoldás úgy van beállítva, hogy tartalmazza a változót, értéke továbbra is módosítható.

## <a name="views"></a>Nézetek
- Minden megoldásnak tartalmaznia kell egy egyetlen nézetet, amely megjelenik a felhasználói portálon.  A nézet több [vizualizációs részt](../../azure-monitor/platform/view-designer-parts.md) is tartalmazhat a különböző adathalmazok szemléltetésére.
- Adjon hozzá egy [adatfolyam-ellenőrzési](../../azure-monitor/platform/view-designer-tiles.md) üzenetet a megoldás bármely nézetéhez, hogy tájékoztassa a felhasználót azadatforrásokról, amelyeket be kell állítani a szükséges adatok gyűjtéséhez.
- Konfigurálja úgy a megoldást, hogy az [tartalmazza](solutions-solution-file.md#solution-resource) a nézetet, hogy a megoldás eltávolítása esetén törlődjön.

## <a name="alerts"></a>Riasztások
- Adja meg a címzettek listáját paraméterként a megoldásfájlban, hogy a felhasználó meghatározhassa őket a megoldás telepítésekor.
- Konfigurálja a megoldást a riasztási [szabályokhivatkozására,](solutions-solution-file.md#solution-resource) hogy a felhasználók módosíthassák a konfigurációjukat.  Előfordulhat, hogy módosítani szeretnék, például módosítani szeretnék a címzettlistát, módosítani szeretnék a riasztás küszöbértékét, vagy le szeretnék tiltani a riasztási szabályt. 


## <a name="next-steps"></a>További lépések
* Végighalad a [felügyeleti megoldás tervezésének és építésének alapvető folyamatán.](solutions-creating.md)
* További információ a [megoldásfájl létrehozásáról.](solutions-solution-file.md)
* [Mentett keresések és riasztások hozzáadása](solutions-resources-searches-alerts.md) a felügyeleti megoldáshoz.
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá az Automation runbookokat és egyéb erőforrásokat](solutions-resources-automation.md) a felügyeleti megoldáshoz.

