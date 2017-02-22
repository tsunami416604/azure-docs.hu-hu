---
title: "Áttelepítés Azure-ra a Site Recoveryvel | Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan telepíthetőek át virtuális gépek és fizikai kiszolgálók az Azure-ra az Azure Site Recoveryvel."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f82634af931a1e9a9646c5631ebd0e5923a0adcc
ms.openlocfilehash: cbb6de4587871c40c9d4e97c9fb2a88eab4945a6


---
# <a name="migrate-to-azure-with-site-recovery"></a>Áttelepítés Azure-ra a Site Recoveryvel

A cikkből megtudhatja, hogyan használható az Azure Site Recovery szolgáltatás virtuális gépek és fizikai kiszolgálók áttelepítésére.

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le.

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információ: [Mi a Site Recovery?](site-recovery-overview.md)

A jelen cikk az [Azure Portalon](https://portal.azure.com) végzett üzembe helyezést ismerteti. A [klasszikus Azure portál](https://manage.windowsazure.com/) használható a meglévő Site Recovery-tárolók fenntartására, de új tárolókat nem hozhat létre.

Felmerülő megjegyzéseit a cikk alján teheti közzé. Műszaki jellegű kérdéseit az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) tegye fel.


## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

Üzembe helyezheti a Site Recovery szolgáltatást helyszíni virtuális gépek és fizikai kiszolgálók Azure-ra vagy egy másodlagos helyre történő teljes replikálásához. Replikálhat gépeket, kimaradások esetén átadhatja a feladataikat az elsődleges helyről, és amikor az elsődleges helyen helyreáll a rendes működés, visszaadhatja a feladatokat. A Site Recoveryt a teljes replikáció mellett virtuális gépek és fizikai kiszolgálók Azure-ra történő áttelepítéséhez is használhatja, hogy a felhasználók hozzáférhessenek a gépeken futó számítási feladatokhoz az Azure virtuális gépekről. Az áttelepítés magában foglalja a replikálást és az elsődleges helyről az Azure-ra irányuló feladatátvételt. A teljes replikációval ellentétben feladat-visszavételi mechanizmust nem tartalmaz.

## <a name="what-can-site-recovery-migrate"></a>Melyen típusú áttelepítéseket lehet elvégezni a Site Recoveryvel?

A következőket teheti:

- Áttelepíthet helyszíni Hyper-V virtuális gépeken, VMWare virtuális gépeken és fizikai kiszolgálókon futó számítási feladatokat Azure virtuális gépekre. Ebben a forgatókönyvben teljes replikációt és feladat-visszavételt is végrehajthat.
- Áttelepíthet [Azure IaaS virtuális gépeket](site-recovery-migrate-azure-to-azure.md) Azure-régiók között. Ebben az esetben jelenleg csak az áttelepítés támogatott, a feladat-visszavétel nem.
- [AWS Windows-példányokat](site-recovery-migrate-aws-to-azure.md) Azure IaaS virtuális gépekre. Ebben az esetben jelenleg csak az áttelepítés támogatott, a feladat-visszavétel nem.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Helyszíni virtuális gépek és fizikai kiszolgálók áttelepítése

Helyszíni Hyper-V- és VMWare-alapú virtuális gépek, illetve fizikai kiszolgálók áttelepítéséhez szinte ugyanazokat a lépéseket kell követnie, amelyeket egy szokványos replikációnál használ. Állítsa be a Recovery Services-tárolót, konfigurálja a szükséges felügyeleti kiszolgálót (attól függően, hogy mit szeretne áttelepíteni), adja hozzá a tárolóhoz, majd adja meg a replikációs beállításokat. Engedélyezze a replikációt azoknál a gépeknél, amelyeket szeretné áttelepíteni, majd futtasson le egy gyors feladatátvételi tesztet annak ellenőrzéséhez, hogy minden megfelelően működik-e.

A replikációs környezet működésének ellenőrzése után, attól függően, hogy a forgatókönyv [mit támogat](site-recovery-failover.md#failover-and-failback), indítson el egy tervezett vagy nem tervezett feladatátvételt. Az áttelepítéséhez nem véglegesíteni a feladatátvételt, és nem kell törölnie semmit. Ehelyett válassza az **Áttelepítés befejezése** lehetőséget az összes áttelepíteni kívánt gépnél. Az **Áttelepítés befejezése** művelet befejezi az áttelepítési folyamatot, eltávolítja a gép replikációját, és leállítja a gép Site Recovery-számlázását.

## <a name="migrate-between-azure-regions"></a>Áttelepítés Azure-régiók között

A Site Recoveryvel Azure-régiók között is áttelepíthet Azure virtuális gépeket. Ebben a forgatókönyvben jelenleg csak az áttelepítés támogatott. Ez azt jelenti, hogy replikálhat Azure virtuális gépeket és átirányíthatja azokat egy másik régióba, de feladat-visszavételt nem hajthat végre. Ebben a forgatókönyvben állítsa be a Recovery Services-tárolót, a replikáció felügyeletéhez helyezzen üzembe egy helyszíni konfigurációs kiszolgálót, adja hozzá a tárolóhoz, majd adja meg a replikációs beállításokat. Engedélyezze a replikációt azoknál a gépeknél, amelyeket szeretné áttelepíteni, majd futtasson le egy gyors feladatátvételi tesztet. Ezután az **Áttelepítés befejezése** lehetőséget használva futtasson egy nem tervezett feladatátvételt.

## <a name="migrate-aws-to-azure"></a>AWS áttelepítése Azure-ra

AWS-példányokat is áttelepíthet Azure virtuális gépekre. Ebben a forgatókönyvben jelenleg csak az áttelepítés támogatott. Ez azt jelenti, hogy replikálhat AWS-példányokat és átirányíthatja azokat az Azure-ba, de feladat-visszavételt nem hajthat végre. Az áttelepítés során az AWS-példányokat a szolgáltatás ugyanúgy kezeli, mint a fizikai kiszolgálókat. Állítsa be a Recovery Services-tárolót, a replikáció felügyeletéhez helyezzen üzembe egy helyszíni konfigurációs kiszolgálót, adja hozzá a tárolóhoz, majd adja meg a replikációs beállításokat. Engedélyezze a replikációt azoknál a gépeknél, amelyeket szeretné áttelepíteni, majd futtasson le egy gyors feladatátvételi tesztet. Ezután az **Áttelepítés befejezése** lehetőséget használva futtasson egy nem tervezett feladatátvételt.




## <a name="next-steps"></a>Következő lépések

- [VMware virtuális gépek áttelepítése az Azure-ba](site-recovery-vmware-to-azure.md)
- [Fizikai kiszolgálók áttelepítése az Azure-ba](site-recovery-vmware-to-azure.md)
- [VMM-felhőben lévő Hyper-V virtuális gépek áttelepítése az Azure-ba](site-recovery-vmm-to-azure.md)
- [VMM nélküli Hyper-V virtuális gépek áttelepítése az Azure-ba](site-recovery-hyper-v-site-to-azure.md)
- [Azure virtuális gépek áttelepítése Azure-régiók között](site-recovery-migrate-azure-to-azure.md)
- [AWS-példányok áttelepítése az Azure-ba](site-recovery-migrate-aws-to-azure.md)



<!--HONumber=Jan17_HO4-->


