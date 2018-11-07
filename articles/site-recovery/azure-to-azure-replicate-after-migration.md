---
title: Vészhelyreállítás beállítása az Azure virtuális gépek az Azure-bA az Azure Site Recovery szolgáltatással az áttelepítés után |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan készíti elő a gépek állíthat be vészhelyreállítást az Azure Site Recovery használatával való migrálás után Azure-régiók között.
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: ae212bebf5a34e9a80f49a750735137d7a8814be
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232664"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Az Azure-beli virtuális gépek vészhelyreállításának beállítása Azure-ba történő migrálás után 


Ez a cikk használja, miután [a helyszíni gépek áttelepítése az Azure virtuális gépeire](tutorial-migrate-on-premises-to-azure.md) használatával a [Site Recovery](site-recovery-overview.md) szolgáltatás. Ez a cikk segít az Azure virtuális gépek előkészítése állít be vészhelyreállítást egy másodlagos Azure régióra, Site Recovery használatával.



## <a name="before-you-start"></a>Előkészületek

Vészhelyreállítás beállítása előtt győződjön meg arról, hogy a várt módon áttelepítés befejeződött. Az áttelepítés sikeres, a feladatátvétel után kell választania a **az áttelepítés befejezése** beállítást, az egyes gépek, amelyeket szeretné áttelepíteni. 



## <a name="install-the-azure-vm-agent"></a>Az Azure-beli Virtuálisgép-ügynök telepítése

Az Azure [Virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) telepítve kell lennie a virtuális Gépet úgy, hogy a Site Recovery segítségével replikálhatja azt.


1. Virtuálisgép-ügynök telepítése Windows rendszerű virtuális gépeken, töltse le és futtassa a [ügynököt telepítő](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A virtuális gépen a telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
2. A Virtuálisgép-ügynök Linux rendszerű virtuális gépeken, telepítse a legújabb [Linux-ügynök](../virtual-machines/extensions/agent-linux.md). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. Javasoljuk, hogy telepítse a terjesztésipont-adattárból. Nem ajánlott közvetlenül a githubból a Linux rendszerű Virtuálisgép-ügynök telepítése. 


## <a name="validate-the-installation-on-windows-vms"></a>A Windows virtuális gépeken a telepítés ellenőrzése

1. Az Azure virtuális gépen, a C:\WindowsAzure\Packages mappába megtekintheti a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, majd a **tulajdonságok**, jelölje be a **részletei** fülre.
3. Ellenőrizze, hogy a **termékverzió** mezőben látható 2.6.1198.718 vagy újabb.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>A VMware virtuális gépek vagy fizikai kiszolgálók áttelepítése

Ha a helyszíni VMware virtuális gépeket (vagy fizikai kiszolgálók) telepít át az Azure-ba, vegye figyelembe, hogy:

- Csak telepítenie kell az Azure-beli Virtuálisgép-ügynök v9.6 a mobilitási szolgáltatást a migrált gépen telepítve van-e vagy annál régebbi.
- Windows virtuális gépek és újabb verziók esetében fut a mobilitási szolgáltatás 9.7.0.0 verziója a szolgáltatás-telepítő telepíti a legújabb elérhető Azure Virtuálisgép-ügynök. Ha telepít át, ezek a virtuális gépek megfelel az ügynök telepítése minden olyan Virtuálisgép-bővítmény, beleértve a Site Recovery-bővítmény előfeltételeinek.
- Manuálisan távolítsa el a mobilitási szolgáltatás a virtuális gépről az Azure, az alábbi módszerek egyikével kell. Indítsa újra a virtuális Gépet, mielőtt a replikáció konfigurálása.
    - A Windows, a Vezérlőpult > **programok**, távolítsa el **a Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló**. Egy rendszergazda jogú parancssorból futtassa a következő:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux esetén jelentkezzen be gyökérszintű felhasználóként. A parancsot egy terminálban váltson **/user/local/ASR**, és futtassa a következő parancsot:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>További lépések

[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális Gépen egy másodlagos régióba.
