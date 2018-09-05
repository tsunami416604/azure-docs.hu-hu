---
title: Az Azure Stack virtuális gépek replikálása az Azure Site Recovery használatával |} A Microsoft Docs
description: Ismerje meg, hogyan vészhelyreállítás beállítása az Azure-bA az Azure Stack-beli virtuális gépek az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/30/2018
ms.author: raynew
ms.openlocfilehash: c71f683355a09c8ba2381db406eeb1ccabdb7afa
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697696"
---
# <a name="replicate-azure-stack-vms-to-azure-preview"></a>Az Azure Stack virtuális gépek replikálása az Azure-ba (előzetes verzió)

Ez a cikk bemutatja, hogyan állítható be vészhelyreállítást az Azure-bA az Azure Stack virtuális gépek, használatával a [Azure Site Recovery szolgáltatás](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

A Site Recovery hozzájárul (az üzleti folytonossági és vészhelyreállítási BCDR) stratégia. A szolgáltatás biztosítja, hogy a virtuális gépek számítási feladataihoz és továbbra is elérhető, amikor várhatóan váratlan leállások esetén.

- A Site Recovery összehangolja, és felügyeli a virtuális gépek replikálása az Azure storage.
- Szolgáltatáskimaradás esetén az elsődleges helyen, a Site Recovery használatával az Azure-bA feladatátvételt.
- Feladatátvétel esetén az Azure virtuális gépek jönnek létre a tárolt virtuális gép adatokból, és a felhasználók továbbra is hozzáférő, Azure virtuális gépeken futó számítási feladatokat.
- Ha mindent újra működik, Azure virtuális gépek feladatokat az elsődleges helynek, és -bA replikáljon az Azure storage újra.


> [!NOTE]
> Az Azure Stack a Site Recovery jelenleg nyilvános előzetes verzióban érhető el.


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * **1. lépés: A replikáció az Azure stack-beli virtuális gépek előkészítése**. Ellenőrizze, hogy a virtuális gépek megfelelnek a Site Recovery követelményeinek, és készítse elő a Site Recovery mobilitási szolgáltatás telepítéséhez. Ez a szolgáltatás minden replikálni kívánt gépen telepítve van.
> * **2. lépés: Recovery Services-tároló beállításához**. Állítsa be a tárolót a Site Recoveryhez, és adja meg, hogy mit szeretne replikálni. Site Recovery-összetevőkhöz, és a műveletek konfigurálása és kezelése a tárolóban.
> * **3. lépés: A forrás replikációs környezet beállítása**. Állítsa be a Site Recovery konfigurációs kiszolgálónak. A konfigurációs kiszolgáló az egyetlen Azure Stack virtuális Gépet a Site Recovery számára szükséges összes összetevőt futtató. Miután beállította a konfigurációs kiszolgálót, regisztrálja a tárolóban.
> * **4. lépés: A replikálási célkörnyezet beállítása**. Válassza ki az Azure-fiókra, és az Azure storage-fiók és a használni kívánt hálózati. A replikáció során a virtuális gép adatait az Azure storage-másolódik. Azure virtuális gépek a feladatátvételt követően a megadott hálózati csatlakoznak.
> * **5. lépés: A replikáció engedélyezése**. Replikációs beállítások konfigurálása, és a virtuális gépek replikációjának engedélyezése. A mobilitási szolgáltatás a virtuális gép települ, amikor a replikáció engedélyezve van. A Site Recovery hajtja végre a virtuális gép kezdeti replikációját, és folyamatban lévő replikáció megkezdi.
> * **6. lépés: Futtassa a vészhelyreállítási próbát**: után a replikáció működik, akkor győződjön meg arról, hogy a feladatátvétel futtatásával a próba várt módon fog működni. A részletezés kezdeményezni, egy feladatátvételi tesztet a Site Recovery futtatja. A feladatátvételi teszt nem érinti az éles környezetben.

A következő lépésekkel teljes körű majd futtathatja egy teljes feladatátvételi Azure-ba, és ha kell.

## <a name="architecture"></a>Architektúra

![Architektúra](./media/azure-stack-site-recovery/architecture.png)

**Hely** | **Összetevő** |**Részletek**
--- | --- | ---
**Konfigurációs kiszolgáló** | Az Azure Stack egyetlen virtuális Gépet futtat. | Az egyes előfizetésekben beállította a konfigurációs kiszolgáló virtuális Géphez. Ez a virtuális gép fut, a következő Site Recovery-összetevők:<br/><br/> – A konfigurációs kiszolgáló: a helyszíni és Azure közötti kommunikáció koordinálását, valamint felügyeli az adatreplikációt. -A folyamatkiszolgáló: replikációs átjáróként üzemel. Ez fogadja a replikációs adatokat, optimalizálja a gyorsítótárazás, tömörítés és titkosítás; és elküldi azt az Azure-tárolóba.<br/><br/> Ha replikálni kívánt virtuális gépek lépik túl az alábbiakban említett, akkor külön önálló folyamatkiszolgáló állíthatja. [További információk](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**A mobilitási szolgáltatás** | Minden replikálni kívánt gépen telepítve. | A jelen cikkben ismertetett lépések azt készíteni egy fiókot, hogy a mobilitási szolgáltatás automatikusan települ a virtuális gép replikációs engedélyezésekor. Ha nem szeretné automatikusan telepítse a szolgáltatást, számos más módszerekkel is használhatja. [További információk](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Az Azure-ban kell a Recovery Services-tárolót, egy tárfiókot és egy virtuális hálózatot. |  A replikált adatok a storage-fiókban tárolt. Az Azure virtuális gépek feladatátvétel esetén kerülnek az Azure-hálózatot. 


Replikáció a következőképpen történik:

1. A tárolóban adja meg a replikációs forrást és célt, állítsa be a konfigurációs kiszolgálót, létrehoz egy replikációs házirendet, és engedélyezze a replikációt.
2. A mobilitási szolgáltatás telepítve van a számítógépen (ha az ügyfélleküldéses telepítés használta), és a gépek a replikációs szabályzat megfelelően replikálása elindítva.
3. Egy kezdeti másolatot készít a kiszolgáló adatainak replikálása az Azure storage.
4. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. A konfigurációs kiszolgáló replikációs folyamat kezelését az Azure-ral (HTTPS 443-as kimenő porton).
6. A folyamatkiszolgáló adatokat fogad az forrásgépek, optimalizálja a és titkosítja azokat, és elküldi őket az Azure storage (a 443-as kimenő porton).
7. A replikált gépek kommunikálnak a konfigurációs kiszolgáló (port HTTPS 443-as porton bejövő kezelése érdekében a replikálást. Gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak (bejövő 9443-as HTTPS - port módosítható).
8. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Azt is megteheti használhatja az Azure ExpressRoute nyilvános társviszony-létesítés. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

## <a name="prerequisites"></a>Előfeltételek

Íme, mi kell állítania ebben a forgatókönyvben.

**Követelmény** | **Részletek**
--- | ---
**Azure-előfizetése fiókját** | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
**Azure-fiók engedélyei** | Az Azure-fiók használata engedélyekre van szüksége:<br/><br/> – A Recovery Services-tároló létrehozása<br/><br/> – A virtuális gép létrehozása az erőforráscsoportban és virtuális hálózat a forgatókönyvet használja<br/><br/> -Írni a megadott tárfiók<br/><br/> Vegye figyelembe:<br/><br/> – Ha létrehoz egy fiókot, akkor Ön az előfizetés rendszergazdája, és minden művelet végrehajtására.<br/><br/> – Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, a rendszergazdától tulajdonosi vagy közreműködői jogosultságot dolgozni szeretne.<br/><br/> – Ha részletesebb engedélyek van szüksége, tekintse át a [Ez a cikk](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Az Azure Stack VM** | Azure Stack virtuális gép a bérlő az előfizetésben, amely telepíti a Site Recovery konfigurációs kiszolgálóként van szüksége. 


### <a name="prerequisites-for-the-configuration-server"></a>A konfigurációs kiszolgáló előfeltételei

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>1. lépés: Az Azure Stack-beli virtuális gépek előkészítése

### <a name="verify-the-operating-system"></a>Ellenőrizze az operációs rendszer

Győződjön meg arról, hogy a virtuális gépek futnak a táblázatban összefoglalt operációs rendszerek egyikét.


**Operációs rendszer** | **Részletek**
--- | ---
**64 bites Windows** | A Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (SP1)
**CentOS** | 5.2 a 5.11, 6.1, 6.9, 7.0-ban való 7.3
**Ubuntu** | 14.04 server LTS, 16.04 LTS server. Felülvizsgálat [kernelekkel támogatott](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Előkészítése a mobilitási szolgáltatás telepítése

Minden replikálni kívánt virtuális gép rendelkeznie kell a mobilitási szolgáltatás telepítve van. Ahhoz, hogy a szolgáltatás automatikusan a virtuális Gépre telepíteni, ha a replikáció engedélyezve van a folyamatkiszolgálón ellenőrizze a virtuális gép beállításait.

#### <a name="windows-machines"></a>Windows-gépeken

- Meg kell hálózati kapcsolat a virtuális gép, amelyen szeretné engedélyezni a replikációt, és a folyamatkiszolgáló futtató gép között (alapértelmezés szerint azt a konfigurációs kiszolgáló virtuális gép).
- Egy fiók rendszergazdai jogosultságokkal (helyi vagy tartományi) a számítógépen, amelynek a replikáció engedélyezése szükséges.
    - A Site Recovery üzembe helyezésekor meg ezt a fiókot adja meg. A folyamatkiszolgáló majd ezt a fiókot használja, ha a replikáció engedélyezve van a mobilitási szolgáltatás telepítése.
    - Ez a fiók csak használják a Site Recovery a leküldéses telepítéshez és frissíteni a mobilitási szolgáltatást.
    - Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlés a virtuális gépen szeretné:
        - A beállításjegyzék létrehozása a DWORD értékét **LocalAccountTokenFilterPolicy** adja.
        - Állítsa az értékét 1-re.
        - Ehhez a parancssorba írja be a következőt: **REG adja hozzá a localaccounttokenfilterpolicy /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- A Windows tűzfal a replikálni kívánt virtuális gépen a fájl és a nyomtató megosztása és a WMI engedélyezése.
    - Ehhez futtassa **wf.msc** a Windows tűzfal konzol megnyitásához. Kattintson a jobb gombbal **bejövő szabályok** > **új szabály**. Válassza ki **előre definiált**, és válassza a **fájl- és nyomtatómegosztás megosztása** a listából. A kapcsolat engedélyezéséhez válassza ki a varázsló befejezéséhez > **Befejezés**.
    - A tartományi számítógépek esetében a csoportházirend-objektum segítségével ehhez.

    
#### <a name="linux-machines"></a>Linux rendszerű gépeken

- Gondoskodjon róla, hogy legyen hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló közt.
- A számítógépen, amelyhez engedélyezi a replikációt szüksége van, akkor a gyökér szintű felhasználó a forrás Linux-kiszolgálón:
    - A Site Recovery üzembe helyezésekor meg ezt a fiókot adja meg. A folyamatkiszolgáló majd ezt a fiókot használja, ha a replikáció engedélyezve van a mobilitási szolgáltatás telepítése.
    - Ez a fiók csak használják a Site Recovery a leküldéses telepítéshez és frissíteni a mobilitási szolgáltatást.
- Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
- Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
- Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
- Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
    1. Ehhez jelentkezzen be gyökérszintű felhasználóként.
    2. Keresse meg a sort, amely a következővel kezdődik: **PasswordAuthentication**, a /etc/ssh/sshd_config fájlban. Állítsa vissza a sort, és módosítsa az értéket **yes** értékre.
    3. Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

        ![Linux a mobilitási szolgáltatás](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Indítsa újra az sshd szolgáltatást.


### <a name="note-the-vm-private-ip-address"></a>Megjegyzés: a virtuális gép magánhálózati IP-cím

Minden replikálni kívánt gépen keresse meg az IP-cím:

1. Az Azure Stack portálon kattintson a virtuális gépen.
2. Az a **erőforrás** menüben kattintson a **hálózati adapterek**.
3. Jegyezze fel a magánhálózati IP-címet.

    ![Magánhálózati IP-cím](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2. lépés: Hozzon létre egy tárolót, és a replikációs cél kiválasztása

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Monitoring és felügyelet** > **Backup és Site Recovery** lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. 
3. A **erőforráscsoport**, hozzon létre vagy válasszon ki egy erőforráscsoportot. Használunk **contosoRG**.
4. A **hely**, adja meg az Azure-régióban. a **Nyugat-Európa** beállítást használjuk.
5. Ha szeretne gyorsan hozzáférni az új tárolóhoz az irányítópultról, válassza a **Rögzítés az irányítópulton** > **Létrehozás** elemet.

   ![Új tároló létrehozása](./media/azure-stack-site-recovery/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult** > **Minden erőforrás** menüben, illetve a központi **Recovery Services-tárolók** lapon.

### <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** > adja meg a tároló nevét. Használunk **ContosoVMVault**.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **virtualizáltak a gépek**válassza **nem virtualizált/egyéb**. Ezután kattintson az **OK** gombra.

    ![Védelmi cél](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3. lépés: A forráskörnyezet beállítása

A konfigurációs kiszolgáló gép, regisztrálja a tárolóban, és a replikálni kívánt gépek felderítése.

1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+Konfigurációs kiszolgáló** gombra.

    ![A forrás beállítása](./media/azure-stack-site-recovery/plus-config-srv.png)

3. A **-kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
5. A Site Recovery egyesített telepítőjének telepítőfájl letöltéséhez.
6. Töltse le a tároló regisztrációs kulcsát. A regisztrációs kulcs egyesített telepítő futtatásakor kell. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Futtassa az Azure Site Recovery egyesített telepítővel

Telepítse, és regisztrálja a konfigurációs kiszolgáló, hajtsa végre egy RDP-kapcsolatot a virtuális gép konfigurációs kiszolgálóként használni kívánt, és futtassa az egyesített telepítő.

Mielőtt elkezdené, győződjön meg arról, hogy van-e az óra [időkiszolgálóval szinkronizálva](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) megkezdése előtt a virtuális gépen. Ha ki helyi idő több mint öt perc alatt az idő nem telepíthető.

Most telepíteni a konfigurációs kiszolgáló:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgálót a parancssorból is telepíthető. [További információk](http://aka.ms/installconfigsrv).

> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemet.

## <a name="step-4-set-up-the-target-environment"></a>4. lépés: A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. A **infrastruktúra előkészítése** > **cél**, válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a cél üzembehelyezési modellje.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ha azok nem talál, meg kell legalább egy tárfiókot és a virtuális hálózat létrehozása annak érdekében, hogy a varázsló befejezéséhez.


## <a name="step-5-enable-replication"></a>5. lépés: A replikáció engedélyezése

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Kattintson a **az infrastruktúra előkészítése** > **replikációs beállítások**.
2. A **replikációs házirend létrehozása**, adja meg a szabályzat nevét.
3. A **helyreállítási Időkorlát küszöbértéke**, adja meg a helyreállítási pont célkitűzés (RPO) vonatkozó korlátozás.
    - A replikált adatok helyreállítási pontjait beállítás szerint jönnek létre.
    - Ez a beállítás nem befolyásolja a replikáció, azaz folyamatos. Ez egyszerűen egy riasztással figyelmeztet, ha a küszöb határt egy helyreállítási pont létrehozása nélkül.
4. A **helyreállítási pont megőrzése**, adja meg, mennyi ideig van megtartva egyes helyreállítási pontok. A replikált virtuális gépek az adott időszakon belül bármikor helyreállíthatók legyenek.
5. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, hogy milyen gyakran jönnek létre alkalmazáskonzisztens pillanatképeket.

    - Alkalmazáskonzisztens pillanatkép a virtuális Gépen található alkalmazásadatok időponthoz pillanatképet.
    - Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális Gépet az alkalmazások konzisztens állapotban a pillanatkép készítésének időpontjában.
6. A szabályzat létrehozásához válassza az **OK** gombot.


### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

Most ezt a lépést kihagyhatja. A **üzembe helyezésének tervezése** legördülő listában kattintson a **Igen, elvégeztem**.



### <a name="enable-replication"></a>A replikáció engedélyezése

Ellenőrizze, hogy végrehajtotta az összes feladat [1. lépés: készítse elő a gép](#step-1-prepare-azure-stack-vms). Engedélyeznie kell a replikációt a következő:

1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa**válassza **fizikai gépek**.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.
5. A **cél**, válassza ki az előfizetést és az erőforráscsoport, amelyben szeretné létrehozni a virtuális gépek a feladatátvételt követően. Válassza ki a feladatátvételi virtuális gépekre használni kívánt üzemi modellhez.
6. Válassza ki az Azure storage-fiók, amelyben a replikált adatokat tárolni szeretné.
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza ki **beállítás később** Ha azt szeretné kiválasztani a külön az egyes gépek az Azure-hálózatot.
9. A **fizikai gépek**, kattintson a **+ fizikai gép**. Adja meg az IP-címe minden gép, és az operációs rendszer replikálni kívánt nevét.

    - A gép belső IP-címet használja.
    - Ha a nyilvános IP-cím replikálás nem a várt módon működik a előfordulhat, hogy adja meg.

10. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.
11. A **replikációs beállítások** > **replikációs beállítások konfigurálása**, ellenőrizze, hogy a megfelelő replikációs szabályzat van kiválasztva.
12. Kattintson a **Replikáció engedélyezése** elemre.
13. Nyomon követheti a a **védelem engedélyezése** feladat látható **beállítások** > **feladatok** > **Site Recovery-feladatok**. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

> [!NOTE]
> Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.

> 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.

> A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6. lépés: Futtassa a vészhelyreállítási próba végrehajtása

Feladatátvételi teszt futtatásakor győződjön meg arról, hogy minden a várt módon működik, az Azure-bA. Ez a feladatátvétel nem befolyásolják az éles környezetben.

### <a name="verify-machine-properties"></a>Ellenőrizze a gép tulajdonságai

Ahhoz, hogy futtatni egy feladatátvételi tesztet, ellenőrizze a gép tulajdonságait, és győződjön meg arról, hogy megfelelnek-e [Azure-követelmények](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Megtekintheti, és módosítsa a következő tulajdonságokat:

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **számítás és hálózat**, szükség szerint módosítsa a beállításokat.

    - Módosíthatja az Azure virtuális gép neve, az erőforráscsoport, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és a felügyelt lemez beállításait.
    - Is megtekintheti és módosíthatja a hálózati beállításokat. Ezek közé tartozik a hálózatot/alhálózatot, amelyhez az Azure virtuális gép feladatátvételi és a virtuális géphez rendelendő IP-cím után csatlakozik.
1. A **lemezek**, az operációs rendszer és az adatlemezek kapcsolatos információk megtekintéséhez a virtuális gépen.
   

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel feldolgozza az adatokat a megadott helyreállítási pont használatával:
    - **Legutóbb feldolgozott**: Site Recovery által feldolgozott, a gép feladatátvételt hajt végre a legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legutóbbi alkalmazáskonzisztens**. A gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontot.
    - **Egyéni**. Válassza ki a feladatátvételhez használt helyreállítási pontot.

3. Egy Azure virtuális gép létrehozása a feldolgozott adatok használatával.
4. Feladatátvételi teszt automatikusan távolíthatja el a részletezés során létrehozott Azure virtuális gépek.

Feladatátvételi teszt futtatása egy virtuális Gépet a következőképpen:

1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gép > **+Feladatátvételi teszt** ikonra.
2. Ebben a bemutatóban használandó választjuk ki a **legutóbb feldolgozott** helyreállítási pontot. 
3. A **feladatátvételi teszt**, jelölje ki a cél Azure-hálózatot.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra.
5. Nyomon követheti a virtuális Gépet a tulajdonságainak megnyitásához kattintva. Vagy kattintson a **feladatátvételi teszt** feladat látható *a tároló neve* > **beállítások** > **feladatok**  > **Site Recovery-feladatok**.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép mérete megfelelő, a megfelelő hálózathoz, a futtatását.
7. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban. [További információk](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **megjegyzések**, mentse a feladatátvételi tesztet megfigyeléseket.

## <a name="fail-over-and-fail-back"></a>Feladatátadás és feladatátvétel

Miután replikációs beállítását, és a egy próbát, győződjön meg arról, hogy minden jól működik, akkor átveheti gépek az Azure-bA szükség szerint.

Ha azt szeretné, majd a feladatátvételt követően csatlakozni az Azure-ban a gép feladatátvétel futtatása előtt [Felkészülés való kapcsolódásra](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) megkezdése előtt.

Ezután futtassa a következő feladatátvétel:


1. A **beállítások** > **replikált elemek**, kattintson a gépre > **feladatátvételi**.
2. Válassza ki a használni kívánt helyreállítási pontot.
3. A **feladatátvételi teszt**, jelölje ki a cél Azure-hálózatot.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. Ezzel a beállítással a Site Recovery megkísérli a forrásgép leállítása a feladatátvétel indítása előtt. Feladatátvétel azonban továbbra is, még akkor is, ha a Leállítás meghiúsul. 
5. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ha felkészült a feladatátvételt követően csatlakozni, ellenőrizze, hogy a virtuális gép a megfelelő méretet, a megfelelő hálózathoz csatlakozik, és futtatja.
7. Miután ellenőrizte a virtuális Gépet, kattintson a **véglegesítése** a feladatátvétel befejezéséhez. Ez törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt: feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.


### <a name="fail-back-to-azure-stack"></a>A feladat-visszavételhez az Azure Stack

Ha elsődleges hely újra működik, visszaadhatja a Azure-ról Azure Stack. Ehhez meg kell töltse le az Azure virtuális gép VHD-t, és töltse fel az Azure Stack.

1. Az Azure virtuális gép leállítása, hogy a virtuális merevlemez tölthető le. 
2. Indítsa el a virtuális merevlemez letöltését, telepítse a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Keresse meg a virtuális gép az Azure Portalon (a virtuális gép nevének használatával).
4. A **lemezek**, kattintson a lemez nevére és beállításainak összegyűjtése.

    - Tegyük fel, a virtuális merevlemez URI ebben a tesztben használt: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd bonthatók parancs a következő bemeneti paramétereket, a virtuális merevlemez letöltésére.
        - Tárfiók: 502055westcentralus
        - Tároló: wahv9b8d2ceb284fb59287
        - Virtuális merevlemez neve: másolt-3676553984.vhd

5. Most az Azure Storage Explorer használatával töltse le a VHD-t.
6. A virtuális merevlemez feltöltése az Azure Stackhez [ezeket a lépéseket](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. Az a meglévő virtuális Gépet vagy új virtuális Gépet, csatlakoztassa a feltöltött virtuális merevlemezeket.
8. Ellenőrizze, hogy helyesen szerepel-e az operációsrendszer-lemez, és indítsa el a virtuális Gépet.


Ezen a ponton a feladat-visszavétel számára befejeződött.


## <a name="conclusion"></a>Összegzés

Ebben a cikkben a Microsoft Azure-felhőbe replikált Azure Stack virtuális gépeket. A helyen replikációval ütköztünk vészhelyreállítási próbát, hogy feladatátvétel az Azure-bA működött a várt módon. A cikk a teljes feladatátvétel futtatása az Azure-ba, és vissza az Azure Stackhez sikertelen lépéseket is tartalmazzák.

## <a name="next-steps"></a>További lépések

Visszavétel után a virtuális gép ismételt védelme, és indítsa el újra szeretne erről, ismételje meg a lépéseket, ez a cikk az Azure-ba történő azt.

