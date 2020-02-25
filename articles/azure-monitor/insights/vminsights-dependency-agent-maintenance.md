---
title: A Azure Monitor for VMs függőségi ügynök frissítése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet frissíteni a Azure Monitor for VMs függőségi ügynököt a parancssori, a telepítővarázsló és más módszerek használatával.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 28b7a2b95e8ad23226f92f2b6fba085cc0fa1bfd
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565553"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>A Azure Monitor for VMs függőségi ügynök frissítése

A Azure Monitor for VMs függőségi ügynök kezdeti üzembe helyezése után a rendszer olyan frissítéseket szabadít fel, amelyek hibajavításokat vagy új funkciók vagy funkciók támogatását tartalmazzák.  Ez a cikk segítséget nyújt az elérhető módszerek megismerésében, valamint a frissítés manuális vagy automatizáláson keresztüli végrehajtásában.

## <a name="upgrade-options"></a>Frissítési beállítások 

A Windows és a Linux rendszerhez készült függőségi ügynök a központi telepítési forgatókönyvtől és a gépen futó környezettől függően manuálisan vagy automatikusan is frissíthető a legújabb kiadásra. Az ügynök frissítésére a következő módszerek használhatók.

|Környezet |Telepítési módszer |Frissítési módszer |
|------------|--------------------|---------------|
|Azure VM | Függőségi ügynök virtuálisgép-bővítménye Windows és [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerekhez](../../virtual-machines/extensions/agent-dependency-windows.md) | A rendszer alapértelmezés szerint automatikusan frissíti az ügynököt, kivéve, ha úgy állította be a Azure Resource Manager sablont, hogy a *autoUpgradeMinorVersion* tulajdonság **hamis**értékre állításával letiltható. Az alverzió verziófrissítése, amelynél az automatikus frissítés le van tiltva, és a főverzió frissítése ugyanezt a módszert alkalmazza – távolítsa el, majd telepítse újra a bővítményt. |
| Egyéni Azure VM-rendszerképek | Függőségi ügynök manuális telepítése Windows/Linux rendszerhez | A virtuális gépeknek az ügynök legújabb verziójára való frissítését a Windows Installer-csomagot vagy a Linux rendszerű önkicsomagoló és telepíthető rendszerhéj-parancsfájlt futtató parancssorból kell elvégezni.|
| Nem Azure-beli virtuális gépek | Függőségi ügynök manuális telepítése Windows/Linux rendszerhez | A virtuális gépeknek az ügynök legújabb verziójára való frissítését a Windows Installer-csomagot vagy a Linux rendszerű önkicsomagoló és telepíthető rendszerhéj-parancsfájlt futtató parancssorból kell elvégezni. |

## <a name="upgrade-windows-agent"></a>Windows-ügynök frissítése 

Ha a Windows rendszerű virtuális gépen lévő ügynököt a függőségi ügynök virtuálisgép-bővítményével nem telepített legújabb verzióra szeretné frissíteni, akkor a parancssorból, parancsfájlból vagy más automatizálási megoldásból vagy a InstallDependencyAgent-Windows. exe telepítővarázslójának használatával kell futtatnia.  

A Windows-ügynök legújabb verzióját [innen](https://aka.ms/dependencyagentwindows)töltheti le.

### <a name="using-the-setup-wizard"></a>A telepítővarázsló használata

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. A telepítővarázsló elindításához futtassa a **InstallDependencyAgent-Windows. exe fájlt** .
   
3. A **Dependency Agent telepítővarázsló** segítségével távolítsa el a függőségi ügynök előző verzióját, majd telepítse a legújabb verziót.


### <a name="from-the-command-line"></a>A parancssorból

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Futtassa a következő parancsot.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    A `/RebootMode=manual` paraméter megakadályozza, hogy a frissítés automatikusan újraindítsa a gépet, ha egyes folyamatok az előző verzióból származó fájlokat használnak, és zárolják őket. 

3. A frissítés sikerességének ellenőrzéséhez ellenőrizze a `install.log` részletes beállítási információkat. A naplózási könyvtár a *%ProgramFiles%\Microsoft függőségi Agent\logs*.

## <a name="upgrade-linux-agent"></a>Linux-ügynök frissítése 

A Linux rendszeren futó függőségi ügynök korábbi verzióiról való frissítés támogatott, és az új telepítéssel megegyező paranccsal végezhető el.

A Windows-ügynök legújabb verzióját [innen](https://aka.ms/dependencyagentlinux)töltheti le.

1. Jelentkezzen be a számítógépre egy rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Futtassa a következő parancsot root`sh InstallDependencyAgent-Linux64.bin -s`ként. 

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. Linux-ügynökök esetén a */var/opt/Microsoft/Dependency-Agent/log*a naplózási könyvtár. 

## <a name="next-steps"></a>További lépések

Ha le szeretné állítani a virtuális gépek figyelését egy adott időtartamra, vagy Azure Monitor for VMs teljes egészében, tekintse [meg a virtuális gépek figyelésének letiltása Azure monitor for VMS-ben](vminsights-optout.md)című témakört.
