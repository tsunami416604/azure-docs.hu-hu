---
title: Az Active Directory/DNS vészhelyreállítás beállítása az Azure Site Recovery szolgáltatással
description: Ez a cikk bemutatja, hogyan valósítható meg egy vész-helyreállítási megoldás az Active Directory és a DNS az Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 2cf4f22be2a4407d73fcc7bb340fad647c8aa145
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546519"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Vészhelyreállítás beállítása az Active Directoryhoz és a DNS-hez

A vállalati alkalmazások, például a SharePoint, a Dynamics AX és az SAP az Active Directorytól és a DNS-infrastruktúrától függenek a megfelelő működéstől. Amikor vészhelyreállítást állít be az alkalmazásokhoz, gyakran helyre kell állítania az Active Directory t és a dns-t, mielőtt más alkalmazásösszetevőket helyreállítana, hogy biztosítsa az alkalmazás megfelelő működését.

A [Site Recovery](site-recovery-overview.md) segítségével vész-helyreállítási tervet hozhat létre az Active Directory hoz létre. Ha zavar lép fel, kezdeményezheti a feladatátvételt. Az Active Directory perceken belül működőképes lehet. Ha az Active Directoryt több alkalmazáshoz telepítette az elsődleges helyen, például a SharePoint és az SAP esetében, érdemes lehet átvenni a teljes helyet. Először a Site Recovery használatával átveheti az Active Directory feletti feladatát. Ezután feladatátvételt a többi alkalmazás alkalmazásspecifikus helyreállítási tervek használatával.

Ez a cikk bemutatja, hogyan hozhat létre vész-helyreállítási megoldást az Active Directoryhoz. Ez magában foglalja az előfeltételeket, és feladatátvételi utasításokat. A kezdés előtt ismernie kell az Active Directoryt és a Site Recovery-t.

## <a name="prerequisites"></a>Előfeltételek

- Ha replikálja az Azure-ba, [készítse elő az Azure-erőforrásokat,](tutorial-prepare-azure.md)beleértve az előfizetést, az Azure virtuális hálózatot, a tárfiókot és a Helyreállítási szolgáltatások tárolóját.
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).

## <a name="replicate-the-domain-controller"></a>A tartományvezérlő replikálása

