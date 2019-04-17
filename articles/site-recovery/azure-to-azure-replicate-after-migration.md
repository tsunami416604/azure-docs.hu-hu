---
title: Vészhelyreállítás beállítása az Azure virtuális gépek az Azure-bA az Azure Site Recovery szolgáltatással az áttelepítés után |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan készíti elő a gépek állíthat be vészhelyreállítást az Azure Site Recovery használatával való migrálás után Azure-régiók között.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 019c6ec776277a9102cb95cd685bbae0fc660d66
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615914"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Az Azure-beli virtuális gépek vészhelyreállításának beállítása Azure-ba történő migrálás után 


Ez a cikk kövesse, ha [a helyszíni gépek áttelepítése az Azure virtuális gépeire](tutorial-migrate-on-premises-to-azure.md) használatával a [Site Recovery](site-recovery-overview.md) szolgáltatás, és most szeretné, hogy a virtuális gépek egy másodlagos Azure régióra vész-helyreállítási beállítása. A cikk ismerteti, hogyan ellenőrizhető, hogy az Azure-beli Virtuálisgép-ügynök telepítve van-e az áttelepített virtuális gépeken, és hogyan távolítsa el a Site Recovery mobilitási szolgáltatást, amely az áttelepítés után már nem szükséges.



## <a name="verify-migration"></a>Áttelepítés ellenőrzése

Vészhelyreállítás beállítása előtt győződjön meg arról, hogy a várt módon áttelepítés befejeződött. Az áttelepítés sikeres, a feladatátvétel után kell választania a **az áttelepítés befejezése** beállítást, az egyes gépek, amelyeket szeretné áttelepíteni. 

## <a name="verify-the-azure-vm-agent"></a>Az Azure-beli Virtuálisgép-ügynök ellenőrzése

Minden egyes Azure virtuális Gépen kell rendelkeznie a [Azure Virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) telepítve. Az Azure-beli virtuális gépek replikálása, a Site Recovery egy kiterjesztést telepít az ügynökön.

- Ha a gép 9.7.0.0 verziója fut, vagy később a Site Recovery mobilitási szolgáltatást, az Azure-beli Virtuálisgép-ügynök automatikusan telepíti a mobilitási szolgáltatást a Windows virtuális gépek. A mobilitási szolgáltatás régebbi verzióin kell automatikusan telepítse az ügynököt.
- Linux rendszerű virtuális gépekhez manuálisan kell telepíteni az Azure-beli Virtuálisgép-ügynök. Csak telepítenie kell az Azure-beli Virtuálisgép-ügynök v9.6 a mobilitási szolgáltatást a migrált gépen telepítve van-e vagy annál régebbi.


### <a name="install-the-agent-on-windows-vms"></a>Telepítse az ügynököt a Windows virtuális gépek

Ha egy korábban 9.7.0.0 a Site Recovery mobilitási szolgáltatás verzióját futtatja, vagy valamilyen más szükséges telepítse kézzel az ügynököt, tegye a következőket:  

1. Győződjön meg arról, hogy a rendszergazdai engedélyekkel rendelkezik a virtuális gépen.
2. Töltse le a [Virtuálisgép-ügynök telepítőjének](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Futtassa a telepítő fájlt.

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése
Ellenőrizze, hogy az ügynök telepítve van:

1. Az Azure virtuális gépen, a C:\WindowsAzure\Packages mappába megtekintheti a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, majd a **tulajdonságok**, jelölje be a **részletei** fülre.
3. Ellenőrizze, hogy a **termékverzió** mezőben látható 2.6.1198.718 vagy újabb.

[További](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) kapcsolatban a Windows-ügynök telepítését.

### <a name="install-the-agent-on-linux-vms"></a>Telepítse az ügynököt a Linux rendszerű virtuális gépek

Telepítse a [Azure Linux rendszerű virtuális gép](../virtual-machines/extensions/agent-linux.md) ügynök manuálisan az alábbiak szerint:

1. Ellenőrizze, hogy rendszergazdai engedélye van a gépen.
2. Javasoljuk, hogy az a terjesztési csomag adattárból az RPM- vagy DEB-csomag használatával Linux rendszerű virtuális gép-ügynök telepítése. Az összes a [terjesztési szolgáltatók által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálhatja az Azure-beli Linuxos ügynök csomag adattárak, valamint a lemezképek.
    - Javasoljuk, hogy frissítse az ügynök csak egy terjesztési tárház keresztül.
    - A Linux rendszerű Virtuálisgép-ügynök telepítésével közvetlenül a githubból, és frissítéskor nem ajánlott.
    -  Ha a legújabb ügynököt a disztribúció nem érhető el, forduljon terjesztési támogatása útmutatást a telepítéshez. 

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése 

1. Futtassa a következő parancsot: **ps -e** , győződjön meg arról, hogy az Azure-ügynököt a Linux rendszerű virtuális gépen fut-e.
2. Ha a folyamat nem fut, indítsa újra a következő parancsokat:
    - Az ubuntu rendszeren: **walinuxagent kezdő szolgáltatás**
    - Egyéb-disztribúciókra vonatkozó: **waagent kezdő szolgáltatás**


## <a name="uninstall-the-mobility-service"></a>A mobilitási szolgáltatás eltávolítása

1. A virtuális gépről az Azure, az alábbi módszerek valamelyikével távolítsa el manuálisan a mobilitási szolgáltatást. 
    - A Windows, a Vezérlőpult > **programok**, távolítsa el **a Microsoft Azure Site Recovery mobilitási szolgáltatás vagy fő célkiszolgálóként kiszolgáló**. Egy rendszergazda jogú parancssorból futtassa a következő:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux esetén jelentkezzen be gyökérszintű felhasználóként. A parancsot egy terminálban váltson **/user/local/ASR**, és futtassa a következő parancsot:
        ```
        ./uninstall.sh -Y
        ```
2. Indítsa újra a virtuális Gépet, mielőtt a replikáció konfigurálása.

## <a name="next-steps"></a>További lépések

[Tekintse át a hibaelhárítási](site-recovery-extension-troubleshoot.md) a Site Recovery-bővítmény, az Azure-beli Virtuálisgép-ügynök.
[Gyorsan replikálja](azure-to-azure-quickstart.md) egy Azure virtuális Gépen egy másodlagos régióba.
