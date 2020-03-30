---
title: Az Azure Migrate korábbi verziójának megoldása
description: Bemutatja, hogyan lehet az Azure Migrate korábbi verzióját kezelni.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c9cea52e04a991e6e3ac64426f0443939f8aaa3a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77914381"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Az Azure Migrate korábbi verziójának megoldása

Ez a cikk az Azure Migrate korábbi verziójával kapcsolatos információkat tartalmaz.


Az Azure Migrate szolgáltatásnak két verziója van:

- **Jelenlegi verzió:** Ezzel a verzióval azure-áttelepítési projekteket hozhat létre, helyszíni gépeket fedezhet fel, és vezényelhet értékeléseket és áttelepítéseket. [További információ](whats-new.md) a jelen verzió újdonságairól.
- **Előző verzió:** Ha az Azure Migrate korábbi verzióját használja (csak a helyszíni VMware virtuális gépek értékelése támogatott), most használja az aktuális verziót. Ha továbbra is az előző verzióban létrehozott Azure Migrate projekteket kell használnia, a következőket teheti és nem teheti meg:
    - Áttelepítési projektek már nem hozhatnak létre.
    - Azt javasoljuk, hogy ne végezzen új felderítéseket.
    - A meglévő projektek továbbra is elérhetők.
    - Még futtathatja az értékeléseket.
    

## <a name="upgrade-between-versions"></a>Frissítés verziók között

Az előző verzióban lévő projektek vagy összetevők nem frissíthetők az új verzióra. Létre kell [hoznia egy új Azure Migrate projektet,](how-to-add-tool-first-time.md)és hozzá kell adnia értékelési és áttelepítési eszközöket.

## <a name="find-projects-from-previous-version"></a>Projektek keresése az előző verzióból

Keresse meg a projekteket az előző verzióból az alábbiak szerint:

1. Az Azure Portalon > **Minden szolgáltatás,** keressen rá, és válassza az **Azure Migrate**lehetőséget. 
2. Az Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate-projektek eléréséhez.
3. Kattintson a hivatkozásra a v1-es projektek megnyitásához.


## <a name="create-an-assessment"></a>Értékelés létrehozása

Miután a virtuális gépek et felderítik a portálon, csoportosítja őket, és értékeléseket hoz létre.

- Azonnal hozhat létre helyszíni értékelések közvetlenül a virtuális gépek a portálon való felfedezése után.
- A teljesítményalapú értékelésekhez azt javasoljuk, hogy várjon legalább egy napot a teljesítményalapú értékelés létrehozása előtt, hogy megbízható méretjavaslatokat kapjon.

Hozzon létre egy értékelést az alábbiak szerint:

1. A projekt **Áttekintés** lapján kattintson az **+Értékelés létrehozása** elemre.
2. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.
3. Hozza létre a csoportot, és adja meg a nevét.
4. Válassza ki a csoporthoz hozzáadni kívánt gépeket.
5. A csoport és az értékelés létrehozásához kattintson az **Értékelés létrehozása** elemre.
6. Az értékelés létrehozása után tekintse meg az **Áttekintő** > **irányítópulton.**
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.

Ha szeretné egy meglévő értékelését a legújabb teljesítményadatokkal frissíteni, akkor ezt az értékelés **Újraszámítás** parancsával teheti meg.

## <a name="review-an-assessment"></a>Értékelés áttekintése 

Az értékelés három szakaszból áll:

- Az értékelés egy alkalmassági elemzéssel kezdődik annak megállapítására, hogy a gépek kompatibilisek-e az Azure-ban.
- Méretezési becslések.
- Havi költségbecslés.

A gép csak akkor halad tovább egy későbbi szakaszba, ha átmegy az előzőn. Ha például egy gép nem felel meg az alkalmassági ellenőrzésen, az Azure számára alkalmatlanként van megjelölve, és a méretezés és a költségszámítás nem történik meg.


### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

Az értékelésben az Azure használatához szükséges állapotnézet az egyes virtuális gépek készenléti állapotát jeleníti meg.

