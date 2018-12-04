---
title: Egy értékelés csoport és az Azure Migrate csoport függőségi leképezés finomíthatja |} A Microsoft Docs
description: Ismerteti, amellyel pontosíthatja az értékelést a csoport függőségi leképezés használatával az Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: 3b75e8607c85f79bb64c57c2154115c41ebe8366
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835506"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Eszközcsoport-leképezés függőségi csoport pontosítása

Ez a cikk ismerteti, amellyel pontosíthatja a csoportot a csoportban lévő összes gép függőségek vizualizációjával. Általában ezt a módszert, ha szeretne egy meglévő csoportot a tagsági pontosítás csoportfüggőségek kereszt-ellenőrzés az értékelés futtatása előtt. Finomítás a függőségek képi megjelenítésének használatával csoport segítségével hatékonyan tervezheti a migrálást az Azure-bA. Felfedezheti, hogy az összes saját rendszerek esetében, amelyek együtt áttelepíteni. Ez segít győződjön meg arról, hogy semmi sem marad, és meglepetés valamilyen okból kimaradás lép fordul elő, ha az Azure-bA áttelepítésekor.


> [!NOTE]
> Csoportok, amelynek meg szeretné függőségek vizualizálása 10-nél több gép nem tartalmazhat. Ha a csoport több mint 10 gépekkel rendelkezik, azt javasoljuk, hogy azt ossza fel kisebb csoportok kihasználhatja a függőségek képi megjelenítésének funkcióival.


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
Egy csoport függőségek megjelenítéséhez szüksége, töltse le és telepítse az ügynököt minden olyan a csoport tagja a helyi gépen. Ezenkívül, ha internetkapcsolat nélküli gépek, kell letölteni és telepíteni [Log Analytics-átjáró](../azure-monitor/platform/gateway.md) rajtuk.

1. A **áttekintése**, kattintson a **kezelés** > **csoportok**, nyissa meg a szükséges csoport.
2. Gépek, a listában az a **függőségi ügynök** oszlopot, kattintson a **telepítés szükséges** megjelenő arról, hogyan töltse le és telepítse az ügynököket.
3. Az a **függőségek** lapon, töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök minden virtuális gépen, amely a csoport része.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Szükség van ezekre az MMA a helyszíni gépeken való telepítésekor.

### <a name="install-the-mma"></a>Az MMA telepítése

Az ügynök telepítése a Windows-gépen:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3. A **célmappa**, megtartani, vagy módosítsa az alapértelmezett telepítési mappa > **tovább**.
4. A **ügynök telepítésének beállításai**válassza **Azure Log Analytics** > **tovább**.
5. Kattintson a **Hozzáadás** hozzáadása egy új Log Analytics-munkaterületet. Illessze be a munkaterület Azonosítóját és kulcsát, a portálról kimásolt. Kattintson a **Tovább** gombra.


Az ügynök telepítése Linux rendszerű gépen:

1. Vigye át a megfelelő csomagot (x86 vagy x64) a Linux rendszerű számítógép scp/sftp használatával.
2. A csomag telepítéséhez használja az--install argumentum.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök telepítése a Windows-gépen, kattintson duplán a telepítőfájlra, és kövesse a varázsló utasításait.
2. A függőségi ügynök telepítése Linux rendszerű gépen telepítse a következő parancsot rendszergazdaként:

    ```sh InstallDependencyAgent-Linux64.bin```

További információ a függőségi ügynök támogatása a [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) és [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operációs rendszereket.

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
4. Ellenőrizze a függő gépek, a folyamat minden gépen futó, és azonosítja azokat a gépeket kell hozzáadott vagy eltávolított a csoportból.
5. Ctrl + kattintás segítségével válassza ki a gépet a térképen hozzáadása vagy eltávolítása a csoportból.
    - Csak a felderített gépek adhat hozzá.
    - Hozzáadásával és a gép eltávolítása egy csoportból érvényteleníti a korábbi, értékelések.
    - Igény szerint létrehozhat egy új értékelés, ha a csoport módosítása.
5. Kattintson a **OK** menteni a csoportot.

    ![Adja hozzá, vagy a gépek eltávolítása](./media/how-to-create-group-dependencies/add-remove.png)

Ha le szeretne ellenőrizni egy adott géphez, amely a csoport függőségi térképe jelenik meg a függőségek [gépfüggőségi leképezések beállítása](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>További lépések
- [További](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) kapcsolatban a gyakori kérdések a függőségek képi megjelenítéséről.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
