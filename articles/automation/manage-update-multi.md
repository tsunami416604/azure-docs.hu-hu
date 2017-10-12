---
title: "Több Azure-beli virtuális gép frissítéseinek kezelése | Microsoft Docs"
description: "Készítsen elő Azure-beli virtuális gépeket frissítések kezelésére."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Több Azure-beli virtuális gép frissítéseinek kezelése

A frissítéskezelés segítségével kezelheti az Azure-beli virtuális gépek frissítéseit és javításait.
[Azure Automation](automation-offering-get-started.md)-fiókjából gyorsan előkészítheti a virtuális gépeket, felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése az összes virtuális gépen, amelyen engedélyezve van a frissítéskezelés.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
* Egy Azure Resource Manager-alapú virtuális gépre (nem klasszikus). A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Frissítéskezelés engedélyezése Azure-beli virtuális gépeken

1. Az Azure Portalon nyissa meg az Automation-fiókot.
2. A képernyő bal oldalán válassza a **Frissítéskezelés** elemet.
3. A képernyő felső részén kattintson az **Azure-beli virtuális gép hozzáadása** elemre.
    ![Virtuális gépek előkészítése](./media/manage-update-multi/update-onboard-vm.png)
4. Válassza ki az előkészíteni kívánt virtuális gépet. Megjelenik a **Frissítéskezelés engedélyezése** képernyő.
5. Kattintson az **Engedélyezés** gombra.

   ![Frissítéskezelés engedélyezése](./media/manage-update-multi/update-enable.png)

A frissítéskezelés engedélyezve van a virtuális gépen.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek.
Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést egy vagy több virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítéstelepítés ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következőket:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
* **Operációs rendszer típusa** – Válassza ki a Windows vagy a Linux lehetőséget.
* **Frissíteni kívánt számítógépek** – Válassza ki a frissíteni kívánt virtuális gépeket.

  ![A frissíteni kívánt virtuális gépek kiválasztása](./media/manage-update-multi/update-select-computers.png)

* **Frissítési besorolás** – Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés belefoglal a telepítésbe. A választható besorolási típusok a következők:
  * Kritikus frissítések
  * Biztonsági frissítések
  * Kumulatív frissítések
  * Funkciócsomagok
  * Szervizcsomagok
  * Definíciófrissítések
  * Eszközök
  * Frissítések
* **Ütemezési beállítások** – Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot.
   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ha ismétlődő ütemezést szeretne beállítani, az Ismétlődés alatt kattintson az Ismétlődő lehetőségre.

   ![A frissítés ütemezés beállításai képernyője](./media/manage-update-multi/update-set-schedule.png)

* **Karbantartási időszak (perc)** – Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen.  Ez biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az imént létrehozott telepítési ütemezés.

> [!WARNING]
> Az újraindítást igénylő frissítések esetében a virtuális gép automatikusan újraindul.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.

![A frissítéstelepítés állapota ](./media/manage-update-multi/update-view-results.png)

Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* Nem lett megkísérelve – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* Sikeres – a frissítés sikeres volt.
* Sikertelen – a frissítés sikertelen volt.

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="next-steps"></a>Következő lépések

* További információk a frissítéskezelésről: [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md).