---
title: "Visual Studio használatával Azure virtuális géphez |} Microsoft Docs"
description: "Visual Studio használatával Azure virtuális géphez."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 813022f1778e2c7f3174e11192b845c2c33ad219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a id="top"></a> Visual Studio képek az Azure-on
Egy előre konfigurált Azure virtuális gépen (VM) futó Visual Studio használatával módja a legegyszerűbben és leggyorsabban eljusson semmi sem egy összesítő és futó fejlesztői környezetben.  A Visual Studio különböző konfigurációival rendszerkép érhetők el a [Azure piactér](https://portal.azure.com/). Ebben az esetben indítsa el a virtuális gép, és ki, hogy nyissa meg.

Most ismerkedik az Azure-ral? [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Milyen konfigurációk és verziói érhetők el?
Az Azure piactéren képek keresése a legutóbbi Főverziók: a Visual Studio 2017 és a Visual Studio 2015-öt.  Az egyes főverzió, megjelenik az eredetileg kiadott (más néven RTW) verzióját, és a "legújabb" frissített verzióitól.  Minden egyes ezen különböző verziói esetében a Visual Studio Enterprise és a Visual Studio Community kiadások található.

|               Verzió              |          Kiadás            |    Termékverzió    |
|:------------------------------------------:|:----------------------------:|:---------------------:|
| A Visual Studio 2017 - legújabb (15.5 verzió) |    Enterprise, Community     |     15.5.3 verzió    |
|         A Visual Studio 2017 – RTW           |    Enterprise, Community     |     15.0.7 verzió    |
|   A Visual Studio 2015 - legújabb (3. frissítés)   |    Enterprise, Community     | 14.0.25431.01 verzió |
|         A Visual Studio 2015 - RTW           | Nincs (a karbantartási lejárt) |          ---          |

> [!NOTE]
> Karbantartás házirend, az eredetileg kiadott Microsoft megfelelően (más néven RTW) Visual Studio 2015 lejárt a karbantartáshoz.  Ezért a Visual Studio 2015 Update 3, az egyetlen fennmaradó verzió érhető el a Visual Studio 2015-öt sor a.

További információkért lásd: a [Visual Studio karbantartása házirend](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Telepített szolgáltatások?
Minden rendszerkép, hogy a Visual Studio Edition ajánlott szolgáltatáskészletet tartalmazza.  A telepítés általában a következőket tartalmazza:

* Adott munkaterhelés ajánlott választható összetevők többek között az összes rendelkezésre álló munkaterhelések
* .NET 4.6.2-es és .NET 4.7 SDK-k, a célcsoport-kezelési csomagokat és a fejlesztői eszközök
* Visual F #
* A Visual Studio GitHub-bővítmény
* LINQ to SQL-eszközök

Ez az a Visual Studio telepítésére, amikor a lemezképek összeállításakor használt parancssor:

   * vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
   * add Microsoft.Net.Component.4.7.SDK ^
   * add Microsoft.Net.Component.4.7.TargetingPack ^ 
   * add Microsoft.Net.Component.4.6.2.SDK ^
   * add Microsoft.Net.Component.4.6.2.TargetingPack ^
   * add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
   * add Microsoft.VisualStudio.Component.FSharp ^
   * add Component.GitHub.VisualStudio ^
   * add Microsoft.VisualStudio.Component.LinqToSql

A lemezképek nem szolgáltatásnak szüksége van a Visual Studio szolgáltatással, adja meg ezt a visszajelzést a visszajelzés eszközzel (a lap jobb felső sarkában).

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű VM válasszam?
Új virtuális gépek kiépítése egyszerű, és Azure számos teljes virtuális gépek méretét.  Csakúgy, mint bármely hardver beszerzési kívánt egyenleg költség és teljesítmény.  Mivel a Visual Studio egy hatékony, többszálas alkalmazást, érdemes, amely tartalmazza a processzorok és a 7 Gigabájt memóriát legalább két Virtuálisgép-méretet.  A legújabb méreteket további információkért lásd: [méretek a Windows virtuális gépek Azure-ban](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Az Azure-ral az első mentse a nem kötelező – a virtuális gép átméretezésével is egyensúlyba a a kezdeti beállítás.  Vagy megadhat egy új virtuális Gépet egy megfelelő méretű, vagy a meglévő virtuális Gépet a különböző mögöttes hardver méretezheti.  További információkért lásd: [a Windows virtuális gépek átméretezésével](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>Miután a virtuális gép fut, majd mi jelenik meg?
A Visual Studio modelljébe "megadják Önnek azt saját licenc" az Azure-ban.  Igen hasonlóan a saját hardver telepítését, az első lépések egyikét van licencelési a Visual Studio telepítése.  Fel is oldhatja a Visual Studio vagy Visual Studio-előfizetéssel társított Microsoft-fiókkal bejelentkezik, vagy fel is oldhatja a termékkulcsot a Visual Studio kezdeti vásárlásakor.  További információkért lásd: [bejelentkezni a Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) és [Visual Studio feloldásához hogyan](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>Kazettanyílás ki a virtuális gép, Fejlesztői build hogyan mentse (rögzítés) a jövőben vagy csoport használata?

Fejlesztési környezetekben skáláját túl nagy, és nincs társítva a összetettebb környezetben létrehozására.  A környezet konfigurációs függetlenül Azure azonban mentése/rögzítve a teljesen konfigurált VM képként"base" későbbi használatra – saját maga és/vagy más olyan tagok esetében a csoport megőrzi az adott befektetési könnyen.  Ezután egy új virtuális gép indításakor építse ki azt az alapjául szolgáló lemezképhez, hanem a Piactéri lemezképhez.

A gyors összegző kell sysprep és a leállítási a virtuális gép, majd *rögzítési (1. ábra)* a virtuális gépet rendszerképként az Azure portál felhasználói felületén keresztül.  Azure menti a `.vhd` fájlt, amely tartalmazza a tárfiók a kép.  Ezt követően az új lemezképet jeleníti meg az előfizetés az erőforrások listájához a kép erőforrásként.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Rögzítsen egy rendszerképet az Azure portál felhasználói felületén keresztül.*</center>

További információkért lásd: [a virtuális gépek lemezkép rögzítése](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Emlékeztető:** ne feledkezzen meg futtatni a Sysprep programot a virtuális gép!  A lépés elmaradt az Azure nem tud kiépíteni egy virtuális Gépet a lemezképből.

> [!NOTE]
> Továbbra is fel Önnek néhány költség, a lemezkép tárolására, de ez a növekményes költség nem valószínű jelentéktelen képest a virtuális gép létrehozása – az a csoport minden egyes személy, aki egy virtuális Gépet kell, hogy a munkaerő költségek.  Például a néhány dollár létrehozni és tárolni egy 127 GB-os lemezképet, amely a csoport összes tag újrafelhasználható havi költségeket.  Azonban ezek a költségek is jelentéktelen minden alkalmazott fektet kimenő építsenek, és egy megfelelően konfigurált fejlesztői mezőben saját használatra érvényesítése órához képest.

Emellett a fejlesztési feladatok vagy technológiák előfordulhat, hogy kell további méretezési – például a fejlesztési és több gép konfigurációjában fajtáinak.  Azure DevTest Labs hozhat létre _receptet_ , amely automatizálja a "arany lemezképes" létrehozása, és a csapata házirendek kezelése a futó virtuális gépek.  [Azure DevTest Labs segítségével a fejlesztők](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) DevTest Labs további információt a legjobb forrása.

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az előre konfigurált Visual Studio-lemezképek, a következő lépésre egy új virtuális gép létrehozásához:

* [Hozzon létre egy virtuális Gépet az Azure portálon keresztül](quick-create-portal.md)
* [Windows virtuális gépek – áttekintés](overview.md)
