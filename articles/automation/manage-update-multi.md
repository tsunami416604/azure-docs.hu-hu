---
title: Több Azure-beli virtuális gép frissítéseinek kezelése
description: Ez a témakör az Azure-beli virtuális gépek frissítéseinek kezelését mutatja be.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ce3d0501900cf8b60daee9aff6eeeaf62fa29409
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-updates-for-multiple-machines"></a>Frissítések kezelése több gép esetén

A frissítéskezelés segítségével kezelheti a Windows és Linux rendszerű gépek frissítéseit és javításait. Az [Azure Automation](automation-offering-get-started.md)-fiókból a következőket végezheti el:

- Virtuális gépek előkészítése.
- Az elérhető frissítések állapotának felmérése.
- A szükséges frissítések telepítésének ütemezése.
- A telepítési eredmények áttekintése, annak ellenőrzéséhez, sikeres volt-e a frissítések telepítése az összes virtuális gépen, amelyen engedélyezve van a frissítéskezelés.

## <a name="prerequisites"></a>Előfeltételek

A frissítéskezelés használatához a következőkre van szükség:

- Azure Automation futtató fiók. A fiók létrehozásával kapcsolatban a [Bevezetés az Azure Automation használatába](automation-offering-get-started.md) című cikk nyújt tájékoztatást.

- Egy támogatott operációs rendszert futtató virtuális gépre vagy számítógépre.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A frissítéskezelés a következő operációs rendszereken támogatott:

### <a name="windows"></a>Windows

- Windows Server 2008 és újabb, a frissítéstelepítések pedig csak Windows Server 2008 R2 SP1 és újabb rendszereken támogatottak. A Nano Server nem támogatott.

  Frissítések a Windows Server 2008 R2 SP1 rendszerre való telepítésének támogatásához .NET-keretrendszer 4.5 és Windows Management Framework 5.0 vagy újabb verzió szükséges.

- Az ügyféloldali Windows operációs rendszerek nem támogatottak.

A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük.

> [!NOTE]
> A System Center Configuration Manager nem tudja párhuzamosan kezelni a Windows-ügynököt.
>

### <a name="linux"></a>Linux

- CentOS 6 (x86/x64) és 7 (x64)

- Red Hat Enterprise 6 (x86/x64) és 7 (x64)

- SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)

- Ubuntu 12.04 LTS és újabb (x86/x64)

