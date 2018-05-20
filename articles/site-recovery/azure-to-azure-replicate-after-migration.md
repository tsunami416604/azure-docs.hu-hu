---
title: Azure virtuális gépek az Azure-bA az Azure Site Recovery áttelepítés után állítsa be a vész-helyreállítási |} Microsoft Docs
description: A cikkből megtudhatja, hogyan készíti elő a vész-helyreállítási Azure-bA az Azure Site Recovery segítségével való áttelepítése után Azure-régiók közötti gépek.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: ponatara
ms.openlocfilehash: f64d61134f61476bcdd803f92019830b7cecf867
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Állítsa be vészhelyreállítás Azure virtuális gépek Azure való áttelepítése után 

>[!NOTE]
> Vészhelyreállítás Azure virtuális gépek az Azure Site Recovery segítségével jelenleg előzetes verzió.

Ez a cikk használja beállította [az Azure virtuális gépen a helyszíni gépeket telepített át](tutorial-migrate-on-premises-to-azure.md) használatával a [Site Recovery](site-recovery-overview.md) szolgáltatás. Ez a cikk segítséget nyújt az Azure virtuális gépek előkészítése katasztrófa utáni helyreállítás egy másodlagos Azure-régió, a Site Recovery segítségével történő beállításával.



## <a name="before-you-start"></a>Előkészületek

Vész-helyreállítási beállítása előtt győződjön meg arról, hogy áttelepítési várt módon befejeződött. Áttelepítés végrehajtása sikeres legyen, a feladatátvétel után ki kell jelölni a **az áttelepítés végrehajtásához** beállítás, az egyes áttelepíteni kívánt gépek. 



## <a name="install-the-azure-vm-agent"></a>Az Azure Virtuálisgép-ügynök telepítése

Az Azure [Virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) telepíteni kell a virtuális Gépet, hogy a Site Recovery segítségével replikálhatja azt.


1. A Virtuálisgép-ügynök telepítése Windows rendszerű virtuális gépeken, töltse le és futtassa a [ügynököt telepítő](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A virtuális Gépen a telepítés befejezéséhez rendszergazdai jogosultságokat igényel.
2. A Linux rendszerű virtuális gépeken a Virtuálisgép-ügynök telepítéséhez telepítse a legújabb [Linux-ügynök](../virtual-machines/extensions/agent-linux.md). A telepítés befejezéséhez rendszergazdai jogosultságokat igényel. Javasoljuk, hogy a terjesztési tárházból telepítse. Nem ajánlott közvetlenül a Githubból a Linux Virtuálisgép-ügynök telepítése. 


## <a name="validate-the-installation-on-windows-vms"></a>A Windows virtuális gépeken a telepítés ellenőrzése

1. Az Azure virtuális gépen, a C:\WindowsAzure\Packages mappában található meg kell jelennie a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, majd a **tulajdonságok**, jelölje be a **részletek** fülre.
3. Ellenőrizze, hogy a **termékverzió** mezőben látható 2.6.1198.718 vagy újabb verzióját.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>A VMware virtuális gépek vagy fizikai kiszolgálók áttelepítése

Ha a helyszíni VMware virtuális gépek (vagy fizikai kiszolgálók) telepít át az Azure-ba, vegye figyelembe, hogy:

- Csak az Azure virtuális gép ügynököt telepíteni, ha a mobilitási szolgáltatás a áttelepített számítógépen telepített v9.6 kell vagy annál régebbi.
- Windows virtuális gépek és újabb verziók esetében fut a mobilitási szolgáltatás 9.7.0.0 verziója a szolgáltatás-telepítő telepíti a legújabb elérhető Azure Virtuálisgép-ügynök. Amikor telepít át, virtuális gépeken már éri el az előfeltételként szükséges összes Virtuálisgép-bővítmény, a Site Recovery kiterjesztéssel együtt ügynöktelepítés.
- Manuálisan távolítsa el a mobilitási szolgáltatást az Azure virtuális gépről, az alábbi módszerek egyikével kell. Indítsa újra a virtuális gép, replikációs konfigurálása előtt.
    - A Windows, a Vezérlőpult > **programok telepítése/törlése**, távolítsa el **Microsoft Azure Site helyreállítási mobilitási szolgáltatás/fő célkiszolgáló**. Futtassa egy rendszergazda jogú parancssorba:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux, jelentkezzen be egy rendszergazda felhasználó. A terminálban keresse meg **/user/local/ASR**, és futtassa a következő parancsot:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális gép távoli másodlagos régióba.
