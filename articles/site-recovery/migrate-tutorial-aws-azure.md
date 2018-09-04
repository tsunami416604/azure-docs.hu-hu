---
title: Virtuális gépek áttelepítése az AWS-ből az Azure-ba az Azure Site Recoveryvel | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan migrálhatók az Amazon Web Servicesben (AWS-ben) futó Windows rendszerű virtuális gépek az Azure-ba az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 48714d71edcf54726eb362c7f56950a1350fd52b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885325"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek áttelepítése az Azure-ba

Az oktatóanyag bemutatja, hogyan migrálhatja az Amazon Web Services-beli (AWS) virtuális gépeket (VM-eket) Azure-beli virtuális gépekre az Azure Site Recovery használatával. AWS EC2-példányok Azure-ba való migrálásakor a rendszer a virtuális gépeket úgy kezeli, mintha azok fizikai, helyszíni számítógépek lennének. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * Azure-erőforrások előkészítése
> * Az AWS EC2-példányok előkészítése a migrálásra
> * Konfigurációs kiszolgáló üzembe helyezése
> * Virtuális gépek replikálásának engedélyezése
> * A feladatátvétel tesztelése annak ellenőrzésére, hogy minden működik-e
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
- Ellenőrizze, hogy a migrálni kívánt virtuális gépek támogatott operációsrendszer-verziót futtatnak-e. Támogatott verziók többek között: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - A Windows Server 2008 R2 SP1 vagy újabb 64 bites verziója
  - Red Hat Enterprise Linux 6.7 (csak HVM virtualizált példányok), Citrix PV- vagy AWS PV-illesztővel. A RedHat PV-illesztőket futtató példányok *nem* támogatottak.
 - A mobilitási szolgáltatásnak az összes replikálni kívánt virtuális gépen telepítve kell lennie. 

    > [!IMPORTANT]
    > A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a virtuális gép replikálását. Az automatikus telepítéshez elő kell készíteni egy fiókot az EC2-példányokon, amelynek használatával a Site Recovery eléri a virtuális gépet. Tartományi vagy helyi fiókot egyaránt használhat. 
    > - Linux virtuális gépek esetében a fióknak rendszergazdai fióknak kell lennie a Linux-forráskiszolgálón. 
    > - Windows virtuális gépek esetében, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen:
    >
    >      Ehhez adja hozzá a **LocalAccountTokenFilterPolicy** DWORD bejegyzést **1** értékkel a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**       helyen található beállításjegyzékhez.

- Egy külön EC2-példány, amelyet Site Recovery konfigurációs kiszolgálóként használhat. A példányon Windows Server 2012 R2 operációs rendszernek kell futnia.

## <a name="prepare-azure-resources"></a>Azure-erőforrások előkészítése

Az Azure-ban rendelkezésre kell állnia néhány erőforrásnak, amelyeket a migrált EC2-példányok használhatnak. Egy tárfiók, egy kulcstároló és egy virtuális hálózat szükséges.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során.