> [!NOTE]
> Ahhoz, hogy Ubuntu rendszeren elkerülje a karbantartási időszakon kívüli frissítéstelepítést, konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. További információt az [Ubuntu kiszolgáló kézikönyvének Automatikus frissítések témakörében](https://help.ubuntu.com/lts/serverguide/automatic-updates.html) talál.

A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.

Ez a megoldás úgy konfigurálva, hogy több Naplóelemzési munkaterület jelentés Linux OMS-ügynököt nem támogatja.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Frissítéskezelés engedélyezése Azure-beli virtuális gépeken

Az Azure portálon, nyissa meg az Automation-fiók, és válassza **frissítéskezelés**.

Válassza az **Azure-beli virtuális gép hozzáadása** lehetőséget az ablak tetején.

![Azure virtuális gép lap hozzáadása](./media/manage-update-multi/update-onboard-vm.png)

Válassza ki az előkészíteni kívánt virtuális gépet. Megjelenik a **Frissítéskezelés engedélyezése** párbeszédpanel. Válassza ki **engedélyezése** bevezetni a virtuális gép. Bevezetési végrehajtása után felügyelete engedélyezve van a virtuális gép.

![Frissítéskezelés engedélyezése párbeszédpanel](./media/manage-update-multi/update-enable.png)

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Frissítéskezelés engedélyezése nem Azure-beli virtuális gépeken és számítógépeken

A nem Azure-beli virtuális gépeken és számítógépeken a frissítéskezelés engedélyezésének útmutatásáért lásd a [Windows rendszerű számítógépek a Log Analytics szolgáltatáshoz az Azure-ban való csatlakoztatását](../log-analytics/log-analytics-windows-agent.md) ismertető dokumentumot.

A nem Azure-beli linuxos virtuális gépeken és számítógépeken a frissítéskezelés engedélyezésének útmutatásáért lásd a [Linux rendszerű számítógépek Log Analyticshez való csatlakoztatását](../log-analytics/log-analytics-agent-linux.md) ismertető témakört.

## <a name="view-computers-attached-to-your-automation-account"></a>Az Automation-fiókhoz csatlakoztatott számítógépek megtekintése

Ha engedélyezte a frissítéskezelést a számítógépeken, a vonatkozó adatokat a **Számítógépek** elemre kattintva tekintheti meg. Számítógép-információk, például *neve*, *megfelelőségi*, *környezet*, *operációsrendszer-típus*, *kritikus és biztonsági frissítések* , *Más frissítések*, és *frissítési ügynök készültségi* érhetők el.

  ![Számítógépek megtekintése lap](./media/manage-update-multi/update-computers-tab.png)

A frissítéskezeléshez nemrégiben hozzáadott számítógépek esetében előfordulhat, hogy a kiértékelés még nem történt meg. Az ilyen számítógépek megfelelőségi állapota *Nincs értékelve*.  Az alábbi lista a megfelelőségi állapot értékeit sorolja fel:

- Megfelelő – Azok a számítógépek, amelyről nem hiányzik kritikus vagy biztonsági frissítés.

- Nem megfelelő – Azok a számítógépek, amelyről legalább egy kritikus vagy biztonsági frissítés hiányzik.

- Nincs értékelve – A frissítés kiértékelésének adatai nem érkeztek meg a számítógépről a meghatározott időkereten belül.  Ez Linux rendszerű számítógépek esetében az elmúlt három órát, Windows rendszerű számítógépeknél pedig az elmúlt 12 órát jelenti.

Az ügynök állapotának megtekintéséhez kattintson a hivatkozásra a **frissítési ÜGYNÖK KÉSZÜLTSÉGI** oszlop. Ekkor megnyílik a hibrid feldolgozó oldal, amely a hibrid feldolgozó állapotát jeleníti meg. Az alábbi képen olyan ügynök, amely nem kapcsolódik frissítéskezelés egy kiterjesztett időtartamig példáját mutatja be.

![Számítógépek megtekintése lap](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

A frissítéskezelés engedélyezése után megjelenik a **Frissítéskezelés** párbeszédpanel. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="collect-data"></a>Adatok gyűjtése

A virtuális gépekre és számítógépekre telepített ügynökök adatokat gyűjtenek a frissítésekről, és elküldik őket az Azure-frissítéskezelésnek.

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A frissítéskezelés begyűjti a Windows-ügynököktől a rendszerfrissítésekről szóló információkat, és kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A frissítéskezelés begyűjti a Linux-ügynököktől a rendszerfrissítésekről szóló információkat, és kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciókon. |
| Az Operations Manager felügyeleti csoportja |Igen |A frissítéskezelés begyűjti a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről szóló információkat. |
| Azure Storage-fiók |Nem |Az Azure Storage nem tartalmaz rendszerfrissítésekkel kapcsolatos információt. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Felügyelt Windows-számítógépek esetében naponta kétszer fut vizsgálat. A rendszer 15 percenként lekérdezi a Windows API utolsó frissítésének időpontját, hogy meghatározza, megváltozott-e az állapot. Ha igen, megfelelőségi vizsgálatot kezdeményez. Felügyelt Linux-számítógépek esetében a vizsgálat három óránként fut.

30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek.
Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést egy vagy több virtuális géphez. Ehhez válassza a **Frissítéskezelés** párbeszédpanel felső részén található **Frissítéstelepítés ütemezése** lehetőséget.
Az **Új frissítéstelepítés** panelen adja meg a következőket:

- **Név**: Adjon meg egy egyedi nevet a frissítéstelepítés azonosításához.
- **Operációs rendszer típusa**: Válassza ki a Windows vagy a Linux lehetőséget.
- **Gépek frissítése**: válassza ki a frissíteni kívánt virtuális gépeket. A számítógép készenléti látható a **frissítési ÜGYNÖK KÉSZÜLTSÉGI** oszlop. Ez lehetővé teszi a számítógép állapotának lásd a frissítés központi telepítés ütemezése előtt.

  ![„Új frissítéstelepítés” panel](./media/manage-update-multi/update-select-computers.png)

- **Frissítési besorolás**: Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés tartalmaz. A besorolási típusú ismertetését lásd: [frissítési besorolások](automation-update-management.md#update-classifications). A választható besorolási típusok a következők:
  - Kritikus frissítések
  - Biztonsági frissítések
  - Kumulatív frissítések
  - Funkciócsomagok
  - Szervizcsomagok
  - Definíciófrissítések
  - Eszközök
  - Frissítések
- **Ütemezési beállítások**: Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, vagy megadhat egy másik időpontot.
   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ismétlődő ütemezés beállításához válassza az **Ismétlődés** alatti **Ismétlődő** lehetőséget.

   ![Ütemezési beállítások párbeszédpanel](./media/manage-update-multi/update-set-schedule.png)

- **Karbantartási időszak (perc)**: Adja meg azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

Ha befejezte az ütemezés konfigurálását, a **Létrehozás** gombra kattintva lépjen vissza az állapot-irányítópultra. Ekkor az **Ütemezett** táblázatban látható az imént létrehozott telepítési ütemezés.

> [!WARNING]
> Az újraindítást igénylő frissítések esetében a virtuális gép automatikusan újraindul.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** párbeszédpanel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha a telepítés fut, az állapota **Folyamatban**. A telepítés sikeres befejeződése után **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, a telepítés állapota **Részben sikertelen**.

![A frissítéstelepítés állapota](./media/manage-update-multi/update-view-results.png)

Adott frissítéstelepítés irányítópultjának megtekintéséhez válassza ki a befejezett telepítést.

A **Frissítés eredményei** panel jeleníti meg a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza. A telepítési eredmények a következő értékek lehetnek:

- Nem lett megkísérelve: A frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
- Sikeres: A frissítés sikeres volt.
- Sikertelen: A frissítés sikertelen volt.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza a **Minden napló** elemet.

Azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen, válassza a **Kimenet** csempét.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>További lépések

- További információt a frissítéskezelés – többek között a következőket naplók, kimeneti és hibák: [frissítése felügyeleti megoldás az Azure-ban](../operations-management-suite/oms-solution-update-management.md).
