---
title: "A Site Recovery szolgáltatásban az Azure-bA feladatátvételi teszt |} Microsoft Docs"
description: "További tudnivalók a helyszíni feladatátvételi tesztet futtatja az Azure-bA"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: pratshar
ms.openlocfilehash: 54f62af6abcdd38254fd5379b95baa05656dc90b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="test--failover-to-azure-in-site-recovery"></a>Feladatátvételi teszt Azure-ba, a Site Recovery szolgáltatásban



Ez a cikk bemutatja, és utasításokat a feladatátvételi teszt vagy a vész-Helyreállítási részletezési a virtuális gépek és fizikai kiszolgálók, amelyek az Azure használatával a helyreállítási helyként, a Site Recovery védett.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

A feladatátvételi teszt ellenőrzi a replikációs stratégiát, vagy hajtsa végre a vész-helyreállítási részletezéshez adatvesztés vagy leállás nélkül fogja futtatni. A teszt feladatátvétel nem rendelkezik gyakorolt hatás, a folyamatban lévő replikáció, illetve az éles környezetben. A feladatátvételi teszt el lehet végezni a virtuális gép vagy egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Váltanak ki egy feladatátvételi tesztet, amikor meg kell adnia a hálózatot, mely tesztelési virtuális gépek be kíván csatlakozni. Miután feladatátvételi teszt akkor váltódik ki, a folyamat állapotát a nyomon követheti a **feladatok** lap.  


## <a name="supported-scenarios"></a>Támogatott helyzetek
Feladatátvételi teszt nem támogatott az összes központi telepítési forgatókönyv [örökölt VMware-hely Azure](site-recovery-vmware-to-azure-classic-legacy.md). A feladatátvételi teszt nem is támogatott, ha a virtuális gép keresztül Azure sikertelen.  


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás ismerteti a helyreállítási terv feladatátvételi teszt futtatása. Másik lehetőségként is futtathatja egyetlen számítógépen feladatátvételi tesztet rajta a megfelelő lehetőséget.

![Feladatátvétel tesztelése](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi teszt**.
1. Válassza ki a **helyreállítási pont** így. Az alábbi lehetőségek egyikét használhatja:
    1.  **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi helyreállítási pontot, amely a Site Recovery szolgáltatás feldolgozása már megtörtént a helyreállítási terv. A virtuális gép feladatátvételi tesztje során, a legutóbbi feldolgozott helyreállítási pontot időbélyegzőjét is látható. Akkor használatos, ha a helyreállítási terv feladatátvételi, nyissa meg az egyes virtuális géphez, és nézze meg **legújabb helyreállítási pontok** csempén ezt az információt. Nem töltött idő, akkor a feldolgozatlan adatok feldolgozására, ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése) feladatátvételi beállítás.
    1.  **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak, amely a Site Recovery szolgáltatás feldolgozása már megtörtént a helyreállítási terv. A virtuális gép feladatátvételi tesztje során, a legújabb alkalmazáskonzisztens helyreállítási pont időbélyegzőjét is látható. Akkor használatos, ha a helyreállítási terv feladatátvételi, nyissa meg az egyes virtuális géphez, és nézze meg **legújabb helyreállítási pontok** csempén ezt az információt.
    1.  **Legújabb**: Ez a beállítás először dolgozza fel, amely el lett küldve a Site Recovery szolgáltatás minden egyes virtuális gép helyreállítási pont létrehozása előtt feladatátadás őket, akkor az összes adatot. Ezt a lehetőséget biztosít a virtuális gép létrehozása után feladatátvételi kell mindazokat az adatokat a feladatátvétel kiváltásakor a Site Recovery szolgáltatás replikálta a legalacsonyabb helyreállítási Időkorlát (helyreállítási időkorlát).
    1.  **Legújabb virtuális Gépre kiterjedő feldolgozott**: Ez a beállítás csak érhető el helyreállítási tervek, amelyeken legalább egy virtuális gép több virtuális Gépre kiterjedő konzisztencia. Virtuális gépek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális Gépre kiterjedő konzisztens helyreállítási pontot. Más virtuális gépek feladatátvétel a legújabb feldolgozott helyreállítási pontnak.  
    1.  **Legújabb virtuális Gépre kiterjedő alkalmazáskonzisztens**: Ez a beállítás csak érhető el, amelyek rendelkeznek legalább egy virtuális gép több virtuális Gépre kiterjedő konzisztencia ON helyreállítási tervek. Virtuális gépek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális Gépre kiterjedő alkalmazáskonzisztens helyreállítási pont. Más virtuális gépek feladatátvétele, a legutóbbi alkalmazáskonzisztens helyreállítási pontnak. 
    1.  **Egyéni**: Ha egy virtuális gép feladatátvételi tesztje során, akkor használhatja ezt a beállítást, így egy adott helyreállítási pontot.
