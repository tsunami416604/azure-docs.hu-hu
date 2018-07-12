---
title: Vészhelyreállítás beállítása Hyper-V virtuális gépek az Azure Site Recovery a helyszíni helyek között |} A Microsoft Docs
description: Ismerje meg, hogyan vészhelyreállítás beállítása Hyper-V virtuális gépek az Azure Site Recovery a helyszíni helyek között.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 4cc4da130d9253bf40e5d02806088a95b2195e7c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531721"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Vészhelyreállítás beállítása Hyper-V virtuális gépek egy másodlagos helyszíni helyre

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ez a cikk bemutatja, hogyan állíthat be vészhelyreállítást egy másodlagos helyre, a System Center Virtual Machine Manager (VMM) felhőkben felügyelt helyszíni Hyper-V virtuális gépeket. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A helyszíni VMM-kiszolgálónak és Hyper-V-gazdagépek előkészítése
> * Hozzon létre egy Recovery Services-tárolót a Site Recovery 
> * Állítsa be a forrás és cél replikációs környezetekben. 
> * Hálózatleképezés beállítása 
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Ez a forgatókönyv végrehajtásához:

- Tekintse át a [forgatókönyv-architektúra és összetevők](hyper-v-vmm-architecture.md).
- Győződjön meg arról, hogy a VMM-kiszolgálónak és Hyper-V-gazdagépek megfelelnek a [memóriakonfigurációt](hyper-v-vmm-secondary-support-matrix.md).
- Ellenőrizze, hogy a replikálni kívánt virtuális gépek megfelelnek [replikált gépek támogatása](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Hálózatleképezés előkészítése a VMM-kiszolgálókon.

### <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[A hálózatleképezés](hyper-v-vmm-network-mapping.md) közötti leképezéseket a helyszíni VMM-Virtuálisgép-hálózatok forrás és cél-felhőkben. Leképezés a következőket teszi:

- Virtuális gépek a feladatátvételt követően megfelelő céloldali Virtuálisgép-hálózatok kapcsolódik. 
- Replika virtuális gépek optimálisan helyezi el a cél Hyper-V gazdakiszolgálókra. 
- Ha nem konfigurálja a hálózatleképezést, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózat a feladatátvételt követően.

A következő előkészítése a VMM-ben:

1. Ellenőrizze, hogy [VMM logikai hálózatok](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás- és VMM-kiszolgálókon.
    - Lehet, hogy a forráskiszolgálón a logikai hálózat társítva a forrás-felhő, amelyben a Hyper-V-gazdagépek találhatók.
    - A célkiszolgálón a logikai hálózat társítva a célfelhő kell lennie.
1. Ellenőrizze, hogy [Virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás- és VMM-kiszolgálókon. Virtuálisgép-hálózatot kösse össze az egyes helyeken a logikai hálózathoz.
2. Virtuális gépek csatlakoztatása a forrás Hyper-V gazdagépeken a forrás Virtuálisgép-hálózathoz. 


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Egy védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hova.

1. Kattintson a **Site Recovery** > **1. lépés: az infrastruktúra előkészítése** > **védelmi cél**.
2. Válassza ki **helyreállítási helyre**, és válassza ki **Igen, a Hyper-V**.
3. Válassza ki **Igen** jelzi a VMM-ben a Hyper-V gazdagépek felügyeletéhez használ.
4. Válassza ki **Igen** Ha rendelkezik egy másodlagos VMM-kiszolgáló. Ha telepít egy VMM-kiszolgáló a felhők közötti replikációt, kattintson a **nem**. Ezután kattintson az **OK** gombra.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Az Azure Site Recovery Provider telepítése a VMM-kiszolgálókon, és Fedezze fel, és regisztrálja a kiszolgálót a tárolóban.

1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.
3. A **-kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Ez szükséges a szolgáltató telepítésekor. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. A szolgáltató telepítése minden egyes VMM-kiszolgálón. Nem kell explicit módon telepít semmit a Hyper-V-gazdagépek.

### <a name="install-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése

1. Futtassa a szolgáltató telepítőfájlját minden VMM-kiszolgálón. Ha a VMM-ben egy fürtben lett telepítve, először a következőképpen telepítheti:
    -  Telepítse a Providert az aktív csomópont, és regisztrálja a VMM-kiszolgálót a tárolóban, a telepítés befejezéséhez.
    - Ezután telepítse a szolgáltatót a többi csomóponton. Fürtcsomópontok összes futtassa a szolgáltató megfelelő verziójával.
2. A telepítő néhány előfeltétel-ellenőrzéseket futtatja, és a VMM szolgáltatás leállítására engedélyt kér. A VMM szolgáltatás automatikusan újraindul, ha a telepítés befejezését. Ha telepíti a VMM-fürtben, megkéri a fürtszerepkör leállítására.
3. A **Microsoft Update**, kérheti a megadásához, hogy frissítéseket a Microsoft Update-szabályzatnak megfelelően vannak-e telepítve.
4. A **telepítési**, fogadja el vagy módosítsa az alapértelmezett telepítési helyét, és kattintson a **telepítése**.
5. Kattintson a telepítés befejezése után **regisztrálása** regisztrálja a kiszolgálót a tárolóban.

    ![Telepítés helye](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. A **Tároló neve** résznél ellenőrizze a tároló nevét, amelyben a kiszolgálót regisztrálni fogja. Kattintson a **Tovább** gombra.
7. A **proxykapcsolatot**, adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon az Azure-bA.
   - Megadhatja, hogy a provider közvetlenül az internethez, vagy egy proxyn keresztül csatlakozni. Adja meg a proxykiszolgáló beállításait, igény szerint.
   - Ha a proxy használatát választja, a VMM RunAs-fiókot (DRAProxyAccount) automatikusan létrejön, a megadott hitelesítő adatokat használ. Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A futtató fiók beállításait módosíthatja a VMM-konzol > **beállítások** > **biztonsági** > **futtató fiókok**.
   - Indítsa újra a VMM szolgáltatás módosításokat.
8. A **Szolgáltatásregisztrációs kulcs**, válassza ki a letöltött és a VMM-kiszolgálóra másolt kulcsot.
9. A titkosítási beállítást nem releváns ebben a forgatókönyvben. 
10. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Egy fürtben adja meg a VMM-fürtszerepkör nevét.
11. A **felhőmetaadatok szinkronizálása**, adja meg, hogy a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni szeretne. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné, hogy az összes felhőt szinkronizálni, ne jelölje be ezt a beállítást. Minden egyes felhőhöz külön-külön, szinkronizálhatja a felhők tulajdonságainál, a VMM-konzolon.
12. A folyamat befejezéséhez kattintson a **Next** (Tovább) gombra. A regisztrációt követően a Site Recovery lekéri a metaadatokat VMM-kiszolgálón. A kiszolgáló megjelenik a **kiszolgálók** > **VMM-kiszolgálók** a tárolóban.
13. Miután megjelenik a kiszolgálót a tárolóban **forrás** > **forrás előkészítése** válassza ki a VMM-kiszolgálón, és válassza ki a felhőt, amelyben a Hyper-V-gazdagépen helyezkedik el. Ezután kattintson az **OK** gombra.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki a célként megadott VMM-kiszolgáló és a felhő:

1. Kattintson a **infrastruktúra előkészítése** > **cél**, és válassza ki a célként megadott VMM-kiszolgáló.
2. A rendszer szinkronizálja a Site Recovery VMM-felhőkben jelennek meg. Válassza ki a cél-felhőt.

   ![Cél](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

Mielőtt elkezdené, győződjön meg róla, hogy a házirendet használó összes gazdagép ugyanazt az operációs rendszert. Ha a gazdagépek futtatja a Windows Server különböző verzióit, kell több replikációs házirend.

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A forrás és cél típusúnak kell lennie **Hyper-V**.
3. A **Hyper-V gazdagép verziója**, válassza ki, melyik operációs rendszer fut a gazdagépen.
4. A **hitelesítési típus** és **hitelesítési port**, adja meg, hogy az elsődleges és helyreállítási Hyper-V gazdakiszolgálók közötti forgalom hitelesítése.
    - Válassza ki **tanúsítvány** , kivéve, ha rendelkezik egy működő Kerberos-környezetben. Az Azure Site Recovery automatikusan konfigurálja a HTTPS-hitelesítéshez tanúsítványokat. Nem kell manuálisan semmit.
    - Alapértelmezés szerint a Hyper-V gazdakiszolgálókon a Windows tűzfal 8083 és 8084 (a tanúsítványok) port fognak megnyílni.
    - Ha **Kerberos**, egy Kerberos-jegyet a gazdakiszolgálók a kölcsönös hitelesítéshez használható. A Kerberos fontos csak a Windows Server 2012 R2 vagy újabb rendszert futtató Hyper-V-kiszolgálóknak.
1. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
2. A **helyreállítási pont megőrzése**, adja meg a \how mennyi ideig (órákban) az egyes helyreállítási pontok adatmegőrzési időtartama lesz. Replikált gépek időtartamon belül bármikor helyreállíthatók legyenek.
3. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, hogy milyen gyakran (1 – 12 óra) a következő helyreállítási pontjai alkalmazáskonzisztens pillanatképeket tartalmazó jönnek létre. A Hyper-V pillanatképekkel két típusú használ:
    - **A standard pillanatkép**: a teljes virtuális gépet egy növekményes pillanatképet tartalmaz.
    - **Alkalmazáskonzisztens pillanatkép**: pillanatképet készít a virtuális Gépen található alkalmazásadatok időponthoz –. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a alkalmazások konzisztens állapotban legyenek, a pillanatkép készítésének időpontjában. Alkalmazások teljesítményét a forrás virtuális gépek alkalmazáskonzisztens pillanatképek engedélyezése, hatással van. Állítsa be egy értéket, amely kisebb, mint a további helyreállítási pontok száma.
4. A **tömörített adatátvitel**, adja meg, hogy tömöríteni kell-átvitt adatokat.
5. Válassza ki **Delete replika virtuális gép**adja meg, hogy a replika virtuális gép törölte-e, ha letiltja a forrásoldali virtuális gép védelmét. Ha engedélyezi ezt a beállítást, ha letiltja a forrásoldali virtuális gép, a rendszer eltávolítja a Site Recovery konzolján védelmét, a Site Recovery beállításait a VMM a VMM-konzol törlődnek, és a replika törlése.
6. A **a kezdeti replikációs módszer**, ha replikál a hálózaton, adja meg, hogy a kezdeti replikáció, vagy átütemezheti azt. Szeretné menteni a hálózati sávszélesség, érdemes ütemezni, amikor ajánlatban foglalt üzemórák kívül. Ezután kattintson az **OK** gombra.

     ![Replikációs szabályzat](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Az új szabályzat automatikusan kapcsolódik a VMM-felhőben. A **replikációs házirend**, kattintson a **OK**. 


## <a name="enable-replication"></a>A replikáció engedélyezése

1. Kattintson az **Alkalmazás replikálása** > **Forrás** elemre. 
2. A **forrás**, válassza ki a VMM-kiszolgáló és a felhő, amelyben a replikálni kívánt Hyper-V-gazdagépek találhatók. Ezután kattintson az **OK** gombra.
3. A **cél**, ellenőrizze a másodlagos VMM-kiszolgáló és a felhőben.
4. A **virtuális gépek**, válassza ki a listából a védeni kívánt virtuális gépeket.


A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. Miután a **védelem véglegesítése** feladat befejeződik, a kezdeti replikálás is befejeződik, és a virtuális gép készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések

[Vészhelyreállítási próba végrehajtása](hyper-v-vmm-test-failover.md)
