---
title: "Hyper-V virtuális gépek replikálása egy másodlagos helyre az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Ismerteti a Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos VMM-helyre, az Azure portál használatával."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 777bddea6b1cb325a6f8ede00196b18e1746d80c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos VMM-helyre, az Azure portál használatával
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-vmm.md)
> * [Klasszikus portál](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Ez a cikk ismerteti, hogyan replikáljon helyszíni Hyper-V virtuális gépek kezelése a System Center Virtual Machine Manager (VMM) felhők, egy másodlagos hely használatával [Azure Site Recovery](site-recovery-overview.md) az Azure portálon. További tudnivalók ezzel [kialakítandó architektúra](site-recovery-components.md).

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.


## <a name="prerequisites"></a>Előfeltételek

**Előfeltétel** | **Részletek**
--- | ---
**Azure** | Szüksége van egy [Microsoft Azure](http://azure.microsoft.com/)-fiókra. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. [További információk](https://azure.microsoft.com/pricing/details/site-recovery/) a Site Recovery díjszabásáról.
**A helyszíni VMM** | Javasoljuk, hogy a két VMM-kiszolgáló, egy elsődleges helyen, és egy-egy másodlagos.<br/><br/> Egyetlen VMM-kiszolgálón felhők közötti replikálhatja.<br/><br/> VMM-kiszolgálókon legalább futnia kell a System Center 2012 SP1 a legújabb frissítésekkel.<br/><br/> VMM-kiszolgáló internetes hozzáférésre van szükségük.
**VMM-felhő** | Minden VMM-kiszolgálót rendelkeznie kell egy vagy több felhőt, és az összes felhő rendelkeznie kell a Hyper-V kapacitásprofilhoz beállítása. <br/><br/>Felhő legalább egy VMM-gazdagépcsoportot kell tartalmaznia.<br/><br/> Csak egy VMM-kiszolgálóval rendelkezik, hogy szükséges-e legalább két felhők, elsődleges és másodlagos nevében járhasson el.
**Hyper-V** | Hyper-V kiszolgálók legalább futnia kell a Hyper-V szerepkör és a Windows Server 2012 és a legújabb frissítések telepítve van.<br/><br/> Minden Hyper-V kiszolgáló tartalmazzon legalább egy virtuális gépet.<br/><br/>  Hyper-V gazdakiszolgálók csoportok tárolása az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük.<br/><br/> Ha a Hyper-V fürt, a Windows Server 2012 R2 rendszeren futtatja, telepítse [2961977 frissítése](https://support.microsoft.com/kb/2961977)<br/><br/> Ha a Hyper-V fürt, a Windows Server 2012 rendszerben futtatja, fürtszervező nem hozza létre automatikusan Ha egy statikus IP cím alapú fürtöt. A fürtszervező kézi konfigurálását. [További](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Hyper-V kiszolgálók internet-hozzáférésre van szükségük.
**URL-címek** | VMM-kiszolgáló és a Hyper-V-gazdagépek kell tudni érnie a URL-címek:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="deployment-steps"></a>A központi telepítés lépései

Ez mit:

1. Előfeltételek ellenőrzése.
2. A VMM-kiszolgáló és a Hyper-V-gazdagépek előkészítése.
3. Recovery Services-tároló létrehozása. A tároló konfigurációs beállításokat tartalmaz, és koordinálja a replikációt.
4. Adja meg a forrás, a cél és a replikációs beállításokat.
5. A mobilitási szolgáltatást a virtuális gépek telepítése szeretne replikálni.
6. Készítse elő a replikáció, és engedélyezze a Hyper-V virtuális gépek replikációját.
7. Egy feladatátvételi teszt futtatásával ellenőrizze, hogy minden a vártnak megfelelően működik-e.

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>Készítse elő a VMM-kiszolgáló és a Hyper-V-gazdagépek

A telepítés előkészítéséhez:

1. Győződjön meg arról, hogy a VMM-kiszolgáló és a Hyper-V gazdagépek megfelelnek a fent ismertetett Előfeltételek, és el lehet érni a szükséges URL-címek.
2. A VMM-hálózatokat állítsa be úgy, hogy az [hálózatleképezés](#network-mapping-overview).

    - Ellenőrizze, hogy a forrás Hyper-V gazdakiszolgálón futó virtuális gépek csatlakoznak-e egy VMM-virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
    Győződjön meg arról, hogy a helyreállításhoz használható másodlagos felhőbe rendelkezik-e a megfelelő Virtuálisgép-hálózatot. A Virtuálisgép-hálózatot kösse össze a másodlagos felhőhöz társított logikai hálózatot.

3. Készítse elő a egy [kiszolgálótelepítést egyetlen](#single-vmm-server-deployment), ha be szeretné replikálni a virtuális gépek a VMM-kiszolgálón felhők között.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **Új** > **Felügyelet** > **Recovery Services** elemre.
3. A **Név** mezőben adja meg a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válasszon egyet ezek közül.
4. [Hozzon létre egy erőforráscsoportot](../azure-resource-manager/resource-group-template-deploy-portal.md), vagy válasszon ki egy meglévőt. Válassza ki a kívánt Azure-régiót. Gépeket ebbe a régióba replikálja a rendszer. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
5. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton** > **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Az új tároló megjelenik a **irányítópult**, a **összes erőforrás**, majd a fő **Recovery Services-tárolók** panelen.


## <a name="choose-a-protection-goal"></a>Válassza ki a védelmi cél

Válassza ki, hogy mit szeretne replikálni, és hova.

2. Kattintson a **Site Recovery** > **1. lépés: az infrastruktúra előkészítése** > **védelmi cél**.
3. Válassza ki **helyreállítási hely**, és válassza ki **Igen, a Hyper-V-vel**.
4. Válassza ki **Igen** annak jelzésére, hogy a VMM segítségével a Hyper-V-gazdagépek kezelése.
5. Válassza ki **Igen** Ha egy másodlagos VMM-kiszolgálóval rendelkezik. Ha egy VMM-kiszolgálón felhők közötti replikáció telepít, kattintson a **nem**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Az Azure Site Recovery Provider telepítése a VMM-kiszolgálókon, és Fedezze fel, és regisztrálja a kiszolgálót a tárolóban.

1. Kattintson a **1. lépés: infrastruktúra előkészítése** > **forrás**.

    ![A forrás beállítása](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.

    ![A forrás beállítása](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus** , és hogy a VMM-kiszolgáló megfelel-e a [Előfeltételek](#prerequisites).
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a telepítő futtatása során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. Nem kell explicit módon a Hyper-V gazdakiszolgálókra telepít semmit.


### <a name="install-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése

1. Futtassa a Providert telepítőfájl minden VMM-kiszolgálón. Ha a VMM fürtben lett telepítve, tegye a következőket telepíti először listájában:
    -  Telepítse a szolgáltató aktív csomópontra, és fejezze be a telepítést, a VMM-kiszolgáló regisztrálása a tárolóban lévő állapottal.
    - Ezután telepítse a szolgáltatót a többi csomóponton. Fürtcsomópontok összes futtassa a szolgáltató azonos verziója.
2. A telepítő néhány előfeltétel-ellenőrzéseket futtatja, és a VMM szolgáltatás engedélyt kér. A VMM szolgáltatás automatikusan újraindul a telepítő befejezése után. Ha telepíti a VMM-fürthöz, megkéri a fürtszerepkör leállítása.
3. A **Microsoft Update**, lapon kérheti a adja meg, hogy szolgáltató frissítések telepítve vannak-e a Microsoft Update-szabályzatnak megfelelően.
4. A **telepítési**, fogadja el vagy módosítsa az alapértelmezett telepítési hely, és kattintson a **telepítése**.

    ![Telepítés helye](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. Kattintson a telepítés befejezése után **regisztrálása** regisztrálni a kiszolgálót a tárolóban lévő állapottal.

    ![Telepítés helye](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. A **Tároló neve** résznél ellenőrizze a tároló nevét, amelyben a kiszolgálót regisztrálni fogja. Kattintson a *Tovább* gombra.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. A **internetkapcsolat**, adja meg, hogy a VMM-kiszolgálón futó provider hogyan csatlakozzon az Azure-bA.

    ![Internetbeállítások](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - Megadhatja, hogy a szolgáltató közvetlenül az internethez, vagy egy proxyn keresztül kell-e csatlakozni.
   - Adja meg a proxybeállításokat, ha szükséges.
   - A proxyt használ, ha a VMM RunAs-fiókot (DRAProxyAccount) jön létre automatikusan a megadott proxy hitelesítő adatok használatával. Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A RunAs fiók beállításait módosíthatja a VMM-konzol > **beállítások** > **biztonsági** > **futtató fiókok**. Indítsa újra a VMM szolgáltatást módosításainak frissítésére.
8. A **Regisztrációs kulcs** résznél válassza ki az Azure Site Recoveryből letöltött, majd a VMM-kiszolgálóra másolt kulcsot.
9. A titkosítási beállítást csak akkor használja a rendszer, amikor a VMM-felhőkben található Hyper-V virtuális gépeket az Azure-ba replikálja. Másodlagos helyre történő replikáláskor a beállítást nem használja a rendszer.
10. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
11. A **Synchronize cloud metaadatok**, adja meg, hogy a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval szeretné. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem kívánja az összes felhő szinkronizálásához, hagyja bejelölve ezt a beállítást, és szinkronizálja egyenként a felhő tulajdonságai a VMM-konzolon.
12. A folyamat befejezéséhez kattintson a **Next** (Tovább) gombra. A regisztrációt követően az Azure Site Recovery lekéri a metaadatokat VMM-kiszolgálóról. A kiszolgáló ezt követően megjelenik a tároló **Kiszolgálók** lapjának **VMM-kiszolgálók** lapján.

    ![Kiszolgáló](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. Miután a kiszolgáló elérhető, a Site Recovery konzolján a **forrás** > **forrás előkészítése** válassza ki a VMM-kiszolgálót, és válassza ki a felhőt, amelyben a Hyper-V gazdagépen helyezkedik el. Ezután kattintson az **OK** gombra.

A provider a parancssorból is telepíthető:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki a cél VMM-kiszolgáló és a felhőben.

1. Kattintson a **infrastruktúra előkészítése** > **cél**, és válassza ki a használni kívánt cél VMM-kiszolgálóval.
2. Felhők a kiszolgálón, amely szinkronizálva legyenek a Site Recovery jelenik meg. Válassza ki a megcélzott felhőt.

   ![cél](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>Replikációs beállítások megadása

- Amikor egy replikációs házirend létrehozásához a házirend az összes állomás ugyanazt az operációs rendszert kell rendelkeznie. A VMM-felhő csak Windows Server különböző verzióit futtató Hyper-V-gazdagépek, de ebben az esetben szüksége több replikációs házirend.
- A kezdeti replikálás offline végezheti el. [További információ](#prepare-for-initial-offline-replication)

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét. A forrás és cél típusnak kell lennie **Hyper-V**.
3. A **Hyper-V gazdagép verziószámánál**, válassza ki, melyik operációs rendszer fut az állomáson.
4. A **hitelesítési típus** és **hitelesítési portot**, adja meg, hogy az elsődleges és a helyreállítási Hyper-V gazdakiszolgálók közötti forgalom hitelesítése. Válassza ki **tanúsítvány** kivéve, ha Kerberos-környezetben működő rendelkezik. Az Azure Site Recovery automatikusan HTTPS-hitelesítési tanúsítványok konfigurálja. Nem kell manuálisan semmit. Alapértelmezés szerint a Windows tűzfalat a Hyper-V gazdakiszolgálók port 8083 és 8084 (a tanúsítványok) fognak megnyílni. Ha **Kerberos**, a Kerberos jegyet a gazdakiszolgálók a kölcsönös hitelesítéshez használható. Vegye figyelembe, hogy ez a beállítás csak a Windows Server 2012 R2 rendszeren futó Hyper-V gazdakiszolgálók szükséges.
5. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
7. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, hogy milyen gyakran (1 – 12 órába) helyreállítási pontokat tartalmazó alkalmazáskonzisztens pillanatképeket jönnek létre. A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent. Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ha engedélyezi az alkalmazáskonzisztens pillanatképeket, az hatással a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
8. A **adatátvitel tömörítésének**, adja meg, hogy van-e a replikált adatok tömörített.
9. Válassza ki **replika virtuális gép törlése**adja meg, hogy a replika virtuális gépet törölni kell a forrás virtuális gép védelmének letiltásakor. Ha a forrás virtuális gép el van távolítva a Site Recovery konzolján védelmének letiltásakor engedélyezi ezt a beállítást, a Site Recovery beállításait a VMM el lesznek távolítva a VMM-konzolon, és a replika törlése.
10. A **kezdeti replikációs módszer**, ha a hálózaton keresztül, replikál adja meg, hogy a kezdeti replikáció elindítása, vagy átütemezheti azt. Ha a hálózati sávszélesség, érdemes ütemezni, amikor kevesen használják az kívül. Ezután kattintson az **OK** gombra.

     ![Replikációs szabályzat](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. A **replikációs házirend**, kattintson a **OK**. További VMM-felhőket (és a virtuális gépek őket) társíthatja a replikációs házirendet a **replikációs** > szabályzat neve > **VMM-felhő társítása**.

     ![Replikációs szabályzat](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

- További tudnivalók [hálózatleképezés](#prepare-for-network-mapping) megkezdése előtt.
- Győződjön meg arról, hogy a virtuális gépek VMM-kiszolgálókon Virtuálisgép-hálózathoz csatlakozik.


1. A **Site Recovery-infrastruktúra** > **Hálózatleképezés** > **Hálózatleképezések**, kattintson a **+ Hálózatleképezés**.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. A **hálózatleképezés hozzáadása** lapra, válassza ki a forrás és cél VMM-kiszolgálók. A Virtuálisgép-hálózatok a VMM-kiszolgálókon társított beolvasása.
3. A **Forráshálózat**, válassza ki a hálózatot a VMM-kiszolgáló társított Virtuálisgép-hálózatok listájában a használni kívánt.
4. A **célhálózat**, válassza ki a hálózatot szeretné használni a másodlagos VMM-kiszolgálón. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Ez történik a hálózatleképezés megkezdésekor:

* A célként megadott Virtuálisgép-hálózathoz csatlakoznak bármely meglévő replika virtuális gépeket, hogy a forrás Virtuálisgép-hálózat.
* Új, a forrás Virtuálisgép-hálózathoz csatlakozó virtuális gépek replikáció után a cél leképezése hálózathoz csatlakoznak.
* Ha módosít egy meglévő hálózatleképezést, a replika virtuális gépek az új beállításokkal fognak csatlakozni.
* Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.

### <a name="configure-storage-mapping"></a>Tárolási leképezés konfigurálása.

[Tároló leképezési](#prepare-for-storage-mapping) nem állítható be az új Azure-portálon. Azonban engedélyezni lehet Powershell használatával. [További információk](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>5. lépés: Kapacitástervezés

Most, hogy az alapszintű infrastruktúra készen áll, fogjon hozzá a kapacitás megtervezéséhez, és döntse el, hogy szüksége van-e további erőforrásokra.

- Töltse le és futtassa a [Azure Site Recovery Capacity planner](site-recovery-capacity-planner.md), össze a virtuális gép, valamint a lemezenkénti lemezek virtuális gépeken, beleértve a replikálási környezetre vonatkozó információt.
- Valós idejű replikálási adatok begyűjtését követően módosíthatja a NetQos házirendet a virtuális gépek replikáció sávszélesség szabályozására. Tudjon meg többet az [Hyper-V replika forgalom szabályozása](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/), Thomas Maurer blogjában. További információk a [New-NetQosPolicy parancsmag](https://technet.microsoft.com/library/hh967468.aspx.).

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Kattintson **2. lépés: Az alkalmazás replikálása** > **Forrás** elemre. Miután először replikációjának bekapcsolását, kattintson **+ replikálás** a további gépek replikációjának engedélyezése a tárolóban lévő állapottal.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. A **forrás**, válassza ki a VMM-kiszolgáló és a felhőben, amelyben a replikálni kívánt Hyper-V-gazdagépek találhatók. Ezután kattintson az **OK** gombra.

    ![A replikáció engedélyezése](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. A **cél**, ellenőrizze a másodlagos VMM-kiszolgáló és a felhőben.
4. A **virtuális gépek**, válassza ki a listából védeni kívánt virtuális gépeket.

    ![A virtuális gép védelmének engedélyezése](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Előrehaladásának nyomon követheti a **Védelemengedélyezési** műveletét **feladatok** > **Site Recovery-feladatok**. Miután a **Védelemvéglegesítési** feladat befejeződik, a virtuális gép készen áll a feladatátvételre.

Vegye figyelembe:

- A VMM-konzol virtuális gépek védelmét is engedélyezheti. Kattintson a **Védelemengedélyezési** az eszköztáron a virtuális gép tulajdonságai > **Azure Site Recovery** fülre.
- Replikáció engedélyezése után megtekintheti a virtuális gép a Tulajdonságok **replikált elemek**. Az a **Essentials** irányítópult, láthatja, hogy a virtuális gép és annak állapotát a replikációs házirend kapcsolatos információkat. Kattintson a **tulajdonságok** további részleteket.

### <a name="onboard-existing-virtual-machines"></a>A bevezetni meglévő virtuális gépek
Ha meglévő virtuális gépek a VMM Alkalmazásban – a Hyper-V replika replikálja, hogy discoveryt őket az Azure Site Recovery replikációs az alábbiak szerint:

1. Győződjön meg arról, hogy a Hyper-V kiszolgáló, a meglévő virtuális Gépet üzemeltető az elsődleges felhőben található, és, hogy a Hyper-V kiszolgáló, a replika virtuális gépet szolgáltató a másodlagos felhőben található.
2. Győződjön meg arról, hogy a replikációs házirend úgy van konfigurálva, az elsődleges VMM-felhő.
3. Engedélyezze az elsődleges virtuális gép replikálását. Az Azure Site Recovery és VMM győződjön meg arról, hogy az azonos másodpéldány-állomás és a virtuális gép észlel, és Azure Site Recovery lesznek ismét felhasználni, és a megadott beállítások replikálási szolgáltatás használatához.

## <a name="test-your-deployment"></a>A központi telepítés tesztelése

A központi telepítés tesztelése, futtassa a [feladatátvételi teszt](site-recovery-test-failover-vmm-to-vmm.md) egyetlen virtuális géphez, vagy [helyreállítási terv létrehozása](site-recovery-create-recovery-plans.md) , amely egy vagy több virtuális gépet tartalmaz.



## <a name="prepare-for-offline-initial-replication"></a>Offline kezdeti replikálás előkészítése

A kezdeti másolat offline replikációt teheti meg. Előkészítheti a következőképpen:

* A forráskiszolgálón adjon meg egy elérési helyet, ahonnan az adatok exportálása akkor kerül sor. Rendelje hozzá a teljes hozzáférés NTFS és megosztási engedélyek az Exportálás elérési úton a VMM szolgáltatást. A célkiszolgálón adjon meg egy elérési helyet, ahonnan az adatok importálását történik. Rendelje hozzá az importálási útvonalat ugyanazokkal az engedélyekkel.
* Ha az importálási vagy exportálási elérési út megosztása, rendelje hozzá a távoli számítógépen, amelyen a megosztott megtalálható a VMM szolgáltatás fiókja rendszergazda, a kiemelt felhasználó, a Nyomtatófelelősök vagy a kiszolgáló operátor csoporttagság.
* A futtató fiókokat vegyen fel a gazdagépet, az importálási és exportálási útvonalakra, rendelje hozzá az olvasási és írási engedéllyel a futtató fiókokat a VMM-ben való használata.
* Az importálási és exportálási megosztások nem kell található egy Hyper-V gazdagép-kiszolgálón, amely minden olyan számítógépen, mert visszacsatolási konfiguráció nem támogatott a Hyper-v.
* Az Active Directory minden Hyper-v gazdagép-kiszolgálón szeretné védeni, akkor engedélyezze és konfigurálja a virtuális gépeket tartalmazó által korlátozott delegálás hogy bízzon meg a távoli számítógépeken, amelyeken az importálási és exportálási útvonalak találhatók, az alábbiak szerint:
  1. A tartományvezérlőn nyissa meg a **Active Directory – felhasználók és számítógépek**.
  2. A konzolfán kattintson **tartománynév** > **számítógépek**.
  3. Kattintson a jobb gombbal a Hyper-V gazdagép-kiszolgálónév > **tulajdonságok**.
  4. Az a **delegálás** lapra, majd **a számítógépen csak a megadott szolgáltatások delegálhatók**.
  5. Kattintson a **bármely hitelesítési protokoll**.
  6. Kattintson a **hozzáadása** > **felhasználók és számítógépek**.
  7. Írja be a nevét, a számítógép, amelyen az Exportálás elérési útja > **OK**. Választható szolgáltatások közül, tartsa lenyomva a CTRL billentyűt, és kattintson a **cifs** > **OK**. Ismételje meg a számítógép, amelyen az importálási útvonalat nevét. Ismételje meg a Hyper-V-gazdagép további kiszolgálókat a megfelelő.



## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése
Hálózatleképezés kapcsolatot hoz létre az elsődleges és másodlagos VMM-kiszolgálókon a VMM-Virtuálisgép-hálózatok között:

* Optimális helyezze el a replika virtuális gépek másodlagos Hyper-V-gazdagépek a feladatátvételt követően.
* A replika virtuális gépek csatlakozni megfelelő Virtuálisgép-hálózatok a feladatátvételt követően.

Vegye figyelembe:
- A hálózatleképezés konfigurálható két VMM-kiszolgálókon, vagy a VMM-kiszolgálón egy egyetlen Ha két hely ugyanarra a kiszolgálóra által kezelt Virtuálisgép-hálózatok között.
- Ha leképezést megfelelően van konfigurálva és engedélyezve van a replikáció, az elsődleges helyen a virtuális gépek csatlakoznak-e a hálózati és fogja a replikáját a célhelyen csatlakoznak-e a csatlakoztatott hálózati.
- Hálózatok rendelkezik megfelelően beállítva a VMM-ben a célként megadott Virtuálisgép-hálózat kiválasztásakor hálózatra való leképezés során, ha a forrás Virtuálisgép-hálózatot használó VMM forrás felhők jelenik meg, az elérhető célkiszolgálók Virtuálisgép-hálózatok a cél felhők, amely a védelemhez használt együtt.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének a neve megegyezik az alhálózat, amelyen a forrás virtuális gép is található, majd a replika virtuális géphez csatlakoznak a cél alhálózathoz feladatátvételt követően. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.


Íme egy példa a mechanizmus mutatja be. A következőkben két helyen található Győr és Chicagói rendelkező szervezeteknél.

| **Hely** | **VMM-kiszolgáló** | **A Virtuálisgép-hálózatok** | **Leképezve** |
| --- | --- | --- | --- |
| New York |A VMM-NewYork |VMNetwork1-NewYork |VMNetwork1-Chicagói leképezve |
| VMNetwork2-NewYork |Nincsenek leképezve: | | |
| Chicago |A VMM-Chicagói |VMNetwork1-Chicagói |VMNetwork1-NewYork leképezve |
| VMNetwork2-Chicagói |Nincsenek leképezve: | | |

Az ebben a példában:

* A replika virtuális gép létrehozásakor bármely virtuális géphez csatlakoztatott VMNetwork1-NewYork, VMNetwork1-Chicagóba csatlakoznak.
* Amikor a replika virtuális gép VMNetwork2-NewYork vagy VMNetwork2-Chicagói hoz létre, azt nem csatlakoznak a hálózathoz.

Ez hogyan VMM-felhő beállítása a szervezet példája, és a logikai hálózatok felhőkhöz van társítva.

### <a name="cloud-protection-settings"></a>Felhő védelmi beállításait
| **Védett felhő** | **Felhő védelme** | **Logikai hálózat (New Yorkban)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>NA</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicagói</p> |
| SilverCloud2 |<p>NA</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicagói</p> |

### <a name="logical-and-vm-network-settings"></a>Logikai és a virtuális gép hálózati beállításai
| **Hely** | **Logikai hálózat** | **Társított Virtuálisgép-hálózat** |
| --- | --- | --- |
| New York |LogicalNetwork1-NewYork |VMNetwork1-NewYork |
| Chicago |LogicalNetwork1-Chicagói |VMNetwork1-Chicagói |
| LogicalNetwork2Chicago |VMNetwork2-Chicagói | |

### <a name="target-networks"></a>Célhálózatok
Ezek a beállítások alapján a célként megadott Virtuálisgép-hálózat kiválasztásakor, az alábbi táblázat a lehetőségeket, amelyek elérhetők lesznek.

| **Kiválasztás** | **Védett felhő** | **Felhő védelme** | **A célhálózat érhető el** |
| --- | --- | --- | --- |
| VMNetwork1-Chicagói |SilverCloud1 |SilverCloud2 |Elérhető |
| GoldCloud1 |GoldCloud2 |Elérhető | |
| VMNetwork2-Chicagói |SilverCloud1 |SilverCloud2 |Nincs |
| GoldCloud1 |GoldCloud2 |Elérhető | |


### <a name="failback"></a>Feladat-visszavétel
Mi történik, a feladat-visszavétel (visszirányú replikálás) esetén megtekintéséhez tegyük fel, hogy VMNetwork1-NewYork van leképezve VMNetwork1-Chicago, a következő beállításokkal.

| **Virtuális gép** | **Virtuálisgép-hálózathoz csatlakozik** |
| --- | --- |
| VM1 |VMNetwork1-hálózat |
| Vm2 virtuális gépnek (VM1 replika) |VMNetwork1-Chicagói |

Ezekkel a beállításokkal tekintsük át, mi történik, több lehetséges forgatókönyv szerint.

| **A forgatókönyv** | **Eredménye** |
| --- | --- |
| A feladatátvételt követően VM-2 hálózati tulajdonságok nem módosult. |VM-1 a forrás hálózati kapcsolatban marad. |
| VM-2 hálózati tulajdonságainak a feladatátvételt követően módosulnak, és le van választva. |VM-1 le van választva. |
| VM-2 hálózati tulajdonságainak a feladatátvételt követően módosulnak, és VMNetwork2-Chicagói csatlakozik. |Ha nincs leképezve VMNetwork2-Chicago, VM-1 le lesz választva. |
| Hálózati leképezése VMNetwork1-Chicagói módosul. |VM-1 VMNetwork1-Chicagói most leképezve a hálózathoz csatlakoznak. |


## <a name="prepare-for-single-server-deployment"></a>Az egykiszolgálós telepítés előkészítése


Ha csak egyetlen VMM-kiszolgáló, virtuális gépek replikálhatja a Hyper-V gazdagépek a VMM-felhőben való [Azure](site-recovery-vmm-to-azure.md) vagy másodlagos VMM-felhőhöz. Az első lehetőség zökkenőmentes felhők közötti replikálása nem ajánlott. Ha szeretné, hogy bizonyos felhők replikálása, replikálhatja egyetlen önálló VMM-kiszolgálóhoz, vagy felhőbe archivált Windows fürtben telepített egyetlen VMM-kiszolgálóhoz

### <a name="standalone-vmm-server"></a>Önálló VMM-kiszolgáló

Ebben az esetben az egyetlen VMM-kiszolgáló telepítése virtuális gépként az elsődleges helyen, és a virtuális gép replikálása egy másodlagos helyre, a Site Recovery és a Hyper-V replika használata.

1. **Állítsa be a Hyper-V virtuális gép VMM**. Javasoljuk, hogy az azonos virtuális gépen a VMM által használt SQL Server-példányhoz tartozó-közös elhelyezése. Ez menti a idő csak egy virtuális gépnél létrehozni. Ha a használni kívánt távoli példányát az SQL Server és a nem tervezett kimaradás következik be, kell helyreállítania ezen példányát, mielőtt VMM próbál helyreállítani.
2. **Ellenőrizze a VMM-kiszolgáló rendelkezik-e konfigurálva, legalább két felhők**. Egy felhőalapú tartalmazni fogja a virtuális gépeket szeretne replikálni, és az egyéb felhő erre a célra a másodlagos helyre. A felhőbe, amely tartalmazza a védeni kívánt virtuális gépek meg kell felelnie [Előfeltételek](#prerequisites).
3. Állítsa be a Site Recovery, ebben a cikkben leírtak szerint. Hozzon létre és regisztrálja a VMM-kiszolgálót a tárolóban, replikációs házirend beállítása, és engedélyezze a replikálást. A forrás és cél VMM-nevek ugyanaz lesz. Adja meg, hogy a kezdeti replikáció a hálózaton keresztül történik.
4. A hálózatleképezés beállításához képezze le az elsődleges felhőbe a Virtuálisgép-hálózatot a másodlagos felhőre vonatkozó Virtuálisgép-hálózathoz.
5. A Hyper-V Manager konzolon engedélyezze a Hyper-V replika a Hyper-V gazdagépen, amely tartalmazza a VMM virtuális gép, és engedélyezze a replikálást a virtuális Gépen. Győződjön meg arról, hogy a hely helyreállítását követően annak érdekében, hogy a Hyper-V replika beállításait nem felülbírálják a Site Recovery által védett felhőt a VMM virtuális gép nem tölti fel.
6. A helyreállítási terv feladatátvételi létrehozásakor a forrás és cél használata megegyező VMM-kiszolgálóhoz.
7. Egy teljes leállás feladatátvételt, és a következőképpen helyre:

   1. Nem tervezett feladatátvétel a másodlagos helyen, hogy áthelyezze a feladatokat a másodlagos hely elsődleges VMM virtuális gép a Hyper-V Manager konzolon.
   2. Győződjön meg arról, hogy a VMM virtuális gép működik-e, és fut, és a tárolóban, nem tervezett feladatátvétel az elsődleges, másodlagos a felhőből a virtuális gépek a feladatátvétel. Véglegesítse a feladatátvételt, és válassza ki a másik helyreállítási pontot, ha szükséges.
   3. A nem tervezett feladatátvétel végeztével összes erőforrás elérhető az elsődleges hely újra.
   4. Az elsődleges hely újra, a Hyper-V Manager konzol a másodlagos helyen érhető el, ha a VMM virtuális gép fordított replikáció engedélyezése. A másodlagos az elsődleges replikáció a virtuális gép elindul.
   5. A tervezett feladatátvétel végrehajtása a Hyper-V Manager konzolon a másodlagos helyen, hogy áthelyezze a feladatokat a VMM virtuális gép az elsődleges helyre. Hajtsa végre a feladatátvételt. A fordított replikáció, majd engedélyezze, hogy a VMM virtuális gép újból replikálódik az elsődleges másodlagos.
   6. A Recovery Services-tároló engedélyezheti a munkaterhelési virtuális gépek replikálásához azokat az elsődleges másodlagos elindítani a fordított replikációt.
   7. A Recovery Services-tároló futtassa a tervezett feladatátvételt a feladat-visszavételt a munkaterhelési virtuális gépekhez az elsődleges helyre. A feladatátvételt fejezze be a véglegesítése. Engedélyezze a fordított replikációt az elsődleges, másodlagos történő replikálása a munkaterhelési virtuális gépek indítására.

### <a name="stretched-vmm-cluster"></a>Felhőbe archivált VMM-fürthöz

Helyett egy önálló VMM-kiszolgáló telepítése egy másodlagos helyre replikált virtuális gépként, akkor is használhatja a VMM magas rendelkezésre állású Windows feladatátvevő fürtben virtuális gépként telepíti azokat. Így lehetővé teszi a rugalmas munkaterhelést és a hardver meghibásodása elleni védelem. A Site Recovery telepítése a VMM-Virtuálisgép kell telepíteni a felhőbe archiválási fürt földrajzilag különálló helyen. Ehhez tegye a következőket:

1. Telepítse a VMM egy virtuális gépen egy Windows feladatátvevő fürtben, és jelölje be a futtató a kiszolgáló magas rendelkezésre állású telepítés során.
2. A VMM által használt SQL Server-példány replikálni kell az SQL Server AlwaysOn rendelkezésre állási csoportok, úgy, hogy az adatbázis másolatát a másodlagos helyen.
3. Kövesse az utasításokat ebben a cikkben, hozzon létre egy tárolót, a kiszolgáló regisztrálásához és védelmének beállítása. A Recovery Services-tárolónak a fürt minden VMM-kiszolgálóhoz regisztrálnia kell. Ehhez az szükséges, telepítse a szolgáltató aktív csomópontra, és regisztrálja a VMM-kiszolgálót. A szolgáltató telepíti a többi csomóponton.
4. Nem tervezett kimaradás esetén a VMM-kiszolgáló és a megfelelő SQL Server-adatbázist átadja a feladatokat, és a másodlagos hely érhető el.



## <a name="prepare-for-storage-mapping"></a>Tárolásleképezés előkészítése


Tárolás beállítása leképezés által leképezési tárhelybesorolások a forrás és cél VMM-kiszolgálók számára a következőket:

  * **Azonosítsa a célként megadott replika virtuális gépek**– A forrás virtuális gép merevlemez replikálja a megadott tároló (SMB-megosztás vagy a fürt megosztott kötetei (CSV)) a célhelyen.
  * **A replika virtuális gépek elhelyezése**– tárolási hozzárendelés optimális helyezhető el a replika virtuális gépek a Hyper-V gazdakiszolgálókra használatos. Replika virtuális gépeket helyez el a gazdagépekhez, amelyek férhetnek hozzá a csatlakoztatott besorolása.
  * **Nincs tárhely leképezés**– Ha nem adja meg tároló leképezési, virtuális gépek a Hyper-V gazdakiszolgáló a replika virtuális géphez társított megadott alapértelmezett tárolási helyét replikálja.

Vegye figyelembe:
- Beállíthat egy kiszolgálón két VMM-felhők közötti leképezést.
- Tárhelybesorolások a forrás és cél-felhőkben található gazdagépcsoportokhoz elérhetőnek kell lennie.
- Besorolások tárolási ugyanolyan nem szükséges. Például a forrás besorolást, amely tartalmazza a cél a besoroláshoz, amely tartalmazza a megosztott fürtkötetek SMB-megosztások is leképezheti.

### <a name="example"></a>Példa
Besorolások helyesen vannak konfigurálva a VMM-ben tárolási leképezés során a forrás és cél VMM-kiszolgáló kiválasztásakor, ha a forrás és cél besorolások jelenik meg. Íme egy példa, tárolási fájlmegosztásokat és besorolása két helyen található Győr és Chicagói rendelkező szervezeteknél.

| **Hely** | **VMM-kiszolgáló** | **A fájlmegosztás (forrás)** | **Besorolási (forrás)** | **Leképezve** | **A fájlmegosztás (cél)** |
| --- | --- | --- | --- | --- | --- |
| New York |VMM_Source |SourceShare1 |ARANY |GOLD_TARGET |TargetShare1 |
| SourceShare2 |EZÜST |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZ |BRONZE_TARGET |TargetShare3 | | |
| Chicago |VMM_Target | |GOLD_TARGET |Nincsenek leképezve: | |
|  |SILVER_TARGET |Nincsenek leképezve: | | | |
|  |BRONZE_TARGET |Nincsenek leképezve: | | | |

Az ebben a példában:

* A replika virtuális gép létrehozásakor bármely virtuális gép, GOLD tárhelyen (SourceShare1), egy GOLD_TARGET (TargetShare1) tárhelyre replikálja.
* A virtuális gépek EZÜST tárhelyen (SourceShare2) a replika virtuális gép létrehozásakor a SILVER_TARGET (TargetShare2) tárhelyre replikálja, és így tovább.

### <a name="multiple-storage-locations"></a>Több tárolóhelyek
Ha a célként megadott besorolási több SMB-megosztások vagy CSV-kben van rendelve, optimális tárolási helyét automatikusan ki lesz választva a virtuális gép védett esetében. Ha nincs megfelelő célként megadott érhető el a megadott besorolású, megadva a Hyper-V gazdagép alapértelmezett tárolási helyét helyezze el a replika virtuális merevlemezek szolgál.

A következő táblázat bemutatják, hogyan tárhelybesorolás és megosztott fürtkötetek beállítása a fenti példában.

| **Hely** | **Besorolás** | **A társított tárolási** |
| --- | --- | --- |
| New York |ARANY |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| EZÜST |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| Chicago |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

A táblázat összefoglalja a viselkedést, ha engedélyezi a védelmet a virtuális gépek (VM1 - VM5) ebben a példában környezetben.

| **Virtuális gép** | **Forrás tároló** | **Forrás besorolás** | **Hozzárendelt céltárolót** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |ARANY |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Mindkét GOLD_TARGET</p> |
| VM2 VIRTUÁLIS GÉPNEK |\\FileServer\SourceShare1 |ARANY |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Mindkét GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |EZÜST |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |EZÜST |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Mindkét SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |N/A |Nincs leképezés, így a Hyper-V gazdagép alapértelmezett tárolási helyét szolgál |



### <a name="data-privacy-overview"></a>Az adatvédelmi áttekintése

A táblázat összefoglalja, ebben a forgatókönyvben az adatok tárolási módját:

- - -
| Műveletek | **Részletek** | **Összegyűjtött adatok** | **Használat** | **Szükséges** |
| --- | --- | --- | --- | --- |
| **Regisztráció** | Recovery Services-tároló rögzítheti egy VMM-kiszolgáló. Ha később meg szeretne a kiszolgáló regisztrációját, akkor törölni kell a kiszolgáló adatait az Azure-portálon teheti meg. | A VMM-kiszolgáló regisztrálása után a Site Recovery gyűjti, eljárások, és átviszi a VMM-kiszolgáló és a Site Recovery által észlelt a VMM-felhő metaadatait. | Az adatok segítségével azonosíthatja és a megfelelő a VMM-kiszolgálóval való kommunikációhoz, és megfelelő VMM-felhő beállításainak konfigurálása. | Ez a szolgáltatás szükség. Ha nem kívánja elküldeni ezeket az információkat a Site Recovery szolgáltatásban, ne használja a Site Recovery szolgáltatásban. |
| **A replikáció engedélyezése** | Az Azure Site Recovery Provider telepítve van a VMM-kiszolgáló és az átjáró számára a Site Recovery szolgáltatással folytatott kommunikáció. A szolgáltató nem található a VMM folyamat egy dinamikus csatolású függvénytár (DLL). A szolgáltató telepítése után a "Adatközpont-helyreállítási" lekérdezi engedélyezve van a VMM felügyeleti konzolon. Új és meglévő virtuális gépek engedélyezheti a funkció lehetővé teszi a virtuális gép védelmét. |E tulajdonság készlet a szolgáltató elküldi a Site Recovery nevét és a virtuális gép azonosítója.  Windows Server 2012 vagy Windows Server 2012 R2 Hyper-V replika engedélyezve van a replikáció. A virtuális gép adatok replikálását, egy Hyper-V gazdagépről egy másikra (általában a különböző "helyreállítási" adatközpontban található). |A Site Recovery használja a metaadatok feltöltése a Virtuálisgép-adatok az Azure portálon. | Ez a szolgáltatás a szolgáltatás nagyon fontos részét képezik, és nem kapcsolható ki. Ha nem kívánja elküldeni ezeket az információkat, nem engedélyezi a Site Recovery által védett virtuális gépekre vonatkozóan. Vegye figyelembe, hogy a Site Recovery szolgáltatásban a szolgáltató által küldött minden adathoz HTTPS-KAPCSOLATON keresztül zajlik. |
| **Helyreállítási terv** | A helyreállítási terv segíteni az orchestration tervének a helyreállítás adatközpont. Megadhatja, hogy a sorrendet, amelyben virtuális gépek vagy virtuális gépek csoportjának el kell indítani a helyreállítási helyen. Futtatási vagy bármilyen, kézi művelet végrehajtását, az egyes virtuális gépek helyreállítási idején kell automatizált parancsfájlokat is megadható. Feladatátvételi általában elindul a helyreállítási terv szinten koordinált helyreállítási. | A Site Recovery gyűjti, folyamatokat, és továbbítja a metaadatok a helyreállítási terv, beleértve a virtuális gép metaadatait, és bármilyen automatizálási parancsfájlokat és a manuális műveletet megjegyzések metaadatait. |A metaadatok segítségével hozhatók létre a helyreállítási tervben az Azure portálon. |Ez a szolgáltatás a szolgáltatás nagyon fontos részét képezik, és nem kapcsolható ki. Ha nem kívánja elküldeni ezeket az információkat a Site Recovery szolgáltatásban, ne hozzon létre helyreállítási tervek. |
| **Hálózatleképezés** | A Maps hálózati adatait az elsődleges adatközpont a helyreállítás adatközpont. Ha virtuális gépeket a helyreállítási helyen helyreállítás, hálózatra való leképezést a hálózati kapcsolatot lehessen segítségével. |A Site Recovery gyűjti, folyamatokat, és továbbítja a minden hely (elsődleges és datacenter) tartozó logikai hálózatok metaadatait. |A metaadatok feltöltése a hálózati beállításokat, így is leképezheti a hálózati szolgál. | Ez a szolgáltatás a szolgáltatás nagyon fontos részét képezik, és nem kapcsolható ki. Ha nem kívánja elküldeni ezeket az információkat a Site Recovery szolgáltatásban, ne használja a hálózat leképezését. |
| **A feladatátvétel (tervezett/nem tervezett/vizsgálat)** | Feladatátvételi feladatátadás a virtuális gépek egy VMM által felügyelt adatközpontból a másikba. A feladatátvételi művelet manuálisan elindul az Azure portálon. |A VMM-kiszolgálón a szolgáltató Site Recovery által a feladatátvételi esemény értesítést kap, és a feladatátvételi művelet futtatja a Hyper-V gazdagép VMM felületeken keresztül. A virtuális gépek tényleges feladatátvétel során egy másik és kezelt Windows Server 2012 vagy Windows Server 2012 R2 Hyper-V replika a Hyper-V gazdagépek. Lehetne a Site Recovery feltölti a feladatátvételi művelet adatokat az Azure portálon állapotának küldött adatok használja. | Ez a szolgáltatás a szolgáltatás nagyon fontos részét képezik, és nem kapcsolható ki. Ha nem kívánja elküldeni ezeket az információkat a Site Recovery szolgáltatásban, ne használjon feladatátvételi. |

## <a name="next-steps"></a>Következő lépések

A központi telepítés tesztelését, követően további információ más típusú [feladatátvételi](site-recovery-failover.md)
