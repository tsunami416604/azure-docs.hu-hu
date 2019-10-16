---
title: Több Azure-beli virtuális gép frissítéseinek kezelése
description: Ez a cikk az Azure-beli virtuális gépek frissítéseinek kezelését ismerteti.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 367a4409c004c98cc4b5ec844aab5b05ec74abcb
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374494"
---
# <a name="manage-updates-for-multiple-machines"></a>Frissítések kezelése több gép esetén

A Update Management megoldással kezelheti a Windows-és Linux-alapú virtuális gépek frissítéseit és javításait. Az [Azure Automation](automation-offering-get-started.md)-fiókból a következőket végezheti el:

- Virtuális gépek előkészítése
- Az elérhető frissítések állapotának felmérése
- A szükséges frissítések telepítésének ütemezett időpontja
- Tekintse át a telepítési eredményeket annak ellenőrzéséhez, hogy a frissítések alkalmazása sikeres volt-e az összes olyan virtuális gépen, amelyhez Update Management engedélyezve van

## <a name="prerequisites"></a>Előfeltételek

A Update Management használatához a következőkre lesz szüksége:

- Egy támogatott operációs rendszert futtató virtuális gépre vagy számítógépre.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Update Management a következő operációs rendszereken támogatott:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | A csak a frissítési felméréseket támogatja.         |
|Windows Server 2008 R2 SP1 és újabb verziók     |A Windows PowerShell 4,0-es vagy újabb verziójára van szükség. ([WMF 4,0 letöltése](https://www.microsoft.com/download/details.aspx?id=40855))</br> A fokozott megbízhatóság érdekében ajánlott a Windows PowerShell 5,1. ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

> [!NOTE]
> Ahhoz, hogy Ubuntu rendszeren elkerülje a karbantartási időszakon kívüli frissítéstelepítést, konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. További információt az [Ubuntu kiszolgáló kézikönyvének Automatikus frissítések témakörében](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) talál.

A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.

Ez a megoldás nem támogatja a Linux rendszerhez készült Log Analytics-ügynököt, amely több Azure Log Analytics-munkaterületnek való jelentésre van konfigurálva.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Update Management engedélyezése Azure-beli virtuális gépekhez

A Azure Portal nyissa meg Automation-fiókját, majd válassza a **frissítés kezelése**lehetőséget.

Válassza az Azure-beli **virtuális gépek hozzáadása**lehetőséget.

![Azure-beli virtuális gép hozzáadása lap](./media/manage-update-multi/update-onboard-vm.png)

Válassza ki az előkészíteni kívánt virtuális gépet.

A **Update Management engedélyezése**területen válassza az **Engedélyezés** lehetőséget a virtuális gép beléptetéséhez.

![Frissítéskezelés engedélyezése párbeszédpanel](./media/manage-update-multi/update-enable.png)

A bevezetést követően a Update Management engedélyezve van a virtuális gépen.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Update Management engedélyezése nem Azure-beli virtuális gépekhez és számítógépekhez

A nem Azure-beli Windows rendszerű virtuális gépek és számítógépek Update Managementének engedélyezéséről a [Windows rendszerű számítógépek összekapcsolása az Azure-beli Azure monitor szolgáltatással](../log-analytics/log-analytics-windows-agent.md)című témakörben talál további információt.

A nem Azure-beli linuxos virtuális gépek és számítógépek Update Managementének engedélyezéséről a Linux rendszerű [számítógépek Összekapcsolása Azure monitor naplókhoz](../log-analytics/log-analytics-agent-linux.md)című témakörben olvashat.

## <a name="view-computers-attached-to-your-automation-account"></a>Az Automation-fiókhoz csatlakoztatott számítógépek megtekintése

Miután engedélyezte Update Management a gépek számára, a **számítógépek**elem kiválasztásával megtekintheti a számítógép adatait. Megtekintheti a *gép neve*, a *megfelelőségi állapot*, a *környezet*, az *operációs rendszer típusa*, a *kritikus és biztonsági frissítések telepítése*, a *telepített egyéb frissítések*és a *frissítési ügynök felkészültségét* számítógépek.

  ![Számítógépek megtekintése lap](./media/manage-update-multi/update-computers-tab.png)

Előfordulhat, hogy a Update Management legutóbb engedélyezett számítógépek még nem lettek felbecsülve. Ezeknek a számítógépeknek a megfelelőségi állapotának **megállapítása nem**történik meg. Az alábbi lista a megfelelőségi állapot lehetséges értékeit sorolja fel:

- **Megfelelő**: azok a számítógépek, amelyek nem hiányoznak a kritikus vagy biztonsági frissítésekkel.

- **Nem megfelelő**: azok a számítógépek, amelyeken legalább egy kritikus vagy biztonsági frissítés hiányzik.

- **Nincs értékelve**: a frissítési értékelési adatok nem érkeztek meg a számítógépről a várt időkereten belül. A Linux rendszerű számítógépek esetében a várakozási időkeret az elmúlt órában van. A Windows rendszerű számítógépek esetében a várt időkeret az elmúlt 12 órában van.

Az ügynök állapotának megtekintéséhez válassza ki a hivatkozást az **Update Agent Readiness** oszlopban. Ha ezt a lehetőséget választja, megnyílik a **hibrid** feldolgozó ablaktábla, és a hibrid feldolgozó állapota látható. Az alábbi képen egy olyan ügynökre mutat példát, amely nem kapcsolódott Update Managementhoz hosszabb ideig:

![Számítógépek megtekintése lap](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Az Update Management engedélyezése után megnyílik az **Update Management** ablaktábla. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="collect-data"></a>Adatok gyűjtése

A virtuális gépekre és számítógépekre telepített ügynökök adatokat gyűjtenek a frissítésekről. Az ügynökök az Azure Update Managementba küldik az adatszolgáltatásokat.

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |Update Management adatokat gyűjt a Windows-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |Update Management adatokat gyűjt a Linux-ügynököktől a rendszerfrissítésekről, majd kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciók esetében. |
| Az Operations Manager felügyeleti csoportja |Igen |Update Management adatokat gyűjt a csatlakoztatott felügyeleti csoportban lévő ügynököktől származó rendszerfrissítésekről. |
| Azure Storage-fiók |Nem |Az Azure Storage szolgáltatás nem tartalmaz információkat a rendszerfrissítésekről. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Miután a számítógép befejezte a frissítések megfelelőségi vizsgálatát, az ügynök tömegesen továbbítja az adatokat Azure Monitor naplókhoz. A Windows rendszerű számítógépeken alapértelmezés szerint 12 óránként futnak a megfelelőségi vizsgálat.

A vizsgálati ütemterven kívül a frissítési megfelelőség vizsgálata az MMA újraindítása után 15 percen belül megkezdődik, a frissítés telepítése előtt és a frissítés telepítése után.

Linux rendszerű számítógépek esetében a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Az MMA-ügynök újraindításakor a megfelelőségi vizsgálat 15 percen belül megkezdődik.

A felügyelt számítógépekről származó frissített adatok megjelenítéséhez az irányítópult 30 perc és 6 óra között is eltarthat.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy központi telepítést, amely igazodik a kiadási ütemtervhez és a szolgáltatás ablakához. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ha egy vagy több virtuális gép új frissítési központi telepítését szeretné ütemezni **, válassza az** **Update Management**(frissítés kezelése) lehetőséget.

Az **új frissítés központi telepítése** ablaktáblán a következő információkat kell megadnia:

- **Név**: adjon meg egy egyedi nevet a frissítés központi telepítésének azonosításához.
- **Operációs rendszer**: válassza a **Windows** vagy a **Linux**lehetőséget.
- **Frissítendő csoportok (előzetes verzió)** : Meghatározhat egy előfizetéseken, erőforráscsoportokon, helyeken és címkéken alapuló lekérdezést, amellyel egy dinamikus, Azure-beli virtuális gépekből álló csoportot hozhat létre, majd belefoglalhatja a telepítésbe. További információ: [Dinamikus csoportok](automation-update-management-groups.md)
- **Frissítendő gépek**: válasszon ki egy mentett keresést, importált csoportot, vagy válassza a gépek lehetőséget a frissíteni kívánt gépek kiválasztásához. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható. A frissítés telepítésének megkezdése előtt láthatja a gép állapotát. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md)

  ![Új frissítés központi telepítése panel](./media/manage-update-multi/update-select-computers.png)

- **Frissítés besorolása**: válassza ki, hogy milyen típusú szoftverek szerepeljenek a frissítés telepítéséhez. A besorolási típusok leírását lásd: [frissítési besorolások](automation-view-update-assessments.md#update-classifications). A választható besorolási típusok a következők:
  - Kritikus frissítések
  - Biztonsági frissítések
  - Kumulatív frissítések
  - Funkciócsomagok
  - Szervizcsomagok
  - Definíciófrissítések
  - Eszközök
  - Frissítések

- **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a **Belefoglalás/kizárás** lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. További információ a felvétel kezeléséről: a [frissítések központi telepítésének ütemezett időpontja](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Ütemezési beállítások**: Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, Megadhat egy másik időpontot is.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ismétlődő ütemezések beállításához az **Ismétlődés**területen válassza az **ismétlődő**lehetőséget.

   ![Ütemezési beállítások párbeszédpanel](./media/manage-update-multi/update-set-schedule.png)

- **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).
- **Karbantartási időszak (perc)** : azt az időtartamot határozza meg, ameddig a frissítés üzembe helyezése megtörténik. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

- **Újraindítás vezérlése** – ez a beállítás határozza meg, hogy a rendszer hogyan kezeli az újraindításokat a frissítés központi telepítéséhez.

   |Lehetőség|Leírás|
   |---|---|
   |Újraindítás szükség esetén| **(Alapértelmezett)** Ha szükséges, a rendszer újraindítást kezdeményez, ha a karbantartási időszak lehetővé teszi.|
   |Mindig induljon újra|A rendszer újraindítást kezdeményez, függetlenül attól, hogy szükség van-e rá. |
   |Soha ne induljon újra|Függetlenül attól, hogy szükség van-e újraindításra, az újraindítások le lesznek tiltva.|
   |Csak újraindítás – frissítések nem lesznek telepítve|Ez a beállítás figyelmen kívül hagyja a frissítések telepítését, és csak újraindítás kezdeményezését kezdeményezi.|

Amikor befejezte az ütemterv konfigurálását, kattintson a **Létrehozás** gombra az állapot-irányítópultra való visszatéréshez. Az **ütemezett** táblázat a létrehozott központi telepítési ütemezést jeleníti meg.

> [!NOTE]
> Az Update Management a belső frissítések üzembe helyezését és a javítócsomagok előzetes letöltését támogatja. Ehhez az szükséges, hogy a rendszer módosításokat hajtson végre a javítás során. az [első fél és a letöltés előtti támogatás](automation-configure-windows-update.md#pre-download-updates) segítségével megtudhatja, hogyan konfigurálhatja ezeket a beállításokat a rendszereken.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.

Ha a telepítés fut, az állapota **Folyamatban**. Az üzembe helyezés sikeres befejeződése után az állapot **sikeresre**változik.

Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, a telepítés állapota **Részben sikertelen**.

![A frissítéstelepítés állapota](./media/manage-update-multi/update-view-results.png)

Adott frissítéstelepítés irányítópultjának megtekintéséhez válassza ki a befejezett telepítést.

A **frissítés eredményei** ablaktábla a frissítések teljes számát és a virtuális gép telepítési eredményeit jeleníti meg. A jobb oldali táblázat az egyes frissítések részletes részletezését és a telepítés eredményét ismerteti. A telepítési eredmények a következő értékek lehetnek:

- **Nem történt kísérlet**: a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem volt elég idő a rendelkezésre állásra.
- **Sikeres**: A frissítés sikeres volt.
- **Sikertelen**: A frissítés sikertelen volt.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza a **Minden napló** elemet.

Ha szeretné megtekinteni a runbook, amely a frissítés központi telepítését kezeli a cél virtuális gépen, válassza a kimenet csempét.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a Update Managementokról, például a naplókról, a kimenetről és a hibákról, tekintse meg [Az Azure-beli Update Management megoldást](../operations-management-suite/oms-solution-update-management.md).

