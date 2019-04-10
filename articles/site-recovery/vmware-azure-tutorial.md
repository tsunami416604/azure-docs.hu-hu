---
title: Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez az Azure Site Recovery szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recoveryvel.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 4/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9e8f450825b7b4ad0402b8976d68bc23c18ce855
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357880"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Ez a cikk bemutatja, hogyan a helyszíni VMware virtuális gépek Azure-bA vész-helyreállítási replikálásának engedélyezéséhez a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Ez az a harmadik oktatóanyag egy sorozat, amely bemutatja, hogyan állítható be vészhelyreállítást az Azure-bA a helyszíni VMware virtuális gépekhez. Az előző oktatóanyagban azt [készíteni a helyszíni VMware-környezetről](vmware-azure-tutorial-prepare-on-premises.md) vész-helyreállítási az Azure-bA.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a forrás replikálási beállításai, és a egy a helyszíni Site Recovery konfigurációs kiszolgálónak.
> * A replikációs cél beállítások megadása.
> * Hozzon létre replikációs szabályzatot.
> * VMware virtuális gép replikációjának engedélyezéséhez.

> [!NOTE]
> Az oktatóanyagok bemutatják a legegyszerűbb telepítési út esetén. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a cikk az útmutató szakaszban a Site Recovery a tartalom.

## <a name="before-you-start"></a>Előkészületek