**Készenlét** | **Állapot** | **Részletek**
--- | --- | ---
Készen áll az Azure-beli használatra | Nincs kompatibilitási probléma. A gép ugyanúgy áttelepíthető az Azure-ba, hogy teljes Körű Azure-támogatással induljon el az Azure-ban. | A kész virtuális gépekhez az Azure Migrate javasol egy virtuálisgép-méretet az Azure-ban.
Feltételesen készen áll az Azure-beli használatra | Előfordulhat, hogy a gép elindul az Azure-ban, de előfordulhat, hogy nem rendelkezik teljes Azure-támogatással. Például egy gép egy régebbi Windows Server-verzió, amely nem támogatja az Azure-ban. | Az Azure Migrate ismerteti a készültségi problémákat, és javítási lépéseket biztosít.
Nem áll készen az Azure-beli használatra |  A virtuális gép nem indul el az Azure-ban. Például ha egy virtuális gép rendelkezik egy lemez, amely több, mint 4 TB, nem üzemeltethető az Azure-ban. | Az Azure Migrate ismerteti a készültségi problémákat, és javítási lépéseket biztosít.
A kompatibilitás ismeretlen | Az Azure Migrate nem tudja azonosítani az Azure-ra való felkészültséget, általában azért, mert az adatok nem érhetők el.


#### <a name="azure-vm-properties"></a>Az Azure virtuális gép tulajdonságai
A készenlét számos virtuális géptulajdonságot vesz figyelembe annak megállapítására, hogy a virtuális gép futtatható-e az Azure-ban.


