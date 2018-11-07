---
title: Vészhelyreállítás beállítása az Active Directory és DNS az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti a vész-helyreállítási megoldás megvalósítása az Active Directory és DNS az Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: f96ed8659fc2f49b89199a813f9fab9d5f4af5a1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232171"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Vészhelyreállítás beállítása az Active Directory és DNS

Vállalati alkalmazások, például a SharePoint, a Dynamics AX és az SAP attól függ, az Active Directory és DNS-infrastruktúra a megfelelő működéshez. Alkalmazások vészhelyreállítása beállításakor gyakran kell az Active Directory és DNS-helyre, mielőtt helyreállítja más alkalmazás-összetevők, hogy helyes-e az alkalmazás funkciói.

Használhat [Site Recovery](site-recovery-overview.md) az Active Directory egy vész-helyreállítási terv létrehozásához. Egy bekövetkező szolgáltatáskimaradás esetén is kezdeményezhető feladatátvétel. Használhat fel az Active Directory és a néhány perc leforgása alatt. Ha az elsődleges helyen telepített Active Directory több alkalmazáshoz, például a SharePoint és az SAP, érdemes a webhely teljes feladatátvételt. Site Recovery használatával az Active Directory először feladatátvételt. Ezt követően átadja a feladatokat a más alkalmazásokhoz, alkalmazásspecifikus helyreállítási tervet.

Ez a cikk bemutatja, hogyan hozhat létre vészhelyreállítási megoldást az Active Directory. Előfeltételek és a feladatátvételi utasításokat tartalmazza. Ismernie kell az Active Directory és a Site Recovery megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure-ba történő replikálás [Azure-erőforrások előkészítése](tutorial-prepare-azure.md), beleértve egy előfizetést, az Azure Virtual Network, egy tárfiókot és egy Recovery Services-tárolót.
* Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálása

