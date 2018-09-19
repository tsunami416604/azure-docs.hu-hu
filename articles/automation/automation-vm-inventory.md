---
title: Azure-beli virtuális gépek kezelése leltár használatával | Microsoft Docs
description: Virtuális gép kezelése leltár használatával
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: leltár, automatizálás, változás, követés
author: jennyhunter-msft
ms.author: jehunte
ms.date: 03/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30569c3a89de320769d433b5b3a4af9cf4e08e66
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "35645273"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Azure-beli virtuális gépek kezelése leltár használatával

A leltár nyomon követése az egyes Azure-beli virtuális gépeken a gép erőforráslapján engedélyezhető. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a leltározás beállításához és konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ez a cikk feltételezi, hogy a megoldás konfigurálása a virtuális gép. Ha nem rendelkezik Azure-virtuális géppel, [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Leltározás engedélyezése a virtuális gép erőforrásainak lapjáról

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listáról.
3. Az a **erőforrás** menü alatt **műveletek**, jelölje be **készlet**.
4. Válasszon ki egy Log Analytics-munkaterületet az adatnaplók tárolásához.
    Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot.
5. A számítógép előkészítéséhez válassza az **Engedélyezés** lehetőséget.

   ![Előkészítési lehetőségek megtekintése](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet. Ez idő alatt bezárhatja az ablakot, vagy Ön nyitva hagyja, és értesíti a felhasználót, amikor a megoldás engedélyezve lett. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![A leltármegoldás megjelenítése közvetlenül az előkészítés után](./media/automation-vm-inventory/inventory-onboarded.png)

Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. A rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. Az a **készlet** nézetben válassza a **beállításainak szerkesztése** gombra az ablak tetején.
2. Új leltározási beállítás hozzáadásához lépjen a hozzáadni kívánt kategóriára a **Windows beállításjegyzék**, a **Windows-fájlok** és a **Linux-fájlok** lapok használatával.
3. Válassza ki a megfelelő kategóriát, és kattintson a **Hozzáadás** az ablak tetején.

Az alábbi táblázatok ismertetik minden állítható be a különböző kategóriák tulajdonság.

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

## <a name="manage-machine-groups"></a>Gép csoportok kezelése

Készlet létrehozása és gép csoportok megtekintése a Log Analytics lehetővé teszi. Gépcsoportok egy lekérdezést a Log Analytics által meghatározott gépek gyűjteményei.

A gép select groups nézetre a **csoportok Machine** fülre a készlet oldalon.

![A készlet oldalon gép csoportok megtekintése](./media/automation-vm-inventory/inventory-machine-groups.png)

Egy gép csoportot a listából nyitja meg a gép csoportok lapon. Ezen a lapon látható a gép csoport adatait. Ilyen információ például a log analytics-lekérdezés, amely a csoport meghatározására szolgál. A lap alján az a csoport részét képező gépeket lapozható listája.

![Gép csoport lap megtekintése](./media/automation-vm-inventory/machine-group-page.png)

Kattintson a **+ Klónozás** klónozásához a gép csoport gombra. Itt meg kell adnia a csoport egy új nevet és egy alias a csoporthoz. A definíció jelenleg is módosítható. A lekérdezés nyomja meg módosítása után **lekérdezések érvényesítés** az előzetes verzióra, hogy ki kellene választani a gépek. Ha elégedett a csoport kattintson **létrehozás** a gép csoport létrehozása

Ha azt szeretné, hozzon létre egy új mchine csoportot, válassza ki a **+ hozzon létre egy gép csoportot**. Ezzel a gombbal megnyithatja a **hozzon létre egy gép csoportlapot** ahol megadhatja az új csoporthoz. Kattintson a **létrehozás** a csoport létrehozásához.

![Új számítógépcsoport létrehozása](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>A virtuális gép kezelésének megszüntetése

A virtuális gép eltávolítása a leltárkezelésből:

1. Az Azure Portal bal oldali paneljén kattintson a **Log Analytics** elemre, majd válassza ki a virtuális gép előkészítése során használt munkaterületet.
2. Nyissa meg a **Log Analytics** ablakot, majd válassza a **Virtuális gépek** lehetőséget az **Erőforrás** menü **Munkaterület adatforrásai** kategóriájában.
3. A listában válassza ki a leválasztani kívánt virtuális gépeket. A virtuális gép mellett egy zöld pipa és az **Ez a munkaterület** felirat jelenik meg az **OMS-kapcsolat** oszlopban.
4. A következő lap tetején válassza a **Leválasztás** lehetőséget.
5. A megerősítési ablakban válassza a **Igen** lehetőséget.
    Ezzel a művelettel megszünteti a gép kezelését.

## <a name="next-steps"></a>További lépések

* A virtuális gépek fájl- és beállításjegyzék-beállításainak módosításával kapcsolatos információkért lásd: [A szoftvermódosítások nyomon követése saját környezetében a Change Tracking megoldás használatával](../log-analytics/log-analytics-change-tracking.md).
* Windows- és a virtuális gépek csomagfrissítéseinek kezelésével kapcsolatos tudnivalókért lásd: [a frissítéskezelési megoldás az Azure-ban](../operations-management-suite/oms-solution-update-management.md).
