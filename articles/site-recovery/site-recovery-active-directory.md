---
title: Az Active Directory/DNS vészhelyreállítás beállítása az Azure Site Recovery szolgáltatással
description: Ez a cikk bemutatja, hogyan valósítható meg egy vész-helyreállítási megoldás az Active Directory és a DNS az Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257809"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Vészhelyreállítás beállítása az Active Directoryhoz és a DNS-hez

A vállalati alkalmazások, például a SharePoint, a Dynamics AX és az SAP az Active Directorytól és a DNS-infrastruktúrától függenek a megfelelő működéstől. Amikor vészhelyreállítást állít be az alkalmazásokhoz, gyakran helyre kell állítania az Active Directoryt és a DNS-t, mielőtt más alkalmazásösszetevőket helyreállítana, hogy biztosítsa az alkalmazás megfelelő működését.

A [Site Recovery](site-recovery-overview.md) segítségével vész-helyreállítási tervet hozhat létre az Active Directory hoz létre. Ha zavar lép fel, kezdeményezheti a feladatátvételt. Az Active Directory perceken belül működőképes lehet. Ha az Active Directoryt több alkalmazáshoz telepítette az elsődleges helyen, például a SharePoint és az SAP esetében, érdemes lehet átvenni a teljes helyet. Először a Site Recovery használatával átveheti az Active Directory feletti feladatát. Ezután feladatátvételt a többi alkalmazás alkalmazásspecifikus helyreállítási tervek használatával.

Ez a cikk bemutatja, hogyan hozhat létre vész-helyreállítási megoldást az Active Directoryhoz. Ez magában foglalja az előfeltételeket, és feladatátvételi utasításokat. A kezdés előtt ismernie kell az Active Directoryt és a Site Recovery-t.

## <a name="prerequisites"></a>Előfeltételek

* Ha replikálja az Azure-ba, [készítse elő az Azure-erőforrásokat,](tutorial-prepare-azure.md)beleértve az előfizetést, az Azure virtuális hálózatot, a tárfiókot és a Helyreállítási szolgáltatások tárolóját.
* Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálása

