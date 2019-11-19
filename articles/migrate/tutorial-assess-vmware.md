---
title: A VMware virtuális gépek felmérése az Azure-ba való Migrálás Azure Migrate
description: Ismerteti, hogyan lehet kiértékelni a helyszíni VMware virtuális gépeket az Azure-ba való Migrálás Azure Migrate használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: 7187cb41fa7ea499035c57e83e04038b1269b418
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158668"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>VMware virtuális gépek felmérése Azure Migrateekkel: kiszolgáló értékelése

Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépeket a Azure Migrate: Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.



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

- [Fejezze](tutorial-prepare-vmware.md) be az első oktatóanyagot ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Azure-engedélyek beállítása](tutorial-prepare-vmware.md#prepare-azure) Azure Migratehoz.
    - A [VMware előkészítése](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) az értékeléshez. A VMware-beállításokat ellenőrizni kell, és rendelkeznie kell engedéllyel egy olyan VMware virtuális gép létrehozásához, amely PETESEJT-sablonnal rendelkezik. Rendelkeznie kell egy, a virtuális gép felderítéséhez beállított fiókkal is. A szükséges portok elérhetőnek kell lenniük, és tisztában kell lennie az Azure-hoz való hozzáféréshez szükséges URL-címekkel.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Állítson be egy új Azure Migrate-projektet a következők szerint.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-vmware/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a földrajzi nevet, amelyben létre kívánja hozni a projektet. Ázsia, Európa, Egyesült Királyság és a Egyesült Államok támogatottak.

    - A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-vmware/migrate-project.png)


7. Kattintson a **Tovább**gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment** > **Next (tovább**) lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-vmware/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.


## <a name="set-up-the-appliance-vm"></a>A berendezés virtuális gép beállítása

Azure Migrate: a kiszolgáló értékelése egy könnyű VMware VM-készüléket futtat.

- Ez a készülék a virtuális gépek felderítését végzi, és a virtuális gépek metaadatait és teljesítményadatait elküldi Azure Migrate kiszolgáló értékelésére.
- A készülék beállítása:
    - Töltse le a petesejtek sablon fájlját, és importálja vCenter Serverba.
    - Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
    - Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.
- Több berendezést is beállíthat egyetlen Azure Migrate projekthez. Minden készüléken a legfeljebb 35 000 virtuális gép felderítése támogatott. Eszközönként legfeljebb 10 000 kiszolgálót lehet felderíteni.

### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A **Gépek felderítése** > **Virtualizáltak a gépek?** területen kattintson az **Igen, a VMware vSphere hipervizorral** lehetőségre.
3. Kattintson a **Letöltés** gombra az .OVA sablonfájl letöltéséhez.

    ![Az .ova-fájl letöltése](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A verzió 2.19.07.30 a generált kivonatnak meg kell egyeznie ezekkel az értékekkel.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.

    ![Az OVF telepítése](./media/tutorial-assess-vmware/deploy-ovf.png)

2. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
5. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
6. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az [Azure URL-címekhez](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket a következő lépések segítségével.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
4. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
        - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDNformában.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
    - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről.
    - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK).
        - Azure Migrate: a kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során.
        - Töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a készülék megadott helyére.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Kattintson **a bejelentkezés**elemre. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
2. Az új lapon jelentkezzen be az Azure-beli hitelesítő adataival.
    - Jelentkezzen be a felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezés után térjen vissza a webalkalmazáshoz.
2. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták, majd válassza ki a projektet.
3. Adja meg a berendezés nevét. A névnek legalább 14 karakterből kell állnia.
4. Kattintson a **regisztrálás**gombra.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **vCenter Server részletek megadása lapon**adja meg a vCenter Server nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen a vCenter Server figyeli.
2. A **Felhasználónév** és a **jelszó**mezőben adja meg a írásvédett fiók hitelesítő adatait, amelyet a berendezés a vCenter-kiszolgálón lévő virtuális gépek felderítéséhez használ majd. Győződjön meg arról, hogy a fiók rendelkezik a [felderítéshez szükséges engedélyekkel](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). A felderítés hatókörét a vCenter-fiókhoz való hozzáférés korlátozásával teheti meg. További információk a hatókör-felderítésről [itt](tutorial-assess-vmware.md#scoping-discovery).
3. Kattintson a **kapcsolat ellenőrzése** elemre, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz.

### <a name="specify-vm-credentials"></a>Virtuális gép hitelesítő adatainak megadása
Az alkalmazások, szerepkörök és szolgáltatások felderítéséhez, valamint a virtuális gépek függőségeinek megjelenítéséhez megadhat egy virtuális gép hitelesítő adatait, amely hozzáfér a VMware virtuális gépekhez. Hozzáadhat egy hitelesítő adatot a Windows rendszerű virtuális gépekhez, és egy hitelesítő adatot a linuxos virtuális gépekhez. [További](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) információ a szükséges hozzáférési jogosultságokról.

> [!NOTE]
> Ez a bemenet nem kötelező, és az alkalmazás-felderítés és az ügynök nélküli függőségi vizualizáció engedélyezéséhez szükséges.

1. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása**lehetőségre.
2. Válassza ki az **operációs rendszert**.
3. Adjon meg egy rövid nevet a hitelesítő adatokhoz.
4. A **Felhasználónév** és a **jelszó**mezőben olyan fiókot válasszon, amely legalább vendég hozzáféréssel rendelkezik a virtuális gépeken.
5. Kattintson az **Hozzáadás** parancsra.

Miután megadta a vCenter Server és a virtuális gép hitelesítő adatait (nem kötelező), kattintson a Save (Mentés) gombra, **és indítsa el a felderítést** a helyszíni környezet felderítésének megkezdéséhez.

Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon. A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe, az időtartam a felderített virtuális gépek számától függ. 500 virtuális gépek esetében körülbelül 1 órát vesz igénybe, hogy az alkalmazás leltára megjelenjen a Azure Migrate-portálon.

### <a name="scoping-discovery"></a>Hatókör felderítése

A felderítés hatóköre a felderítéshez használt vCenter-fiók hozzáférésének korlátozásával lehetséges. A hatókört beállíthatja vCenter Server adatközpontok, fürtök, a fürtök, a gazdagépek, a gazdagépek mappája vagy az egyes virtuális gépek számára.

A hatókör beállításához a következő lépéseket kell elvégeznie:
1.  Hozzon létre egy vCenter felhasználói fiókot.
2.  Adjon meg egy új szerepkört a szükséges jogosultságokkal. (<em>az ügynök nélküli kiszolgáló áttelepítéséhez szükséges</em>)
3.  Engedélyeket rendelhet a felhasználói fiókhoz a vCenter-objektumokon.

**VCenter felhasználói fiók létrehozása**
1.  Jelentkezzen be a vSphere webes ügyfélprogramba vCenter Server rendszergazdaként.
2.  Kattintson a **felügyelet** > **SSO felhasználók és csoportok** > **felhasználók** fülre.
3.  Kattintson az **új felhasználó** ikonra.
4.  Adja meg az új felhasználó létrehozásához szükséges információkat, majd kattintson **az OK gombra**.

**Adjon meg egy új szerepkört a szükséges jogosultságokkal** (<em>az ügynök nélküli kiszolgáló áttelepítéséhez szükséges</em>)
1.  Jelentkezzen be a vSphere webes ügyfelére vCenter Server rendszergazdaként.
2.  Tallózással keresse meg az **adminisztráció** > a **szerepkör-kezelőt**.
3.  Válassza ki a vCenter Server a legördülő menüből.
4.  Kattintson a **szerepkör létrehozása** művelet elemre.
5.  Adja meg az új szerepkör nevét. (például <em>Azure_Migrate</em>).
6.  Rendelje hozzá ezeket az [engedélyeket](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) az újonnan definiált szerepkörhöz.
7.  Kattintson az **OK** gombra.

**Engedélyek kiosztása vCenter-objektumokhoz**

A vCenter lévő leltári objektumokra vonatkozóan 2 megközelítés rendelhető hozzá a vCenter felhasználói fiókhoz, amelyhez hozzá van rendelve egy szerepkör.
- A kiszolgáló-értékeléshez **csak olvasási** szerepkört kell alkalmazni az összes olyan szülőobjektum vCenter felhasználói fiókjához, ahol a felderített virtuális gépek futnak. Az összes szülőobjektum – gazdagép, gazdagép, fürt, a hierarchiában lévő fürtök mappája, amelybe az adatközpontot fel kell venni. Ezeket az engedélyeket a rendszer a hierarchiában lévő alárendelt objektumokra propagálja.

    A kiszolgálók áttelepítéséhez hasonlóan a felhasználó által definiált ( <em>Azure _Migrate</em>nevű) szerepkört is [](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) alkalmazni kell az összes olyan szülőobjektum vCenter felhasználói fiókjához, ahol az áttelepíteni kívánt virtuális gépek futnak.

![Engedélyek kiosztása](./media/tutorial-assess-vmware/assign-perms.png)

- A másik megoldás, ha a felhasználói fiókot és szerepkört az adatközpont szintjén rendeli hozzá, és propagálja azokat a gyermekobjektumok számára. Ezután adja meg a fióknak, hogy ne legyen **hozzáférési** szerepkör minden olyan objektumhoz (például virtuális gépekhez), amelyet nem szeretne felderíteni vagy áttelepíteni. Ez a konfiguráció nehézkes. Lehetővé teszi a véletlen hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést is. Ezért javasoljuk, hogy az első módszert használja.

> [!NOTE]
> A kiszolgáló értékelése jelenleg nem képes felderíteni a virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. Ha a virtuális gép mappáiban szeretné kiterjeszteni a felderítést, a vCenter-fióknak csak olvasási hozzáférése van hozzárendelve a virtuális gép szintjén.  A következő utasításokat követve teheti meg:
>
> 1. Rendeljen írásvédett engedélyeket azon virtuálisgép-mappák összes virtuális gépén, amelyeken a felderítés hatókörét el szeretné osztani.
> 2. Csak olvasási hozzáférést biztosítson az összes olyan szülő objektumhoz, ahol a virtuális gépeket üzemeltetik. Az összes szülőobjektum – gazdagép, gazdagépek, fürt, a fürtök mappája – a hierarchiában az adatközpontig kell szerepelnie. Nem kell propagálnia az engedélyeket az összes gyermekobjektum számára.
> 3. Használja a felderítési hitelesítő adatokat az adatközpont *gyűjtési hatókörként*való kiválasztásához. A RBAC beállítása biztosítja, hogy a megfelelő vCenter-felhasználó csak a bérlőre jellemző virtuális gépekhez férhessen hozzá.
>
> Vegye figyelembe, hogy a gazdagépek és a fürtök mappája támogatott.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.


### <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson az **értékelés**elemre.

    ![Kiértékelés](./media/tutorial-assess-vmware/assess.png)

2. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-vmware/view-all.png)

3. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-vmware/assessment-create.png)

6. Az értékelés létrehozása után megtekintheti azt a **kiszolgálók** > **Azure Migrate: kiszolgálói felmérés** > **értékelések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  - **kiszolgálók**területen kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

2. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-vmware/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](best-practices-assessment.md#best-practices-for-confidence-ratings) információ a megbízhatósági minősítéssel kapcsolatos ajánlott eljárásokról.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Azure Migrate berendezés beállítása
> * Értékelés létrehozva és felülvizsgálva

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepítheti át a VMware virtuális gépeket az Azure-ba Azure Migrate Server áttelepítéssel.

> [!div class="nextstepaction"]
> [VMware virtuális gépek migrálása](./tutorial-migrate-vmware.md)
