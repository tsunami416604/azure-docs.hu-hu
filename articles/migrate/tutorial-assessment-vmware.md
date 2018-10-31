---
title: Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz az Azure Migrate segítségével | Microsoft Docs
description: Ismerteti, hogyan derítheti fel és értékelheti ki a helyszíni VMware virtuális gépeket az Azure-ba való migráláshoz az Azure Migrate szolgáltatással.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f468bac6f4d8c209fae51f0b84980dc8c611a29b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025886"
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
- **vCenter Server-fiók**: A vCenter Server eléréséhez egy csak olvasási jogokat biztosító fiók szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Serveren megfelelő jogosultságra van szüksége ahhoz, hogy a virtuális gépeket .OVA-formátumú fájlok importálásával létrehozhassa.
- **Statisztikai beállítások**: Ez az előfeltétel csak az egyszeri felderítéses modellek esetén alkalmazható. Az egyszeri felderítés működéséhez a vCenter Server statisztikai beállításait az üzembe helyezés megkezdése előtt a 3. szintre kell állítani. A 3. szintnél alacsonyabb konfiguráció esetén a kiértékelés működni fog, de a tároló és a hálózat adatai nem lesznek gyűjtve. A méretezési javaslatok alapjául ebben az esetben a processzor és a memória teljesítményadatai, valamint a lemezek és a hálózati adapterek konfigurációs adatai szolgálnak majd.

## <a name="create-an-account-for-vm-discovery"></a>Fiók létrehozása virtuális gépek felderítéséhez

Az Azure Migrate hozzá kell férnie a VMware-kiszolgálókhoz a virtuális gépek felderítéséhez kiértékelés céljából. Hozzon létre egy VMware-fiókot az alábbi tulajdonságokkal. Ezt a fiókot az Azure Migrate beállítása során határozza meg.