- Be kell állítania [Site Recovery replikációs](#enable-protection-using-site-recovery), legalább egy virtuális gépen, amelyen egy tartományvezérlő vagy a DNS.
- Ha rendelkezik [több tartományvezérlő](#environment-with-multiple-domain-controllers) környezete is be kell állítania egy [további tartományvezérlő](#protect-active-directory-with-active-directory-replication) a célként megadott helyen. A további tartományvezérlő lehet az Azure-ban, vagy egy másodlagos helyszíni adatközpontba.
- Ha csak néhány alkalmazások és a egy tartományvezérlő, érdemes lehet, hogy feladatátvételt együtt a teljes helyre. Ebben az esetben ajánlott a Site Recovery használatával a tartományvezérlő replikálja a célhely (vagy egy másodlagos helyszíni adatközpontba vagy az Azure-ban). Az azonos replikált tartományvezérlőt vagy a DNS virtuális gép [feladatátvételi teszt](#test-failover-considerations).
- - Ha több alkalmazás és több tartományvezérlő van a környezetben, vagy ha azt tervezi, hogy átadja a feladatokat néhány alkalmazások egyszerre, továbbá a tartományt vezérlő virtuális gépet a Site Recovery replikálja azt javasoljuk, hogy beállította- [további tartományvezérlő](#protect-active-directory-with-active-directory-replication) a célként megadott helyen (vagy egy másodlagos helyszíni adatközpontba vagy az Azure-ban). A [feladatátvételi teszt](#test-failover-considerations), használhatja a Site Recovery által replikált tartományvezérlőt. A feladatátvételhez használhatja a további tartományvezérlő a célként megadott helyen.

## <a name="enable-protection-with-site-recovery"></a>Engedélyezze a Site recoveryvel

A Site Recovery segítségével megvédheti a virtuális gép, amelyen a tartományvezérlő vagy a DNS.

### <a name="protect-the-vm"></a>A virtuális gép védelme
A Site Recovery használatával replikált tartományvezérlő használt [feladatátvételi teszt](#test-failover-considerations). Győződjön meg arról, hogy megfelel-e a következő követelményeknek:

1. A tartományvezérlő globáliskatalógus-kiszolgáló.
2. A tartományvezérlő feladatátvételi teszt során szükséges szerepkörök a műveleti Főkiszolgálói szerepkör tulajdonosa kell lennie. Ellenkező esetben ezek a szerepkörök kell lennie [lefoglalt](https://aka.ms/ad_seize_fsmo) a feladatátvétel után.

### <a name="configure-vm-network-settings"></a>Virtuális gép hálózati beállításainak konfigurálása
A virtuális gép, amelyen a tartományvezérlő vagy a DNS, a Site Recoveryben, a hálózati beállítások konfigurálása a **számítás és hálózat** a replikált virtuális gép beállításait. Ez biztosítja, hogy a virtuális gép a feladatátvételt követően a megfelelő hálózathoz csatlakozik.

## <a name="protect-active-directory"></a>Az Active Directory védelme

### <a name="site-to-site-protection"></a>Site-to-site védelme
Hozzon létre egy tartományvezérlőt a másodlagos helyen. Amikor előlépteti a kiszolgáló egy tartományvezérlő szerepkör, adja meg annak a tartománynak az elsődleges helyen használt nevét. Használhatja a **Active Directory – helyek és szolgáltatások** beépülő modul konfigurálja a hely hivatkozásra objektum, amely a helyek ad hozzá. A helykapcsolatot. a beállítások konfigurálásával szabályozhatja, hogy milyen gyakran jelentkezik, és két vagy több helyek közötti replikáció során. További információkért lásd: [helyek közötti replikáció ütemezése](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Hely – Azure védelme
Először hozzon létre egy olyan tartományvezérlő egy Azure virtuális hálózatban. Amikor előlépteti a kiszolgáló egy tartományvezérlő szerepkör, adja meg az elsődleges helyen használt ugyanazon a néven.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgáló használata az Azure-ban, a DNS-kiszolgáló.

![Azure Network-hálózat](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure – Azure védelme
Először hozzon létre egy olyan tartományvezérlő egy Azure virtuális hálózatban. Amikor előlépteti a kiszolgáló egy tartományvezérlő szerepkör, adja meg az elsődleges helyen használt ugyanazon a néven.

Ezután konfigurálja újra a virtuális hálózat DNS-kiszolgáló használata az Azure-ban, a DNS-kiszolgáló.

## <a name="test-failover-considerations"></a>Feladatátvételi szempontokat részletező cikkben
Éles számítási feladatokra gyakorolt hatás elkerülése érdekében, hogy az éles hálózattól elkülönített hálózat feladatátvételi teszt történik.

A legtöbb alkalmazás tartományvezérlőként vagy DNS-kiszolgáló meglétének megkövetelése. Ezért mielőtt az alkalmazás átadja a feladatokat, létre kell hoznia tartományvezérlő a elkülönített hálózat feladatátvételi teszthez használni. Ennek legegyszerűbb módja, hogy a Site Recovery használatával egy olyan tartományvezérlő vagy a DNS-üzemeltető virtuális gép replikálása. Ezután futtasson egy feladatátvételi tesztet a domain controller virtuális gép az alkalmazás a helyreállítási terv feladatátvételi teszt futtatása előtt. Itt látható, hogyan teheti, hogy:

1. A Site Recovery használata [replikálása](vmware-azure-tutorial.md) a virtuális gép, amelyen a tartományvezérlő vagy a DNS.
2. Hozzon létre egy elkülönített hálózatot. Bármely hoz létre az Azure-beli virtuális hálózat el különítve a más hálózatok alapértelmezés szerint. Azt javasoljuk, hogy használja-e az IP-címtartományból ehhez a hálózathoz, amelyek használatával az éles hálózattól. Nem engedélyezi a helyek közötti kapcsolat a hálózaton.
3. Adjon meg egy DNS IP-címet a elkülönített hálózatban. A DNS virtuális gép első várt IP-címet használja. Ha az Azure-bA replikál, adja meg az IP-címet a virtuális gép feladatátvételi használt. Adja meg az IP-cím, a replikált virtuális gépen, hogy a **számítás és hálózat** beállításaiban, válassza a **cél IP-címet** beállításait.

    ![Az Azure tesztelési célú hálózat](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > A teszt virtuális gépek létrehozása egy alhálózatot az azonos nevű és azonos IP-cím van megadva a használatával a Site Recovery megkísérli a **számítás és hálózat** a virtuális gép beállításait. Az azonos nevű alhálózat nem található az Azure virtuális hálózat feladatátvételi teszthez megadott érhető el, ha a teszt virtuális gép jön létre a betűrend szerint az első alhálózat.
    >
    > Ha a figyelt IP cím nem a kiválasztott alhálózatnak a része, a Site Recovery megkísérli a feladatátvételi teszt virtuális gép létrehozása a cél IP-cím használatával. Ha a cél IP-cím nem része a kijelölt alhálózat, a teszt feladatátvételi virtuális gépen jön létre a kiválasztott alhálózatban a legközelebbi elérhető IP-cím használatával.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvételi teszt egy másodlagos helyre

1. Ha egy másik helyszíni helyre replikál, és a DHCP szolgáltatást [állítsa be a DNS és DHCP-feladatátvételi teszthez](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Ezt a tartományt vezérlő virtuális gép, amely az elkülönített hálózatra irányuló feladatátvételi teszt. Használja a legújabb elérhető *alkalmazáskonzisztens* helyreállítási pontot a tartomány tartományvezérlő virtuális gép feladatátvételi teszt végrehajtásához.
3. A helyreállítási terv, amely tartalmazza az alkalmazást futtató virtuális gépek feladatátvételi teszt futtatása.
4. Ha a vizsgálat befejeződött, *távolítsa el a feladatátvételi tesztet* a domain controller virtuális gépen. Ezzel a lépéssel törli a teszt feladatátvételhez létrehozott tartományvezérlő.


### <a name="remove-references-to-other-domain-controllers"></a>Más tartományvezérlők mutató hivatkozások eltávolítása
Feladatátvételi teszt után, a tartományvezérlők ne tartalmazza a tesztelési célú hálózat. Más tartományvezérlők létező mutató hivatkozások eltávolításához az éles környezetben szüksége lehet [Active Directory műveleti Főkiszolgálói szerepkörök](https://aka.ms/ad_seize_fsmo) és [metaadattörlést](https://technet.microsoft.com/library/cc816907.aspx) a hiányzó tartományvezérlők .


### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációvédelmi által okozott problémák

> [!IMPORTANT]
> A jelen szakaszban bemutatott konfigurációk némelyike nem standard vagy alapértelmezett tartományvezérlő-konfigurációkban. Ha nem szeretné, hogy ezek a változások éles-tartományvezérlővé, létrehozhat egy olyan tartományvezérlőt, a feladatátvételi teszthez használni a Site Recovery számára van fenntartva. Ezek a módosítások csak az adott tartományvezérlőhöz.  
>
>

Windows Server 2012 verziótól [szabályozzuk Active Directory Domain Services (AD DS) beépített](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ezen védelmi funkciók segítségével a virtualizált tartományvezérlők USN visszagörgetése elleni védelmére, ha az alapul szolgáló hipervizorkonzol támogatja **virtuális gép Generációazonosítóját**. Az Azure támogatja a **virtuális gép Generációazonosítóját**. Emiatt a tartományvezérlők, melyeken a Windows Server 2012 vagy később az Azure virtual machines ezek szabályozzuk rendelkezik.


Amikor **virtuális gép Generációazonosítóját** alaphelyzetbe áll, a **InvocationID** az AD DS-adatbázis értékét is alaphelyzetbe áll. Emellett a rendszer elveti a RID-verem, és a SYSVOL nem mérvadó van megjelölve. További információkért lásd: [az Active Directory Domain Services virtualizálásába bevezető](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és [DFSR biztonságos virtualizálása](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Előfordulhat, hogy az Azure-ba irányuló feladatátvétel **virtuális gép Generációazonosítóját** alaphelyzetbe állítása. Alaphelyzetbe állítás **virtuális gép Generációazonosítóját** szabályozzuk aktivál, ha a tartomány tartományvezérlő virtuális gép elindul az Azure-ban. Emiatt előfordulhat, hogy egy *jelentős késleltetés* képes arra, hogy jelentkezzen be a tartomány tartományvezérlő virtuális gép található.

Ez a tartományvezérlő csak feladatátvételi teszt használja, mert a virtualizációvédelmi nem szükséges. Annak érdekében, hogy a **virtuális gép Generációazonosítóját** érték a domain controller virtuális gép nem változik, a következő DWORD értékét módosítani **4** a helyszíni tartományvezérlő:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációvédelmi tünetei

A virtualizációvédelmi aktivált tesztelési feladatátvétel után, ha egy vagy több alábbi tünetek felbukkanását jelenhetnek meg:  

* A **GenerationID** érték változik.

    ![Létrehozás ID módosítása](./media/site-recovery-active-directory/Event2170.png)

* A **InvocationID** érték változik.

    ![Meghívási azonosító módosítását](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL és NETLOGON-megosztások nem érhetők el.

    ![SYSVOL-megosztás](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Elosztott fájlrendszer-replikációs adatbázisokat is törli.

    ![Elosztott fájlrendszer-replikációs adatbázisok törlése](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Feladatátvételi teszt során tartomány tartományvezérlő hibáinak elhárítása

> [!IMPORTANT]
> A jelen szakaszban bemutatott konfigurációk némelyike nem standard vagy alapértelmezett tartományvezérlő-konfigurációkban. Ha nem szeretné, hogy ezek a változások éles-tartományvezérlővé, létrehozhat egy olyan tartományvezérlőt, a Site Recovery feladatátvételi teszt számára van fenntartva. A módosítások csak az adott dedikált tartományvezérlőhöz.  
>
>

1. Parancsot a parancssorba a következő parancs futtatásával ellenőrizze e SYSVOL és NETLOGON mappák meg vannak osztva:

    `NET SHARE`

2. A parancssorban futtassa a ellenőrizze, hogy a tartományvezérlő megfelelően működik-e a következő parancsot:

    `dcdiag /v > dcdiag.txt`

3. Keresse meg a következő szöveget a kimeneti naplót. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik-e.

    * "kapcsolat átadott test"
    * "a sikeres vizsgálat hirdetési"
    * "test átadott MachineAccount"

Ha az előző feltételek teljesülnek, akkor valószínű, hogy a tartományvezérlő megfelelően működik-e. Ha nem, kövesse az alábbi lépéseket:

1. Hajtsa végre a tartományvezérlő mérvadó visszaállítást. Tartsa szem előtt az alábbi adatokat:
    * Bár nem javasoljuk, hogy [fájlreplikációs](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), ha fájlreplikációs, kövesse a lépéseket a mérvadó visszaállítást. Az eljárást a [fájlreplikációs szolgáltatás újrainicializálása a BurFlags beállításkulcs használatával](https://support.microsoft.com/kb/290762).

        BurFlags kapcsolatos további információkért lásd a következő blogbejegyzésben: [D2 és D4: Mi az a?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Ha az elosztott fájlrendszer-replikációs replikációt használ, a lépéseket a mérvadó visszaállítást. Az eljárást a [egy mérvadó és nem mérvadó szinkronizálást kényszerítheti az elosztott fájlrendszer replikációs szolgáltatása által replikált SYSVOL (például "D4/D2" beállításaihoz az FRS)](https://support.microsoft.com/kb/2218556).

        A PowerShell-funkciók is használható. További információkért lásd: [DFSR-SYSVOL mappa mérvadó és nem mérvadó visszaállítást PowerShell funkciók](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. A kezdeti szinkronizálási követelményt megkerülése úgy, hogy a következő beállításkulcs **0** a helyi tartományvezérlőn. Ha a DWORD nem létezik, létrehozhatja a a **paraméterek** csomópont.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    További információkért lásd: [hibaelhárítása DNS Event ID 4013: A DNS-kiszolgáló nem tudta betölteni az AD integrált DNS-zónák](https://support.microsoft.com/kb/2001093).

3. Tiltsa le az, hogy egy globáliskatalógus-kiszolgáló érhetők el a felhasználói bejelentkezési ellenőrzéséhez követelmény. Ehhez a helyi tartományvezérlő, a következő beállításkulcs beállítása **1**. Ha a DWORD nem létezik, létrehozhatja a a **Lsa** csomópont.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    További információkért lásd: [tiltsa le a követelmény, hogy elérhetők-e egy globáliskatalógus-kiszolgáló felhasználói bejelentkezések ellenőrzése](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS és a tartományvezérlő különböző gépeken

Ha a tartományvezérlő és DNs ugyanazon a virtuális Gépen futtatja, kihagyhatja ezt az eljárást.


Ha DNS ugyanarról a virtuális gépről, ahol a tartományvezérlő nem, a teszt feladatátvételhez DNS virtuális gép létrehozásához szüksége. Friss DNS-kiszolgálóra, és hozhat létre a szükséges zónákat. Például az Active Directory-tartománya a contoso.com, létrehozhat egy DNS-zóna neve Contoso.com. A bejegyzéseket, amelyek megfelelnek az Active Directory frissíteni kell a DNS-ben a következő:

1. Győződjön meg arról, hogy ezek a beállítások vannak, a helyreállítási tervben szereplő bármely más virtuális gép elindítása előtt:
   * A zóna névvel kell rendelkeznie az erdő gyökér neve után.
   * A zóna fájl alapú elemnek kell lennie.
   * A zóna engedélyezni kell a biztonságos és biztonságos frissítéseket.
   * A virtuális gép, amelyen a tartományvezérlő a feloldó DNS virtuális gép IP-címre kell mutatnia.

2. Futtassa a következő parancsot a tartományvezérlőt üzemeltető virtuális gépen:

    `nltest /dsregdns`

3. Futtassa a következő parancsokat, adjon hozzá egy zónát a DNS-kiszolgálón, nem biztonságos frissítések engedélyezése, és adjon hozzá egy bejegyzést a zóna DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>További lépések
Tudjon meg többet [vállalati számítási feladatokat az Azure Site Recovery védelmét](site-recovery-workload.md).
