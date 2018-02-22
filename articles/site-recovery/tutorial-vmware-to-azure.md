---
title: "Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez az Azure Site Recovery szolgáltatással | Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást helyszíni VMware virtuális gépekhez az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d9248d2501c7fea0492bad2687b6bdfb0b903e8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Az Azure-ba irányuló vészhelyreállítás beállítása helyszíni VMware virtuális gépekhez

Az oktatóanyag bemutatja, hogyan állíthat be az Azure-ba irányuló vészhelyreállítást Windows rendszerű helyszíni VMware virtuális gépekhez. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A replikációs forrás és cél megadása.
> * A forrás replikációs környezet (többek között a helyszíni Site Recovery-összetevők), valamint a cél replikálási környezet beállítása.
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](tutorial-prepare-on-premises-vmware.md)

Mielőtt elkezdené, érdemes [áttekinteni az architektúrát](concepts-vmware-to-azure-architecture.md), hogy létezik-e vészhelyreállítási forgatókönyv.


## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

1. A **Recovery Services-tárolók** listában kattintson a tároló nevére: **ContosoVMVault**.
2. A **Bevezetés** területen kattintson a Site Recovery elemre. Kattintson az **Infrastruktúra előkészítése** elemre.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

A forráskörnyezet telepítéséhez egyetlen, magas rendelkezésre állású, helyszíni gépre van szükség a helyszíni Site Recovery-összetevők futtatásához. Ilyen összetevő lehet a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló.

- A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamatkiszolgáló replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak. A folyamatkiszolgáló ezenfelül telepíti a mobilitási szolgáltatást a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.

A konfigurációs kiszolgáló magas rendelkezésre állású VMware virtuális gépként való beállításához letöltünk egy előkészített OVF-sablont, és importáljuk azt a VMware-be a virtuális gép létrehozásához. Miután beállította a konfigurációs kiszolgálót, regisztrálja a tárolóban. A regisztrációt követően a Site Recovery felderíti a helyszíni VMware virtuális gépeket.

