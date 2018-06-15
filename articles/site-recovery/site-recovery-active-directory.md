---
title: Active Directory és a DNS az Azure Site Recovery védelmét |} Microsoft Docs
description: A cikk ismerteti az Active Directory, Azure Site Recovery segítségével megvalósítható a vész-helyreállítási megoldást.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 97923af5ed4191f66434166c4743e398f8ac635a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072606"
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Azure Site Recovery használatával védi az Active Directory és a DNS-

Vállalati alkalmazások, például a SharePoint, a Dynamics AX és az SAP attól függ, az Active Directory és a DNS-infrastruktúra a megfelelő működéshez. Az alkalmazások vész-helyreállítási beállításakor gyakran kell helyreállítania Active Directory és a DNS, megfelelő működésének biztosításához más alkalmazás-összetevő helyreállítása előtt.

Használhat [Site Recovery](site-recovery-overview.md) az Active Directory egy vész-helyreállítási terv létrehozásához. A szüneteltetése esetén is kezdeményezhető a feladatátvételt. Akkor is fel az Active Directory és a futtató néhány perc múlva. Ha az elsődleges helyen telepített Active Directory egyszerre több alkalmazás, például a SharePoint és az SAP, érdemes a webhely teljes feladatátvételt. Active Directory használata a webhely helyreállítási először feladatátvételt. Ezt követően átadja a más alkalmazásokhoz, és alkalmazásspecifikus helyreállítási tervet.

Ez a cikk ismerteti a vész-helyreállítási megoldás létrehozása az Active Directory. Ez magában foglalja az Előfeltételek és a feladatátvételi utasításokat. Ismernie kell az Active Directory és a helyreállítás megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Ha az Azure-bA replikál [előkészítése az Azure-erőforrások](tutorial-prepare-azure.md), beleértve az előfizetés, egy Azure virtuális hálózatot, tárfiókot és Recovery Services-tároló.
* Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálja.

