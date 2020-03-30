---
title: A Citrix XenDesktop/XenApp vészhelyreállításának beállítása az Azure Site Recovery szolgáltatással
description: Ez a cikk ismerteti, hogyan állíthat be vész-helyreállítási fo Citrix XenDesktop és a XenApp-telepítések az Azure Site Recovery használatával.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084556"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>vészhelyreállítás beállítása többrétegű Citrix XenApp és XenDesktop telepítéshez



A Citrix XenDesktop egy asztali virtualizációs megoldás, amely asztali számítógépeket és alkalmazásokat kínál igény szerinti szolgáltatásként bármely felhasználónak, bárhol. A FlexCast szállítási technológiával a XenDesktop gyorsan és biztonságosan képes alkalmazásokat és asztali számítógépeket szállítani a felhasználóknak.
Ma a Citrix XenApp nem biztosít vész-helyreállítási képességeket.

Egy jó vész-helyreállítási megoldás, lehetővé kell tennie a helyreállítási tervek modellezése körül a fenti összetett alkalmazásarchitektúrák és azt is képes nek kell lennie, hogy adjunk testre szabott lépéseket kezelni alkalmazás leképezések különböző szintek között, így biztosítva egy kattintással biztos lövés megoldás esetén a katasztrófa vezető alacsonyabb RTO.

Ez a dokumentum lépésről lépésre útmutatást nyújt a helyszíni Citrix XenApp-telepítések vész-helyreállítási megoldásának létrehozásához a Hyper-V és vMware vSphere platformokon. Ez a dokumentum azt is ismerteti, hogyan hajthatja végre a teszt feladatátvétel (vész-helyreállítási gyakorlat) és a nem tervezett feladatátvétel az Azure helyreállítási tervek, a támogatott konfigurációk és előfeltételek használatával.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy megértette a következőket:

