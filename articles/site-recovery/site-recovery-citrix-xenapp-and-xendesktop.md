---
title: "Egy Azure Site Recovery segítségével többrétegű Citrix XenDesktop és XenApp telepítési replikálni |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan védelme és helyreállítása Azure Site Recovery segítségével Citrix XenDesktop és XenApp telepítések."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: ponatara
ms.openlocfilehash: 52b123b598226e7b03ea9a31c40dd192fd76b191
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Egy Azure Site Recovery segítségével többrétegű Citrix XenApp és XenDesktop telepítési replikálása

## <a name="overview"></a>Áttekintés

Citrix XenDesktop egy asztali virtualizálási megoldás, amely asztali környezetek és alkalmazások ondemand-szolgáltatásként juttatja bárhonnan bármely felhasználó számára. A FlexCast kézbesítési technológia XenDesktop gyorsan és biztonságosan biztosíthat alkalmazásokhoz és az asztali a felhasználók számára.
Ma, Citrix XenApp nem minden vész helyreállítási képességeket biztosítják.

Egy jó vész-helyreállítási megoldást lehetővé kell tennie, hogy a fenti körül helyreállítási tervek modellezési összetett alkalmazási architektúrákban és is képesek legyenek kezelni az alkalmazás-hozzárendelések megoldás ezért biztosítása, hogy az egy kattintással indítható megtekinthet egy alacsonyabb RTO vezető katasztrófa esetén különböző rétegek közötti testreszabott lépéseket adhat hozzá.

Ez a dokumentum részletes útmutatást a vész-helyreállítási megoldást a helyszíni Hyper-V és a VMware vSphere platformokon Citrix XenApp telepítések készítéséhez. Ez a dokumentum is ismerteti, hogyan hajthat végre egy feladatátvételi tesztet (vész-helyreállítási részletezési) és a nem tervezett feladatátvételt az Azure-ban, a helyreállítási terv által támogatott konfigurációk és az előfeltételek.


## <a name="prerequisites"></a>Előfeltételek

Megkezdése előtt győződjön meg arról, hogy a következő:

