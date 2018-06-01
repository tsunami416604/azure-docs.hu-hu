---
title: Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez az Azure Site Recovery szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recoveryvel.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2a96655c26e2df2534f420239b56ef0c3959319a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212874"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Az oktatóanyag bemutatja, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást Windows rendszerű helyszíni VMware virtuális gépekhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A replikációs forrás és cél megadása.
> * A replikációs forráskörnyezet (többek között a helyszíni Azure Site Recovery-összetevők), valamint a replikálási célkörnyezet beállítása.
> * Hozzon létre replikációs szabályzatot.
> * Engedélyezze egy virtuális gép replikációját.

Ez az oktatóanyag egy sorozat harmadik része. Az oktatóanyag során feltételezzük, hogy elvégezte az azt megelőző oktatóanyagok feladatait:

* [Az Azure előkészítése](tutorial-prepare-azure.md). Ez az oktatóanyag leírja, hogyan állíthat be Azure-tárfiókot és -hálózatot, hogyan ellenőrizheti, hogy rendelkezik-e a megfelelő engedélyekkel, és hogyan hozhat létre helyreállítási tárat.
* [Helyszíni VMware előkészítése](vmware-azure-tutorial-prepare-on-premises.md). Ebben az oktatóanyagban fiókokat készít elő, hogy a Site Recovery VMware-kiszolgálókat érjen el a virtuális gépek felderítése érdekében, és opcionálisan elvégezheti a Site Recovery mobilitási szolgáltatási összetevő leküldéses telepítését, amikor engedélyezi egy virtuális gép replikációját. Arról is meggyőződik, hogy a VMware-kiszolgálók és a virtuális gépek megfelelnek a Site Recovery követelményeinek.

Mielőtt nekikezdene, érdemes [áttekintenie, hogy léteznek-e az architektúrában](vmware-azure-architecture.md) vészhelyreállítási forgatókönyvek.


## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. Ehhez a forgatókönyvhöz a **ContosoVMVault** nevet használjuk.
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása


A forráskörnyezet telepítéséhez egyetlen magas rendelkezésre állású helyszíni gépre van szükség a helyszíni Site Recovery-összetevők futtatásához. Ilyen összetevő lehet a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló:

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az adatokat az Azure-tárolónak. A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

A konfigurációs kiszolgáló magas rendelkezésre állású VMware virtuális gépként való beállításához töltsön le egy előkészített Open Virtualization Format-sablont (OVF, nyílt virtualizációs formátum), és importálja a VMware-be a virtuális gép létrehozásához. Miután beállította a konfigurációs kiszolgálót, regisztrálja a tárolóban. A regisztrációt követően a Site Recovery felderíti a helyszíni VMware virtuális gépeket.

> [!TIP]
> Ez az oktatóanyag OVF-sablonnal hozza létre a konfigurációs kiszolgáló VMware virtuális gépét. Ha ezt nem tudja megtenni, [manuális beállítást](physical-manage-configuration-server.md) is használhat. 


### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF-sablonját.

  > [!TIP]
  A konfigurációs kiszolgálói sablon legújabb verzióját közvetlenül letöltheti a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához. 

     ![OVF-sablon](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** (Bekapcsolás a telepítést követően) beállítás jelölését. Ezután kattintson a **Befejezés** gombra. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

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
4. Az első bejelentkezés alkalmával elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.

### <a name="configure-settings-and-add-the-vmware-server"></a>Beállítások konfigurálása és a VMware-kiszolgáló hozzáadása

1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza a **Setup connectivity** (Kapcsolat beállítása) gombot, majd válassza ki a folyamatkiszolgáló által a virtuális gépekről érkező replikációs forgalom fogadására használt hálózati adaptert. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás a konfigurálás után nem módosítható.
2. A **Helyreállítási tár kiválasztása** területen válassza ki az Azure-előfizetést, valamint a megfelelő erőforráscsoportot és tárolót.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
4. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.
8. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás gépekre való automatikus telepítéséhez használt felhasználónevet és jelszót, ha a replikáció engedélyezve van. A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
9. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez. 
10. A regisztráció befejezését követően ellenőrizze az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepelnek-e a tároló **Forrás** lapján. Ezután válassza az **OK** gombot a célbeállítások konfigurálásához.


A Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez válassza a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemet.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Válassza **Az infrastruktúra előkészítése** > **Cél** lehetőséget. Válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg, hogy a cél üzembehelyezési modellje Azure Resource Manager-alapú vagy klasszikus legyen.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél lap](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és válassza a **Minden erőforrás** elemet.
2. Válassza ki a **ContosoVMVault** nevű helyreállítási tárat.
3. A replikációs szabályzat létrehozásához válassza a **Site Recovery-infrastruktúra** > **Replikációs szabályzatok** > **+Replikációs szabályzat** elemet.
4. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét. Ehhez a forgatókönyvhöz a **VMwareRepPolicy** nevet használjuk.
5. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál használja az alapértelmezett 24 óra értéket, ami azt mutatja, hogy a rendszer milyen hosszan őrzi meg az egyes helyreállítási pontokat. A jelen oktatóanyag elvégzéséhez érdemes 72 órát megadnia. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításnál használja az alapértelmezett 60 perc értéket, az alkalmazáskonzisztens pillanatképek létrehozásának gyakoriságára. A szabályzat létrehozásához válassza az **OK** gombot.

   ![Replikációs házirend létrehozása](./media/vmware-azure-tutorial/replication-policy.png)

A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer a feladat-visszavételhez is automatikusan létrehoz egy megfelelő szabályzatot. Ha például a replikációs szabályzat a **rep-policy**, a feladat-visszavételi szabályzat a **rep-policy-failback** lesz. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.

A replikációt a következők szerint engedélyezheti:

1. Válassza az **Alkalmazás replikálása** > **Forrás** elemet.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet.
5. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.
6. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. Kattintson a feladatátvételi virtuális gépekre használni kívánt Azure üzembehelyezési modellre (klasszikus vagy Resource Manager).
7. Válassza ki az adatok replikálásához használni kívánt Azure Storage-fiókot.
8. Válassza ki azt az Azure-hálózatot és alhálózatot, amelyhez a feladatátvétel után létrejövő Azure-beli virtuális gépek csatlakoznak.
9. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
10. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.
11. A **Tulajdonságok** > **Tulajdonságok konfigurálása** mezőben válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.
12. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva.
13. Válassza ki a **Replikáció engedélyezése** elemet.

A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

A hozzáadott virtuális gépek monitorozásához ellenőrizze a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók** > **Legutóbbi kapcsolat** területen. Ha nem szeretné megvárni az ütemezett felderítést a virtuális gépek hozzáadásához, emelje ki a konfigurációs kiszolgálót (ne válassza ki), majd válassza a **Frissítés** elemet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
