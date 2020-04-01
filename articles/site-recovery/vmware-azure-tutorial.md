---
title: VMware virtuális gép vész-helyreállítási az Azure-ban az Azure Site Recovery
description: Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recoveryvel.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238866"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Ez a cikk ismerteti, hogyan engedélyezheti a replikációt a helyszíni VMware virtuális gépek, a vész-helyreállítási azure-ba az [Azure-ba](site-recovery-overview.md) az Azure Site Recovery szolgáltatás használatával.

Ez a harmadik oktatóanyag egy sorozatban, amely bemutatja, hogyan állíthatja be a vészhelyreállítást az Azure-ban a helyszíni VMware virtuális gépek. Az előző oktatóanyagban [készítettük el a helyszíni VMware-környezetet az Azure-ba](vmware-azure-tutorial-prepare-on-premises.md) való vészhelyreállításhoz.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a forrásreplikáció beállításait és egy helyszíni site recovery konfigurációs kiszolgálót.
> * Állítsa be a replikációs célbeállításokat.
> * Hozzon létre replikációs szabályzatot.
> * VMware virtuális gép replikációjának engedélyezése.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a webhely-helyreállítási tartalomjegyzék Útmutató szakaszában található cikket.

## <a name="before-you-start"></a>Előkészületek

Töltse ki az előző oktatóanyagokat:
1. Győződjön meg arról, hogy beállította az [Azure-t](tutorial-prepare-azure.md) a helyszíni VMware vész-helyreállítási Azure-ba.
2. Kövesse [az alábbi lépéseket](vmware-azure-tutorial-prepare-on-premises.md) a helyszíni VMware üzembe helyezésének előkészítéséhez az Azure-ba történő vész-helyreállításhoz.
3. Ebben az oktatóanyagban megmutatjuk, hogyan replikálhatja egyetlen virtuális gép. Ha több VMware virtuális gépet telepít, a [Telepítéstervező eszközt](https://aka.ms/asr-deployment-planner)kell használnia. [További információ](site-recovery-deployment-planner.md) az eszközről.
4. Ez az oktatóanyag számos olyan lehetőséget használ, amelyet esetleg másképp szeretne megtenni:
    - Az oktatóanyag egy OVA sablont használ a VMware VM konfigurációs kiszolgáló létrehozásához. Ha valamilyen okból nem tudja ezt megtenni, kövesse [az alábbi utasításokat](physical-manage-configuration-server.md) a konfigurációs kiszolgáló manuális beállításához.
    - Ebben az oktatóanyagban a Site Recovery automatikusan letölti és telepíti a MySQL-t a konfigurációs kiszolgálóra. Ha szeretné, manuálisan is beállíthatja. [További információ](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelem cél:** > **Hol találhatók a gépek,** válassza **a Helyszíni**lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.



## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezetben egyetlen, magas rendelkezésre állású helyszíni gépre van szükség a helyszíni site recovery-összetevők üzemeltetéséhez:

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként működik. Replikációs adatokat fogad; gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja, és elküldi egy azure-beli gyorsítótár-tárfiókba. A folyamatkiszolgáló telepíti a replikálni kívánt virtuális gépeken a Mobility Service-ügynököt is, és automatikusan elvégzi a helyszíni VMware virtuális gépek felderítését.
- **Fő célkiszolgáló**: A fő célkiszolgáló kezeli a replikációs adatokat az Azure-ból történő feladat-visszavétel során.


Ezek az összetevők együtt vannak telepítve a *konfigurációs kiszolgálóként*ismert egyetlen helyszíni gépen. Alapértelmezés szerint a VMware vész-helyreállítási, mi létre a konfigurációs kiszolgáló, mint egy magas rendelkezésre állású VMware VM. Ehhez töltse le az előkészített Open Virtualization Application (OVA) sablont, és importálja a sablont a VMware-be a virtuális gép létrehozásához. 

- A konfigurációs kiszolgáló legújabb verziója elérhető a portálon. Azt is letöltheti közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver).
- Ha valamilyen okból nem tud egy OVA sablont használni a virtuális gép beállításához, kövesse [az alábbi utasításokat](physical-manage-configuration-server.md) a konfigurációs kiszolgáló manuális beállításához.
- Az OVF sablonnal ellátott licenc 180 napig érvényes értékelési licenc. A virtuális gépen futó Windowst aktiválni kell a szükséges licenccel. 


### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban nyissa **meg az Infrastruktúra** > forrás előkészítése**című**részt.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF-sablonját.



## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben


1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **Fájl** **menüOVF-sablon telepítése parancsára** kattintson az **OVF-sablon telepítése varázsló**elindításához. 

     ![OVF-sablon](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A virtuális gép alapértelmezett beállításokkal való telepítéséhez a **Ready to complete** (Befejezésre kész) területen válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

   > [!TIP]
   > Ha további hálózati adaptert szeretne hozzáadni, törölje **a bekapcsolás** > lehetőséget a telepítés**befejezése**után. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, adja hozzá, mielőtt regisztrálna a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután válassza a **Tovább**gombot.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Válassza a **Következő** > **befejezés lehetőséget.** Ezután kattintson az **OK** gombra.


## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

A konfigurációs kiszolgáló beállítása után regisztrálja azt a tárolóban.

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával néhány másodpercen belül elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután válassza a **Tovább**gombot.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót. Győződjön meg arról, hogy a szükséges [szerepkörök](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) hozzá vannak rendelve ehhez a felhasználóhoz.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.


### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

A konfigurációs kiszolgáló beállításának és regisztrálásának befejezése. A folytatás előtt győződjön meg arról, hogy a konfigurációs kiszolgáló sikeres beállításához minden [előfeltétel](vmware-azure-deploy-configuration-server.md#prerequisites) teljesül.


1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **Telepítési kapcsolat**lehetőséget. A legördülő menüből válassza ki a hálózati adaptert, amelyet a beépített folyamatkiszolgáló használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásgépeken, majd válassza ki azt a hálózati adaptert, amelyet a Configuration Server használ az Azure-ral való kapcsolathoz. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás nem módosítható a beállítás konfigurálása után.
2. A **Recovery Services-tároló kiválasztása alkalmazásban**válassza ki az Azure-előfizetést és a megfelelő erőforráscsoportot és -tárolót.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez. Ha a MySQL-t az elérési útra helyezte, ezt a lépést ki lehet hagyni. [További](vmware-azure-deploy-configuration-server.md#configure-settings) információ
4. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
5. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
6. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt felhasználói hitelesítő adatokat. Győződjön meg a felhasználónév és a jelszó helyességéről, és a védelmük érdekében arról is, hogy a virtuális gép Rendszergazdák csoportjába tartoznak. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.
7. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás virtuális gépekre való automatikus telepítéséhez használni kívánt felhasználónevet és jelszót, ha a replikáció engedélyezve van.
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
8. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
9. A regisztráció befejezése után nyissa meg az Azure portalt, és ellenőrizze, hogy a **konfigurációs**kiszolgáló és a VMware-kiszolgáló szerepel-e a Helyreállítási szolgáltatások > **tárolójában** > **a hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak kezelése szolgáltatásban.**


A konfigurációs kiszolgáló regisztrálása után a Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgáló** > ***kiszolgálójának neve*** > **Frissítési kiszolgáló**lehetőséget.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Válassza **az Infrastruktúra cél előkészítése** > **lehetőséget.** Válassza ki a használni kívánt Azure-előfizetést. Resource Manager-modellt használunk.
2. A Site Recovery ellenőrzi, hogy egy vagy több virtuális hálózattal rendelkezik-e. Ezeknek már léteznie kell, amikor a jelen oktatóanyag-sorozat [első oktatóanyagában](tutorial-prepare-azure.md) beállítja az Azure-összetevőket.

   ![Cél lap](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg és válassza a **Helyreállítási szolgáltatások tárolóit.**
2. Válassza ki a Recovery Services helyreállítási tárat (ebben az oktatóanyagban ez a **ContosoVMVault**).
3. Replikációs házirend létrehozásához válassza a **Hely-helyreállítási infrastruktúra** > **replikációs házirendjeit** > **+Replikációs házirend lehetőséget.**
4. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét. Itt a **VMwareRepPolicy** nevet használjuk.
5. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A jelen oktatóanyagban 72 órát adunk meg. A replikált virtuális gépek ezen a megőrzési időtartamon belül bármikor helyreállíthatók.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításban adhatja meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket. Az alapértelmezett 60 percet használjuk. A szabályzat létrehozásához válassza az **OK** gombot.

   ![Replikációs házirend létrehozása](./media/vmware-azure-tutorial/replication-policy.png)

- A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.
- Alapértelmezés szerint a rendszer a feladat-visszavételhez is automatikusan létrehoz egy megfelelő szabályzatot. Ha például a replikációs szabályzat a **rep-policy**, a feladat-visszavételi szabályzat a **rep-policy-failback** lesz. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

A virtuális gépek replikációjának engedélyezése az alábbiak szerint:

1. Válassza az**Alkalmazásforrás** >  **replikálása**lehetőséget.
2. A **Forrás** mezőben válassza a **Helyszíni** lehetőséget, majd a **Forrás helye** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet vagy az azt felügyelő vCenter-kiszolgálót.
5. Válassza ki a folyamatkiszolgálót (alapértelmezés szerint telepítve van a konfigurációs kiszolgáló virtuális gépén). Ezután kattintson az **OK** gombra. Az egyes folyamatkiszolgálók állapota az ajánlott korlátok és egyéb paraméterek szerint van feltüntetve. Válasszon egy kifogástalan folyamatkiszolgálót. Nem lehet [kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) folyamatkiszolgálót választani. Elháríthatja [és megoldhatja](vmware-physical-azure-troubleshoot-process-server.md) a hibákat, **vagy** beállíthat egy [kibővített folyamatkiszolgálót.](vmware-azure-set-up-process-server-scale.md)
6. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. A Resource Manager-alapú üzemi modellt használjuk. 
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Ha a hálózati beállítást minden olyan virtuális gépre alkalmazni szeretné, amelyen engedélyezte a replikációt, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza **a Később konfigurálása** lehetőséget az Azure-hálózat számítógépenkéntkiválasztásához.
9. A **Virtuális gépek** > válassza ki a**virtuális gépeket,** válassza ki a replikálni kívánt gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra. Ha nem tud megtekinteni/kiválasztani egy adott virtuális gépet, [további információ](https://aka.ms/doc-plugin-VM-not-showing) a probléma megoldásáról.
10. A **Tulajdonságok** > **konfigurálása párbeszédpanelen**válassza ki a folyamatkiszolgáló által a Mobility Service automatikus telepítéséhez használt fiókot a számítógépre.
11. A **Replikációs beállítások** > **konfigurálása**párbeszédpanelen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva.
12. Válassza ki a **Replikáció engedélyezése** elemet. Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
13. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai között követheti**nyomon. A **Véglegesítési védelem** feladat futtatása és a helyreállítási pont létrehozása befejeződött, a gép készen áll a feladatátvételre.
14. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
15. A hozzáadott virtuális gépek figyeléséhez ellenőrizze a virtuális gépek utolsó felderített idejét a **konfigurációs kiszolgálók** > **utolsó kapcsolata a területen.** Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.

## <a name="next-steps"></a>További lépések
A replikáció engedélyezése után futtasson egy fúrót, hogy minden a várt módon működjön.
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