**Tulajdonság** | **Részletek** | **Készenlét**
--- | --- | ---
**Rendszerindítás típusa** | A BIOS támogatott. Az UEFI nem támogatott. | Feltételesen készen áll, ha a rendszerindítás típusa UEFI.
**Cores** | A gépek magja <= az Azure virtuális gépek által támogatott magok maximális száma (128).<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate figyelembe veszi a használt magokat.<br/>Ha az értékelési beállításokban meg van adva egy komforttényező, a felhasznált magok számát megszorozza a komforttényező.<br/><br/> Ha nincs teljesítményelőzmények, az Azure Migrate a kiosztott magokat használja, a kényelmi tényező alkalmazása nélkül. | Készen áll, ha kisebb vagy egyenlő a határértékeket.
**Memory (Memória)** | A gép memóriamérete <= a maximális memória (3892 GB Az Azure M sorozat Standard_M128m&nbsp;<sup>2)</sup>egy Azure virtuális gép. [További információ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate figyelembe veszi a felhasznált memóriát.<br/><br/>Ha meg van adva egy kényelmi tényező, a felhasznált memóriát megszorozza a komforttényező.<br/><br/> Ha nincs előzmény, a lefoglalt memória a komforttényező alkalmazása nélkül használatos.<br/><br/> | Készen állunk, ha a határokon belül.
**Tárolólemez** | A lemez lefoglalt méretének legkisebbméretűnek (4096 GB) kell lennie.<br/><br/> A számítógéphez csatlakoztatott lemezek számának legkisebbnek kell lennie, beleértve az operációs rendszer lemezét is. | Készen állunk, ha a határokon belül.
**Hálózat** | A géphez leg32 vagy annál kevesebb hálózati adapternek kell hozzákapcsolódnia. | Készen állunk, ha a határokon belül.

#### <a name="guest-operating-system"></a>Vendég operációs rendszer

A virtuális gép tulajdonságaival együtt az Azure Migrate is megnézi a helyszíni virtuális gép vendég operációs rendszerét, hogy azonosítsa, hogy a virtuális gép futtatható-e az Azure-ban.

- Az Azure Migrate a vCenter Serverben megadott operációs rendszert veszi figyelembe.
- Mivel az Azure Migrate által végzett felderítés berendezésalapú, nem rendelkezik módtal annak ellenőrzésére, hogy a virtuális gépen belül futó operációs rendszer megegyezik-e a vCenter-kiszolgálóban megadott webszolgáltatással.

A következő logika használatos.

**Operációs rendszer** | **Részletek** | **Készenlét**
--- | --- | ---
Windows Server 2016 és az összes sp | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 és az összes SP | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2012 és az összes sp | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 és az összes SP | Az Azure teljes körű támogatást nyújt.| Készen áll az Azure-beli használatra
Windows Server 2008 (32 és 64 bites) | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2003, 2003 R2 | Az Azure-beli támogatáshoz nem támogatott, és [egyéni támogatási szerződésre (CSA)](https://aka.ms/WSosstatement) van szüksége. | Feltételesen készen áll az Azure-ra, fontolja meg az operációs rendszer frissítését, mielőtt átkontatna az Azure-ba.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Kikerült a támogatásból. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer-támogatást. | Feltételesen készen áll az Azure-ra, javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Windows-ügyfél 7, 8 és 10 | Az Azure [csak visual studio-előfizetéssel](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztal | Az Azure támogatást nyújt a [több-bérlős üzemeltetési jogokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Kikerült a támogatásból. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer-támogatást. | Feltételesen készen áll az Azure-ra, javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket.](../virtual-machines/linux/endorsed-distros.md) Más Linux operációs rendszerek is indulhat az Azure-ban, de azt javasoljuk, hogy frissítse az operációs rendszer egy jóváhagyott verzióra, mielőtt az Azure-ba való áttérés. | Készen áll az Azure-ra, ha a verzió t.<br/><br/>Feltételesen kész, ha a verzió nincs jóváhagyva.
Egyéb operációs rendszerek<br/><br/> Például Oracle Solaris, Apple Mac OS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. A gép elindítható az Azure-ban, de az Azure nem nyújt operációs rendszer-támogatást. | Feltételesen készen áll az Azure-ra, javasoljuk, hogy telepítsen egy támogatott operációs rendszert, mielőtt áttelepülne az Azure-ba.  
A vCenter-kiszolgálón **másként** megadott operációs rendszer | Az Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készenlét. Győződjön meg arról, hogy a virtuális gépen belül futó operációs rendszer támogatja az Azure-ban.
32 bites operációs rendszerek | A gép elindítható az Azure-ban, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra, fontolja meg a számítógép operációs rendszerének 32 bites operációs rendszerről 64 bites operációs rendszerre való frissítését, mielőtt áttérne az Azure-ra.


### <a name="review-sizing"></a>Méretezés áttekintése

 Az Azure Áttelepítés mérete javaslat attól függ, hogy a méretezési feltétel az értékelési tulajdonságokban megadott.

- Ha a méretezés teljesítményalapú, a méretjavaslat figyelembe veszi a virtuális gépek (CPU és a memória) és a lemezek (IOPS és átviteli teljesítmény) teljesítményelőzményeit.
- Ha a méretezési feltétel "helyszíni", az Azure-ban a méretjavaslat a virtuális gép helyszíni mérete alapján. A lemezméretezés az értékelési tulajdonságokban megadott Tárolási típuson alapul (az alapértelmezett a prémium szintű lemezek). Az Azure Migrate nem veszi figyelembe a virtuális gép és a lemezek teljesítményadatait.

### <a name="review-cost-estimates"></a>Költségbecslések áttekintése

A költségbecslések a virtuális gépek Azure-beli futtatásának teljes számítási és tárolási költségét, valamint az egyes gépek részleteit mutatják.

- A költségbecslések kiszámítása a virtuális gép és lemezei méretjavaslata, valamint az értékelési tulajdonságok alapján történik.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.
- A költségbecslés a helyszíni virtuális gép Azure Infrastructure szolgáltatásként (IaaS) virtuális gépekként való futtatásához. Az Azure Migrate nem tekinti a Platformot szolgáltatásnak (PaaS) vagy szoftvernek szolgáltatásként (SaaS).

### <a name="review-confidence-rating-performance-based-assessment"></a>A megbízhatósági minősítés áttekintése (teljesítményalapú értékelés)

Minden teljesítményalapú értékelés megbízhatósági minősítéssel van társítva.

- A megbízhatósági besorolás az egycsillagostól az ötcsillagosig terjed (az egycsillagos a legalacsonyabb, az ötcsillagos a legmagasabb).
- A megbízhatósági minősítés egy értékeléshez van rendelve, amely az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- Megbízhatósági minősítés nem érhető el a helyszíni értékelések "as-is".Confidence rating is isn't available for "as-is" on-premises assessments.

A teljesítményalapú méretezéshez az Azure Migrate-nek a következőkre van szüksége:
- A processzor kihasználtsági adatai.
- Virtuálisgép memóriaadatai.
- A virtuális géphez csatlakoztatott minden lemezhez szüksége van a lemez IOPS-és átviteli adatok.
- A virtuális géphez csatlakoztatott minden egyes hálózati adapterhez az Azure Migrate-nek szüksége van a hálózati bemenetre/kimenetre.
- Ha a fentiek bármelyike nem érhető el, méretjavaslatok (és így megbízhatósági minősítések) nem lehet megbízható.


A rendelkezésre álló adatpontok százalékos arányától függően a lehetséges megbízhatósági minősítések a táblázatban kerülnek összegzésre.

**Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag


#### <a name="assessment-issues-affecting-confidence-ratings"></a>A megbízhatósági minősítéseket érintő értékelési problémák

Előfordulhat, hogy az értékelés nem rendelkezik az összes rendelkezésre álló adatponttal, mivel számos oka van:

- Nem profilozta a környezetét az értékelés időtartama alatt. Ha például úgy hozza létre az értékelést, hogy a teljesítmény időtartama egy napra van állítva, legalább egy napot kell várnia a felderítés megkezdése után, vagy az összes összegyűjtendő adatpontot.
- Egyes virtuális gépek leálltak abban az időszakban, amelyre az értékelést számították ki. Ha bármelyik virtuális gép ki van kapcsolva az időtartam egy részében, az Azure Migrate nem tud teljesítményadatokat gyűjteni az adott időszakra vonatkozóan.
- Néhány virtuális gép jött létre a kettő között az értékelési számítási időszak alatt. Ha például egy értékelést hoz létre a múlt havi teljesítményelőzmények használatával, de hozzon létre egy hétkel ezelőtt a környezetben több virtuális gépet, az új virtuális gépek teljesítményelőzményei nem lesznek a teljes időtartamalatt.

> [!NOTE]
> Ha bármely értékelés megbízhatósági besorolása ötcsillag alatt van, várjon legalább egy napot, amíg a készülék profilozná a környezetet, majd újrakell számítania az értékelést. Ha nem teljesítményalapú méretezés e nem megbízható. Ha nem szeretné újraszámolni, javasoljuk, hogy a helyszíni méretezésként váltson át az értékelési tulajdonságok módosításával.



## <a name="create-groups-using-dependency-visualization"></a>Csoportok létrehozása függőségi megjelenítéssel

A csoportok manuális létrehozása mellett függőségi vizualizációval is létrehozhat csoportokat.
- Ezt a módszert általában akkor használja, ha magasabb megbízhatósági szinttel rendelkező csoportokat szeretne felmérni a gépfüggőségek keresztellenőrzésével, mielőtt egy felmérést futtatna.
- A függőségi megjelenítés segítségével hatékonyan tervezheti meg az Azure-ba való migrálást. Ez segít biztosítani, hogy semmi sem marad hátra, és hogy a meglepetés kimaradások nem fordulnak elő, amikor az Azure-ba való áttérés.
- Felderítheti az összes egymástól függő rendszert, amelyeknek együtt kell áttérniük, és azonosíthatja, hogy egy futó rendszer továbbra is szolgálja-e a felhasználókat, vagy az áttelepítés helyett leszerelésre jelölt.You can discover all interdependent systems that need to migrate together and identify whether a running system is serving users or is a candidate for decommissioning instead of migration.
- Az Azure Migrate a Service Map-megoldást használja az Azure Monitorban a függőségi vizualizáció engedélyezéséhez.

> [!NOTE]
> Függőségi megjelenítés nem érhető el az Azure Government.

Függőségi vizualizáció beállításához társítson egy Log Analytics-munkaterületet egy Azure Migrate projekttel, telepítsen ügynököket olyan gépekre, amelyekfüggőségeit vizualizálni szeretné, majd csoportokat hozzon létre a függőségi adatok használatával. 



### <a name="associate-a-log-analytics-workspace"></a>Log Analytics-munkaterület társítása

Függőségi vizualizáció használatához a Log Analytics munkaterületet egy áttelepítési projekthez kell társítani. Csak abban az előfizetésben hozhat létre vagy csatolhat munkaterületet, ahol az áttelepítési projekt létrejön.

1. Ha Log Analytics-munkaterületet szeretne csatolni egy projekthez, az **Áttekintés**> **Essentials**programban kattintson **a Konfigurációra van szüksége**gombra.
2. Létrehozhat új munkaterületet, vagy csatolhat egy meglévőt:
  - Új munkaterület létrehozásához adjon meg egy nevet. A munkaterület egy olyan régióban jön létre, amely ugyanabban az [Azure-földrajzi helyen](https://azure.microsoft.com/global-infrastructure/geographies/) található, mint az áttelepítési projekt.
  - Meglévő munkaterület csatolásakor az áttelepítési projekttel azonos előfizetésben lévő összes rendelkezésre álló munkaterület közül választhat. Csak azok a munkaterületek vannak felsorolva, amelyek et [egy támogatott Szolgáltatástérkép-régióban](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)hoztak létre. Munkaterület csatolásához győződjön meg arról, hogy "Olvasó" hozzáféréssel rendelkezik a munkaterülethez.

> [!NOTE]
> Az áttelepítési projekthez társított munkaterület nem módosítható.

### <a name="download-and-install-vm-agents"></a>Virtuálisgép-ügynökök letöltése és telepítése

A munkaterület konfigurálása után minden kiértékelni kívánt helyszíni gépen letölti és telepíti az ügynököket. Ezenkívül ha rendelkezik internetkapcsolattal nem rendelkező gépekkel, le kell töltenie és telepítenie kell rájuk a [Log Analytics átjárót.](../azure-monitor/platform/gateway.md)

1. Az **Áttekintés menüben**kattintson a**Gépek** **kezelése** > gombra, és válassza ki a szükséges gépet.
2. A **Függőségek oszlopban** kattintson az **Ügynökök telepítése gombra.**
3. A **Függőségek** lapon töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök minden virtuális gép felmérni kívánt.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Ezekre akkor van szüksége, amikor telepíti az MMA-t a helyszíni gépen.

> [!NOTE]
> Az ügynökök telepítésének automatizálásához használhatja a központi telepítési eszközt, például a Configuration Managert vagy egy olyan partnereszközt, mint [az Intigua,](https://www.intigua.com/getting-started-intigua-for-azure-migration)amely ügynök-telepítési megoldást biztosít az Azure Áttelepítéshez.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Az MMA-ügynök telepítése Windows-gépre

Az ügynök telepítése Windows rendszerű számítógépen:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** lapon kattintson az **Elfogadom** a licenc elfogadásához elemre.
3. A **Célmappában**tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > **Tovább**.
4. Az **Ügynök beállítási beállításai párbeszédpanelen**válassza az **Azure Log Analytics** > **Next lehetőséget.**
5. Új Log Analytics-munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be a munkaterület-azonosítót és a portálról másolt kulcsot. Kattintson a **Tovább** gombra.

Az ügynököt a parancssorból vagy egy automatikus módszerrel, például a Configuration Manager használatával telepítheti. [További információ](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) az MMA-ügynök telepítéséhez ezen módszerek használatáról.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Az MMA-ügynök telepítése Linux-gépre

Az ügynök linuxos gépen való telepítése:

1. A megfelelő csomag (x86 vagy x64) átvitele linuxos számítógépre az scp/sftp használatával.
2. Telepítse a köteget a --install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) az MMA Által támogatott Linux operációs rendszerek listájáról.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Az MMA-ügynök telepítése az Operations Manager által felügyelt számítógépre

A System Center Operations Manager 2012 R2 vagy újabb verziói által monitorozott gépek esetében nincs szükség az MMA-ügynök telepítésére. A Service Map integrálható az Operations Manager MMA-val a szükséges függőségi adatok összegyűjtéséhez. [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). A függőségi ügynököt telepíteni kell.

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows-gépre való telepítéséhez kattintson duplán a telepítőfájlra, és kövesse a varázslót.
2. A függőségi ügynök Linux-gépre történő telepítéséhez telepítse root-ként a következő paranccsal:

    ```sh InstallDependencyAgent-Linux64.bin```

- További információ a Windows és Linux operációs rendszerek [függőségi ügynökének támogatásáról.](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)
- [További információ](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) arról, hogyan használhatja a parancsfájlokat a függőségi ügynök telepítéséhez.

>[!NOTE]
> Az Azure Monitor szolgáltatás virtuális gépekre cikk hivatkozott, hogy áttekintést nyújtson a rendszer előfeltételeit és a függőségi ügynök üzembe helyezésének módszereit is alkalmazható a Service Map-megoldás.

### <a name="create-a-group-with-dependency-mapping"></a>Függőségi leképezéssel rendelkező csoport létrehozása

1. Az ügynökök telepítése után nyissa meg a portált, és kattintson a**Gépek** **kezelése** > gombra.
2. Keresse meg azt a gépet, amelyen az ügyintézőktelepítve volt.
3. A számítógép **Függőségek** oszlopának most **antól függőségek megtekintéseként kell megjelennie.** Kattintson az oszlopra a számítógép függőségeinek megtekintéséhez.
4. A gép függőségi térképe a következő részleteket jeleníti meg:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a számítógéphez/a számítógépről
        - Azok a függő gépek, amelyeken nincs telepítve az MMA és a függőségi ügynök, portszámok szerint vannak csoportosítva.
        - Az MMA-val és a függőségi ügynökkel telepített függő gépek külön mezőként jelennek meg.
    - Folyamatok fut a gépen belül, akkor bontsa ki az egyes gépdoboz megtekintéséhez a folyamatok
    - Megjelennek a számítógép tulajdonságai, beleértve a teljes tartománynnt, az operációs rendszert és a MAC-címet. A részletek megtekintéséhez kattintson az egyes gépdobozra.

4. A különböző időtartamok függőségeit az időtartomány címkéjében az időtartamra kattintva tekintheti meg. Alapértelmezés szerint a tartomány egy óra. Módosíthatja az időtartományt, vagy megadhatja a kezdési és befejezési dátumokat és az időtartamot.

   > [!NOTE]
   >    Legfeljebb egy órás időtartomány támogatott. Az Azure Monitor naplók használatával [a lekérdezési függőségi adatok](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamú lekérdezéséhez.

5. Miután azonosította azokat a függő gépeket, amelyeket csoportosítani szeretne, a Ctrl+Click billentyűkombinációval jelöljön ki több gépet a térképen, és kattintson a **Gépek csoportosítása**gombra.
6. Adja meg a csoport nevét. Ellenőrizze, hogy a függő gépek et az Azure Migrate fedezte fel.

    > [!NOTE]
    > Ha egy függő gép nem fedezte fel az Azure Migrate, nem tudja hozzáadni a csoporthoz. Ilyen gépek hozzáadása a csoporthoz, újra kell futtatnia a felderítési folyamatot a megfelelő hatókörrel a vCenter Server ben, és győződjön meg arról, hogy a gépet az Azure Migrate fedezte fel.  

7. Ha értékelést szeretne létrehozni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, ha új értékelést szeretne létrehozni a csoportszámára.
8. A csoport mentéséhez kattintson az **OK** gombra.

A csoport létrehozása után ajánlott ügynököket telepíteni a csoport összes gépére, és finomítani a csoportot a teljes csoport függőségének megjelenítésével.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Lekérdezési függőségi adatok az Azure Monitor naplóiból

A Service Map által rögzített függőségi adatok az Azure Migrate projekthez társított Log Analytics-munkaterületen érhetők el. [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) a Service Map adattáblák lekérdezéshez az Azure Monitor naplók. 

A Kusto-lekérdezések futtatása:

1. Az ügynökök telepítése után nyissa meg a portált, és kattintson **az Áttekintés gombra.**
2. Az **Áttekintés menüben**nyissa meg a projekt **Essentials** szakaszát, és kattintson az **OMS-munkaterület**melletti munkaterület nevére.
3. A Log Analytics munkaterület lapon kattintson az **Általános** > **naplók gombra.**
4. Írja meg a lekérdezést a függőségi adatok gyűjtéséhez az Azure Monitor naplóihasználatával. Mintalekérdezések keresése a következő szakaszban.
5. Futtassa a lekérdezést a Futtatás gombra kattintva. 

[További információ](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) a Kusto-lekérdezések írásáról. 

### <a name="sample-azure-monitor-logs-queries"></a>Minta Az Azure Monitor naplózza lekérdezések

A következőkben a függőségi adatok kinyeréséhez használható mintalekérdezések. A lekérdezések módosításával kinyerheti az előnyben részesített adatpontokat. A függőségi adatrekordok mezőinek teljes listája [itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)érhető el. További mintalekérdezéseket [itt](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)talál.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Bejövő kapcsolatok összegzése gépek készletén

A virtuális gépkapcsolati metrikák táblájában szereplő rekordok nem képviselnek egyéni fizikai hálózati kapcsolatokat. Több fizikai hálózati kapcsolat logikai kapcsolatba van csoportosítva. [További információ](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) arról, hogyan történik a fizikai hálózati kapcsolat adatai egyetlen logikai rekordba összesítése a VMConnection szolgáltatásban. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Gépek egy készlete közötti bejövő kapcsolatokon küldött és fogadott adatok mennyiségének összegzése

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>További lépések
[Ismerje meg](migrate-services-overview.md) az Azure Migrate legújabb verzióját.
