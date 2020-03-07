---
title: Active Directory/DNS-vész-helyreállítás beállítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan valósítható meg a vész-helyreállítási megoldás a Active Directory és a DNS-hez a Azure Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362760"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Vész-helyreállítás beállítása a Active Directory és a DNS-hez

Az olyan vállalati alkalmazások, mint a SharePoint, a Dynamics AX és az SAP, a Active Directory és a DNS-infrastruktúra megfelelő működéséhez függenek. Az alkalmazások vész-helyreállításának beállításakor a többi alkalmazás-összetevő helyreállítása előtt gyakran helyre kell állítania a Active Directory és a DNS-t, hogy az alkalmazás megfelelő funkciói elérhetők legyenek.

A [site Recovery](site-recovery-overview.md) használatával vész-helyreállítási tervet hozhat létre a Active Directoryhoz. Megszakítás esetén feladatátvételt indíthat. Néhány perc alatt elvégezhető Active Directory. Ha az elsődleges helyen több alkalmazáshoz is telepített Active Directory, például a SharePoint és az SAP esetében, akkor előfordulhat, hogy a teljes webhely feladatátvételét szeretné elvégezni. Active Directory a Site Recovery használatával végezheti el a feladatátvételt. Ezután hajtsa végre a többi alkalmazás feladatátvételét az alkalmazásspecifikus helyreállítási tervekkel.

Ez a cikk azt ismerteti, hogyan hozható létre vész-helyreállítási megoldás a Active Directory számára. Előfeltételeket és feladatátvételi utasításokat is tartalmaz. Mielőtt elkezdené, ismerkedjen meg Active Directory és Site Recoveryával.

## <a name="prerequisites"></a>Előfeltételek

* Ha az Azure-ba replikálódik, [készítse elő az Azure-erőforrásokat](tutorial-prepare-azure.md), például egy előfizetést, egy Azure-Virtual Network, egy Storage-fiókot és egy Recovery Services-tárolót.
* Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálása

