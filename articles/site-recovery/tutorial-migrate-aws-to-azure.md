---
title: "Virtuális gépek áttelepítése az AWS-ből az Azure-ba az Azure Site Recoveryvel | Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan telepíthetők át az Amazon Web Servicesben (AWS) futó virtuális gépek az Azure-ba az Azure Site Recovery használatával."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4acdc540ca1a87a4545130eb5fbc096633b0605c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-beli (AWS) virtuális gépek áttelepítése az Azure-ba

Az oktatóanyag bemutatja, hogyan telepítheti át az Amazon Web Services-beli (AWS) virtuális gépeket (VM-eket) Azure-beli virtuális gépekre a Site Recovery használatával. Az EC2-példányok az Azure-ba való áttelepítésekor a rendszer a virtuális gépeket úgy kezeli, mintha azok fizikai, helyszíni számítógépek lennének. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure-erőforrások előkészítése
> * Az AWS EC2-példányok előkészítése az áttelepítésre
> * Konfigurációs kiszolgáló üzembe helyezése
> * Virtuális gépek replikálásának engedélyezése
> * A feladatátvétel tesztelése annak ellenőrzésére, hogy minden működik-e
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure-resources"></a>Azure-erőforrások előkészítése 

Az Azure-ban rendelkezésre kell állnia néhány erőforrásnak, amelyeket az áttelepített EC2-példányok használhatnak. Egy tárfiók, egy kulcstároló és egy virtuális hálózat szükséges.

### <a name="create-a-storage-account"></a>Create a storage account

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során.

