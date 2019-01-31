---
title: Egy értékelés csoport és az Azure Migrate csoport függőségi leképezés finomíthatja |} A Microsoft Docs
description: Ismerteti, amellyel pontosíthatja az értékelést a csoport függőségi leképezés használatával az Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 007f7fe95be77a2b1661cd6c82118eb875401f24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472575"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Eszközcsoport-leképezés függőségi csoport pontosítása

Ez a cikk ismerteti, amellyel pontosíthatja a csoportot a csoportban lévő összes gép függőségek vizualizációjával. Általában ezt a módszert, ha szeretne egy meglévő csoportot a tagsági pontosítás csoportfüggőségek kereszt-ellenőrzés az értékelés futtatása előtt. Finomítás a függőségek képi megjelenítésének használatával csoport segítségével hatékonyan tervezheti a migrálást az Azure-bA. Felfedezheti, hogy az összes saját rendszerek esetében, amelyek együtt áttelepíteni. Ez segít győződjön meg arról, hogy semmi sem marad, és meglepetés valamilyen okból kimaradás lép fordul elő, ha az Azure-bA áttelepítésekor.


> [!NOTE]
> Csoportok, amelynek meg szeretné függőségek vizualizálása 10-nél több gép nem tartalmazhat. Ha a csoport több mint 10 gépekkel rendelkezik, azt javasoljuk, hogy azt ossza fel kisebb csoportok kihasználhatja a függőségek képi megjelenítésének funkcióival.


## <a name="prepare-for-dependency-visualization"></a>Előkészítéséhez a függőségek képi megjelenítése
Az Azure Migrate a Log Analytics engedélyezése a függőségek vizualizációjához a Service Map megoldást használja.

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

### <a name="associate-a-log-analytics-workspace"></a>Log Analytics-munkaterület társítása
Kihasználhatja a függőségek képi megjelenítésével, hozzá kell rendelni egy Log Analytics-munkaterületet, vagy új vagy meglévő, az Azure Migrate-projektet. Csak létrehozása vagy csatolása ugyanahhoz az előfizetéshez egy munkaterületet, ahol a migrálási projekt létrejön.

- Egy projektet a Log Analytics-munkaterület csatlakoztatni kívánt **áttekintése**, lépjen a **Essentials** projekt kattintson **konfigurálást igényel**

    ![Log Analytics-munkaterület társítása](./media/concepts-dependency-visualization/associate-workspace.png)

