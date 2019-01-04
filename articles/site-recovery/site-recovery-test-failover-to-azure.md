---
title: Futtassa a vészhelyreállítási próbát az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerje meg a irányuló vészhelyreállítási próbát a helyszínről az Azure-ba, az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 19f41256866b42962be36bbb97f5f6d3c06d7fed
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976555"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Vészhelyreállítási próba végrehajtása az Azure-ba 


Ez a cikk ismerteti, hogyan futtathat egy vészhelyreállítási próba végrehajtása az Azure Site Recovery feladatátvételi teszt segítségével.  

Ellenőrizheti a replikálás és a vész-helyreállítási stratégiát, adatveszteség és állásidő nélkül a feladatátvételi teszt futtatásakor. Feladatátvételi teszt nem érinti a folyamatban lévő replikáció vagy az éles környezetben. Az egy adott virtuális gép (VM) vagy a feladatátvételi teszt futtathatja egy [helyreállítási terv](site-recovery-create-recovery-plans.md) több virtuális gépeket tartalmazó.


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás ismerteti egy helyreállítási terv feladatátvételi teszt futtatása. Ha azt szeretné, a feladatátvételi teszt futtatása egyetlen virtuális gép, ismertetett lépéseket követve [Itt](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Feladatátvételi teszt](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. A Site Recovery az Azure Portalon, kattintson **helyreállítási tervek** > *recoveryplan_name* > **feladatátvételi teszt**.
2. Válassza ki a **helyreállítási pont** , amelyhez a feladatátvételt. Az alábbi lehetőségek egyikét használhatja:
    - **Legutóbb feldolgozott**: Ez a beállítás a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak a csomag összes virtuális gép feladatait. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a következőket **legutóbbi helyreállítási pontok** a virtuális gép beállításaiban. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legutóbbi alkalmazáskonzisztens**: Ezt a beállítást a tervben a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pont a virtuális gépek feladatait. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a következőket **legutóbbi helyreállítási pontok** a virtuális gép beállításaiban.
    - **Legújabb**: Ez a lehetőség először feldolgozza a helyreállítási pont létrehozása előtt feladatátadás azt a virtuális gépek Site Recovery szolgáltatásba küldött összes adat. Ezt a lehetőséget biztosít a a legkisebb helyreállítási Időkorlát (helyreállítási időkorlát), mert a virtuális gép létrehozása után a feladatátvételi lesz a feladatátvétel elindításakor a Site Recoverybe replikált összes adatot.
    - **Legújabb több virtuális gépre kiterjedő feldolgozott**: Ez a beállítás érhető el helyreállítási terveket biztosít egy vagy több virtuális gépek, amelyeken engedélyezve van a virtuális gépre kiterjedő konzisztencia. A beállítás engedélyezve van a virtuális gépek feladatátvételt a legutóbbi közös virtuális gépre kiterjedő konzisztens helyreállítási pont. Más virtuális gépek feladatátvételt a legutóbbi feldolgozott helyreállítási pontot.  
    - **Legújabb több virtuális gépre kiterjedően alkalmazáskonzisztens**: Ez a beállítás érhető el helyreállítási terveket biztosít egy vagy több virtuális gépek, amelyeken engedélyezve van a virtuális gépre kiterjedő konzisztencia. A replikációs csoport részét képező virtuális gépek feladatátvételt a legutóbbi közös virtuális gépre kiterjedően alkalmazáskonzisztens helyreállítási pontot. Más virtuális gépek feladatátvételt a legutóbbi alkalmazáskonzisztens helyreállítási pontot.
    - **Egyéni**: Ez a beállítás használatával egy adott helyreállítási pontra az adott virtuális gép feladatainak átadásához.
3. Válassza ki a teszt virtuális gépek létrehozott Azure virtuális hálózatban.

    - Hely helyreállítási kísérletek hozhat létre egy alhálózatot az azonos nevű és azonos IP-címre, amelyek a virtuális gépek tesztelése a **számítás és hálózat** a virtuális gép beállításait.
    - Ha egy azonos nevű alhálózat nem érhető el a feladatátvételi teszthez használni az Azure virtuális hálózatban, majd a teszt virtuális gép létrehozása az első alhálózat betűrend szerint.
    - Ha azonos IP-cím nem érhető el az alhálózatot, majd a virtuális gép kap egy másik elérhető IP-cím az alhálózat. [További információk](#create-a-network-for-test-failover).
4. Ha Ön feladatátvétele már az Azure-ba, és engedélyezett az adattitkosítás, a **titkosítási kulcs**, válassza ki a titkosítási szolgáltató telepítése közben engedélyezésekor korábban kiadott tanúsítványt. Ebben a lépésben figyelmen kívül hagyhatja nincs engedélyezve a titkosítás.
5. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre. Megtekintheti a teszt replika gépet az Azure Portalon kell lennie.
6. Az Azure virtuális gép RDP-kapcsolatának kezdeményez, kell [egy nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) , a feladatátvételen átesett virtuális gép hálózati adapterén.
7. Ha minden a várt módon működik, kattintson az **feladatátvételi teszt utáni karbantartás**. Ezzel törli a feladatátvételi teszt során létrehozott virtuális gépeket.
8. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket.


![Feladatátvételi teszt](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Feladatátvételi teszt akkor aktiválódik, amikor az alábbiak történnek:

1. **Előfeltételek**: Győződjön meg arról, hogy a feladatátvételhez szükséges feltételek közül mind teljesül-e, hogy lefuttatja az előfeltételek ellenőrzését.
2. **Feladatátvétel**: A feladatátvétel feldolgozza és előkészített az adatokat, hogy egy Azure virtuális gép hozható létre belőle.
3. **Legújabb**: Ha a legutóbbi helyreállítási pontot választotta, a helyreállítási pont létrehozása az adatokból a szolgáltatásnak küldött.
4. **Indítsa el**: Ez a lépés létrehoz egy Azure virtuális gépen az előző lépésben feldolgozott adatok használatával.

### <a name="failover-timing"></a>Feladatátvételi időzítése

A következő esetekben feladatátvételi egy extra közbenső lépés, amely általában körülbelül 8 – 10 perc végrehajtásához szükséges:

* VMware virtuális gépek 9.8 régebbi mobilitásiszolgáltatás-verziót futtat
* Fizikai kiszolgálók
* A VMware Linux rendszerű virtuális gépek
* A Hyper-V virtuális gép védett, mint a fizikai kiszolgálókat
* VMware virtuális gép, ahol a következő illesztőprogramok nem a rendszerindítási illesztőprogramok:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-kompatibilis, megadásától használatával e DHCP vagy statikus IP-címek.

Minden más esetben nincs köztes lépés nem kötelező, és a feladatátvételi jelentősen kevesebb időt vesz igénybe.


## <a name="create-a-network-for-test-failover"></a>Hálózat létrehozása feladatátvételi teszt számára

Azt javasoljuk, hogy a feladatátvétel teszteléséhez olyan hálózatot válasszon, amely el van különítve az éles helyreállítási oldal hálózatától, amely az egyes virtuális gépek **számítási és hálózati** beállításaiban van megadva. Alapértelmezés szerint egy Azure-beli virtuális hálózat létrehozásakor a hálózat el van különítve az egyéb hálózatoktól. A teszthálózatnak az éles hálózatot kell utánoznia:

- A teszthálózatnak ugyanannyi alhálózattal kell rendelkeznie, mint az éles hálózatnak. Az alhálózatoknak ugyanazzal a névvel kell rendelkezniük.
- A teszthálózatnak ugyanazt az IP-címtartományt kell használnia.
- Frissítse a teszthálózat DNS-ét a DNS VM-hez a **számítási és hálózati** beállítások között megadott IP-címmel. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat](site-recovery-active-directory.md#test-failover-considerations) ismertető cikket.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Feladatátvételi teszt, éles hálózati környezetben a helyreállítási hely

Habár javasoljuk, hogy az éles hálózattól elkülönített tesztelési hálózatot használ, ha szeretné, hogy tesztelje a vészhelyreállítási próbát átvételéhez az éles hálózatba, vegye figyelembe a következőket:

- Győződjön meg arról, hogy az elsődleges virtuális gép állítsa le, amikor a feladatátvételi tesztet futtatja. Ellenkező esetben lesz ugyanazzal az identitással, ugyanazon a hálózaton fut egyszerre két virtuális gépet. Ez váratlan következményekkel vezethet.
- A teszt feladatátvételhez létrehozott virtuális gépek módosítások elvesznek, megtisztítani a feladatátvétel során. Ezek a módosítások nem lesznek replikálva az elsődleges virtuális gép vissza a.
- Tesztelés éles környezetben, az alkalmazás éles üzemét üzemkimaradást vezet. Felhasználók ne használja a ha folyamatban van a feladatátvételi tesztet a virtuális gépeken futó alkalmazások.  



## <a name="prepare-active-directory-and-dns"></a>Készítse elő az Active Directory és DNS

Az alkalmazás tesztelése a feladatátvételi teszt futtatásához, a termelési Active Directory-környezetet egy példányát a tesztelési környezetben kell. Olvasási [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations) további.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha azt szeretné, az Azure-beli virtuális gépek a feladatátvételt követően RDP/SSH segítségével kapcsolódni, kövesse a követelmények a táblázat foglalja össze.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows rendszerű Azure virtuális gép** | A helyi gépen a feladatátvétel előtt | Az Azure virtuális gép hozzáférhet az interneten keresztül, engedélyezze az RDP-, és győződjön meg arról, hogy a TCP és UDP-szabályokat a adják **nyilvános**, és az összes profil számára engedélyezett RDP **Windows tűzfal**  >  **Engedélyezett alkalmazások**.<br/><br/> Az Azure virtuális gép eléréséhez egy helyek közötti kapcsolaton keresztül, engedélyezze az RDP a gépen, és győződjön meg arról, hogy az RDP engedélyezve van-e a a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások**, a **Tartomány és privát** hálózatok.<br/><br/>  Ellenőrizze, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzata értéke **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows frissítések a virtuális gépen, amikor feladatátvételt indít el. Windows update előfordulhat, hogy kezdődik, amikor feladatátvételt hajt végre, és nem tud bejelentkezni a virtuális Gépet, a frissítés befejeződéséig.
**Windows rendszerű Azure virtuális gép** | Az Azure virtuális Géphez feladatátvétel után |  [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> A hálózati biztonsági csoport szabályai a feladatait átadó virtuális gép (és az Azure-alhálózaton, amelyhez csatlakoztatva van) engedélyeznie kell az RDP-port bejövő kapcsolatait.<br/><br/> Ellenőrizze **rendszerindítási diagnosztika** egy Képernyőkép a virtuális gép ellenőrzése.<br/><br/> Ha nem sikerül, ellenőrizze, hogy a virtuális gép fut, és tekintse át a [hibaelhárítási tippek](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Linux rendszerű Azure virtuális Gépen** | A helyi gépen a feladatátvétel előtt | Győződjön meg arról, hogy a virtuális gépen a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon értéke.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Linux rendszerű Azure virtuális Gépen** | Az Azure virtuális Géphez feladatátvétel után | A hálózati biztonsági csoport szabályai a feladatait átadó virtuális gép (és az Azure-alhálózaton, amelyhez csatlakoztatva van) engedélyeznie kell az SSH-port bejövő kapcsolatait.<br/><br/> [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> Ellenőrizze **rendszerindítási diagnosztika** egy Képernyőkép a virtuális gép számára.<br/><br/>

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="next-steps"></a>További lépések
Miután végzett egy vészhelyreállítási próbát, tudjon meg többet a más típusú [feladatátvételi](site-recovery-failover.md).
