---
title: "Hyper-V virtuális gépek replikálása az Azure-bA a klasszikus portálon |} Microsoft Docs"
description: "Ez a cikk ismerteti a Hyper-V virtuális gépek replikálása Azure-ba, amikor a gépek nem felügyel a VMM-felhőkben."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 3f4c4483-e3dd-495a-bd02-c16e9e28c88d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/21/2017
ms.author: raynew
ms.openlocfilehash: 438f32ee3605e2dd0c46de7993a359cc269262fe
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-without-vmm-with-azure-site-recovery"></a>A helyszíni Hyper-V virtuális gépek és az Azure (VMM nélkül) az Azure Site Recovery közötti replikáció
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Klasszikus portál](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

Ez a cikk ismerteti a helyszíni Hyper-V virtuális gépek replikálása Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatáshoz, az Azure portálon. Ebben az esetben a Hyper-V kiszolgáló nem kezelt VMM-felhőkben.

A cikk elolvasása után bármely fűzhetnek alsó, vagy a műszaki kérdései a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon

Az Azure két különböző [üzembe helyezési modellt](../resource-manager-deployment-model.md) kínál az erőforrások létrehozására és kezelésére: az Azure Resource Manager-modellt és a klasszikus modellt. Az Azure emellett kétféle portállal rendelkezik, a klasszikus Azure-portállal és az Azure Portal portállal.

A cikkből megtudhatja, hogyan telepítheti a klasszikus portálon. A klasszikus portál a meglévő tárolók fenntartására használható. Új tárolók nem hozhatók létre a klasszikus portál használatával.

## <a name="site-recovery-in-your-business"></a>A Site Recovery szerepe a vállalatban

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. A Site Recovery a következőket kínálja:

* Külső helyszíni védelem a Hyper-V virtuális gépeken futó üzleti alkalmazások számára.
* Egyetlen helyen állíthatja be, kezelheti és figyelheti a replikációt, a feladatátvételt és a helyreállítást.
* Egyszerű feladatátadás az Azure-nak, majd feladatátvétel (helyreállítás) az Azure-ból a helyszíni Hyper-V-gazdakiszolgálókra.
* Több virtuális gépet tartalmazó helyreállítási tervek, így az egy szinthez tartozó alkalmazások számítási feladatai együtt hajtják végre a feladatátvételt.

## <a name="azure-prerequisites"></a>Azure-előfeltételek
* Szüksége van egy [Microsoft Azure](https://azure.microsoft.com/)-fiókra. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
* A replikált adatok tárolásához Azure-tárfiókra van szükség. A fiókot kell georeplikáció engedélyezve van. És az Azure Site Recovery-tárolónak ugyanabban a régióban legyen, és társítható ugyanahhoz az előfizetéshez. [További tudnivalók az Azure storage](../storage/common/storage-introduction.md). Vegye figyelembe, hogy létre áthelyezése tárfiókok nem támogatottak a [új Azure-portálon](../storage/common/storage-create-storage-account.md) erőforráscsoportok között.
* Szüksége lesz egy Azure virtuális hálózatra, hogy az Azure virtuális gépek csatlakoznak-e a hálózaton történő feladatátadást követően az elsődleges helyről.

## <a name="hyper-v-prerequisites"></a>Hyper-V előfeltételei
* A forráshely helyszíni szüksége lesz egy vagy több futtató **Windows Server 2012 R2** telepítve van a Hyper-V szerepkör vagy **Microsoft Hyper-V Server 2012 R2**. Ez a kiszolgáló a következőket:
* Egy vagy több virtuális gépet tartalmaznia.
* Kapcsolódnia kell az internetre, közvetlen vagy proxyn keresztülmenő.
* Fut a Tudásbáziscikkben leírt javítások [2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977").

## <a name="virtual-machine-prerequisites"></a>A virtuális gép előfeltételei
Védeni kívánt virtuális gépek meg kell felelnie az [Azure virtuális gép követelményeinek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="provider-and-agent-prerequisites"></a>Provider és agent Előfeltételek
Az Azure Site Recovery üzembe helyezése során telepítse az Azure Site Recovery Provider és az Azure Recovery Services Agent minden Hyper-V kiszolgálón. Vegye figyelembe:

* Javasoljuk, hogy mindig futtassa a Provider és agent legújabb verzióját. Elérhetők a Site Recovery portálon.
* Minden Hyper-V kiszolgáló az adott tárolóban kell rendelkeznie a Provider és agent azonos verzióit.
* A kiszolgálón futó Provider az interneten keresztül kapcsolódik a Site Recovery. Ehhez a proxy nélkül a Hyper-V kiszolgálón jelenleg konfigurált proxybeállításokat, vagy az Egyéni proxybeállítások szolgáltató telepítése közben konfigurálhatók. Győződjön meg arról, hogy a használni kívánt proxykiszolgáló hozzáférhet ezeket az URL-címek az Azure-ba történő összekapcsolására vonatkozó lesz szüksége:

  * *.accesscontrol.windows.net
  * *.backup.windowsazure.com
  * *.hypervrecoverymanager.windowsazure.com
  * *.store.core.windows.net      
  * *.blob.core.windows.net
  - https://www.msftncsi.com/ncsi.txt
  - time.windows.com
  - time.nist.gov
* Továbbá lehetővé teszi az IP-címek ismertetett [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) és a HTTPS (443) protokollt. Ahhoz, hogy a használni kívánt Azure-régiót IP-címtartományok és az USA nyugati régiója rendelkezik.

Az ábrán látható, a különböző kommunikációs csatornák és a vezénylési és a replikálás a Site Recovery által használt portok

![B2A topológia](./media/site-recovery-hyper-v-site-to-azure-classic/b2a-topology.png)

## <a name="step-1-create-a-vault"></a>1. lépés: A tároló létrehozása
1. Jelentkezzen be a [kezelési portál](https://portal.azure.com).
2. Bontsa ki a **adatszolgáltatások** > **Recovery Services** kattintson **Site Recovery-tároló**.
3. Kattintson az **Új létrehozása** > **Gyorslétrehozás** elemre.
4. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet.
5. A **Régió** mezőben válassza ki a tároló földrajzi régióját. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Kattintson a **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-hyper-v-site-to-azure-classic/vault.png)

Az állapotsorban ellenőrizheti, hogy a tároló sikeresen létrejött-e. Ezt követően a tároló **Aktív** állapottal jelenik meg a Recovery Services főoldalán.

## <a name="step-2-create-a-hyper-v-site"></a>2. lépés: Hyper-V hely létrehozása
1. A Recovery Services lapon kattintson arra a tárolóra az első lépések lap megnyitásához. Az Első lépések bármikor megnyitható az ikon segítségével is.

    ![Első lépések](./media/site-recovery-hyper-v-site-to-azure-classic/quick-start-icon.png)
2. A legördülő listában válassza ki a **egy helyszíni Hyper-V hely és az Azure közötti**.

    ![Hyper-V hely forgatókönyv](./media/site-recovery-hyper-v-site-to-azure-classic/select-scenario.png)
3. A **hozzon létre egy Hyper-V hely** kattintson **hozzon létre Hyper-V hely**. Adjon meg egy helynevet, és mentse.

    ![Hyper-V-hely](./media/site-recovery-hyper-v-site-to-azure-classic/create-site.png)

## <a name="step-3-install-the-provider-and-agent"></a>3. lépés: A szolgáltató és az ügynök telepítése
A szolgáltató és az ügynök telepítése minden védeni kívánt virtuális gépeket tartalmazó Hyper-V kiszolgálón.

Hyper-V fürt telepítése, az lépéseket 5-11 hajtja végre a feladatátvevő fürt mindegyik csomópontján. Miután regisztrált összes csomópontját, és engedélyezve van a védelem, virtuális gépek védi akkor is, ha az áttelepítés után a fürt csomópontjai között.

1. A **készítse elő a Hyper-V kiszolgálók**, kattintson a **a regisztrációs kulcs letöltése** fájlt.
2. A a **regisztrációs kulcs letöltése** kattintson **letöltése** mellett a helyhez. Töltse le a kulcsot egy biztonságos helyre, a Hyper-V kiszolgáló által könnyen elérhető. Ezt követően 5 napig érvényes a kulcs.

    ![Regisztrációs kulcs](./media/site-recovery-hyper-v-site-to-azure-classic/download-key.png)
3. Kattintson a **a letöltése** a legújabb verzió beszerzéséhez.
4. Futtassa a fájlt szeretne regisztrálni a tárolóban lévő minden Hyper-V kiszolgálón. A fájl két összetevőt telepíti:
   * **Az Azure Site Recovery Provider**– kezeli a kommunikációs és vezénylési között a Hyper-V kiszolgáló és az Azure Site Recovery portálon.
   * **Az Azure Recovery Services Agent**– kezeli az adatokat átvitel a forrás Hyper-V kiszolgáló és az Azure storage futó virtuális gépek között.
5. A **Microsoft Update** lapon kérheti a frissítések telepítését. Engedélyezi ezt a beállítást, a Provider és Agent frissítések a Microsoft Update-szabályzatnak megfelelően telepíteni fogja.

    ![Microsoft Update](./media/site-recovery-hyper-v-site-to-azure-classic/provider1.png)
6. A **telepítési** adja meg, ahol a Provider és Agent telepítése a Hyper-V kiszolgálón.

    ![Telepítés helye](./media/site-recovery-hyper-v-site-to-azure-classic/provider2.png)
7. Telepítés befejezése után regisztrálja a kiszolgálót a tárolóban való telepítés folytatásához.
8. Az a **tároló beállításait** kattintson **Tallózás** válassza ki a fő fájlt. Adja meg az Azure Site Recovery-előfizetést, a tároló neve és a Hyper-V hely, amelyhez a Hyper-V kiszolgáló tartozik.

    ![Kiszolgáló regisztrációja](./media/site-recovery-hyper-v-site-to-azure-classic/provider8.PNG)
9. Az a **internetkapcsolat** lapra jut, hogyan csatlakozzon a Provider az Azure Site Recovery. A kiszolgálón beállított alapértelmezett internetkapcsolat-konfiguráció használatához válassza a **Use default system proxy settings** (Alapértelmezett rendszerproxy-beállítások használata) lehetőséget. Ha nem adja meg egy értéket az alapértelmezett beállításokat lesz.

   ![Internetbeállítások](./media/site-recovery-hyper-v-site-to-azure-classic/provider7.PNG)
10. Elindul a regisztráció után regisztrálja a kiszolgálót a tárolóban lévő állapottal.

    ![Kiszolgáló regisztrációja](./media/site-recovery-hyper-v-site-to-azure-classic/provider15.PNG)
11. Regisztráció metaadatok befejezését követően át a Hyper-V kiszolgáló Azure Site Recovery által lekért és a kiszolgáló megjelenik a **Hyper-V helyek** lapján a **kiszolgálók** lap a tárolóban lévő állapottal.

### <a name="install-the-provider-from-the-command-line"></a>A szolgáltató telepítése a parancssorból
Másik megoldásként az Azure Site Recovery Provider a parancssorból is telepítheti. Ezt a módszert kell használnia, ha azt szeretné, hogy a szolgáltató telepítéséhez Windows Server Core 2012 R2 rendszert futtató számítógépeken. Futtassa a parancssorból a következőképpen:

1. Töltse le egy mappába a Provider telepítőfájlját és a regisztrációs kulcsot. A mappa lehet például a következő: C:\ASR.
2. A parancssor futtatása a rendszergazda, írja be:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Telepítse a szolgáltató futtatásával:

        C:\ASR> setupdr.exe /i
4. Futtassa a következő regisztrálást:

        CD C:\Program Files\Microsoft Azure Site Recovery Provider
        C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Ha paraméterek a következők:

* **/ Credentials**: a letöltött regisztrációs kulcs elérési útja.
* **/ FriendlyName**: Adjon meg egy nevet a Hyper-V gazdakiszolgáló azonosításához. Ez a név megjelenik a portálon
* **/ EncryptionEnabled**: nem kötelező. Adja meg, hogy a replika virtuális gépek az Azure-ban (inaktív adatok titkosítása) a titkosításához.
* **/ proxyaddress**; **/proxyport**; **/proxyusername**; **/proxypassword**: nem kötelező. Adja meg a proxy paraméterek, ha egyéni proxyt használni kívánt, vagy az aktuálisan használt proxy hitelesítést igényel.

## <a name="step-4-create-an-azure-storage-account"></a>4. lépés: Azure-tárfiók létrehozása
* A **erőforrás előkészítése** válasszon **Storage-fiók létrehozása** Azure storage-fiók létrehozásához, ha még nem rendelkezik ilyennel. A fióknak rendelkeznie kell georeplikáció engedélyezve van. És az Azure Site Recovery-tárolónak ugyanabban a régióban legyen, és társítható ugyanahhoz az előfizetéshez.

    ![Storage-fiók létrehozása](./media/site-recovery-hyper-v-site-to-azure-classic/create-resources.png)

> [!NOTE]
> 1. A rendszer nem támogatja az áthelyezés használatával létrehozott tárfiókok a [új Azure-portálon](../storage/common/storage-create-storage-account.md) erőforráscsoportok között.
> 2. A Site Recovery üzembe helyezéséhez használt tárfiókok esetében a [Migration of storage accounts](../azure-resource-manager/resource-group-move-resources.md) (Tárfiókok áttelepítése) művelet az egy előfizetésen belüli erőforráscsoportok között vagy előfizetések között nem támogatott.
>

## <a name="step-5-create-and-configure-protection-groups"></a>5. lépés: Csoportok létrehozása és konfigurálása védelme
Védelmi csoport azonos védelmi konfigurációval segítségével védeni kívánt virtuális gépek logikai csoportosításain alapulnak. A védelmi csoport védelmi beállításainak alkalmaz, és ezek a beállítások érvényesek minden, a csoporthoz hozzáadni kívánt virtuális gépnek.

1. A **létrehozása és a védelmi csoportok konfigurálása** kattintson **védelmi csoport létrehozása**. Ha bármely előfeltételek nem teljesülnek egy üzenet jelenik meg, és kattintson **részleteinek megtekintéséhez** további információt.
2. Az a **védelmi csoportok** lapon vegye fel egy védelmi csoportot. Adjon meg egy nevet, a forráshely Hyper-V, a cél **Azure**, az Azure Site Recovery-előfizetés nevét, és az Azure storage-fiók.

    ![Védelmi csoport](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group.png)
3. A **replikációs beállítások** állítsa be a **másolás gyakorisága** adhatja meg, milyen gyakran az adatok különbözeti szinkronizálnia kell a forrása és célja között. Beállíthat 30 másodperces, 5 perc és 15 perc.
4. A **helyreállítási pontok megőrzése** adja meg, hogy hány órán helyreállítási előzmények kell tárolni.
5. A **alkalmazáskonzisztens pillanatképek gyakorisága** megadhatja, hogy a kötet árnyékmásolata szolgáltatás (VSS) segítségével győződjön meg arról, hogy a alkalmazások konzisztens állapotban van, a pillanatkép készítésének időpontjában pillanatfelvételeket-e. Alapértelmezés szerint ezek nincsenek használatban. Győződjön meg arról, hogy ez a változó értéke kisebb, mint a további helyreállítási pontok száma. Ez csak akkor támogatott, ha a virtuális gép Windows operációs rendszert futtat.
6. A **kezdeti replikáció kezdési ideje** adja meg a védelmi csoportban lévő virtuális gépek kezdeti replikációja, mikor legyenek elküldve az Azure-bA.

    ![Védelmi csoport](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group2.png)

## <a name="step-6-enable-virtual-machine-protection"></a>6. lépés: Virtuális gép védelmének engedélyezése
Virtuális gépek felvétele a védelmi csoport kívánja engedélyezni a védelmet a számukra.

> [!NOTE]
> A statikus IP-című linuxos virtuális gépek számára nem biztosítható védelem.
>
>

1. Az a **gépek** a védelmi csoport lapján, kattintson ** virtuális gépek hozzáadása a védelmi csoportok védelmi ** engedélyezése.
2. Az a **a virtuális gép védelmének engedélyezése** oldalon válassza ki a védeni kívánt virtuális gépeket.

    ![A virtuális gép védelmének engedélyezése](./media/site-recovery-hyper-v-site-to-azure-classic/add-vm.png)

    A védelem engedélyezése feladatok kezdődik. A végrehajtás nyomon a **feladatok** fülre. A védelem véglegesítése feladat futtatása után a virtuális gép készen áll a feladatátvételre.
3. Védelmi után van beállítása, hogy a következőket teheti:

   * A virtuális gépek megtekintése **védett elemek** > **védelmi csoportok** > *protectiongroup_name*  >  **Virtuális gépek** le a gép összes adatát megtekintheti a **tulajdonságok** lapon...
   * A virtuális gépek a feladatátvétel tulajdonságainak konfigurálása **védett elemek** > **védelmi csoportok** > *protectiongroup_name*  >  **Virtuális gépek** *virtual_machine_name* > **konfigurálása**. Konfigurálhatja:

     * **Név**: az a virtuális gép nevét, az Azure-ban.
     * **Méret**: a virtuális gépet, amely átadja a feladatokat a cél méretét.

       ![Virtuális gép tulajdonságainak konfigurálása](./media/site-recovery-hyper-v-site-to-azure-classic/vm-properties.png)
   * További virtuális gép beállítások konfigurálása *védett elemek** > **védelmi csoportok** > *protectiongroup_name*  >   **Virtuális gépek** *virtual_machine_name* > **konfigurálása**, többek között a következőket:

     * **A hálózati adapterek**: A hálózati adapterek számát adja meg, ha a cél virtuális gép méretét határozza meg. Ellenőrizze [virtuális gép mérete specifikációk](../virtual-machines/linux/sizes.md) számára a virtuális gép mérete által támogatott hálózati adapterek száma.

       Amikor módosítja a virtuális gép méretét, és menti a beállításokat, a **Configure** (Konfiguráció) lap következő megnyitásakor látni fogja, hogy a hálózati adapterek száma megváltozott. A cél virtuális gépek hálózati adapterek száma nem minimális a forrás virtuális gép hálózati adapterek száma és a hálózati adapterek a kiválasztott virtuális gép mérete által támogatott maximális számát. Azt az alábbi leírásban is látható:

       * Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
       * Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
       * Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.

     * **Azure-hálózat**: Adja meg a hálózatot, amelyhez a virtuális gép átadják a. Ha a virtuális gépnek több hálózati adaptert kell az összes adapter csatlakozik az azonos Azure-hálózatot.
     * **Alhálózati** az összes hálózati adapteren a virtuális gépen, jelölje ki az alhálózatot, amelyhez a gép a feladatátvételt követően kell csatlakozni az Azure-hálózatot.
     * **Cél IP-címe**: Ha a forrás virtuális gép hálózati adaptert statikus egy IP-cím ezután megadhatja, hogy a cél virtuális gép győződjön meg arról, hogy a gép rendelkezik-e a feladatátvételt követően ugyanazt a címet az IP-cím használatára van konfigurálva.  Ha nem adja meg egy IP-cím majd bármely elérhető címek lesznek osztva a feladatátvétel időpontjában. Ha megad egy címet, amely használatban van a feladatátvétel sikertelen lesz.

     > [!NOTE]
     > A Site Recovery üzembe helyezéséhez használt hálózatok esetében a [Migration of networks](../azure-resource-manager/resource-group-move-resources.md) (Hálózatok áttelepítése) művelet az egy előfizetésen belüli erőforráscsoportok között vagy előfizetések között nem támogatott.
     >

     ![Virtuális gép tulajdonságainak konfigurálása](./media/site-recovery-hyper-v-site-to-azure-classic/multiple-nic.png)




## <a name="step-7-create-a-recovery-plan"></a>7. lépés: A helyreállítási terv létrehozása
Ahhoz, hogy a központi telepítés tesztelése futtathatja egyetlen virtuális gép vagy egy vagy több virtuális gépet tartalmazó helyreállítási terv feladatátvételi tesztjét. [További](site-recovery-create-recovery-plans.md) : a helyreállítási terv létrehozása.

## <a name="step-8-test-the-deployment"></a>8. lépés: A központi telepítés tesztelése
Az Azure-ba irányuló feladatátvételi tesztet két különböző módon futtathatja.

* **Feladatátvételi teszt Azure-hálózat nélkül** – Ez a típus azt ellenőrzi, hogy a virtuális hálózat megfelelően feláll-e az Azure-ban. A virtuális gép a feladatátvételt követően nem csatlakozik Azure-hálózathoz.
* **Az Azure-hálózatot a feladatátvételi teszt**– ilyen típusú feladatátvételi ellenőrzi, hogy az egész replikációs környezet megfelelően feláll-e, és hogy a virtuális gépek a feladatátvételt a megadott cél Azure-hálózathoz csatlakozik. Vegye figyelembe, hogy a feladatátvételi teszthez a teszt virtuális gép alhálózata lesz kell szerepelnek a replika virtuális gép alhálózat alapján. Ez eltér a szokásos replikációtól, amelynek keretében a replika virtuális gép alhálózata a forrás virtuális gép alhálózatán alapul.

Ha szeretné futtatni a feladatátvételi teszt Azure-hálózat megadása nélkül nem kell előkészületeket végeznie.

Ha cél Azure-hálózat megadása mellett szeretné futtatni a feladatátvételi tesztet, hozzon létre egy, az éles Azure-hálózattól elkülönített új Azure-hálózatot (az új Azure-hálózatok létrehozásánál ez az alapértelmezés). Olvasási [feladatátvételi teszt futtatása](site-recovery-failover.md) további részleteket.

A replikáció- és hálózati telepítés teljes tesztelésére lesz szüksége, hogy működjön, a replikált virtuális gép várható az infrastruktúra beállítása. Ezzel a tartományvezérlőként, DNS-beli virtuális gép beállítását, és replikálja az Azure Site Recovery segítségével hozza létre a tesztelési célú hálózat a feladatátvételi teszt futtatásával egyik módja.  [További](site-recovery-active-directory.md#test-failover-considerations) teszt feladatátvételi szempontokat részletező cikket az Active Directory kapcsolatban.

Feladatátvételi teszt futtatása az alábbiak szerint:

> [!NOTE]
> Ahhoz, hogy a lehető legjobb teljesítményt érhesse el az Azure-ba irányuló feladatátvétel során, ellenőrizze, hogy telepítette-e az Azure Agent ügynököt a védett gépen. Az ügynök segít a rendszerindítás felgyorsításában és az esetlegesen felmerülő problémák diagnosztizálásában. A linuxos ügynök [itt](https://github.com/Azure/WALinuxAgent) található, a windowsos ügynök pedig [itt](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

1. A **Helyreállítási tervek** lapon válassza ki a kívánt tervet, majd kattintson a **Feladatátvételi teszt** lehetőségre.
2. A **Confirm Test Failover** (Feladatátvételi teszt megerősítése) lapon válassza a **None** (Nincs) lehetőséget, vagy a kívánt Azure-hálózatot.  Vegye figyelembe, hogy ha **nincs** feladatátvételi teszt ellenőrzi, hogy a virtuális gép Azure-felhőbe replikált megfelelően, de nem ellenőrzi a replikációs hálózat konfigurációját.

    ![A feladatátvételi teszt](./media/site-recovery-hyper-v-site-to-azure-classic/test-nonetwork.png)
3. A **Feladatok** lapon nyomon követheti a feladatátvétel előrehaladását. A virtuális gép tesztelési replikájának meg kell jelennie az Azure portálon. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.
4. Ha a feladatátvételi művelet elér a **Teszt befejezése** fázisba, a feladatátvételi teszt befejezéséhez kattintson a **Teszt végrehajtása** lehetőségre. A feladatátvételi folyamat előrehaladásának és állapotának megtekintéséhez, illetve a szükséges műveletek elvégzéséhez nyissa meg a **Feladatok** lapot.
5. A feladatátvételt követően a virtuális gép tesztelési replikájának meg kell jelennie az Azure portálon. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.

   1. Ellenőrizze, hogy a virtuális gépek sikeresen elindulnak-e.
   2. Ha a feladatátvételt követően távoli asztali kapcsolat segítségével szeretne csatlakozni a virtuális géphez, még a feladatátvételi teszt futtatása előtt engedélyezze a távoli asztali kapcsolat funkciót a virtuális gépen. Akkor adja hozzá egy RDP-végpontot a virtuális gépen is. Kihasználhatja a [Azure automation-runbook](site-recovery-runbook-automation.md) ehhez.
   3. Ha a feladatátvételt követően nyilvános IP-cím és a távoli asztal funkció segítségével kapcsolódik az Azure virtuális géphez, ellenőrizze, hogy nincsenek-e érvényben olyan tartományi szabályzatok, amelyek megakadályozzák a virtuális gépek nyilvános címekről való elérését.
6. A tesztelés befejezését követően tegye a következőket:

   * Kattintson **A feladatátvételi teszt befejeződött** elemre. Számolja fel a tesztkörnyezetet. lehetőségre a teszteléshez használt virtuális gép automatikus kikapcsolásához és törléséhez.
   * A feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez és mentéséhez kattintson a **Jegyzetek** elemre.
7. Ha a feladatátvétel elérte a **teszt befejezése** fázis Befejezés az ellenőrzés az alábbiak szerint:
   1. Tekintse meg a replika virtuális gépet az Azure portálon. Ellenőrizze, hogy a virtuális gép sikeresen elindul-e.
   2. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.
   3. A befejezéshez kattintson a **Complete the test** (Teszt befejezése) elemre.
   4. A feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez és mentéséhez kattintson a **Jegyzetek** elemre.
   5. Kattintson **A feladatátvételi teszt befejeződött** elemre. Számolja fel a tesztkörnyezetet. lehetőségre a teszteléshez használt virtuális gép automatikus kikapcsolásához és törléséhez.

## <a name="next-steps"></a>Következő lépések
Ha sikerült beállítania és elindítani az üzemelő példányt, [ismerkedjen meg részletesebben](site-recovery-failover.md) a feladatátvételi folyamattal.
