---
title: "Erőforráscsoportok eltávolításának automatizálása | Microsoft Docs"
description: "Egy Azure Automation-forgatókönyv PowerShell-munkafolyamati verziója, amely az előfizetéséhez tartozó összes erőforráscsoport eltávolítására szolgáló forgatókönyveket tartalmaz."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: 6aa3897d44b146483204d43e026d3d158c3e13b6


---
# <a name="azure-automation-scenario-automate-removal-of-resource-groups"></a>Azure Automation-forgatókönyv – erőforráscsoportok eltávolításának automatizálása
Számos ügyfél hoz létre több erőforráscsoportot. Vannak, amelyeket éles alkalmazások felügyeletéhez, és vannak, amelyeket fejlesztési, tesztelési és átmeneti környezetként használnak. Az ilyen erőforrások üzembe helyezésének automatizálása egy dolog, az erőforráscsoportok egyetlen kattintással történő üzemen kívül helyezése viszont egy teljesen más kérdés. Az Azure Automation használatával leegyszerűsítheti ezt a gyakori felügyeleti feladatot. Egy ilyen megoldás hasznos lehet, ha egy tagoknak szóló ajánlat (pl. MSDN vagy a Microsoft Partner Network Cloud Essentials programja) keretein belül egy költségkerettel rendelkező Azure-előfizetést használ.

Ez a forgatókönyv egy PowerShell-forgatókönyvön alapul, és arra szolgál, hogy egy vagy több megadott erőforráscsoportot távolítson el az előfizetésből. A forgatókönyv az alapértelmezett beállítás szerint tesztet hajt végre a továbblépés előtt. Így nem fogja véletlenül az erőforráscsoport törlését kezdeményezni, mielőtt teljes mértékben megbizonyosodna arról, hogy készen áll az eljárás végrehajtására.   

## <a name="getting-the-scenario"></a>A forgatókönyv beszerzése
Ez a forgatókönyv egy PowerShell-forgatókönyvet tartalmaz, amelyet a [PowerShell-katalógusból](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) tölthet le. Közvetlenül a [forgatókönyv-katalógusból](automation-runbook-gallery.md) is importálhatja az Azure Portalon.<br><br>

| Forgatókönyv | Leírás |
| --- | --- |
| Remove-ResourceGroup |Eltávolít egy vagy több Azure-erőforráscsoportot és a kapcsolódó forrásait az előfizetésből. |

<br>
Ehhez a forgatókönyvhöz a következő bemeneti paraméterek vannak meghatározva:

| Paraméter | Leírás |
| --- | --- |
| NameFilter (kötelező) |Megad egy névszűrőt a törölni kívánt erőforráscsoportok korlátozására. Vesszővel tagolt listaként több értéket is megadhat.<br>A szűrő nem különbözteti meg a kis- és nagybetűket, így a karakterláncot tartalmazó bármely erőforráscsoport esetében egyezést fog mutatni. |
| PreviewMode (választható) |Végrehajtja a forgatókönyvet, hogy láthassa, mely erőforráscsoportok törlődnének, de nem végzi el a műveletet.<br>Az alapértelmezett érték az **igaz**, így elkerülhető, hogy véletlenül törlődjön a forgatókönyvnek továbbadott egy vagy több erőforráscsoport. |

## <a name="install-and-configure-this-scenario"></a>A forgatókönyv telepítése és konfigurálása
### <a name="prerequisites"></a>Előfeltételek
Ez a forgatókönyv az [Azure-beli futtató fiók](automation-sec-configure-azure-runas-account.md) használatával végez hitelesítést.    

### <a name="install-and-publish-the-runbooks"></a>A forgatókönyv telepítése és közzététele
A forgatókönyvet letöltése után a [forgatókönyvek importálási eljárásait](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation) ismertető témakörben leírtak szerint importálhatja. Miután sikerrel importálta az Automation-fiókba, tegye közzé a forgatókönyvet.

## <a name="using-the-runbook"></a>A forgatókönyv használata
A következő lépések végigvezetik a forgatókönyv végrehajtásán, és megismertetik annak működésével. Ebben a példában csak a forgatókönyv tesztelését hajtja végre, az erőforráscsoportot valójában nem törli.  

1. Az Azure Portalon nyissa meg Automation-fiókját, majd kattintson a **Forgatókönyvek** elemre.
2. Válassza a **Remove-ResourceGroup** forgatókönyvet, majd kattintson az **Indítás** gombra.
3. A forgatókönyv elindításakor megnyílik a **Forgatókönyv indítása** panel, amelyen konfigurálhatja a paramétereket. Adja meg az előfizetéséhez tartozó erőforráscsoportok nevét, amelyekkel tesztet hajthat végre, és amelyek véletlen törlése nem okoz problémát.<br> ![A Remove-ResouceGroup paraméterei](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > A kiválasztott erőforráscsoport(ok) törlésének elkerülése érdekében győződjön meg arról, hogy a **Previewmode** beállítás értéke **true** (igaz).  **Vegye figyelembe**, hogy ez a forgatókönyv nem távolítja el a forgatókönyvet futtató Automation-fiókot tartalmazó erőforráscsoportot.  
   >
   >
4. Miután konfigurálta az összes paraméter értékét, kattintson az **OK** gombra, és a forgatókönyv a végrehajtási várólistára kerül.  

Ha meg szeretné tekinteni a **Remove-ResourceGroup** forgatókönyv-feladatot az Azure Portalon, válassza a forgatókönyv **Feladatok** elemét. A feladat összegzésében megtekinthetők a bemeneti paraméterek, a kimeneti stream, valamint a feladattal kapcsolatos általános információk és az előforduló kivételek.<br> ![A Remove-ResourceGroup forgatókönyv-feladat állapota](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

A **Feladat összegzése** a kimeneti, figyelmeztető és hibastreamek üzeneteit tartalmazza. A forgatókönyv végrehajtásának részletes eredményeinek megtekintéséhez válassza a **Kimenet** elemet.<br> ![A Remove-ResourceGroup forgatókönyv kimeneti eredményei](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Következő lépések
* A saját forgatókönyvek létrehozására vonatkozó első lépések leírását a [Forgatókönyv létrehozása vagy importálása az Azure Automationben](automation-creating-importing-runbook.md) témakör ismerteti.
* A PowerShell-alapú munkafolyamat-forgatókönyvekre vonatkozó első lépések leírását [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md) témakör ismerteti.



<!--HONumber=Nov16_HO2-->