- Felhasználó típusa: Legalább egy, csak olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a Gyermekobjektumba propagálás objektummal rendelkező Nincs hozzáférés szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-project"></a>Projekt létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate** elemet a keresési eredmények közül. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, majd a projekthez tartozó Azure-előfizetést.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a földrajzi helyet a projekt létrehozásához, majd kattintson a **Létrehozás** gombra. Azure Migrate-projektet csak egyesült államokbeli földrajzi helyen lehet létrehozni. Azonban továbbra is bármely Azure-beli célhelyre tervezheti a migrálást. A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához egy .OVA-fájlt kell letöltenie, majd importálnia a helyszíni vCenter-kiszolgálóra a virtuális gép létrehozásához.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **Gépek felderítése** területen kétféle berendezés közül választhat. Kattintson a **Letöltés** gombra az igényeinek megfelelő berendezés letöltéséhez.

    a. **Egyszeri felderítés:** Az ilyen modellű berendezés a vCenter Serverrel kommunikálva gyűjt metaadatokat a virtuális gépekről. A virtuális gépek teljesítményadatainak gyűjtése esetén a teljesítményadatoknak a vCenter Serveren tárolt előzményeit veszi figyelembe, és az előző hónap teljesítményelőzményeit gyűjti össze. Ebben a modellben az Azure Migrate a metrikák átlagértékeit (nem pedig a maximumértékeit) gyűjti. [További információ](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Ez egy egyszeri felderítés, így a felderítés befejeződését követően nem fognak megjelenni a helyszíni környezet változásai. Ha szeretné, hogy megjelenjenek ezek a változások, végre kell hajtania egy ismételt felderítést ugyanennek a projektnek ugyanezen környezetén.

    b. **Folyamatos felderítés:** Az ilyen modellű berendezés folyamatosan profilkészítést végez a helyszíni környezeten, így valós idejű használati adatokat gyűjt az egyes virtuális gépekről. Ez a modell a metrikák (processzorhasználat, memóriahasználat stb.) maximumszámlálóit gyűjti össze. Ez a modell a teljesítményadatok gyűjtése során nem függ a vCenter Server statisztikai beállításaitól. A berendezésben bármikor leállítható a folyamatos profilkészítés.

    Vegye figyelembe, hogy a berendezés a teljesítményadatok folyamatosan gyűjtését végzi, és nem észleli a konfiguráció változását a helyszíni környezetben (pl. virtuális gépek hozzáadását, törlését, lemezek hozzáadását stb.). Ha a helyszíni környezet konfigurációja módosul, a következőket teheti a változások tükrözésére a portálon:

    1. Elemek (virtuális gépek, lemezek, magok stb.) hozzáadása: Ezeknek a módosításoknak az Azure Portalon való tükrözéséhez állítsa le, majd indítsa újra a felderítést a berendezésen. Ez biztosítja, hogy a módosítások frissítése megtörténjen az Azure Migrate-projektben.

    2. Virtuális gépek törlése: A berendezés kialakítása miatt a virtuális gépek törlése akkor sem lesz látható, ha leállítja, majd újraindítja a felderítést. Ennek az oka, hogy a későbbi felderítések adatait a rendszer hozzáfűzi a korábbi felderítések adataihoz, nem pedig felülírja azokat. Ebben az esetben egyszerűen figyelmen kívül hagyhatja a virtuális gépet a portálon. Ehhez távolítsa el a csoportból, és számítsa újra az értékelést.

    > [!NOTE]
    > A folyamatos felderítés funkciója jelenleg előzetes verzióban érhető el. Javasoljuk, hogy így járjon el, mivel ez a módszer részletes teljesítményadatokat gyűjt, és pontosan számítja a megfelelő méretezést.

3. A **Projekt hitelesítő adatainak másolása** területen másolja ki a projekt azonosítóját és kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

    ![Az .ova-fájl letöltése](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A telepítése előtt ellenőrizze, hogy az .OVA-fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal.

#### <a name="one-time-discovery"></a>Egyszeri felderítés

  Az OVA 1.0.9.15-ös verziója esetén

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Az OVA 1.0.9.14-es verziója esetén

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Az OVA 1.0.9.12-es verziója esetén

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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

#### <a name="continuous-discovery"></a>Folyamatos felderítés

  Az OVA 1.0.10.4-es verziója esetén

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

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
    - Ha a virtuális gép proxykiszolgálón keresztül éri el az internetet, kattintson a **Proxy settings** (Proxybeállítások) gombra, és adja meg a proxykiszolgáló címét és a figyelőportot. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel. [Itt tekinthet meg további információkat](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity) az internetes csatlakozási követelményekkel és a gyűjtő által elért URL-címek listájával kapcsolatban.

    > [!NOTE]
    > A proxykiszolgáló címét http://ProxyIPAddress vagy http://ProxyFQDN formátumban kell megadni. Csak a HTTP-proxyk használata támogatott. Elfogó proxy használata esetén az internethez való csatlakozás kezdetben sikertelen lehet, ha nem importálta a proxytanúsítványt; [további információk](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity-with-intercepting-proxy) arról, hogyan oldhatja meg a problémát a proxytanúsítvány megbízható tanúsítványként való importálásával a gyűjtő virtuális gépen.

    - A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.
    - Töltse le és telepítse a VMware PowerCLI-t.

6. A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a vCenter-kiszolgáló nevét (FQDN, teljes tartománynév) vagy IP-címét.
    - A **User name** (Felhasználónév) és a **Password** (Jelszó) mezőben adja meg a csak olvasási jogokkal rendelkező fiók hitelesítő adatait, amelyet a gyűjtő a virtuális gépek felderítéséhez használ majd a vCenter-kiszolgálón.
    - A **Collection scope** (Gyűjtés hatóköre) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1500-nál több virtuális gépet. [Itt tekinthet meg további információkat](how-to-scale-assessment.md) azzal kapcsolatban, hogyan fedezheti fel a nagyméretű környezeteket.

7. A **Specify migration project** (Migrálási projekt megadása) területen adja meg az Azure Migrate projekt a portálról kimásolt azonosítóját és kulcsát. Ha nem másolta ki őket, nyissa meg az Azure Portalt a gyűjtő virtuális gépről. A projekt **Áttekintés** lapján kattintson a **Gépek felderítése** elemre, és másolja ki az értékeket.  
8. Az **Adatgyűjtési folyamat megtekintése** részen monitorozhatja a felderítés állapotát. [Itt tekinthet meg további információkat](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected) az Azure Migrate-gyűjtő által gyűjtött adatokról.

> [!NOTE]
> Az adatgyűjtő csak az „Angol (Egyesült Államok)” nyelvet támogatja az operációs rendszer és az adatgyűjtő felület nyelveként.
> Ha módosítja a beállításokat egy értékelni kívánt gépen, az értékelés futtatása előtt aktiválja újra az észlelést. Ehhez használja a gyűjtőben a **Gyűjtés újbóli elindítását**. Miután elkészült a gyűjtemény, válassza az **Újraszámítás** lehetőséget az értékeléshez a portálon, hogy megkapja a frissített értékelési eredményeket.



### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Egyszeri felderítés esetén a felderítés ideje a felderíteni kívánt virtuális gépek számától függ. 100 virtuális gép esetében a gyűjtő futtatását követően általában nagyjából egy órát vesz igénybe a konfigurációs adatok és a teljesítményadatok gyűjtésének befejezése. Az felderítés végeztével azonnal létrehozhat értékeléseket (teljesítményalapú és helyszíni értékeléseket egyaránt).

Folyamatos felderítés (előzetes verzió) esetén a gyűjtő folyamatos profilkészítést fog végezni a helyszíni környezeten, és a teljesítményadatokat folyamatosan, egyórás időközönként fogja küldeni. Egy órával a felderítés indítását követően tekintheti át a gépeket a portálon. Javasoljuk, hogy várjon legalább egy napot, és csak utána hozza létre a virtuális gépek teljesítményalapú értékeléseit.

1. A migrálási projektben kattintson a **Kezelés** > **Gépek** elemre.
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

> [!NOTE]
> Folyamatos felderítés esetén javasoljuk, hogy a felderítés elindítását követően várjon legalább egy napot, és csak utána hozzon létre értékeléseket. Ha szeretné egy meglévő értékelését a legújabb teljesítményadatokkal frissíteni, akkor ezt az értékelés **Újraszámítás** parancsával teheti meg.

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

Az Azure Migrate minden teljesítményalapú értékelése olyan megbízhatósági minősítéssel van társítva, amely 1 csillagtól az 5 csillagig terjed (az 1 csillag a legalacsonyabb, az 5 csillag pedig a legmagasabb). A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve. Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát. A megbízhatósági minősítés nem vonatkozik a helyszíni értékelésekre.

A teljesítményalapú méretezéshez az Azure Migrate-nek szüksége van a virtuális gép processzorának és memóriájának kihasználtsági adataira. Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan. Ugyanígy az Azure Migrate-nek a virtuális géphez csatlakoztatott összes hálózati adapterre vonatkozóan szüksége van a bejövő és kimenő hálózati forgalom adataira a teljesítményalapú méretezés elvégzéséhez. Ha a fenti kihasználtsági számok valamelyike nem érhető el a vCenter Serveren, lehet, hogy az Azure Migrate által adott méretjavaslat nem megbízható. Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

Az értékelésekben a következő okok miatt nem lehet elérhető az összes adatpont:

**Egyszeri felderítés**

- A vCenter Server statisztikai beállítása nem a 3. szintre van állítva. Az egyszeri felderítés függ a vCenter Server statisztikai beállításaitól, így ha ez a beállítás a 3. szintnél alacsonyabb, akkor a lemez és a hálózat teljesítményadatai nem lesznek begyűjtve a vCenter Serverről. Ebben az esetben az Azure Migrate által a lemezhez és a hálózathoz nyújtott javaslat nem a kihasználtságon alapul. A lemez IOPS-értékének/adatátviteli teljesítményének figyelembe vétele nélkül az Azure Migrate nem tudja meghatározni, hogy a lemez prémium szintű lemezt igényel-e az Azure-ban, ezért minden esetben standard lemezeket javasol az összes lemezhez.
- A vCenter Server statisztikai beállítása csak nem sokkal a felderítés megkezdése előtt lett a 3. szintre lett állítva. Vegyünk például egy olyan forgatókönyvet, ahol ma 3. szintre módosítja a statisztikai beállítást, és holnap elindítja a felderítést a gyűjtőberendezéssel (24 óra eltelte után). Ha egy nap értékelését hozza létre, az összes adatponttal rendelkezik, az értékelés megbízhatósági minősítése pedig 5 csillagos lesz. Ha azonban egy hónapra változtatja az értékelésben a teljesítmény időtartamát, a megbízhatósági minősítés csökken, mert nem lennének elérhetők az utolsó egy hónap lemezzel és hálózati teljesítménnyel kapcsolatos adatai. Ha az utolsó egy hónap teljesítményadatait szeretné figyelembe venni, a felderítés megkezdése előtt egy hónapig ajánlott a 3. szinten tartani a vCenter Server statisztikai beállítását.

**Folyamatos felderítés**

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például létrehoz egy teljesítményértékelést 1 napra állított időtartammal, akkor a felderítés indítását követően legalább egy napot várnia kell arra, hogy az összes adatpont be legyen gyűjtve.

**Gyakori okok**  

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha valamelyik virtuális gép egy ideig ki volt kapcsolva, nem fogjuk tudni begyűjteni az adott időszak teljesítményadatait.
- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépeknek nincsenek teljesítményelőzményei a teljes időtartamhoz.

> [!NOTE]
> Ha valamely értékelés megbízhatósági minősítése 4 csillag alatt van, egyszeri felderítéses modell esetén ajánlott a 3. szintre állítani a vCenter Server statisztikai beállításait, megvárni az értékeléshez használni kívánt időt (1 nap/1 hét/1 hónap), és ezután elvégezni a felderítést és az értékelést. Folyamatos felderítés esetén várjon legalább egy napot, hogy a berendezés elvégezhesse a profilkészítést környezeten, és ezután futtassa az értékelés *Újraszámítását*. Ha ez nem végezhető el, akkor lehet, hogy a teljesítményalapú méretezés nem megbízható, és azt javasoljuk, hogy az értékelés tulajdonságainak módosításával váltson *helyszíni méretezésre*.

## <a name="next-steps"></a>További lépések

- [Megismerheti](how-to-modify-assessment.md), hogyan szabhatja testre az értékeléseket az igényeinek megfelelően.
- Ismerje meg, hogyan hozhat létre megbízható értékelési csoportokat [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) használatával
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
- [Ismerje meg](how-to-scale-assessment.md), hogyan deríthetők fel és értékelhetők a nagyobb VMware-környezetek.
- [További információ](resources-faq.md) az Azure Migrate használatára vonatkozó gyakori kérdésekről
