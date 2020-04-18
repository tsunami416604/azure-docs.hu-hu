---
title: Azure-beli virtuális gépek kezelése leltár használatával | Microsoft Docs
description: Virtuális gép kezelése leltár használatával
services: automation
ms.subservice: change-inventory-management
keywords: leltár, automatizálás, változás, követés
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617364"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Azure-beli virtuális gépek kezelése leltár használatával

A leltár nyomon követése az egyes Azure-beli virtuális gépeken a gép erőforráslapján engedélyezhető. A számítógépen a következő készletadatokat gyűjtheti és tekintheti meg:

- Windows-szoftverek (Windows-alkalmazások és Windows-frissítések), szolgáltatások, fájlok és beállításkulcsok
- Linux szoftver (csomagok) démonok és fájlok

Ez a módszer egy böngészőalapú felhasználói felületet biztosít a leltározás beállításához és konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

Ez a cikk feltételezi, hogy rendelkezik egy virtuális gép konfigurálása a megoldás. Ha nem rendelkezik Azure-virtuális géppel, [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Leltározás engedélyezése a virtuális gép erőforrásainak lapjáról

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listáról.
3. Az **Erőforrás** menü **Műveletek**területén válassza a **Készlet**lehetőséget.
4. Válassza ki a Log Analytics munkaterületet az adatnaplók tárolásához.
    Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot.
5. A számítógép előkészítéséhez válassza az **Engedélyezés** lehetőséget.

   ![Előkészítési lehetőségek megtekintése](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet. Ez alatt az idő alatt bezárhatja az ablakot, vagy nyitva tarthatja, és értesíti, ha a megoldás engedélyezve van. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![A leltármegoldás megjelenítése közvetlenül az előkészítés után](./media/automation-vm-inventory/inventory-onboarded.png)

Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. A rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. A Készlet lapon kattintson a lap tetején a **Beállítások szerkesztése** gombra.
2. Új gyűjteménybeállítás hozzáadásához lépjen a hozzáadni kívánt beállítási kategóriára a **Windows rendszerleíró adatbázis,** a **Windows-fájlok**vagy a **Linux-fájlok** lap kiválasztásával.
3. Jelölje ki a megfelelő kategóriát, és kattintson a lap tetején a **Hozzáadás** gombra.

Az alábbi táblázatok a különböző kategóriákhoz konfigurálható minden tulajdonságról tartalmaznak információt.

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
|Engedélyezve     | Igaz, ha a beállítás van alkalmazva, és hamis egyébként.        |
|Elem neve     | A követendő fájl rövid neve.        |
|Csoport     | A fájlok logikai csoportosításának csoportneve.       |
|Elérési út megadása     | A fájl ellenőrzésének elérési útja, például **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux fájlok

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Igaz, ha a beállítás van alkalmazva, és hamis egyébként.        |
|Elem neve     | A követendő fájl rövid neve.        |
|Csoport     | A fájlok logikai csoportosításának csoportneve.        |
|Elérési út megadása     | A fájl ellenőrzésének elérési útja, például **/etc/*.conf**.       |
|Elérési út típusa     | A nyomon követendő elem típusa. Az értékek a Fájl és a Könyvtár.        |
|Rekurzió     | Igaz, ha rekurziót használ, amikor a nyomon követendő elemet keresi, és a Hamis egyéb.        |
|Sudo használata     | Igaz, ha sudo az elem ellenőrzése, és hamis egyébként.         |
|Hivatkozások     | Érték, amely azt jelzi, hogy a szimbolikus hivatkozásokat hogyan kezelik a könyvtárak bejárásakor. Lehetséges értékek: <br> Mellőzés – Mellőzi a szimbolikus hivatkozásokat, és nem szerepelteti a hivatkozott fájlokat/címtárakat<br>Követés – Követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat<br>Kezelés – Követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom kezelésének módosítását      |

## <a name="manage-machine-groups"></a>Gépcsoportok kezelése

A leltár lehetővé teszi, hogy gépcsoportokat hozzon létre és tekintsen meg az Azure Monitor naplóiban. A gépcsoportok az Azure Monitor naplóiban lekérdezés által meghatározott gépek gyűjteményei.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A számítógépcsoportok megtekintéséhez válassza a **Gépcsoportok** lapot a Készlet lapon.

![Gépcsoportok megtekintése a készletlapon](./media/automation-vm-inventory/inventory-machine-groups.png)

Ha kiválaszt egy gépcsoportot a listából, megnyílik a Gépcsoportok lap. Ez az oldal a gépcsoport részleteit jeleníti meg. Ezek a részletek tartalmazzák a csoport definiálására használt naplóelemzési lekérdezést. A lap alján található a csoport részét játszó gépek lapozható listája.

![Gépcsoport lap megtekintése](./media/automation-vm-inventory/machine-group-page.png)

Kattintson **+ Klón klón** klóna a gépcsoport. Új nevet és aliast kell adnia a csoportnak a csoportszámára. A definíció jelenleg módosítható. A lekérdezés módosítása után kattintson a **Lekérdezés ellenőrzése gombra** a kijelölt gépek előnézetének megtekintéséhez. Ha elégedett a csoporttal, kattintson a **Létrehozás gombra** a gépcsoport létrehozásához.

Ha új gépcsoportot szeretne létrehozni, kattintson **a + Gépcsoport létrehozása gombra.** Ez a gomb megnyitja a **Gépcsoport létrehozása** lapot, ahol meghatározhatja az új csoportot. A csoport létrehozásához kattintson a **Létrehozás** parancsra.

![Új gépcsoport létrehozása](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>A virtuális gép kezelésének megszüntetése

A virtuális gép eltávolítása a leltárkezelésből:

1. Az Azure Portal bal oldali paneljén kattintson a **Log Analytics** elemre, majd válassza ki a virtuális gép előkészítése során használt munkaterületet.
2. A Log Analytics lapon nyissa meg az **Erőforrás menüt.**
3. A Munkaterület **adatforrásai**csoportban válassza a **Virtuális gépek** lehetőséget.
4. A listában válassza ki a leválasztani kívánt virtuális gépeket. A virtuális gép mellett egy zöld pipa és az **Ez a munkaterület** felirat jelenik meg az **OMS-kapcsolat** oszlopban.

   >[!NOTE]
   >Az Operations Management Suite (OMS) mostantól Azure Monitor-naplóknak nevezi.
   
5. A következő lap tetején kattintson a **Kapcsolat bontása gombra.**
6. A megerősítést kérő ablakban kattintson az **Igen** gombra a gép és a felügyelet leválasztásához.

## <a name="next-steps"></a>További lépések

* A virtuális gépek fájl- és beállításjegyzék-beállításainak módosításával kapcsolatos információkért lásd: [A szoftvermódosítások nyomon követése saját környezetében a Change Tracking megoldás használatával](../log-analytics/log-analytics-change-tracking.md).
* A Windows és a virtuális gépek frissítéseinek kezeléséről [az Azure frissítéskezelési megoldása című témakörben](../operations-management-suite/oms-solution-update-management.md)olvashat.