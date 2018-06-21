---
title: Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz az Azure Migrate segítségével | Microsoft Docs
description: Ismerteti, hogyan derítheti fel és értékelheti ki a helyszíni VMware virtuális gépeket az Azure-ba való migráláshoz az Azure Migrate szolgáltatással.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 71d4bc0aa1ea2658c4cd40834a769eaaac649bc3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228373"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz

Az [Azure Migrate](migrate-overview.md) szolgáltatás a helyszíni számítási feladatokat értékeli ki az Azure-ra történő migráláshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Olyan fiók létrehozása, amellyel az Azure Migrate helyszíni virtuális gépeket derít fel.
> * Azure Migrate-projekt létrehozása.
> * Helyszíni gyűjtő virtuális gép (VM) beállítása a helyszíni VMware virtuális gépek felderítéséhez kiértékelésre.
> * Virtuális gépek csoportosítása és értékelés készítése.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A migrálni kívánt virtuális gépeket egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú vCenter Servernek kell felügyelnie. Emellett egy 5.0-s vagy újabb verziójú ESXi gazdagépre is szükség van a gyűjtő virtuális gép üzembe helyezéséhez.

> [!NOTE]
> A Hyper-V támogatása tervbe van véve, és hamarosan megvalósul.

- **vCenter Server-fiók**: A vCenter Server eléréséhez egy csak olvasási jogokat biztosító fiók szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Serveren megfelelő jogosultságra van szüksége ahhoz, hogy a virtuális gépeket .OVA-formátumú fájlok importálásával létrehozhassa.
- **Statisztikai beállítások**: A vCenter Server statisztikai beállításait a 3. szintre kell konfigurálni a telepítés megkezdése előtt. A 3. szintnél alacsonyabb konfiguráció esetén a kiértékelés működni fog, de a tároló és a hálózat adatai nem lesznek gyűjtve. A méretezési javaslatok alapjául ebben az esetben a processzor és a memória teljesítményadatai, valamint a lemezek és a hálózati adapterek konfigurációs adatai szolgálnak majd.

## <a name="create-an-account-for-vm-discovery"></a>Fiók létrehozása virtuális gépek felderítéséhez

Az Azure Migrate hozzá kell férnie a VMware-kiszolgálókhoz a virtuális gépek felderítéséhez kiértékelés céljából. Hozzon létre egy VMware-fiókot az alábbi tulajdonságokkal. Ezt a fiókot az Azure Migrate beállítása során határozza meg.

