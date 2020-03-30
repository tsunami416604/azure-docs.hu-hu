---
title: Az Azure Stack virtuális gépek replikálása az Azure-ba az Azure Site Recovery használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be a vészhelyreállítást az Azure For Azure Stack virtuális gépekhez az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 15cd729063545914f791de39a075af9084f72bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426571"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Azure Stack rendszerű virtuális gépek replikálása az Azure-ba

Ez a cikk bemutatja, hogyan állíthatja be a vészhelyreállítási Azure Stack virtuális gépeket az Azure-ba az [Azure Site Recovery szolgáltatás](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)használatával.

A Site Recovery hozzájárul az üzletmenet folytonosságára és a vészhelyreállításra (BCDR) vonatkozó stratégiához. A szolgáltatás biztosítja, hogy a virtuális gép számítási feladatai továbbra is elérhetőek maradnak, ha várható és váratlan kimaradások fordulnak elő.

- A Site Recovery vezényli és kezeli a virtuális gépek azure-tárolóba történő replikációját.
- Ha kimaradás történik az elsődleges helyen, a Site Recovery használatával adja át a feladatát az Azure-ba.
- Feladatátvételkor az Azure virtuális gépek a tárolt virtuálisgép-adatokból jönnek létre, és a felhasználók továbbra is hozzáférhetnek az ilyen Azure virtuális gépeken futó számítási feladatokhoz.
- Ha minden újra működik, visszaveheti az Azure-beli virtuális gépeket az elsődleges helyre, és újra replikálhatja az Azure Storage-ba.


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * **1. lépés: Készítse elő az Azure stack virtuális gépeket a replikációhoz.** Ellenőrizze, hogy a virtuális gépek megfelelnek-e a hely-helyreállítási követelményeknek, és előkészíti a site recovery mobilitási szolgáltatás telepítését. Ez a szolgáltatás minden replikálni kívánt virtuális gépre telepítve van.
> * **2. lépés: Állítsa be a Helyreállítási szolgáltatások tároló**. Állítson be egy tárolót a Site Recovery számára, és adja meg, hogy mit szeretne replikálni. A Site Recovery összetevői és műveletek konfigurálása és kezelése a tárolóban van konfigurálva és kezelve.
> * **3. lépés: Állítsa be a forrásreplikációs környezetet.** Site Recovery konfigurációs kiszolgáló beállítása. A konfigurációs kiszolgáló egyetlen Azure Stack virtuális gép, amely a Site Recovery által szükséges összes összetevőt futtatja. Miután beállította a konfigurációs kiszolgálót, regisztrálja azt a tárolóban.
> * **4. lépés: Állítsa be a célreplikációs környezetet.** Válassza ki az Azure-fiókot, valamint a használni kívánt Azure-tárfiókot és hálózatot. A replikáció során a virtuális gép adatait másolja az Azure storage.During replication, VM data is copied to Azure storage. Feladatátvétel után az Azure-beli virtuális gépek csatlakoznak a megadott hálózathoz.
> * **5. lépés: Replikáció engedélyezése**. Konfigurálja a replikációs beállításokat, és engedélyezze a virtuális gépek replikációját. A mobilitási szolgáltatás a virtuális gépre lesz telepítve, ha a replikáció engedélyezve van. A Site Recovery végrehajtja a virtuális gép kezdeti replikációját, majd megkezdődik a folyamatos replikáció.
> * **6. lépés: A vész-helyreállítási gyakorlat futtatása:** A replikáció futása után ellenőrzi, hogy a feladatátvétel a várt módon fog működni egy fúró futtatásával. A gyakorlat elindításához futtasson egy teszt feladatátvételt a Site Recovery alkalmazásban. A teszt feladatátvétel nincs hatással az éles környezetben.

Ezekkel a lépésekkel teljes, majd futtathatja a teljes feladatátvétel az Azure-ba, ahogy és amikor szükséges.

## <a name="architecture"></a>Architektúra

![Architektúra](./media/azure-stack-site-recovery/architecture.png)

