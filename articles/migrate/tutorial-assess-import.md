---
title: Kiszolgálók értékelése az importált kiszolgáló adataival Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan értékelheti a helyszíni kiszolgálókat az Azure-ba való áttelepítéshez Azure Migrate kiszolgáló értékelésével az importált adatértékek használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 86d61dbd75363f68ee4651bfb36f940810909c00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501847"
---
# <a name="assess-servers-using-imported-data"></a>Kiszolgálók felmérése importált adatszolgáltatásokkal

Ez a cikk bemutatja, hogyan értékelheti a helyszíni kiszolgálókat a [Azure Migrate: kiszolgáló-értékeléssel](migrate-services-overview.md#azure-migrate-server-assessment-tool), a kiszolgálói metaadatok CSV használatával történő importálásával. Ezzel az értékelési módszersel nem kell beállítania a Azure Migrate berendezést az értékelés létrehozásához. Ez a következő esetekben hasznos: 

- A berendezés üzembe helyezése előtt létre szeretne hozni egy gyors kezdeti értékelést.
- A Azure Migrate berendezés nem helyezhető üzembe a szervezetében.
- A helyszíni kiszolgálókhoz való hozzáférést engedélyező hitelesítő adatok nem oszthatók meg.
- A biztonsági korlátozások megakadályozzák a készülék által az Azure-ba gyűjtött adatok összegyűjtését és küldését. Az importált fájlokkal szabályozhatja a megosztott adatmennyiséget, és számos (például IP-címet biztosító) adattal rendelkezhet.


## <a name="before-you-start"></a>Előkészületek

Vegye figyelembe:

- Legfeljebb 20000 kiszolgálót adhat hozzá egyetlen CSV-fájlban.
- A CSV használatával akár 20000-es kiszolgálót is hozzáadhat egy Azure Migrate projekthez.
- A kiszolgálói adatok több alkalommal is feltölthetők a CSV használatával Azure Migrate kiszolgáló értékeléséhez.
- Bár az alkalmazásadatok összegyűjtése hasznos lehet a helyszíni környezet áttelepítésre való kiértékelése során, Azure Migrate Server Assessment jelenleg nem hajtja végre az alkalmazás szintű értékelést, és nem veszi figyelembe az alkalmazásokat, ha Értékelés létrehozása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Töltsön ki egy CSV-fájlt a kiszolgáló adataival.
> * Importálja a fájlt, és adja hozzá a kiszolgáló adatait Azure Migrate Server Assessment szolgáltatáshoz.
> * Értékelés létrehozása és áttekintése.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-engedélyek beállítása Azure Migratehoz 

Az Azure-fióknak engedélyre van szüksége Azure Migrate projekt létrehozásához.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Állítson be egy új Azure Migrate-projektet a következők szerint.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-import/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a földrajzi nevet, amelyben létre kívánja hozni a projektet.

    - [Tekintse át](migrate-support-matrix.md#supported-geographies) a támogatott földrajzi régiókat. A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-import/migrate-project.png)


7. Kattintson a **Tovább** gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment** > **Next (tovább**) lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-import/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.


## <a name="prepare-the-csv"></a>A CSV előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgáló adatait.


### <a name="download-the-template"></a>A sablon letöltése

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A **gépek felderítése**területen válassza az **Importálás a használatával lehetőséget. CSV**.
3. Kattintson a **Letöltés** gombra a letöltéséhez. CSV-sablon. Azt is megteheti, hogy [közvetlenül letölti](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Letöltés. CSV-sablon](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Kiszolgáló adatainak hozzáadása

Gyűjtse össze a kiszolgálói adatokat, és adja hozzá a CSV-fájlhoz.

- Az adatok összegyűjtéséhez exportálhatja azt a helyszíni kiszolgálók felügyeletéhez használt eszközökről, például VMware vSphere vagy a Configuration Management-adatbázishoz (CMDB).
- A mintaadatok áttekintéséhez töltse le a [példánkban szereplő fájlt](https://go.microsoft.com/fwlink/?linkid=2108405).


A következő táblázat összefoglalja a kitöltendő fájl mezőket.

**Mező neve** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgálónév** | Igen | A teljes tartománynév megadását javasoljuk. 
**IP-cím** | Nem | Kiszolgáló címe.
**Magok száma** | Igen | A kiszolgálóhoz lefoglalt processzor-magok száma.
**Memória** | Igen | A kiszolgálóhoz lefoglalt összes memória (MB).
**Operációs rendszer neve** | Igen | Kiszolgálói operációs rendszer.
**Operációs rendszer verziója** | Nem | Kiszolgáló operációs rendszerének verziója.
**Lemezek száma** | Nem | Nem szükséges, ha az egyes lemezek adatait megadja.
**1. lemez mérete**  | Nem | A lemez maximális mérete (GB)<br/> A sablon [oszlopainak hozzáadásával](#add-multiple-disks) további lemezeket is hozzáadhat. Legfeljebb nyolc lemezt adhat hozzá.
**1. lemez olvasási Ops** | Nem | Lemez olvasási műveletei másodpercenként.
**1. lemez írási Ops** | Nem | Lemez írási műveletei másodpercenként.
**1. lemez olvasási sebesség** | Nem | A lemezről másodpercenként beolvasott adatok másodpercenkénti száma (MB/s).
**1. lemez írási átviteli sebessége** | Nem | A másodpercenként lemezre írt adatmennyiség MB-onként.
**CPU-kihasználtság százalékos aránya** | Nem | A CPU kihasználtsága százalékban kifejezve.
**Memória kihasználtsága (%)** | Nem | A RAM kihasználtsága százalékban kifejezve.
**Összes lemez olvasási művelete** | Nem | Lemez olvasási műveletei másodpercenként.
**Összes lemez írási Ops** | Nem | Lemez írási műveletei másodpercenként.
**Összes lemez olvasási sebessége** | Nem | A lemezről másodpercenként beolvasott adatok száma (MB).
**Összes lemez írási sebessége** | Nem | MB/s-ban lemezre írt adatmennyiség.
**bejövő hálózati forgalom átviteli sebesség** | Nem | A kiszolgáló által a másodpercenként fogadott adatmennyiség.
**kimenő hálózati forgalom átviteli sebesség** | Nem | A kiszolgáló által továbbított adatok MEGABÁJT/másodpercben.
**Belső vezérlőprogram típusa** | Nem | Kiszolgáló belső vezérlőprogram. Az értékek "BIOS" vagy "UEFI" lehet.
**Kiszolgáló típusa** | Nem | Az értékek "fizikai" vagy "virtuális" lehet.
**Hypervisor** | Nem | A számítógépeket futtató hypervisorok. <br/> Az értékek lehetnek a "VMware", a "Hyper-V", a "Xen", az "AWS", a "GCP" vagy a "other".
**Hypervisor verziószáma** | Nem | Hypervisor verziója.
**Virtuális gép azonosítója** | Nem | VM-azonosító. Ez a VMware vCenter VM vagy a Hyper-V **Hyper-v virtuálisgép-azonosítójának** **értékű** .
**Virtual Machine Manager-azonosító** | Nem | Ez a VMWare vCenter **értékű** . Nem szükséges a Hyper-V-hez.
**MAC-címe**| Nem | Kiszolgáló MAC-címe.
**BIOS-AZONOSÍTÓ** | Nem | Kiszolgáló BIOS-azonosítója.
**Egyéni kiszolgáló azonosítója**| Nem | Helyi egyedi kiszolgálói azonosítók a helyszínen. <br/> Hasznos az importált kiszolgáló helyi AZONOSÍTÓval való nyomon követéséhez. 
**1. alkalmazás neve** | Nem | A kiszolgálón futó munkaterhelések neve.<br/> További alkalmazásokhoz további adatokat is hozzáadhat, ha [oszlopokat ad](#add-multiple-applications) hozzá a sablonhoz. Legfeljebb öt alkalmazást adhat hozzá.
**1. alkalmazás típusa** | Nem | A kiszolgálón futó munkaterhelés típusa
**1. alkalmazás verziója** | Nem | A kiszolgálón futó munkaterhelés verziója.
**1. alkalmazás licenc lejárata** | Nem | A munkaterheléshez tartozó licenc lejárata (ha van ilyen).
**Üzleti egység** | Nem | A kiszolgálóhoz tartozó üzleti egység.
**Üzleti tulajdonos** | Nem | Üzleti egység tulajdonosa.
**Üzleti alkalmazás neve** | Nem | Annak az alkalmazásnak a neve, amelyhez az alkalmazás tartozik.
**Hely** | Nem | Az adatközpont, amelyben a kiszolgáló található.
**Kiszolgáló leszerelésének dátuma** | Nem | A fizikai kiszolgáló vagy a virtuális kiszolgáló mögöttes fizikai kiszolgáló leszerelési dátuma

### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

Az értékelés felismeri az operációs rendszerek bizonyos neveit. A megadott operációs rendszer nevének meg kell egyeznie a [támogatott nevek](#supported-operating-system-names) listán szereplő egyik lehetőséggel.


### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon alapértelmezett mezőket biztosít az első lemezhez.  Akár 8 lemezhez is hozzáadhat hasonló oszlopokat. 

Például egy második lemez összes mezőjének megadásához adja hozzá az oszlopokat:

2\. lemez, 2. lemez beolvasása, 2. írási műveleti lemez

Szükség esetén csak a lemezekhez adhat hozzá konkrét mezőket.


### <a name="add-multiple-applications"></a>Több alkalmazás hozzáadása

A sablon egyetlen alkalmazáshoz biztosít mezőket. Akár öt alkalmazáshoz is hozzáadhat hasonló oszlopokat.  

Például egy második alkalmazás összes mezőjének megadásához adja hozzá az oszlopokat:

Application 2 Name Application 2 Type Application 2 Version Application 2 licenc lejárata


Opcionálisan hozzáadhat konkrét mezőket csak az alkalmazásokhoz.

> [!NOTE]
> Az alkalmazásadatok a helyszíni környezet áttelepítésre való kiértékelése során hasznosak. Azure Migrate Server Assessment azonban jelenleg nem hajtja végre az alkalmazás szintű értékelést, és az értékelés létrehozásakor nem veszi figyelembe az alkalmazásokat.


## <a name="upload-the-server-information"></a>A kiszolgáló adatainak feltöltése

Miután hozzáadta az adatokat a CSV-sablonhoz, importálja a kiszolgálókat Azure Migrateba: Server Assessment.

1. A Azure Migrate > **gépek felderítése**területen keresse meg a kitöltött sablont.
2. Kattintson az **Importálás** gombra.
3. Megjelenik az importálás állapota. 
    - Ha figyelmeztetések jelennek meg az állapotában, megjavíthatja őket, vagy folytathatja a kezelés nélkül.
    - A kiszolgálói információk javítása a figyelmeztetések által javasolt módon javítja az értékelési pontosságot.
    - Ha meg szeretné tekinteni és elhárítani a figyelmeztetéseket, kattintson a **Figyelmeztetés részleteinek letöltése elemre. CSV**. Ezzel letölti a CSV-fájlt, és figyelmeztetéseket adott hozzá. Megtekintheti a figyelmeztetéseket, és szükség szerint kijavíthatja a problémákat. 
    Ha a hibák megjelennek az állapotban (az importálás állapota **sikertelen**), az importálás folytatása előtt ki kell javítania ezeket. Ehhez töltse le a CSV-fájlt, amely mostantól a hiba részleteivel bővült. Szükség szerint tekintse át és oldja meg a hibákat. Ezután töltse fel újra a módosított fájlt.
4. Az importálási állapot **befejezése után**a rendszer importálja a kiszolgáló adatait.


> [!NOTE]
> A Azure Migrate feltöltött kiszolgáló adatainak frissítéséhez töltse fel újra az adatokat a kiszolgáló számára ugyanazzal a **kiszolgálónévvel**. Vegye figyelembe, hogy a **kiszolgáló neve** mező a sablon importálása után nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="updating-server-information"></a>Kiszolgáló adatainak frissítése

A kiszolgáló adatainak frissítéséhez töltse fel újra a kiszolgáló adatait ugyanazzal a **kiszolgálónévvel**. A **kiszolgáló neve** mező nem módosítható. 

A kiszolgálók törlése jelenleg nem támogatott.

### <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a kiszolgálók megjelennek-e a Azure Portal.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.
3. Kattintson az **importálási alapú** fülre.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | A megadott teljesítmény-adatértékeken alapuló értékelések | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a megadott kiszolgáló mérete alapján<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.


### <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson az **értékelés**elemre.

    ![Értékelés](./media/tutorial-assess-physical/assess.png)

2. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
3. A **felderítési forrás**területen válassza a **Azure Migrate importálással hozzáadott gépek** lehetőséget.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-physical/view-all.png)

3. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt kiszolgálókat.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

6. Az értékelés létrehozása után megtekintheti azt a **kiszolgálók** > **Azure Migrate: kiszolgálói felmérés** > **értékelések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: az, hogy a kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a kiszolgálók Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  - **kiszolgálók**területen kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a kiszolgálók készen állnak-e az Azure-ba való áttelepítésre.
2. Tekintse át az állapotot:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

2. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a kiszolgáló készültségi adatait, és részletesen megtekintheti a kiszolgáló adatait, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes kiszolgáló számára a vizsgált csoportban történik.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni kiszolgálók IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.

> [!NOTE]
> A megbízhatósági minősítéseket nem rendeli hozzá a rendszer a CSV használatával Azure Migrate Server Assessment-be importált kiszolgálók értékeléséhez.


## <a name="supported-operating-system-names"></a>Támogatott operációs rendszerek nevei

Name (Név) | Name (Név)
--- | ---
**A – H** | 
Apple Mac OS X 10 | 3\. Asianux<br/>4\. Asianux<br/>5\. Asianux
CentOS<br/>CentOS 4/5 | CoreOS Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I-R** | 
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server küszöbértéke<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional
    

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Importált kiszolgálók Azure Migrate: a kiszolgáló értékelése CSV használatával.
> * Értékelés létrehozva és felülvizsgálva

Most [helyezzen üzembe egy készüléket](./migrate-appliance.md) a pontosabb értékelésekhez, és Gyűjtse össze a kiszolgálókat csoportokba a [függőségi elemzések](./concepts-dependency-visualization.md)használatával végzett mélyebb értékeléshez.
