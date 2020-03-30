---
title: Vészhelyreállítás beállítása az Azure-ba való migrálás után az Azure Site Recovery segítségével
description: Ez a cikk ismerteti, hogyan készülhet fel a gépeket az Azure-régiók közötti vészhelyreállítás beállítására az Azure-ba való áttelepítés után az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159112"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Az Azure-beli virtuális gépek vészhelyreállításának beállítása Azure-ba történő migrálás után 


Kövesse ezt a cikket, ha [áttelepítette a helyszíni gépeket az Azure-beli virtuális gépekre](tutorial-migrate-on-premises-to-azure.md) a [Site Recovery](site-recovery-overview.md) szolgáltatás használatával, és most szeretné, hogy a vész-helyreállítási egy másodlagos Azure-régióba beállított virtuális gépeket. A cikk ismerteti, hogyan biztosíthatja, hogy az Azure virtuális gép ügynök telepítve van az áttelepített virtuális gépeken, és hogyan távolíthatja el a site recovery mobilitási szolgáltatás, amely már nincs szükség az áttelepítés után.



## <a name="verify-migration"></a>Áttelepítés ellenőrzése

A vészhelyreállítás beállítása előtt győződjön meg arról, hogy az áttelepítés a várt módon befejeződött. Az áttelepítés sikeres befejezéséhez a feladatátvétel után válassza ki a **Teljes áttelepítés** beállítást minden áttelepíteni kívánt géphez. 

## <a name="verify-the-azure-vm-agent"></a>Az Azure virtuálisgép-ügynök ellenőrzése

Minden Azure virtuális gép nek telepítve kell lennie az [Azure virtuálisgép-ügynöknek.](../virtual-machines/extensions/agent-windows.md) Az Azure virtuális gépek replikálásához a Site Recovery egy bővítményt telepít az ügynökre.

- Ha a számítógép 9.7.0.0-s vagy újabb verzióját futtatja a Site Recovery Mobility szolgáltatás, az Azure virtuálisgép-ügynök automatikusan telepíti a Mobilitás szolgáltatás windowsos virtuális gépeken. A Mobilitás szolgáltatás korábbi verzióiban manuálisan telepíti az ügynököt.
- Linuxos virtuális gépek esetén manuálisan kell telepítenie az Azure virtuálisgép-ügynököt. Csak akkor kell telepítenie az Azure virtuálisgép-ügynököt, ha az áttelepített gépen telepített mobilitási szolgáltatás 9.6-os vagy korábbi.


### <a name="install-the-agent-on-windows-vms"></a>Az ügynök telepítése Windows virtuális gépekre

Ha a Site Recovery mobilitási szolgáltatás 9.7.0.0-nál korábbi verzióját futtatja, vagy más módon manuálisan kell telepítenie az ügynököt, tegye a következőket:  

1. Győződjön meg arról, hogy rendszergazdai engedélyekkel rendelkezik a virtuális gép.
2. Töltse le a [Virtuálisgép-ügynök telepítőjének letöltését.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)
3. Futtassa a telepítőfájlt.

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése
Annak ellenőrzése, hogy az ügynök telepítve van-e:

1. Az Azure virtuális gép, a C:\WindowsAzure\Packages mappában, meg kell jelennie a WaAppAgent.exe fájlt.
2. Kattintson a jobb gombbal a fájlra, és a **Tulajdonságok párbeszédpanelen**válassza a **Részletek** lapot.
3. Ellenőrizze, hogy a **Termékverzió** mezőben látható-e 2.6.1198.718 vagy újabb.

[További információ](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) a Windows ügynöktelepítéséről.

### <a name="install-the-agent-on-linux-vms"></a>Az ügynök telepítése Linux os virtuális gépekre

Telepítse az [Azure Linux virtuálisgép-ügynököt](../virtual-machines/extensions/agent-linux.md) manuálisan az alábbiak szerint:

1. Győződjön meg arról, hogy rendszergazdai engedélyekkel rendelkezik a számítógépen.
2. Javasoljuk, hogy telepítse a Linux virtuálisgép-ügynököt egy RPM vagy egy DEB-csomag használatával a disztribúció csomagtárából. Az összes [jóváhagyott terjesztési szolgáltatók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálja az Azure Linux-ügynök csomagot a rendszerképek és adattárak.
    - Javasoljuk, hogy az ügynök frissítése csak egy terjesztési tárház.
    - Nem javasoljuk a Linux virtuálisgép-ügynök közvetlenül a GitHubról történő telepítését és frissítését.
    -  Ha a legújabb ügynök a terjesztés nem érhető el, forduljon a terjesztési támogatási szolgálathoz a telepítésével kapcsolatos utasításokért. 

#### <a name="validate-the-installation"></a>A telepítés ellenőrzése 

1. Futtassa ezt a parancsot: **ps -e** annak érdekében, hogy az Azure-ügynök fut a Linux virtuális gép.
2. Ha a folyamat nem fut, indítsa újra a következő parancsokkal:
    - Ubuntu esetén: **szolgáltatás walinuxagent start**
    - Egyéb disztribúciók esetén: **a service waagent start**


## <a name="uninstall-the-mobility-service"></a>A Mobilitás szolgáltatás eltávolítása

1. Manuálisan távolítsa el a Mobilitási szolgáltatást az Azure virtuális gépről az alábbi módszerek egyikének használatával. 
    - Windows rendszerben a Vezérlőpult > **programok telepítése/törlése**segédprogramjában távolítsa el a **Microsoft Azure Site Recovery Mobility Service/Master Target kiszolgálót.** A rendszergazda jogú parancssorban futtassa a következőket:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux esetén jelentkezzen be root felhasználóként. Terminálon nyissa meg a **/user/local/ASR**kapcsolót, és futtassa a következő parancsot:
        ```
        ./uninstall.sh -Y
        ```
2. A replikáció konfigurálása előtt indítsa újra a virtuális gép.

## <a name="next-steps"></a>További lépések

Tekintse át a site recovery bővítmény [hibaelhárítási](site-recovery-extension-troubleshoot.md) az Azure virtuálisgép-ügynök.
[Gyorsan replikálja](azure-to-azure-quickstart.md) az Azure virtuális gép egy másodlagos régióban.