**Helyen** | **Összetevő** |**Részletek**
--- | --- | ---
**Konfigurációs kiszolgáló** | Egyetlen Azure Stack virtuális gépen fut. | Minden előfizetésben beállít egy konfigurációs kiszolgáló virtuális gép. Ez a virtuális gép a következő Site Recovery összetevőket futtatja:<br/><br/> - Konfigurációs kiszolgáló: Koordinálja a helyszíni és az Azure közötti kommunikációt, és kezeli az adatreplikációt. - Process server: Működik, mint a replikációs átjáró. Replikációs adatokat fogad, gyorsítótárazással, tömörítéssel és titkosítással optimalizál; és elküldi az Azure storage-ba.<br/><br/> Ha a replikálni kívánt virtuális gépek meghaladják az alábbi korlátokat, beállíthat egy külön önálló folyamatkiszolgálót. [További információ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Mobilitási szolgáltatás** | Minden replikáni kívánt virtuális gépre telepítve. | Ebben a cikkben leírt lépésekben előkészítünk egy fiókot, hogy a mobilitási szolgáltatás automatikusan települjön a virtuális gépre, ha a replikáció engedélyezve van. Ha nem szeretné automatikusan telepíteni a szolgáltatást, számos más módszert is használhat. [További információ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Az Azure-ban szüksége van egy Recovery Services-tárolóra, egy tárfiókra és egy virtuális hálózatra. |  A replikált adatok a tárfiókban tárolódnak. Az Azure virtuális gépek kerülnek az Azure-hálózathoz feladatátvétel esetén. 


A replikáció a következőképpen működik:

1. A tárolóban megadhatja a replikációforrást és a célt, beállíthatja a konfigurációs kiszolgálót, létrehozhat egy replikációs házirendet, és engedélyezheti a replikációt.
2. A Mobilitás szolgáltatás telepítve van a számítógépen (ha már használt leküldéses telepítést), és a gépek a replikációs házirendnek megfelelően kezdik meg a replikációt.
3. A kiszolgálóadatok kezdeti másolata replikálódik az Azure storage-ba.
4. A kezdeti replikáció befejezése után megkezdődik az Azure-ba történő különbözeti módosítások replikációja. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
5. A konfigurációs kiszolgáló vezényli a replikációkezelést az Azure-ral (HTTPS 443 kimenő port).
6. A folyamatkiszolgáló adatokat fogad a forrásgépekről, optimalizálja és titkosítja azokat, és elküldi azokat az Azure storage-ba (kimenő 443-as port).
7. A replikált gépek kommunikálnak a konfigurációs kiszolgálóval (a HTTPS 443 port bejövő, replikációkezeléshez. A gépek replikációs adatokat küldenek a folyamatkiszolgálónak (a HTTPS 9443 port bejövő - módosítható).
8. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute nyilvános társviszony-létesítési szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

## <a name="prerequisites"></a>Előfeltételek

A forgatókönyv beállításához az alábbiakra van szükség.

**Követelmény** | **Részletek**
--- | ---
**Azure-előfizetési fiók** | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)
**Azure-fiók engedélyei** | A használt Azure-fiókhoz engedélyek szükségesek:<br/><br/> - Hozzon létre egy helyreállítási szolgáltatás tároló<br/><br/> - Hozzon létre egy virtuális gép az erőforrás-csoport és a virtuális hálózat használata a forgatókönyv<br/><br/> - Írjon a megadott tárfiókba<br/><br/> Vegye figyelembe:<br/><br/> -Ha létrehoz egy fiókot, akkor az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> - Ha meglévő előfizetést használ, és nem Ön a rendszergazda, akkor a rendszergazdával együtt kell dolgoznia a tulajdonosi vagy közreműködői engedélyek hozzárendeléséhez.<br/><br/> - Ha részletesebb engedélyekre van szüksége, tekintse át [ezt a cikket](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Azure Stack virtuális gép** | Szüksége van egy Azure Stack virtuális gép a bérlői előfizetésben, amely a Site Recovery konfigurációs kiszolgálóként lesz telepítve. 


### <a name="prerequisites-for-the-configuration-server"></a>A konfigurációs kiszolgáló előfeltételei

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>1. lépés: Az Azure Stack virtuális gépek előkészítése

### <a name="verify-the-operating-system"></a>Az operációs rendszer ellenőrzése

Győződjön meg arról, hogy a virtuális gépek a táblázatban összegzett operációs rendszerek egyikét futtatják.


**Operációs rendszer** | **Részletek**
--- | ---
**64 bites Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (SP1 rendszerből)
**CentOS** | 5,2–5,11, 6,1–6,9, 7,0–7,3
**Ubuntu** | 14.04 LTS kiszolgáló, 16.04 LTS szerver. [A támogatott kernelek áttekintése](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Felkészülés a mobilitási szolgáltatás telepítésére

Minden replikálni kívánt virtuális gépnek telepítve kell lennie a Mobilitás szolgáltatásnak. Annak érdekében, hogy a folyamatkiszolgáló automatikusan telepíthesse a szolgáltatást a virtuális gépre, ha a replikáció engedélyezve van, ellenőrizze a virtuális gép beállításait.

#### <a name="windows-machines"></a>Windows-gépek

- Hálózati kapcsolatra van szükség a virtuális gép, amelyen engedélyezni szeretné a replikációt, és a folyamatkiszolgálót futtató gép (alapértelmezés szerint ez a konfigurációs kiszolgáló virtuális gépe).
- Rendszergazdai jogosultságokkal (tartomány vagy helyi) rendelkező fiókra van szükség azon a számítógépen, amelyhez engedélyezi a replikációt.
    - Ezt a fiókot a Site Recovery beállításakor adja meg. Ezután a folyamatkiszolgáló ezt a fiókot használja a Mobilitás szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot a Site Recovery csak a leküldéses telepítéshez és a Mobilitás szolgáltatás frissítéséhez fogja használni.
    - Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés vezérlését a virtuális gépen:
        - A beállításjegyzékben hozzon létre egy Duplaszó értéket **LocalAccountTokenFilterPolicy** HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System területen.
        - Az értéket állítsa be 1-re.
        - A parancssorba való beíráshoz írja be a következőt: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- A replikálni kívánt virtuális gép Windows tűzfalán engedélyezze a fájl- és nyomtatómegosztást, valamint a WMI-t.
    - Ehhez futtassa **a wf.msc** fájl futtatását a Windows tűzfal konzol megnyitásához. Kattintson a jobb gombbal **a Bejövő szabályok** > **új szabályparancsára.** Válassza **az Előre definiált**lehetőséget, majd a listából a **Fájl- és nyomtatómegosztás lehetőséget.** Fejezze be a varázslót, és jelölje be a kapcsolat engedélyezését > **Befejezés gombra**.
    - Tartományi számítógépek esetén ehhez főházirend-kiszolgálót használhat.

    
#### <a name="linux-machines"></a>Linux gépek

- Gondoskodjon róla, hogy legyen hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló közt.
- Azon a számítógépen, amelyen engedélyezi a replikációt, olyan fiókra van szüksége, amely a forrás Linux-kiszolgálón root felhasználó:
    - Ezt a fiókot a Site Recovery beállításakor adja meg. Ezután a folyamatkiszolgáló ezt a fiókot használja a Mobilitás szolgáltatás telepítéséhez, ha a replikáció engedélyezve van.
    - Ezt a fiókot a Site Recovery csak a leküldéses telepítéshez és a Mobilitás szolgáltatás frissítéséhez fogja használni.
- Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
- Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
- Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
- Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
    1. Ehhez jelentkezzen be gyökérként.
    2. Keresse meg a **PasswordAuthentication**, az /etc/ssh/sshd_config fájlban kezdődő sort. Állítsa vissza a sort, és módosítsa az értéket **yes** értékre.
    3. Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

        ![Linux mobilitási szolgáltatás](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Indítsa újra az sshd szolgáltatást.


### <a name="note-the-vm-private-ip-address"></a>Megjegyzés: A virtuális gép privát IP-címe

A replikálni kívánt gépekhez keresse meg az IP-címet:

1. Az Azure Stack Portal, kattintson a virtuális gép.
2. Kattintson az **Erőforrás** menü **Hálózati adapterek parancsára.**
3. Jegyezze fel a privát IP-címet.

    ![Privát IP-cím](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2. lépés: Tároló létrehozása és replikációs cél kiválasztása

1. Az Azure Portalon válassza az > **Erőforrás-kezelő eszközök** > **biztonsági mentése és a Webhely-helyreállítás** **létrehozása**lehetőséget.
2. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. 
3. Az **Erőforráscsoportban**hozzon létre vagy jelöljön ki egy erőforráscsoportot. **ContosoRG-t**használunk.
4. A **Hely mezőbe**írja be az Azure-régiót. a **Nyugat-Európa** beállítást használjuk.
5. Ha gyorsan el szeretné érni a tárolót az irányítópultról, válassza a **Rögzítés az irányítópult** > **létrehozása lehetőséget.**

   ![Új tároló létrehozása](./media/azure-stack-site-recovery/new-vault-settings.png)

   Az új tároló megjelenik az **Irányítópult minden** > **erőforrás**és a fő Helyreállítási **szolgáltatások tárolók** lapon.

### <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** > adja meg a tároló nevét. **A ContosoVMVault-ot**használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelem cél:** > **Hol találhatók a gépek,** válassza **a Helyszíni**lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizált gépek (Virtualized)** területen válassza **a Nem virtualizált/egyéb**lehetőséget. Ezután kattintson az **OK** gombra.

    ![Védelmi cél](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3. lépés: A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgálógép, regisztrálja azt a tárolóban, és felderíteni gépek replikálni kívánt.

1. Kattintson **az Infrastruktúraforrás** > **előkészítése gombra.**
2. A **Forrás előkészítése** ablakban kattintson a **+Konfigurációs kiszolgáló** gombra.

    ![A forrás beállítása](./media/azure-stack-site-recovery/plus-config-srv.png)

3. A **Kiszolgáló hozzáadása területen**ellenőrizze, hogy a **konfigurációs kiszolgáló** kiszolgáló **típusú-e.**
5. Töltse le a Site Recovery egyesített telepítési telepítőfájlját.
6. Töltse le a tároló regisztrációs kulcsát. Az Egyesített telepítés futtatásakor a regisztrációs kulcsra van szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Az Azure Site Recovery egységes telepítésének futtatása

A konfigurációs kiszolgáló telepítéséhez és regisztrálásához végezze el az RDP-kapcsolatot a konfigurációs kiszolgálóhoz használni kívánt virtuális géptel, és futtassa az egyesített telepítést.

Mielőtt elkezdené, győződjön meg arról, hogy az óra szinkronizálva van [egy időkiszolgálóval](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) a virtuális gépen, mielőtt elkezdené. A telepítés sikertelen, ha az idő több mint öt perccel eltér a helyi időnek.

Most telepítse a konfigurációs kiszolgálót:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló a parancssorból is telepíthető. [További információ](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgáló** > ***kiszolgálójának neve*** > **Frissítési kiszolgáló**lehetőséget.

## <a name="step-4-set-up-the-target-environment"></a>4. lépés: A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Az **infrastruktúra-cél** > **Target**előkészítése területen válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a céltelepítési modellt.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. Ha nem találja őket, létre kell hoznia legalább egy tárfiókot és virtuális hálózatot a varázsló befejezéséhez.


## <a name="step-5-enable-replication"></a>5. lépés: A replikáció engedélyezése

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Kattintson **az Infrastruktúra-replikáció** > beállításainak előkészítése**gombra.**
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO).
    - A replikált adatok helyreállítási pontjai a beállított időnek megfelelően jönnek létre.
    - Ez a beállítás nincs hatással a replikációra, amely folyamatos. Egyszerűen riasztást ad ki, ha a küszöbértéket helyreállítási pont létrehozása nélkül éri el.
4. A **helyreállítási pont megőrzése**területen adja meg, hogy az egyes helyreállítási pontok mennyi ideig maradjanak meg. A replikált virtuális gépek a megadott időablak bármely pontjára visszavezethetők.
5. Az **alkalmazáskonzisztens pillanatkép-gyakoriságban**adja meg, hogy milyen gyakran jönnek létre alkalmazáskonzisztens pillanatképek.

    - Az alkalmazáskonzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időbeli pillanatképe.
    - Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gép alkalmazások konzisztens állapotban, amikor a pillanatkép készül.
6. A szabályzat létrehozásához válassza az **OK** gombot.


### <a name="confirm-deployment-planning"></a>Az üzembe helyezés megtervezésének megerősítése

Ezt a lépést most azonnal kihagyhatja. A **Telepítéstervezés** legördülő listában kattintson az **Igen, én csináltam**gombra.



### <a name="enable-replication"></a>A replikáció engedélyezése

Győződjön meg arról, hogy az [1.](#step-1-prepare-azure-stack-vms) Ezután engedélyezze a replikációt az alábbiak szerint:

1. Válassza az**Alkalmazásforrás** >  **replikálása**lehetőséget.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Géptípus**csoportban válassza **a Fizikai gépek**lehetőséget.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezt követően kattintson az **OK** gombra.
5. A **Cél ban**válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátvétel után létre szeretné hozni a virtuális gépeket. Válassza ki a sikertelen virtuális gépekhez használni kívánt központi telepítési modellt.
6. Válassza ki azt az Azure storage-fiókot, amelyben a replikált adatokat tárolni szeretné.
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Válassza **a Beállítás most a kijelölt gépekhez** lehetőséget, ha a hálózati beállítást minden kiválasztott gépre alkalmazni szeretné a védelemhez. Válassza **a Később konfigurálása** lehetőséget, ha az Azure-hálózatot külön szeretné kiválasztani az egyes gépekhez.
9. A **Fizikai gépek csoportban**kattintson a **+Fizikai gép gombra.** Adja meg a replikálni kívánt gépek nevét, IP-címét és operációsrendszer-típusát.

    - Használja a számítógép belső IP-címét.
    - Ha megadja a nyilvános IP-címet, előfordulhat, hogy a replikáció nem a várt módon működik.

10. A **Tulajdonságok** > **konfigurálása párbeszédpanelen**válassza ki azt a fiókot, amelyet a folyamatkiszolgáló a Mobility Service automatikus telepítéséhez használni fog a számítógépen.
11. A **Replikációs beállítások** > **konfigurálása**párbeszédpanelen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva.
12. Kattintson **a Replikáció engedélyezése gombra.**
13. A Védelem **engedélyezése** feladat folyamatának nyomon követése a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai ban.** A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

> [!NOTE]
> Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
> 
> 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
> 
> A hozzáadott virtuális gépek figyeléséhez ellenőrizze a virtuális gépek utolsó felderített idejét a **konfigurációs kiszolgálók** > **utolsó kapcsolata a területen.** Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6. lépés: Vész-helyreállítási gyakorlat futtatása

Egy teszt feladatátvételt futtat az Azure-ba, hogy megbizonyosodjon arról, hogy minden a várt módon működik. Ez a feladatátvétel nem befolyásolja az éles környezetben.

### <a name="verify-machine-properties"></a>A számítógép tulajdonságainak ellenőrzése

A teszt feladatátvétel futtatása előtt ellenőrizze a számítógép tulajdonságait, és győződjön meg arról, hogy azok megfelelnek az [Azure követelményeinek.](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements) A tulajdonságok at a következőképpen tekintheti meg és módosíthatja:

1. A **Védett elemek** területen kattintson a **Replikált elemek** > VM lehetőségre.
2. A **Replikált elemek** ablaktáblában szerepel a virtuális gép információinak összegzése, állapota és a legújabb elérhető helyreállítási pontok. Kattintson a **Tulajdonságok** lehetőségre a további részletek megtekintéséhez.
3. A **Számítási és hálózati területen**szükség szerint módosítsa a beállításokat.

    - Módosíthatja az Azure virtuális gép nevét, erőforráscsoportot, célméretet, [rendelkezésre állási készletét](../virtual-machines/windows/tutorial-availability-sets.md)és a felügyelt lemez beállításait.
    - Megtekintheti és módosíthatja a hálózati beállításokat is. Ezek közé tartozik a hálózat/alhálózat, amelyhez az Azure virtuális gép a feladatátvétel után csatlakozik, és az IP-cím, amely hozzá lesz rendelve a virtuális géphez.
1. A **Lemezek**alkalmazásban tekintse meg a virtuális gép operációs rendszerével és adatlemezeivel kapcsolatos információkat.
   

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. A rendszer lefuttatja az előfeltételek ellenőrzését, hogy a feladatátvételhez szükséges feltételek biztosan teljesüljenek.
2. A feladatátvétel a megadott helyreállítási pont használatával dolgozza fel az adatokat:
    - **Legutóbbi feldolgozás:** A gép átadja a helyét helyreállítási hely által feldolgozott legújabb helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
    - **Legújabb alkalmazáskonzisztens:** A gép átadja a legutóbbi alkalmazáskonzisztens helyreállítási pontot.
    - **Egyéni**: Válassza ki a feladatátvételhez használt helyreállítási pontot.

3. Egy Azure virtuális gép jön létre a feldolgozott adatok használatával.
4. A tesztfeladat-átvétel automatikusan megtisztíthatja a fúró során létrehozott Azure virtuális gépeket.

Futtasson egy teszt feladatátvételt egy virtuális géphez az alábbiak szerint:

1. A**Replikált elemek** **beállításai** > párbeszédpanelen kattintson a virtuális gép > **+A feladatátvétel tesztelése**.
2. Ebben a forgatókönyvben a **Legújabb feldolgozott** helyreállítási pontot választjuk. 
3. A **Teszt feladatátvételi**területen válassza ki a cél Azure-hálózat.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra.
5. A virtuális gépre kattintva nyomon követheti a folyamatelőrehaladását a tulajdonságainak megnyitásához. Vagy kattintson a **Feladatátvételi** feladat tesztelése a tároló beállításai**Settings** > **feladat-helyreállítási** >**feladatok ban.** *vault name* > 
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ellenőrizze, hogy a virtuális gép a megfelelő méretű, csatlakozik a megfelelő hálózathoz, és fut.
7. Így már tudnia kell csatlakozni a replikált virtuális géphez az Azure-ban. [További információ](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. A feladatátvételi teszt során létrehozott Azure-beli virtuális gépek törléséhez kattintson a **Feladatátvételi teszt eltávolítása** elemre a virtuális gépen. A **Megjegyzések**alkalmazásban mentse a tesztfeladat-átvételhez kapcsolódó megfigyeléseket.

## <a name="fail-over-and-fail-back"></a>Feladatátadás és feladatátvétel

Miután beállította a replikációt, és futtatott egy fúrót, hogy megbizonyosodjon arról, hogy minden működik, szükség szerint átveheti a gépeket az Azure-ba.

A feladatátvétel futtatása előtt, ha szeretne csatlakozni a géphez az Azure-ban a feladatátvétel után, majd [előkészíti a csatlakozást,](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) mielőtt elkezdené.

Ezután futtasson feladatátvételt az alábbiak szerint:


1. A**Replikált elemek** **beállításai** > párbeszédpanelen kattintson a > **feladatátvételi**gépre.
2. Jelölje ki a használni kívánt helyreállítási pontot.
3. A **Teszt feladatátvételi**területen válassza ki a cél Azure-hálózat.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. Ezzel a beállítással a Site Recovery megpróbálja leállítani a forrásgépet a feladatátvétel megkezdése előtt. A feladatátvétel azonban akkor is folytatódik, ha a leállítás sikertelen. 
5. A feladatátvételi művelet elindításához kattintson az **OK** gombra. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
6. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Ha kész csatlakozni a feladatátvétel után, ellenőrizze, hogy a virtuális gép a megfelelő méretű, csatlakozik a megfelelő hálózathoz, és fut.
7. A virtuális gép ellenőrzése után kattintson a **Véglegesítés gombra** a feladatátvétel befejezéséhez. Ezzel törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> Ne szakítsa meg a folyamatban lévő feladatátvételt: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.


### <a name="fail-back-to-azure-stack"></a>Visszavétel az Azure Stacknek

Ha az elsődleges hely újra működik, az Azure-ból visszaléphet az Azure Stackbe. Ehhez le kell töltenie az Azure virtuális gép virtuális merevlemezét, és fel kell töltenie az Azure Stackbe.

1. Állítsa le az Azure virtuális gép, a virtuális merevlemez letölthető. 
2. A virtuális merevlemez letöltésének megkezdéséhez telepítse az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)t.
3. Keresse meg a virtuális gép az Azure Portalon (a virtuális gép nevét).
4. A **Lemezek csoportban**kattintson a lemez nevére, és gyűjtse össze a beállításokat.

    - Például a tesztben használt VHD https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd URI: a következő bemeneti paraméterek, amelyek a virtuális merevlemez letöltéséhez használt a következő bemeneti paraméterek lehívása érdekében.
        - Tárfiók: 502055westcentralus
        - Konténer: wahv9b8d2ceb284fb59287
        - VHD-név: másolt-3676553984.vhd

5. Most az Azure Storage Explorer segítségével töltse le a virtuális merevlemezt.
6. Töltse fel a virtuális merevlemezt az Azure Stackbe [ezekkel](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm)a lépésekkel.
7. A meglévő virtuális gép vagy új virtuális gép, csatolja a feltöltött virtuális gépek.
8. Ellenőrizze, hogy az operációs rendszer lemeze helyes,, és indítsa el a virtuális gép.


Ebben a szakaszban a feladat-visszavétel befejeződött.


## <a name="conclusion"></a>Összegzés

Ebben a cikkben replikáltuk az Azure Stack virtuális gépek et az Azure-ba. A replikáció a helyén, futtattuk a vész-helyreállítási gyakorlat, hogy megbizonyosodjon arról, feladatátvétel az Azure-ba működött a várt módon. A cikk is lépéseket tartalmaz egy teljes feladatátvétel azure-ba, és nem sikerült vissza az Azure Stack.

## <a name="next-steps"></a>További lépések

Miután nem sikerült vissza, újra megvédheti a virtuális gép, és indítsa el a replikálása az Azure-ba ismét Ehhez ismételje meg a ebben a cikkben leírt lépéseket.