1. Válasszon egy **Azure-beli virtuális hálózat**: Adjon meg egy Azure virtuális hálózatra, ahol a teszt virtuális gépek jönnek létre. A Site Recovery megpróbálja létrehozni a teszt virtuális gépek az azonos név és az azonos IP-cím használatával, mint a megadott alhálózat **számítás és hálózat** a virtuális gép beállításai között. Ha azonos nevű alhálózat az Azure virtuális hálózatban, a feladatátvételi teszthez megadott nem érhető el, majd teszt virtuális gép jön létre az első alhálózat ábécésorrendben vannak. Azonos IP-cím nem érhető el az alhálózatot, ha virtuális gépet egy másik alhálózaton elérhető IP-cím lekérése. Ebben a szakaszban olvasható [további részletekért](#creating-a-network-for-test-failover)
1. Ha visszavétele keresztül az Azure-ba, és az adattitkosítás engedélyezve van, a **titkosítási kulcs** válassza ki a tanúsítványt, ha engedélyezte az adattitkosítást a szolgáltató telepítése során ki. Ha nincs engedélyezve a virtuális gép a titkosítás, kihagyhatja ezt a lépést.
1. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre. Meg kell tudni az Azure portálon replika tesztgép.
1. A virtuális gép RDP-kapcsolatot kezdeményezzen, szüksége lesz a [egy nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) a hálózati illesztőn az átadó virtuális gép. Ha a klasszikus virtuális gép feladatátvétele, akkor kell [végpont hozzáadása](../virtual-machines/windows/classic/setup-endpoints.md) 3389-es porton
1. Amikor elkészült, kattintson a **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ez a művelet törli a virtuális gépek feladatátvételi teszt során lettek létrehozva.


> [!TIP]
> A Site Recovery megpróbálja létrehozni a teszt virtuális gépek az azonos név és az azonos IP-cím használatával, mint a megadott alhálózat **számítás és hálózat** a virtuális gép beállításai között. Ha azonos nevű alhálózat az Azure virtuális hálózatban, a feladatátvételi teszthez megadott nem érhető el, majd teszt virtuális gép jön létre az első alhálózat ábécésorrendben vannak. Ha a cél IP-címet a kiválasztott alhálózat egy része, majd a site recovery megkísérli létrehozni a teszt feladatátvételi virtuális gépet a cél IP-címet használ. Ha a figyelt IP cím nem része a kiválasztott alhálózatnak majd teszt feladatátvételi virtuális gép lekérdezi létre bármilyen elérhető IP-kiválasztott alhálózaton.
>
>

## <a name="test-failover-job"></a>A tesztfeladat feladatátvételt

![Feladatátvétel tesztelése](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Feladatátvételi teszt aktiválása esetén magában foglalja a következő lépéseket:

1. Előfeltételek ellenőrzése: Ez a lépés biztosítja, hogy a feladatátvétel szükséges összes feltétel teljesül-e
1. Feladatátvétel: Ebben a lépésben fel az adatokat, így készen, hogy egy Azure virtuális gépek hozhatók létre belőle. Ha úgy döntött, **legújabb** helyreállítási pont ebben a lépésben létrehoz egy helyreállítási pontot, amely a szolgáltatás el lett küldve az adatokból.
1. Kezdete: Ebben a lépésben létrehoz az előző lépésben feldolgozott adatokat használó Azure virtuális géphez.

## <a name="time-taken-for-failover"></a>A feladatátvételi idő

Bizonyos esetekben virtuális gépeinek feladatátvételi egy extra köztes lépés, amely általában körülbelül 8 – 10 perc befejezéséhez szükséges. Ezekben az esetekben vannak, mint a következő:

* A mobilitási szolgáltatás 9.8 régebbi verzióját használja a VMware virtuális gépek
* Fizikai kiszolgálók
* VMware Linux virtuális gépek
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek
* VMware virtuális gépek nincsenek jelen, ahol következő illesztőprogramok rendszerindító illesztőprogramok
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van, függetlenül attól, hogy használják DHCP vagy statikus IP-címek

Minden egyéb esetben a köztes lépés nem szükséges, a feladatátvételi idő azonban jelentősen kisebb.


## <a name="creating-a-network-for-test-failover"></a>A feladatátvételi teszthez-hálózat létrehozása
Javasoljuk, hogy amikor a teszt feladatátvétel választhat olyan hálózatot, amely el van különítve a termelési helyreállítási helyen lévő hálózat a megadott **számítás és hálózat** a virtuális gép beállításait. Alapértelmezés szerint egy Azure virtuális hálózat létrehozásakor az elkülönül más hálózatokkal. Ehhez a hálózathoz az éles hálózattól kell utánoznia:

1. Teszt hálózati azonos számú alhálózatok, amely a termelési hálózat, és ezek alhálózatok megegyező névvel kell rendelkezniük az éles hálózattól.
1. Teszt hálózati használjon az azonos IP-címtartomány legyen, mint az éles hálózattól.
1. Frissítse a DNS-, a teszt hálózat, az IP-cím, cél IP a DNS-virtuális gép a megadott **számítás és hálózat** beállításait. Lépkedjen végig [active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations) című szakaszban talál további információt.


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Feladatátvételi teszt végrehajtásához éles hálózati környezetben a helyreállítási helyen
Javasoljuk, hogy amikor a teszt feladatátvétel választhat egy hálózatot, amely eltér a termelési helyreállítási helyen lévő hálózat a megadott **számítás és hálózat** a virtuális gép beállításait. De ha biztosan szeretné érvényesíteni egy sikertelen a végpontok közötti hálózati kapcsolat átadó virtuális gép, vegye figyelembe a következő szempontokat:

1. Győződjön meg arról, hogy az elsődleges virtuális gép leáll a feladatátvételi teszt során. Ha nem így tesz, két virtuális gépek ugyanazzal az identitással fut egyszerre ugyanazon a hálózaton, és, amely nemkívánatos következmények vezethet.
1. A teszt feladatátvételi virtuális gépekké végzett módosításokat kellene elveszhet törlése, a feladatátvételi teszt virtuális gépek. Ezek a változások nem replikálja az elsődleges virtuális gépre.
1. A módszerre tesztelése az éles alkalmazás egy állásidő vezet. Az alkalmazás felhasználóinak kell megkéri, hogy nem használják az alkalmazást, ha a vész-Helyreállítási részletezési van folyamatban.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory és a DNS előkészítése
Alkalmazás tesztelése a feladatátvételi teszt futtatása szüksége az Active Directory munkakörnyezetben másolatát a tesztkörnyezetben. Lépkedjen végig [active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations) című szakaszban talál további információt.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha szeretne csatlakozni az Azure virtuális gépek a feladatátvételt követően RDP segítségével, győződjön meg arról, hogy Ön a műveleteket hajthatja végre a táblázat tartalmazza.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows operációs rendszert futtató Azure virtuális gép** | A feladatátvétel előtt a helyi számítógépen | Hozzáférés az Azure virtuális Gépen az interneten keresztül, engedélyezze az RDP-, győződjön meg arról, hogy a TCP és UDP-szabályokat a hozzáadott a **nyilvános**, és az engedélyezett RDP **Windows tűzfal** > **engedélyezett alkalmazások**, minden profil esetében.<br/><br/> A pont-pont keresztül elérését, engedélyezze az RDP a gépen, és győződjön meg arról, hogy engedélyezi-e az RDP a a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és a saját** hálózatok.<br/><br/>  Győződjön meg arról, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows frissítések a virtuális gépen, amikor feladatátvételt indít el. A Windows update előfordulhat, hogy indítható, ha a feladatátvételt, és nem tudnak bejelentkezni a virtuális gép, csak a frissítés befejeződése után. <br/><br/>
**Windows operációs rendszert futtató Azure virtuális gép** | Azure virtuális gépen a feladatátvételt követően | A klasszikus virtuális gép [vegyen fel egy nyilvános végpontot](../virtual-machines/windows/classic/setup-endpoints.md) az RDP protokollba (3389-es port)<br/><br/>  A Resource Manager virtuális gép [egy nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) rajta.<br/><br/> A hálózati biztonsági csoportszabályok a feladatait átadó virtuális gép, és az Azure-alhálózaton, amelyhez csatlakozik, engedélyeznie kell az RDP-portjára bejövő kapcsolatokat.<br/><br/> A Resource Manager virtuális gép, ellenőrizheti a **rendszerindítási diagnosztika** közül egy Képernyőkép a virtuális gép<br/><br/> Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális Gépet, és keresse meg a következő [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).<br/><br/>
**Az Azure virtuális gépet** | A feladatátvétel előtt a helyi számítógépen | Ellenőrizze, hogy az Azure virtuális gépen beállította-e, hogy a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Az Azure virtuális gépet** | Azure virtuális gép a feladatátvételt követően | Az Azure-alhálózaton, amelyhez a feladatait átadó virtuális gép csatlakozik, illetve magán a gépen érvényes hálózati biztonsági csoport szabályainak engedélyezniük kell az SSH-porthoz való csatlakozást.<br/><br/> A klasszikus virtuális gép [vegyen fel egy nyilvános végpontot](../virtual-machines/windows/classic/setup-endpoints.md) kell létrehozni, az SSH-port (TCP-port 22 alapértelmezés szerint) bejövő kapcsolatok lehetővé tételéhez.<br/><br/> A Resource Manager virtuális gép [egy nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) rajta.<br/><br/> A Resource Manager virtuális gép, ellenőrizheti a **rendszerindítási diagnosztika** közül egy Képernyőkép a virtuális gép<br/><br/>



## <a name="next-steps"></a>Következő lépések
Miután sikeresen próbált meg a feladatátvételi teszt próbálkozzon ezzel egy [feladatátvételi](site-recovery-failover.md).