1. Az [Azure Portal](https://portal.azure.com) bal oldali menüjében válassza az **Erőforrás létrehozása** > **Tárolás** > **Tárfiók** lehetőséget.
2. Adja meg a tárfiók nevét. Ezekben az oktatóanyagokban az **awsmigrated2017** nevet használjuk. A névre vonatkozó feltételek:
    - Egyedinek kell lennie az Azure-ban
    - 3 és 24 karakter között lehet a hosszúsága
    - Csak számokat és kisbetűket tartalmazhat
3. Tartsa meg az **Üzemi modell**, a **Fiók altípusa**, a **Teljesítmény** és **Biztonságos átvitelre van szükség** mezők alapértelmezett értékeit.
5. A **Replikáció** mezőben válassza az alapértelmezett **RA-GRS** értéket.
6. Válassza ki az oktatóanyaghoz használni kívánt előfizetést.
7. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget. A jelen példában a **migrationRG** erőforráscsoportot használjuk.
8. A **Hely** mezőnél válassza a **Nyugat-Európa** lehetőséget.
9. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

### <a name="create-a-vault"></a>Tároló létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. Keresse meg és válassza ki a **Recovery Services-tárolók** elemet.
2. Az Azure Recovery Services.tárolók oldalon válassza a **Hozzáadás** lehetőséget.
3. A **Név** mezőbe írja be a **myVault** nevet.
4. Az **Előfizetés** mezőben válassza ki a használni kívánt előfizetést.
4. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd a **migrationRG** lehetőséget.
5. A **Hely** mezőnél válassza a **Nyugat-Európa** lehetőséget.
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** elemre.
7. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

Az új tároló megtekintéséhez lépjen az **Irányítópult** > **Minden erőforrás** elemhez. Az új tároló a fő **Recovery Services-tárolók** lapon is megjelenik.

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor a migrálás (feladatátvétel) után Azure-beli virtuális gépek jönnek létre, ehhez az Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása**>**Hálózatkezelés**>
   **Virtuális hálózat** lehetőséget.
3. A **Név** mezőbe írja be a **myMigrationNetwork** nevet.
4. Ne módosítsa a **Címtér** alapértelmezett értékét.
5. Az **Előfizetés** mezőben válassza ki a használni kívánt előfizetést.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd a **migrationRG** lehetőséget.
7. A **Hely** mezőnél válassza a **Nyugat-Európa** lehetőséget.
8. Az **Alhálózat** területen hagyja meg az alapértelmezett beállításokat a **Név** és az **IP-címtartomány** alatt.
9. A **Szolgáltatásvégpontok** beállítást hagyja letiltva.
10. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése

Az Azure Portalon lévő tárolóoldal **Bevezetés** szakaszában válassza a **Site Recovery**, majd az **Infrastruktúra előkészítése** lehetőséget. Hajtsa végre a következő lépéseket.

### <a name="1-protection-goal"></a>1. Védelmi cél

A **Védelmi cél** oldalon válassza a következő értékeket:

|    |  |
|---------|-----------|
| Hol találhatók a gépek? |Válassza a **Helyszíni** lehetőséget.|
| Hová szeretné replikálni a gépeket? |Válassza **Az Azure-ba** lehetőséget.|
| Virtualizálva vannak a gépek? |Válassza a **Nincsenek virtualizálva / Egyéb** lehetőséget.|

Ha végzett, válassza az **OK** gombot a következő szakaszra való továbblépéshez.

### <a name="2-prepare-source"></a>2. Forrás előkészítése

A **Forrás előkészítése** lapon válassza a **+ Konfigurációs kiszolgáló** gombra.

1. Használjon egy Windows Server 2012 R2 rendszert futtató EC2-példányt a konfigurációs kiszolgáló létrehozásához és a helyreállítási tárolóban való regisztrálásához.
2. Konfigurálja a konfigurációs kiszolgálóként használt EC2-példány virtuális gépén lévő proxyt, hogy elérje a [szolgáltatás URL-címeit](site-recovery-support-matrix-to-azure.md).
3. Töltse le a [Microsoft Azure Site Recovery egyesített telepítő](http://aka.ms/unifiedinstaller_wus) programot. A programot letöltheti a helyi gépére, majd onnan átmásolhatja a konfigurációs kiszolgálóként használt virtuális gépre.
4. Válassza a **Letöltés** gombot a tárolóregisztrációs kulcs letöltéséhez. Másolja át a letöltött fájlt a konfigurációs kiszolgálóként használt virtuális gépre.
5. A virtuális gépen kattintson a jobb gombbal a letöltött Microsoft Azure Site Recovery egyesített telepítőre, majd válassza a **Futtatás rendszergazdaként** lehetőséget.

    1. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget, majd válassza a **Tovább** lehetőséget.
    2. A **Független gyártótól származó szoftverlicenc** területen jelölje be az **Elfogadom a független gyártó licencszerződését** lehetőséget, majd válassza a **Tovább** lehetőséget.
    3. A **Regisztráció** területen válassza a **Tallózás** lehetőséget, és navigáljon arra a helyre, ahová a tárolóregisztrációs kulcsfájlt mentette. Kattintson a **Tovább** gombra.
    4. Az **Internetbeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül**, majd a **Tovább** lehetőséget.
    5. Az **Előfeltételek ellenőrzése** lapon több elem ellenőrzése is lefut. Ha befejeződött, válassza a **Tovább** lehetőséget.
    6. A **MySQL-konfiguráció** területen adja meg a szükséges jelszavakat, majd válassza a **Tovább** lehetőséget.
    7. A **Környezet részletei** területen jelölje be a **Nem** lehetőséget. A VMware gépeket nem szükséges védeni. Ezután válassza a **Tovább** lehetőséget.
    8. A **Telepítés helye** területen válassza a **Tovább** lehetőséget az alapértelmezett érték elfogadásához.
    9. A **Hálózatválasztás** területen válassza a **Tovább** lehetőséget az alapértelmezett érték elfogadásához.
    10. Az **Összegzés** területen válassza a **Telepítés** lehetőséget.
    11. A **Telepítési folyamat** a telepítési folyamattal kapcsolatos információkat jelenít meg. Ha befejeződött, válassza a **Befejezés** lehetőséget. Egy ablakban megjelenik egy újraindítással kapcsolatos üzenet. Kattintson az **OK** gombra. Ezután egy ablakban megjelenik egy üzenet a konfigurációs kiszolgáló kapcsolati jelszavával. Másolja a jelszót a vágólapra, majd mentse egy biztonságos helyre.
6. A virtuális gépen a cspsconfigtool.exe programot futtatva hozzon létre egy vagy több felügyeleti fiókot a konfigurációs kiszolgálón. Gondoskodjon arról, hogy a felügyeleti fiókok rendelkezzenek rendszergazdai jogosultságokkal a migrálni kívánt EC2-példányokon.

Ha végzett a konfigurációs kiszolgáló beállításával, lépjen vissza a portálra, és válassza ki az imént létrehozott kiszolgálót a **Konfigurációs kiszolgáló** területen. Az **OK** lehetőséget választva lépjen a 3. A cél előkészítése szakaszhoz.

### <a name="3-prepare-target"></a>3. A cél előkészítése

Ebben a szakaszban adhatja meg a korábban, az oktatóanyag [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszában létrehozott erőforrások adatait.

1. Az **Előfizetés** mezőben válassza ki [Az Azure előkészítése](tutorial-prepare-azure.md) oktatóanyagban használt Azure-előfizetést.
2. Válassza a **Resource Manager** lehetőséget üzemi modellként.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ezek azok az erőforrások, amelyeket korábban, az oktatóanyag [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszában hozott létre.
4. Amikor elkészült, válassza az **OK** lehetőséget.


### <a name="4-prepare-replication-settings"></a>4. Replikációs beállítások előkészítése

A replikáció engedélyezése előtt létre kell hoznia egy replikációs szabályzatot.

1. Válassza a **Replikálás és társítás** lehetőséget.
2. A **Név** mezőben adja meg a **myReplicationPolicy** nevet.
3. Hagyja meg a többi alapértelmezett beállítást, és válassza az **OK** gombot a szabályzat létrehozásához. Az új szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.

### <a name="5-select-deployment-planning"></a>5. Az üzembe helyezés tervezésének kiválasztása

A **Végzett az üzembe helyezés tervezésével?** területen válassza a **Később végzem el** lehetőséget, majd válassza az **OK** lehetőséget.

Ha az **Infrastruktúra előkészítése** szakasz mind az öt lépését elvégezte, válassza az **OK** lehetőséget.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikálást mindegyik migrálni kívánt virtuális gép esetében. Ha a replikáció engedélyezve van, a Site Recovery automatikusan telepíti a mobilitási szolgáltatást.

1. Nyissa meg az [Azure Portal](htts://portal.azure.com).
1. A tároló oldalán, a **Bevezetés** szakaszban válassza a **Site Recovery** lehetőséget.
2. A **Helyszíni gépek és Azure-beli virtuális gépek esetében** területen válassza az **1. lépés: Az alkalmazás replikálása** elemet. Töltse ki a varázsló lapjait az alábbi adatokkal. Minden oldalon válassza az **OK** lehetőséget, ha elkészült:
    - 1. A forrás konfigurálása

    |  |  |
    |-----|-----|
    | Forrás: | Válassza a **Helyszíni** lehetőséget.|
    | Forrás helye:| Adja meg a konfigurációs kiszolgáló EC2-példányának nevét.|
    |Gép típusa: | Válassza a **Fizikai gépek** lehetőséget.|
    | Folyamatkiszolgáló: | Válassza ki a konfigurációs kiszolgálót a legördülő listában.|

    - 2. A cél konfigurálása

    |  |  |
    |-----|-----|
    | Cél: | Hagyja meg az alapértelmezett értéket.|
    | Előfizetés: | Válassza ki a használt előfizetést.|
    | Feladatátvétel utáni erőforráscsoport:| Használja az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott erőforráscsoportot.|
    | Feladatátvétel utáni üzemi modell: | Válassza a **Resource Manager** lehetőséget.|
    | Tárfiók: | Válassza ki az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott tárfiókot.|
    | Azure-hálózat: | Válassza a **Beállítás most a kijelölt gépekhez** lehetőséget.|
    | Feladatátvétel utáni Azure-hálózat: | Válassza az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott hálózatot.|
    | Alhálózat: | Válassza az **alapértelmezett** lehetőséget a legördülő listában.|

    - 3. A fizikai gépek kiválasztása

      Válassza a **Fizikai gép** lehetőséget, és adja meg a migrálni kívánt EC2-példány **Nevét**, **IP-címét** és **Operációsrendszer-típusát**. Kattintson az **OK** gombra.

    - 4. A tulajdonságok konfigurálása

      Válassza ki a konfigurációs kiszolgálón létrehozott fiókot, és válassza az **OK** lehetőséget.

    - 5. A replikációs beállítások konfigurálása

      Győződjön meg arról, hogy a legördülő listában kiválasztott replikációs szabályzat a **myReplicationPolicy**, majd válassza az **OK** lehetőséget.

3. Ha a varázsló befejeződött, válassza a **Replikáció engedélyezése** lehetőséget.

A **Védelem engedélyezése** feladat állapotának nyomon követéséhez lépjen a **Figyelés és jelentéskészítés** > **Feladatok** > **Site Recovery-feladatok** menüpontra. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.        

Miután engedélyezte egy virtuális gép replikálását, 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő események történnek:

- A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
- A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
- A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

A portálon futtassa a feladatátvételi tesztet:

1. A tároló oldalán lépjen a **Védett elemek** > **Replikált elemek** pontra. Válassza ki a virtuális gépet, majd válassza a **Feladatátvételi teszt** lehetőséget.
2. Válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:
    - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
    - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. A **Feladatátvételi teszt** területen válassza ki, hogy az Azure virtuális gépek mely cél Azure-hálózathoz csatlakozzanak majd a feladatátvételt követően. Ennek az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott hálózatnak kell lennie.
4. A feladatátvételi művelet elindításához válassza az **OK** lehetőséget. Az állapot nyomon követéséhez a virtuális gépet kiválasztva jelenítse meg a tulajdonságait. Vagy kiválaszthatja a tár oldalán lévő **Feladatátvételi teszt** feladatot. Ehhez válassza a **Figyelés és jelentéskészítés** > **Feladatok** >  **Site Recovery-feladatok** lehetőséget.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portalon. A virtuális gép megtekintéséhez válassza a **Virtuális gépek** lehetőséget. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
6. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban.
7. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez válassza a **Feladatátvételi teszt eltávolítása** elemet a helyreállítási terven. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges. A feldolgozás befejezése 8–10 percet is igénybe vehet.

## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy tényleges feladatátvételt az EC2-példányokon az Azure-beli virtuális gépekre való migrálásukhoz:

1. A **Védett elemek** > **Replikált elemek** területen válassza az AWS-példányokat, majd a **Feladatátvétel** lehetőséget.
2. A **Feladatátvétel** területen válassza ki a **helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Győződjön meg arról, hogy a virtuális gép megjelenik a **replikált elemek** között.
5. Kattintson a jobb gombbal az egyes virtuális gépekre, majd válassza a **Migrálás befejezése** lehetőséget. Ez befejezi az áttelepítési folyamatot, valamint leállítja az AWS virtuális gép replikálását és a gép Site Recovery-számlázását.

    ![Az áttelepítés befejezése](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Ne szakítsa meg a folyamatban lévő feladatátvételt*. A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="next-steps"></a>További lépések

Ebben a cikkben az AWS EC2-példányok Azure-beli virtuális gépekre való migrálásával ismerkedett meg. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagokra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](../virtual-machines/windows/tutorial-manage-vm.md)