Végezze el az előző oktatóanyagok:
1. Győződjön meg arról, hogy [beállítása az Azure](tutorial-prepare-azure.md) a helyszíni VMware-vészhelyreállításhoz az Azure-bA.
2. Hajtsa végre a [ezeket a lépéseket](vmware-azure-tutorial-prepare-on-premises.md) , hogy a helyszíni VMware-környezet előkészítése a vészhelyreállítás az Azure-bA.
3. Ebben az oktatóanyagban bemutatjuk, hogyan replikálható egyetlen virtuális Gépet. Ha több VMware virtuális gépeket helyezi üzembe kell használnia a [Deployment Planner eszköz](https://aka.ms/asr-deployment-planner). [További információ](site-recovery-deployment-planner.md) az eszközről.
4. Ez az oktatóanyag számos lehetőséget szeretne előtérként érdemes használja:
    - Az oktatóanyag egy OVA sablont használ, a konfigurációs kiszolgáló VMware virtuális gép létrehozásához. Ha nem ezt teszi valamilyen okból, hajtsa végre a [ezek az utasítások](physical-manage-configuration-server.md) manuális beállítása a konfigurációs kiszolgáló.
    - Ebben az oktatóanyagban a Site Recovery automatikusan letölti és telepíti a MySQL és a konfigurációs kiszolgáló. Ha szeretné, akkor állíthatja, manuálisan helyette. [További információk](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.



## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezetében szüksége van egy egyetlen, magas rendelkezésre állású helyszíni gépre ezeket a helyszíni Site Recovery-összetevők futtatásához:

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként üzemel. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, és elküldi azokat a gyorsítótárfiókot, az Azure-ban. A folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatás ügynökének szeretne replikálni, virtuális gépeken, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- **Fő célkiszolgáló**: A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.


Ezek az összetevők mindegyikét egyetlen helyszíni gépeken, az úgynevezett együtt települnek a *konfigurációs kiszolgáló*. Alapértelmezés szerint a VMware-vészhelyreállításhoz beállítottuk a konfigurációs kiszolgáló magas rendelkezésre állású VMware virtuális gépként. Ehhez töltse le egy előkészített Open Virtualization alkalmazás (OVA) sablont, és importálja a VMware-be a virtuális gép létrehozásához. 

- A konfigurációs kiszolgáló legújabb verzióját a portálon érhető el. Emellett letöltheti közvetlenül a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Ha valamilyen okból az OVA sablon nem használható egy virtuális gép beállításához, kövesse az [ezek az utasítások](physical-manage-configuration-server.md) manuális beállítása a konfigurációs kiszolgálón.
- Az OVF-sablon a megadott licenc egy próbalicencre 180 napig érvényes. A virtuális gépen futó Windows aktiválni kell a szükséges licenccel. 


### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF-sablonját.



## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben


1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget a **Deploy OVF Template** (OVF-sablon telepítése) varázsló elindításához. 

     ![OVF-sablon](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A virtuális gép alapértelmezett beállításokkal való telepítéséhez a **Ready to complete** (Befejezésre kész) területen válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

   > [!TIP]
   > Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) beállítás jelölését. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha szeretne további hálózati Adaptereket adhat hozzá a konfigurációs kiszolgálót, adja hozzá ahhoz regisztrálja a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Válassza a **Next** > **Finish** (Tovább > Befejezés) elemet. Ezután kattintson az **OK** gombra.


## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

Miután a konfigurációs kiszolgáló be van állítva, akkor regisztrálja a tárolóban.

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával néhány másodpercen belül elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.


### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

Befejezés beállítását, és a konfigurációs kiszolgáló regisztrálása. 


1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza **kapcsolat beállítása**. A legördülő menük először válassza ki a felderítés és leküldéses forrásgépek futó mobilitási szolgáltatás telepítéséhez használja a beépített folyamatkiszolgáló hálózati Adaptert, és válassza ki a hálózati Adaptert, amely a konfigurációs kiszolgáló használ a kapcsolat az Azure-ral. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás konfigurálását követően nem módosítható.
2. A **Helyreállítási tár kiválasztása** területen válassza ki az Azure-előfizetést, valamint a megfelelő erőforráscsoportot és tárolót.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez. Ha a MySQL-t az elérési útra helyezte, ezt a lépést a rendszer kihagyja.
4. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt felhasználói hitelesítő adatokat. Győződjön meg a felhasználónév és a jelszó helyességéről, és a védelmük érdekében arról is, hogy a virtuális gép Rendszergazdák csoportjába tartoznak. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.
8. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás virtuális gépekre való automatikus telepítéséhez használni kívánt felhasználónevet és jelszót, ha a replikáció engedélyezve van.
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
9. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
10. A regisztráció befejezését követően ellenőrizze az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepelnek-e a tároló **Forrás** lapján. Ezután válassza az **OK** gombot a célbeállítások konfigurálásához.


A konfigurációs kiszolgáló regisztrálása után a Site Recovery a VMware-kiszolgálókhoz a megadott beállításokkal csatlakozik, és felderíti a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemet.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget. Válassza ki a használni kívánt Azure-előfizetést. Resource Manager-modellt használunk.
2. A Site Recovery ellenőrzi, hogy egy vagy több virtuális hálózatot. Ezeknek már léteznie kell, amikor a jelen oktatóanyag-sorozat [első oktatóanyagában](tutorial-prepare-azure.md) beállítja az Azure-összetevőket.

   ![Cél lap](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és válassza a **Minden erőforrás** elemet.
2. Válassza ki a Recovery Services helyreállítási tárat (ebben az oktatóanyagban ez a **ContosoVMVault**).
3. A replikációs szabályzat létrehozásához válassza a **Site Recovery-infrastruktúra** > **Replikációs szabályzatok** > **+Replikációs szabályzat** elemet.
4. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét. Itt a **VMwareRepPolicy** nevet használjuk.
5. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A jelen oktatóanyagban 72 órát adunk meg. A replikált virtuális gépek ezen a megőrzési időtartamon belül bármikor helyreállíthatók.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításban adhatja meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket. Az alapértelmezett 60 percet használjuk. A szabályzat létrehozásához válassza az **OK** gombot.

   ![Replikációs házirend létrehozása](./media/vmware-azure-tutorial/replication-policy.png)

- A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval.
- Alapértelmezés szerint a rendszer a feladat-visszavételhez is automatikusan létrehoz egy megfelelő szabályzatot. Ha például a replikációs szabályzat a **rep-policy**, a feladat-visszavételi szabályzat a **rep-policy-failback** lesz. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Virtuális gépek replikálásának engedélyezése a következőképpen:

1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
1. A **Forrás** mezőben válassza a **Helyszíni** lehetőséget, majd a **Forrás helye** mezőben válassza ki a konfigurációs kiszolgálót.
1. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
1. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet vagy az azt felügyelő vCenter-kiszolgálót.
1. Válassza ki a folyamatkiszolgálót (alapértelmezés szerint telepítve van a konfigurációs kiszolgáló virtuális gépén). Ezután kattintson az **OK** gombra.
1. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. A Resource Manager-alapú üzemi modellt használjuk. 
1. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
1. Ha a hálózati beállítást minden olyan virtuális gépre alkalmazni szeretné, amelyen engedélyezte a replikációt, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
1. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra. Ha nem tudja megtekintése/kiválasztani bármely adott virtuális gép [további](https://aka.ms/doc-plugin-VM-not-showing) a probléma megoldása.
1. A **Tulajdonságok** > **Tulajdonságok konfigurálása** mezőben válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.
1. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva.
1. Válassza ki a **Replikáció engedélyezése** elemet. Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
1. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
1. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
1. A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.

## <a name="next-steps"></a>További lépések
Replikáció engedélyezése után egy próbát győződjön meg arról, hogy minden a várt módon működik.
> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