1. [Virtuális gép replikálása az Azure-ba](site-recovery-vmware-to-azure.md)
1. Helyreállítási [hálózat tervezése](site-recovery-network-design.md)
1. [Tesztfeladat-átvétel az Azure-ba](site-recovery-test-failover-to-azure.md)
1. [Feladat-átvétel az Azure-ba](site-recovery-failover.md)
1. Tartományvezérlő [replikálása](site-recovery-active-directory.md)
1. Az [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Telepítési minták

A Citrix XenApp és a XenDesktop farmok általában a következő telepítési mintával rendelkeznek:

**Telepítési minta**

Citrix XenApp és XenDesktop telepítés AD DNS-kiszolgálóval, SQL adatbázis-kiszolgálóval, Citrix kézbesítési vezérlővel, StoreFront szerverrel, XenApp Masterrel (VDA), Citrix XenApp licenckiszolgálóval

![1. telepítési minta](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery támogatása

E cikk alkalmazásában a Citrix a vSphere 6.0 / System Center VMM 2012 R2 által kezelt VMware virtuális gépeken telepített központi telepítéseket a DR beállításához használták.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Nem terjed ki a rendelet hatálya | Igen
**Vmware** | Nem terjed ki a rendelet hatálya | Igen
**Fizikai kiszolgáló** | Nem terjed ki a rendelet hatálya | Igen

### <a name="versions"></a>Verziók
Az ügyfelek telepíthetik a XenApp-összetevőket Hyper-V vagy VMware vagy Fizikai kiszolgálókon futó virtuális gépekként. Az Azure Site Recovery mind a fizikai, mind a virtuális azure-telepítéseket képes megvédeni.
Mivel a XenApp 7.7-es vagy újabb verzióját támogatja az Azure, csak az ezekkel a verziókkal rendelkező telepítések adhatók át az Azure-nak vészhelyreállításvagy áttelepítés esetén.

### <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra

1. A helyszíni telepítések védelme és helyreállítása a Kiszolgáló operációs rendszer gépei segítségével a XenApp közzétett alkalmazások és a XenApp közzétett asztali számítógépek kézbesítéséhez támogatott.

2. A helyszíni telepítések védelme és helyreállítása asztali operációs rendszerű gépek használatával asztali virtuális asztalokhoz, beleértve a Windows 10-et is, nem támogatott. Ennek az az oka, hogy a Site Recovery nem támogatja az asztali operációs rendszerrel rendelkező gépek helyreállítását.  Is, néhány kliens virtuális asztali operációs rendszerek (pl. Windows 7) még nem támogatott a licencelés az Azure-ban. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

3.  Az Azure Site Recovery nem replikálhatja és védheti a meglévő helyszíni MCS- vagy PVS-klónokat.
Újra létre kell hoznia ezeket a klónokat az Azure RM-kiépítés t a kézbesítési vezérlőből használatával.

4. A NetScaler nem védhető az Azure Site Recovery használatával, mivel a NetScaler a FreeBSD-n alapul, és az Azure Site Recovery nem támogatja a FreeBSD operációs rendszer védelmét. Az Azure Market-feladatátvétel után egy új NetScaler-készüléket kell üzembe helyeznie és konfigurálnia az Azure-ba való átvétel után.


## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálása

A Citrix XenApp központi telepítésének következő összetevőit védeni kell a replikáció és a helyreállítás engedélyezéséhez.

* Az AD DNS-kiszolgáló védelme
* AZ SQL adatbázis-kiszolgáló védelme
* A Citrix kézbesítési vezérlő védelme
* A StoreFront kiszolgáló védelme.
* A XenApp Master (VDA) védelme
* A Citrix XenApp licenckiszolgáló védelme


**AD DNS-kiszolgáló replikációja**

Tekintse meg az [Active Directory és a DNS védelme az Azure Site Recovery](site-recovery-active-directory.md) című témakört az Azure-beli tartományvezérlő replikálásához és konfigurálásához.

**SQL adatbázis-kiszolgáló replikációja**

Az [SQL Server védelme az SQL Server vész-helyreállítási rendszerrel című](site-recovery-sql.md) oldalon és az Azure Site Recovery szolgáltatással kapcsolatos részletes technikai útmutatást az SQL-kiszolgálók védelmére ajánlott lehetőségekről.

Kövesse [ezt az útmutatást](site-recovery-vmware-to-azure.md) a többi összetevő virtuális gépek replikálásának megkezdéséhez az Azure-ba.

![A XenApp-összetevők védelme](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Számítási és hálózati beállítások**

A gépek védelme után (az állapot "Védettként" jelenik meg a Replikált elemek alatt), a számítási és hálózati beállításokat konfigurálni kell.
A számítási és hálózati > számítási tulajdonságok, megadhatja az Azure vm nevét és a cél mérete.
Ha szükséges, írja át úgy a nevet, hogy az megfeleljen az Azure-követelményeknek. Megtekintheti és hozzáadhatja a célhálózatra, az alhálózatra és az Azure-beli virtuális géphez hozzárendelt IP-címre vonatkozó információkat is.

Vegye figyelembe a következőket:

* A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címet állít be, amely nem érhető el feladatátvételkor, a feladatátvétel nem fog működni. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.

* Az AD/DNS-kiszolgáló esetében a helyszíni cím megőrzése lehetővé teszi, hogy ugyanazt a címet adja meg, mint az Azure virtuális hálózat DNS-kiszolgálója.

A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

*   Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
*   Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
* Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.
*   Ha a virtuális gép több hálózati adapterrel rendelkezik, akkor mindegyik ugyanahhoz a hálózathoz fog csatlakozni.
*   Ha a virtuális gép több hálózati adapterrel rendelkezik, majd a listában látható első az Azure virtuális gép alapértelmezett hálózati adaptere lesz.


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

Miután a XenApp-összetevő virtuális gépei számára engedélyezve van a replikáció, a következő lépés egy helyreállítási terv létrehozása.
A helyreállítási terv csoportok csoportok virtuális gépek hasonló követelmények feladatátvételi és helyreállítási.  

**Helyreállítási terv létrehozásának lépései**

1. Adja hozzá a XenApp-összetevő virtuális gépeit a helyreállítási tervhez.
2. Kattintson a helyreállítási tervek -> + helyreállítási terv elemre. Adjon meg egy intuitív nevet a helyreállítási tervnek.
3. VMware virtuális gépek esetén: Válassza ki a forrást VMware folyamatkiszolgálóként, a microsoft Azure-ként, a telepítési modellt erőforrás-kezelőként, és kattintson az Elemek kiválasztása elemre.
4. Hyper-V virtuális gépek esetén: Válassza ki a forrást VMM-kiszolgálóként, a microsoft Azure-ként, a telepítési modellt erőforrás-kezelőként, majd kattintson az Elemek kiválasztása elemre, majd válassza ki a XenApp központi telepítéséhez szükséges virtuális gépeket.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása feladatátvételi csoportokhoz

A helyreállítási tervek testreszabhatók feladatátvételi csoportok hozzáadásához adott indítási sorrendhez, parancsfájlokhoz vagy manuális műveletekhez. A következő csoportokat kell hozzáadni a helyreállítási tervhez.

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépek
2. Feladatátvételi csoport3: VDA-főrendszerkép virtuális gép
3. Feladatátvételi csoport4: Kézbesítési vezérlő és StoreFront-kiszolgáló virtuális gépei


### <a name="adding-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez

A parancsfájlok futtathatók a helyreállítási terv ben egy adott csoport előtt vagy után. Manuális műveletek is szerepelhetnek, és feladatátvétel során hajthatók végre.

A személyre szabott helyreállítási terv az alábbira néz:

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépek
3. Feladatátvételi csoport3: VDA-főrendszerkép virtuális gép

   >[!NOTE]     
   >A manuális vagy parancsfájlműveleteket tartalmazó 4 >., 6.

4. 3. csoport Kézi vagy parancsfájl művelet: Állítsa le a fő VDA vm.
A master VDA virtuális gép, amikor feladatátvételt az Azure-ba lesz egy futó állapotban lesz. Új MCS-katalógusok létrehozása az Azure hosting használatával, a fő VDA virtuális gép szükséges, hogy leállított (de lefoglalt) állapotban kell lennie. Állítsa le a virtuális gép az Azure Portalon.

5. Feladatátvételi csoport4: Kézbesítési vezérlő és StoreFront-kiszolgáló virtuális gépei
6. 3. csoport manuális vagy parancsfájlművelet 1:

    ***Azure RM-állomáskapcsolat hozzáadása***

    Hozzon létre Azure-gazdatakapcsolatot a kézbesítési vezérlő gépében új MCS-katalógusok azure-beli kiépítéséhez. Kövesse a [cikkben](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)ismertetett lépéseket.

7. 3. csoport kézi vagy script művelet 2:

    ***MCS-katalógusok újbóli létrehozása az Azure-ban***

    Az elsődleges helyen lévő meglévő MCS- vagy PVS-klónok nem lesznek replikálva az Azure-ba. Újra létre kell hoznia ezeket a klónokat a replikált fő VDA és az Azure-kiépítés a kézbesítési vezérlőből használatával. Kövesse a [cikkben](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) ismertetett lépéseket az MCS-katalógusok létrehozásához az Azure-ban.

![Helyreállítási terv a XenApp-összetevőkhöz](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >A [helyszínen](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) lévő parancsfájlok segítségével frissítheti a DNS-t a feladatátvételi >virtuális gépek új IP-hálózatával, vagy szükség esetén csatolhat egy terheléselosztót a feladatátvételi virtuális gépen.


## <a name="doing-a-test-failover"></a>Tesztfeladat-átvétel e

Kövesse [ezt az útmutatást](site-recovery-test-failover-to-azure.md) a teszt feladatátvételhez.

![Helyreállítási terv](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Feladatátvétel

Kövesse [ezt az útmutatást,](site-recovery-failover.md) amikor feladatátvételt végez.

## <a name="next-steps"></a>További lépések

A Citrix XenApp és a XenDesktop központi telepítéseireplikálásáról ebben a tanulmányban [olvashat bővebben.](https://aka.ms/citrix-xenapp-xendesktop-with-asr) Tekintse meg az útmutatót [replikálni más alkalmazások](site-recovery-workload.md) segítségével Site Recovery.