- Be kell állítania Site Recovery replikációt legalább egy olyan virtuális gépen, amelyen tartományvezérlő vagy DNS fut.
- Ha több tartományvezérlővel rendelkezik a környezetben, egy további tartományvezérlőt is be kell állítania a célhelyen. A További tartományvezérlő lehet az Azure-ban vagy egy másodlagos helyszíni adatközpontban is.
- Ha csak néhány alkalmazással és egy tartományvezérlővel rendelkezik, akkor előfordulhat, hogy a teljes helyet együtt szeretné felvenni a feladatátvételre. Ebben az esetben javasoljuk, hogy az Site Recovery használatával replikálja a tartományvezérlőt a célhelyre (az Azure-ban vagy egy másodlagos helyszíni adatközpontban). A [feladatátvételi teszthez](#test-failover-considerations)használhatja ugyanazt a replikált tartományvezérlőt vagy DNS virtuális gépet.
- - Ha sok alkalmazást és több tartományvezérlőt használ a környezetben, vagy ha egyszerre több alkalmazás feladatátvételét tervezi, a tartományvezérlő virtuális gép Site Recovery-vel való replikálásán kívül azt javasoljuk, hogy állítson be egy További tartományvezérlő a célhelyen (akár az Azure-ban, akár egy másodlagos helyszíni adatközpontban). A [feladatátvételi teszthez](#test-failover-considerations)használhatja a site Recovery által replikált tartományvezérlőt. A feladatátvételhez a további tartományvezérlőt használhatja a célhelyen.

## <a name="enable-protection-with-site-recovery"></a>Védelem engedélyezése Site Recovery

A Site Recovery használatával biztosíthatja a tartományvezérlőt vagy a DNS-t futtató virtuális gép védettségét.

### <a name="protect-the-vm"></a>A virtuális gép megóvása
A [feladatátvételi teszthez](#test-failover-considerations)a site Recovery használatával replikált tartományvezérlőt használja a rendszer. Győződjön meg arról, hogy az megfelel a következő követelményeknek:

1. A tartományvezérlő a globáliskatalógus-kiszolgáló.
2. A tartományvezérlőnek az FSMO-szerepkör tulajdonosának kell lennie a feladatátvételi teszt során szükséges szerepkörökhöz. Ellenkező esetben ezeket a szerepköröket a feladatátvétel után le kell [ragadni](https://aka.ms/ad_seize_fsmo) .

### <a name="configure-vm-network-settings"></a>Virtuálisgép-hálózati beállítások konfigurálása
A tartományvezérlőt vagy a DNS-t futtató virtuális gép esetében a Site Recovery konfigurálja a hálózati beállításokat a replikált virtuális gép **számítási és hálózati** beállításai alatt. Ez biztosítja, hogy a virtuális gép a megfelelő hálózathoz legyen csatlakoztatva a feladatátvételt követően.

## <a name="protect-active-directory"></a>Védelem Active Directory

### <a name="site-to-site-protection"></a>Helyek közötti védelem
Hozzon létre egy tartományvezérlőt a másodlagos helyen. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg az elsődleges helyen használt tartomány nevét. A **Active Directory helyek és szolgáltatások** beépülő modullal konfigurálhatja azokat a Helykapcsolati objektum beállításait, amelyekhez a helyek hozzá lettek adva. A helykapcsolatok beállításainak konfigurálásával szabályozhatja, hogy mikor történjen a replikálás két vagy több hely között, és milyen gyakran fordul elő. További információ: a [helyek közötti replikáció ütemezése](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Helyek közötti védelem
Először hozzon létre egy tartományvezérlőt egy Azure-beli virtuális hálózaton. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg ugyanazt a tartománynevet, amelyet az elsődleges helyen használ.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgálóját, hogy a DNS-kiszolgálót használja az Azure-ban.

![Azure Network-hálózat](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure – Azure védelem
Először hozzon létre egy tartományvezérlőt egy Azure-beli virtuális hálózaton. Amikor előlépteti a kiszolgálót egy tartományvezérlői szerepkörbe, adja meg ugyanazt a tartománynevet, amelyet az elsődleges helyen használ.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgálóját, hogy a DNS-kiszolgálót használja az Azure-ban.

## <a name="test-failover-considerations"></a>Feladatátvételi teszttel kapcsolatos megfontolások
Az éles munkaterhelésekre gyakorolt hatás elkerülése érdekében a feladatátvételi teszt olyan hálózatban történik, amely el van különítve az éles hálózattól.

A legtöbb alkalmazáshoz tartományvezérlő vagy DNS-kiszolgáló jelenléte szükséges. Ezért az alkalmazás feladatátvétele előtt létre kell hoznia egy tartományvezérlőt az elkülönített hálózatban, amelyet a feladatátvételi teszthez kell használni. Ennek legegyszerűbb módja, ha a Site Recovery használatával replikálja a tartományvezérlőt vagy a DNS-t futtató virtuális gépeket. Ezután futtasson egy feladatátvételi tesztet a tartományvezérlő virtuális gépről, mielőtt futtatja az alkalmazás helyreállítási tervének feladatátvételi tesztjét. A következőképpen teheti meg:

1. A Site Recovery használatával [replikálhatja](vmware-azure-tutorial.md) a tartományvezérlőt vagy a DNS-t futtató virtuális gépet.
2. Hozzon létre egy elkülönített hálózatot. Az Azure-ban létrehozott bármely virtuális hálózat alapértelmezés szerint el van különítve más hálózatokból. Azt javasoljuk, hogy használja ugyanazt az IP-címtartományt ehhez a hálózathoz, amelyet az üzemi hálózatában használ. Ne engedélyezze a helyek közötti kapcsolatot ezen a hálózaton.
3. Adjon meg egy DNS IP-címet az elkülönített hálózatban. Használja azt az IP-címet, amelyre a DNS-virtuális gép beolvasása várható. Ha az Azure-ba végzi a replikálást, adja meg a feladatátvételhez használt virtuális gép IP-címét. Az IP-cím megadásához a replikált virtuális gép **számítási és hálózati** beállításainál válassza ki a **cél IP-** beállításokat.

    ![Azure test Network](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery a virtuális gép **számítási és hálózati** beállításaiban megadott IP-cím használatával megkísérli létrehozni a teszt virtuális gépeket egy azonos nevű alhálózatban. Ha az azonos nevű alhálózat nem érhető el a feladatátvételi teszthez megadott Azure-beli virtuális hálózaton, a teszt virtuális gép a betűrendbe szedett első alhálózatban jön létre.
    >
    > Ha a cél IP-cím a kiválasztott alhálózat része, Site Recovery megpróbálja létrehozni a feladatátvételi teszt virtuális gépet a cél IP-cím használatával. Ha a cél IP-cím nem része a kiválasztott alhálózatnak, a rendszer a kijelölt alhálózat következő elérhető IP-címének használatával hozza létre a feladatátvételi teszt virtuális gépet.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvétel tesztelése másodlagos helyre

1. Ha egy másik helyszíni helyre végzi a replikálást, és DHCP-t használ, [állítsa be a DNS-t és a DHCP-t a feladatátvételi teszthez](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Végezzen feladatátvételi tesztet az elkülönített hálózaton futó tartományvezérlő virtuális gépről. A feladatátvételi teszt végrehajtásához használja a tartományvezérlő virtuális gép legújabb elérhető, *egységes* helyreállítási pontját.
3. Futtasson feladatátvételi tesztet az alkalmazást futtató virtuális gépeket tartalmazó helyreállítási tervhez.
4. A tesztelés befejezésekor törölje a *feladatátvételi tesztet* a tartományvezérlő virtuális gépén. Ez a lépés törli a feladatátvételi teszthez létrehozott tartományvezérlőt.


### <a name="remove-references-to-other-domain-controllers"></a>Más tartományvezérlőkre mutató hivatkozások eltávolítása
Ha feladatátvételi tesztet kezdeményez, ne adja meg az összes tartományvezérlőt a tesztelési hálózaton. Ha el szeretné távolítani az éles környezetben létező többi tartományvezérlőre mutató hivatkozásokat, előfordulhat, hogy le kell kérnie az [FSMO Active Directory szerepköröket](https://aka.ms/ad_seize_fsmo) , és el kell végeznie a [metaadatok törlését](https://technet.microsoft.com/library/cc816907.aspx) a hiányzó tartományvezérlőkön.


### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációs védelem által okozott problémák

> [!IMPORTANT]
> Az ebben a szakaszban leírt konfigurációk némelyike nem szabványos vagy Alapértelmezett tartományvezérlői konfiguráció. Ha nem szeretné, hogy ezek a módosítások egy éles tartományvezérlőn legyenek, létrehozhat egy Site Recovery dedikált tartományvezérlőt, amelyet a feladatátvételi teszthez kíván használni. Hajtsa végre ezeket a módosításokat csak a tartományvezérlőn.  
>
>

A Windows Server 2012-től kezdve a [további óvintézkedések Active Directory tartományi szolgáltatásokba (AD DS) épülnek](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ezek a védelmi intézkedések segítenek a virtualizált tartományvezérlők USN-visszaállítások elleni védelmében, ha az alapul szolgáló hypervisor platform támogatja a virtuális gépek **GenerationID**. Az Azure támogatja a **virtuális gépek GenerationID**. Emiatt a Windows Server 2012-es vagy újabb verzióit futtató tartományvezérlők az Azure Virtual Machines szolgáltatásban rendelkeznek ezekkel a további garanciákkal.


A **virtuális gép GenerationID** alaphelyzetbe állításakor a AD DS adatbázis **InvocationID** értékét is alaphelyzetbe állítja. Emellett a rendszer elveti a RID-készletet, és a SYSVOL mappa nem mérvadóként van megjelölve. További információ: [Bevezetés a Active Directory tartományi szolgáltatások virtualizálás](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és a [biztonságos virtualizálás DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Az Azure-ba való feladatátvétel a **VM-GenerationID** alaphelyzetbe állítását okozhatja. A **VM-GenerationID** alaphelyzetbe állítása további óvintézkedéseket indít, amikor a tartományvezérlő virtuális gépe elindul az Azure-ban. Ez *jelentős késleltetést* eredményezhet a tartományvezérlő virtuális gépre való bejelentkezéshez.

Mivel ez a tartományvezérlő csak feladatátvételi tesztben használatos, a virtualizációs védelem nem szükséges. Annak ellenőrzéséhez, hogy a tartományvezérlő virtuális gép **GenerationID** értéke nem változik-e, a helyszíni tartományvezérlőn a következő duplaszó értékét módosíthatja: **4** .


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációs védelem tünetei

Ha a virtualizálási garanciákat egy feladatátvételi teszt után indítja el, a következő tünetek közül választhat:  

* A **GenerationID** értéke megváltozik.

    ![Létrehozás-azonosító módosítása](./media/site-recovery-active-directory/Event2170.png)

* A **InvocationID** értéke megváltozik.

    ![Meghívási azonosító változása](./media/site-recovery-active-directory/Event1109.png)

* A SYSVOL mappa és a NETLOGON-megosztások nem érhetők el.

    ![SYSVOL mappa megosztása](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL mappa](./media/site-recovery-active-directory/Event13565.png)

* A rendszer törli a DFSR-adatbázisokat.

    ![DFSR-adatbázisok törölve](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Tartományvezérlői hibák elhárítása a feladatátvételi teszt során

> [!IMPORTANT]
> Az ebben a szakaszban leírt konfigurációk némelyike nem szabványos vagy Alapértelmezett tartományvezérlői konfiguráció. Ha nem szeretné, hogy ezek a módosítások egy éles tartományvezérlőn legyenek, létrehozhat egy Site Recovery-feladatátvételi teszthez dedikált tartományvezérlőt. A módosításokat csak erre a dedikált tartományvezérlőre végezze.  
>
>

1. A parancssorban futtassa a következő parancsot annak vizsgálatához, hogy a SYSVOL mappa és a NETLOGON mappa meg van-e osztva:

    `NET SHARE`

2. A parancssorban futtassa a következő parancsot, és győződjön meg arról, hogy a tartományvezérlő megfelelően működik:

    `dcdiag /v > dcdiag.txt`

3. A kimeneti naplóban keresse meg a következő szöveget. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik.

    * "sikeres tesztelési kapcsolat"
    * "Passed test Advertising"
    * "Passed test MachineAccount"

Ha az előző feltételek teljesülnek, valószínű, hogy a tartományvezérlő megfelelően működik. Ha nem, hajtsa végre a következő lépéseket:

1. Végezze el a tartományvezérlő mérvadó visszaállítását. Tartsa szem előtt a következő információkat:
    * Bár a fájlreplikációs szolgáltatás replikálását [nem javasoljuk,](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)ha fájlreplikációs szolgáltatást használ, kövesse a mérvadó visszaállítás lépéseit. A folyamatot a [BurFlags beállításkulcs használatával írja le a fájlreplikációs szolgáltatás újrainicializálásához](https://support.microsoft.com/kb/290762).

        A BurFlags kapcsolatos további információkért tekintse [meg a D2-és D4-es blogbejegyzéset: mi ez?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Ha DFSR-replikációt használ, hajtsa végre a mérvadó visszaállítás lépéseit. A folyamat a [mérvadó és nem mérvadó szinkronizálás kényszerítése a DFSR replikált SYSVOL-mappához (például "D4/D2" a fájlreplikációs szolgáltatáshoz)](https://support.microsoft.com/kb/2218556).

        Használhatja a PowerShell-függvényeket is. További információ: [DFSR-SYSVOL mérvadó/nem mérvadó visszaállítás PowerShell-függvények](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. A kezdeti szinkronizálási követelmény megkerüléséhez állítsa be a következő beállításkulcsot **0** értékre a helyszíni tartományvezérlőn. Ha a DWORD nem létezik, akkor a **Paraméterek** csomópontban hozhatja létre.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    További információ [: a 4013-es DNS-eseményazonosító hibáinak megoldása: a DNS-kiszolgáló nem tudta betölteni az ad integrált DNS-zónákat](https://support.microsoft.com/kb/2001093).

3. Tiltsa le azt a követelményt, hogy a globáliskatalógus-kiszolgáló elérhető legyen a felhasználói bejelentkezés érvényesítéséhez. Ehhez a helyszíni tartományvezérlőn állítsa be a következő beállításkulcsot **1**értékre. Ha a DWORD nem létezik, akkor az **LSA** csomópontban hozhatja létre.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    További információkért lásd: [a globáliskatalógus-kiszolgáló elérhetővé tételének letiltása a felhasználói bejelentkezések érvényesítéséhez](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS-és tartományvezérlők különböző gépeken

Ha a tartományvezérlőt és a DNs-t ugyanazon a virtuális gépen futtatja, akkor kihagyhatja ezt az eljárást.


Ha a DNS nem ugyanazon a virtuális gépen található, mint a tartományvezérlő, létre kell hoznia egy DNS virtuális gépet a feladatátvételi teszthez. Használhatja a friss DNS-kiszolgálót, és létrehozhatja az összes szükséges zónát. Ha például a Active Directory tartománya contoso.com, létrehozhat egy contoso.com nevű DNS-zónát. A Active Directorynak megfelelő bejegyzéseket a következőképpen kell frissíteni a DNS-ben:

1. Győződjön meg arról, hogy ezek a beállítások érvényben vannak, mielőtt a helyreállítási tervben szereplő bármely más virtuális gép elindul:
   * A zónát az erdő gyökerének neve után kell elnevezni.
   * A zónának fájlból kell állnia.
   * A zónának engedélyezve kell lennie a biztonságos és nem biztonságos frissítésekhez.
   * A tartományvezérlőt üzemeltető virtuális gép feloldójának a DNS virtuális gép IP-címére kell mutatnia.

2. Futtassa a következő parancsot a tartományvezérlőt üzemeltető virtuális gépen:

    `nltest /dsregdns`

3. Futtassa a következő parancsokat egy zóna DNS-kiszolgálón való hozzáadásához, engedélyezze a nem biztonságos frissítéseket, és adjon hozzá egy bejegyzést a zónához a DNS-ben:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Következő lépések
További információ a [vállalati munkaterhelések Azure site Recoverysal való védelméről](site-recovery-workload.md).
