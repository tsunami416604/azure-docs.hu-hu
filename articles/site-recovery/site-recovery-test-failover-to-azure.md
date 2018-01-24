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
ms.date: 10/16/2017
ms.author: pratshar
ms.openlocfilehash: a4555b1cc758e2d4bdd11a16776dc3bb209adee8
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="test--failover-to-azure-in-site-recovery"></a>Feladatátvételi teszt Azure-ba, a Site Recovery szolgáltatásban



Ez a cikk ismerteti a vész-helyreállítási részletezési az Azure-bA a Site Recovery feladatátvételi teszt futtatásához.  

A feladatátvételi teszt végrehajtásához ellenőrizheti a replikáció és a vész-helyreállítási stratégiát adatvesztés vagy leállás nélkül futtatja. Feladatátvételi teszt nem befolyásolja a folyamatban lévő replikáció, vagy az éles környezetben. Egy adott virtuális gép (VM), vagy a feladatátvételi teszt futtathatja egy [helyreállítási terv](site-recovery-create-recovery-plans.md) több virtuális gépeket tartalmazó. 


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás ismerteti a helyreállítási terv feladatátvételi teszt futtatása. 

![Feladatátvétel tesztelése](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. A Site Recovery szolgáltatásban az Azure portálon, kattintson a **helyreállítási tervek** > *recoveryplan_name* > **feladatátvételi teszt**.
2. Válassza ki a **helyreállítási pont** , amelyhez a feladatátvételt. Az alábbi lehetőségek egyikét használhatja:
    - **Legújabb feldolgozott**: a tervben a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott összes virtuális gép átadja ezt a beállítást. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a következőket **legújabb helyreállítási pontok** a virtuális gép beállításai. Ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése), mert nincs van feldolgozásával töltött idő feldolgozatlan adatokat.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a virtuális gépeket a tervben a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a következőket **legújabb helyreállítási pontok** a virtuális gép beállításai. 
    - **Legújabb**: Ez a beállítás először dolgozza fel a el lett küldve a Site Recovery szolgáltatásban, a helyreállítási pont létrehozása az egyes virtuális gépek, mielőtt hibát jelentene keresztül hozzá összes adatot. Ezt a lehetőséget biztosít a legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés), mert a virtuális gép létrehozása után feladatátvételi kell replikálni a Site Recovery szolgáltatásban a feladatátvétel kiváltásakor összes adatot.
    - **Legújabb virtuális Gépre kiterjedő feldolgozott**: Ez a beállítás érhető el a helyreállítási terv legalább egy virtuális gépek, amelyeken engedélyezve van a virtuális Gépre kiterjedő konzisztencia. A beállítás engedélyezve van a virtuális gépek átadja a virtuális Gépre kiterjedő legújabb közös alkalmazáskonzisztens helyreállítási pontnak. Más virtuális gépek feladatátvételt a legutóbbi feldolgozott helyreállítási pontot.  
    - **Legújabb virtuális Gépre kiterjedő alkalmazáskonzisztens**: Ez a beállítás érhető el a helyreállítási terv legalább egy virtuális gépek, amelyeken engedélyezve van a virtuális Gépre kiterjedő konzisztencia. A virtuális Gépre kiterjedő legutóbbi közös alkalmazáskonzisztens helyreállítási pontnak feladatátvételt a replikálási csoport részét képező virtuális gépek. Más virtuális gépek feladatátvételt a legutóbbi alkalmazáskonzisztens helyreállítási pontnak. 
    - **Egyéni**: használja ezt a beállítást egy adott VM adott helyreállítási pont létrehozása a feladatátvételt.
