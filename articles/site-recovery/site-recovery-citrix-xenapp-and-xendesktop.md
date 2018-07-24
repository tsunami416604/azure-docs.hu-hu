---
title: Replikálni egy többrétegű a Citrix XenDesktop XenApp központi és az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan védelme és helyreállítása az Azure Site Recovery használatával a Citrix XenDesktop és XenApp telepítések.
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 45d366842416ddfa7b0153a1d075ee6de58e45a1
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213633"
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Egy többrétegű Citrix XenApp és xendesktop-példányok üzembe helyezés az Azure Site Recovery replikálása

## <a name="overview"></a>Áttekintés

A Citrix XenDesktop asztali virtualizálási megoldás, amely asztali környezetek és alkalmazások egy ondemand-szolgáltatás biztosít bármely felhasználó számára, bárhol is. A FlexCast kézbesítési technológia XenDesktop gyorsan és biztonságosan közvetíti alkalmazások és asztali felhasználók.
Még ma Citrix XenApp nem biztosít semmilyen vész helyreállítási funkciók.

Jó vész-helyreállítási megoldást is engedélyeznie kell a modellezést, a helyreállítási tervek körül a fenti összetett architektúrák, és emellett lehetővé teszi, hogy testre szabott lépéseket, ezért az egy kattintással biztosít különböző rétegek közötti alkalmazás-hozzárendelések kezeléséhez Képernyőkép arról megoldás és a egy alacsonyabb RTO egy esetleges vészhelyzet esetén.

Ez a dokumentum egy című útmutató lépésenként ismerteti, amellyel egy vész-helyreállítási megoldást a helyszíni Hyper-V és VMware vSphere-platformok Citrix XenApp-telepítéshez. Ez a dokumentum is ismerteti, hogyan hajthat végre egy feladatátvételi tesztet (vészhelyreállítási próbát) és a nem tervezett feladatátvétel az Azure-ban, a helyreállítási terv által támogatott konfigurációk és az előfeltételek.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megértette a következő:

