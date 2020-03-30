---
title: Több Azure-beli virtuális gép frissítéseinek kezelése
description: Ez a cikk ismerteti, hogyan kezelheti az Azure és a nem Azure virtuális gépek frissítéseit.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: c9a3c88ea0c3e656adf0f8c514b418cfc07c9590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335773"
---
# <a name="manage-updates-for-multiple-machines"></a>Frissítések kezelése több gép esetén

Az Update Management megoldás sal kezelheti a Windows és Linux virtuális gépek frissítéseit és javításait. Az [Azure Automation](automation-offering-get-started.md)-fiókból a következőket végezheti el:

- Beépített virtuális gépek
- Az elérhető frissítések állapotának felmérése
- A szükséges frissítések telepítésének ütemezése
- Tekintse át a telepítés eredményeit, és ellenőrizze, hogy a frissítések sikeresen alkalmazva voltak-e minden olyan virtuális gépen, amelyhez engedélyezve van az Update Management szolgáltatás

## <a name="prerequisites"></a>Előfeltételek

Az Update Management használatához a következőkre van szükség:

- Egy támogatott operációs rendszert futtató virtuális gépre vagy számítógépre.

- Hozzáférés a megoldásba beépített Linux-virtuális gépek frissítési tárházához.

Az Update Management rendszerkövetelményeiről a [Frissítéskezelő ügyfélkövetelményeinek](automation-update-management.md#clients)című témakörben olvashat.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Frissítéskezelés engedélyezése az Azure virtuális gépein

Az Azure Portalon nyissa meg az Automation-fiókot, és válassza **a Frissítéskezelés lehetőséget.**

Válassza **az Azure virtuális gépek hozzáadása**lehetőséget.

![Az Azure-beli virtuális gép hozzáadása fül](./media/manage-update-multi/update-onboard-vm.png)

Válassza ki az előkészíteni kívánt virtuális gépet.

A **Frissítéskezelés engedélyezése**csoportban jelölje **be az Engedélyezés** a virtuális gép fedélzetének bedeszkázására jelölőnégyzetet.

![Frissítéskezelés engedélyezése párbeszédpanel](./media/manage-update-multi/update-enable.png)

Amikor a bevezetés befejeződött, update management engedélyezve van a virtuális gép.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Frissítéskezelés engedélyezése nem Azure-beli virtuális gépeken és számítógépeken

A Windows és Linux Log Analytics-ügynök kell telepíteni a virtuális gépek, amelyek a vállalati hálózaton vagy más felhőalapú környezetben futnak annak érdekében, hogy azok frissítési kezelése. Az ügynök Azure-on kívüli üzemeltetett gépekre történő üzembe helyezéséhez szükséges rendszerkövetelményekés támogatott módszerek ről [a Log Analytics-ügynök áttekintése című témakörben olvashat.](../azure-monitor/platform/log-analytics-agent.md)

## <a name="view-computers-attached-to-your-automation-account"></a>Az Automation-fiókhoz csatolt számítógépek megtekintése

Miután engedélyezte a frissítéskezelést a gépeken, a **Számítógépek**lehetőséget választva megtekintheti a számítógép adatait. Megtekintheti a *számítógép nevére,* *a megfelelőségi állapotra,* a *környezetre,* az operációs *rendszer típusára,* a telepített kritikus és *biztonsági frissítésekre,* *a telepített egyéb frissítésekre*és a *frissítési ügynök frissítésére vonatkozó információkat.*

  ![Számítógépek megtekintése lap](./media/manage-update-multi/update-computers-tab.png)

Előfordulhat, hogy a frissítéskezeléshez a közelmúltban engedélyezett számítógépek értékelése még nem történt meg. A rendszer nem **értékeli**a számítógépek megfelelőségi állapotát. Az alábbiakban a megfelelőségi állapot lehetséges értékeit listául sorolják:

- **Megfelelő**: Olyan számítógépek, amelyek nem hiányoznak a kritikus vagy biztonsági frissítésekből.

- **Nem megfelelő**: Olyan számítógépek, amelyeken legalább egy kritikus vagy biztonsági frissítés hiányzik.

- **Nincs értékelve:** A frissítésértékelési adatok nem érkeztek meg a számítógépről a várt időkereten belül. Linux számítógépek esetén a várt időkeret az elmúlt órában van. Windows rendszerű számítógépek esetén a várt időkeret az elmúlt 12 órában van.

Az ügynök állapotának megtekintéséhez jelölje ki a hivatkozást az **Ügynök készültség frissítése** oszlopban. Ez a beállítás megnyitja a **hibrid feldolgozó** ablaktáblát, és megjeleníti a hibrid feldolgozó állapotát. Az alábbi képen egy olyan ügyintéző látható, amely hosszabb ideje nem csatlakozott az Update Management szolgáltatáshoz:

![Számítógépek megtekintése lap](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Az Update Management engedélyezése után megnyílik az **Update Management** ablaktábla. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="collect-data"></a>Adatok gyűjtése

A virtuális gépekre és számítógépekre telepített ügynökök adatokat gyűjtenek a frissítésekről. Az ügynökök elküldik az adatokat az Azure Update Management nek.

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |Az Update Management információkat gyűjt a rendszerfrissítésekről a Windows-ügynököktől, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |Az Update Management információkat gyűjt a rendszerfrissítésekről a Linux-ügynököktől, majd elindítja a szükséges frissítések telepítését a támogatott disztribúciókon. |
| Az Operations Manager felügyeleti csoportja |Igen |Az Update Management információkat gyűjt a rendszerfrissítésekről egy csatlakoztatott felügyeleti csoport ügynökeitől. |
| Azure Storage-fiók |Nem |Az Azure Storage nem tartalmaz információt a rendszerfrissítésekről. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Miután a számítógép befejezte a frissítésmegfelelőség vizsgálatát, az ügynök továbbítja az adatokat ömlesztve az Azure Monitor naplóiba. Windows rendszerű számítógépen a megfelelőségi vizsgálat alapértelmezés szerint 12 óránként fut.

A vizsgálat ütemezése mellett a frissítés megfelelőségének vizsgálata az MMA újraindítását követő 15 percen belül, a frissítés telepítése előtt és a frissítés telepítése után kezdődik.

Linux rendszerű számítógép esetén a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Ha az MMA-ügynök újraindul, 15 percen belül megfelelőségi vizsgálat indul.

30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy központi telepítést, amely megfelel a kiadási ütemezésnek és a szervizablaknak. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>Frissítési központi telepítés ütemezésekor létrehoz egy [ütemezési](shared-resources/schedules.md) erőforrást a **Patch-MicrosoftOMSComputers** runbookhoz kapcsolódóan, amely kezeli a frissítés központi telepítését a célgépeken. Ha törli az ütemezési erőforrást az Azure Portalról, vagy a Központi telepítés létrehozása után használja a PowerShellt, megszakítja az ütemezett frissítés központi telepítését, és hibát észlel, amikor megpróbálja újrakonfigurálni a portálról. Az ütemezési erőforrás csak a megfelelő telepítési ütemezés törlésével törölhető.
>

Ha egy vagy több virtuális gépúj központi telepítését szeretné ütemezni, a **Frissítéskezelés**csoportban válassza **a Frissítés telepítésének ütemezése**lehetőséget.

Az **Új frissítés központi telepítése** ablaktáblán adja meg a következő információkat:

- **Név**: Adjon meg egy egyedi nevet a frissítés központi telepítésének azonosításához.
- **Operációs rendszer**: Válassza a **Windows** vagy **a Linux**lehetőséget.
- **Frissítandó csoportok:** Hozzon létre egy lekérdezést előfizetés, erőforráscsoportok, helyek és címkék kombinációjára, hogy az Azure virtuális gépek dinamikus csoportját hozhatja létre a központi telepítésben. A nem Azure-beli virtuális gépek, mentett keresések segítségével hozzon létre egy dinamikus csoportot, amely tartalmazza a központi telepítésben. További információ: [Dinamikus csoportok](automation-update-management-groups.md).
- **Frissítandó gépek**: Válassza ki a Mentett keresés, az Importált csoport vagy a Gépek lehetőséget a frissíteni kívánt gépek kiválasztásához.

   >[!NOTE]
   >A Mentett keresés beállítás választása nem adja vissza a számítógép identitását, csak a nevüket. Ha több virtuális gép azonos nevű több erőforráscsoportok között, azok az eredmények. A **Csoportok frissítésre** beállítás használata ajánlott annak biztosítására, hogy a feltételeknek megfelelő egyedi virtuális gépeket tartalmazzon.

   Ha a Gépek lehetőséget **választja,** a gép készenléte a **Frissítési ügynök készenléte** oszlopban jelenik meg. A frissítés központi telepítésének ütemezése előtt megtekintheti a számítógép állapotát. A számítógépcsoportok létrehozásának különböző módszereiről az Azure Monitor naplóiban a [Számítógépcsoportok az Azure Monitor naplóiban című témakörben olvashat.](../azure-monitor/platform/computer-groups.md)

  ![Új frissítés központi telepítési ablaktáblája](./media/manage-update-multi/update-select-computers.png)

- **Frissítési besorolás**: Válassza ki a frissítés központi telepítésébe felvenni kívánt szoftvertípusokat. A besorolási típusok leírását a Besorolások frissítése című [témakörben ismertetjük.](automation-view-update-assessments.md#update-classifications) A választható besorolási típusok a következők:
  - Kritikus frissítések
  - Biztonsági frissítések
  - Kumulatív frissítések
  - Funkciócsomagok
  - Szervizcsomagok
  - Definíciófrissítések
  - Eszközök
  - Frissítések

- **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a **Belefoglalás/kizárás** lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. A felvétel kezeléséről a Frissítés [telepítésének ütemezése](automation-tutorial-update-management.md#schedule-an-update-deployment)című témakörben talál további információt.

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a zárványokat. Ha például a kizárási `*`szabályt a, akkor nem javítások vagy csomagok vannak telepítve, mivel azok mind ki vannak zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépből. Linux gépek, ha egy csomag szerepel, de van egy függő csomagot, amely kizárt, a csomag nincs telepítve.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyek et felülítettek a frissítés központi telepítésével való felvételhez.
>

- **Ütemezési beállítások**: Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, Más időpontot is megadhat.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ismétlődő ütemezés beállításához az **Ismétlődés**csoportban válassza **az Ismétlődés**lehetőséget.

   ![Ütemezési beállítások párbeszédpanel](./media/manage-update-multi/update-set-schedule.png)

- **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).
- **Karbantartási időszak (perc)**: Adja meg azt az időtartamot, amerre a frissítés központi telepítését el szeretné látni. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

- **Újraindítás vezérlés** – Ez a beállítás határozza meg, hogyan kezeli az újraindítások a frissítés központi telepítéséhez.

   |Beállítás|Leírás|
   |---|---|
   |Újraindítás, ha szükséges| **(Alapértelmezett)** Szükség esetén újraindul a rendszer, ha a karbantartási időszak lehetővé teszi.|
   |Mindig induljon újra|Az újraindítás attól függetlenül megtörténik, hogy szükség van-e rá. |
   |Soha ne induljon újra|Függetlenül attól, hogy újra kell-e indítani, az újraindítások levannak tiltva.|
   |Csak újraindítás – frissítések nem lesznek telepítve|Ez a beállítás figyelmen kívül hagyja a frissítések telepítését, és csak újraindítást kezdeményez.|

Ha befejezte az ütemezés konfigurálását, a **Létrehozás gombra** kattintva visszatérhet az állapot-irányítópultra. Az **Ütemezett** tábla a létrehozott telepítési ütemezést mutatja.

> [!NOTE]
> Az Update Management a belső frissítések üzembe helyezését és a javítócsomagok előzetes letöltését támogatja. Ehhez módosítani kell a javított rendszereket, lásd a [külső és letöltés előtti támogatást,](automation-configure-windows-update.md#pre-download-updates) hogy megtudja, hogyan konfigurálhatja ezeket a beállításokat a rendszereken.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.

Ha a telepítés fut, az állapota **Folyamatban**. A telepítés sikeres befejezése után az állapot **sikeresre változik.**

Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, a telepítés állapota **Részben sikertelen**.

![A frissítéstelepítés állapota](./media/manage-update-multi/update-view-results.png)

Adott frissítéstelepítés irányítópultjának megtekintéséhez válassza ki a befejezett telepítést.

A **Frissítés eredmények** ablaktábla a frissítések teljes számát és a virtuális gép telepítési eredményeit jeleníti meg. A jobb oldali táblázat részletesbontást ad az egyes frissítésekről és a telepítés eredményeiről. A telepítési eredmények a következő értékek lehetnek:

- **Nem kísérlet :** A frissítés nincs telepítve, mert a megadott karbantartási időszak alapján nem állt rendelkezésre elegendő idő.
- **Sikeres :** A frissítés sikerült.
- **Nem sikerült**: A frissítés nem sikerült.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza a **Minden napló** elemet.

A célvirtuális gépen a frissítés központi telepítését kezelő runbook feladatfolyamának megtekintéséhez válassza ki a kimeneti csempét.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Felügyeleti frissítések naplóiról, a kimenetről és a hibákról, olvassa el [a Lekérdezésfrissítési rekordok frissítése a frissítéskezeléshez](automation-update-management-query-logs.md).
