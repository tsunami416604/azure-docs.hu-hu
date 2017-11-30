---
title: "Készítse elő a gépek áttelepítése az Azure Site Recovery segítségével után Azure-régiók közötti vész helyreállítási |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan készíti elő a vész-helyreállítási Azure Site Recovery segítségével Azure való áttelepítése után Azure-régiók közötti gépek."
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
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 7658bedc0bd5c4a289f3271504a006ba54c783b6
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Azure virtuális gépek replikálása egy másik régióban Azure való áttelepítése után Azure Site Recovery segítségével

>[!NOTE]
> Az Azure virtuális gépek (VM) az Azure Site Recovery replikációs jelenleg előzetes verzió.

## <a name="overview"></a>Áttekintés

Ez a cikk segít az Azure virtuális gépek előkészítése után ezek a gépek áttelepítését egy a helyszíni környezetből Azure Azure Site Recovery segítségével két Azure régiók közötti replikáció.

## <a name="disaster-recovery-and-compliance"></a>Vész-helyreállítási és megfelelőség
Napjainkban egyre több vállalatok áthelyezi a munkaterhelések az Azure-bA. A vállalatok áthelyezése kritikus fontosságú helyszíni termelési számítási feladatokhoz, az Azure-ba, az ilyen terhelések vész-helyreállítási beállításának megadása kötelező, a megfelelőség és az Azure-régióban esetleges megszakadását elleni védelmében.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Felkészülés az áttelepített gépek replikációjának lépései
Előkészítése a replikációt, hogy egy másik Azure-régiót beállításának gépeket telepített át:

1. Az áttelepítés végrehajtásához.
2. Szükség esetén telepítse az Azure-ügynököt.
3. Távolítsa el a mobilitási szolgáltatást.  
4. Indítsa újra a virtuális Gépet.

A következő szakaszokban részletesebben ezeket a lépéseket ismerteti.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>1. lépés:, Telepítse át a Hyper-V virtuális gépek, a VMware virtuális gépek és a fizikai kiszolgálókon futtatásához Azure virtuális gépeken futó számítási feladatok

A replikáció beállítása és áttelepítése a helyszíni Hyper-V, VMware és fizikai munkaterhelések az Azure-ba, kövesse a [át Azure IaaS virtuális gépeket az Azure Site Recovery Azure-régiók közötti](site-recovery-migrate-azure-to-azure.md) cikk. 

Az áttelepítés után nem kell véglegesítése, vagy törölje a feladatátvételt. Ehelyett válassza ki a **az áttelepítés végrehajtásához** minden egyes áttelepíteni kívánt géphez tartozó lehetőséget:
1. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Kattintson a **OK** a lépés befejezéséhez. Folyamatban van a virtuális gép tulajdonságaiban nyomon követéséhez a befejeződött áttelepítési feladatot a figyelés **Site Recovery-feladatok**.
2. A **az áttelepítés végrehajtásához** művelet az áttelepítési folyamat befejeződik, eltávolítja a gép replikálását, és leállítja a gépet a Site Recovery számlázási.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>2. lépés: Az Azure Virtuálisgép-ügynök telepítése a virtuális gépen
Az Azure [Virtuálisgép-ügynök](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) telepíteni kell a virtuális gép működéséhez, illetve a virtuális gép védelmét a Site Recovery-bővítmény.

>[!IMPORTANT]
>A Windows virtuális gépek 9.7.0.0, verziójától a mobilitási szolgáltatások telepítőjének is telepíti a legújabb elérhető Azure Virtuálisgép-ügynök. Az áttelepítési a virtuális gép megfelel-e a bármely Virtuálisgép-bővítmény, beleértve a Site Recovery bővítmény használatára vonatkozó előfeltételek telepítése. Az Azure virtuális gép ügynöknek szüksége van, manuálisan kell telepíteni, csak ha a mobilitási szolgáltatást a áttelepített számítógépen telepített 9.6 vagy korábbi verzió.

A következő táblázat további információt a Virtuálisgép-ügynök telepítése, és ellenőrzi, hogy telepítette:

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A Virtuálisgép-ügynök telepítése |Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokat igényel. |Telepítse a legújabb [Linux-ügynök](../../virtual-machines/linux/agent-user-guide.md). A telepítés befejezéséhez rendszergazdai jogosultságokat igényel. Azt javasoljuk, hogy az ügynök telepítése a telepítési tárházból. A Microsoft *nem javasoljuk* közvetlenül a Githubból a Linux Virtuálisgép-ügynök telepítése.  |
| A Virtuálisgép-ügynök telepítésének ellenőrzése |1. Keresse meg az Azure virtuális Gépen a C:\WindowsAzure\Packages mappát. Meg kell jelennie a WaAppAgent.exe fájlt. <br>2. Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A **termékverzió** mező lehet 2.6.1198.718 vagy újabb verzióját. |N/A |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>3. lépés: Távolítsa el a mobilitási szolgáltatást az áttelepített virtuális gép

Ha áttelepítette a helyszíni VMware gépek vagy windowsos/Linuxos fizikai kiszolgálók, meg kell manuálisan távolítsa el, illetve az Eltávolítás az áttelepített virtuális gépről a mobilitási szolgáltatást.

>[!IMPORTANT]
>Ez a lépés nincs szükség a Hyper-V virtuális gépek áttelepítése az Azure-bA.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Távolítsa el a mobilitási szolgáltatást a Windows Server virtuális gép
Az alábbi módszerek valamelyikével távolítsa el a mobilitási szolgáltatást a Windows Server rendszerben.

##### <a name="uninstall-by-using-the-windows-ui"></a>Távolítsa el a Windows felhasználói felület használatával
1. Válassza a Vezérlőpult **programok**.
2. Válassza ki **Microsoft Azure Site helyreállítási mobilitási szolgáltatás/fő célkiszolgáló**, majd válassza ki **Eltávolítás**.

##### <a name="uninstall-at-a-command-prompt"></a>Távolítsa el a parancssorba
1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Távolítsa el a mobilitási szolgáltatást a Linux rendszerű számítógépen
1. A Linux-kiszolgálón jelentkezzen be egy **legfelső szintű** felhasználó.
2. A terminálban keresse /user/local/ASR.
3. Távolítsa el a mobilitási szolgáltatást, futtassa a következő parancsot:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>4. lépés: Indítsa újra a virtuális gép

A mobilitási szolgáltatás eltávolítása után indítsa újra a virtuális gép egy másik Azure-régió, hogy a replikáció beállítása előtt.


## <a name="next-steps"></a>Következő lépések
- A munkaterhelések számára a védelmének megkezdéséhez [Azure virtuális gépek replikálásához](azure-to-azure-quickstart.md).
- További információ [útmutató az Azure virtuális gépek replikálása a hálózat](site-recovery-azure-to-azure-networking-guidance.md).