Be kell állítania [Site Recovery replikációs](#enable-protection-using-site-recovery), legalább egy virtuális gépre, a tartományvezérlő vagy a DNS-tároló. Ha rendelkezik [több tartományvezérlő](#environment-with-multiple-domain-controllers) a környezetben is be kell állítania egy [további tartományvezérlő](#protect-active-directory-with-active-directory-replication) a célként megadott helyen. A további tartományvezérlő lehet az Azure-ban, vagy egy másodlagos helyszíni adatközpontba.

### <a name="single-domain-controller"></a>Egyetlen tartományvezérlő
Ha csak néhány alkalmazások vagy egy tartományvezérlő van, érdemes feladataikat együtt átadó a teljes webhelyet. Ebben az esetben ajánlott a tartományvezérlő replikálja az adatokat a célhelyre (vagy az Azure-ban, vagy egy másodlagos helyszíni adatközpontba) a Site Recovery segítségével. Használhatja a azonos replikált tartományvezérlő vagy a virtuális gép DNS [feladatátvételi teszt](#test-failover-considerations).

### <a name="multiple-domain-controllers"></a>Több tartományvezérlő
Ha több alkalmazás és egynél több tartományvezérlő van a környezetben, vagy ha azt tervezi, hogy feladatátvételt néhány alkalmazások egyszerre, továbbá a tartomány tartományvezérlő virtuális gépnek a Site Recovery replikálása azt javasoljuk, hogy beállított egy [további tartományvezérlő](#protect-active-directory-with-active-directory-replication) a célként megadott helyen (vagy az Azure-ban, vagy egy másodlagos helyszíni adatközpontba). A [feladatátvételi teszt](#test-failover-considerations), használhatja a Site Recovery által replikált tartományvezérlő. Feladatátvétel a további tartományvezérlő használhatja, ha a célként megadott helyen.

## <a name="enable-protection-with-site-recovery"></a>A Site Recovery védelmének engedélyezése

A Site Recovery segítségével a virtuális gép, amelyen a tartományvezérlő vagy a DNS-védelme.

### <a name="protect-the-vm"></a>A virtuális gép védelme
A Site Recovery segítségével replikált használja a rendszer a [feladatátvételi teszt](#test-failover-considerations). Győződjön meg arról, hogy megfelel-e az alábbi követelményeknek:

1. A tartományvezérlő globáliskatalógus-kiszolgáló nem.
2. A tartományvezérlőnek kell lennie, a feladatátvételi teszt során szükséges szerepkörök a műveleti Főkiszolgálói szerepkör tulajdonosával. Ellenkező esetben ezek a szerepkörök kell lennie [lefoglalt](http://aka.ms/ad_seize_fsmo) a feladatátvétel után.

### <a name="configure-vm-network-settings"></a>Virtuális gép hálózati beállításainak konfigurálása
A virtuális gép, amelyen a tartományvezérlő vagy a DNS, a Site Recovery szolgáltatásban, konfigurálja a hálózati beállításait a **számítás és hálózat** a replikált virtuális gép beállításai között. Ez biztosítja, hogy a virtuális géphez van csatolva a megfelelő hálózati feladatátvételt követően.

## <a name="protect-active-directory"></a>Az Active Directory védelmére

### <a name="site-to-site-protection"></a>Pont-pont védelme
A tartományvezérlő létrehozása a másodlagos helyen. Amikor előlépteti egy tartomány tartományvezérlői szerepkör a kiszolgálót, adja meg annak a tartománynak az elsődleges helyen használt nevét. Használhatja a **Active Directory – helyek és szolgáltatások** beépülő modul segítségével a hely objektum, amelyhez a helyek ad hozzá a beállításokat. A helykapcsolatot beállítások konfigurálásával szabályozhatja, hogy milyen gyakran történik, és két vagy több hely közötti replikáció során. További információkért lásd: [helyek közötti replikáció ütemezése](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Hely Azure védelem
Először hozzon létre egy tartományvezérlő egy Azure virtuális hálózatban. Amikor előlépteti egy tartomány tartományvezérlői szerepkör a kiszolgálót, adja meg az elsődleges helyen használt ugyanazon tartomány nevét.

Ezután konfigurálja úgy a DNS-kiszolgáló a DNS-kiszolgáló használatára az Azure virtuális hálózat.

![Azure Network-hálózat](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure-Azure védelme
Először hozzon létre egy tartományvezérlő egy Azure virtuális hálózatban. Amikor előlépteti egy tartomány tartományvezérlői szerepkör a kiszolgálót, adja meg az elsődleges helyen használt ugyanazon tartomány nevét.

Ezután konfigurálja úgy a DNS-kiszolgáló a DNS-kiszolgáló használatára az Azure virtuális hálózat.

## <a name="test-failover-considerations"></a>Feladatátvételi szempontokat részletező cikket
A feladatátvételi teszt termelési számítási feladatokhoz gyakorolt hatás elkerülése érdekében a termelési hálózati elkülönített hálózatban következik be.

A legtöbb alkalmazás egy tartományvezérlő és DNS-kiszolgáló szükséges. Ezért az alkalmazás nem keresztül, mielőtt, a tartományvezérlő feladatátvételi teszt végrehajtásához használja a elkülönített hálózatot kell létrehoznia. Ennek legegyszerűbb módja a Site Recovery segítségével a virtuális gép, amelyen egy tartományvezérlő vagy a DNS-replikáció. Ezután futtassa az alkalmazás a helyreállítási terv feladatátvételi teszt futtatása előtt tartomány a tartományvezérlő virtuális gép feladatátvételi tesztet. Itt látható, hogyan teheti, hogy:

1. A Site Recovery használata [replikálása](vmware-azure-tutorial.md) a virtuális gépet, amely a tartományvezérlő vagy a DNS.
2. Hozzon létre egy elkülönített hálózatot. Az Azure-ban létrehozott virtuális hálózatnak el különítve a többi hálózat alapértelmezés szerint. Azt javasoljuk, hogy használja-e az azonos IP-címtartomány ehhez a hálózathoz, amely a termelési hálózat használata. Hely-hely kapcsolatot, a hálózat nem engedélyezi.
3. Adjon meg egy DNS-IP-címet, az elkülönített hálózatban. Használja az IP-cím, a DNS-virtuális gép beolvasandó várt. Ha az Azure-bA replikál, adja meg az IP-címet a virtuális gép feladatátvételi használt. Adja meg az IP-cím, a replikált virtuális gépen, hogy a **számítás és hálózat** beállításaiban, válassza a **cél IP-címet** beállításait.

    ![Azure tesztet hálózathoz](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > A Site Recovery megpróbálja létrehozni a teszt virtuális gépek azonos nevű és ugyanazon az IP-cím, a megadott alhálózat a **számítás és hálózat** a virtuális gép beállításai között. Ha azonos nevű alhálózat nem használható az Azure virtuális hálózatban, a feladatátvételi teszthez megadott, a teszt virtuális gépét a betűrendben az első alhálózat jön létre.
    >
    > Ha a cél IP-címet a kijelölt alhálózathoz tartozik, a Site Recovery megkísérli a feladatátvételi teszt virtuális gép létrehozása a figyelt IP cím használatával. Ha a figyelt IP cím nem része a kiválasztott alhálózatnak, a feladatátvételi teszt virtuális gép hozta létre a következő rendelkezésre álló IP használata a kiválasztott alhálózatnak.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvételi teszt egy másodlagos helyre

1. Ha egy másik helyszíni helyre replikál, és a DHCP használatára [állítsa be a DNS és DHCP-feladatátvételi teszt](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Végezzen a tartomány a tartományvezérlő virtuális gép elkülönített hálózatban futó feladatátvételi tesztet. Használja a legújabb elérhető *konzisztens alkalmazás* helyreállítási pont a tartomány a tartományvezérlő virtuális gép feladatátvételi teszt végrehajtásához.
3. A helyreállítási terv, amely tartalmazza az alkalmazás a futó virtuális gépek feladatátvételi teszt futtatása.
4. Ha a vizsgálat befejeződött, *karbantartása a feladatátvételi tesztet* a tartomány a tartományvezérlő virtuális gépen. Ezzel a lépéssel törli a teszt feladatátvételhez létrehozott tartományvezérlő.


### <a name="remove-references-to-other-domain-controllers"></a>Távolítsa el a többi tartományvezérlőn hivatkozásokat
Elindít egy feladatátvételi tesztet, ne adja meg a tartományvezérlők a tesztelési célú hálózat. Távolítsa el a többi tartományvezérlőn, amely létezik a hivatkozásokat az éles környezetben, szükség van a [FSMO Active Directory-szerepkörök zárolása](http://aka.ms/ad_seize_fsmo) és [metaadatok](https://technet.microsoft.com/library/cc816907.aspx) a hiányzó tartományvezérlők .


### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációvédelmi funkciók által okozott problémák

> [!IMPORTANT]
> A jelen szakaszban bemutatott konfigurációk némelyike nem standard vagy alapértelmezett tartományvezérlő-konfigurációkban. Ha nem kívánja ilyen jellegű változtatásokat éles-tartományvezérlővé, létrehozhat egy tartományvezérlő, amely a Site Recovery számára a feladatátvételi teszthez használni. Ezek a módosítások csak az adott tartományvezérlő.  
>
>

Windows Server 2012 rendszertől kezdődően [további védelmet az Active Directory tartományi szolgáltatások (AD DS) beépített](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Ezek a funkciók védelme a virtualizált tartományvezérlők USN visszagörgetése ellen, ha az alapul szolgáló hipervizor platform támogatja **virtuális gép Generációazonosítóját**. Az Azure által támogatott **virtuális gép Generációazonosítóját**. Ebből kifolyólag a Windows Server 2012 vagy újabb Azure virtuális gépek futtató tartományvezérlők ezek további funkciók rendelkezik.


Ha **virtuális gép Generációazonosítóját** alaphelyzetbe áll, a **InvocationID** érték az AD DS-adatbázis is alaphelyzetbe áll. Ezenkívül a program elveti a RID-verem, és a SYSVOL nem mérvadó van megjelölve. További információkért lásd: [Bevezetés az Active Directory tartományi szolgáltatások virtualizálása](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és [DFSR biztonságos virtualizálása](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Előfordulhat, hogy az Azure-bA feladatátvételét **virtuális gép Generációazonosítóját** alaphelyzetbe állítása. Alaphelyzetbe állítása **virtuális gép Generációazonosítóját** váltja ki további védelmet, ha a tartomány a tartományvezérlő virtuális gép elindul az Azure-ban. Emiatt előfordulhat, hogy egy *jelentős késés* a tudnak majd bejelentkezni a tartomány a tartományvezérlő virtuális gépet.

Ezt használja a rendszer csak a feladatátvételi tesztet, mert a virtualizációvédelmi funkciók nem szükséges. Annak érdekében, hogy a **virtuális gép Generációazonosítóját** érték a tartomány a tartományvezérlő virtuális gépnek nem változik, módosíthatja a következő DWORD értékét **4** a helyi tartományvezérlő:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációvédelmi funkciók tünetei

A virtualizációvédelmi funkciók lépnek működésbe. a feladatátvételi teszt után nem látható alábbi jelenségek közül:  

* A **GenerationID** érték változik.

    ![Létrehozás ID módosítása](./media/site-recovery-active-directory/Event2170.png)

* A **InvocationID** érték változik.

    ![Meghívási azonosító módosításának](./media/site-recovery-active-directory/Event1109.png)

* NETLOGON és a SYSVOL megosztás nem érhetők el.

    ![SYSVOL-megosztás](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Elosztott fájlrendszer-replikációs adatbázisok törlődnek.

    ![Elosztott fájlrendszer-replikációs adatbázisok törlődnek.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Tartomány a tartományvezérlő elhárítása feladatátvételi tesztje közben

> [!IMPORTANT]
> A jelen szakaszban bemutatott konfigurációk némelyike nem standard vagy alapértelmezett tartományvezérlő-konfigurációkban. Ha nem kívánja ilyen jellegű változtatásokat éles-tartományvezérlővé, létrehozhat egy tartományvezérlő, amely a Site Recovery feladatátvételi teszt végrehajtásához. A módosítások csak az adott dedikált tartományvezérlőről.  
>
>

1. A parancssorban futtassa a következő parancs futtatásával ellenőrizze, hogy megosztott SYSVOL-és a NETLOGON:

    `NET SHARE`

2. A parancssorban futtassa a következő parancs futtatásával győződjön meg arról, hogy a tartományvezérlő megfelelően működik-e:

    `dcdiag /v > dcdiag.txt`

3. Keresse meg a következő szöveget a kimeneti naplót. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik-e.

    * "test átadott kapcsolat"
    * "átadott teszt hirdetési"
    * "test átadott MachineAccount"

Ha az előző feltételek teljesülnek, valószínű, hogy a tartományvezérlő megfelelően működik-e. Ha nem, kövesse az alábbi lépéseket:

1. Hajtsa végre a tartományvezérlő mérvadó visszaállítást. Vegye figyelembe a következőket:
    * Bár nem javasolt [fájlreplikációs](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), ha fájlreplikációs, kövesse a lépéseket mérvadó visszaállítást. Az eljárást a [fájlreplikációs szolgáltatás újrainicializálása a BurFlags beállításkulcs használatával](https://support.microsoft.com/kb/290762).

        BurFlags kapcsolatos további információkért lásd a következő blogbejegyzésben [D2 és D4: Mi az az?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Elosztott fájlrendszer-replikációs replikáció használata, végezze el a lépéseket mérvadó visszaállítást. Az eljárást a [egy mérvadó és nem mérvadó szinkronizálásának kikényszerítéséhez a DFSR-rel replikált SYSVOL mappa ("D4/D2" beállításaihoz hasonlóan az FRS)](https://support.microsoft.com/kb/2218556).

        A PowerShell funkciókat is használhatja. További információkért lásd: [DFSR-SYSVOL mappa mérvadó/nem mérvadó visszaállítást PowerShell funkciók](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. A kezdeti szinkronizálási követelményt kihagyása a következő beállításkulcsot értékre állításával **0** a helyi tartományvezérlőn. Ha a DWORD nem létezik, létrehozhatja a a **paraméterek** csomópont.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    További információkért lásd: [hibaelhárítása DNS Event ID 4013: A DNS-kiszolgáló nem tudta betölteni AD integrált DNS-zónák](https://support.microsoft.com/kb/2001093).

3. Tiltsa le a követelmény, hogy elérhetők-e globáliskatalógus-kiszolgáló a felhasználói bejelentkezési érvényesítéséhez. Ehhez a helyi tartományvezérlő a következő beállításkulcs beállítása **1**. Ha a DWORD nem létezik, létrehozhatja a a **Lsa** csomópont.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    További információkért lásd: [tiltsa le a követelmény, hogy elérhetők-e globáliskatalógus-kiszolgáló felhasználói bejelentkezések érvényesítéséhez](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>A különböző gépeken DNS- és tartományvezérlő
Ha a DNS nem szerepel az azonos virtuális gépen, ahol a tartományvezérlő, a feladatátvételi teszt virtuális gép DNS kell létrehoznia. Ha a DNS és a tartományvezérlő nem ugyanazon a virtuális gépen, ez a szakasz kihagyhatja.

Egy új DNS-kiszolgálót használjon, és hozzon létre a szükséges zónákat. Például ha az Active Directory-tartomány pedig contoso.com, létrehozhat egy DNS-zónát a contoso.com tartománynevet a. A bejegyzéseket, hogy az Active Directory frissíteni kell a DNS-ben az alábbiak szerint:

1. Győződjön meg arról, hogy ezek a beállítások vannak, a helyreállítási tervben szereplő bármely más virtuális gép indítása előtt:
   * A zóna nevet kell kapniuk az erdő gyökér neve után.
   * A zóna fájl alapú elemnek kell lennie.
   * A zóna biztonságos és nem biztonságos frissítések engedélyezni kell.
   * A DNS-virtuális gép IP-címét a tartományvezérlőt üzemeltető virtuális gép a feloldó kell mutatnia.

2. A következő parancsot a tartományvezérlőt üzemeltető virtuális Gépen:

    `nltest /dsregdns`

3. Vegyen fel egy zónát a DNS-kiszolgálón, nem biztonságos frissítések engedélyezése, és adjon hozzá egy bejegyzést a zóna DNS a következő parancsok futtatásával:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>További lépések
További információ [az Azure Site Recovery vállalati munkaterhelések védelme](site-recovery-workload.md).
