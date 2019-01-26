---
title: A gépek függőségeivel az Azure Migrate |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre értékeléseket függőségeivel az Azure Migrate szolgáltatással.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: a345b410dcf256e8cd07e7708906f5582b5f1828
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077387"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Gépek csoportosítása a gépek függőségi leképezés használatával

Ez a cikk bemutatja, hogyan hozhat létre egy csoportot a gépek [Azure Migrate](migrate-overview.md) értékelés gépek függőségek vizualizációjával. Ez a módszer általában esetén annak ellenőrzéséhez, virtuális gépek magasabb megbízhatósági szintű csoportok szerint kereszt-ellenőrzés gépek függőségeit, az értékelés futtatása előtt célszerű használni. Függőségek képi megjelenítésének segítségével hatékonyan tervezheti a migrálást az Azure-bA. Ez segít győződjön meg arról, hogy semmi sem marad, és meglepetés valamilyen okból kimaradás lép fordul elő, ha az Azure-bA áttelepítésekor. Felfedezheti, hogy az összes saját rendszerek esetében, amelyek együtt áttelepíti, és adja meg, hogy a futó rendszerek továbbra is szolgálja ki felhasználók, illetve egy jelölt helyett áttelepítési leszerelése.

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

## <a name="prepare-for-dependency-visualization"></a>Előkészítéséhez a függőségek képi megjelenítése
Az Azure Migrate a Log Analytics engedélyezése a függőségek vizualizációjához a Service Map megoldást használja.

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
Miután konfigurál egy munkaterületet, akkor kell töltse le és telepítse az ügynököt minden olyan szeretne értékelni a helyszíni gépen. Ezenkívül, ha internetkapcsolat nélküli gépek, kell letölteni és telepíteni [Log Analytics-átjáró](../azure-monitor/platform/gateway.md) rajtuk.

1. A **áttekintése**, kattintson a **kezelés** > **gépek**, és válassza ki a szükséges gépet.
2. Az a **függőségek** oszlopot, kattintson a **ügynökök telepítése**.
3. Az a **függőségek** lapon, töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök minden virtuális Géphez szeretne értékelni.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Szükség van ezekre az MMA a helyi gépen való telepítésekor.

> [!NOTE]
> Az ügynökök telepítésének automatizálásához használhatja bármely üzembe helyezési eszköz például a System Center Configuration Manager vagy a partner eszközzel [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), az Azure Migrate-ügynök telepítési megoldás, amely rendelkezik.

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

[További](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) kapcsolatos Linux operációs rendszerek támogatása az MMA által listája.

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök telepítése a Windows-gépen, kattintson duplán a telepítőfájlra, és kövesse a varázsló utasításait.
2. A függőségi ügynök telepítése Linux rendszerű gépen telepítse a következő parancsot rendszergazdaként:

    ```sh InstallDependencyAgent-Linux64.bin```

További információ a függőségi ügynök támogatása a [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) és [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operációs rendszereket.

[További](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) hogyan használja a szkriptek a függőségi ügynököt.

## <a name="create-a-group"></a>Csoport létrehozása

1. Miután telepítette az ügynököket, nyissa meg a portálon, majd kattintson **kezelés** > **gépek**.
2. Keresse meg a gép, amelyen az ügynökök.
3. A **függőségek** oszlop a gép ekkor látnia kell **függőségek megtekintése**. Kattintson az oszlop a gép függőségeinek megtekintése.
4. A függőségi térkép a gép a következő részleteket tartalmazza:
    - (Ügyfelek) a bejövő és kimenő (kiszolgálók) TCP-kapcsolatok és- tárolókról a gép
        - A függő gépek, amelyeken nincs telepítve az MMA és a függőségi ügynök portszámok szerint vannak csoportosítva
        - Az MMA és a függőségi ügynök telepítve van a függő gépek külön mezőkben jelennek meg
    - A gépen futó folyamatokat, bővítheti, minden gép, ha azt szeretné, hogy a folyamatok megtekintése
    - Például a teljes tartománynév, az operációs rendszer, az egyes gépek MAC-cím stb tulajdonságait, kattintson a minden gép, ha azt szeretné, hogy ezek a részletek megtekintéséhez

      ![Gépek függőségeinek megtekintése](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Tekintse függőségek másik időpontot időtartamok az az időtartam, az az idő tartománycímke kattintva. Alapértelmezés szerint a tartománya egy órán keresztül. Módosíthatja az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.

    > [!NOTE]
      A függőségek képi megjelenítéséről felhasználói felület jelenleg nem támogatja egy óránál hosszabb időtartomány kiválasztását. A Log Analytics használatához [a függőségi adatok lekérdezése](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) hosszabb ideig keresztül.

5. Miután csoportba a kívánt függő gép azonosítása, használatával Ctrl + kattintás jelölje ki több gépet a térképen, és kattintson a **gépeket**.
6. Adjon meg egy csoportnevet. Győződjön meg arról, hogy a függő gépek Azure Migrate által felderített.

    > [!NOTE]
    > Ha egy függő gép az Azure Migrate által nem észlelhető, nem adhat, a csoporthoz. Az ilyen gépeket ad hozzá a csoporthoz, kell futtatnia a felderítési folyamat a vCenter-kiszolgáló a megfelelő hatókörben, és győződjön meg arról, hogy a gép az Azure Migrate által felderített.  

7. Ha azt szeretné, hozzon létre egy értékelést a csoport számára, jelölje be a jelölőnégyzetet a csoport egy új értékelés létrehozása.
8. Kattintson a **OK** menteni a csoportot.

A csoport létrehozása után ajánlott ügynökök telepítése a csoport összes gépen, és a csoportok pontosításához függőséget, a teljes csoport vizualizációjával.

## <a name="query-dependency-data-from-log-analytics"></a>A Log Analytics függőségi adatok lekérdezése

Rögzíti a Service Map a függőségi adatokat a Log Analytics lekérdezéséhez érhető el. [További](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) kapcsolatban a Service Map adattáblák a Log Analytics-lekérdezést. 

A Log Analytics-lekérdezések futtatása:

1. Miután telepítette az ügynököket, nyissa meg a portálon, majd kattintson **áttekintése**.
2. A **áttekintése**, lépjen a **Essentials** szakaszában a projektet, majd kattintson a munkaterület neve melletti megadva **OMS-munkaterület**.
3. A Log Analytics munkaterület oldalán kattintson a **általános** > **naplók**.
4. A Log Analytics használatával információkat gyűjthet dependency lekérdezés írása. Mintalekérdezések információkat gyűjthet dependency érhetők el [Itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. A lekérdezés futtatásához kattintson a Futtatás. 

[További](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) Log Analytics-lekérdezések írásával kapcsolatban. 

## <a name="next-steps"></a>További lépések

- [További](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) kapcsolatban a gyakori kérdések a függőségek képi megjelenítéséről.
- [Ismerje meg, hogyan](how-to-create-group-dependencies.md) , a csoport pontosítása csoportos függőségek vizualizációjával.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
