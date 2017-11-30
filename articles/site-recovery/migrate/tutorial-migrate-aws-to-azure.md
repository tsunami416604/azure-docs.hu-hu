---
title: "Virtuális gépek áttelepítésére AWS az Azure-bA az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepíthet át virtuális gépeket, Amazon Web Services (AWS) fut az Azure-ba, az Azure Site Recovery segítségével."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6eb6489deacab71e870585d209e26dad801ddc07
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services (AWS) virtuális gépek áttelepítése az Azure-bA

Ez az oktatóanyag útmutatást ad, hogyan telepítheti át az Amazon Web Services (AWS) virtuális gépek (VM), a Site Recovery segítségével Azure virtuális gépekhez. Az Azure-ba, a rendszer kezeli, mintha fizikai, VMsare EC2 példányok áttelepítésekor a helyszíni számítógépek. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure erőforrás előkészítése
> * Az AWS EC2 példányok Felkészülés az áttelepítésre
> * Konfigurációs kiszolgáló központi telepítése
> * Virtuális gépek replikálásának engedélyezése
> * Győződjön meg arról, hogy minden a feladatátvételi teszt működik
> * Egyszeri feladatátvételt végez az Azure

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure-resources"></a>Az Azure erőforrás előkészítése 

Néhány erőforrások készen áll a áttelepített EC2 példányok használata Azure-ban van szüksége. Ezek közé tartozik egy tárfiókot, a tároló és a virtuális hálózati.

### <a name="create-a-storage-account"></a>Create a storage account

Lemezképek replikált gép az Azure storage tartanak. Azure virtuális gépek jönnek létre a tárból feladatátvétel a helyszíni Azure-bA.

