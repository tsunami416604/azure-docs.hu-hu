---
title: Az Azure Automation forgatókönyv- és minták
description: Forgatókönyveit és moduljait a Microsoft és a Közösség érhetők el, telepítése és használata az Azure Automation-környezetben.  Ez a cikk ismerteti, hogyan férhet hozzá ezekhez az erőforrásokhoz, és a runbookok a gyűjteményébe hozzájárulás.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 61d267898bab5e5df394d4097eacb33ada809518
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Az Azure Automation forgatókönyv- és minták
Ahelyett, hogy a saját forgatókönyveit és moduljait létrehozása az Azure Automationben, különféle forgatókönyvekhez, amik már Microsoft és a Közösség által készített végezheti el.  Használja a következő használati helyzetekben módosítás nélkül, vagy használhatja őket a kiindulási pontként, és szerkesztheti azokat az adott igények szerint.

A runbookok kaphat a [forgatókönyvek](#runbooks-in-runbook-gallery) és a modulok a [PowerShell-galériában](#modules-in-powerShell-gallery).  Meg is hozzájárulhat a közösségi ossza meg, hogy a most kialakított forgatókönyvek.

## <a name="runbooks-in-runbook-gallery"></a>Runbookok a Runbook-galériában
A [forgatókönyvek](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) runbookok különféle biztosít a Microsoft és a Közösség, amely az Azure Automation importálhatja. A gyűjteményből, amely által egy runbook vagy letöltheti a [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload), vagy a runbookok az Azure portálon gyűjteményből közvetlenül importálhatók.

Csak importálhatja a Runbook gyűjteményből közvetlenül az Azure portál használatával. Ez a funkció a Windows PowerShell használatával nem hajtható végre.

> [!NOTE]
> Ellenőriznie kell, hogy a Runbook-galériából beolvasása, és járjon el körültekintően telepítése, és futtassa azokat éles környezetben a runbook tartalmát.
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Runbook importálása a Runbook-galériából és az Azure portál
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **folyamat**, kattintson a **Runbookok gyűjteménye**
3. Keresse meg a gyűjteményelem szeretne, és válassza ki azt a részletek megtekintéséhez. A bal oldali további keresési paraméterek megadása a gyártó és típus.
   
    ![Keresse meg a gyűjtemény](media/automation-runbook-gallery/browse-gallery.png)
5. Kattintson a **nézet forráskódú projektként** elem megtekintéséhez a [TechNet Script Center](http://gallery.technet.microsoft.com/).
6. Szeretne importálni egy elemet, kattintson a részletek megtekintéséhez, és kattintson a **importálása** gombra.
   
    ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)
7. Szükség esetén változtassa meg a runbook nevét, és kattintson a **OK** a runbook importálásához.
8. A runbook jelenik meg a **Runbookok** fülre az Automation-fiókhoz.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Egy runbook ad hozzá a runbook gyűjteménye
A Microsoft javasolja, hogy a runbookok hozzáadni a Runbook gyűjteménye, amelyek úgy gondolja, hogy más ügyfelek számára hasznos lehet.  Hozzáadhat egy runbook által [ismét feltölteni a Script Center](http://gallery.technet.microsoft.com/site/upload) figyelembe véve a következő részletekkel.

* Meg kell adnia *Windows Azure* a a **kategória** és *Automation* a a **alkategóriát** is megjelennek a runbook a varázsló.  
* A feltöltés egyetlen .ps1 vagy .graphrunbook fájlnak kell lennie.  Ha a runbook van szüksége, bármilyen modulok, gyermekforgatókönyvek vagy eszközök, majd sorolja fel azokat a küldése leírása és a runbook megjegyzéseket tartalmazó részét.  Ha több runbook igénylő környezettel rendelkezik, külön-külön feltöltése minden egyes, és az felsorolja a kapcsolódó runbookokat az egyes azok leírásait tartalmazza. Győződjön meg arról, hogy így azok megjelennek a ugyanilyen kategóriájú használja ugyanazt a címkét. A felhasználónak kell tudnia, hogy szükség-e más runbookokat leírása olvasható a forgatókönyv működjön.
* Adja hozzá a következő címke: "GraphicalPS" közzétételekor a **grafikus forgatókönyvnek** (ez nem egy grafikus munkafolyamat). 
* A leírás használata a PowerShell vagy a PowerShell-munkafolyamati kódrészletet beszúrása **helyezze be a kódját a szakasz** ikonra.
* A feltöltésének megjelennek a forgatókönyvek eredmények, a felhasználó azonosítására a funkciók a runbook részletes információkat kell biztosítania.
* A feltöltés egy-három a következő címkék kell hozzárendelni.  A runbook, a varázsló a a kategóriák, amelyek megfelelnek a címkék szerepel.  A varázsló a listában nem szereplő címkéket figyelmen kívül hagyja. A címke nem adja meg, ha a runbook szerepel a kategóriában.
  
  * Backup
  * Kapacitáskezelés
  * Változáskezelés
  * Megfelelőség
  * Fejlesztési / tesztelési környezetben
  * Vészhelyreállítás
  * Figyelés
  * Javítás
  * Kiépítés
  * Szervizelés
  * Virtuális gép életciklusának kezelésére
* Automation frissíti a gyűjtemény óránként egyszer, ezért az nem fog a hozzájárulások azonnal.

## <a name="modules-in-powershell-gallery"></a>A PowerShell-galériában modulok
PowerShell-modul a runbookokban használható parancsmagokat tartalmaznak, és az Azure Automationben telepítése meglévő modulok érhetők el a [PowerShell-galériában](http://www.powershellgallery.com).  Indítsa el ezt a tárat, Azure-portálról, és közvetlenül az Azure Automation a telepítést, vagy letöltheti a fájlokat, és manuális telepítése.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Modul importálása Automation-modul galériából és az Azure portál
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki **modulok** alatt **megosztott erőforrások** modulok listájának megnyitásához.
4. Kattintson a **Tallózás gallery** az oldal tetején.
   
    ![A modul gyűjteménye](media/automation-runbook-gallery/modules-blade.png) <br>
5. Az a **Tallózás gyűjtemény** lapon úgy keresheti meg a következő mezőket:
   
   * Modulnév
   * Címkék
   * Szerző
   * A parancsmag/DSC-erőforrás neve
6. Keresse meg a modul, amely továbbra is érdekli, és válassza ki azt a részletek megtekintéséhez.  
   Ha egy adott modult elemezze, további információkat láthat egy hivatkozást a PowerShell-galériában, beleértve a modulban kapcsolatos el a szükséges függőségek, és az összes, a parancsmagok és/vagy a DSC-erőforrások, amely a modul tartalmazza.
   
    ![PowerShell modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Közvetlenül az Azure Automation-modul telepítéséhez, kattintson a **importálási** gombra.
8. Ha az Importálás gombra kattint, a a **importálása** ablaktáblán látható a neve, amely az importálni kívánt. Ha az összes függősége telepítve van, a **OK** gomb aktiválásakor. Ha függőségek hiányzik, azokat importálni, ez a modul importálása előtt szeretné.
9. Az a **importálása** kattintson **OK** a modul importálásához. Azure Automation importálja a modult, a fiókjához, amíg a modul és a parancsmagokkal kapcsolatos metaadatok bontja ki. Ez eltarthat néhány percig, mivel minden tevékenység kinyerni kell.
10. Befejezését követően, hogy a modul telepítése értesítés és egy másik értesítést kapni.
11. A modul az importálása után megjelenik az elérhető tevékenységeket, és használhatja az erőforrásokat a runbookok és a célállapot-konfiguráció.

> [!NOTE]
> PowerShell core támogató modulok nem támogatottak az Azure Automationben, és nem importálhatók az Azure portálon, vagy közvetlenül a PowerShell-galériából telepítve.

## <a name="requesting-a-runbook-or-module"></a>A kért egy runbook vagy modul
Kéréseket küldhet [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Ha a szükséges súgó írása egy runbookot, vagy PowerShell kapcsolatos kérdése van, küldéséhez a [fórum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>További lépések
* Első lépésként a runbookokkal, lásd: [létrehozása vagy egy Azure Automation forgatókönyv importálása](automation-creating-importing-runbook.md)
* Runbookok PowerShell és a PowerShell-munkafolyamatok közötti különbségek ismertetése: [tanulási PowerShell munkafolyamat](automation-powershell-workflow.md)

