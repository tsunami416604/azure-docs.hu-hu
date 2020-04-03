---
title: VMware virtuális gépek engedélyezése vészhelyreállításhoz az Azure Site Recovery használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezheti a VMware VM replikációját a vészhelyreállításhoz az Azure Site Recovery szolgáltatás használatával
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584146"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>A vmware-virtuális gépek azure-beli replikációjának engedélyezése

Ez a cikk ismerteti, hogyan engedélyezheti a helyszíni VMware virtuális gépek (VM) azure-ba replikációját.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy a rendszer megfelel az alábbi feltételeknek:

- [Állítsa be a helyszíni forráskörnyezetet.](vmware-azure-set-up-source.md)
- [Állítsa be a célkörnyezetet az Azure-ban.](vmware-azure-set-up-target.md)
- A kezdés előtt [ellenőrizze a követelményeket és az előfeltételeket.](vmware-physical-azure-support-matrix.md) Fontos megjegyezni a következőket:
  - [Támogatott operációs rendszerek](vmware-physical-azure-support-matrix.md#replicated-machines) replikált gépekhez.
  - [A tárolás/lemez](vmware-physical-azure-support-matrix.md#storage) támogatása.
  - [Az Azure-követelmények,](vmware-physical-azure-support-matrix.md#azure-vm-requirements) amelyeknek a helyszíni gépeknek meg kell felelniük.

### <a name="resolve-common-issues"></a>Gyakori problémák megoldása

- Minden lemeznek 4 TB-nál kisebbnek kell lennie.
- Az operációs rendszer lemezének alaplemeznek kell lennie, nem dinamikus lemeznek.
- A 2.

## <a name="before-you-start"></a>Előkészületek

VMware virtuális gépek replikálásakor tartsa szem előtt ezt az információt:

- Az Azure felhasználói fiókjának rendelkeznie kell bizonyos [engedélyekkel](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) egy új virtuális gép Azure-ba replikálásának engedélyezéséhez.
- A VMware virtuális gépek 15 percenként kerülnek felderítésre. 15 percet vagy többet is igénybe vehet, amíg a virtuális gépek a felderítés után megjelennek az Azure Portalon. Új vCenter-kiszolgáló vagy vSphere-gazdagép hozzáadásakor a felderítés 15 percet vagy többet is igénybe vehet.
- A virtuális gépen a környezet változásainak frissítése a portálon 15 percet vagy többet is igénybe vehet. Például a VMware eszközök telepítése.
- Ellenőrizheti a VMware virtuális gépek utoljára felderített idejét: Tekintse meg a vCenter-kiszolgáló/vSphere gazdagép **Konfigurációs kiszolgálók** lapjának **Utolsó kapcsolatfelvételi időpontja** mezőt.
- Ha az ütemezett felderítésre való várakozás nélkül szeretne virtuális gépeket hozzáadni a replikációhoz, jelölje ki a konfigurációs kiszolgálót (de ne kattintson rá), és válassza a **Frissítés**lehetőséget.
- Ha engedélyezi a replikációt, ha a virtuális gép előkészíti, a folyamatkiszolgáló automatikusan telepíti az Azure Site Recovery Mobility szolgáltatást a virtuális gépen.

## <a name="enable-replication"></a>A replikáció engedélyezése

Az ebben a szakaszban ismertetett lépések előtt tekintse át az alábbi információkat:

- Az Azure Site Recovery mostantól közvetlenül replikálja a felügyelt lemezeket az összes új replikációhoz. A folyamatkiszolgáló replikációs naplókat ír a célrégióban lévő gyorsítótártárfiókba. Ezek a naplók helyreállítási pontok létrehozására szolgálnak a replikán kezelt lemezeken, amelyek elnevezési konvenciója `asrseeddisk`.
- A felügyelt lemezekre való replikáció PowerShell-támogatása az [Az.RecoveryServices modul 2.0.0-s verziójával](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) kezdődik
- A feladatátvétel időpontjában a kiválasztott helyreállítási pont a célfelügyelt lemez létrehozásához használatos.
- A korábban a céltárfiókokra replikálásra konfigurált virtuális gépeket ez nem érinti.
- Egy új virtuális gép tárfiókok replikációja csak egy REprezentációs állapotátviteli (REST) API-n és powershellen keresztül érhető el. Az Azure REST API 2016-08-10-es vagy 2018-01-10-es verziója a tárfiókokreplikációhoz.

A replikáció engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Lépjen a **2.** > **Source** Miután első alkalommal engedélyezte a replikációt, válassza a **+Replikálás** lehetőséget a tárolóban a további virtuális gépek replikációjának engedélyezéséhez.
1. A **Forrás** lapon > **Forrás**lapon válassza ki a konfigurációs kiszolgálót.
1. A **Géptípus mezőben**válassza a **Virtuális gépek** vagy a Fizikai **gépek**lehetőséget.
1. A **vCenter/vSphere hipervizor** mezőben válassza ki a vSphere-gazdagépet felügyelő vCenter-kiszolgálót, vagy válassza ki magát a gazdagépet. Ez a beállítás nem releváns, ha fizikai számítógépeket replikál.
1. Válassza ki a folyamatkiszolgálót. Ha nem hoznak létre további folyamatkiszolgálókat, a konfigurációs kiszolgáló beépített folyamatkiszolgálója elérhető lesz a legördülő menüben. Az egyes folyamatkiszolgálók állapota az ajánlott korlátok és egyéb paraméterek szerint van feltüntetve. Válasszon egy kifogástalan folyamatkiszolgálót. [Kritikus](vmware-physical-azure-monitor-process-server.md#process-server-alerts) folyamatkiszolgáló nem választható ki. Elháríthatja [és megoldhatja](vmware-physical-azure-troubleshoot-process-server.md) a hibákat, **vagy** beállíthat egy [kibővített folyamatkiszolgálót.](vmware-azure-set-up-process-server-scale.md)

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Replikációs forrásablak engedélyezése":::

   > [!NOTE]
   > A [9.24-es verziótól](site-recovery-whats-new.md)kezdődően további riasztásokat vezetnek be a folyamatkiszolgáló állapotriasztásainak javítása érdekében. Frissítse a Site Recovery összetevőit a 9.24-es vagy újabb verzióra az összes létrehozandó riasztáshoz.

1. A **Target területen**válassza ki azt az előfizetési és erőforráscsoportot, ahol létre szeretné hozni a feladatátvételt a virtuális gépeken. Válassza ki a központi telepítési modellt, amelyet használni szeretne az Azure-ban a feladatátvételi virtuális gépek.
1. Válassza ki az Azure-hálózat ot és az alhálózatot, amelyhez az Azure-beli virtuális gépek a feladatátvétel után csatlakoznak. A hálózatnak ugyanabban a régióban kell lennie, mint a Site Recovery szolgáltatás tárolójának.

   Válassza **a Beállítás most a kijelölt gépekhez** lehetőséget, ha a hálózati beállítást az összes olyan virtuális gépre alkalmazni szeretné, amelyet védelemre választott. Válassza **a Később konfigurálása** lehetőséget az Azure-hálózat virtuális gépenkéntkiválasztásához. Ha nem rendelkezik hálózattal, létre kell hoznia egyet. Ha hálózatot szeretne létrehozni az Azure Resource Manager használatával, válassza **az Új létrehozása lehetőséget.** Ha van ilyen, válasszon egy alhálózatot, majd kattintson **az OK gombra.**

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Replikációs célablak engedélyezése":::

1. **Virtuális gépek:** > Válassza ki a**virtuális gépeket,** válassza ki a replikálni kívánt virtuális gépeket. Csak olyan virtuális gépeket választhat ki, amelyekreplikációja engedélyezhető. Ezután kattintson az **OK** gombra. Ha nem lát vagy nem jelöl ki egy adott virtuális gépet, olvassa el a [Forrásgép nem szerepel az Azure Portalon a](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) probléma megoldásához.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Replikáció engedélyezése A virtuális gépek ablakának kiválasztása":::

1.  > Tulajdonságok **Properties****konfigurálása tulajdonsága,** válassza ki a fiókot, hogy a folyamat kiszolgáló automatikusan telepíti a Site Recovery Mobility szolgáltatás a virtuális gép. Azt is válassza ki, hogy milyen típusú cél felügyelt lemezt használni a replikációaz adatforgalmi minták alapján.
1. Alapértelmezés szerint a forrás virtuális gép összes lemeze replikálódik. A lemezek replikációból való kizárásához törölje a jelet a **Másolat** jelölőnégyzetből azon lemezek esetében, amelyeket nem szeretne replikálni. Ezután kattintson az **OK** gombra. A további tulajdonságokat később is beállíthatja. [További információ](vmware-azure-exclude-disk.md) a lemezek kizárásáról.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Replikációs konfigurálási tulajdonságok ablakának engedélyezése":::

1. A **Replikációbeállításaitól** > Konfigurálja a**replikációs beállításokat,** ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. A replikációs házirend beállításait a **Beállítások** > **replikációs házirendházirendek** > házirendjének beállításai a Beállítások_beállításainak szerkesztése című_ > lapon**módosíthatja.** A házirendekre alkalmazott módosítások a replikálásra és az új virtuális gépekre is vonatkoznak.
1. Ha virtuális gépeket szeretne gyűjteni egy replikációs csoportba, engedélyezze **a többvirtuális gép konzisztenciáját.** Adja meg a csoport nevét, majd kattintson az **OK gombra.**

   > [!NOTE]
   > - A replikációs csoportban lévő virtuális gépek együtt replikálódnak, és megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokkal rendelkeznek, amikor feladatátvételt kapnak.
   > - Gyűjtse össze a virtuális gépeket és a fizikai kiszolgálókat, hogy tükrözze a számítási feladatokat. A több virtuális gép konzisztenciájának engedélyezése hatással lehet a számítási feladatok teljesítményére. Ezt csak akkor tegye, ha a virtuális gépek ugyanazt a számítási feladatot futtatják, és konzisztenciára van szükség.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Replikációs ablak engedélyezése":::

1. Válassza ki a **Replikáció engedélyezése** elemet. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai ban követheti**nyomon. A **Véglegesítési védelem** feladat futtatása után a virtuális gép készen áll a feladatátvételre.

## <a name="view-and-manage-vm-properties"></a>A virtuális gépek tulajdonságainak megtekintése és kezelése

Ezután ellenőrizze a forrás virtuális gép tulajdonságait. Ne feledje, hogy az Azure virtuális gép nevének meg kell felelnie az [Azure virtuális gép követelményeinek.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. Nyissa meg a **Replikált** > **elemek beállításait,** és jelölje ki a virtuális gépet. Az **Essentials** lap a virtuális gép beállításaival és állapotával kapcsolatos információkat jeleníti meg.
1. A **Tulajdonságok** résznél tekintheti meg a virtuális gép replikációs és feladatátvételi adatait.
1. A **Számítási és hálózati** > **számítási tulajdonságokban**több virtuálisgép-tulajdonság is módosítható.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Számítási és hálózati tulajdonságok ablak":::

   - **Azure virtuális gép neve:** szükség esetén módosítsa a nevet az Azure-követelményeknek megfelelően.
   - **Cél virtuális gép mérete vagy virtuális gép típusa:** Az alapértelmezett virtuális gép mérete a lemezszám, a hálózati adapterek számát, a processzormagok számát, a memóriát és a cél Azure-régióban rendelkezésre álló virtuálisgép-szerepkör-méretek alapján választjuk ki a rendszer. Az Azure Site Recovery kiválasztja az első elérhető virtuális gép méretét, amely megfelel az összes feltételnek. A feladatátvétel előtt bármikor kiválaszthat egy másik virtuális gép méretet az igények alapján. A virtuális gép lemezmérete is a forráslemez mérete, és csak feladatátvétel után módosítható. További információ a lemezméretekről és az IOPS-díjakról a [Windows virtuálisgép-lemezei méretezhetőségi és teljesítménycéljainak eléréséről.](/azure/virtual-machines/windows/disk-scalability-targets)
   - **Erőforráscsoport**: Kiválaszthatja azt az [erőforráscsoportot,](/azure/azure-resource-manager/management/overview#resource-groups)amelyből a virtuális gép a post feladatátvétel részévé válik. Ezt a beállítást a feladatátvétel előtt bármikor módosíthatja. Feladatátvétel után, ha áttelepíti a virtuális gépet egy másik erőforráscsoportba, a virtuális gép védelmi beállításai megszakadnak.
   - **Rendelkezésre állási csoport:** Kiválaszthatja a [rendelkezésre állási csoport,](/azure/virtual-machines/windows/tutorial-availability-sets) ha a virtuális gép kell egy része egy post feladatátvétel. Amikor kiválaszt egy rendelkezésre állási készletet, tartsa szem előtt a következő információkat:
     - Csak a megadott erőforráscsoporthoz tartozó rendelkezésre állási csoportok jelennek meg.
     - A különböző virtuális hálózatokon lévő virtuális gépek nem lehetnek ugyanannak a rendelkezésre állási csoportnak a részei.
     - Csak az azonos méretű virtuális gépek lehetnek egy rendelkezésre állási csoport részét.

1. A célhálózat, az alhálózat és az Azure virtuális géphez rendelt IP-cím adatait is hozzáadhatja.
1. A **Lemezek alkalmazásban láthatja**a replikálandó virtuális gép operációs rendszerét és adatlemezeit.

### <a name="configure-networks-and-ip-addresses"></a>Hálózatok és IP-címek konfigurálása

Beállíthatja a cél IP-címet:

- Ha nem ad meg címet, a feladatátvételi virtuális gép DHCP-t használ.
- Ha olyan címet állít be, amely nem érhető el feladatátvételkor, a feladatátvétel nem működik.
- Ha a cím elérhető a teszt feladatátvételi hálózatban, használhatja ugyanazt a cél IP-címet a teszt feladatátvételhez.

A hálózati adapterek számát a célvirtuális géphez megadott méret határozza meg, az alábbiak szerint:

- Ha a hálózati adapterek száma a forrás virtuális gépen kisebb vagy egyenlő, hogy hány adapterek, amelyek a cél virtuális gép mérete, a cél rendelkezik ugyanannyi adapterek, mint a forrás.
- Ha a forrás virtuális gép adaptereinek száma meghaladja a cél virtuális gép méretének engedélyezett számát, a rendszer a célméretet használja. Ha például egy forrás virtuális gép két hálózati adapterrel rendelkezik, és a célvirtuális gép mérete négy, a cél virtuális gép két adapterrel rendelkezik. Ha a forrás virtuális gép két adapter, de a célméretecsak támogatja az egyik, a cél virtuális gép csak egy adapter.
- Ha a virtuális gép több hálózati adapterrel rendelkezik, mindegyik ugyanahhoz a hálózathoz csatlakozik. Emellett a listában látható első adapter lesz az Azure virtuális gép alapértelmezett hálózati adaptere.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

A Microsoft Frissítési Garancia-ügyfelek az Azure Hybrid Benefit használatával megtakaríthatják az Azure-ba áttelepített Windows Server-számítógépek licencelési költségeit. Az előny az Azure vész-helyreállítási is vonatkozik. Ha jogosult, hozzárendelheti a kedvezményt a virtuális gép, amely site recovery hoz létre, ha van egy feladatátvétel.

1. Nyissa meg a replikált virtuális gép **Számítógép és hálózat tulajdonságait.**
1. Válaszoljon, amikor megkérdezi, hogy rendelkezik-e olyan Windows Server-licenccel, amely alkalmassá teszi önt az Azure Hybrid Benefit használatára.
1. Győződjön meg arról, hogy rendelkezik egy jogosult Windows Server-licencfrissítési garanciával, amely segítségével alkalmazhatja a kedvezményt a feladatátvételkor létrehozandó virtuális gépre.
1. Mentse a replikált virtuális gép beállításait.

[További információ](https://azure.microsoft.com/pricing/hybrid-benefit/) az Azure Hybrid Benefit szolgáltatásról.

## <a name="next-steps"></a>További lépések

Miután a virtuális gép elérte a védett állapotot, próbálja meg egy [feladatátvétel,](site-recovery-failover.md) hogy ellenőrizze, hogy az alkalmazás jelenik-e meg az Azure-ban.

- [További információ](site-recovery-manage-registration-and-protection.md) arról, hogyan lehet megtisztítani a regisztrációs és védelmi beállításokat a replikáció letiltásához.
- [További információ](vmware-azure-disaster-recovery-powershell.md) arról, hogyan automatizálhatja a virtuális gépek replikációját a PowerShell használatával.
