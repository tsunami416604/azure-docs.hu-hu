---
title: Több virtuális gép frissítéseinek kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan kezelheti a több virtuális gép frissítéseit.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 864b6793f65c69c83c0e26d01a10e156b1094889
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741027"
---
# <a name="manage-updates-for-multiple-vms"></a>Frissítések kezelése több virtuális gép esetén

A Windows-és Linux-alapú virtuális gépek frissítéseinek és javításának kezeléséhez Azure Automation Update Management is használhatja. Az [Azure Automation](automation-offering-get-started.md)-fiókból a következőket végezheti el:

- Engedélyezze a virtuális gépeket az Update Management szolgáltatásban.
- Az elérhető frissítések állapotának felmérése.
- A szükséges frissítések telepítésének ütemezése.
- Tekintse át a telepítési eredményeket annak ellenőrzéséhez, hogy a frissítések alkalmazása sikeres volt-e az összes olyan virtuális gépen, amelyhez Update Management engedélyezve van.

A Update Management rendszerkövetelményeivel kapcsolatos további információkért lásd: [Update Management ügyfélre vonatkozó követelmények](automation-update-management.md#client-requirements).

## <a name="prerequisites"></a>Előfeltételek

* Olyan virtuális gép vagy számítógép, amelyen telepítve van a támogatott operációs rendszerek egyike.
* Hozzáférés a Update Management számára engedélyezett linuxos virtuális gépek frissítési tárházához.

## <a name="enable-update-management-for-azure-vms"></a>Azure-beli virtuális gépek Update Managementának engedélyezése

1. A Azure Portal nyissa meg Automation-fiókját, majd válassza a **frissítés kezelése**lehetőséget.

2. Válassza az Azure-beli **virtuális gépek hozzáadása**lehetőséget.

    ![Az Azure-beli virtuális gép hozzáadása fül](./media/manage-update-multi/update-onboard-vm.png)

3. Válassza ki az engedélyezni kívánt virtuális gépet, és válassza **az Engedélyezés lehetőséget** a **Update Management engedélyezése**területen.

    ![Frissítéskezelés engedélyezése párbeszédpanel](./media/manage-update-multi/update-enable.png)

    A művelet befejezésekor Update Management engedélyezve van a virtuális gépen.

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>Update Management engedélyezése nem Azure-beli virtuális gépek és számítógépek számára

A Windows és a Linux rendszerhez készült Log Analytics ügynöknek telepítve kell lennie a vállalati hálózaton vagy más felhőalapú környezetben futó virtuális gépeken, hogy azok a Update Management. Az ügynök az Azure-on kívül üzemeltetett gépekre való központi telepítéséhez szükséges rendszerkövetelmények és támogatott módszerek megismeréséhez tekintse meg [a log Analytics ügynök áttekintését](../azure-monitor/platform/log-analytics-agent.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Az Automation-fiókhoz csatlakoztatott számítógépek megtekintése

Miután engedélyezte Update Management a gépek számára, a **számítógépek**elem kiválasztásával megtekintheti a számítógép adatait. A számítógép nevére, a megfelelőségi állapotra, a környezetre, az operációs rendszer típusára, a kritikus és biztonsági frissítések telepítésére, a telepített egyéb frissítésekre és a számítógépek frissítésére való felkészültségre vonatkozó információkat itt tekintheti meg.

  ![Számítógépek megtekintése lap](./media/manage-update-multi/update-computers-tab.png)

Előfordulhat, hogy a Update Management legutóbb engedélyezett számítógépek még nem lettek felbecsülve. Ezeknek a számítógépeknek a megfelelőségi állapota `Not assessed` . Az alábbi lista a megfelelőségi állapot lehetséges értékeit sorolja fel:

- `Compliant`: A kritikus vagy biztonsági frissítéseket nem tartalmazó számítógépek.
- `Non-compliant`: Legalább egy kritikus vagy biztonsági frissítésből hiányzó számítógépek.
- `Not assessed`: A frissítési értékelési adatok nem érkeztek meg a számítógépről a várt időkereten belül. A Linux rendszerű számítógépek esetében a várt időkeret az utolsó óra. A Windows rendszerű számítógépek esetében a várt időkeret az utolsó 12 óra.

Az ügynök állapotának megtekintéséhez válassza ki a hivatkozást az **Update Agent Readiness** oszlopban. Ha ezt a lehetőséget választja, megnyílik a hibrid feldolgozó ablaktábla, és a hibrid feldolgozó állapota látható. Az alábbi képen egy olyan ügynökre mutat példát, amely nem kapcsolódott Update Managementhoz hosszabb ideig:

![Számítógépek megtekintése lap](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Update Management engedélyezése után megnyílik a Update Management panel. A **Hiányzó frissítések** lapon a hiányzó frissítések listája látható.

## <a name="collect-data"></a>Adatok gyűjtése

A virtuális gépekre és számítógépekre telepített ügynökök adatokat gyűjtenek a frissítésekről. Az ügynökök az Azure Update Managementba küldik az adatszolgáltatásokat.

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a Update Management által támogatott csatlakoztatott forrásokat:

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

30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy központi telepítést, amely igazodik a kiadási ütemtervhez és a szolgáltatás ablakához. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>A frissítések központi telepítésének megkezdése után egy olyan [ütemezett](shared-resources/schedules.md) erőforrást hoz létre, amely kapcsolódik a **MicrosoftOMSComputers** runbook, amely kezeli a frissítés központi telepítését a célszámítógépen. Ha törli az ütemezési erőforrást a Azure Portal vagy a PowerShellt a központi telepítés létrehozása után, megszakítja az ütemezett frissítést, és hibaüzenetet jelenít meg, amikor megkísérli az újrakonfigurálást a portálról. Az ütemezett erőforrást csak a megfelelő központi telepítési ütemterv törlésével törölheti.
>

Ha egy vagy több virtuális gép új frissítési központi telepítését szeretné ütemezni, válassza az **Update Management**(frissítés **telepítése**) lehetőséget.

Az **új frissítés központi telepítése** ablaktáblán a következő információkat kell megadnia:

- **Név**: adjon meg egy egyedi nevet a frissítés központi telepítésének azonosításához.
- **Operációs rendszer**: válassza a **Windows** vagy a **Linux**lehetőséget.
- **Frissítendő csoportok**: az előfizetés, az erőforráscsoportok, a helyszínek és a címkék kombinációján alapuló lekérdezéseket hozhat létre, amelyekkel felépítheti az üzembe helyezésbe felvenni kívánt Azure-beli virtuális gépek dinamikus csoportját. A nem Azure-beli virtuális gépek esetében a mentett keresések segítségével létrehozható egy dinamikus csoport, amely belefoglalja a központi telepítésbe. További információ: [dinamikus csoportok](automation-update-management-groups.md).
- **Frissítendő gépek**: válasszon ki egy mentett keresést, importált csoportot, vagy válassza a gépek lehetőséget a frissíteni kívánt gépek kiválasztásához.

   >[!NOTE]
   >A mentett keresés lehetőség választása nem ad vissza számítógép-identitásokat, csak a nevüket. Ha több, azonos nevű virtuális géppel rendelkezik több erőforráscsoport között, a rendszer az eredményeket adja vissza. A **csoportok frissítése** beállítás használata ajánlott annak biztosítására, hogy a feltételnek megfelelő egyedi virtuális gépeket tartalmazzon.

   Ha a **gépeket**választja, a gép készültsége megjelenik a **frissítési ügynök készültsége** oszlopban. A frissítés telepítésének megkezdése előtt láthatja a gép állapotát. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md)

  ![Új frissítés központi telepítése panel](./media/manage-update-multi/update-select-computers.png)

- **Frissítés besorolása**: válassza ki, hogy milyen típusú szoftverek szerepeljenek a frissítés telepítéséhez. A besorolási típusok leírását lásd: [frissítési besorolások](automation-view-update-assessments.md#work-with-update-classifications). A választható besorolási típusok a következők:
  - Kritikus frissítések
  - Biztonsági frissítések
  - Kumulatív frissítések
  - Funkciócsomagok
  - Szervizcsomagok
  - Definíciófrissítések
  - Eszközök
  - Frissítések

- **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a Belefoglalás/kizárás lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. További információ a felvétel kezeléséről: a [frissítések központi telepítésének ütemezett időpontja](automation-tutorial-update-management.md#schedule-an-update-deployment).

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy kizárási szabályt `*` , akkor a rendszer nem telepíti a javításokat és a csomagokat, mivel azok ki vannak zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépről. Linux rendszerű gépek esetén, ha csomag szerepel a csomagban, de egy kizárt függő csomaggal rendelkezik, a csomag nincs telepítve.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyeket a rendszer a frissítés központi telepítésére való felvételre váltott ki.

- **Ütemezési beállítások**: Elfogadhatja az alapértelmezett időpontot, amely a 30 perccel az aktuális idő utáni időpont, Megadhat egy másik időpontot is.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön. Ismétlődő ütemezések beállításához az **Ismétlődés**területen válassza az **ismétlődő**lehetőséget.

   ![Ütemezési beállítások párbeszédpanel](./media/manage-update-multi/update-set-schedule.png)

- **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).
- **Karbantartási időszak (perc)**: azt az időtartamot határozza meg, ameddig a frissítés üzembe helyezése megtörténik. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

- **Újraindítás vezérlése** – ez a beállítás határozza meg, hogy a rendszer hogyan kezeli az újraindításokat a frissítés központi telepítéséhez.

   |Beállítás|Leírás|
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

A frissítés eredményei ablaktábla megjeleníti a frissítések teljes számát és a virtuális gép telepítési eredményeit. A jobb oldali táblázat az egyes frissítések részletes részletezését és a telepítés eredményét ismerteti. A telepítési eredmények a következő értékek lehetnek:

- `Not attempted`: A frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem volt elég idő a rendelkezésére.
- `Succeeded`: A frissítés sikeresen befejeződött.
- `Failed`: A frissítés nem sikerült.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza a **Minden napló** elemet.

Ha szeretné megtekinteni a runbook, amely a frissítés központi telepítését kezeli a cél virtuális gépen, válassza a kimenet csempét.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>További lépések

[Update Management-naplók lekérdezése](automation-update-management-query-logs.md)