1. Az a [Azure-portálon](https://portal.azure.com) menüben kattintson a **új** -> **tárolási** -> **tárfiók**.
2. Adja meg a tárfiók nevét. Ezek az oktatóanyagok nevét használjuk **awsmigrated2017**. A névnek kell Azure belül egyedinek kell, és 3 – 24 karakter, csak számokat és kisbetűket közötti lehet.
3. Tartsa meg az alapértelmezett értéket **telepítési modell**, **fiók kind**, **teljesítmény**, és **szükséges átviteli biztonságos**.
5. Válassza ki az alapértelmezett **RA-GRS** a **replikációs**.
6. Válassza ki az ehhez az oktatóanyaghoz használni kívánt előfizetést.
7. A **erőforráscsoport**, jelölje be **hozzon létre új**. A jelen példában használjuk **migrationRG** neveként.
8. Válassza ki **Nyugat-Európában** helyeként. 
9. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

### <a name="create-a-vault"></a>Tároló létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a bal oldali navigációs **további szolgáltatások** keresse meg és válassza a **Recovery Services-tárolók**.
2. A Recovery Services-tárolók oldalon kattintson **+ Hozzáadás** a lap bal felső sarokban.
3. A **neve**, típus *myVault*. 
4. A **előfizetés**, válassza ki a megfelelő előfizetést.
4. A **erőforráscsoport**, jelölje be **meglévő** válassza *migrationRG*. 
5. A **hely**, jelölje be *Nyugat-Európában*. 
5. Gyorsan hozzáférhet az új tároló az irányítópulton, válassza ki a **rögzítés az irányítópulton**.
7. Amikor elkészült, kattintson a **létrehozása**.

Az új tároló megjelenik a **irányítópult** > **összes erőforrás**, majd a fő **Recovery Services-tárolók** lap.

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Az áttelepítés (feladatátvétel) után az Azure virtuális gépek létrehozásakor, azok ehhez a hálózathoz van csatlakoztatva.

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **új** > **hálózati** >
    **virtuális hálózat**
3. A **neve**, típus *myMigrationNetwork*.
4. Hagyja meg az alapértelmezett érték **Címtéren**.
5. A **előfizetés**, válassza ki a megfelelő előfizetést.
6. A **erőforráscsoport**, jelölje be **használata meglévő** válassza *migrationRG* a legördülő lista a.
7. A **hely**, jelölje be **Nyugat-Európában**. 
8. Hagyja meg az alapértelmezett értéket **alhálózati**, mind a **neve** és **IP-címtartomány**.
9. Hagyja **Szolgáltatásvégpontok** le van tiltva.
10. Amikor elkészült, kattintson a **létrehozása**.


## <a name="prepare-the-ec2-instances"></a>Készítse elő a EC2 példányok

Egy vagy több virtuális gépeket áttelepíteni kívánt van szüksége. Ezek EC2 példány futnia kell a 64 bites Windows Server 2008 R2 SP1 vagy újabb, Windows Server 2012, Windows Server 2012 R2 vagy a Red Hat Enterprise Linux 6.7 (csak HVM virtuális példány). A kiszolgáló csak a Citrix PV vagy AWS PV illesztőprogramok kell rendelkeznie. RedHat PV illesztőprogramok futó példányok nem támogatottak.

A mobilitási szolgáltatás minden replikálni kívánt virtuális gép telepítve kell lennie. A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a virtuális gép replikálása. Automatikus telepítés elő kell készíteni egy fiókot, amely a Site Recovery használatával fogja elérni a virtuális gép EC2-példányokon.

A tartományi vagy helyi fiók is használhatja. Linux virtuális gépekhez a fióknak kell lennie a forráskiszolgálón Linux legfelső szintű. Windows virtuális gépek, ha nem használ egy olyan tartományi fiók, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen:

  - A beállításjegyzékben a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy** majd az értékét állítsa 1.
    
Meg kell, amely akkor használható a Site Recovery konfigurációs kiszolgáló külön EC2 példányt is. Ez a példány futnia kell a Windows Server 2012 R2. 
    

## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése 

Válassza ki a tároló számára a portálon **Site Recovery** a a **bevezetés** szakaszt, és kattintson a **infrastruktúra előkészítése**.

### <a name="1-protection-goal"></a>1 védelmi cél

Válassza ki a következő értékeket a a **védelmi cél** lap:
   
|    |  | 
|---------|-----------|
| Hol találhatók a gépek? | **A helyszíni**|
| Ha szeretné replikálni a gépeket? |**Az Azure-bA**|
| Virtualizáltak a gépek? | **Nem virtualizált vagy egyéb**|

Amikor elkészült, kattintson a **OK** áthelyezése a következő szakaszban.

### <a name="2-source-prepare"></a>2 forrás előkészítése 

Az a **forrás előkészítése** kattintson **+ konfigurációs kiszolgáló**. 

1. Windows Server 2012 R2 rendszerű EC2 példány használatával hozzon létre egy konfigurációs kiszolgálót, és a recovery-tárolóban való regisztrálása.

2. A proxy konfigurálása a EC2 példány virtuális gépet, a konfigurációs kiszolgáló, hogy hozzáférhessen a [szolgáltatás URL-címek](../site-recovery-support-matrix-to-azure.md).

3. Töltse le a [Microsoft Azure Site Recovery az egységes telepítő](http://aka.ms/unifiedinstaller_wus) program. Töltse le a helyi gépére, és majd másolja át a virtuális gép, mint a konfigurációs kiszolgálót használ.

4. Kattintson a **letöltése** gombra kattintva töltse le a tárolóregisztrációs kulcsot. A letöltött fájl másolja át a virtuális gép, mint a konfigurációs kiszolgálót használ.

5. A virtuális Gépre, kattintson a jobb gombbal a letöltött installer a **Microsoft Azure Site Recovery az egységes telepítő** válassza **Futtatás rendszergazdaként**. 

    1. A **előkészületek**, jelölje be **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése** majd **következő**.
    2. A **külső szoftverlicenc**, jelölje be **elfogadom a külső licencszerződést.** majd **következő**.
    3. A **regisztrációs**, kattintson a Tallózás gombra, és navigáljon a ahol helyezze a tároló regisztrációs kulcsot tartalmazó fájlt, és kattintson a **következő**.
    4. A **Internetbeállítások**, jelölje be **csatlakozás az Azure Site Recovery proxykiszolgáló nélkül.** majd **következő**.
    5. Az a **szükséges előfeltételek ellenőrzése** lapon, akkor több cikk ellenőrzéseket futtatja. Amikor elkészült, kattintson **következő**. 
    6. A **MySQL konfigurációs**, adja meg a szükséges jelszavakat, és kattintson a **következő**.
    7. A **környezet részletei**, jelölje be **nem**, nem kell VMware gépet véd, és kattintson a **következő**.
    8. A **helyre telepítse**, kattintson a **következő** az alapértelmezés elfogadásához.
    9. A **Hálózatválasztás**, kattintson a **következő** az alapértelmezés elfogadásához.
    10. A **összegzés** kattintson **telepítése**.
    11. **Folyamatban lévő telepítés** elsajátíthatja, hogy hol találhatók a telepítési folyamat kapcsolatos információkat. Amikor elkészült, kattintson **Befejezés**. Egy előugró ablak kapcsolatos lehetséges újraindítás kellene beolvasni, kattintson a **OK**. Is beolvasni a konfigurációs kiszolgáló kapcsolat jelszava kapcsolatos előugró ablak, a jelszót a vágólapra másolja, majd biztonságos helyen mentse.
    
6. A virtuális Gépre, futtassa **cspsconfigtool.exe** számára hozzon létre egy vagy több felügyeleti fiókot a konfigurációs kiszolgálón. Győződjön meg arról, hogy a felügyeleti fiókok rendszergazdai jogosultságokkal rendelkezik az áttelepíteni kívánt EC2 példányok. 

Ha végzett a konfigurációs kiszolgáló beállítása, lépjen vissza a portálra, és válassza ki a kiszolgálót az imént létrehozott **konfigurációs kiszolgáló** kattintson *OK** áthelyezése előkészítése. cél 3. lépés.

### <a name="3-target-prepare"></a>3 cél előkészítése 

Ebben a szakaszban adhatja meg az erőforrások adatait, akkor jön létre, amikor a végrehajtás során a [előkészítése Azure-erőforrások](#prepare-azure-resources) korábbi szakaszában leírt, ez az oktatóanyag.

1. A **előfizetés**, válassza ki az Azure-előfizetés használatos a [előkészítése Azure](../tutorial-prepare-azure.md) oktatóanyag.
2. Válassza a **Resource Manager** lehetőséget üzemi modellként.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ezeket az erőforrásokat, ha a végrehajtás során létrehozott kell lennie a [előkészítése Azure-erőforrások](#prepare-azure-resources) korábbi szakaszában leírt, ez az oktatóanyag
4. Ha elkészült, kattintson az **OK** gombra.


### <a name="4-replication-settings-prepare"></a>4 replikációs beállítások előkészítése 

Replikáció engedélyezése előtt egy replikációs házirend létrehozásához szükséges

1. Kattintson a **+ replikálja, és társítsa**.
2. A **neve**, típus **myReplicationPolicy**.
3. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **OK** a szabályzat létrehozásához. Az új házirend automatikusan tartozik a konfigurációs kiszolgáló. 

### <a name="5-deployment-planning-select"></a>5 telepítési válasszon tervezése 

A **végzett központi telepítésének megtervezése?**, jelölje be **végzem el újabb** a legördülő majd **OK**.

Ha az összes befejezte az összes 5 szakaszát **infrastruktúra előkészítése**, kattintson a **OK**.


## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze az egyes áttelepíteni kívánt virtuális gépek replikálását. Ha engedélyezve van a replikáció, a Site Recovery automatikusan telepíti a mobilitási szolgáltatást. 

1. Nyissa meg az [Azure portált](htts://portal.azure.com).
1. A tároló számára az oldalon a **bevezetés**, kattintson a **Site Recovery**.
2. A **a helyszíni gépeket, és az Azure virtuális gépek**, kattintson a **1:Replicate alkalmazás lépés**. Fejezze be a következő információkat, és kattintson a varázslólapok **OK** minden oldalon befejezése:
    - 1 forrás konfigurálása:
      
    |  |  |
    |-----|-----|
    | Adatforrás: | **A helyszíni**|
    | Forrás helye:| A konfigurációs kiszolgáló EC2 példány nevét.|
    |Számítógép típusa: | **Fizikai gépek**|
    | Folyamatkiszolgáló: | A legördülő listából válassza ki a konfigurációs kiszolgáló.|
    
    - 2 tároló konfigurálása
        
    |  |  |
    |-----|-----|
    | Cél: | Hagyja változatlanul.|
    | Előfizetés: | Válassza ki az előfizetést, használja.|
    | Feladatátvételt követően erőforráscsoport:| A létrehozott erőforráscsoport használja a [előkészítése Azure-erőforrások](#prepare-azure-resources) szakasz.|
    | Feladatátvételt követően üzembe helyezési modellt: | Válasszon **erőforrás-kezelő**|
    | Storage-fiók: | Válassza ki a tárolási fiók létrehozott a [előkészítése Azure-erőforrások](#prepare-azure-resources) szakasz.|
    | Azure-hálózathoz: | Válasszon **beállítás most a kijelölt gépekhez**|
    | Feladatátvételt követően Azure-hálózathoz: | Válassza ki a létrehozott hálózatot a [előkészítése Azure-erőforrások](#prepare-azure-resources) szakasz.|
    | Alhálózat: | Válassza ki a **alapértelmezett** a a legördülő listán.|
    
    - Válassza ki a 3 fizikai gépek
        
        Kattintson a **+ a fizikai gép** és írja be a **neve**, a **IP-cím** és **operációsrendszer-típus** az áttelepíteni kívánt EC2 példány és Kattintson a **OK**.
        
    - 4 tulajdonságok tulajdonságainak konfigurálása
        
        Válassza ki a fiókot, amelyet a legördülő lista a konfigurációs kiszolgálón hozott létre, és kattintson a **OK**.
        
    - 5 replikációs beállítások replikációs beállítások konfigurálása
    
        Győződjön meg arról, hogy a replikációs házirendet, a legördülő listán kijelölt **myReplicationPolicy** majd **OK**.
        
3. Ha a varázsló befejeződött, kattintson a **engedélyezze a replikálást**.
        

Előrehaladásának nyomon követheti a **Védelemengedélyezési** feladat **figyelés és jelentéskészítés** > **feladatok** > **Site Recovery-feladatok** . A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.        
        
Ha engedélyezi a virtuális gépek replikálása, képes 15 percet vesz igénybe, vagy már érvényesíti a módosítások életbe és jelenik meg a portálon.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. Egy Előfeltételek ellenőrzése során annak biztosítását, hogy a feladatátvétel szükséges feltételek teljesülnek.
2. Feladatátvételi dolgozza fel az adatokat, a, hogy az Azure virtuális gép hozhatók létre. Ha jelölje be a legutóbbi helyreállítási pontot, a helyreállítási pont az adataiból jön létre.
3. Egy Azure virtuális gép jön létre az előző lépésben feldolgozott adatokat használ.

A portálon, a feladatátvételi tesztet futtatni az alábbiak szerint:

1. A tároló számára az oldalon Ugrás **védett elemek** > **replikált elemek**> kattintson a virtuális gép > **+ feladatátvételi teszt**.

2. Válasszon ki egy helyreállítási pontot a a feladatátvételre használni:
    - **Legújabb feldolgozott**: a virtuális gép átadja a feladatokat a Site Recovery által feldolgozott legutóbbi helyreállítási pontot. Az időbélyeg jelenik meg. Ezzel a beállítással nem van feldolgozásával töltött idő adatokat, így egy alacsony RTO (helyreállítási idő célkitűzése) biztosít.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak. Az időbélyeg jelenik meg.
    - **Egyéni**: válassza ki a helyreállítási pontot.
3. A **feladatátvételi teszt**, jelölje ki a cél Azure hálózati mely Azure virtuális gépek csatlakoznak feladatátvételt követően. A létrehozott hálózati legyen a [előkészítése Azure-erőforrások](#prepare-azure-resources) szakasz.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a virtuális gép tulajdonságainak megnyitásához kattintson követheti nyomon. Vagy kattintson a **feladatátvételi teszt** az oldalon, a tároló, a feladat **figyelés és jelentéskészítés** > **feladatok** >
   **hely A helyreállítási feladatok**.
5. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure portálon > **virtuális gépek**. Ellenőrizze, hogy a virtuális gép a megfelelő méretűek, hogy csatlakozik-e a megfelelő hálózati-e és fut-e.
6. Most kell kapcsolódnia kell az Azure-ban a replikált virtuális gép.
7. A feladatátvételi teszt során létrehozott Azure virtuális gépek törléséhez kattintson **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. 


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtassa a EC2 példányok való áttelepítéshez Azure virtuális gépek tényleges feladatátvételt. 

1. A **védett elemek** > **replikált elemek** kattintson az AWS példányok > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** így. Válassza ki a legutóbbi helyreállítási pontot.
3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
4. Ellenőrizze, hogy a virtuális gép megjelenik a **replikált elemek**. 
5. Kattintson a jobb gombbal az egyes virtuális gépek > **áttelepítése**. Ez az áttelepítési folyamat befejezését, leállítja az AWS virtuális gép replikációjának és a Site Recovery számlázási a virtuális gép leáll.

    ![Az áttelepítés végrehajtásához](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikációs le van állítva. Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.  


    

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben már megismerte AWS EC2 példányok áttelepítése az Azure virtuális gépen. További információt Azure virtuális gépeken, továbbra is az oktatóanyagok Windows virtuális gépek esetén.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](../../virtual-machines/windows/tutorial-manage-vm.md)
