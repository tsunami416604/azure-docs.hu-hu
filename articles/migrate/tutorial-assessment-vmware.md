---
title: Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz az Azure Migrate segítségével | Microsoft Docs
description: Ismerteti, hogyan derítheti fel és értékelheti ki a helyszíni VMware virtuális gépeket az Azure-ba való migráláshoz az Azure Migrate szolgáltatással.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 61eca35bb03ba62bbc10105819e12a8c0ebb41ca
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487907"
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

- **VMware**: A virtuális gépek áttelepítését tervezi verziójú vCenter Servernek egy 5.5-ös, 6.0-s vagy 6.5-ös kell felügyelnie. Emellett szükség egy ESXi gazdagépet verziójú 5.5-ös vagy újabb, a gyűjtő virtuális gép üzembe helyezéséhez.
- **vCenter Server-fiók**: A vCenter Server eléréséhez egy csak olvasható fiókra van szükség. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter-kiszolgáló, a virtuális gép létrehozása a fájl importálásával engedélyre van szükségük. OVA formátumot.

## <a name="create-an-account-for-vm-discovery"></a>Fiók létrehozása virtuális gépek felderítéséhez

Az Azure Migrate hozzá kell férnie a VMware-kiszolgálókhoz a virtuális gépek felderítéséhez kiértékelés céljából. Hozzon létre egy VMware-fiókot az alábbi tulajdonságokkal. Ezt a fiókot az Azure Migrate beállítása során határozza meg.

- Felhasználó típusa: Legalább egy csak olvasási jogosultsággal rendelkező felhasználó
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a Gyermekobjektumba propagálás objektummal rendelkező Nincs hozzáférés szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-project"></a>Projekt létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate** elemet a keresési eredmények közül. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, majd a projekthez tartozó Azure-előfizetést.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a földrajzi helyet a projekt létrehozásához, majd kattintson a **Létrehozás** gombra. Azure Migrate-projektet csak az alábbi földrajzi területeken hozhat létre. Azonban továbbra is bármely Azure-beli célhelyre tervezheti a migrálást. A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

**Régiócsoport** | **Tárolási hely**
--- | ---
Azure Government | USA-beli államigazgatás – Virginia
Ázsia | Délkelet-Ázsia
Európa | Észak-Európában és Nyugat-Európa
Több egység állapotok | USA keleti RÉGIÓJA és USA nyugati középső RÉGIÓJA

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához egy .OVA-fájlt kell letöltenie, majd importálnia a helyszíni vCenter-kiszolgálóra a virtuális gép létrehozásához.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **gépek felderítése**, kattintson a **letöltése** a berendezés letöltése.

    Az Azure Migrate készülék kommunikál a vCenter-kiszolgálóhoz, és folyamatosan profilt készít a valós idejű használati adatok gyűjtéséhez az egyes virtuális Gépekhez a helyszíni környezetben. Összegyűjti az csúcs számlálói mindegyik metrikát (CPU-használat, memóriahasználat stb.). Ez a modell a teljesítményadatok gyűjtése során nem függ a vCenter Server statisztikai beállításaitól. A berendezésben bármikor leállítható a folyamatos profilkészítés.

    > [!NOTE]
    > A felderítés egyszeri felderítés berendezés elavulttá vált, ez a módszer támaszkodtak a vCenter Server statisztikai beállításait teljesítmény adatok pont rendelkezésre állását és virtuális gépek áttelepítése az Azure-ba való korrigáljuk méretezésének eredményezett átlagos teljesítményszámlálók gyűjtése.

    **Gyors értékelést:** A folyamatos felderítési berendezéssel után a felderítés befejezéséhez (veszi fel néhány óra múlva, virtuális gépek számától függően), itt azonnal létrehozhat értékeléseket. A teljesítményadat-gyűjtés akkor kezdődik, amikor Ön indíthat felderítési, ha a gyors értékelések, mivel az értékelésben, mint a méretezési feltétel teljesítményalapú kiválasztani *helyszíni*. Teljesítmény-alapú értékelések javasolt a legalább egy napot várni megbízható méretezési javaslatokat kaphat a felderítés megkezdése után.

    A berendezés csak az folyamatosan teljesítményadatokat gyűjt, semmilyen konfigurálási változást nem észleli a helyszíni környezetben, (, virtuális gép hozzáadása, törlése, lemez hozzáadása stb.). Ha a helyszíni környezet konfigurációja módosul, a következőket teheti a változások tükrözésére a portálon:

    - További elemek (virtuális gépek, lemezek, magok stb.): A változásoknak az Azure Portalon, a felderítés a készülék leállítása és a majd indítsa el újra. Ez biztosítja, hogy a módosítások frissítése megtörténjen az Azure Migrate-projektben.

    - Virtuális gépek törlése: Lehet a célja, a készülék virtuális gépek törlése nem tükrözi, akkor is, ha leállítja és elindítja a felderítést. Ennek az oka, hogy a későbbi felderítések adatait a rendszer hozzáfűzi a korábbi felderítések adataihoz, nem pedig felülírja azokat. Ebben az esetben egyszerűen figyelmen kívül hagyhatja a virtuális gépet a portálon. Ehhez távolítsa el a csoportból, és számítsa újra az értékelést.