3. Válassza ki a teszt virtuális gépek létre Azure virtuális hálózat.

    - Hely helyreállítási kísérletek létrehozása az azonos nevű és ugyanazon IP-címre, amely a megadott alhálózat tesztelése a virtuális gépek a **számítás és hálózat** a virtuális gép beállításait.
    - Ha ilyen nevű alhálózat nem használható a feladatátvételi teszthez használni az Azure virtuális hálózatban, majd a teszt virtuális gép jön létre az első alhálózat ábécésorrendben vannak.
    - Ha IP-cím nem érhető el az alhálózatot, majd a virtuális gépek számára egy másik elérhető IP-cím az alhálózat. [További információk](#creating-a-network-for-test-failover).
4. Ha visszavétele keresztül az Azure-ba, és az adattitkosítás engedélyezve van, a **titkosítási kulcs**, válassza ki a titkosítási szolgáltató telepítése során engedélyezésekor korábban kiadott tanúsítványt. Ezt a lépést kihagyhatja titkosítás nincs engedélyezve.
5. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre. Meg kell tudni az Azure portálon replika tesztgép.
6. Az Azure virtuális gép RDP-kapcsolatot kezdeményezzen, kell [egy nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md) , a feladatait átadó virtuális gép virtuális hálózati adapterén. 
7. Ha minden a várt módon működik, kattintson **karbantartása a feladatátvételi teszt**. Ez a művelet törli a teszt feladatátvétele során létrehozott virtuális gépeket.
8. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez. 


![Feladatátvétel tesztelése](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Feladatátvételi teszt aktiválása esetén a következő történik:

1. **Előfeltételek**: egy Előfeltételek ellenőrzése során győződjön meg arról, hogy a feladatátvétel szükséges összes feltétel teljesül-e.
2. **Feladatátvételi**: A feladatátvevő feldolgozza és előkészítve az adatokat, hogy az Azure virtuális gép is hozható létre belőle.
3. **Legújabb**: Ha a legutóbbi helyreállítási pontot választotta, a helyreállítási pont létrehozása az adatokból a szolgáltatásnak küldött.
4. **Start**: Ezzel a lépéssel hoz létre az előző lépésben feldolgozott adatokat használó Azure virtuális géphez.

### <a name="failover-timing"></a>Feladatátvételi időzítése

A következő esetekben feladatátvételi egy extra köztes lépés, amely általában körülbelül 8 – 10 perc befejezéséhez szükséges:

* VMware virtuális gépek régebbi, mint 9.8 mobilitásiszolgáltatás-verziót futtat
* Fizikai kiszolgálók
* VMware Linux virtuális gépek
* Hyper-V virtuális gép védett, fizikai kiszolgálókon
* VMware virtuális gép, amelyen a következő illesztőprogramokat nem rendszerindító illesztőprogramok:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuális Gépet, amely nincs engedélyezve, DHCP rrespective, hogy DHCP vagy statikus IP-címeket használnak.

Minden más esetben nem köztes lépésre nincs szükség, és feladatátvételi jelentősen kevesebb időt vesz igénybe.


## <a name="create-a-network-for-test-failover"></a>Hozzon létre egy hálózatot a feladatátvételi teszthez

Azt javasoljuk, hogy a feladatátvételi teszt választhat egy elkülönített hálózatot a termelési helyreállítási helyen lévő hálózat az adott a **számítás és hálózat** beállítások az egyes virtuális gépek. Alapértelmezés szerint egy Azure virtuális hálózat létrehozásakor az elkülönül más hálózatokkal. A tesztelési célú hálózat az éles hálózattól kell utánoznia:

- A tesztelési célú hálózat az éles hálózattól alhálózatok egyező számú kell rendelkeznie. Alhálózatok kell ugyanaz a neve.
- A tesztelési célú hálózat az azonos IP-cím rangek kell használnia.
- Frissítse a DNS-, a tesztelési célú hálózat az IP-címét a DNS virtuális Géphez megadott **számítás és hálózat** beállításait. Olvasási [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations) további részleteket.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Feladatátvételi teszt végrehajtásához éles hálózati környezetben a helyreállítási hely

Azt javasoljuk, hogy az éles hálózattól, külön tesztelési hálózatot használ, ha szeretné tesztelni a vész-helyreállítási részletezéshez azokat az éles hálózattól, bár vegye figyelembe a következőket: 

- Győződjön meg arról, hogy az elsődleges virtuális gép le van állítva a feladatátvételi teszt futtatásakor. Nincs Otherewise két virtuális gépek ugyanazzal az identitással, egyszerre ugyanazon a hálózaton futó lesz. Ez is váratlan következményekkel járhat.
- A teszt feladatátvételhez létrehozott virtuális gépek módosításai elvesznek, ha távolítja el a feladatátvételt. Ezeket a módosításokat vissza az elsődleges virtuális gép nem replikálódnak.
- A leállás az éles alkalmazás tesztelése az éles környezetben vezet. Felhasználók ne használja a folyamatban lévő feladatátvételi teszt esetén a virtuális gépeken futó alkalmazások.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory és a DNS előkészítése

Alkalmazás tesztelése a feladatátvételi teszt futtatása szüksége az Active Directory éles környezetben egy példányát a tesztkörnyezetben. Olvasási [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations) további.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Azure virtuális gépek a feladatátvételt követően RDP segítségével való kapcsolódáshoz, hajtsa végre a követelmények a táblázat tartalmazza.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows operációs rendszert futtató Azure virtuális gép** | A helyi számítógépen, a feladatátvétel előtt | Az interneten keresztül fér hozzá az Azure virtuális Géphez, engedélyezze az RDP-, és győződjön meg arról, hogy a TCP és UDP-szabályok hozzáadása történik meg a **nyilvános**, és hogy az összes profil számára engedélyezve van az RDP **Windows tűzfal**  >  **Engedélyezett alkalmazások**.<br/><br/> Webhelyek kapcsolaton keresztül fér hozzá az Azure virtuális Géphez, engedélyezze az RDP a gépen, és győződjön meg arról, hogy engedélyezi-e az RDP a a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások**, a **Tartomány és a saját** hálózatok.<br/><br/>  Győződjön meg arról, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát értéke **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows frissítések a virtuális Gépre, amikor feladatátvételt indít el. A Windows update előfordulhat, hogy indítható el, amikor a rendszer átadja, és nem lehet majd jelentkezzen be a virtuális gép, csak a frissítés befejeződése után. 
**Windows operációs rendszert futtató Azure virtuális gép** | Azure virtuális gép a feladatátvételt követően |  [A nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md) a virtuális gép számára.<br/><br/> A hálózati biztonsági csoportszabályok a feladatait átadó virtuális gép (és az Azure-alhálózaton, amelyhez csatlakozik) kell a bejövő kapcsolatokat az RDP-portjára.<br/><br/> Ellenőrizze **rendszerindítási diagnosztika** egy Képernyőkép a virtuális gép ellenőrzése.<br/><br/> Ha nem sikerül, ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Az Azure virtuális gépet** | A helyi számítógépen, a feladatátvétel előtt | Győződjön meg arról, hogy a virtuális Gépet a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon van beállítva.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Az Azure virtuális gépet** | Azure virtuális gép a feladatátvételt követően | A hálózati biztonsági csoportszabályok a feladatait átadó virtuális gép (és az Azure-alhálózaton, amelyhez csatlakozik) engedélyeznie kell az SSH-portot a bejövő kapcsolatok.<br/><br/> [A nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md) a virtuális gép számára.<br/><br/> Ellenőrizze **rendszerindítási diagnosztika** egy Képernyőkép a virtuális gép számára.<br/><br/>



## <a name="next-steps"></a>További lépések
Miután megadta a vész-helyreállítási részletezési, további információ más típusú [feladatátvételi](site-recovery-failover.md).
