---
title: Helyszíni helyek közötti vészhelyreállítás beállítása Hyper-V virtuális gépekhez az Azure Site Recovery használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthat be helyszíni helyek közötti vészhelyreállítást Hyper-V virtuális gépekhez az Azure Site Recoveryvel.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 040d0dae7bb16e0c2a79640812b18ad75754ca3e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846606"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Másodlagos helyszíni helyre irányuló vészhelyreállítás beállítása Hyper-V virtuális gépekhez

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

A cikk bemutatja, hogyan állíthat be vészhelyreállítást egy másodlagos helyre a System Center Virtual Machine Manager-(VMM-) felhőkben felügyelt, helyszíni Hyper-V virtuális gépekhez. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A helyszíni VMM-kiszolgálók és a Hyper-V gazdagépek előkészítése
> * Helyreállítási tár létrehozása a Site Recovery számára 
> * Replikációs forrás- és célkörnyezet beállítása 
> * A hálózatleképezés beállítása 
> * Replikációs házirend létrehozása
> * Virtuális gép replikálásának engedélyezése

## <a name="prerequisites"></a>Előfeltételek

A forgatókönyv teljesítéséhez:

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](hyper-v-vmm-architecture.md).
- Győződjön meg arról, hogy a VMM-kiszolgálók és a Hyper-V gazdagépek megfelelnek a [támogatási követelményeknek](hyper-v-vmm-secondary-support-matrix.md).
- Ellenőrizze, hogy a replikálni kívánt virtuális gépek megfelelnek-e [a replikált gépekre vonatkozó támogatás](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support) feltételeinek.
- Készítse elő a VMM-kiszolgálókat a hálózatleképezéshez.

### <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

A [hálózatleképezés](hyper-v-vmm-network-mapping.md) kapcsolatot hoz létre a forrás- és célfelhőkben lévő helyszíni VMM-virtuálisgép-hálózatok között. A leképezés a következőket hajtja végre:

- A feladatátvételt követően összekapcsolja a virtuális gépeket a megfelelő céloldali virtuálisgép-hálózatokkal. 
- Optimális módon helyezi el a virtuális replikagépeket a céloldali Hyper-V gazdakiszolgálókon. 
- Ha nem konfigurálja a hálózatleképezést, a virtuális replikagépek nem lesznek csatlakoztatva egyetlen VM-hálózathoz sem a feladatátvétel után.

A VMM előkészítését a következőképpen végezze el:

1. Győződjön meg arról, hogy rendelkezik [VMM logikai hálózatokkal](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás- és céloldali VMM-kiszolgálókon.
    - A forrásoldali kiszolgálón található logikai hálózatnak ahhoz a forrásfelhőhöz kell tartoznia, amelyikben a Hyper-V gazdagépek találhatók.
    - A céloldali kiszolgálón található logikai hálózatnak a célfelhőhöz kell tartoznia.
1. Győződjön meg arról, hogy rendelkezik [virtuálisgép-hálózatokkal](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás- és céloldali VMM-kiszolgálókon. A virtuálisgép-hálózatokat minden helyen össze kell kapcsolni a logikai hálózattal.
2. A forrásoldali Hyper-V gazdagépeken található virtuális gépeket a forrásoldali virtuálisgép-hálózathoz kell csatlakoztatni. 


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hova.

1. Kattintson a **Site Recovery** > **1. lépés: Az infrastruktúra előkészítése** > **Védelmi cél** elemre.
2. Válassza a **Helyreállítási helyre**, valamint az **Igen, a következővel: Hyper-V** lehetőséget.
3. Az **Igen** lehetőség kiválasztásával erősítse meg, hogy VMM-mel felügyeli a Hyper-V gazdagépeket.
4. Válassza az **Igen** lehetőséget, ha rendelkezik másodlagos VMM-kiszolgálóval. Ha felhők közötti replikációt helyez üzembe egyetlen VMM-kiszolgálón, kattintson a **Nem** lehetőségre. Ezután kattintson az **OK** gombra.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Telepítse az Azure Site Recovery Providert a VMM-kiszolgálókra, és keresse meg, majd regisztrálja a kiszolgálókat a tárolóban.

1. Kattintson **Az infrastruktúra előkészítése** > **Forrás** lehetőségre.
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **System Center VMM-kiszolgáló** érték látható-e.
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a Provider telepítése során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Telepítse a Providert minden VMM-kiszolgálón. A Hyper-V gazdagépekre semmit nem szükséges külön telepíteni.

### <a name="install-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése

1. Futtassa a Provider telepítőfájlját minden VMM-kiszolgálón. Ha a VMM fürtben üzemel, az első telepítésnél a következőképpen járjon el:
    -  Telepítse a Providert egy aktív csomópontra, majd fejezze be a telepítést a VMM-kiszolgáló a tárolóban való regisztrálásához.
    - Ezt követően telepítse a Providert a többi csomópontra. Minden fürtcsomóponton ugyanannak a Provider-verziónak kell futnia.
2. A telepítő lefuttat néhány előfeltétel-ellenőrzést, majd engedélyt kér a VMM szolgáltatás leállítására. A rendszer a telepítés befejezését követően automatikusan újraindítja a VMM szolgáltatást. Ha VMM-fürtben végzi a telepítést, a telepítő megkéri a fürtszerepkör leállítására.
3. A **Microsoft Update** lapon kérheti, hogy a rendszer a Microsoft Update-szabályzatnak megfelelően telepítse a Providerhez kiadott frissítéseket.
4. A **Telepítés** lapon tetszés szerint fogadja el vagy módosítsa az alapértelmezett telepítési helyet, majd kattintson a **Telepítés** gombra.
5. A telepítést követően a kiszolgálónak a tárolóban való regisztrálásához kattintson a **Regisztrálás** elemre.

    ![Telepítés helye](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. A **Tároló neve** résznél ellenőrizze a tároló nevét, amelyben a kiszolgálót regisztrálni fogja. Kattintson a **Tovább** gombra.
7. A **Proxykapcsolat** lapon adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon az Azure-hoz.
   - Megadhatja, hogy a Provider közvetlenül vagy proxyn keresztül csatlakozzon az internethez. Szükség szerint adja meg a proxybeállításokat.
   - Ha proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A RunAs-fiók beállításait a VMM-konzolban módosíthatja: **Beállítások** > **Biztonság** > **Futtató fiókok**.
   - Indítsa újra a VMM szolgáltatást a módosítások frissítéséhez.
8. A **Regisztrációs kulcs** résznél válassza ki a letöltött, majd a VMM-kiszolgálóra másolt kulcsot.
9. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási beállítást. 
10. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürt használata esetén adja meg a VMM-fürtszerepkör nevét.
11. A **Felhőmetaadatok szinkronizálása** mezőben válassza ki, hogy szeretné-e a VMM-kiszolgáló összes felhőjének metaadatait szinkronizálni. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be ezt a beállítást. A felhőket egyenként is szinkronizálhatja a VMM-konzolban, a felhők tulajdonságainál.
12. A folyamat befejezéséhez kattintson a **Next** (Tovább) gombra. A regisztrációt követően a Recovery lekéri a metaadatokat VMM-kiszolgálóról. A kiszolgáló ezt követően megjelenik a tároló **Kiszolgálók** > **VMM-kiszolgálók** lapján.
13. Ha a kiszolgáló megjelent a tárolóban, a **Forrás** > **Forrás előkészítése** panelen válassza ki a VMM-kiszolgálót, majd azt a felhőt, amelyben a Hyper-V gazdagép található. Ezután kattintson az **OK** gombra.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki a céloldali VMM-kiszolgálót és felhőt:

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a céloldali VMM-kiszolgálót.
2. Megjelennek a Site Recovery használatával szinkronizált VMM-felhők. Válassza ki a célfelhőt.

   ![Cél](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

Mielőtt hozzálátna, győződjön meg róla, hogy a szabályzatot használó összes gazdagép ugyanazzal az operációs rendszerrel rendelkezik. Ha a gazdagépek a Windows Server különböző verzióit futtatják, többféle replikációs szabályzatra van szükség.

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A forrás és a cél típusa egyaránt a **Hyper-V** kell legyen.
3. A **Hyper-V gazdagép verziója** elemnél válassza ki a gazdagépen futó operációs rendszer típusát.
4. A **Hitelesítési típus** és **Hitelesítési port** beállításnál adja meg, hogy a forgalom hitelesítése hogyan történik az elsődleges és a helyreállítási Hyper-V gazdakiszolgálók között.
    - Ha nincs működő Kerberos-környezete, válassza a **Tanúsítvány** lehetőséget. Az Azure Site Recovery automatikusan konfigurálja a HTTPS-hitelesítéshez szükséges tanúsítványokat. Manuálisan semmit sem kell beállítania.
    - Alapértelmezés szerint a 8083-as 8084-es (tanúsítványoknak szánt) port nyílik meg a Hyper-V gazdakiszolgálók Windows tűzfalán.
    - A **Kerberos** kiválasztása esetén a gazdakiszolgálók kölcsönös hitelesítése Kerberos-jegyekkel történik. A Kerberos csak a Windows Server 2012 R2 vagy annál újabb rendszert futtató Hyper-V gazdakiszolgálók esetén jöhet számításba.
1. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
2. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
3. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). A Hyper-V kétféle pillanatképet használ:
    - **Standard pillanatkép**: A virtuális gép egészét lefedő növekményes pillanatképet biztosít.
    - **Alkalmazáskonzisztens pillanatkép**: A virtuális gépen található alkalmazásadatok időponthoz kötött pillanatképe. A kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésekor. Az alkalmazáskonzisztens pillanatképek engedélyezése hatással van az alkalmazások teljesítményére a forrásoldali virtuális gépeken. Ügyeljen rá, hogy a beállított érték kisebb legyen a további beállított helyreállítási pontok számánál.
4. A **Tömörített adatátvitel** beállításnál adja meg, hogy tömöríteni kívánja-e az átvitt replikációs adatokat.
5. A **Virtuális replikagép törlése** beállítással megadhatja, hogy a virtuális replikagép törölhető-e a forrásoldali virtuális gép védelmének letiltása esetén. Ha engedélyezi ezt a beállítást, a forrásoldali virtuális gép védelmének letiltásakor a rendszer eltávolítja azt a Site Recovery konzoljáról, a Site Recovery VMM-beállításai törlődnek a VMM konzoljáról, és a replika is törlődik.
6. Ha a replikáció a hálózaton keresztül történik, a **Kezdeti replikációs módszer** beállításnál adhatja meg, hogy a replikáció azonnal vagy egy ütemezett időpontban kezdődjön. A sávszélesség megtakarítása érdekében érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet. Ezután kattintson az **OK** gombra.

     ![Replikációs szabályzat](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Az új szabályzat automatikusan társítva lesz a VMM-felhővel. A **Replikációs szabályzat** elemnél kattintson az **OK** gombra. 


## <a name="enable-replication"></a>A replikáció engedélyezése

1. Kattintson az **Alkalmazás replikálása** > **Forrás** elemre. 
2. A **Forrás** területen válassza ki a VMM-kiszolgálót és a felhőt, amelyben a replikálni kívánt Hyper-V gazdagépek futnak. Ezután kattintson az **OK** gombra.
3. A **Cél** területen ellenőrizze a másodlagos VMM-kiszolgálót és felhőt.
4. A **Virtuális gépek** területen válassza ki a listából a védelemmel ellátni kívánt virtuális gépeket.


A **Védelem engedélyezése** művelet előrehaladását a **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeztével a kezdeti replikálás is befejeződik, a virtuális gép pedig készen áll a feladatátvételre.

## <a name="next-steps"></a>További lépések

[Vészhelyreállítási próba végrehajtása](hyper-v-vmm-test-failover.md)
