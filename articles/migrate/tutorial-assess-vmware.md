---
title: A VMware virtuális gépek felmérése az Azure-ba való Migrálás Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan lehet kiértékelni a helyszíni VMware virtuális gépeket az Azure-ba való Migrálás Azure Migrate használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 7f161afe13bad8c548806d4b4ceb9372dc511cc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239321"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>VMware virtuális gépek felmérése Azure Migrate Server Assessment használatával

Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépeket (VM-EK) a Azure Migrate kiszolgáló-értékelési eszközének használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és a Microsoft partnereitől származó független szoftvergyártói (ISV) ajánlatokat tartalmaz.

Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket az Azure-ba. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Hozzon létre egy Azure Migrate berendezést, amely a virtuális gépek felmérésére a helyszínen fut.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését. A készülék konfigurációs és teljesítményadatokat küld a felderített virtuális gépeknek az Azure-ba.
> * Csoportosítsa a felderített virtuális gépeket, és mérje fel a virtuálisgép-csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Fejezze be az első oktatóanyagot](tutorial-prepare-vmware.md) ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.

Az első oktatóanyagban az alábbiakat kell elvégeznie:

- [Azure-engedélyek beállítása](tutorial-prepare-vmware.md#prepare-azure) Azure Migratehoz.
- A [VMware előkészítése](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) az értékeléshez:
   - [Ellenőrzés](migrate-support-matrix-vmware.md#vmware-requirements) VMware-beállítások.
   - Állítsa be az engedélyeket a VMware-ben, és hozzon létre egy virtuális gépet egy PETESEJT-sablonnal.
   - Hozzon létre egy [fiókot a virtuális gép felderítéséhez](migrate-support-matrix-vmware.md#vmware-requirements). 
   - Tegye elérhetővé a [szükséges portokat](migrate-support-matrix-vmware.md#port-access) .
   - Ügyeljen az Azure-hoz való hozzáféréshez [szükséges URL-címekre](migrate-replication-appliance.md#url-access) .

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Hozzon létre egy új Azure Migrate projektet a következőképpen:

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
1. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
1. Az **Áttekintés**területen a **kiszolgálók felderítése, felmérése és migrálása**szakaszban válassza a **kiszolgálók felmérése és áttelepíteni**lehetőséget.

   ![A kiszolgálók értékelésére és átmigrálára szolgáló gomb](./media/tutorial-assess-vmware/assess-migrate.png)

1. Az **első lépések**területen válassza az **eszközök hozzáadása**lehetőséget.
1. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
1. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Ázsia, Európa, Egyesült Királyság és Egyesült Államok támogatottak.

   A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. Migrálás futtatása során bármilyen célrégiót választhat.

   ![A projekt neve és a régió mezői](./media/tutorial-assess-vmware/migrate-project.png)

1. Kattintson a **Tovább** gombra.
1. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment** > **Next (tovább**) lehetőséget.

   ![A kiszolgáló-Assessment eszköz kiválasztása](./media/tutorial-assess-vmware/assessment-tool.png)

1. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
1. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd válassza az **eszközök hozzáadása**lehetőséget.
1. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="set-up-the-appliance-vm"></a>A berendezés virtuális gép beállítása

Azure Migrate Server Assessment egy egyszerűsített VMware VM-készüléket futtat. Ez a készülék virtuális gépek felderítését végzi, és a virtuális gépek metaadatait és teljesítményadatait gyűjti.

A készülék beállításához a következőket kell tennie:

- Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
- Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
- Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

Több berendezést is beállíthat egyetlen Azure Migrate projekthez. Az összes készüléken a kiszolgáló értékelése legfeljebb 35 000 virtuális gép felderítését támogatja. Legfeljebb 10 000 kiszolgálót képes észlelni eszközönként.

### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
1. A számítógépek **felderítése** > a **gépek virtualizáltak?** területen válassza **az igen, a VMware vSphere hypervisor**lehetőséget.
1. Válassza a **Letöltés** lehetőséget a petesejtek sablon fájljának letöltéséhez.

   ![A PETESEJT-fájlok letöltésének kiválasztása](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
1. Futtassa a következő parancsot a PETESEJT-fájl kivonatának létrehozásához:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

A verzió 2.19.07.30 a generált kivonatnak meg kell egyeznie az alábbi értékekkel:

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet:

1. A vSphere-ügyfél konzolon válassza a **fájl** > **OVF-sablon telepítése**lehetőséget.

   ![OVF-sablon üzembe helyezéséhez használható menüparancsok](./media/tutorial-assess-vmware/deploy-ovf.png)

1. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
1. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
1. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
1. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
1. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
1. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózat internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
1. Tekintse át és erősítse meg a beállításokat, majd kattintson a **Befejezés gombra**.

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az [Azure URL-címekhez](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket a következő lépések segítségével:

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a virtuális gépre, majd válassza a **konzol megnyitása**lehetőséget.
1. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
   - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
   - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - Válassza a **Proxybeállítások**lehetőséget, majd adja meg a proxy címe és a figyelő portot az űrlapon http://ProxyIPAddress vagy http://ProxyFQDN.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Vegye figyelembe, hogy csak a HTTP-proxy támogatott.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a készülék megadott helyére.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során.       

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Válassza **a bejelentkezés**lehetőséget. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
1. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz.
1. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták, majd válassza ki a projektet.
1. Adja meg a berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **Register** (Regisztrálás) elemre.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **vCenter Server részletek megadása lapon**adja meg a vCenter Server példány nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli.
1. A **Felhasználónév** és a **jelszó**mezőben adja meg azokat a vCenter Server fiók hitelesítő adatait, amelyeket a berendezés a vCenter Server példányon lévő virtuális gépek felderítéséhez használni fog. 

   Győződjön meg arról, hogy a fiók rendelkezik a [felderítéshez szükséges engedélyekkel](migrate-support-matrix-vmware.md#vmware-requirements). [A felderítést](tutorial-assess-vmware.md#set-the-scope-of-discovery) a vCenter-fiókhoz való hozzáférés korlátozásával szűkítheti.
1. Válassza a **kapcsolat ellenőrzése** lehetőséget, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz.

### <a name="specify-vm-credentials"></a>Virtuális gép hitelesítő adatainak megadása
Az alkalmazások, szerepkörök és szolgáltatások felderítéséhez, valamint a virtuális gépek függőségeinek megjelenítéséhez megadhatja a VMware virtuális gépekhez hozzáféréssel rendelkező virtuálisgép-hitelesítő adatokat. Hozzáadhat egy hitelesítő adatot a Windows rendszerű virtuális gépekhez, és egy hitelesítő adatot a linuxos virtuális gépekhez. [További](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) információ a szükséges hozzáférési engedélyekről.

> [!NOTE]
> Ez a bemenet nem kötelező, de szükség van rá, ha engedélyezni szeretné az alkalmazás-felderítést és az ügynök nélküli függőségi vizualizációt.

1. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen válassza a **hitelesítő adatok hozzáadása**elemet.
1. Válasszon ki egy **operációs rendszert**.
1. Adjon meg egy rövid nevet a hitelesítő adatokhoz.
1. A **Felhasználónév** és a **jelszó**mezőben olyan fiókot válasszon, amely legalább vendég hozzáféréssel rendelkezik a virtuális gépeken.
1. Válassza a **Hozzáadás** lehetőséget.

Miután megadta a vCenter Server példányt és a virtuális gép hitelesítő adatait (nem kötelező), válassza a mentés lehetőséget, **és indítsa el a felderítést** a helyszíni környezet felderítésének megkezdéséhez.

Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon. A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. 500 virtuális gépek esetében körülbelül 1 órát vesz igénybe, hogy az alkalmazás leltára megjelenjen a Azure Migrate-portálon.

### <a name="set-the-scope-of-discovery"></a>A felderítés hatókörének beállítása

A felderítés hatóköre a felderítéshez használt vCenter-fiók hozzáférésének korlátozásával lehetséges. A hatókört beállíthatja vCenter Server adatközpontok, fürtök, fürtök, gazdagépek, gazdagépek vagy egyéni virtuális gépek mappájára.

A hatókör beállításához hajtsa végre az alábbi eljárásokat.

#### <a name="1-create-a-vcenter-user-account"></a>1. hozzon létre egy vCenter felhasználói fiókot
1.  Jelentkezzen be a vSphere webes ügyfélprogramba vCenter Server rendszergazdaként.
1.  Válassza az **adminisztráció** > **SSO felhasználók és csoportok**lehetőséget, majd válassza a **felhasználók** lapot.
1.  Válassza az **új felhasználó** ikont.
1.  Adja meg a szükséges adatokat egy új felhasználó létrehozásához, majd kattintson **az OK gombra**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. Adjon meg egy új szerepkört a szükséges engedélyekkel
Ez az eljárás szükséges az ügynök nélküli kiszolgálók áttelepítéséhez.
1.  Jelentkezzen be a vSphere webes ügyfélprogramba vCenter Server rendszergazdaként.
1.  Tallózással keresse meg az **adminisztráció** > a **szerepkör-kezelőt**.
1.  Válassza ki a vCenter Server példányt a legördülő menüből.
1.  Válassza a **szerepkör létrehozása**lehetőséget.
1.  Adja meg az új szerepkör nevét (például <em>Azure_Migrate</em>).
1.  Rendeljen [engedélyeket](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) az újonnan definiált szerepkörhöz.
1.  Kattintson az **OK** gombra.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. engedélyek kiosztása vCenter-objektumokhoz

A vCenter lévő leltári objektumokra vonatkozó engedélyek hozzárendelésének két módja van: az vCenter felhasználói fiókhoz a hozzárendelt szerepkörrel.

A kiszolgáló értékelése során a **csak olvasási jogosultsággal** rendelkező szerepkört a vCenter felhasználói fiókra kell alkalmazni az összes olyan szülőobjektum esetében, ahol a felderíteni kívánt virtuális gépek futnak. A rendszer az összes fölérendelt objektumot tartalmazza: gazdagép, gazdagépek, fürt és a hierarchiában lévő fürtök mappája az adatközpontba. Ezeket az engedélyeket a rendszer a hierarchiában lévő alárendelt objektumokra propagálja.

A kiszolgálók áttelepítéséhez hasonlóan olyan felhasználó által definiált szerepkört kell alkalmaznia, amely jogosult a vCenter felhasználói fiókjára az összes olyan szülőobjektum [esetében, ahol](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) az áttelepíteni kívánt virtuális gépek futnak. Ezt a szerepkört <em>Azure _Migrate</em>is lehet nevezni.

![Engedélyek kiosztása](./media/tutorial-assess-vmware/assign-perms.png)

A másik megoldás, ha a felhasználói fiókot és szerepkört az adatközpont szintjén rendeli hozzá, és propagálja azokat a gyermekobjektumok számára. Ezután adja meg a fióknak, hogy ne legyen **hozzáférési** szerepkör minden olyan objektumhoz (például egy virtuális géphez), amelyet nem szeretne felderíteni vagy áttelepíteni. 

Ez az alternatív konfiguráció nehézkes. Lehetővé teszi a véletlen hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést is. Ezért javasoljuk, hogy az első módszert használja.

> [!NOTE]
> A kiszolgáló értékelése jelenleg nem tudja felderíteni a virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. Ha a felderítést virtuálisgép-mappák szerint szeretné használni, ezt a következő eljárással teheti meg. Gondoskodik arról, hogy a vCenter fiók csak olvasási hozzáféréssel legyen kiosztva virtuálisgép-szinten.
>
> 1. Rendeljen írásvédett engedélyeket azon virtuálisgép-mappák összes virtuális gépén, amelyeken a felderítés hatókörét el szeretné osztani.
> 1. Csak olvasási hozzáférést biztosítson az összes olyan szülő objektumhoz, ahol a virtuális gépeket üzemeltetik. A rendszer a hierarchiában lévő összes szülő objektumot (gazdagép, gazdagép, fürt, a fürtök mappáját) is felveszi az adatközpontba. Nem kell propagálnia az engedélyeket az összes gyermekobjektum számára.
> 1. A felderítési hitelesítő adatok használatával válassza ki az adatközpontot **gyűjtemény hatókörként**. A beállított szerepköralapú hozzáférés-vezérlés biztosítja, hogy a megfelelő vCenter-felhasználó csak a bérlőre jellemző virtuális gépekhez férhessen hozzá.
>
> Vegye figyelembe, hogy a gazdagépek és fürtök mappái támogatottak.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban:

1. Nyissa meg a Azure Migrate irányítópultot.
1. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése**lapon válassza ki a **felderített kiszolgálók**darabszámát megjelenítő ikont.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Azure Migrate Server Assessment használatával kétféle értékelést hozhat létre:

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Felmérések helyszíni méretezés alapján | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően.<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus beállításán alapul.

## <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
1. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén válassza az **értékelés**lehetőséget.

   ![Az értékelés gomb helye](./media/tutorial-assess-vmware/assess.png)

1. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
1. Válassza az **összes megtekintése**lehetőséget, majd tekintse át az értékelés tulajdonságait.

   ![Értékelés tulajdonságai](./media/tutorial-assess-vmware/view-all.png)

1. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
1. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
1. Az **Értékelés létrehozása** elemre kattintva hozza létre a csoportot, és futtassa az értékelést.

   ![Kiszolgálók értékelése](./media/tutorial-assess-vmware/assessment-create.png)

1. Az értékelés létrehozása után megtekintheti azt a **kiszolgálók** > **Azure Migrate: kiszolgálói felmérés** > **értékelések**.
1. Válassza az **értékelés exportálása** lehetőséget, ha Excel-fájlként szeretné letölteni.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

Értékelés megtekintése:

1. Az **áttelepítési célok** > - **kiszolgálók**lapon válassza az **értékelések** **Azure Migrate: kiszolgáló értékelése**lehetőséget.
1. Az **értékelések**területen válasszon ki egy értékelést a megnyitásához.

   ![Értékelés összegzése](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
1. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: használatban van, amikor a Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha a Azure Migrate nem tudja felmérni a készültséget az adatelérhetőségi problémák miatt.

1. Válasszon ki egy **Azure-készültségi** állapotot. Megtekintheti a VM-készültség részleteit. A virtuális gép részleteit is megtekintheti, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Az értékelés összegzése az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit mutatja. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban. A részletezést lenyomva megtekintheti az adott virtuális gépek részletes költségeit.

> [!NOTE]
> A költségbecslés a gép, a lemezek és a hozzá tartozó tulajdonságok méretére vonatkozó javaslatok alapján történik. A becslések a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgálnak. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

Az értékelt csoport összesített tárolási költségei különböző típusú tárolóeszközökön vannak felosztva. 

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Azure Migrate Server Assessment megbízhatósági minősítést rendel egy teljesítmény-alapú értékeléshez, 1 csillag (legalacsonyabb) és 5 csillag között (a legmagasabb).

![Megbízhatósági minősítés](./media/tutorial-assess-vmware/confidence-rating.png)

A megbízhatósági minősítés segít megbecsülni az értékelés méretére vonatkozó javaslatok megbízhatóságát. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul:

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[Ismerje meg](best-practices-assessment.md#best-practices-for-confidence-ratings) a megbízhatósági minősítéssel kapcsolatos ajánlott eljárásokat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Migrate berendezést állít be. Az értékelést is létrehozta és felülvizsgálta.

A VMware virtuális gépek Azure-ba történő áttelepítésének megismeréséhez Azure Migrate kiszolgáló áttelepítésének használatával folytassa a sorozat harmadik oktatóanyagával:

> [!div class="nextstepaction"]
> [VMware virtuális gépek migrálása](./tutorial-migrate-vmware.md)
