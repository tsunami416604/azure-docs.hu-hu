---
title: Helyszíni kiszolgálók felderítése egy importált CSV-fájllal Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan lehet felderíteni a helyszíni kiszolgálókat az Azure-ba való áttelepítéshez egy importált CSV-fájllal Azure Migrate Server Assessment használatával
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 6526961df225e4f347216428141e8217043161df
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064318"
---
# <a name="tutorial-discover-servers-using-an-imported-csv-file"></a>Oktatóanyag: kiszolgálók felderítése importált CSV-fájl használatával

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket. 

Ebből az oktatóanyagból megtudhatja, hogyan derítheti fel a helyszíni VMware virtuális gépeket (VM-EK) a Azure Migrate: Server Assessment Tool használatával, egy CSV-fájl importálásával. 

Ha CSV-fájlt használ, nem kell beállítania a Azure Migrate berendezést a kiszolgálók felderítéséhez. Megadhatja a fájlban megosztott adatmennyiséget, és az adatmennyiség nagy része nem kötelező. Ez a módszer a következő esetekben hasznos:

- A berendezés üzembe helyezése előtt létre szeretne hozni egy gyors, kezdeti értékelést.
- A Azure Migrate berendezés nem helyezhető üzembe a szervezetében.
- A helyszíni kiszolgálókhoz való hozzáférést engedélyező hitelesítő adatok nem oszthatók meg.
- A biztonsági korlátozások megakadályozzák a készülék által az Azure-ba gyűjtött adatok összegyűjtését és küldését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure-fiók beállítása
> * Azure Migrate projekt beállítása.
> * CSV-fájl előkészítése
> * Importálja a fájlt.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges. 

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

- Legfeljebb 20 000 kiszolgálót adhat hozzá egyetlen CSV-fájlban és egy Azure Migrate-projektben. 
- A CSV-fájlban megadott operációsrendszer-neveknek tartalmaznia kell és meg kell egyezniük a [támogatott nevekkel](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához és a Azure Migrate berendezés regisztrálásához a következő fiókra van szüksége:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások**területen válassza az **előfizetések**lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-import/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési**területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása**párbeszédpanelen kattintson a **Hozzáadás**gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-import/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása**lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-import/assign-role.png)

7. A portálon keressen felhasználókat, és a **szolgáltatások**területen válassza a **felhasználók**lehetőséget.
8. A **felhasználói beállítások**területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Projekt beállítása

Hozzon létre egy új Azure Migrate projektet.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen válassza a **projekt létrehozása**lehetőséget.
5. A **projekt létrehozása**lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-import/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>A CSV előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgáló adatait.

