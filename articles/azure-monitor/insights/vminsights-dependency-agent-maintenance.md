---
title: Az Azure-figyelő virtuálisgépek függőségi ügynökének frissítése
description: Ez a cikk ismerteti, hogyan frissítheti az Azure Monitor virtuális gépek függőségi ügynök parancssori, beállítási varázsló és egyéb módszerek használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617848"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Az Azure-figyelő virtuálisgépek függőségi ügynökének frissítése

Az Azure Monitor virtuálisgépek függőségi ügynök kezdeti üzembe helyezése után olyan frissítések jelennek meg, amelyek hibajavításokat vagy új funkciók vagy funkciók támogatását tartalmazzák.  Ez a cikk segít megérteni a rendelkezésre álló módszereket, és hogyan hajthatja végre a frissítést manuálisan vagy automatizálással.

## <a name="upgrade-options"></a>Frissítési lehetőségek 

A Windows és Linux függőségi ügynök manuálisan vagy automatikusan frissíthető a legújabb kiadásra a ttól függően, hogy a gép a központi telepítési forgatókönyvés a környezet a gép fut.The Dependency agent for Windows and Linux can be upgraded to the latest release manually or automatically depending on the deployment scenario and environment the machine is running in. Az ügynök frissítéséhez a következő módszerek használhatók.

|Környezet |Telepítési módszer |Frissítési módszer |
|------------|--------------------|---------------|
|Azure VM | Függőségi ügynök virtuálisgép-bővítménye [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) és [Linux rendszeren](../../virtual-machines/extensions/agent-dependency-linux.md) | Az ügynök alapértelmezés szerint automatikusan frissül, kivéve, ha az Azure Resource Manager-sablont úgy állította be, hogy letiltsa a tulajdonság *autoUpgradeMinorVersion* tulajdonságát **hamisértékre**állítva. Az olyan alverzió frissítése, amelyen az automatikus frissítés le van tiltva, és a főverziófrissítése ugyanazt a módszert követi – távolítsa el és telepítse újra a bővítményt. |
| Egyéni Azure virtuálisgép-lemezképek | A Windows/Linux függőségi ügynök manuális telepítése | A virtuális gépek frissítése az ügynök legújabb verziójára a Windows telepítőcsomagot vagy a Linux önkicsomagoló és telepíthető rendszerhéj-kötegköteget futtató parancssorból történik.|
| Nem Azure-beli virtuális gépek | A Windows/Linux függőségi ügynök manuális telepítése | A virtuális gépek frissítése az ügynök legújabb verziójára a Windows telepítőcsomagot vagy a Linux önkicsomagoló és telepíthető rendszerhéj-kötegköteget futtató parancssorból történik. |

## <a name="upgrade-windows-agent"></a>Frissítés Windows-ügynök 

Ha a Windows virtuális gép en lévő ügynököt a függőségi ügynök virtuálisgép-bővítményével nem telepített legújabb verzióra szeretné frissíteni, vagy a parancssorból, a parancsfájlból vagy más automatizálási megoldásból, vagy az InstallDependencyAgent-Windows.exe telepítővarázslóval fut.  

A Windows-ügynök legújabb verzióját [innen](https://aka.ms/dependencyagentwindows)töltheti le.

### <a name="using-the-setup-wizard"></a>A Telepítő varázsló használata

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Az **InstallDependencyAgent-Windows.exe** parancs végrehajtása a telepítővarázsló elindításához.
   
3. A **függőségi ügynök** előző verziójának eltávolításához, majd a legújabb verzió telepítéséhez kövesse a Függőségi ügynök telepítővarázslóját.


### <a name="from-the-command-line"></a>A parancssorból

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Futtassa a következő parancsot.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    A `/RebootMode=manual` paraméter megakadályozza, hogy a frissítés automatikusan újraindítsa a számítógépet, ha egyes folyamatok az előző verzióból származó fájlokat használnak, és zárolják őket. 

3. A frissítés sikeresse megállapításához ellenőrizze a `install.log` részletes telepítési információkat. A naplókönyvtár *a következő: %Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Linux-ügynök frissítése 

A függőségi ügynök linuxos verziójának frissítése támogatott, és ugyanazt a parancsot követve hajtja végre, mint egy új telepítést.

A Linux-ügynök legújabb verzióját [innen](https://aka.ms/dependencyagentlinux)töltheti le.

1. Jelentkezzen be a számítógépre rendszergazdai jogosultságokkal rendelkező fiókkal.

2. Futtassa a`sh InstallDependencyAgent-Linux64.bin -s`következő parancsot gyökérként. 

Ha a függőségi ügynök nem indul el, ellenőrizze a naplókat a részletes hibainformációkért. Linux ügynökök, a naplókönyvtár */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>További lépések

Ha egy ideig le szeretné állítani a virtuális gépek figyelését, vagy teljesen el szeretné távolítani a virtuális gépekhez szolgáltatást, olvassa [el a virtuális gépek figyelésének letiltása az Azure-beli virtuális gépekhez című témakört.](vminsights-optout.md)
