---
title: Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez az Azure Site Recovery szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recoveryvel.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 3/3/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5b3dca8bd5135581afc12303ed9fa8c532c56258
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438890"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.


Ebben az oktatóanyagban bemutatjuk, hogyan állíthatja be és engedélyezheti a VMware virtuális gépek Azure-ba történő replikálását az Azure Site Recovery használatával. Az oktatóanyagok bemutatják, hogy hogyan helyezheti üzembe a Site Recovery szolgáltatást alapvető beállításokkal. A legegyszerűbb utat mutatják be, és nem minden lehetőséget tüntetnek fel. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A replikációs forrás és cél megadása.
> * A replikációs forráskörnyezet (többek között a helyszíni Azure Site Recovery-összetevők), valamint a replikálási célkörnyezet beállítása.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, érdemes végrehajtania az alábbiakat:

- [Tekintse át az architektúrát](vmware-azure-architecture.md), hogy szerepel-e benne ez a vészhelyreállítási forgatókönyv.
- Ha szeretné részletesebben is megismerni a VMware virtuális gépek vészhelyreállításának beállítását, tekintse át és használja az alábbi forrásanyagokat:
    - [Gyakori kérdéseket](vmware-azure-common-questions.md) a VMware vészhelyreállításával kapcsolatban.
    - [Ismerje meg](vmware-physical-azure-support-matrix.md), hogy mit támogatott és mi szükséges a VMware esetén.
-  Olvassa el **útmutatóink** részletes utasításait, amelyek a VMware összes üzembe helyezési beállítására kiterjednek:
    - A [replikációs forrás](vmware-azure-set-up-source.md) és a [konfigurációs kiszolgáló](vmware-azure-deploy-configuration-server.md) beállítása.
    - A [replikációs cél](vmware-azure-set-up-target.md) beállítása.
    - [Replikációs szabályzat](vmware-azure-set-up-replication.md) konfigurálása és a [replikáció engedélyezése](vmware-azure-enable-replication.md).


## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.


## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Ez az oktatóanyag bemutatja, hogyan replikálható egyetlen virtuális gép, és a **Deployment Planning** (Központi telepítés megtervezése) szakaszban a **Yes, I have done it** (Igen, elvégeztem) lehetőséget választjuk majd. Ha több virtuális gép üzembe helyezését végzi, javasoljuk, hogy ne hagyja ki ezt a lépést. Segítségül biztosítjuk Önnek a [Deployment Planner eszközt](https://aka.ms/asr-deployment-planner). [További információ](site-recovery-deployment-planner.md) az eszközről.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Az üzembe helyezés első lépéseként állítsa be a forráskörnyezetet. A helyszíni Site Recovery-összetevők futtatásához egyetlen, magas rendelkezésre állású helyszíni gépre van szükség. Ilyen összetevő lehet a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló:

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket majd küld, hogy a gyorsítótárban a fiók az Azure-ban. A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

A konfigurációs kiszolgáló magas rendelkezésre állású VMware virtuális gépként való beállításához töltsön le egy előkészített Open Virtualization Application-sablont (OVA, nyílt virtualizációs alkalmazás), és importálja a VMware-be a virtuális gép létrehozásához. Miután beállította a konfigurációs kiszolgálót, regisztrálja a tárolóban. A regisztrációt követően a Site Recovery felderíti a helyszíni VMware virtuális gépeket.

> [!TIP]
> Ez az oktatóanyag egy OVA-sablonnal hozza létre a konfigurációs kiszolgáló VMware virtuális gépét. Ha ezt nem tudja megtenni, [manuálisan is beállíthatja a konfigurációs kiszolgálót](physical-manage-configuration-server.md).

> [!TIP]
> Ebben az oktatóanyagban a Site Recovery tölti le és telepíti a MySQL-t a konfigurációs kiszolgálóra. Ha nem szeretné, hogy a Site Recovery végezze ezt, manuálisan is telepítheti. [További információk](vmware-azure-deploy-configuration-server.md#configure-settings).


### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF-sablonját.

 > [!TIP]
 >A konfigurációs kiszolgálói sablon legújabb verzióját közvetlenül letöltheti a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Az OVF-sablon a megadott licenc egy próbalicencre 180 napig érvényes. Ügyfél kell határidődátumával licenccel a windows aktiválása.

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
  Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) beállítás jelölését. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha további hálózati adaptereket szeretne hozzáadni a konfigurációs kiszolgálóhoz, ezt azelőtt tegye meg, hogy regisztrálná a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Válassza a **Next** > **Finish** (Tovább > Befejezés) elemet. Ezután kattintson az **OK** gombra.


## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával néhány másodpercen belül elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

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


A Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

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

A replikáció engedélyezése a következő módon történik:

1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
2. A **Forrás** mezőben válassza a **Helyszíni** lehetőséget, majd a **Forrás helye** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet vagy az azt felügyelő vCenter-kiszolgálót.
5. Válassza ki a folyamatkiszolgálót (alapértelmezés szerint telepítve van a konfigurációs kiszolgáló virtuális gépén). Ezután kattintson az **OK** gombra.
6. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. A Resource Manager-alapú üzemi modellt használjuk. 
7. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
8. Ha a hálózati beállítást minden olyan virtuális gépre alkalmazni szeretné, amelyen engedélyezte a replikációt, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
9. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra. Ha nem tudja megtekintése/kiválasztani bármely adott virtuális gép, kattintson a [Itt](https://aka.ms/doc-plugin-VM-not-showing) a probléma megoldásához.
10. A **Tulajdonságok** > **Tulajdonságok konfigurálása** mezőben válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.
11. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva.
12. Válassza ki a **Replikáció engedélyezése** elemet. Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást.
13. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.
- 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.
- A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