- Be kell állítania a Site Recovery replikációt legalább egy tartományvezérlőt vagy DNS-t tartalmazó virtuális gépen.
- Ha a környezetében több tartományvezérlő is van, akkor egy további tartományvezérlőt is be kell állítania a célhelyen. A további tartományvezérlő lehet az Azure-ban, vagy egy másodlagos helyszíni adatközpontban.
- Ha csak néhány alkalmazással és egy tartományvezérlővel rendelkezik, érdemes lehet az egész helyet együtt átvenni. Ebben az esetben azt javasoljuk, hogy a Site Recovery használatával replikálja a tartományvezérlőt a célhelyre (akár az Azure-ban, akár egy másodlagos helyszíni adatközpontban). Használhatja ugyanazt a replikált tartományvezérlőt vagy DNS-virtuális gépet a [feladatátvétel teszteléséhez.](#test-failover-considerations)
- - Ha a környezetében több alkalmazás és egynél több tartományvezérlő található, vagy ha egyszerre csak néhány alkalmazást szeretne átadni, a tartományvezérlő virtuális gépének a Site Recovery segítségével történő replikálása mellett azt javasoljuk, hogy állítson be egy további tartományvezérlő a célhelyen (akár az Azure-ban, akár egy másodlagos helyszíni adatközpontban). A [teszt feladatátvételhez](#test-failover-considerations)használhatja a Site Recovery által replikált tartományvezérlőt. Feladatátvételhez használhatja a célhelyen található további tartományvezérlőt.

## <a name="enable-protection-with-site-recovery"></a>Védelem engedélyezése a Site Recovery segítségével

A Site Recovery segítségével megvédheti a tartományvezérlőt vagy a DNS-t tartalmazó virtuális gépet.

### <a name="protect-the-vm"></a>A virtuális gép védelme
A Site Recovery használatával replikált tartományvezérlőt a [rendszer a rendszer a feladatátvételhez](#test-failover-considerations)használja. Győződjön meg arról, hogy megfelel az alábbi követelményeknek:

1. A tartományvezérlő globáliskatalógus-kiszolgáló.
2. A tartományvezérlőnek az FSMO szerepkör tulajdonosának kell lennie a tesztfeladat-átvétel során szükséges szerepkörök esetében. Ellenkező esetben ezeket a szerepköröket a feladatátvétel után le kell [foglalni.](https://aka.ms/ad_seize_fsmo)

### <a name="configure-vm-network-settings"></a>Virtuális gép hálózati beállításainak konfigurálása
A tartományvezérlőt vagy DNS-t üzemeltető virtuális gép esetében a Site Recovery párbeszédpanelen adja meg a hálózati beállításokat a replikált virtuális gép **Számítási és Hálózati** beállításai alatt. Ez biztosítja, hogy a virtuális gép a feladatátvétel után a megfelelő hálózathoz legyen csatlakoztatva.

## <a name="protect-active-directory"></a>Az Active Directory védelme

### <a name="site-to-site-protection"></a>Helyek közötti védelem
Hozzon létre egy tartományvezérlőt a másodlagos helyen. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartomány nevét. Az Active **Directory – helyek és szolgáltatások** beépülő modul segítségével konfigurálhatja a beállításokat azon helykapcsolati objektumon, amelyhez a helyeket hozzáadják. A helykapcsolat beállításainak beállításával szabályozhatja, hogy mikor történjen replikáció két vagy több hely között, és milyen gyakran fordul elő. További információt a [Helyek közötti replikáció ütemezése](https://technet.microsoft.com/library/cc731862.aspx)című témakörben talál.

### <a name="site-to-azure-protection"></a>Helyek közötti védelem
Először hozzon létre egy tartományvezérlőt egy Azure virtuális hálózatban. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartománynevet.

Ezután konfigurálja újra a DNS-kiszolgálót a virtuális hálózathoz az Azure-beli DNS-kiszolgáló használatához.

![Azure Network-hálózat](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-ból Azure-ba védelem
Először hozzon létre egy tartományvezérlőt egy Azure virtuális hálózatban. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartománynevet.

Ezután konfigurálja újra a DNS-kiszolgálót a virtuális hálózathoz az Azure-beli DNS-kiszolgáló használatához.

## <a name="test-failover-considerations"></a>Feladatátvételi szempontok tesztelése
Az éles számítási feladatokra gyakorolt hatás elkerülése érdekében a tesztfeladat-átvétel az éles hálózattól elkülönített hálózatban történik.

A legtöbb alkalmazáshoz tartományvezérlő vagy DNS-kiszolgáló szükséges. Ezért az alkalmazás feladatátvétele előtt létre kell hoznia egy tartományvezérlőt az elkülönített hálózaton a tesztfeladat-átvételhez. Ennek legegyszerűbb módja a Site Recovery használata tartományvezérlőt vagy DNS-t tartalmazó virtuális gépek replikálásához. Ezután futtassa a tartományvezérlő virtuális gépének tesztfeladat-átvételét, mielőtt futtatná az alkalmazás helyreállítási tervének tesztfeladat-átvételét. Itt van, hogyan kell csinálni, hogy:

1. A Site Recovery segítségével [replikálja](vmware-azure-tutorial.md) a tartományvezérlőt vagy a DNS-t tartalmazó virtuális gépet.
2. Hozzon létre egy elszigetelt hálózatot. Az Azure-ban létrehozott virtuális hálózatok alapértelmezés szerint elkülönülnek a többi hálózattól. Azt javasoljuk, hogy használja ugyanazt az IP-címtartományt ehhez a hálózathoz, amelyet az éles hálózatban használ. Ne engedélyezze a helyek közötti kapcsolatot ezen a hálózaton.
3. Adjon meg egy DNS-IP-címet az elkülönített hálózatban. Használja azt az IP-címet, amelyet a DNS virtuális gépvárhatóan megkap. Ha replikálja az Azure-ba, adja meg a feladatátvételhez használt virtuális gép IP-címét. Az IP-cím megadásához a replikált virtuális gépen a **Számítási és hálózati** beállításokban válassza ki a Cél **IP-beállításokat.**

    ![Azure teszthálózat](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > A Site Recovery megkísérli a tesztvirtuális gépek létrehozását egy azonos nevű alhálózatban, és a virtuális gép **számítási és hálózati** beállításaiban megadott IP-cím használatával. Ha egy azonos nevű alhálózat nem érhető el az Azure virtuális hálózat, amely a teszt feladatátvétel, a teszt virtuális gép jön létre az ábécésorrendben első alhálózat.
    >
    > Ha a cél IP-cím a kijelölt alhálózat része, a Site Recovery megpróbálja létrehozni a teszt feladatátvételi virtuális gépet a cél IP-cím használatával. Ha a cél IP nem része a kijelölt alhálózatnak, a teszt feladatátvételi virtuális gép a kiválasztott alhálózat következő elérhető IP-címével jön létre.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvétel tesztelése egy másodlagos helyre

1. Ha egy másik helyszíni helyre replikál, és DHCP-t használ, [állítsa be a DNS-t és a DHCP-t a tesztfeladat-átvételhez.](hyper-v-vmm-test-failover.md#prepare-dhcp)
2. Végezze el az elkülönített hálózaton futó tartományvezérlő virtuális gépének tesztfeladat-átvételét. Használja a tartományvezérlő virtuális gépének legújabb elérhető *alkalmazáskonzisztens* helyreállítási pontját a tesztfeladat-feladatátvételhez.
3. Futtasson egy tesztfeladat-átvételt a helyreállítási tervhez, amely olyan virtuális gépeket tartalmaz, amelyeken az alkalmazás fut.
4. Ha a tesztelés befejeződött, tisztítsa meg a tartományvezérlő virtuális gépen a *tesztfeladat-átvételt.* Ez a lépés törli a tesztfeladat-átvételhez létrehozott tartományvezérlőt.


### <a name="remove-references-to-other-domain-controllers"></a>Más tartományvezérlőkre mutató hivatkozások eltávolítása
Tesztfeladat-átvétel kezdeményezésekkor ne foglalja bele az összes tartományvezérlőt a teszthálózatba. Az éles környezetben lévő más tartományvezérlőkre mutató hivatkozások eltávolításához előfordulhat, hogy le kell foglalnia az [FSMO Active Directory szerepköreit,](https://aka.ms/ad_seize_fsmo) és el kell végeznie a hiányzó tartományvezérlők [metaadat-karbantartását.](https://technet.microsoft.com/library/cc816907.aspx)


### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációs biztosítékok által okozott problémák

> [!IMPORTANT]
> Az ebben a szakaszban ismertetett konfigurációk némelyike nem szabványos vagy alapértelmezett tartományvezérlő-konfiguráció. Ha nem szeretné ezeket a módosításokat egy éles tartományvezérlőn végrehajtani, létrehozhat egy tartományvezérlőt, amely a Site Recovery számára van kivéve a tesztfeladat-átvételhez. Csak az adott tartományvezérlőn hajtsa végre ezeket a módosításokat.  
>
>

A Windows Server 2012-től [kezdődően további biztonsági intézkedések találhatók az Active Directory tartományi szolgáltatásokban (AD DS).](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Ezek a biztonsági intézkedések segítenek megvédeni a virtualizált tartományvezérlőket az USN-visszaállításokkal szemben, ha az alapul szolgáló hipervizor platform támogatja a **Virtuálisgép-GenerationID-t.** Az Azure támogatja a **VM-GenerationID szolgáltatást.** Emiatt a Windows Server 2012-es vagy újabb rendszert az Azure virtuális gépeken futó tartományvezérlők rendelkeznek ezekkel a további biztonsági intézkedéseket.


**A virtuális gép-generációs azonosító** alaphelyzetbe állításakor az AD DS-adatbázis **InvocationID** értéke is alaphelyzetbe áll. Ezenkívül a RID-készlet elvetésre kerül, és a sysvol mappa nem mérvadóként van megjelölve. További információt az [Active Directory tartományi szolgáltatások virtualizálásának](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és a [DFSR biztonságos virtualizálásának](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)című témakörben talál.

Az Azure-ba való átadás a **virtuális gép-generationid** alaphelyzetbe állítását okozhatja. A **Virtuálisgép-GenerationID** alaphelyzetbe állítása további biztosítékokat vált ki, amikor a tartományvezérlő virtuális gépe elindul az Azure-ban. Ez *jelentős késedelmet* okozhat a tartományvezérlő virtuális gépére való bejelentkezésben.

Mivel ez a tartományvezérlő csak a teszt feladatátvétel, virtualizálási biztosítékok nem szükségesek. Annak érdekében, hogy a tartományvezérlő virtuális gépének virtuális gépének virtuális gépének virtuális gépének virtuális gépének **virtuális gépének értéke** ne változzon, módosíthatja a következő duplaszó értékét **4-re** a helyszíni tartományvezérlőn:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációs biztosítékok tünetei

Ha a virtualizációs óvintézkedések a teszt feladatátvételután aktiválódnak, a következő jelenségek közül egy vagy több jelenhet meg:  

* A **GenerationID** érték megváltozik.

    ![Generációs azonosító módosítása](./media/site-recovery-active-directory/Event2170.png)

* Az **InvocationID** érték megváltozik.

    ![Meghívási azonosító módosítása](./media/site-recovery-active-directory/Event1109.png)

* A Sysvol mappa- és NETLOGON-megosztások nem érhetők el.

    ![Sysvol mappamegosztás](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol mappa](./media/site-recovery-active-directory/Event13565.png)

* A DFSR-adatbázisok törlődnek.

    ![A DFSR-adatbázisok törlődnek](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Tartományvezérlővel kapcsolatos problémák elhárítása a tesztfeladat-átvétel során

> [!IMPORTANT]
> Az ebben a szakaszban ismertetett konfigurációk némelyike nem szabványos vagy alapértelmezett tartományvezérlő-konfiguráció. Ha nem szeretné ezeket a módosításokat egy éles tartományvezérlőn végrehajtani, létrehozhat egy tartományvezérlőt, amely a Site Recovery teszt feladatátvételre van kiszentelve. Csak az adott dedikált tartományvezérlőn hajtsa végre a módosításokat.  
>
>

1. A parancssorban futtassa a következő parancsot annak ellenőrzéséhez, hogy a sysvol mappa és a NETLOGON mappa meg van-e osztva:

    `NET SHARE`

2. A parancssorban futtassa a következő parancsot a tartományvezérlő megfelelő működésének biztosításához:

    `dcdiag /v > dcdiag.txt`

3. A kimeneti naplóban keresse meg a következő szöveget. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik.

    * "megfelelt teszt Connectivity"
    * "megfelelt teszt Reklám"
    * "átment teszt MachineAccount"

Ha az előző feltételek teljesülnek, valószínű, hogy a tartományvezérlő megfelelően működik. Ha nem, hajtsa végre az alábbi lépéseket:

1. A tartományvezérlő mérvadó visszaállítása. Tartsa szem előtt a következő információkat:
    * Bár nem javasoljuk [az FRS replikációt,](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)ha FRS replikációt használ, kövesse a mérvadó visszaállítás lépéseit. A folyamat leírása [a Fájlreplikációs szolgáltatás újrainicializálásához a BurFlags beállításkulcs használata](https://support.microsoft.com/kb/290762)című részben található.

        További információ a BurFlags, lásd a blogbejegyzést [D2 és D4: Mi ez a?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Dfsr replikáció használata esetén hajtsa végre a mérvadó visszaállítás lépéseit. A folyamat leírása: [A DfSR által replikált sysvol mappához (például "D4/D2" az FRS-hez) mérvadó és nem mérvadó szinkronizálás a force and non-mérvadó szinkronizálásban ismertetjük.](https://support.microsoft.com/kb/2218556)

        A PowerShell-függvényeket is használhatja. További információ: [DFSR-SYSVOL mérvadó/nem mérvadó visszaállítási PowerShell-függvények.](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)

2. A kezdeti szinkronizálási követelmény megkerülése a következő beállításkulcs **0-ra** állításával a helyszíni tartományvezérlőn. Ha a duplaszó nem létezik, **létrehozhatja** azt a Paraméterek csomópont alatt.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    További információt a [4013-as azonosítójú DNS-esemény hibaelhárítása: A DNS-kiszolgáló nem tudta betölteni az AD integrált DNS-zónáit.](https://support.microsoft.com/kb/2001093)

3. Tiltsa le azt a követelményt, hogy a globáliskatalógus-kiszolgáló elérhető legyen a felhasználói bejelentkezés érvényesítéséhez. Ehhez a helyszíni tartományvezérlőn állítsa a következő beállításkulcsot **1-re.** Ha a duplaszó nem létezik, létrehozhatja az **Lsa** csomópont alatt.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    További információt a [Felhasználói bejelentkezések érvényesítéséhez globáliskatalógus-kiszolgálóra vonatkozó követelmény letiltása című témakörben talál.](https://support.microsoft.com/kb/241789)

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS és tartományvezérlő különböző gépeken

Ha a tartományvezérlőt és a dn-eket ugyanazon a virtuális gépen futtatja, kihagyhatja ezt az eljárást.


Ha a DNS nem ugyanazon a virtuális gépen található, mint a tartományvezérlő, létre kell hoznia egy DNS virtuális gép a teszt feladatátvételhez. Használhat egy friss DNS-kiszolgálót, és létrehozhatja az összes szükséges zónát. Ha például az Active Directory-tartomány contoso.com, létrehozhat egy DNS-zónát contoso.com néven. Az Active Directorynak megfelelő bejegyzéseket a következőképpen kell frissíteni a DNS-ben:

1. Győződjön meg arról, hogy ezek a beállítások a helyreállítási terv bármely más virtuális gépének megkezdése előtt érvényben vannak:
   * A zónát az erdő gyökérnevéről kell elnevezni.
   * A zónának fájlalapúnak kell lennie.
   * A biztonságos és nem biztonságos frissítésekhez engedélyezni kell a zónát.
   * A tartományvezérlőt fogadó virtuális gép feloldójának a DNS virtuális gép IP-címére kell mutatnia.

2. Futtassa a következő parancsot a tartományvezérlőt tartalmazó virtuális számítógépen:

    `nltest /dsregdns`

3. A következő parancsokkal adjon hozzá zónát a DNS-kiszolgálón, engedélyezze a nem biztonságos frissítéseket, és adjon hozzá egy bejegyzést a zóna DNS-hez:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>További lépések
További információ [a vállalati számítási feladatok védelméről az Azure Site Recovery segítségével.](site-recovery-workload.md)
