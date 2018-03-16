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
ms.date: 03/02/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: e5b289b2be982653461c4f5b933e1ec444fa9716
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="visual-studio-images-on-azure"></a>A Visual Studio képek az Azure-on
Visual Studio egy előre konfigurált Azure virtuális gépen (VM) használata egy gyors és egyszerű módot eljusson semmi sem egy összesítő és futó fejlesztői környezetben. A Visual Studio különböző konfigurációival rendszerkép érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps?search=%22visual%20studio%202017%22&page=1).

Most ismerkedik az Azure-ral? [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Milyen konfigurációk és verziói érhetők el?
A legutóbbi Főverziók, a Visual Studio 2017 és a Visual Studio 2015-öt, képek található az Azure piactéren. Minden egyes főverzió látja, eredetileg kiadott (RTW) verziója és a legújabb frissített verziója. A Visual Studio Enterprise és a Visual Studio Community kiadások szövegek kínál. Ezeket a lemezképeket legalább havonta frissítve lett, hogy tartalmazza a legújabb Visual Studio és a Windows-frissítéseket. A képek nevének azonban változatlan marad, mindegyik lemezkép leírása tartalmazza a telepített verzió és a lemezkép "dátum".

| Verzió              | Kiadás            | Termék verziószáma     |
|:-----------------------------------------:|:----------------------------:|:-----------------------:|
| A Visual Studio 2017: Legújabb (15.5 verzió) |    Enterprise, Community     |      15.6.0 verzió     |
|         Visual Studio 2017: RTW           |    Enterprise, Community     |      15.0.10 verzió    |
|   Visual Studio 2015-öt: Legújabb (3. frissítés)   |    Enterprise, Community     |  14.0.25431.01 verzió  |
|         Visual Studio 2015: RTW           |              Nincs            | (A karbantartáshoz lejárt) |

> [!NOTE]
> Megfelel-e a Microsoft karbantartása házirend a Visual Studio 2015 eredetileg végleges (RTW) verziójának karbantartáshoz érvényessége lejárt. A Visual Studio 2015 Update 3 az egyetlen fennmaradó verzió érhető el a Visual Studio 2015-öt sor a rendszer.

További információkért lásd: a [Visual Studio karbantartása házirend](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Telepített szolgáltatások?
Minden rendszerkép, hogy a Visual Studio Edition ajánlott szolgáltatáskészletet tartalmazza. A telepítés általában a következőket tartalmazza:

* Az összes rendelkezésre álló feladatoknál – beleértve az egyes munkaterhelésekhez ajánlott választható összetevők
* .NET 4.6.2-es és .NET 4.7 SDK-k, a célcsoport-kezelési csomagokat és a fejlesztői eszközök
* Visual F #
* A Visual Studio GitHub-bővítmény
* LINQ to SQL-eszközök

A Visual Studio telepítésére, amikor a lemezképek összeállításakor használt parancssor a következőképpen történik:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Ha a képek nem adja meg a Visual Studio szolgáltatás, amelyekre szüksége van, a lap jobb felső sarkában a visszajelzés eszközzel visszajelzést.

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű VM válasszam?
Azure virtuális gépek méretét teljes számos. Mivel a Visual Studio egy hatékony, többszálas alkalmazást, érdemes, amely tartalmazza a processzorok és a 7 Gigabájt memóriát legalább két Virtuálisgép-méretet. Azt javasoljuk, hogy a következő Virtuálisgép-méretek a Visual Studio lemezképek:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
A legújabb méreteket további információkért lásd: [méretek a Windows virtuális gépek Azure-ban](/azure/virtual-machines/windows/sizes).

Az Azure-ral a virtuális gép átméretezésével is a kezdeti beállítás egyensúlyba. Egy új virtuális Gépet egy megfelelő méretű kiépítése, vagy a meglévő virtuális Gépet a különböző mögöttes hardver átméretezése. További információkért lásd: [méretezze át a Windows virtuális gépek](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Miután a virtuális gép fut, mi az a következő?
A Visual Studio modelljébe "állapotba hozása a saját licenc" az Azure-ban. Csakúgy, mint egy saját hardver-telepítést, az első lépések egyikét a Visual Studio telepítési van licencelési. Visual Studio vagy feloldásához:
- Bejelentkezés a Visual Studio-előfizetéssel társított Microsoft-fiókkal 
- A kezdeti vásárlásakor kapott termékkulcsot a Visual Studio feloldása

További információkért lásd: [jelentkezzen be a Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) és [Visual Studio feloldásához hogyan](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hogyan lehet, mentse a jövőben a virtuális gép fejlesztési vagy team használnak?

Fejlesztési környezetekben skáláját túl nagy, és nincs társítva a összetettebb környezetben létrehozására. Függetlenül a környezet konfiguráció mentéséhez, vagy rögzítése képként"base" későbbi felhasználás céljából, vagy más a csoport tagjai számára a konfigurált virtuális Gépet. Ezután egy új virtuális gép indításakor kiépítése azt az Azure piactér kép, hanem az alapjául szolgáló lemezképhez.

Rövid összefoglaló: a rendszer-előkészítő eszköz (Sysprep) és a virtuális gép leállítása és rögzítse *(1. ábra)* a virtuális gépet rendszerképként az Azure-portálon a felhasználói felületen. Az Azure menti a `.vhd` fájlt, amely tartalmazza a tárfiók a kép. Az új lemezkép majd mutatja az előfizetés az erőforrások listájához a kép erőforrásként.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Rögzítsen egy rendszerképet az Azure portálon felhasználói felületén keresztül.*</center>

További információkért lásd: [egy felügyelt képre egy általánosított virtuális gép létrehozása az Azure-ban](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Ne felejtse el a virtuális gép előkészítése a Sysprep használatával. A lépés elmaradt az Azure nem tud kiépíteni egy virtuális Gépet a lemezképből.

> [!NOTE]
> Néhány lemezkép tárolási költsége továbbra is fel Önnek, de a teljesen új virtuális gép számára minden csapattag számára, akik szüksége van rá, hogy az általános költségek növekményes költség jelentéktelen lehet képest. Például a néhány dollár létrehozni és tárolni egy 127 GB-os lemezképet, amely a teljes csapata által újrafelhasználható havi költségeket. Azonban ezek a költségek is jelentéktelen minden alkalmazott fektet kimenő építsenek, és egy megfelelően konfigurált fejlesztői mezőben saját használatra érvényesítése órához képest.

Emellett a fejlesztési feladatok vagy technológiák előfordulhat, hogy kell további méretezési, például a fejlesztési és több gép konfigurációjában fajtáinak. Azure DevTest Labs hozhat létre _receptet_ , amely automatizálja a "mesterlemezzel." rekordcsoportjának létrehozása DevTest Labs segítségével a csapata futó virtuális gépeinek házirendjeinek kezelése. [Azure DevTest Labs segítségével a fejlesztők](/azure/devtest-lab/devtest-lab-developer-lab) DevTest Labs további információt a legjobb forrása.

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az előre konfigurált Visual Studio-lemezképek, a következő lépésre egy új virtuális gép létrehozásához:

* [Hozzon létre egy virtuális Gépet az Azure portálon keresztül](quick-create-portal.md)
* [Windows virtuális gépek – áttekintés](overview.md)