- Felhasználó típusa: Legalább egy, csak olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a Gyermekobjektumba propagálás objektummal rendelkező Nincs hozzáférés szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-project"></a>Projekt létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate** elemet a keresési eredmények közül. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, majd a projekthez tartozó Azure-előfizetést.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyet a projekt létrehozásához, majd kattintson a **Létrehozás** gombra. Azure Migrate-projektet csak az USA középnyugati régiójában és keleti régiójában lehet létrehozni. Azonban továbbra is bármely Azure-beli célhelyre tervezheti a migrálást. A projekthez megadott hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához egy .OVA-fájlt kell letöltenie, majd importálnia a helyszíni vCenter-kiszolgálóra a virtuális gép létrehozásához.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **Gépek felderítése** területen kattintson a **Letöltés** gombra az .OVA-fájl letöltéséhez.
3. A **Projekt hitelesítő adatainak másolása** területen másolja ki a projekt azonosítóját és kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

    ![Az .ova-fájl letöltése](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A telepítése előtt ellenőrizze, hogy az .OVA-fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal.

  Az OVA 1.0.9.8-as verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Az OVA 1.0.9.7-es verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Az OVA 1.0.9.5-ös verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Az OVA 1.0.9.2-es verziója esetén

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

Importálja a letöltött fájlt a vCenter Serverre.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.

    ![Az OVF telepítése](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. Az OVF-sablon telepítése varázsló **Source** (Forrás) lapján adja meg az .ova-fájl helyét.
3. A **Name** (Név) és a **Location** (Hely) mezőben adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint az azt futtató leltárobjektumnak.
5. A **Host/Cluster** (Gazdagép/fürt) mezőben adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő virtuális gép futni fog.
7. A tárolóban adja meg a célhelyet a gyűjtő virtuális gép tárolásához.
8. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
9. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózatnak internetkapcsolattal kell rendelkeznie a metaadatok az Azure-ba küldéséhez.
10. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3. Az asztalon kattintson a **Gyűjtő futtatása** parancsikonra.
4. Kattintson a **Frissítések keresése** lehetőségre a gyűjtő felhasználói felületének felső sávján, és ellenőrizze, hogy a gyűjtő legújabb verzióban fut-e. Ha nem, a hivatkozás használatával töltse le a legújabb frissítési csomagot, és frissítse a gyűjtőt.
5. Az Azure Migrate Collectorban nyissa meg a **Set up prerequisites** (Előfeltételek megadása) felületet.
    - Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
    - Ha a virtuális gép proxykiszolgálón keresztül éri el az internetet, kattintson a **Proxy settings** (Proxybeállítások) gombra, és adja meg a proxykiszolgáló címét és a figyelőportot. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel. [Itt tekinthet meg további információkat](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#internet-connectivity) az internetes csatlakozási követelményekkel és a gyűjtő által elért URL-címek listájával kapcsolatban.

    > [!NOTE]
    > A proxykiszolgáló címét http://ProxyIPAddress vagy http://ProxyFQDN formátumban kell megadni. Csak a HTTP-proxyk használata támogatott.

    - A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.
    - Töltse le és telepítse a VMware PowerCLI-t.

6. A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a vCenter-kiszolgáló nevét (FQDN, teljes tartománynév) vagy IP-címét.
    - A **User name** (Felhasználónév) és a **Password** (Jelszó) mezőben adja meg a csak olvasási jogokkal rendelkező fiók hitelesítő adatait, amelyet a gyűjtő a virtuális gépek felderítéséhez használ majd a vCenter-kiszolgálón.
    - A **Collection scope** (Gyűjtés hatóköre) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1500-nál több virtuális gépet. [Itt tekinthet meg további információkat](how-to-scale-assessment.md) azzal kapcsolatban, hogyan fedezheti fel a nagyméretű környezeteket.

7. A **Specify migration project** (Migrálási projekt megadása) területen adja meg az Azure Migrate projekt a portálról kimásolt azonosítóját és kulcsát. Ha nem másolta ki őket, nyissa meg az Azure Portalt a gyűjtő virtuális gépről. A projekt **Áttekintés** lapján kattintson a **Gépek felderítése** elemre, és másolja ki az értékeket.  
8. A **View collection progress** (Gyűjtési folyamat megtekintése) területen monitorozhatja a felderítési folyamatot, és győződhet meg róla, hogy a virtuális gépekről gyűjtött metaadatok a hatókörbe tartoznak-e. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát. [Itt tekinthet meg további információkat](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#what-data-is-collected) az Azure Migrate-gyűjtő által gyűjtött adatokról.

> [!NOTE]
> Az adatgyűjtő csak az „Angol (Egyesült Államok)” nyelvet támogatja az operációs rendszer és az adatgyűjtő felület nyelveként. A támogatás további nyelveken is hamarosan elérhetővé válik.


### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítési idő a felderített virtuális gépek számától függ. 100 virtuális gép esetében tipikusan egy órát vesz igénybe a felderítés lefutása a gyűjtő futtatását követően.

1. A Migration Planner-projektben kattintson a **Manage** > **Machines** (Felügyelet > Gépek) elemre.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="create-and-view-an-assessment"></a>Értékelés készítése és megtekintése

A virtuális gépeket a felderítésüket követően csoportosíthatja, és értékelést hozhat létre.

1. A projekt **Áttekintés** lapján kattintson az **+Értékelés létrehozása** elemre.
2. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.
3. Hozza létre a csoportot, és adja meg a nevét.
4. Válassza ki a csoporthoz hozzáadni kívánt gépeket.
5. A csoport és az értékelés létrehozásához kattintson az **Értékelés létrehozása** elemre.
6. Az értékelést a létrehozását követően az **Áttekintés** > **Irányítópult** menüpontban tekintheti meg.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.

### <a name="assessment-details"></a>Értékelés részletei

Az értékelések információt nyújtanak arról, hogy a helyszíni virtuális gépek kompatibilisek-e az Azure-ral, hogy mi lenne a megfelelő virtuálisgép-méret a virtuális gép Azure-ban történő futtatásához, valamint a becsült havi Azure-költségekről.

![Értékelési jelentés](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Az Azure használatához szükséges állapot

Az értékelésben az Azure használatához szükséges állapotnézet az egyes virtuális gépek készenléti állapotát jeleníti meg. A virtuális gép tulajdonságaitól függően az egyes virtuális gépek a következőképpen jelölhetők meg:
- Készen áll az Azure-beli használatra
- Feltételesen készen áll az Azure-beli használatra
- Nem áll készen az Azure-beli használatra
- A kompatibilitás ismeretlen

A kész virtuális gépekhez az Azure Migrate javasol egy virtuálisgép-méretet az Azure-ban. Az Azure Migrate által tett méretjavaslat az értékelési tulajdonságokban meghatározott méretezési feltételektől függ. Ha a méretezési feltétel teljesítményalapú méretezés, a méretre vonatkozó javaslat a virtuális gépek (processzor és memória) és lemezek (IOPS-érték és adatátvitel) teljesítményelőzményeit veszi figyelembe. Ha a méretezési feltétel „helyszíni”, az Azure Migrate nem veszi figyelembe a virtuális gépek és lemezek teljesítményadatait. Az Azure-beli virtuális gépre vonatkozó méretezési javaslat a helyszíni virtuális gép méretét veszi figyelembe, a lemez méretezése pedig az értékelési tulajdonságok között megadott tárolótípus (alapértelmezés szerint prémium szintű lemez) alapján történik. [További információ](concepts-assessment-calculation.md) az Azure Migrate-beli méretezés módjáról.

Az Azure-hoz nem készen álló vagy feltételesen készen álló virtuális gépek esetén az Azure Migrate ismerteti a készenléttel kapcsolatos problémákat, és javítási lépésekre tesz javaslatot.

Azok a virtuális gépek, amelyekhez az Azure Migrate nem tudja meghatározni az Azure-beli készenléti állapotot (az adatok rendelkezésre állásának hiánya miatt), ismeretlen kompatibilitási állapotúként vannak megjelölve.

Az Azure-beli készenléti állapoton és a méretezésen kívül az Azure Migrate olyan eszközöket is javasol, amelyeket a virtuális gép migrálásához használhat. Ehhez a helyszíni környezet mélyebb felderítésére van szükség. [Itt tekinthet meg további információt](how-to-get-migration-tool.md) azzal kapcsolatban, hogy hogyan hajthat végre mélyebb felderítés ügynököknek a helyszíni gépekre való telepítésével. Ha az ügynökök nem lettek telepítve a helyszíni gépeken, átemeléses migrálás ajánlott az [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) használatával. Ha az ügynökök telepítve lettek a helyszíni gépre, az Azure Migrate megvizsgálja a gépen futó folyamatokat, és meghatározza, hogy a gép adatbázisgép-e vagy sem. Ha a gép adatbázisgép, a rendszer az [Azure Database Migration Service-t](https://docs.microsoft.com/azure/dms/dms-overview) javasolja migrációs eszközként, egyéb esetben pedig az Azure Site Recoveryt.

  ![Készenléti állapot értékelése](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Havi költségbecslés

Ez a nézet a virtuális gépek az Azure-ban való futtatásával kapcsolatos összes számítási és tárolási költséget mutatja az egyes gépek adataival együtt. A költségbecslések az Azure Migrate által a gépre és annak lemezeire vonatkozóan adott méretajánlásokon, valamint az értékelési tulajdonságokon alapulnak.

> [!NOTE]
> Az Azure Migrate által adott költségbecslések a helyszíni virtuális gépek az Azure szolgáltatott infrastruktúrában (IaaS) üzemelő virtuális gépekként való futtatására vonatkoznak. Az Azure Migrate a szolgáltatásként nyújtott platformokra (PaaS) vagy a szolgáltatott szoftverekre (SaaS) vonatkozó költségeket nem vesz figyelembe.

A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.

![Értékelés – VM-költségek](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Megbízhatósági minősítés

Az Azure Migrate minden értékelése olyan megbízhatósági minősítéssel van társítva, amely 1 csillagtól az 5 csillagig terjed (az 1 csillag a legalacsonyabb, az 5 csillag pedig a legmagasabb). A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve. Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.

A megbízhatósági minősítés különösen olyan értékelések esetében hasznos, ahol a méretezési feltétel a teljesítményalapú méretezés. A teljesítményalapú méretezéshez az Azure Migrate-nek szüksége van a virtuális gép processzorának és memóriájának kihasználtsági adataira. Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan. Ugyanígy az Azure Migrate-nek a virtuális géphez csatlakoztatott összes hálózati adapterre vonatkozóan szüksége van a bejövő és kimenő hálózati forgalom adataira a teljesítményalapú méretezés elvégzéséhez. Ha a fenti kihasználtsági számok valamelyike nem érhető el a vCenter Serveren, lehet, hogy az Azure Migrate által adott méretjavaslat nem megbízható. Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

Az értékelésekben a következő okok miatt nem lehet elérhető az összes adatpont:
- A vCenter Server statisztikai beállítása nem a 3. szintre van állítva. Ha a vCenter Server statisztikai beállítása a 3. szintnél alacsonyabb, akkor a lemez és a hálózat teljesítményadatai nem lesznek begyűjtve a vCenter Serverről. Ebben az esetben az Azure Migrate által a lemezhez és a hálózathoz nyújtott javaslat nem a kihasználtságon alapul. A lemez IOPS-értékének/adatátviteli teljesítményének figyelembe vétele nélkül az Azure Migrate nem tudja meghatározni, hogy a lemez prémium szintű lemezt igényel-e az Azure-ban, ezért minden esetben standard lemezeket javasol az összes lemezhez.
- A vCenter Server statisztikai beállítása rövidebb időre a 3. szintre lett állítva a felderítés megkezdése előtt. Vegyünk például egy olyan forgatókönyvet, ahol ma 3. szintre módosítja a statisztikai beállítást, és holnap elindítja a felderítést a gyűjtőberendezéssel (24 óra eltelte után). Ha egy nap értékelését hozza létre, az összes adatponttal rendelkezik, az értékelés megbízhatósági minősítése pedig 5 csillagos lesz. Ha azonban egy hónapra változtatja az értékelésben a teljesítmény időtartamát, a megbízhatósági minősítés csökken, mert nem lennének elérhetők az utolsó egy hónap lemezzel és hálózati teljesítménnyel kapcsolatos adatai. Ha az utolsó egy hónap teljesítményadatait szeretné figyelembe venni, a felderítés megkezdése előtt egy hónapig ajánlott a 3. szinten tartani a vCenter Server statisztikai beállítását.
- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha valamely virtuális gép egy ideig ki lett kapcsolva, a vCenter Server nem rendelkezik az adott időszak teljesítményadataival.
- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépeknek nincsenek teljesítményelőzményei a teljes időtartamhoz.

> [!NOTE]
> Ha valamely értékelés megbízhatósági minősítése 4 csillag alatt van, ajánlott a 3. szintre állítani a vCenter Server statisztikai beállításait, megvárni az értékeléshez használni kívánt időt (1 nap/1 hét/1 hónap), majd elvégezni a felderítést és az értékelést. Ha ez nem végezhető el, akkor lehet, hogy a teljesítményalapú méretezés nem megbízható, és azt javasoljuk, hogy váltson *helyszíni méretezésre* az értékelés tulajdonságainak módosításával.

## <a name="next-steps"></a>További lépések

- [Megismerheti](how-to-modify-assessment.md), hogyan szabhatja testre az értékeléseket az igényeinek megfelelően.
- Ismerje meg, hogyan hozhat létre megbízható értékelési csoportokat [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) használatával
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
- [Ismerje meg](how-to-scale-assessment.md), hogyan deríthetők fel és értékelhetők a nagyobb VMware-környezetek.
- [További információ](resources-faq.md) az Azure Migrate használatára vonatkozó gyakori kérdésekről