- A Site Recovery replikációt legalább egy tartományvezérlőt vagy DNS-t tartalmazó virtuális gépen be kell állítania.
- Ha a környezetében több tartományvezérlő is van, akkor egy további tartományvezérlőt is be kell állítania a célhelyen. A további tartományvezérlő lehet az Azure-ban, vagy egy másodlagos helyszíni adatközpontban.
- Ha csak néhány alkalmazással és egy tartományvezérlővel rendelkezik, érdemes lehet az egész helyet együtt átvenni. Ebben az esetben azt javasoljuk, hogy a Site Recovery használatával replikálja a tartományvezérlőt a célhelyre, akár az Azure-ban, akár egy másodlagos helyszíni adatközpontban. Használhatja ugyanazt a replikált tartományvezérlőt vagy DNS-virtuális gépet a [feladatátvétel teszteléséhez.](#test-failover-considerations)
- Ha a környezetben több alkalmazás és egynél több tartományvezérlő is található, vagy ha egyszerre csak néhány alkalmazást szeretne átvenni, a tartományvezérlő virtuális gépének a Site Recovery szolgáltatással történő replikálása mellett azt javasoljuk, hogy állítson be egy további tartományvezérlőt a célhelyen (akár az Azure-ban, akár egy másodlagos helyszíni adatközpontban). A [teszt feladatátvételhez](#test-failover-considerations)használhatja a Site Recovery által replikált tartományvezérlőt. Feladatátvételhez használhatja a célhelyen található további tartományvezérlőt.

## <a name="enable-protection-with-site-recovery"></a>Védelem engedélyezése a Site Recovery segítségével

A Site Recovery segítségével megvédheti a tartományvezérlőt vagy a DNS-t tartalmazó virtuális gépet.

### <a name="protect-the-vm"></a>A virtuális gép védelme

A Site Recovery használatával replikált tartományvezérlőt a [rendszer a rendszer a feladatátvételhez](#test-failover-considerations)használja. Győződjön meg arról, hogy megfelel az alábbi követelményeknek:

1. A tartományvezérlő globáliskatalógus-kiszolgáló.
1. A tartományvezérlőnek a rugalmas egyfőkiszolgálós műveletek (FSMO) szerepkör-tulajdonosának kell lennie a tesztfeladat-átvétel során szükséges szerepkörökhöz. Ellenkező esetben ezeket a szerepköröket a feladatátvétel után le kell [foglalni.](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control)

### <a name="configure-vm-network-settings"></a>Virtuális gép hálózati beállításainak konfigurálása

A tartományvezérlőt vagy DNS-t üzemeltető virtuális gép esetében a Site Recovery párbeszédpanelen adja meg a hálózati beállításokat a replikált virtuális gép **Számítási és Hálózati** beállításai alatt. Ez biztosítja, hogy a virtuális gép a feladatátvétel után a megfelelő hálózathoz legyen csatlakoztatva.

## <a name="protect-active-directory"></a>Az Active Directory védelme

### <a name="site-to-site-protection"></a>Helyek közötti védelem

Hozzon létre egy tartományvezérlőt a másodlagos helyen. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartomány nevét. Az Active **Directory – helyek és szolgáltatások** beépülő modul segítségével konfigurálhatja a beállításokat azon helykapcsolati objektumon, amelyhez a helyeket hozzáadják. A helykapcsolat beállításainak beállításával szabályozhatja, hogy mikor történjen replikáció két vagy több hely között, és milyen gyakran fordul elő. További információt a [Helyek közötti replikáció ütemezése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11))című témakörben talál.

### <a name="site-to-azure-protection"></a>Helyek közötti védelem

Először hozzon létre egy tartományvezérlőt egy Azure virtuális hálózatban. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartománynevet.

Ezután konfigurálja újra a DNS-kiszolgálót a virtuális hálózathoz az Azure-beli DNS-kiszolgáló használatához.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Azure-hálózat":::

### <a name="azure-to-azure-protection"></a>Azure-ból Azure-ba védelem

Először hozzon létre egy tartományvezérlőt egy Azure virtuális hálózatban. Amikor a kiszolgálót tartományvezérlői szerepkörré lépteti elő, adja meg az elsődleges helyen használt tartománynevet.

Ezután konfigurálja újra a DNS-kiszolgálót a virtuális hálózathoz az Azure-beli DNS-kiszolgáló használatához.

## <a name="test-failover-considerations"></a>Feladatátvételi szempontok tesztelése

Az éles számítási feladatokra gyakorolt hatás elkerülése érdekében a teszt feladatátvétel az éles hálózattól elkülönített hálózatban történik.

A legtöbb alkalmazáshoz tartományvezérlő vagy DNS-kiszolgáló szükséges. Ezért az alkalmazás feladatátvétele előtt létre kell hoznia egy tartományvezérlőt az elkülönített hálózaton a tesztfeladat-átvételhez. Ennek legegyszerűbb módja a Site Recovery használata tartományvezérlőt vagy DNS-t tartalmazó virtuális gépek replikálásához. Ezután futtassa a tartományvezérlő virtuális gépének tesztfeladat-átvételét, mielőtt futtatná az alkalmazás helyreállítási tervének tesztfeladat-átvételét.

1. A Site Recovery segítségével [replikálja](vmware-azure-tutorial.md) a tartományvezérlőt vagy a DNS-t tartalmazó virtuális gépet.
1. Hozzon létre egy elszigetelt hálózatot. Az Azure-ban létrehozott virtuális hálózatok alapértelmezés szerint elkülönülnek a többi hálózattól. Azt javasoljuk, hogy használja ugyanazt az IP-címtartományt ehhez a hálózathoz, amelyet az éles hálózatban használ. Ne engedélyezze a helyek közötti kapcsolatot ezen a hálózaton.
1. Adjon meg egy DNS-IP-címet az elkülönített hálózatban. Használja azt az IP-címet, amelyet a DNS virtuális gépvárhatóan megkap. Ha replikálja az Azure-ba, adja meg a feladatátvételhez használt virtuális gép IP-címét. Az IP-cím megadásához a replikált virtuális gépen a **Számítási és hálózati** beállításokban válassza ki a Cél **IP-beállításokat.**

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Azure teszthálózat":::

   > [!TIP]
   > A Site Recovery megkísérli a tesztvirtuális gépek létrehozását egy azonos nevű alhálózatban, és a virtuális gép **számítási és hálózati** beállításaiban megadott IP-cím használatával. Ha egy azonos nevű alhálózat nem érhető el az Azure virtuális hálózat, amely a teszt feladatátvétel, a teszt virtuális gép jön létre az ábécésorrendben első alhálózat.
   >
   > Ha a cél IP-cím a kijelölt alhálózat része, a Site Recovery megpróbálja létrehozni a teszt feladatátvételi virtuális gépet a cél IP-cím használatával. Ha a cél IP nem része a kijelölt alhálózatnak, a teszt feladatátvételi virtuális gép a kiválasztott alhálózat következő elérhető IP-címével jön létre.

### <a name="test-failover-to-a-secondary-site"></a>Feladatátvétel tesztelése egy másodlagos helyre

1. Ha egy másik helyszíni helyre replikál, és DHCP-t használ, [állítsa be a DNS-t és a DHCP-t a tesztfeladat-átvételhez.](hyper-v-vmm-test-failover.md#prepare-dhcp)
1. Végezze el az elkülönített hálózaton futó tartományvezérlő virtuális gépének tesztfeladat-átvételét. Használja a tartományvezérlő virtuális gépének legújabb elérhető _alkalmazáskonzisztens_ helyreállítási pontját a tesztfeladat-feladatátvételhez.
1. Futtasson egy tesztfeladat-átvételt a helyreállítási tervhez, amely olyan virtuális gépeket tartalmaz, amelyeken az alkalmazás fut.
1. Ha a tesztelés befejeződött, tisztítsa meg a tartományvezérlő virtuális gépen a _tesztfeladat-átvételt._ Ez a lépés törli a tesztfeladat-átvételhez létrehozott tartományvezérlőt.

### <a name="remove-references-to-other-domain-controllers"></a>Más tartományvezérlőkre mutató hivatkozások eltávolítása

Tesztfeladat-átvétel kezdeményezésekkor ne foglalja bele az összes tartományvezérlőt a teszthálózatba. Az éles környezetben lévő más tartományvezérlőkre mutató hivatkozások eltávolításához előfordulhat, hogy le kell foglalnia az [FSMO Active Directory szerepköreit,](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) és el kell végeznie a hiányzó tartományvezérlők [metaadat-karbantartását.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10))

### <a name="issues-caused-by-virtualization-safeguards"></a>A virtualizációs biztosítékok által okozott problémák

> [!IMPORTANT]
> Az ebben a szakaszban ismertetett konfigurációk némelyike nem szabványos vagy alapértelmezett tartományvezérlő-konfiguráció. Ha nem szeretné ezeket a módosításokat egy éles tartományvezérlőn végrehajtani, létrehozhat egy tartományvezérlőt, amely a Site Recovery számára van kivéve a tesztfeladat-átvételhez. Csak az adott tartományvezérlőn hajtsa végre ezeket a módosításokat.

A Windows Server 2012-től [kezdődően további biztonsági intézkedések találhatók az Active Directory tartományi szolgáltatásokban (AD DS).](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Ezek a biztosítékok segítenek megvédeni a virtualizált tartományvezérlőket a frissítési sorszám (USN) visszaállításaitól, ha az alapul szolgáló hipervizor platform támogatja a **Virtuálisgép-generációs azonosítót.** Az Azure támogatja a **VM-GenerationID szolgáltatást.** Emiatt a Windows Server 2012-es vagy újabb rendszert az Azure virtuális gépeken futó tartományvezérlők rendelkeznek ezekkel a további biztonsági intézkedéseket.

**A virtuális gép-generációs azonosító** alaphelyzetbe állításakor az AD DS-adatbázis **InvocationID** értéke is alaphelyzetbe áll. Ezenkívül a relatív azonosító (RID) készlete `SYSVOL` elvetésre kerül, a mappa pedig nem mérvadóként van megjelölve. További információt az [Active Directory tartományi szolgáltatások virtualizálásának](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) és az [elosztott fájlrendszer replikációjának biztonságos virtualizálásának (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671)című témakörben talál.

Az Azure-ba való átadás a **virtuális gép-generationid** alaphelyzetbe állítását okozhatja. A **Virtuálisgép-GenerationID** alaphelyzetbe állítása további biztosítékokat vált ki, amikor a tartományvezérlő virtuális gépe elindul az Azure-ban. Ez jelentős késedelmet okozhat a tartományvezérlő virtuális gépére való bejelentkezésben.

Mivel ez a tartományvezérlő csak a teszt feladatátvétel, virtualizálási biztosítékok nem szükségesek. Annak érdekében, hogy a tartományvezérlő virtuális gépének virtuális gépének virtuális gépének **virtuális** `DWORD` gépének virtuális gépének virtuális gépértéke ne változzon, módosíthatja a következő értéket **4-re** a helyszíni tartományvezérlőn:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>A virtualizációs biztosítékok tünetei

Ha a virtualizációs óvintézkedések a teszt feladatátvételután aktiválódnak, a következő jelenségek közül egy vagy több jelenhet meg:

- A **GenerationID** érték megváltozik:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Generációs azonosító módosítása":::

- Az **InvocationID** érték megváltozik:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Meghívási azonosító módosítása":::

- `SYSVOL`mappa `NETLOGON` és a megosztások nem érhetők el.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="SYSVOL mappamegosztás":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="NtFrs SYSVOL mappa":::

- A DFSR-adatbázisok törlődnek.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="A DFSR-adatbázisok törlődnek":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Tartományvezérlővel kapcsolatos problémák elhárítása a tesztfeladat-átvétel során

> [!IMPORTANT]
> Az ebben a szakaszban ismertetett konfigurációk némelyike nem szabványos vagy alapértelmezett tartományvezérlő-konfiguráció. Ha nem szeretné ezeket a módosításokat egy éles tartományvezérlőn végrehajtani, létrehozhat egy tartományvezérlőt, amely a Site Recovery teszt feladatátvételre van kiszentelve. Csak az adott dedikált tartományvezérlőn hajtsa végre a módosításokat.

1. A parancssorban futtassa a következő `SYSVOL` parancsot annak ellenőrzéséhez, hogy a mappa és `NETLOGON` a mappa meg van-e osztva:

    `NET SHARE`

1. A parancssorban futtassa a következő parancsot a tartományvezérlő megfelelő működésének biztosításához:

    `dcdiag /v > dcdiag.txt`

1. A kimeneti naplóban keresse meg a következő szöveget. A szöveg megerősíti, hogy a tartományvezérlő megfelelően működik.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Ha az előző feltételek teljesülnek, valószínű, hogy a tartományvezérlő megfelelően működik. Ha nem, hajtsa végre az alábbi lépéseket:

1. A tartományvezérlő mérvadó visszaállítása. Tartsa szem előtt a következő információkat:

    - Bár nem javasoljuk a replikációt a [fájlreplikációs szolgáltatás (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379)használatával, ha FRS replikációt használ, kövesse a mérvadó visszaállítás lépéseit. A folyamat leírása [a Fájlreplikációs szolgáltatás újrainicializálásához a BurFlags beállításkulcs használata](https://support.microsoft.com/kb/290762)című részben található.

      További információ a BurFlags, lásd a blogbejegyzést [D2 és D4: Mi ez a?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Dfsr replikáció használata esetén hajtsa végre a mérvadó visszaállítás lépéseit. A folyamat leírása: [A DfSR által replikált SYSVOL mappa mérvadó és nem mérvadó szinkronizálása (például "D4/D2" az FRS esetében)](https://support.microsoft.com/kb/2218556)című dokumentum ismerteti.

      A PowerShell-függvényeket is használhatja. További információ: [DFSR-SYSVOL mérvadó/nem mérvadó visszaállítási PowerShell-függvények.](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions)

1. A kezdeti szinkronizálási követelmény megkerülése a következő beállításkulcs **0-ra** állításával a helyszíni tartományvezérlőn. Ha `DWORD` a nem létezik, **létrehozhatja** azt a Paraméterek csomópont alatt.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   További információt a [4013-as azonosítójú DNS-esemény hibaelhárítása: A DNS-kiszolgáló nem tudta betölteni az AD integrált DNS-zónáit.](https://support.microsoft.com/kb/2001093)

1. Tiltsa le azt a követelményt, hogy a globáliskatalógus-kiszolgáló elérhető legyen a felhasználói bejelentkezés érvényesítéséhez. Ehhez a helyszíni tartományvezérlőn állítsa a következő beállításkulcsot **1-re.** Ha `DWORD` a nem létezik, létrehozhatja az **Lsa** csomópont alatt.

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   További információ: [Hogyan működik a globális katalógus.](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10))

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS és tartományvezérlő különböző gépeken

Ha a tartományvezérlőt és a dn-eket ugyanazon a virtuális gépen futtatja, kihagyhatja ezt az eljárást.

Ha a DNS nem ugyanazon a virtuális gépen található, mint a tartományvezérlő, létre kell hoznia egy DNS virtuális gép a teszt feladatátvételhez. Használhat egy friss DNS-kiszolgálót, és létrehozhatja az összes szükséges zónát. Ha például az Active `contoso.com`Directory tartomány a , létrehozhat `contoso.com`egy DNS-zónát a névvel. Az Active Directorynak megfelelő bejegyzéseket a következőképpen kell frissíteni a DNS-ben:

1. Győződjön meg arról, hogy ezek a beállítások a helyreállítási terv bármely más virtuális gépének megkezdése előtt érvényben vannak:

   - A zónát az erdő gyökérnevéről kell elnevezni.
   - A zónának fájlalapúnak kell lennie.
   - A biztonságos és nem biztonságos frissítésekhez engedélyezni kell a zónát.
   - A tartományvezérlőt fogadó virtuális gép feloldójának a DNS virtuális gép IP-címére kell mutatnia.

1. Futtassa a következő parancsot a tartományvezérlőt tartalmazó virtuális számítógépen:

   `nltest /dsregdns`

1. A következő parancsokkal adjon hozzá zónát a DNS-kiszolgálón, engedélyezze a nem biztonságos frissítéseket, és adjon hozzá egy bejegyzést a zóna DNS-hez:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>További lépések

[További információ](site-recovery-workload.md) a vállalati számítási feladatok védelméről az Azure Site Recovery segítségével.