1. [A virtuális gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md)
1. Hogyan [egy helyreállítási hálózat tervezése](site-recovery-network-design.md)
1. [Teszt feladatátvétel végrehajtása az Azure-bA](site-recovery-test-failover-to-azure.md)
1. [Feladatátvétel végrehajtása az Azure-bA](site-recovery-failover.md)
1. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
1. Hogyan [SQL Server replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Üzembe helyezési minták

A Citrix XenApp és xendesktop-példányok farm általában az alábbi üzembe helyezési minta rendelkezik:

**Üzembe helyezési minta**

A Citrix XenApp és xendesktop-példányok üzembe helyezési AD DNS-kiszolgáló, SQL-adatbázis-kiszolgáló, Citrix kézbesítési vezérlő, StoreFront-kiszolgáló, XenApp Master (VDA), Citrix XenApp licenckiszolgáló

![1. telepítési minta](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery támogatási

Ebben a cikkben céljából Citrix üzemelő példányok a VMware virtuális gépek vSphere 6.0 által kezelt / System Center VMM 2012 R2 szolgáló vészhelyreállítás.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Nincs a hatókörben | Igen
**VMware** | Nincs a hatókörben | Igen
**Fizikai kiszolgáló** | Nincs a hatókörben | Igen

### <a name="versions"></a>Verziók
Ügyfelei XenApp összetevők üzembe Hyper-V vagy VMware virtuális gépek vagy fizikai kiszolgálók. Az Azure Site Recovery szolgáltatás védi a fizikai és virtuális üzemelő példányokhoz is az Azure-bA.
XenApp Észrevételek 7.7 vagy újabb verzióját az Azure-ban támogatott, mivel csak ezek a fájlok központi telepítéseknél feladatátvételre alkalmas az Azure-bA a vész-helyreállítási vagy az áttelepítés.

### <a name="things-to-keep-in-mind"></a>Szem előtt tartani

1. Védelem és helyreállítás a helyszínen üzemelő példányok XenApp közzétett asztalok és a kiszolgálói operációs rendszert használ, hogy a XenApp gépek közzétett alkalmazások használata támogatott.

2. A helyszíni üzemelő operációs rendszer asztali gépek használata a virtuális asztalok, beleértve a Windows 10-es, továbbítására asztali VDI-ügyfél védelme és helyreállítása nem támogatott. Ennek az oka az ASR nem támogatja a desktopban OS'es gépek helyreállítását.  Emellett néhány ügyfél virtuális asztali operációs rendszer (például) Windows 7) még nem támogatottak a licencelése az Azure-ban. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

3.  Az Azure Site Recovery nem replikálja, és meglévő helyszíni MCS vagy PVS klónok védelme.
Hozza létre újra a e klónok használatával az Azure RM-Alkalmazáskézbesítési vezérlőt a kiépítés kell.

4. A NetScaler nem védhető NetScaler alapja a FreeBSD és az Azure Site Recovery nem támogatja a FreeBSD operációs rendszer védelmét az Azure Site Recovery használatával. Telepítheti és konfigurálhatja az Azure Marketplace-beli új NetScaler berendezések az Azure-bA a feladatátvételt követően kell.


## <a name="replicating-virtual-machines"></a>Virtuális gépek replikálásához

A Citrix XenApp-telepítés a következő összetevőket kell replikálásának és helyreállításának engedélyezéséhez meg kell védeni.

* AD DNS-kiszolgáló védelme
* Az SQL server-adatbázis védelme
* Citrix kézbesítési vezérlő védelméről
* StoreFront-kiszolgáló védelme.
* A XenApp Master (VDA) védelme
* A Citrix XenApp licenckiszolgáló védelméről


**AD DNS-kiszolgáló replikáció**

Tekintse meg [védelme az Active Directory és DNS az Azure Site Recovery](site-recovery-active-directory.md) az útmutató replikál, és a tartományvezérlő beállítása az Azure-ban.

**SQL database-kiszolgáló replikálása**

Tekintse meg [SQL Server védelme SQL Server vészhelyreállítási és az Azure Site Recovery](site-recovery-sql.md) vonatkozó részletes technikai útmutató a védelme érdekében az SQL Server-kiszolgálók ajánlott beállításokat.

Hajtsa végre a [Ez az útmutató](site-recovery-vmware-to-azure.md) elindítani, a többi összetevő virtuális gépek replikálása Azure-bA.

![A XenApp összetevők védelme](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Számítási és hálózati beállítások**

A gépek védett (állapota "Védett" replikált elemek felületen), a számítási és hálózati beállításokat konfigurálni kell.
A számítás és hálózat > számítási tulajdonságok, megadhatja, hogy az Azure virtuális gép nevét és a cél méretét.
Módosítsa a nevét, ha szeretné az Azure-követelményeknek megfelelő. Megtekintheti, és adja hozzá a célként megadott hálózat, alhálózat és az Azure virtuális géphez rendelendő IP-cím kapcsolatos információkat.

Vegye figyelembe a következőket:

* A cél IP-címe beállítható. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha egy címet, amely nem használható feladatátadásra, a feladatátvétel nem fog működni. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.

* Az AD/DNS-kiszolgáló a helyi cím megőrzése lehetővé teszi, hogy adja meg ugyanazt a címet az Azure-beli virtuális hálózat DNS-kiszolgálóként.

A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:

*   Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
*   Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
* Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.
*   Ha a virtuális gép több hálózati adapter lesz az összes az ugyanazon hálózathoz csatlakoznak.
*   Ha a virtuális gép több hálózati adapterrel rendelkezik, az elsőt a listában látható lesz az alapértelmezett hálózati adapter az Azure-beli virtuális gépen.


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

Replikációs összetevő XenApp-alapú virtuális gépek engedélyezése után a következő lépés az helyreállítási terv létrehozásához.
A helyreállítási terv csoportok együtt rendelkező virtuális gépek feladatátvétele és helyreállítása hasonló követelményeinek.  

**A helyreállítási terv létrehozásának lépései**

1. A XenApp összetevő virtuális gépek hozzáadása a helyreállítási terv.
2. Kattintson a helyreállítási terv -> + a helyreállítási terv. Adja meg a helyreállítási terv egy intuitív nevét.
3. A VMware virtuális gépek: VMware folyamatkiszolgáló, a cél a Microsoft Azure, és üzembe helyezési modellt használja, mint a Resource Manager, majd kattintson a elemek kiválasztásával forrás kiválasztása.
4. Hyper-V virtuális gépek: válassza ki a forrás VMM-kiszolgálóval, a Microsoft Azure, valamint a mint a Resource Manager üzemi modell cél és kattintson az elemek kiválasztását és válassza ki a XenApp üzembe helyezés virtuális gépeken.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok felvétele

A helyreállítási terv feladatátvételi csoportok az adott indítási sorrend, szkriptek vagy manuális műveletek hozzáadása testre szabható. A következő csoportok hozzá kell adni a helyreállítási tervbe.

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépek
2. Feladatátvételi 3: VDA fő kép virtuális Gépet
3. Feladatátvételi 4: Alkalmazáskézbesítési vezérlőt és StoreFront-kiszolgáló virtuális gépek


### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási tervbe szkriptek hozzáadása

Parancsfájlokat lehet futtatni előtt vagy után adott csoportban lévő helyreállítási terv. Manuális műveletek is lehet is tartalmaz és feladatátvétel során.

A testre szabott helyreállítási terv néz ki az alábbi:

1. Feladatátvételi csoport1: AD DNS
2. Feladatátvételi csoport2: SQL Server virtuális gépek
3. Feladatátvételi 3: VDA fő kép virtuális Gépet

   >[!NOTE]     
   >Csak egy helyszíni xenapp alkalmazandók, 4, 6, 7 manuális vagy parancsfájl műveleteket tartalmazó lépéseket > MCS/PVS katalógusok környezetet.

4. 3. csoport manuális vagy parancsfájl művelet: fő VDA virtuális gép a fő VDA virtuális gép leállítása az Azure-ba irányuló feladatátvételkor futó állapotban lesz. Hozhat létre új MCS-katalógusok Azure ARM használatával, a fő VDA virtuális gép szükség lehet a Leállítva (de lefoglalt) állapot. Állítsa le a virtuális gép az Azure Portalról.

5. Feladatátvételi 4: Alkalmazáskézbesítési vezérlőt és StoreFront-kiszolgáló virtuális gépek
6. 3 manuális vagy parancsfájl 1 művelet:

    ***Azure RM-gazdagép kapcsolat hozzáadása***

    Hozzon létre Azure ARM-gazdagép kapcsolati Alkalmazáskézbesítési vezérlőt gép kiépítése az Azure-ban új MCS-katalógusok. Kövesse a lépéseket, amint azt ez [cikk](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. 3 manuális vagy parancsfájl művelet 2:

    ***Hozza létre újból az Azure-ban MCS-katalógusok***

    Az Azure-ba nem lesznek replikálva az elsődleges hely meglévő MCS vagy PVS klónok. Hozza létre újra a e klónok használatával a replikált főkiszolgáló VDA és az Azure ARM-ből Alkalmazáskézbesítési vezérlőt kell. Kövesse a lépéseket, amint azt ez [cikk](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) MCS-katalógusok létrehozása az Azure-ban.

![Helyreállítási terv XenApp-összetevők](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >A parancsfájlokat használhat [hely](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) frissíteni a DNS-ben az új IP-címek a sikertelen a keresztül > virtuális gépek vagy csatolni a feladatait egy terheléselosztó feladatátviteli virtuális géphez, ha szükséges.


## <a name="doing-a-test-failover"></a>Teszt feladatátvétel végrehajtása

Hajtsa végre a [Ez az útmutató](site-recovery-test-failover-to-azure.md) feladatátvételi teszt végrehajtásához.

![Helyreállítási terv](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Feladatátvétel végrehajtása

Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) Ha feladatátvételt végez.

## <a name="next-steps"></a>További lépések

Is [további](https://aka.ms/citrix-xenapp-xendesktop-with-asr) kapcsolatos Citrix XenApp és xendesktop-példányok központi telepítések a tanulmányban szereplő replikálásához. Tekintse meg az útmutató [más alkalmazások replikálása](site-recovery-workload.md) Site Recovery használatával.
