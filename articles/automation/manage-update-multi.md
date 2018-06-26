---
title: Több Azure-beli virtuális gép frissítéseinek kezelése
description: Ez a cikk ismerteti, hogyan kezelheti a frissítéseket az Azure virtuális gépek.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59a00f5605f7664148b65f2ec9a88fbaa9057ccf
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946057"
---
# <a name="manage-updates-for-multiple-machines"></a>Frissítések kezelése több gép esetén

A frissítés felügyeleti megoldás segítségével kezelheti a frissítések és javítások a Windows és Linux rendszerű virtuális gépek számára. Az [Azure Automation](automation-offering-get-started.md)-fiókból a következőket végezheti el:

- A bevezetni virtuális gépek
- Mérje fel a rendelkezésre álló frissítések állapotát
- Kötelező frissítések telepítésének ütemezése
- Győződjön meg arról, hogy frissítéseket sikeresen alkalmazása megtörtént-e az összes virtuális gép, amelynek felügyelete engedélyezve van a központi telepítés eredményeinek áttekintése

## <a name="prerequisites"></a>Előfeltételek

Frissítéskezelés használatához az alábbiak szükségesek:

- Azure Automation futtató fiók. A létrehozáshoz, lásd: [Ismerkedés az Azure Automation](automation-offering-get-started.md).
- Egy támogatott operációs rendszert futtató virtuális gépre vagy számítógépre.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Frissítéskezelés a következő operációs rendszereken támogatott:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Támogatja a értékelések csak frissítése.         |
|Windows Server 2008 R2 SP1 és újabb verziók     |Windows PowerShell 4.0-s vagy újabb rendszer szükséges. ([Töltse le a WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> A Windows PowerShell 5.1 megbízhatóbbak ajánlott. ([Töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 12.04 LTS, 14.04 LTS és 16.04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

> [!NOTE]
> Ahhoz, hogy Ubuntu rendszeren elkerülje a karbantartási időszakon kívüli frissítéstelepítést, konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. További információt az [Ubuntu kiszolgáló kézikönyvének Automatikus frissítések témakörében](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) talál.

A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.

Ez a megoldás, amely konfigurálva van a jelentés több Azure Log Analytics munkaterületekhez való Linux Operations Management Suite (OMS) ügynök nem támogatja.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Frissítéskezelés engedélyezése az Azure virtuális gépek

Az Azure portálon, nyissa meg az Automation-fiók, és válassza **frissítéskezelés**.

Válassza ki **Azure virtuális gépek hozzáadása**.

![Azure virtuális gép lap hozzáadása](./media/manage-update-multi/update-onboard-vm.png)

Válassza ki az előkészíteni kívánt virtuális gépet. 

A **engedélyezze Frissítéskezelésről**, jelölje be **engedélyezése** bevezetni a virtuális gép.

![Frissítéskezelés engedélyezése párbeszédpanel](./media/manage-update-multi/update-enable.png)

Amikor befejeződött az bevezetése, felügyelete engedélyezve van a virtuális gép.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Frissítéskezelés engedélyezése nem Azure virtuális gépek és a számítógépek

Megtudhatja, hogyan lehet engedélyezni az Azure Windows virtuális gépek és a számítógépek, lásd: [csatlakozás Windows-számítógépek számára az Azure Naplóelemzés szolgáltatás](../log-analytics/log-analytics-windows-agent.md).

Megtudhatja, hogyan lehet engedélyezni az Azure Linux virtuális gépek és a számítógépek, lásd: [a Linux számítógépek csatlakoztatása a Log Analyticshez](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Az Automation-fiók kapcsolódó számítógépek megtekintése

Miután engedélyezte a gépek felügyelete, gép információk is megtekinthetők kiválasztásával **számítógépek**. Vonatkozó információk láthatók *számítógépnév*, *megfelelőségi állapot*, *környezet*, *operációsrendszer-típus*, *kritikus és biztonsági frissítések telepítve*, *más telepített frissítések*, és *ügynök readiness frissíti* a számítógépekről.

  ![Számítógépek megtekintése lap](./media/manage-update-multi/update-computers-tab.png)

Számítógépek, amelyeken mostanában engedélyezve van a kezeléséhez előfordulhat, hogy nem volna értékelni még. Azon számítógépek esetén a megfelelőségi állapot állapota **nem sor**. Ez egy lista megfelelőségi állapot lehetséges értékek:

- **Megfelelő**: számítógépek, amelyek nem hiányzik-e kritikus vagy biztonsági frissítések.

- **Nem megfelelő**: számítógépek, amelyek hiányzik legalább egy kritikus vagy biztonsági frissítés.

- **Nem sor**: A frissítés megfelelőségvizsgálati adatai még nem érkezett a számítógép a várt időkereten belül. Linux-számítógépek esetén a várt időkeretre van, az elmúlt 3 óra. Windows rendszerű számítógépek esetén a várt időkeretre van, az utolsó 12 órában.

Válassza ki, ha az ügynök állapotát a hivatkozásra a **frissítési ÜGYNÖK KÉSZÜLTSÉGI** oszlop. Ez a beállítás kiválasztásával megnyílik a **Hibridfeldolgozó** ablaktáblán, és a hibrid feldolgozó állapotát jeleníti meg. Az alábbi ábrán egy olyan ügynök, amely még nincs csatlakoztatva a frissítéskezelés hosszabb ideig példát:

![Számítógépek megtekintése lap](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Frissítéskezelés engedélyezése után a **frissítéskezelés** ablaktábla megnyitása. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="collect-data"></a>Adatok gyűjtése

Virtuális gépek és a számítógépeken telepített ügynökök frissítésével kapcsolatos adatok gyűjtéséhez. Az ügynökök Azure frissítéskezelés elküldi az adatokat.

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |Frissítéskezelés rendszer frissítésével kapcsolatos információkat gyűjti össze a Windows-ügynökök, és ezután kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |Frissítéskezelés rendszer frissítésével kapcsolatos információkat gyűjti össze az Linux-ügynököt, és ezután kezdeményezi a támogatott disztribúcióiról kötelező frissítések telepítésének. |
| Az Operations Manager felügyeleti csoportja |Igen |Frissítéskezelés rendszerfrissítések adatokat gyűjt az ügynökök a csatlakoztatott felügyeleti csoport. |
| Azure Storage-fiók |Nem |Az Azure Storage nem tartalmazza a rendszer frissítésével kapcsolatos információkat. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

A vizsgálat futtatása az egyes felügyelt Windows-számítógépek naponta kétszer. A Windows API hívása 15 percenként lekérdezze a meghatározásához, hogy állapota megváltozott az utolsó frissítés időpontja. Ha módosította az állapotát, a megfelelőségi vizsgálat indítása. A vizsgálatok az egyes felügyelt Linux rendszerű számítógépek 3 óránként fut.

30 perc és az irányítópult származó felügyelt számítógépek frissített adatokat 6 óra között is igénybe vehet.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Frissítések telepítéséhez ütemezése központi telepítések, a kiadás ütemezés és a szolgáltatás ablak igazodik. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Egy új központi telepítés egy vagy több virtuális gépek esetén a ütemezése **frissítéskezelés**, jelölje be **ütemezés központi telepítésének**.

Az a **új üzemelő példány frissítése** panelen adja meg a következőket:

- **Név**: Adja meg a központi telepítést egy egyedi nevét.
- **Operációs rendszer**: válasszon **Windows** vagy **Linux**.
- **Gépek frissítése**: válassza ki a frissíteni kívánt virtuális gépeket. A számítógép készenléti látható a **frissítési ÜGYNÖK KÉSZÜLTSÉGI** oszlop. Ütemezheti a frissítés telepítése előtt a gép állapotát tekintheti meg.

  ![Új központi telepítési ablaktábla](./media/manage-update-multi/update-select-computers.png)

- **Frissítés besorolása**: ahhoz, hogy a központi telepítési szerepeljen szoftvertípusok kiválasztása. A besorolási típusú ismertetését lásd: [frissítési besorolások](automation-update-management.md#update-classifications). A választható besorolási típusok a következők:
  - Kritikus frissítések
  - Biztonsági frissítések
  - Kumulatív frissítések
  - Funkciócsomagok
  - Szervizcsomagok
  - Definíciófrissítések
  - Eszközök
  - Frissítések

- **Frissítések kizárandó**: Ez a beállítás kiválasztásával megnyílik a **kizárása** lap. Adja meg a kapcsolatos tudásbáziscikkeket vagy kizárása nevek.

- **Ütemezési beállítások**: Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, Egy másik idő is megadható.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ismétlődő ütemezés szerint, a beállítása **ismétlődési**, jelölje be **ismétlődő**.

   ![Ütemezési beállítások párbeszédpanel](./media/manage-update-multi/update-set-schedule.png)
- **Karbantartási időszak (perc)**: Adja meg az időszakot, amelyet a frissítés telepítése megtörténik. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

Amikor végzett az ütemezés konfigurálása, válassza ki a **létrehozása** gombra kattintva visszatérhet a állapota irányítópult. A **ütemezett** táblázat mutatja a központi telepítési ütemezés létrehozott.

> [!WARNING]
> A számítógép újraindítását igénylő frissítések a virtuális gép automatikusan újraindul.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Az ütemezett telepítés elindulása után megtekintheti az adott központi telepítés állapota a **telepítésének folyamatát** lap **frissítéskezelés**.

Ha a telepítés fut, az állapota **Folyamatban**. A telepítés sikeres befejezése után a állapota **sikeres**.

Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, a telepítés állapota **Részben sikertelen**.

![A frissítéstelepítés állapota](./media/manage-update-multi/update-view-results.png)

Adott frissítéstelepítés irányítópultjának megtekintéséhez válassza ki a befejezett telepítést.

A **frissítésének elmulasztása az** ablaktábla megjeleníti azokat a frissítéseket és a telepítés eredményeit a virtuális gép teljes száma. A jobb oldali tábla minden egyes frissítés és a telepítés eredményét részletes információkat biztosít. A telepítési eredmények a következő értékek lehetnek:

- **Nem történt kísérlet**: A frissítés nem lett telepítve, mert nincs elég ideje volt elérhető a megadott karbantartási ablak alapján.
- **Sikeres**: A frissítés telepítése sikeres volt.
- **Nem sikerült**: A frissítés nem sikerült.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza a **Minden napló** elemet.

A feladat adatfolyam a runbook, amely felügyeli a frissítés telepítése a cél virtuális gépen, jelölje ki a kimeneti csempén.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>További lépések

- További információ a Frissítéskezelésről, beleértve a naplókat, kimeneti és hibák, lásd: [frissítés felügyeleti megoldás az Azure-ban](../operations-management-suite/oms-solution-update-management.md).