- Munkaterület társítása, miközben kap arra, hozzon létre egy új munkaterületet, vagy csatlakoztasson egy már meglévőt:
    - Amikor létrehoz egy új munkaterületet, adja meg a munkaterület nevét kell. A munkaterületen létrejön ugyanabban a régióban [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) a migrálási projektet.
    - Ha csatlakoztat egy meglévő munkaterületet, a rendelkezésre álló munkaterületek ugyanahhoz az előfizetéshez tartozik, mint a migrálási projekt közül választhat. Vegye figyelembe, hogy csak azokat a munkaterületeket egy régióban létrehozott szerepel ahol [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Tudják csatolása egy munkaterületet, győződjön meg arról, hogy a munkaterület "Olvasó" elérhető lesz.

> [!NOTE]
> A migrálási projekthez tartozó munkaterület nem módosítható.

### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése
Egy csoport függőségek megjelenítéséhez szüksége, töltse le és telepítse az ügynököt minden olyan a csoport tagja a helyi gépen. Ezenkívül, ha internetkapcsolat nélküli gépek, kell letölteni és telepíteni [Log Analytics-átjáró](../azure-monitor/platform/gateway.md) rajtuk.

1. A **áttekintése**, kattintson a **kezelés** > **csoportok**, nyissa meg a szükséges csoport.
2. Gépek, a listában az a **függőségi ügynök** oszlopot, kattintson a **telepítés szükséges** megjelenő arról, hogyan töltse le és telepítse az ügynököket.
3. Az a **függőségek** lapon, töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök minden virtuális gépen, amely a csoport része.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Szükség van ezekre az MMA a helyszíni gépeken való telepítésekor.

### <a name="install-the-mma"></a>Az MMA telepítése

#### <a name="install-the-agent-on-a-windows-machine"></a>Az ügynök telepítése Windows gépen

Az ügynök telepítése a Windows-gépen:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3. A **célmappa**, megtartani, vagy módosítsa az alapértelmezett telepítési mappa > **tovább**.
4. A **ügynök telepítésének beállításai**válassza **Azure Log Analytics** > **tovább**.
5. Kattintson a **Hozzáadás** hozzáadása egy új Log Analytics-munkaterületet. Illessze be a munkaterület Azonosítóját és kulcsát, a portálról kimásolt. Kattintson a **tovább**.

A parancssorból vagy egy automatizált módszer, például az Azure Automation DSC, a System Center Configuration Manager, vagy egy Azure Resource Manager-sablon használatával, ha a helyi adatközpontban telepített Microsoft Azure Stack segítségével telepítse az ügynököt. [További](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) ezen módszerek használatáról az MMA-ügynök telepítése.

#### <a name="install-the-agent-on-a-linux-machine"></a>Az ügynök telepítése Linux rendszerű gépen

Az ügynök telepítése Linux rendszerű gépen:

1. Vigye át a megfelelő csomagot (x86 vagy x64) a Linux rendszerű számítógép scp/sftp használatával.
2. A csomag telepítéséhez használja az--install argumentum.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Az ügynök telepítése a System Center Operations Manager által figyelt gépen

A gépek figyelt az Operations Manager 2012 R2 vagy újabb nem az MMA-ügynök telepítése nem szükséges. A Service Map rendelkezik, amely az Operations Manager MMA a szükséges függőséget adatgyűjtés az Operations Manager-integráció. Az útmutató segítségével az integráció engedélyezheti [Itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Vegye figyelembe azonban, hogy a függőségi ügynököt kell telepíteni, ezek a gépek.

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök telepítése a Windows-gépen, kattintson duplán a telepítőfájlra, és kövesse a varázsló utasításait.
2. A függőségi ügynök telepítése Linux rendszerű gépen telepítse a következő parancsot rendszergazdaként:

    ```sh InstallDependencyAgent-Linux64.bin```

További információ a függőségi ügynök támogatása a [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) és [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operációs rendszereket.

[További](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) hogyan használja a szkriptek a függőségi ügynököt.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Pontosíthatja a csoportot, a függőségek képi megjelenítésének alapján
Miután a csoport összes gépen telepített ügynökök, a függőségeket, a csoport megjelenítheti és pontosítsa a következő az alábbi lépéseket.

1. Az Azure Migrate-projektben alatt **kezelés**, kattintson a **csoportok**, és válassza ki azt a csoportot.
2. A csoport lapján, kattintson a **függőségek megtekintése**, a csoport függőségi térkép megnyitásához.
3. A függőségi térkép a csoport a következő részleteket tartalmazza:
    - (Ügyfelek) a bejövő és kimenő (kiszolgálók) TCP-kapcsolatok és- tárolókról a csoport részét képező összes gép
        - A függő gépek, amelyeken nincs telepítve az MMA és a függőségi ügynök portszámok szerint vannak csoportosítva
        - Az MMA és a függőségi ügynök telepítve van a függő gépek külön mezőkben jelennek meg
    - A gépen futó folyamatokat, bővítheti, minden gép, ha azt szeretné, hogy a folyamatok megtekintése
    - Például a teljes tartománynév, az operációs rendszer, az egyes gépek MAC-cím stb tulajdonságait, kattintson a minden gép, ha azt szeretné, hogy ezek a részletek megtekintéséhez

     ![Csoportos függőségek megtekintése](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. A függőségek részletesebb megtekintéséhez kattintson az időtartományt, módosíthatja azt. Alapértelmezés szerint a tartománya egy órán keresztül. Módosíthatja az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.

    > [!NOTE]
      A függőségek képi megjelenítéséről felhasználói felület jelenleg nem támogatja egy óránál hosszabb időtartomány kiválasztását. A Log Analytics használatához [a függőségi adatok lekérdezése](https://docs.microsoft.com/azure/migrate/how-to-create-a-group#query-dependency-data-from-log-analytics) hosszabb ideig keresztül.

4. Ellenőrizze a függő gépek, a folyamat minden gépen futó, és azonosítja azokat a gépeket kell hozzáadott vagy eltávolított a csoportból.
5. Ctrl + kattintás segítségével válassza ki a gépet a térképen hozzáadása vagy eltávolítása a csoportból.
    - Csak a felderített gépek adhat hozzá.
    - Hozzáadásával és a gép eltávolítása egy csoportból érvényteleníti a korábbi, értékelések.
    - Igény szerint létrehozhat egy új értékelés, ha a csoport módosítása.
5. Kattintson a **OK** menteni a csoportot.

    ![Adja hozzá, vagy a gépek eltávolítása](./media/how-to-create-group-dependencies/add-remove.png)

Ha le szeretne ellenőrizni egy adott géphez, amely a csoport függőségi térképe jelenik meg a függőségek [gépfüggőségi leképezések beállítása](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-log-analytics"></a>A Log Analytics függőségi adatok lekérdezése

A Service Map által rögzített függőségi adatokat érhető el a lekérdezés a a Log Analytics workspare társítva az Azure Migrate-projekt. [További](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) kapcsolatban a Service Map adattáblák a Log Analytics-lekérdezést. 

A Log Analytics-lekérdezések futtatása:

1. Miután telepítette az ügynököket, nyissa meg a portálon, majd kattintson **áttekintése**.
2. A **áttekintése**, lépjen a **Essentials** szakaszában a projektet, majd kattintson a munkaterület neve melletti megadva **OMS-munkaterület**.
3. A Log Analytics munkaterület oldalán kattintson a **általános** > **naplók**.
4. A Log Analytics használatával információkat gyűjthet dependency lekérdezés írása. Mintalekérdezések információkat gyűjthet dependency érhetők el [Itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. A lekérdezés futtatásához kattintson a Futtatás. 

[További](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) Log Analytics-lekérdezések írásával kapcsolatban. 


## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) kapcsolatban a gyakori kérdések a függőségek képi megjelenítéséről.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
