---
title: Tesztfeladat-átvétel (vész-helyreállítási gyakorlat) futtatása az Azure-ban az Azure Site Recovery szolgáltatásban
description: Ismerje meg, hogyan futtathat egy tesztfeladat-átvételt a helyszíni Azure-ból az Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257523"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Tesztfeladat-átvétel (vész-helyreállítási gyakorlat) futtatása az Azure-ba 


Ez a cikk ismerteti, hogyan futtathat egy vész-helyreállítási gyakorlatot az Azure-ba egy site recovery teszt feladatátvétel használatával.  

Futtategy teszt feladatátvételt a replikáció és a vész-helyreállítási stratégia érvényesítéséhez, adatvesztés vagy állásidő nélkül. A tesztfeladat-átvétel nincs hatással a folyamatos replikációra vagy az éles környezetre. Futtathat egy tesztfeladat-átvételt egy adott virtuális gépen (VM), vagy egy több virtuális gépet tartalmazó [helyreállítási terven.](site-recovery-create-recovery-plans.md)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás azt ismerteti, hogyan futtathatok tesztfeladat-átvételt a helyreállítási tervhez. Ha egyetlen virtuális gép tesztfeladat-átvételét szeretné futtatni, kövesse az [itt](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) leírt lépéseket

![Feladatátvételi teszt](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Az Azure Portal Site Recovery szolgáltatásában kattintson a **helyreállítási tervek** > *recoveryplan_name* > **tesztfeladat-átvételi gombra.**
2. Válassza ki azt a **helyreállítási pontot,** amelyre át szeretné adni a feladatátvételt. Az alábbi lehetőségek egyikét használhatja:
    - **Legutóbbi feldolgozott:** Ez a beállítás átadja a csomagban szereplő összes virtuális gépet a Site Recovery által feldolgozott legújabb helyreállítási pontnak. Egy adott virtuális gép legújabb helyreállítási pontjának megtekintéséhez tekintse meg **a legújabb helyreállítási pontokat** a virtuális gép beállításaiban. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legújabb alkalmazás-konzisztens:** Ez a beállítás átadja a csomagban szereplő összes virtuális gépet a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak. Egy adott virtuális gép legújabb helyreállítási pontjának megtekintéséhez tekintse meg **a legújabb helyreállítási pontokat** a virtuális gép beállításaiban.
    - **Legújabb**: Ez a beállítás először feldolgozza az összes adatot, amely et elküldött site recovery szolgáltatás, hozzon létre egy helyreállítási pontot minden virtuális gép, mielőtt átadott neki. Ez a beállítás biztosítja a legalacsonyabb RPO (helyreállításipont), mert a feladatátvétel után létrehozott virtuális gép az összes adatot replikálja a Site Recovery, amikor a feladatátvétel t.
    - **Legújabb több virtuális gép feldolgozása:** Ez a beállítás érhető el a helyreállítási tervek egy vagy több virtuális gép, amely több virtuális gép konzisztenciája engedélyezve van. Virtuális gépek a beállítás engedélyezve van feladatátvétel a legújabb közös multi-vm konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb feldolgozott helyreállítási pont.  
    - **Legújabb többvm-es alkalmazáskonzisztens:** Ez a beállítás egy vagy több virtuális gép, amelyek több virtuális gép konzisztenciája engedélyezve van. Virtuális gépek, amelyek egy replikációs csoport részét képezik, feladatátvétela a legújabb közös többvirtuális gép alkalmazás-konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb alkalmazáskonzisztens helyreállítási pont.
    - **Egyéni:** Ezzel a beállítással egy adott virtuális gép egy adott helyreállítási pont.
3. Válasszon ki egy Azure virtuális hálózatot, amelyben teszt virtuális gépek jönnek létre.

    - A Site Recovery megkísérli a tesztvirtuális gépek létrehozását egy olyan alhálózatban, amelynek neve és IP-címe megegyezik a virtuális gép **számítási és hálózati** beállításaiban megadottal.
    - Ha egy azonos nevű alhálózat nem érhető el a tesztfeladat-átvételhez használt Azure virtuális hálózatban, majd a teszt virtuális gép az első alhálózatban jön létre betűrendben.
    - Ha ugyanaz az IP-cím nem érhető el az alhálózatban, majd a virtuális gép kap egy másik elérhető IP-címet az alhálózatban. [További információ](#create-a-network-for-test-failover).
4. Ha az Azure-ba való áttöltést, és az adattitkosítás engedélyezve van, a **Titkosítási kulcs**ban válassza ki azt a tanúsítványt, amelyet akkor adott ki, amikor engedélyezte a titkosítást a szolgáltató telepítése során. Figyelmen kívül hagyhatja ezt a lépést, ha a titkosítás nincs engedélyezve.
5. A **feladatátvétel** folyamatának nyomon követése a Feladatok lapon. A teszt replikáni a gép az Azure Portalon.
6. Ha rdp-kapcsolatot szeretne kezdeményezni az Azure virtuális géphez, hozzá kell [adnia egy nyilvános IP-címet](https://aka.ms/addpublicip) a feladatátvételi rendszer hálózati adapterén.
7. Ha minden a várt módon működik, kattintson a **Karbantartás teszt feladatátvétele gombra.** Ezzel törli a teszt feladatátvétel során létrehozott virtuális gépeket.
8. A **Megjegyzések alkalmazásban**rögzítse és mentse a tesztfeladat-átvételhez kapcsolódó megfigyeléseket.


![Feladatátvételi teszt](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

A tesztfeladat-átvétel aktiválásakor a következő történik:

1. **Előfeltételek:** Az előfeltételek ellenőrzése fut, győződjön meg arról, hogy a feladatátvételhez szükséges feltételek teljesülnek.
2. **Feladatátvétel:** A feladatátvételi folyamatok és előkészítette az adatokat, hogy egy Azure virtuális gép hozható létre belőle.
3. **Legutóbbi**: Ha a legújabb helyreállítási pontot választotta, a szolgáltatásnak küldött adatokból létrejön egy helyreállítási pont.
4. **Start**: Ez a lépés létrehoz egy Azure virtuális gép az előző lépésben feldolgozott adatok használatával.

### <a name="failover-timing"></a>Feladatátvétel időzítése

A következő esetekben a feladatátvételhez egy további köztes lépés szükséges, amely általában körülbelül 8–10 percet vesz igénybe:

* A Mobility szolgáltatás 9,8-nál régebbi verzióját futtató Virtuális gépgyártók
* Fizikai kiszolgálók
* VMware Linux virtuális gépek
* A Hyper-V Virtuális gép fizikai kiszolgálóként védett
* VMware VM, ahol a következő illesztőprogramok nem rendszerindító illesztőprogramok:
    * storvsc között
    * vmbus között
    * storflt között
    * intelide
    * Atapi
* VMware virtuális gép, amely nem rendelkezik DHCP engedélyezve , függetlenül attól, hogy dhcp-vagy statikus IP-címeket használ.

Az összes többi esetben nincs szükség köztes lépésre, és a feladatátvétel lényegesen kevesebb időt vesz igénybe.


## <a name="create-a-network-for-test-failover"></a>Hálózat létrehozása feladatátvételi teszt számára

Azt javasoljuk, hogy a feladatátvétel teszteléséhez olyan hálózatot válasszon, amely el van különítve az éles helyreállítási oldal hálózatától, amely az egyes virtuális gépek **számítási és hálózati** beállításaiban van megadva. Alapértelmezés szerint egy Azure-beli virtuális hálózat létrehozásakor a hálózat el van különítve az egyéb hálózatoktól. A teszthálózatnak az éles hálózatot kell utánoznia:

- A teszthálózatnak ugyanannyi alhálózattal kell rendelkeznie, mint az éles hálózatnak. Az alhálózatoknak ugyanazzal a névvel kell rendelkezniük.
- A teszthálózatnak ugyanazt az IP-címtartományt kell használnia.
- Frissítse a teszthálózat DNS-ét a DNS VM-hez a **számítási és hálózati** beállítások között megadott IP-címmel. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat](site-recovery-active-directory.md#test-failover-considerations) ismertető cikket.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Feladatátvétel tesztelése egy éles hálózatra a helyreállítási helyen

Bár azt javasoljuk, hogy az éles hálózattól elkülönülő teszthálózatot használjon, ha egy vész-helyreállítási gyakorlatot szeretne tesztelni az éles hálózatban, vegye figyelembe a következőket:

- Győződjön meg arról, hogy az elsődleges virtuális gép le van állítva, amikor a teszt feladatátvétel futtatásakor. Ellenkező esetben két, azonos identitással rendelkező virtuális gép lesz, amelyek ugyanabban a hálózatban futnak egy időben. Ez váratlan következményekhez vezethet.
- A tesztfeladat-átvételhez létrehozott virtuális gépek módosításai elvesznek a feladatátvétel karbantartásakor. Ezek a változások nem replikálódik vissza az elsődleges virtuális gépre.
- Az éles környezetben végzett tesztelés az éles alkalmazás állásidejét eredményezi. A felhasználók ne használjanak virtuális gépeken futó alkalmazásokat, ha a teszt feladatátvétel folyamatban van.  



## <a name="prepare-active-directory-and-dns"></a>Az Active Directory és a DNS előkészítése

Az alkalmazásteszteléshez tesztfeladat-átvétel futtatásához szüksége van az éles Active Directory-környezet egy példányára a tesztkörnyezetben. További információért olvassa el [az Active Directory feladatátvételi szempontjait.](site-recovery-active-directory.md#test-failover-considerations)

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvétel után RDP/SSH használatával szeretne csatlakozni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket.

**Feladatátvétel** | **Helyen** | **Műveletek**
--- | --- | ---
**Windows t futtató Azure virtuális gép** | Helyszíni gép feladatátvétel előtt | Az Azure virtuális gép interneten keresztül való eléréséhez engedélyezze az RDP-t, és győződjön meg arról, hogy a TCP- és UDP-szabályok nyilvánosak, és hogy az RDP a **Windows tűzfal** > **engedélyezett alkalmazásai**összes profiljához engedélyezett. **Public**<br/><br/> Az Azure virtuális gép elérése a helyek közötti kapcsolaton keresztül, rdp engedélyezése a számítógépen, és győződjön meg arról, hogy rdp engedélyezett a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások,** **a tartományi és magánhálózatok.**<br/><br/>  Győződjön meg arról, hogy az operációs rendszer SAN-házirendje **OnlineAll**. [További információ](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows-frissítések a virtuális gép, amikor elindítja a feladatátvételt. Előfordulhat, hogy a Windows update akkor indul el, amikor feladatátvételt végez, és a frissítés befejezéséig nem tud bejelentkezni a virtuális gépre.
**Windows t futtató Azure virtuális gép** | Azure virtuális gép feladatátvétel után |  [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az RDP-port.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** segítségével ellenőrizze a virtuális gép képernyőképének ellenőrzéséhez.<br/><br/> Ha nem tud csatlakozni, ellenőrizze, hogy a virtuális gép fut-e, és tekintse át ezeket a [hibaelhárítási tippeket.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Linuxot futtató Azure virtuális gép** | Helyszíni gép feladatátvétel előtt | Győződjön meg arról, hogy a biztonságos rendszer szolgáltatás a virtuális gép van beállítva, hogy automatikusan elindul a rendszer indításakor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Linuxot futtató Azure virtuális gép** | Azure virtuális gép feladatátvétel után | A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az SSH-port.<br/><br/> [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** egy screenshot a virtuális gép.<br/><br/>

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="next-steps"></a>További lépések
Miután befejezte a vész-helyreállítási gyakorlatot, további információ a [feladatátvétel](site-recovery-failover.md)más típusairól.
