---
title: Azure-beli virtuális gépek kezelése leltár használatával | Microsoft Docs
description: Virtuális gép kezelése leltár használatával
services: automation
ms.subservice: change-inventory-management
keywords: leltár, automatizálás, változás, követés
ms.date: 02/06/2019
ms.topic: conceptual
ms.openlocfilehash: c5ac5cb1b1f02ea1e8b7eccb65fba978c99cc4f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75419816"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Azure-beli virtuális gépek kezelése leltár használatával

A leltár nyomon követése az egyes Azure-beli virtuális gépeken a gép erőforráslapján engedélyezhető. Összegyűjtheti a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok listáját, és leltárt készíthet belőlük. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a leltározás beállításához és konfigurálásához.

## <a name="before-you-begin"></a>Előzetes teendők

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ez a cikk feltételezi, hogy rendelkezik egy virtuális géppel a megoldás konfigurálásához. Ha nem rendelkezik Azure-virtuális géppel, [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Leltározás engedélyezése a virtuális gép erőforrásainak lapjáról

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listáról.
3. Az **erőforrás** menüben, a **műveletek**területen válassza a **leltár**elemet.
4. Válasszon ki egy Log Analytics munkaterületet az adatnaplók tárolásához.
    Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot.
5. A számítógép előkészítéséhez válassza az **Engedélyezés** lehetőséget.

   ![Előkészítési lehetőségek megtekintése](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet. Ebben az időszakban lezárhatja az ablakot, vagy megnyithatja, és értesítést kaphat, ha a megoldás engedélyezve van. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![A leltármegoldás megjelenítése közvetlenül az előkészítés után](./media/automation-vm-inventory/inventory-onboarded.png)

Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. A rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. A **leltár** nézetben válassza a **beállítások szerkesztése** gombot az ablak tetején.
2. Új leltározási beállítás hozzáadásához lépjen a hozzáadni kívánt kategóriára a **Windows beállításjegyzék**, a **Windows-fájlok** és a **Linux-fájlok** lapok használatával.
3. Válassza ki a megfelelő kategóriát, majd kattintson a **Hozzáadás** gombra az ablak tetején.

A következő táblázatok a különböző kategóriákhoz konfigurálható egyes tulajdonságokra vonatkozó információkat tartalmazzák.

### <a name="windows-registry"></a>Windows-beállításjegyzék

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Windows-beállításkulcs   | A fájl elérési útja, például: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows-fájlok

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Elérési út megadása     | A fájl elérési útja, például: „c:\temp\myfile.txt”

### <a name="linux-files"></a>Linux-fájlok

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Elérési út megadása     | A fájl elérési útja, például: „/etc/*.conf”       |
|Elérési út típusa     | Írja be a követni kívánt elem típusát. A lehetséges értékek a Fájl és a Címtár        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Mellőzés** – Mellőzi a szimbolikus hivatkozásokat, és nem szerepelteti a hivatkozott fájlokat/címtárakat<br>**Követés** – Követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat<br>**Kezelés** – Követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom kezelésének módosítását      |

## <a name="manage-machine-groups"></a>Számítógépcsoportok kezelése

A leltár lehetővé teszi, hogy Azure Monitor naplókban hozzon létre és tekintse meg a számítógépcsoportokat. A számítógépcsoportok olyan gépek gyűjteményei, amelyeket Azure Monitor naplók lekérdezése határoz meg.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A számítógépcsoportok megtekintéséhez válassza a **Machine groups (számítógép-csoportok** ) lapot a leltár lapon.

![Számítógépcsoportok megtekintése a leltár lapon](./media/automation-vm-inventory/inventory-machine-groups.png)

Ha kijelöl egy gépcsoport elemet a listából, megnyílik a számítógép-csoportok lap. Ez az oldal a számítógépcsoport részleteit jeleníti meg. Ezek az adatok tartalmazzák a csoport definiálásához használt log Analytics-lekérdezést. A lap alján található a csoport részét képező gépek lapozható listája.

![Számítógép-csoport megtekintése lap](./media/automation-vm-inventory/machine-group-page.png)

A számítógép csoport klónozásához kattintson a **+ klónozás** gombra. Itt meg kell adnia a csoport új nevét és aliasnevét. A definíció most már módosítható. A lekérdezés módosítása után nyomja le a **lekérdezés ellenőrzése** elemet a kijelölni kívánt gépek előnézetének megtekintéséhez. Ha elégedett a csoporttal, kattintson a **Létrehozás** elemre a számítógépcsoport létrehozásához.

Ha új számítógépcsoportot szeretne létrehozni, válassza **a + számítógépcsoport létrehozása**lehetőséget. Ezzel a gombbal megnyílik a **számítógép-csoport létrehozása lap** , amelyen megadhatja az új csoportot. A csoport létrehozásához kattintson a **Létrehozás** parancsra.

![Új számítógépcsoport létrehozása](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>A virtuális gép kezelésének megszüntetése

A virtuális gép eltávolítása a leltárkezelésből:

1. Az Azure Portal bal oldali paneljén kattintson a **Log Analytics** elemre, majd válassza ki a virtuális gép előkészítése során használt munkaterületet.
2. Nyissa meg a **Log Analytics** ablakot, majd válassza a **Virtuális gépek** lehetőséget az **Erőforrás** menü **Munkaterület adatforrásai** kategóriájában.
3. A listában válassza ki a leválasztani kívánt virtuális gépeket. A virtuális gép mellett egy zöld pipa és az **Ez a munkaterület** felirat jelenik meg az **OMS-kapcsolat** oszlopban.

   >[!NOTE]
   >A OMS már Azure Monitor naplók néven is ismert.
   
4. A következő lap tetején válassza a **Leválasztás** lehetőséget.
5. A megerősítési ablakban válassza a **Igen** lehetőséget.
    Ezzel a művelettel megszünteti a gép kezelését.

## <a name="next-steps"></a>Következő lépések

* A virtuális gépek fájl- és beállításjegyzék-beállításainak módosításával kapcsolatos információkért lásd: [A szoftvermódosítások nyomon követése saját környezetében a Change Tracking megoldás használatával](../log-analytics/log-analytics-change-tracking.md).
* A Windows és a csomagok frissítéseinek virtuális gépeken történő kezeléséről [a Update Management megoldás az Azure-ban](../operations-management-suite/oms-solution-update-management.md)című témakörben olvashat bővebben.