### <a name="download-the-vm-template"></a>A virtuálisgép-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban kattintson a **+Konfigurációs kiszolgáló** gombra.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF- (Open Virtualization Format – nyílt virtualizációs formátum) sablonját.

  > [!TIP]
  A konfigurációs kiszolgálói sablon legújabb verziója közvetlenül letölthető a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.  

     ![OVF-sablon](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen kattintson a **Next** (Tovább) gombra.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
4. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
5. A **Ready to complete** (Befejezésre kész) területen:
  - A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.
  - Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** (Bekapcsolás a telepítést követően) beállítás jelölését, majd kattintson a **Finish** (Befejezés) gombra. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját egyetlen hálózati adapterre telepíti a rendszer, de a telepítést követően további hálózati adaptereket is hozzáadhat.

  
## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, azt tegye meg a kiszolgáló a tárolóban való regisztrálása előtt. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen kattintson az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemre. Ezután kattintson a **Next** (Tovább) gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Kattintson a **Next** > **Finish** (Tovább > Befejezés), majd az **OK** gombra.


## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Next** (Tovább) gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.

### <a name="configure-settings-and-connect-to-vmware"></a>Beállítások konfigurálása és csatlakozás a VMware-hez

1. A konfigurációs kiszolgáló felügyeleti varázslójában kattintson a **Setup connectivity** (Kapcsolat beállítása) gombra, és válassza ki a hálózati adaptert, amely a replikációs forgalmat fogadni fogja. Ezután kattintson a **Save** (Mentés) gombra. Ez a beállítás a konfigurációt követően nem módosítható.
2. A **Helyreállítási tár kiválasztása** területen válassza ki az Azure-előfizetést, valamint a megfelelő erőforráscsoportot és tárolót.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést, és kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
4. Kattintson a **VMware PowerCLI telepítése** gombra. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután kattintson a **Folytatás** gombra.
5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket a folytatás előtt.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg a vCenter-kiszolgáló teljes tartománynevét vagy IP-címét, amelyen a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel, valamint a VMware-kiszolgáló a tárolóban használt rövid nevét.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Kattintson a **Hozzáadás**, majd a **Folytatás** elemre.
8. A **Virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a felhasználónevet és jelszót a mobilitási szolgáltatás a gépekre való automatikus telepítéséhez, ha a replikáció engedélyezve van. A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
9. Kattintson a **Konfiguráció véglegesítése** gombra a regisztráció befejezéséhez. 
10. A regisztráció befejezését követően ellenőrizze az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepelnek-e a tároló **Forrás** lapján. Kattintson az **OK** gombra a célbeállítások konfigurálásához.


A Site Recovery a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket.

> [!NOTE]
> Akár 15 vagy még több percbe is beletelhet, amíg a fiók neve megjelenik a portálon. Az azonnali frissítéshez kattintson a **Konfigurációs kiszolgálók** > ***kiszolgáló neve*** > **Kiszolgáló frissítése** elemre.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg, hogy a cél üzembehelyezési modellje Resource Manager-alapú vagy klasszikus.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és kattintson a **Minden erőforrás** elemre.
2. Kattintson a **ContosoVMVault** nevű Recovery Services-tárolóra.
3. A replikációs szabályzat létrehozásához kattintson a **Site Recovery-infrastruktúra** > **Replikációs szabályzatok** > **+Replikációs szabályzat** elemre.
4. A **Replikációs szabályzat létrehozása** területen adja meg a szabályzat nevét: **VMwareRepPolicy**.
5. A **Helyreállítási időkorlát küszöbértéke** beállításnál használja az alapértelmezett 60 percet. Ez az érték határozza meg, hogy milyen gyakran jönnek létre helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál használja az alapértelmezett 24 óra értéket, ami azt mutatja, hogy a rendszer milyen hosszan őrzi meg az egyes helyreállítási pontokat. Ebben az oktatóanyagban 72 órát használunk. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállításnál használja az alapértelmezett 60 perc értéket, az alkalmazáskonzisztens pillanatképek létrehozásának gyakoriságára. A szabályzat létrehozásához kattintson az **OK** gombra.

   ![Szabályzat](./media/tutorial-vmware-to-azure/replication-policy.png)

A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Ha például a replikációs szabályzat a **rep-policy**, a feladat-visszavételi szabályzat a **rep-policy-failback** lesz. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Amikor engedélyezi a replikációt egy adott virtuális gépen, a Site Recovery telepíti a mobilitási szolgáltatást. 15 vagy több percet is igénybe vehet, hogy a módosítások életbe lépjenek és megjelenjenek a portálon.

A replikációt a következők szerint engedélyezheti:

1. Kattintson az **Alkalmazás replikálása** > **Forrás** elemre.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Gép típusa** mezőben válassza a **Virtual Machines** lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet.
5. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezután kattintson az **OK** gombra.
6. A **Cél** mezőben válassza ki az előfizetést és az erőforráscsoportot, amelyben a feladatátviteli virtuális gépeket létre szeretné hozni. Kattintson a feladatátvételi virtuális gépekre használni kívánt Azure üzembehelyezési modellre (klasszikus vagy erőforrás-kezelés).
7. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot.
8. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
9. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre.
10. A **Virtuális gépek** > **Virtuális gépek kijelölése** menüben kattintással jelölje ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután kattintson az **OK** gombra.
11. A **Tulajdonságok** > **Tulajdonságok konfigurálása** mezőben válassza ki a fiókot, amelynek használatával a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen.
12. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva.
13. Kattintson a **Replikáció engedélyezése** elemre.

A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.

A hozzáadott virtuális gépek monitorozásával kapcsolatban a virtuális gépek legutolsó felderítésének időpontját a **Konfigurációs kiszolgálók**
> **Legutóbbi kapcsolat** területen ellenőrizheti. Ha a virtuális gépek hozzáadásához nem szeretné megvárni az ütemezett felderítést, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), majd kattintson a **Frissítés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítási próba végrehajtása](site-recovery-test-failover-to-azure.md)