3. A **Projekt hitelesítő adatainak másolása** területen másolja ki a projekt azonosítóját és kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

    ![Az .ova-fájl letöltése](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A telepítése előtt ellenőrizze, hogy az .OVA-fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal.

#### <a name="continuous-discovery"></a>Folyamatos felderítés

  Az OVA verziója 1.0.10.11

  **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  Az OVA verziója 1.0.10.9

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  Az OVA 1.0.10.4-es verziója esetén

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Felderítés egyszeri felderítés (most már elavult)

Ez a modell már elavult, meglévő berendezések biztosítunk támogatást.

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
    - Válassza ki az Azure-felhő, amelyhez (az Azure globális vagy az Azure Government) áttelepítését tervezi.
    - Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
    - Ha a virtuális gép proxykiszolgálón keresztül éri el az internetet, kattintson a **Proxy settings** (Proxybeállítások) gombra, és adja meg a proxykiszolgáló címét és a figyelőportot. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel. [További](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) internet kapcsolat követelményeivel kapcsolatos és a [URL-címek listája](https://docs.microsoft.com/azure/migrate/concepts-collector#connect-to-urls) a gyűjtő fér hozzá.

      > [!NOTE]
      > A proxykiszolgáló címét http://ProxyIPAddress vagy http://ProxyFQDN formátumban kell megadni. Csak a HTTP-proxyk használata támogatott. Ha egy lehallgató proxy, az internetkapcsolat kezdetben meghiúsulhat, ha nem importálta a proxy-tanúsítvány [további](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity-with-intercepting-proxy) meg arról, hogyan oldhatja ezt egy megbízható tanúsítványt a gyűjtő virtuális Gépen, a proxy tanúsítvány importálásával.

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

A gyűjtőberendezés folyamatosan fog profilját a helyszíni környezetben, és fog tartani az adatküldés teljesítmény órás időközzel. Egy órával a felderítés megkezdése után a gépek a portálon tekintheti meg.

1. A migrálási projektben kattintson a **Kezelés** > **Gépek** elemre.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="create-and-view-an-assessment"></a>Értékelés készítése és megtekintése

Virtuális gépek a portálon a felderítésüket követően csoportosíthatja, és hozzon létre értékeléseket. Itt azonnal létrehozhat a helyszíni felmérését követően a virtuális gépeket a felderítésüket a portálon. Javasoljuk, hogy Várjon, amíg legalább egy napig bármely teljesítményalapú értékelések megbízható javaslatok létrehozása előtt.

1. A projekt **Áttekintés** lapján kattintson az **+Értékelés létrehozása** elemre.
2. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.
3. Hozza létre a csoportot, és adja meg a nevét.
4. Válassza ki a csoporthoz hozzáadni kívánt gépeket.
5. A csoport és az értékelés létrehozásához kattintson az **Értékelés létrehozása** elemre.
6. Az értékelést a létrehozását követően az **Áttekintés** > **Irányítópult** menüpontban tekintheti meg.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.

> [!NOTE]
> Erősen ajánlott, az értékelés létrehozása előtt felderítési, elindítása után legalább egy napot várnia. Ha szeretné egy meglévő értékelését a legújabb teljesítményadatokkal frissíteni, akkor ezt az értékelés **Újraszámítás** parancsával teheti meg.

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

Az Azure Migrate minden teljesítményalapú értékelése olyan megbízhatósági minősítéssel van társítva, amely 1 csillagtól az 5 csillagig terjed (az 1 csillag a legalacsonyabb, az 5 csillag pedig a legmagasabb). A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve. Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát. Megbízhatósági minősítés esetében nem érvényes "as-a" helyszíni értékelések.

A teljesítményalapú méretezéshez az Azure Migrate-nek szüksége van a virtuális gép processzorának és memóriájának kihasználtsági adataira. Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan. Ugyanígy az Azure Migrate-nek a virtuális géphez csatlakoztatott összes hálózati adapterre vonatkozóan szüksége van a bejövő és kimenő hálózati forgalom adataira a teljesítményalapú méretezés elvégzéséhez. Ha a fenti kihasználtsági számok valamelyike nem érhető el a vCenter Serveren, lehet, hogy az Azure Migrate által adott méretjavaslat nem megbízható. Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

Az értékelésekben a következő okok miatt nem lehet elérhető az összes adatpont:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például létrehoz egy teljesítményértékelést 1 napra állított időtartammal, akkor a felderítés indítását követően legalább egy napot várnia kell arra, hogy az összes adatpont be legyen gyűjtve.

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha valamelyik virtuális gép egy ideig ki volt kapcsolva, nem fogjuk tudni begyűjteni az adott időszak teljesítményadatait.

- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépeknek nincsenek teljesítményelőzményei a teljes időtartamhoz.

> [!NOTE]
> Ha valamely értékelés megbízhatósági minősítése 5 csillag alatt van, várja meg a berendezés, hogy a profil a környezet legalább egy napot, majd *újraszámítása* az értékelést. Ha ez nem végezhető el, akkor lehet, hogy a teljesítményalapú méretezés nem megbízható, és azt javasoljuk, hogy az értékelés tulajdonságainak módosításával váltson *helyszíni méretezésre*.

## <a name="next-steps"></a>További lépések

- [Megismerheti](how-to-modify-assessment.md), hogyan szabhatja testre az értékeléseket az igényeinek megfelelően.
- Ismerje meg, hogyan hozhat létre megbízható értékelési csoportokat [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) használatával
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
- [Ismerje meg](how-to-scale-assessment.md), hogyan deríthetők fel és értékelhetők a nagyobb VMware-környezetek.
- [További információ](resources-faq.md) az Azure Migrate használatára vonatkozó gyakori kérdésekről
