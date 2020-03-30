---
title: VMware virtuális gépek engedélyezése vészhelyreállításhoz az Azure Site Recovery használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezheti a VMware VM replikációját a vészhelyreállításhoz az Azure Site Recovery szolgáltatás használatával
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257315"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>A vmware-virtuális gépek azure-beli replikációjának engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a helyszíni VMware virtuális gépek azure-ba replikációját.

## <a name="resolve-common-issues"></a>Gyakori problémák megoldása

* Minden lemeznek 4 TB-nál kisebbnek kell lennie.
* Az operációs rendszer lemezének alaplemeznek kell lennie, nem dinamikus lemeznek.
* A 2/UEFI-kompatibilis virtuális gépek esetében az operációs rendszer családnak Windows nak kell lennie, a rendszerindító lemeznek pedig 300 GB-nál kisebbnek kell lennie.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy a következőket feltételezi:

- [Állítsa be a helyszíni forráskörnyezetet.](vmware-azure-set-up-source.md)
- [Állítsa be a célkörnyezetet az Azure-ban.](vmware-azure-set-up-target.md)
- A kezdés előtt [ellenőrizze a követelményeket és az előfeltételeket.](vmware-physical-azure-support-matrix.md) Fontos megjegyezni a következőket:
    - [Támogatott operációs rendszerek](vmware-physical-azure-support-matrix.md#replicated-machines) replikált gépekhez.
    - [A tárolás/lemez](vmware-physical-azure-support-matrix.md#storage) támogatása.
    - [Az Azure-követelmények,](vmware-physical-azure-support-matrix.md#azure-vm-requirements) amelyeknek a helyszíni gépeknek meg kell felelniük.


## <a name="before-you-start"></a>Előkészületek
VMware virtuális gépek replikálásakor tartsa szem előtt ezt az információt:

* Az Azure felhasználói fiókjának rendelkeznie kell bizonyos [engedélyekkel](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) egy új virtuális gép Azure-ba replikálásának engedélyezéséhez.
* A VMware virtuális gépek 15 percenként kerülnek felderítésre. 15 percet vagy hosszabb időt is igénybe vehet, amíg a virtuális gépek a felderítés után megjelennek az Azure Portalon. Hasonlóképpen a felderítés 15 percet vagy hosszabb időt is igénybe vehet, ha új vCenter-kiszolgálót vagy vSphere-gazdat ad hozzá.
* A virtuális gépen végrehajtott környezeti módosítások (például a VMware-eszközök telepítése) a portálon történő frissítéshez 15 percet vagy hosszabb időt is igénybe vehet.
* Ellenőrizheti a VMware virtuális gépek utoljára felderített idejét: Tekintse meg a vCenter-kiszolgáló/vSphere gazdagép **Konfigurációs kiszolgálók** lapjának **Utolsó kapcsolatfelvételi időpontja** mezőt.
* Ha az ütemezett felderítésre való várakozás nélkül szeretne virtuális gépeket hozzáadni a replikációhoz, jelölje ki a konfigurációs kiszolgálót (de ne kattintson rá), és válassza a **Frissítés**lehetőséget.
* Ha engedélyezi a replikációt, ha a virtuális gép készen áll, a folyamatkiszolgáló automatikusan telepíti az Azure Site Recovery Mobility szolgáltatást.

## <a name="enable-replication"></a>A replikáció engedélyezése

Mielőtt követene az ebben a szakaszban leírt lépéseket, vegye figyelembe a következő információkat:
* Az Azure Site Recovery mostantól közvetlenül replikálja a felügyelt lemezeket az összes új replikációhoz. A folyamatkiszolgáló replikációs naplókat ír a célrégióban lévő gyorsítótártárfiókba. Ezek a naplók helyreállítási pontok létrehozására szolgálnak az asrseeddisk elnevezési konvencióval rendelkező replika kezelt lemezein.
* A Powershell támogatja a felügyelt lemezekre való replikálást az [Az.RecoveryServices modul 2.0.0-s verziójától kezdve](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* A feladatátvétel időpontjában a kiválasztott helyreállítási pont a célfelügyelt lemez létrehozásához használatos.
* A korábban a céltárfiókokra replikálásra konfigurált virtuális gépeket ez nem érinti.
* Egy új virtuális gép tárfiókok replikációja csak egy REprezentációs állapotátviteli (REST) API-n és Powershellen keresztül érhető el. Az Azure REST API 2016-08-10-es vagy 2018-01-10-es verziója a tárfiókokreplikálására.

Kérjük, kövesse az alábbi lépéseket a replikáció engedélyezéséhez:
1. Lépjen a **2.** > **Source** Miután első alkalommal engedélyezte a replikációt, válassza a **+Replikálás** lehetőséget a tárolóban a további virtuális gépek replikációjának engedélyezéséhez.
2. A **Forrás** lapon > **Forrás**lapon válassza ki a konfigurációs kiszolgálót.
3. A **Géptípus mezőben**válassza a **Virtuális gépek** vagy a Fizikai **gépek**lehetőséget.
4. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem releváns, ha fizikai számítógépeket replikál.
5. Válassza ki a folyamatkiszolgálót. Ha nincsenek további folyamatkiszolgálók létrehozva, a legördülő menüben elérhető lesz a konfigurációs kiszolgáló beépített folyamatkiszolgálója. Az egyes folyamatkiszolgálók állapota az ajánlott korlátok és egyéb paraméterek szerint van feltüntetve. Válasszon egy kifogástalan folyamatkiszolgálót. Nem lehet [kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) folyamatkiszolgálót választani. Elháríthatja [és megoldhatja](vmware-physical-azure-troubleshoot-process-server.md) a hibákat, **vagy** beállíthat egy [kibővített folyamatkiszolgálót.](vmware-azure-set-up-process-server-scale.md)
    ![Replikációs forrásablak engedélyezése](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> A [9.24-es verziókból](service-updates-how-to.md#links-to-currently-supported-update-rollups)további riasztások at vezetnek be a folyamatkiszolgáló állapotriasztásainak javítása érdekében. Frissítse a Site Recovery összetevőit 9.24-es vagy újabb verzióra az összes létrehozandó riasztáshoz.

6. A **Target**területen válassza ki azt az előfizetési és erőforráscsoportot, amelyben létre szeretné hozni a feladatátvevő virtuális gépeket. Válassza ki a központi telepítési modellt, amelyet használni szeretne az Azure-ban a sikertelen virtuális gépekhez.
2. Válassza ki az Azure-hálózat ot és az alhálózatot, amelyhez az Azure-beli virtuális gépek a feladatátvétel után csatlakoznak. A hálózatnak ugyanabban a régióban kell lennie, mint a Site Recovery szolgáltatás tárolójának.

   Válassza **a Beállítás most a kijelölt gépekhez** lehetőséget, ha a hálózati beállítást az összes olyan virtuális gépre alkalmazni szeretné, amelyet védelemre választott. Válassza **a Később konfigurálása** lehetőséget az Azure-hálózat virtuális gépenkéntkiválasztásához. Ha nem rendelkezik hálózattal, létre kell hoznia egyet. Ha hálózatot szeretne létrehozni az Azure Resource Manager használatával, válassza **az Új létrehozása lehetőséget.** Ha van ilyen, válasszon egy alhálózatot, majd kattintson **az OK gombra.**
   
   ![Replikációs célablak engedélyezése](./media/vmware-azure-enable-replication/enable-rep3.png)

1. **Virtuális gépek:** > Válassza ki a**virtuális gépeket,** válassza ki a replikálni kívánt virtuális gépeket. Csak olyan virtuális gépeket választhat ki, amelyekreplikációja engedélyezhető. Ezután kattintson az **OK** gombra. Ha nem lát vagy nem jelöl ki egy adott virtuális gépet, olvassa el a [Forrásgép nem szerepel az Azure Portalon a](https://aka.ms/doc-plugin-VM-not-showing) probléma megoldásához.

    ![Replikáció engedélyezése A virtuális gépek ablakának kiválasztása](./media/vmware-azure-enable-replication/enable-replication5.png)

1. A **Tulajdonságok** > **konfigurálása területen**válassza ki azt a fiókot, amelyet a folyamatkiszolgáló a Site Recovery Mobility szolgáltatás virtuális gépen való automatikus telepítéséhez használ. Emellett válassza ki a cél felügyelt lemez replikálni az adatforgalmi minták alapján.
10. Alapértelmezés szerint a forrás virtuális gép összes lemeze replikálódik. A lemezek replikációból való kizárásához törölje a jelet a **Másolat** jelölőnégyzetből azon lemezek esetében, amelyeket nem szeretne replikálni. Ezután kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. További információ [a lemezek kizárásáról](vmware-azure-exclude-disk.md).

    ![Replikációs konfigurálási tulajdonságok ablakának engedélyezése](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Replikációs **beállítások:** > Adja meg a**replikációs beállításokat,** ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. A replikációs házirend beállításait a **Beállítások** > **replikációs házirendházirendek** > házirendjének beállításai a Beállítások***beállításainak szerkesztése című*** > lapon**módosíthatja.** A házirendekre alkalmazott módosítások a replikálásra és az új virtuális gépekre is vonatkoznak.
1. Engedélyezze **a többvirtuális gép konzisztenciáját,** ha virtuális gépeket szeretne összegyűjteni egy replikációs csoportba. Adja meg a csoport nevét, majd kattintson az **OK gombra.**

    > [!NOTE]
    >    * A replikációs csoportban lévő virtuális gépek együtt replikálódnak, és megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkeznek, amikor feladatátvételt kapnak.
    >    * Gyűjtse össze a virtuális gépeket és a fizikai kiszolgálókat, hogy tükrözze a számítási feladatokat. A több virtuális gép konzisztenciájának engedélyezése hatással lehet a számítási feladatok teljesítményére. Ezt csak akkor tegye, ha a virtuális gépek ugyanazt a számítási feladatot futtatják, és konzisztenciára van szükség.

    ![Replikációs ablak engedélyezése](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Válassza ki a **Replikáció engedélyezése** elemet. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai ban követheti**nyomon. A **Véglegesítési védelem** feladat futtatása után a virtuális gép készen áll a feladatátvételre.

## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizze a forrás virtuális gép tulajdonságait. Ne feledje, hogy az Azure virtuális gép nevének meg kell felelnie az [Azure virtuális gép követelményeinek.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. Nyissa meg a **Replikált** > **elemek beállításait,** és jelölje ki a virtuális gépet. Az **Essentials** lap a virtuális gép beállításaival és állapotával kapcsolatos információkat jeleníti meg.
1. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
1. A **Számítási és hálózati** > **számítási tulajdonságokban**több virtuálisgép-tulajdonság is módosítható. 

    ![Számítási és hálózati tulajdonságok ablak](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure virtuális gép neve: Szükség esetén módosítsa a nevet az Azure-követelményeknek megfelelően.
    * Cél virtuális gép mérete vagy virtuális gép típusa: Az alapértelmezett virtuális gép mérete van kiválasztva néhány paraméter, amely tartalmazza a lemezszám, a hálózati adapterek száma, a processzormag-számláló, a memória és a rendelkezésre álló virtuálisgép-szerepkör méretek a cél Azure-régióban. Az Azure Site Recovery kiválasztja az első elérhető virtuális gép méretét, amely megfelel az összes feltételnek. A feladatátvétel előtt bármikor kiválaszthat egy másik virtuális gép méretet az igények alapján. Vegye figyelembe, hogy a virtuális gép lemezmérete is a forráslemez mérete, és csak feladatátvétel után módosítható. További információ a lemezméretekről és az IOPS-díjakról a [Windows virtuálisgép-lemezei méretezhetőségi és teljesítménycéljainak eléréséről.](../virtual-machines/windows/disk-scalability-targets.md)

    *  Erőforráscsoport: Kiválaszthatja azt az [erőforráscsoportot,](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)amelyből a virtuális gép egy post feladatátvétel részévé válik. Ezt a beállítást a feladatátvétel előtt bármikor módosíthatja. Feladatátvétel után, ha áttelepíti a virtuális gépet egy másik erőforráscsoportba, a virtuális gép védelmi beállításai megszakadnak.
    * Rendelkezésre állási csoport: Kiválaszthatja a [rendelkezésre állási csoport,](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) ha a virtuális gép kell egy része egy post feladatátvétel. Amikor kiválaszt egy rendelkezésre állási készletet, tartsa szem előtt a következő információkat:

        * Csak a megadott erőforráscsoporthoz tartozó rendelkezésre állási csoportok jelennek meg.  
        * A különböző virtuális hálózatokon lévő virtuális gépek nem lehetnek ugyanannak a rendelkezésre állási csoportnak a részei.
        * Csak az azonos méretű virtuális gépek lehetnek egy rendelkezésre állási csoport részét.
1. A célhálózat, az alhálózat és az Azure virtuális géphez rendelt IP-cím adatait is hozzáadhatja.
2. A **Lemezek alkalmazásban láthatja**a replikálandó virtuális gép operációs rendszerét és adatlemezeit.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

A cél IP-címe beállítható. Ha nem ad meg címet, a sikertelen virtuális gép DHCP-t használ. Ha olyan címet állít be, amely nem érhető el feladatátvételkor, a feladatátvétel nem működik. Ha a cím elérhető a teszt feladatátvételi hálózatban, használhatja ugyanazt a cél IP-címet a teszt feladatátvételhez.

A hálózati adapterek számát a célvirtuális géphez megadott méret határozza meg, az alábbiak szerint:

- Ha a hálózati adapterek száma a forrás virtuális gépen kisebb vagy egyenlő, hogy hány adapterek, amelyek a cél virtuális gép mérete, a cél rendelkezik ugyanannyi adapterek, mint a forrás.
- Ha a forrás virtuális gép adaptereinek száma meghaladja a cél virtuális gép méretének engedélyezett számát, a rendszer a célméretet használja. Ha például egy forrás virtuális gép két hálózati adapterrel rendelkezik, és a célvirtuális gép mérete négy, a cél virtuális gép két adapterrel rendelkezik. Ha a forrás virtuális gép két adapter, de a célméretecsak támogatja az egyik, a cél virtuális gép csak egy adapter.
- Ha a virtuális gép több hálózati adapterrel rendelkezik, mindegyik ugyanahhoz a hálózathoz csatlakozik. Emellett a listában látható első adapter lesz az Azure virtuális gép *alapértelmezett* hálózati adaptere. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft Frissítési Garancia-ügyfelek az Azure Hybrid Benefit használatával megtakaríthatják az Azure-ba áttelepített Windows Server-számítógépek licencelési költségeit. Az előny az Azure vész-helyreállítási is vonatkozik. Ha jogosult, hozzárendelheti a kedvezményt a virtuális gép, amely site recovery hoz létre, ha van egy feladatátvétel. Ehhez kövesse az alábbi lépéseket:
1. Nyissa meg a replikált virtuális gép **Számítógép és hálózat tulajdonságait.**
2. Válaszoljon, amikor megkérdezi, hogy rendelkezik-e olyan Windows Server-licenccel, amely alkalmassá teszi önt az Azure Hybrid Benefit használatára.
3. Győződjön meg arról, hogy rendelkezik egy jogosult Windows Server-licencfrissítési garanciával, amely segítségével alkalmazhatja a kedvezményt a feladatátvételkor létrehozandó virtuális gépre.
4. Mentse a replikált virtuális gép beállításait.

További információ az [Azure Hybrid Benefit szolgáltatásról.](https://aka.ms/azure-hybrid-benefit-pricing)



## <a name="next-steps"></a>További lépések

Miután a virtuális gép elérte a védett állapotot, próbálja meg egy [feladatátvétel,](site-recovery-failover.md) hogy ellenőrizze, hogy az alkalmazás jelenik-e meg az Azure-ban.

* További információ a rról, hogyan lehet megtisztítani a [regisztrációs és védelmi beállításokat](site-recovery-manage-registration-and-protection.md) a replikáció letiltásához.
* Megtudhatja, hogy [miként automatizálhatja a virtuális gépek replikációját a Powershell használatával.](vmware-azure-disaster-recovery-powershell.md)