### <a name="download-the-template"></a>A sablon letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **számítógépek felderítése**lapon válassza az **Importálás CSV használatával**lehetőséget.
3. A CSV-sablon letöltéséhez kattintson a **Letöltés** gombra. Azt is megteheti, hogy [közvetlenül letölti](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![CSV-sablon letöltése](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Kiszolgáló adatainak hozzáadása

Gyűjtsön kiszolgáló adatokat, és adja hozzá a CSV-fájlhoz.

- Az adatok összegyűjtéséhez exportálhatja azt a helyszíni kiszolgálók felügyeletéhez használt eszközökről, például VMware vSphere vagy a konfigurációs felügyeleti adatbázishoz (CMDB).
- A mintaadatok áttekintéséhez töltse le a [példánkban szereplő fájlt](https://go.microsoft.com/fwlink/?linkid=2108405).

A következő táblázat összefoglalja a kitöltendő fájl mezőket:

**Mezőnév** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgáló neve** | Yes | Javasoljuk, hogy adja meg a teljes tartománynevet (FQDN).
**IP-cím** | No | Kiszolgáló címe.
**Cores** | Yes | A kiszolgáló számára lefoglalt processzor-magok száma.
**Memória** | Yes | A kiszolgálóhoz lefoglalt összes memória (MB).
**Operációs rendszer neve** | Yes | Kiszolgálói operációs rendszer. <br/> Az értékelés felismeri azokat az operációs rendszerek neveit, amelyek megfelelnek vagy tartalmazzák [a listában szereplő](#supported-operating-system-names) neveket.
**Operációs rendszer verziója** | No | Kiszolgáló operációs rendszerének verziója.
**Operációs rendszer architektúrája** | No | Kiszolgálói operációs rendszer architektúrája <br/> Az érvényes értékek: x64, x86, amd64, 32 bites vagy 64 bites
**Lemezek száma** | No | Nem szükséges, ha az egyes lemezek adatait megadja.
**1. lemez mérete**  | No | A lemez maximális mérete GB-ban.<br/>A sablon [oszlopainak hozzáadásával](#add-multiple-disks) további lemezeket is hozzáadhat. Legfeljebb nyolc lemezt adhat hozzá.
**1. lemez olvasási Ops** | No | Lemez olvasási műveletei másodpercenként.
**1. lemez írási Ops** | No | Lemez írási műveletei másodpercenként.
**1. lemez olvasási sebesség** | No | A lemezről másodpercenként olvasott adatok másodpercenkénti száma (MB/s).
**1. lemez írási átviteli sebessége** | No | A másodpercenként lemezre írt adatmennyiség MB-onként.
**CPU-kihasználtság százalékos aránya** | No | A felhasznált CPU százalékos aránya.
**Memória kihasználtsága (%)** | No | A felhasznált memória százalékos aránya.
**Összes lemez olvasási művelete** | No | Lemezes olvasási műveletek másodpercenként.
**Összes lemez írási Ops** | No | Lemezes írási műveletek másodpercenként.
**Összes lemez olvasási sebessége** | No | A lemezről beolvasott adatok (MB/s).
**Összes lemez írási sebessége** | No | Lemezre írt, másodpercenként MB-ban tárolt adatmennyiség.
**Átviteli sebességű hálózat** | No | A kiszolgáló által fogadott, MEGABÁJT/másodpercben tárolt adatmennyiség.
**Hálózati kimenő átviteli sebesség** | No | A kiszolgáló által továbbított adatok (MB/s).
**Belső vezérlőprogram típusa** | No | Kiszolgáló belső vezérlőprogram. Az értékek "BIOS" vagy "UEFI" lehet.
**MAC-cím**| No | Kiszolgáló MAC-címe.


### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

Az értékelés felismeri az operációs rendszerek bizonyos neveit. A megadott névnek pontosan egyeznie kell a [támogatott nevek listában](#supported-operating-system-names)szereplő karakterláncok egyikével.

### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon alapértelmezett mezőket biztosít az első lemezhez. Akár nyolc lemezhez is hozzáadhat hasonló oszlopokat.

Ha például egy második lemez összes mezőjét meg szeretné adni, adja hozzá a következő oszlopokat:

- 2. lemez mérete
- 2. lemez olvasási Ops
- 2. lemez írási Ops
- 2. lemez olvasási sebesség
- 2. lemez írási átviteli sebessége


## <a name="import-the-server-information"></a>A kiszolgáló adatainak importálása

Miután hozzáadta az adatokat a CSV-sablonhoz, importálja a kiszolgálókat az értékelésbe.

1. Azure Migrate a **gépek felderítése**területen lépjen a befejezett sablonra.
2. Válassza az **Importálás** lehetőséget.
3. Megjelenik az importálás állapota.
    - Ha figyelmeztetések jelennek meg az állapotában, megjavíthatja őket, vagy folytathatja a kezelés nélkül.
    - Az értékelés pontosságának javítása érdekében a figyelmeztetésekben javasolt módon javítsa a kiszolgáló adatait.
    - A figyelmeztetések megtekintéséhez és kijavításához válassza a **Figyelmeztetés részleteinek letöltése lehetőséget. CSV**. Ez a művelet letölti a CSV-fájlt, amely tartalmazza a figyelmeztetéseket. Szükség szerint tekintse át a figyelmeztetéseket és javítsa ki a problémákat.
    - Ha a hibák úgy jelennek meg az állapotban, hogy az importálási állapot **meghiúsuljon**, akkor az importálás folytatása előtt ki kell javítania ezeket a hibákat:
        1. Töltse le a CSV-fájlt, amely mostantól tartalmazza a hiba részleteit.
        1. Szükség szerint tekintse át és oldja meg a hibákat. 
        1. Töltse fel újra a módosított fájlt.
4. Az importálási állapot **befejezése után**a kiszolgáló adatai importálása megtörtént.

## <a name="update-server-information"></a>Kiszolgáló adatainak frissítése

A kiszolgáló adatainak frissítéséhez importálja újra a kiszolgáló adatait ugyanazzal a **kiszolgálónévvel**. A **kiszolgáló neve** mező nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók megjelennek-e a Azure Portal a felderítés után:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon válassza ki azt az ikont, amely a **felderített kiszolgálók**darabszámát jeleníti meg.
3. Válassza az **importálási alapú** lapot.



## <a name="supported-operating-system-names"></a>Támogatott operációs rendszerek nevei

A CSV-fájlban megadott operációs rendszer nevének tartalmaznia kell és egyeznie kell. Ha nem, akkor nem fogja tudni értékelni őket. 

**A – H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>3. Asianux<br/>4. Asianux<br/>5. Asianux<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server küszöbértéke<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Létrehozott egy Azure Migrate projektet 
> * Egy importált CSV-fájlt használó felderített kiszolgálók. Most futtasson egy értékelést a [VMWare virtuális gép Azure-beli virtuális gépekre való áttelepítéséről](tutorial-assess-vmware.md).