1. [A virtuális gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md)
1. Hogyan [egy helyreállítási hálózathoz. terv](site-recovery-network-design.md)
1. [A teszt feladatátvétel az Azure-bA](site-recovery-test-failover-to-azure.md)
1. [A feladatátvétel az Azure-bA](site-recovery-failover.md)
1. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
1. Hogyan [SQL-kiszolgáló replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Központi telepítés minták

A Citrix XenApp és XenDesktop farmhoz jellemzően a következő telepítési minta rendelkezik:

**Telepítési minta**

Központi Citrix XenApp és XenDesktop AD DNS-kiszolgálóval, az SQL-adatbázis-kiszolgáló, Citrix kézbesítési vezérlő, kirakat kiszolgálót, XenApp főkiszolgáló (VDA), Citrix XenApp licenckiszolgáló

![Telepítési minta 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

Ebben a cikkben céljából vSphere 6.0 által felügyelt VMware virtuális gépeken a Citrix központi telepítések / System Center VMM 2012 R2 használt vész-Helyreállítási beállítása.

### <a name="source-and-target"></a>Forrása és célja

**A forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-bA**
--- | --- | ---
**Hyper-V** | Nincs a hatókörben | Igen
**VMware** | Nincs a hatókörben | Igen
**Fizikai kiszolgáló** | Nincs a hatókörben | Igen

### <a name="versions"></a>Verziók
Futó Hyper-V vagy VMware virtuális gépek vagy fizikai kiszolgálók, ügyfelek XenApp összetevőit is telepítheti. Az Azure Site Recovery megvédheti a fizikai és virtuális telepítések az Azure-bA.
XenApp Észrevételek 7.7 vagy újabb támogatott Azure-ban, mert csak ezen verziói telepítések esetén is a feladatátvételt az Azure-bA vész-helyreállítási vagy áttelepítési.

### <a name="things-to-keep-in-mind"></a>Vegye figyelembe a következőkre

1. Védelem és helyreállítás a helyszíni központi telepítések segítségével a kiszolgálói operációs rendszer képes biztosítani a XenApp gépek közzétett alkalmazást, és XenApp közzétett asztalok használata támogatott.

2. Védelmi és helyreállítási a helyszíni központi telepítések asztali VDI-ügyfél képes biztosítani a virtuális asztalok, beleértve a Windows 10 operációs rendszer asztali gépek használata nem támogatott. Ennek oka az, így nem támogatja a visszaállítást OS'es asztali gépek.  Emellett egyes ügyfél virtuális asztali operációs rendszerek (például) Windows 7) még nem támogatottak a licencelés az Azure-ban. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

3.  Az Azure Site Recovery nem replikálja, és védeni a meglévő helyszíni MCS vagy PVS klónok.
Létre kell hoznia a klónok használatával Azure RM kiépítése a kézbesítési vezérlőről.

4. NetScaler nem látható el védelemmel, Azure Site Recovery segítségével NetScaler freebsd rendszerű alapul, és az Azure Site Recovery nem támogatja az operációs rendszer freebsd rendszerű védelméről. Kellene központi telepítése és konfigurálása az Azure piactéren új NetScaler berendezések Azure feladatátvétel után.


## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

A Citrix XenApp központi telepítés a következő összetevőket kell replikáció és a helyreállítás engedélyezéséhez.

* AD DNS-kiszolgáló védelme
* Az SQL server-adatbázis védelme
* Citrix kézbesítési vezérlő védelme
* Kirakat server védelméről.
* Védelmi XenApp főkiszolgáló (VDA)
* Citrix XenApp licenckiszolgáló védelme


**AD DNS-kiszolgáló replikáció**

Tekintse meg [védelme Active Directory és az Azure Site Recovery DNS](site-recovery-active-directory.md) kapcsolatos replikálódik, és a tartományvezérlő beállítása az Azure-ban.

**SQL Server adatbázisreplikáció**

Tekintse meg [SQL Server védelme az SQL Server vészhelyreállítási és az Azure Site Recovery](site-recovery-sql.md) részletes műszaki útmutatást az SQL Server-kiszolgálók védelme érdekében ajánlott beállításai.

Hajtsa végre a [Ez az útmutató](site-recovery-vmware-to-azure.md) elindítani, a többi összetevő virtuális gépek replikálása Azure-bA.

![XenApp összetevők védelme](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Számítási és hálózati beállítások**

Miután a gépek védelmének (állapotüzenet látható, a replikált elemek "Protected"), a számítási és hálózati beállításokat kell megadni.
A számítás és hálózat > számítási tulajdonságok, megadhatja, hogy az Azure virtuális gép nevét és a cél méretét.
A név ahhoz Azure-követelményeknek, ha szeretné, hogy módosítható. Is megtekintheti, és a cél hálózati alhálózat és az Azure virtuális géphez rendelendő IP-cím kapcsolatos információval.

Vegye figyelembe a következőket:

* A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha egy címet, amely nem használható feladatátadásra, a feladatátvétel nem fog működni. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.

* Az AD és a DNS-kiszolgáló esetén, megtartja a helyi cím megadását teszi lehetővé ugyanazt a címet az Azure virtuális hálózat DNS-kiszolgálóként.

A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

*   Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
*   Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
* Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.
*   Ha a virtuális gépnek több hálózati adapter lesz az összes csatlakoznak az ugyanazon a hálózaton.
*   Ha a virtuális gépnek több hálózati adaptert, majd az elsőt a listán látható lesz a virtuális gépet az Azure alapértelmezett hálózati adapter.


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A XenApp összetevő virtuális gépek replikáció engedélyezése után a következő lépés a helyreállítási terv.
A helyreállítási terv csoportok együtt a virtuális gépek a feladatátvétel és helyreállítás hasonló követelményei.  

**A helyreállítási terv létrehozásának lépései**

1. A XenApp összetevő virtuális gépek hozzáadása a helyreállítási terv.
2. Kattintson a helyreállítási terv -> + helyreállítási terv. Adja meg a helyreállítási terv egy egyszerűen elsajátítható nevét.
3. A VMware virtuális gépek: forrás kiválasztása VMware folyamatkiszolgálót, mint a Microsoft Azure cél, és üzembe helyezési modellel, az erőforrás-kezelő, majd kattintson a elemeket választhat ki.
4. A Hyper-V virtuális gépek: válassza ki a forrás VMM-kiszolgálóként, Microsoft Azure és a telepítési modell, erőforrás-kezelő cél és kattintson az elemeket választhat ki és válassza ki a XenApp telepítési virtuális gépek.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok hozzáadása

Adja hozzá az adott indítási sorrendje, parancsprogramok és manuális műveletek feladatátvételi csoportokat a helyreállítási terv testre szabható. A következő csoportok kell felvenni a helyreállítási terv.

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépen
2. Feladatátvételi 3: VDA fő kép VM
3. Feladatátvételi 4: Kézbesítési vezérlő és kirakat server virtuális gépen


### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása

Parancsfájlokat lehet futtatni előtt vagy után egy adott csoport a helyreállítási tervet. Manuális műveletek lehet is része és feladatátvétel során.

A testre szabott helyreállítási tervet a következőképpen néz az alábbi:

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépen
3. Feladatátvételi 3: VDA fő kép VM

   >[!NOTE]     
   >Csak egy helyszíni XenApp lépéseket, 4, 6, 7 tartalmazó manuális vagy parancsfájl műveletek alkalmazhatók > MCS/PVS katalógusok környezetben.

4. 3. csoport kézi vagy parancsfájl művelet: fő VDA VM a fő VDA VM leállítása, ha a feladatátvételt az Azure-ba kerül, a futó állapotot. Hozzon létre új MCS katalógusok Azure ARM használatával, a fő VDA VM szükség lehet leállítva (de lefoglalt) állapotát. Állítsa le a virtuális gép Azure-portálon.

5. Feladatátvételi 4: Kézbesítési vezérlő és kirakat server virtuális gépen
6. 3 manuális vagy parancsfájl 1 művelet:

    ***Azure RM állomás kapcsolat hozzáadása***

    Kézbesítési vezérlő gép kiépítéséhez új MCS katalógusok az Azure-ban Azure ARM-állomás kapcsolat létrehozása. Kövesse a lépéseket, ez a [cikk](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. 3 manuális vagy parancsfájl művelet 2:

    ***Hozza létre az MCS katalógusok az Azure-ban***

    A meglévő MCS vagy PVS klónok az elsődleges helyen nem replikálja az Azure-bA. Létre kell hoznia ezeket a replikált fő VDA és a kézbesítési vezérlőről kiépítés Azure ARM klónok. Kövesse a lépéseket, ez a [cikk](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) MCS katalógusok létrehozása az Azure-ban.

![Helyreállítási terv XenApp összetevők](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Parancsfájlokat, [hely](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) frissíteni a DNS és az új IP-címek a keresztül > virtuális gépek vagy csatolni egy terhelés-kiegyenlítő a feladatait átadó virtuális gép, ha szükséges.


## <a name="doing-a-test-failover"></a>A teszt feladatátvétel

Hajtsa végre a [Ez az útmutató](site-recovery-test-failover-to-azure.md) feladatátvételi teszt végrehajtásához.

![Helyreállítási terv](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>A feladatátvétel

Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) Ha feladatátvételt végez.

## <a name="next-steps"></a>Következő lépések

Is [további](https://aka.ms/citrix-xenapp-xendesktop-with-asr) kapcsolatos Citrix XenApp és XenDesktop a központi telepítések Ez a dokumentum replikálása. Nézze meg az útmutató a [replikálja más alkalmazások](site-recovery-workload.md) Site Recovery segítségével.
