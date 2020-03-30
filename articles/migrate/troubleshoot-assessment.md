---
title: Értékelés és függőségi megjelenítés hibaelhárítása az Azure Áttelepítés szolgáltatásban
description: Segítséget kaphat az Azure Migrate szolgáltatásban végzett hibaértékelési és függőségi vizualizációk elhárításához.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127679"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Értékelés/függőségek vizualizációjának hibaelhárítása

Ez a cikk segítséget nyújt az Azure [Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)használatával kapcsolatos értékelési és függőségi vizualizációval kapcsolatos problémák elhárításához.


## <a name="assessment-readiness-issues"></a>Értékelési készültségi problémák

Az értékeléskészültségi problémákat az alábbiak szerint oldja meg:

**Probléma** | **Hibajavítás**
--- | ---
Nem támogatott rendszerindítási típus | Az Azure nem támogatja az EFI rendszerindítási típusú virtuális gépeket. Javasoljuk, hogy az áttelepítés futtatása előtt konvertálja a rendszerindítási típust BIOS-ra. <br/><br/>Az Azure Áttelepítési kiszolgáló áttelepítése segítségével az ilyen virtuális gépek áttelepítésének kezeléséhez. Az áttelepítés során átalakítja a virtuális gép rendszerindító típusát BIOS-ra.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer lejárt a támogatás dátuma, és egyéni támogatási szerződésre (CSA) van szüksége [az Azure-beli támogatáshoz.](https://aka.ms/WSosstatement) Fontolja meg a frissítést, mielőtt áttelepülne az Azure-ba.
Nem támogatott Windows operációs rendszer | Az Azure csak a [windowsos operációs rendszer egyes verzióit](https://aka.ms/WSosstatement)támogatja. Fontolja meg a számítógép frissítését, mielőtt áttelepülne az Azure-ba.
Feltételesen jóváhagyott Linux operációs rendszer | Az Azure csak a [kiválasztott Linux operációs rendszer verzióit](../virtual-machines/linux/endorsed-distros.md)támogatja. Fontolja meg a számítógép frissítését, mielőtt áttelepülne az Azure-ba.
Nem jóváhagyott Linux operációs rendszer | Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt támogatást az operációs rendszerhez. Fontolja meg a [jóváhagyott Linux-verzióra](../virtual-machines/linux/endorsed-distros.md) való frissítést, mielőtt áttelepülne az Azure-ba.
Ismeretlen operációs rendszer | A virtuális gép operációs rendszere "Egyéb" értékként lett megadva a vCenter Server rendszerben. Ez a viselkedés blokkolja az Azure Áttelepítési a virtuális gép Azure-készenlétének ellenőrzése. Győződjön meg arról, hogy az Azure [támogatja](https://aka.ms/azureoslist) az operációs rendszert a számítógép áttelepítése előtt.
Nem támogatott bites verzió | Előfordulhat, hogy a 32 bites operációs rendszerrel rendelkező virtuális gépek elindulnak az Azure-ban, de azt javasoljuk, hogy az Azure-ba való áttelepítés előtt frissítsen 64 bitesre.
Microsoft Visual Studio-előfizetés szükséges | A számítógépen Windows ügyféloperációs rendszer fut, amelyet csak Visual Studio-előfizetés támogat.
A virtuális gép nem található a szükséges tárolási teljesítményhez | A géphez szükséges tárolási teljesítmény (bemeneti/kimeneti műveletek másodpercenkénti [IOPS] és átviteli teljesítmény) meghaladja az Azure virtuális gép támogatását. Csökkentse a gép tárolási igényeit az áttelepítés előtt.
A virtuális gép nem található a szükséges hálózati teljesítményhez | A számítógéphez szükséges hálózati teljesítmény (be/ki) meghaladja az Azure virtuális gép támogatását. Csökkentse a gép hálózati követelményeit.
A virtuális gép nem található a megadott helyen | Az áttelepítés előtt használjon másik célhelyet.
Egy vagy több nem megfelelő lemez | A virtuális géphez csatlakoztatott egy vagy több lemez nem felel meg az Azure követelményeinek. A<br/><br/> Azure Migrate: Server Assessment jelenleg nem támogatja az Ultra SSD-lemezek, és értékeli a lemezek et a prémium szintű felügyelt lemezek (32 TB) lemezkorlátai alapján.<br/><br/> A virtuális géphez csatlakoztatott minden egyes lemez esetében győződjön meg arról, hogy a lemez mérete < 64 TB (ultra SSD-lemezek támogatják).<br/><br/> Ha nem, csökkentse a lemez méretét, mielőtt az Azure-ba való áttelepítés, vagy több lemezt az Azure-ban, és [csíkozni őket együtt,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) hogy magasabb tárolási korlátokat. Győződjön meg arról, hogy az Azure [által felügyelt virtuálisgép-lemezek támogatják](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)az egyes lemezekhez szükséges teljesítményt (IOPS és átviteli teljesítmény).
Egy vagy több nem megfelelő hálózati adapter. | Az áttelepítés előtt távolítsa el a nem használt hálózati adaptereket a készülékről.
A lemezszám túllépi a korlátot | Áttelepítés előtt távolítsa el a nem használt lemezeket a gépről.
A lemez mérete meghaladja a korlátot | Azure Migrate: Server Assessment jelenleg nem támogatja az Ultra SSD-lemezek, és értékeli a lemezeket a prémium szintű lemezkorlátok (32 TB) alapján.<br/><br/> Az Azure azonban legfeljebb 64 TB méretű lemezeket támogat (ultra SSD-lemezek támogatják). A lemezeket az áttelepítés előtt 64 TB-nál kisebb rezsugorítsa, vagy használjon több lemezt az Azure-ban, és [csíkozott őket a](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) magasabb tárolási korlátok lehetővé teszi.
A lemez nem érhető el a megadott helyen | Az áttelepítés előtt győződjön meg arról, hogy a lemez a célhelyen van.
A lemez nem érhető el a megadott redundanciához | A lemeznek az értékelési beállításokban meghatározott redundanciatárolási típust kell használnia (alapértelmezés szerint LRS).
Belső hiba miatt nem állapítható meg a lemez alkalmassága | Próbáljon meg új értékelést létrehozni a csoport számára.
Virtuális gép a szükséges magokkal és a memóriával nem található | Az Azure nem talált megfelelő virtuálisgép-típust. Az áttelepítés előtt csökkentse a helyszíni gép memóriáját és magjainak számát.
Belső hiba miatt nem lehetett meghatározni a virtuális gép alkalmasságát | Próbáljon meg új értékelést létrehozni a csoport számára.
Egy vagy több lemez alkalmassága belső hiba miatt nem határozható meg | Próbáljon meg új értékelést létrehozni a csoport számára.
Egy vagy több hálózati adapter alkalmassága belső hiba miatt nem határozható meg | Próbáljon meg új értékelést létrehozni a csoport számára.

## <a name="linux-vms-are-conditionally-ready"></a>A Linux virtuális gépek "feltételesen készen állnak"

A Kiszolgálófelmérés a Linux-virtuális gépeket "feltételesen készként" jelöli meg a kiszolgálófelmérés ismert rése miatt.

- A rés megakadályozza, hogy észlelje a helyszíni virtuális gépeken telepített Linux operációs rendszer alverzióját.
- Az RHEL 6.10 esetében például jelenleg a Kiszolgálófelmérés csak rhel 6-ot észlel operációs rendszer-verzióként.
-  Mivel az Azure csak a Linux csak bizonyos verzióit támogatja, a Linux virtuális gépek jelenleg feltételesen készen állnak a kiszolgálói értékelésben.
- Az [Azure Linux-támogatás](https://aka.ms/migrate/selfhost/azureendorseddistros)áttekintésével meghatározhatja, hogy a helyszíni virtuális gépen futó Linux operációs rendszer támogatott-e az Azure-ban.
-  Miután ellenőrizte a jóváhagyott terjesztést, figyelmen kívül hagyhatja ezt a figyelmeztetést.

## <a name="azure-skus-bigger-than-on-premises"></a>A helyszíninál nagyobb Azure-sK-k

Az Azure Áttelepítési kiszolgáló értékelése javasolhatja az Azure VM sk-ek több mag és a memória, mint a jelenlegi helyszíni foglalás alapján a felmérés típusa:


- A virtuális gép termékváltozatának javaslata az értékelési tulajdonságoktól függ.
- Ezt befolyásolja a Kiszolgálófelmérés: *Teljesítményalapú*vagy Mint a *helyszíni*alkalmazásban végzett értékelés típusa.
- A teljesítményalapú értékelések esetében a Kiszolgálófelmérés a helyszíni virtuális gépek (CPU, memória, lemez és hálózati kihasználtság) kihasználtsági adatait veszi figyelembe a helyszíni virtuális gépek megfelelő célvirtuális gép termékváltozatának meghatározásához. Ez is összead egy vigasztal tényező mikor meghatározó hatásos használat.
- A helyszíni méretezés, a teljesítményadatok nem veszik figyelembe, és a cél termékváltozat ajánlott helyszíni foglalás alapján.

Ha meg szeretné mutatni, hogy ez hogyan befolyásolhatja a javaslatokat, vegyünk egy példát:

Van egy helyszíni virtuális gép, négy mag és nyolc GB memória, 50%-os CPU-kihasználtság és 50%-os memóriakihasználtság, és egy meghatározott kényelmi tényező 1.3.

-  Ha a felmérés a **helyszíni,** egy Azure Virtuális gép termékváltozat négy maggal és 8 GB memóriával ajánlott.
- Ha az értékelés teljesítményalapú, a processzor és a memória hatékony kihasználtsága (a 4 mag 50%-a * 1,3 = 2,6 mag és a 8 GB-os memória 50%-a * 1,3 = 5,3 GB memória), a legolcsóbb virtuális gép SKU négy magból (legközelebbi támogatott magszám) és nyolc GB memória (legközelebbi támogatott memóriaméret) használata ajánlott.
- [További információ](concepts-assessment-calculation.md#types-of-assessments) az értékelések méretezéséről.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>A helyszíninál nagyobb Azure-lemezes készlet

Az Azure Áttelepítési kiszolgáló értékelése javasolhat egy nagyobb lemez t a felmérés típusa alapján.
- A kiszolgálói értékelés lemezméretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól.
- Ha a méretezési feltételek **teljesítményalapúak,** és a tárolótípusa **Automatikus**, az IOPS és a lemez átviteli értékeinek értéke a céllemez típusának (standard HDD, Standard SSD vagy Premium) azonosításakor lesz figyelembe véve. Ezután ajánlott a lemez típusából származó lemeztermékváltozat használata, és a javaslat figyelembe veszi a helyszíni lemez méretkövetelményeit.
- Ha a méretezési feltételek **teljesítményalapú,** és a tárolási típus **prémium szintű,** az Azure-ban egy prémium szintű lemez termékváltozat ajánlott az IOPS, átviteli és méretkövetelmények alapján a helyszíni lemez. Ugyanez a logika a lemezméretezéshez is használható, ha a méretezési feltételek **a helyszíni minta,** a tárolási típus pedig **standard HDD**, **Standard SSD**vagy **Premium**.

Például, ha egy helyszíni lemez 32 GB memóriával rendelkezik, de a lemez összesített olvasási és írási IOPS-a 800 IOPS, a Server Assessment prémium szintű lemezt javasol (a magasabb IOPS-követelmények miatt), majd egy olyan lemeztermék-változatot javasol, amely támogatja a szükséges IOPS és mérete. Ebben a példában a legjobb megoldást a P15-ös változat (256 GB, 1100 IOPS) adja. Annak ellenére, hogy a helyszíni lemez mérete 32 GB volt, a Server Assessment nagyobb lemezt javasol a helyszíni lemez magas IOPS-követelménye miatt.

## <a name="utilized-corememory-percentage-missing"></a>A felhasznált mag/memória százaléka hiányzik

A kiszolgálóértékelése "PercentageOfCoresUtilizedMissing" vagy "PercentageOfMemoryUtilizedMissing" jelentést készít, ha az Azure Migrate készülék nem tudja összegyűjteni a megfelelő helyszíni virtuális gépek teljesítményadatait.

- Ez akkor fordulhat elő, ha a virtuális gépek ki vannak kapcsolva az értékelés időtartama alatt. A készülék nem tud teljesítményadatokat gyűjteni a virtuális géphez, ha ki van kapcsolva.
- Ha csak a memóriaszámlálók hiányoznak, és megpróbálja felmérni a Hyper-V virtuális gépeket, ellenőrizze, hogy van-e dinamikus memória engedélyezve ezeken a virtuális gépeken. Van egy ismert probléma csak a Hyper-V virtuális gépek, amelyben egy Azure Migrate készülék nem tudja összegyűjteni a memória kihasználtsági adatokat a virtuális gépek, amelyek nem rendelkeznek a dinamikus memória engedélyezve van.
- Ha a teljesítményszámlálók bármelyike hiányzik, az Azure Áttelepítési kiszolgáló értékelése visszaesik a lefoglalt magok és a memória, és azt javasolja, hogy a megfelelő virtuális gép méretét.
- Ha az összes teljesítményszámláló hiányzik, győződjön meg arról, hogy a porthoz való hozzáférés reklezési követelményei teljesülnek. További információ a [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access)a [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) és a [fizikai](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) kiszolgálók állapotfelmérésének porthozzáférési követelményeiről.

## <a name="is-the-operating-system-license-included"></a>Az operációs rendszer licencét tartalmazza?

Az Azure Migrate Server Assessment jelenleg csak a Windows-gépek operációsrendszer-licencköltségét veszi figyelembe. A Linux-gépek licencköltségeit jelenleg nem veszik figyelembe.

## <a name="how-does-performance-based-sizing-work"></a>Hogyan működik a teljesítményalapú méretezés?

A Server Assessment folyamatosan gyűjti a helyszíni gépek teljesítményadatait, és ezek alapján tesz javaslatot az Azure-beli virtuálisgép- és lemez-termékváltozatra. [Ismerje meg, hogyan](concepts-assessment-calculation.md#calculate-sizing-performance-based) történik a teljesítményalapú adatok gyűjtése.


## <a name="dependency-visualization-in-azure-government"></a>Függőségi megjelenítés az Azure Governmentben

Az Azure Áttelepítés függ szolgáltatástérkép a függőségi vizualizációs funkció. Mivel a Szolgáltatástérkép jelenleg nem érhető el az Azure Government ben, ez a funkció nem érhető el az Azure Government ben.

## <a name="dependencies-dont-show-after-agent-install"></a>A függőségek nem jelennek meg az ügynök telepítése után

Miután telepítette a függőségi vizualizációs ügynököket a helyszíni virtuális gépeken, az Azure Migrate általában 15–30 percet vesz igénybe a függőségek megjelenítése a portálon. Ha több mint 30 percet várt, győződjön meg arról, hogy a Microsoft Monitoring Agent (MMA) csatlakozhat a Log Analytics munkaterülethez.

Windows virtuális gépek esetén:
1. A Vezérlőpulton indítsa el az MMA-t.
2. A **Microsoft Monitoring Agent tulajdonságai** > **Azure Log Analytics (OMS)** győződjön meg arról, hogy a munkaterület **állapota** zöld.
3. Ha az állapot nem zöld, próbálja meg eltávolítani a munkaterületet, és adja hozzá újra az MMA-hoz.

    ![MMA-állapot](./media/troubleshoot-assessment/mma-properties.png)

Linuxos virtuális gépek esetén győződjön meg arról, hogy az MMA és a függőségi ügynök telepítési parancsai sikeresek.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- **MMS-ügynök**: Tekintse át a támogatott [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) operációs rendszereket.
- **Függőségi ügynök**: a támogatott [Windows és Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) operációs rendszerek.

## <a name="visualize-dependencies-for--hour"></a>függőségek megjelenítése > órára

Bár az Azure Migrate lehetővé teszi, hogy visszatérjen egy adott dátumra az elmúlt hónapban, a függőségek megjelenítésének maximális időtartama egy óra.

Használhatja például a függőségi térkép időidőtartam-funkcióját a tegnapi függőségek megtekintéséhez, de csak egy órás időszakra tekintheti meg őket.

Azonban az Azure Monitor naplók segítségével [lekérdezheti a függőségi adatok hosszabb](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) időtartamú.

## <a name="visualized-dependencies-for--10-machines"></a>Láthatóvá tették > 10 gép megjelenítési függőségeit

Az Azure Áttelepítési kiszolgáló értékelése, legfeljebb 10 virtuális géptel rendelkező [csoportok függőségeit jelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) meg. Nagyobb csoportok esetén azt javasoljuk, hogy a virtuális gépekfelosztása kisebb csoportokra függőségek megjelenítéséhez.

## <a name="machines-show-install-agent"></a>A gépeken a "Install agent" (Ügynök telepítése)

Miután olyan gépeket telepített át, amelyekfüggőségi vizualizációengedélyezve van az Azure-ba, a gépek a következő viselkedés miatt "Ügynök telepítése" műveletet jeleníthetnek meg a "Függőségek megtekintése" helyett:


- Az Azure-ba való migrálás után a helyszíni gépek ki vannak kapcsolva, és az egyenértékű virtuális gépek az Azure-ban kerülnek fel. Ezek a gépek más MAC-címet kapnak.
- A gépek is előfordulhat, hogy egy másik IP-címet, attól függően, hogy megtartotta-e a helyszíni IP-címet, vagy sem.
- Ha mind a MAC és az IP-címek eltérnek a helyszíni, az Azure Migrate nem társítja a helyszíni gépek et a Service Map-függőségi adatok. Ebben az esetben a függőségek megtekintése helyett az ügynök telepítésének lehetőségét jeleníti meg.
- Az Azure-ba való tesztáttelepítés után a helyszíni gépek a várt módon bekapcsolva maradnak. Az Azure-ban megpördült egyenértékű gépek különböző MAC-címeket szereznek be, és különböző IP-címeket szerezhetnek be. Ha nem blokkolja a kimenő Azure Monitor naplóforgalmat ezekről a gépekről, az Azure Migrate nem társítja a helyszíni gépeket a Service Map függőségi adataival, és így a függőségek megtekintése helyett az ügynökök telepítésének lehetőségét jeleníti meg.


## <a name="capture-network-traffic"></a>Hálózati forgalom rögzítése

A hálózati forgalmi naplók gyűjtése az alábbiak szerint:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyomja le az F12 billentyűt a Fejlesztői eszközök elindításához. Szükség esetén törölje a **törlési bejegyzéseket a navigációs beállításból.**
3. Válassza a **Hálózat** lapot, és kezdje el rögzíteni a hálózati forgalmat:
   - A Chrome-ban válassza **a Napló megőrzése**lehetőséget. A felvételnek automatikusan el kell indulnia. A piros kör azt jelzi, hogy a forgalom rögzítésalatt áll. Ha a piros kör nem jelenik meg, jelölje ki a kezdő fekete kört.
   - A Microsoft Edge és az Internet Explorer programban a felvételnek automatikusan el kell indulnia. Ha nem, válassza a zöld lejátszás gombot.
4. Próbálja meg reprodukálni a hibát.
5. Miután a felvétel közben észlelte a hibát, állítsa le a felvételt, és mentse a rögzített tevékenység egy példányát:
   - A Chrome-ban kattintson a jobb gombbal, és válassza **a Mentés HAR-ként lehetőséget a tartalommal**. Ez a művelet tömöríti és exportálja a naplókat .har fájlként.
   - A Microsoft Edge vagy az Internet Explorer programban jelölje be a **Rögzített forgalom exportálása jelölőnégyzetet.** Ez a művelet tömöríti és exportálja a naplót.
6. A **Konzol** fülre választva ellenőrizze, hogy vannak-e figyelmeztetések vagy hibák. A konzolnapló mentése:
   - A Chrome-ban kattintson a jobb gombbal a konzolnapló tetszőleges pontjára. Válassza a **Mentés másként**lehetőséget az exportáláshoz, és a zip-ela naplót.
   - A Microsoft Edge vagy az Internet Explorer programban kattintson a jobb gombbal a hibákra, és válassza **az Összes másolása parancsot.**
7. Zárja be a fejlesztői eszközöket.

## <a name="next-steps"></a>További lépések

[Értékelés létrehozása](how-to-create-assessment.md) vagy [testreszabása.](how-to-modify-assessment.md)