1. Az [Azure Portal](https://portal.azure.com) menüjében kattintson az **Új** -> **Tárolás** -> **Tárfiók** elemre.
2. Adja meg a tárfiók nevét. Ezekben az oktatóanyagokban az **awsmigrated2017** nevet használjuk. A névnek egyedinek kell lennie az Azure-ban, 3–24 karakter közé kell esnie, továbbá csak számokat és kisbetűket tartalmazhat.
3. Tartsa meg az **Üzemi modell**, a **Fiók altípusa**, a **Teljesítmény** és **Biztonságos átvitelre van szükség** mezők alapértelmezett értékeit.
5. Válassza az alapértelmezett **RA-GRS** értéket a **Replikáció** mezőben.
6. Válassza ki az oktatóanyaghoz használni kívánt előfizetést.
7. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget. A jelen példában a **migrationRG** nevet használjuk.
8. A helynél válassza a **Nyugat-Európa** beállítást. 
9. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

### <a name="create-a-vault"></a>Tároló létrehozása

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs menüjében kattintson a **További szolgáltatások** lehetőségre, majd keresse meg és válassza a **Recovery Services-tárolók** lehetőséget.
2. A Recovery Services-tárolók oldalon kattintson **+ Hozzáadás** gombra a lap bal felső sarkában.
3. A **Név** mezőbe írja be a *myVault* nevet. 
4. Az **Előfizetés** mezőben válassza ki a megfelelő előfizetést.
4. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd a *migrationRG* lehetőséget. 
5. A **Hely** mezőben válassza a *Nyugat-Európa* lehetőséget. 
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** elemre.
7. Ha végzett, kattintson a **Létrehozás** gombra.

Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor az áttelepítés (feladatátvétel) után létrejönnek az Azure-beli virtuális gépek, azok ehhez a hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Új** > **Hálózat** >
   **Virtuális hálózat** elemre.
3. A **Név** mezőbe írja be a *myMigrationNetwork* nevet.
4. Ne módosítsa a **Címtér** alapértelmezett értékét.
5. Az **Előfizetés** mezőben válassza ki a megfelelő előfizetést.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata** lehetőséget, majd a *migrationRG* lehetőséget a legördülő menüből.
7. A **Hely** mezőnél válassza a **Nyugat-Európa** lehetőséget. 
8. Ne módosítsa az **Alhálózat** alapértelmezett értékeit, sem a **Név**, sem az **IP-címtartomány** mezőben.
9. A **Szolgáltatásvégpontok** beállítást hagyja letiltva.
10. Ha végzett, kattintson a **Létrehozás** gombra.


## <a name="prepare-the-ec2-instances"></a>Az EC2-példányok előkészítése

Szükség lesz egy vagy több áttelepíteni kívánt virtuális gépre. Az EC2-példánynak a Windows Server 2008 R2 SP1 vagy újabb, a Windows Server 2012, a Windows Server 2012 R2 vagy a Red Hat Enterprise Linux 6.7 (csak HVM virtualizált példányok) 64 bites verzióját kell futtatnia. A kiszolgáló csak Citrix PV vagy AWS PV illesztőprogramokkal rendelkezhet. A RedHat PV illesztőprogramokat futtató példányok nem támogatottak.

A mobilitási szolgáltatásnak az összes replikálni kívánt virtuális gépen telepítve kell lennie. A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a virtuális gép replikálását. Az automatikus telepítéshez elő kell készíteni egy fiókot az EC2-példányokon, amelynek használatával a Site Recovery eléri a virtuális gépet.

Tartományi vagy helyi fiókot egyaránt használhat. Linux virtuális gépek esetében a fióknak rendszergazdai fióknak kell lennie a Linux-forráskiszolgálón. Windows virtuális gépek esetében, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen:

  - Adja hozzá a **LocalAccountTokenFilterPolicy** DWORD bejegyzést a beállításjegyzékben a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** területen, és állítsa az értékét 1-re.
    
Egy külön EC2-példányra is szükség lesz, amelyet Site Recovery konfigurációs kiszolgálóként használhat. A példányon Windows Server 2012 R2 operációs rendszernek kell futnia. 
    

## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése 

A tároló portáloldalán válassza a **Site Recovery** lehetőséget a **Bevezetés** szakaszban, és kattintson az **Infrastruktúra előkészítése** gombra.

### <a name="1-protection-goal"></a>1. Védelmi cél

Válassza a következő értékeket a **Védelmi cél** lapon:
   
|    |  | 
|---------|-----------|
| Hol találhatók a gépek? | **A helyszínen**|
| Hová szeretné replikálni a gépeket? |**Az Azure-ba**|
| Virtualizálva vannak a gépek? | **Nincsenek virtualizálva / Egyéb**|

Ha végzett, kattintson az **OK** gombra a következő szakaszra való továbblépéshez.

### <a name="2-source-prepare"></a>2. Forrás előkészítése 

A **Forrás előkészítése** lapon kattintson **+ Konfigurációs kiszolgáló** gombra. 

1. Használjon egy Windows Server 2012 R2 rendszert futtató EC2-példányt a konfigurációs kiszolgáló létrehozásához és a helyreállítási tárolóban való regisztrálásához.

2. Konfigurálja a konfigurációs kiszolgálóként használt EC2-példány virtuális gépén lévő proxyt, hogy elérje a [szolgáltatás URL-címeit](site-recovery-support-matrix-to-azure.md).

3. Töltse le a [Microsoft Azure Site Recovery egyesített telepítő](http://aka.ms/unifiedinstaller_wus) programot. A programot letöltheti a helyi gépére, majd onnan átmásolhatja a konfigurációs kiszolgálóként használt virtuális gépre.

4. Kattintson a **Letöltés** gombra a tárolóregisztrációs kulcs letöltéséhez. Másolja át a letöltött fájlt a konfigurációs kiszolgálóként használt virtuális gépre.

5. A virtuális gépen kattintson a jobb gombbal a letöltött **Microsoft Azure Site Recovery egyesített telepítőre**, majd válassza **Futtatás rendszergazdaként** lehetőséget. 

    1. Az **Előkészületek** területen válassza **A konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** lehetőséget, majd kattintson a **Tovább** gombra.
    2. A **Független gyártótól származó szoftverlicenc** területen jelölje be az **Elfogadom a független gyártó licencszerződését** lehetőséget, majd kattintson a **Tovább** gombra.
    3. A **Regisztráció** területen kattintson a Tallózás gombra, és navigáljon arra a helyre, ahová a tárolóregisztrációs kulcsot mentette, majd kattintson a **Tovább** gombra.
    4. Az **Internetbeállítások** területen válassza a **Közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül** lehetőséget. majd kattintson a **Tovább** gombra.
    5. Az **Előfeltételek ellenőrzése** lapon több elem ellenőrzése is lefut. Ha befejeződött, kattintson a **Tovább** gombra. 
    6. A **MySQL-konfiguráció** területen adja meg a szükséges jelszavakat, majd kattintson a **Tovább** gombra.
    7. A **Környezet részletei** területen jelölje be a **Nem** lehetőséget (a VMware gépeket nem szükséges védeni), majd kattintson a **Tovább** gombra.
    8. A **Telepítés helye** területen kattintson a **Tovább** gombra az alapértelmezett érték elfogadásához.
    9. A **Hálózatválasztás** területen kattintson a **Tovább** gombra az alapértelmezett érték elfogadásához.
    10. Az **Összefoglalás** területen kattintson a **Telepítés** parancsra.
    11. A **Telepítési folyamat** a telepítési folyamat állapotával kapcsolatos információkat mutatja. Ha befejeződött, kattintson a **Befejezés** gombra. Egy előugró ablak jelenik meg, amely egy esetleges újraindításra szólítja fel. Kattintson az **OK** gombra. A konfigurációs kiszolgálói kapcsolat hozzáférési kódjára vonatkozóan is megjelenik egy előugró ablak. Másolja a jelszót a vágólapra, majd mentse egy biztonságos helyre.
    
6. A virtuális gépen a **cspsconfigtool.exe** programot futtatva hozzon létre egy vagy több felügyeleti fiókot a konfigurációs kiszolgálón. Gondoskodjon róla, hogy a felügyeleti fiókok rendelkezzenek rendszergazdai jogosultságokkal az áttelepíteni kívánt EC2-példányokon. 

Ha végzett a konfigurációs kiszolgáló beállításával, lépjen vissza a portálra, válassza ki az imént létrehozott kiszolgálót a **Konfigurációs kiszolgáló** területen, majd az *OK** gombra kattintva lépjen tovább a 3. lépésre (Cél előkészítése).

### <a name="3-target-prepare"></a>3. Cél előkészítése 

Ebben a szakaszban adhatja meg a korábban, az oktatóanyag [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszának végrehajtása során létrehozott erőforrások adatait.

1. Az **Előfizetés** mezőben válassza ki [Az Azure előkészítése](tutorial-prepare-azure.md) oktatóanyagban használt Azure-előfizetést.
2. Válassza a **Resource Manager** lehetőséget üzemi modellként.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ezeknek a korábban, az oktatóanyag [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszának végrehajtása során létrehozott erőforrásoknak kell lenniük.
4. Ha elkészült, kattintson az **OK** gombra.


### <a name="4-replication-settings-prepare"></a>4. Replikációs beállítások – előkészítés 

A replikáció engedélyezése előtt létre kell hoznia egy replikációs szabályzatot.

1. Kattintson a **+ Replikálás és társítás** gombra.
2. A **Név** mezőbe írja be a **myReplicationPolicy** nevet.
3. Hagyja meg a többi alapértelmezett beállítást, és kattintson az **OK** gombra a szabályzat létrehozásához. Az új szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. 

### <a name="5-deployment-planning-select"></a>5. Üzembe helyezés tervezése – kiválasztás 

A **Végzett az üzembe helyezés tervezésével?** területen válassza a **Később végzem el** lehetőséget a legördülő menüből, majd kattintson az **OK** gombra.

Ha az **Infrastruktúra előkészítése** folyamat mind az 5 szakaszával végzett, kattintson az **OK** gombra.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikálást mindegyik áttelepíteni kívánt virtuális gép esetében. Ha a replikáció engedélyezve van, a Site Recovery automatikusan telepíti a mobilitási szolgáltatást. 

1. Nyissa meg az [Azure portált](htts://portal.azure.com).
1. A tároló oldalán a **Bevezetés** szakaszban kattintson a **Site Recovery** gombra.
2. A **Helyszíni gépek és Azure-beli virtuális gépek esetében** területen kattintson az **1. lépés: Az alkalmazás replikálása** elemre. Töltse ki a varázsló lapjait az alábbi adatokkal, és ha végzett, kattintson minden lapon az **OK** gombra:
    - 1. Forrás konfigurálása:
      
    |  |  |
    |-----|-----|
    | Forrás: | **Helyszíni**|
    | Forrás helye:| A konfigurációs kiszolgáló EC2-példányának neve.|
    |Gép típusa: | **Fizikai gépek**|
    | Folyamatkiszolgáló: | Válassza ki a konfigurációs kiszolgálót a legördülő listában.|
    
    - 2. Cél konfigurálása
        
    |  |  |
    |-----|-----|
    | Cél: | Hagyja meg az alapértelmezett értéket.|
    | Előfizetés: | Válassza ki a használt előfizetést.|
    | Feladatátvétel utáni erőforráscsoport:| Használja az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott erőforráscsoportot.|
    | Feladatátvétel utáni üzemi modell: | Válassza a **Resource Manager** lehetőséget.|
    | Tárfiók: | Válassza az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott tárfiókot.|
    | Azure-hálózat: | Válassza a **Beállítás most a kijelölt gépekhez** lehetőséget.|
    | Feladatátvétel utáni Azure-hálózat: | Válassza az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott hálózatot.|
    | Alhálózat: | Válassza az **alapértelmezett** lehetőséget a legördülő listában.|
    
    - 3. Fizikai gépek kiválasztása
        
        Kattintson a **+ Fizikai gép** gombra, és adja meg az áttelepíteni kívánt EC2-példány **Nevét**, **IP-címét** és **Operációsrendszer-típusát**, majd kattintson az **OK** gombra.
        
    - 4. Tulajdonságok – tulajdonságok konfigurálása
        
        Válassza ki a konfigurációs kiszolgálón létrehozott fiókot a legördülő listából, és kattintson az **OK** gombra.
        
    - 5 Replikációs beállítások – replikációs beállítások konfigurálása
    
        Győződjön meg róla, hogy a legördülő listában a **myReplicationPolicy** replikációs szabályzat van kiválasztva, majd kattintson az **OK** gombra.
        
3. A varázsló befejezésekor kattintson a **Replikáció engedélyezése** lehetőségre.
        

A **Védelem engedélyezése** feladat előrehaladását a **Figyelés és jelentéskészítés** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.        
        
Miután engedélyezte egy virtuális gép replikálását, 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel feldolgozza az adatokat, hogy az Azure-beli virtuális gép létrehozható legyen. Ha a legutóbbi helyreállítási pont van kiválasztva, a rendszer egy helyreállítási pontot hoz létre az adatokból.
3. A rendszer létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatok használatával.

A portálon futtassa a feladatátvételi tesztet az alábbiak szerint:

1. A tároló oldalán lépjen a **Védett elemek** > **Replikált elemek** pontra, és kattintson a virtuális gépre, majd a **+ Feladatátvétel tesztelése** gombra.

2. Válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:
    - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
    - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.
3. A **Feladatátvételi teszt** területen válassza ki, hogy az Azure virtuális gépek mely cél Azure-hálózathoz csatlakozzanak majd a feladatátvételt követően. Ennek az [Azure-erőforrások előkészítése](#prepare-azure-resources) szakaszban létrehozott hálózatnak kell lennie.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A folyamat előrehaladásának megtekintéséhez kattintson a virtuális gépre, és nyissa meg a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló oldalán a **Figyelés és jelentéskészítés** > **Feladatok** >
   **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
6. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban.
7. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a helyreállítási terven. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. 


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy tényleges feladatátvételt az EC2-példányokon az Azure-beli virtuális gépekre való áttelepítésükhöz. 

1. A **Védett elemek** > **Replikált elemek** területen kattintson az AWS-példányok > **Feladatátvétel** gombra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Ellenőrizze, hogy a virtuális gép megjelenik-e a **Replikált elemek** között. 
5. Kattintson a jobb gombbal az egyes virtuális gépekre, majd válassza **Az áttelepítés befejezése** lehetőséget. Ez befejezi az áttelepítési folyamatot, valamint leállítja az AWS virtuális gép replikálását és a gép Site Recovery-számlázását.

    ![Az áttelepítés befejezése](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


    

## <a name="next-steps"></a>További lépések

Ebben a témakörben az AWS EC2-példányok az Azure-beli virtuális gépekre való áttelepítésével ismerkedett meg. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagokra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](../virtual-machines/windows/tutorial-manage-vm.md)
