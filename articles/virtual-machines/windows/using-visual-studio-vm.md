---
title: A Visual Studio használata Azure-beli virtuális gépen
description: A Visual Studio használata egy Azure virtuális gépen.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: visualstudio (visualstudio)
ms.openlocfilehash: 76c8ec8f3d691a897ec924e06b76beec746ad14a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451584"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-képek az Azure-ban
A Visual Studio használata egy előre konfigurált Azure virtuális gépen (VM) egy gyors, egyszerű módja annak, hogy a semmiből egy up-and-running fejlesztői környezetbe lépjen. A különböző Visual Studio-konfigurációkkal rendelkező rendszerképek az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)érhetők el.

Most ismerkedik az Azure-ral? [Hozzon létre egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free)

> [!NOTE]
> Nem minden előfizetés telepíthető Windows 10-es rendszerképekre. További információ: [A Windows-ügyfél használata az Azure-ban a fejlesztési és tesztelési forgatókönyvekhez](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Milyen konfigurációk és verziók érhetők el?
A legújabb főverziók, a Visual Studio 2019, a Visual Studio 2017 és a Visual Studio 2015 képei az Azure Piactéren találhatók.  Minden kiadott főverzióhoz megjelenik az eredetileg "megjelent a webre" (RTW) verzió és a legújabb frissített verziók.  Ezen verziók mindegyike a Visual Studio Enterprise és a Visual Studio Community kiadásait kínálja.  Ezek a képek legalább havonta frissülnek, hogy tartalmazzák a legújabb Visual Studio és Windows frissítéseket.  Bár a képek neve nem egyezik, minden kép leírása tartalmazza a telepített termékverziót és a kép "kezdő" dátumát.

| Kiadási verzió                                                                                                                                                | Kiadások              | Termékváltozat   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Legújabb (16.4-es verzió)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Vállalkozás, Közösség | 16.4.0-s verzió    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Vállalkozás, Közösség | 16.0.9-es verzió    |
| [Visual Studio 2017: Legújabb (15.9-es verzió)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Vállalkozás, Közösség | 15.9.17-es verzió   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Vállalkozás, Közösség | 15.0.27-es verzió  |
| [Visual Studio 2015: Legújabb (3. frissítés)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Vállalkozás, Közösség | 14.0.25431.01-es verzió |

> [!NOTE]
> A Microsoft karbantartási szabályzatának megfelelően a Visual Studio 2015 eredetileg kiadott (RTW) verziója lejárta a szervizelést. A Visual Studio 2015 3.

További információt a [Visual Studio karbantartási szabályzata tartalmaz.](https://www.visualstudio.com/productinfo/vs-servicing-vs)

## <a name="what-features-are-installed"></a>Milyen szolgáltatások vannak telepítve?
Minden lemezkép tartalmazza az adott Visual Studio-kiadáshoz ajánlott szolgáltatáskészletet. Általában a telepítés a következőket tartalmazza:

* Az összes rendelkezésre álló számítási feladat, beleértve az egyes számítási feladatok által ajánlott opcionális összetevőket
* .NET 4.6.2 és .NET 4.7 SDK-k, célzási csomagok és fejlesztői eszközök
* Vizuális F #
* GitHub-bővítmény a Visual Studio-hoz
* LINQ –SQL eszközök

A Visual Studio képek létrehozásakor történő telepítéséhez használt parancssor a következő:

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

Ha a képek nem tartalmaznak a Szükséges Visual Studio-funkciót, küldjön visszajelzést a lap jobb felső sarkában található visszajelzési eszközsegítségével.

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű virtuális gép kell választani?
Az Azure a virtuális gépek méretének teljes skáláját kínálja. Mivel a Visual Studio egy nagy teljesítményű, többszálas alkalmazás, legalább két processzort és 7 GB memóriát tartalmazó virtuális gépméretet szeretne. A Visual Studio-lemezképekhez a következő virtuális gépméreteket javasoljuk:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
A legújabb gépméretekről az [Azure-beli Windows-alapú virtuális gépek méretei](/azure/virtual-machines/windows/sizes)című témakörben talál további információt.

Az Azure-ral újra egyensúlyozhatja a kezdeti választást a virtuális gép átméretezésével. Kiépíthet egy megfelelőbb méretű új virtuális gép, vagy átméretezheti a meglévő virtuális gép különböző mögöttes hardverre. További információt a [Windows virtuális gép átméretezése című témakörben talál.](/azure/virtual-machines/windows/resize-vm)

## <a name="after-the-vm-is-running-whats-next"></a>Miután a virtuális gép fut, mi a következő lépés?
A Visual Studio a "saját licenchez való hozása" modellt követi az Azure-ban. A saját hardverre történő telepítéshez az egyik első lépés a Visual Studio telepítésének licencelése. A Visual Studio zárolásának feloldásához:
- Bejelentkezés Visual Studio-előfizetéshez társított Microsoft-fiókkal 
- A Visual Studio feloldása a vásárláshoz elérkezett termékkulccsal

További információt a [Bejelentkezés a Visual Stúdióba](/visualstudio/ide/signing-in-to-visual-studio) és a Visual Studio zárolásának feloldása című [témakörben talál.](/visualstudio/ide/how-to-unlock-visual-studio)

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hogyan menthetem a fejlesztői virtuális gép jövőbeli vagy csapathasználatra?

A fejlesztési környezetek spektruma hatalmas, és valós költségekkel jár az összetettebb környezetek kiépítése. Függetlenül attól, hogy a környezet konfigurációját, mentheti, vagy elfog, a konfigurált virtuális gép, mint egy "alap rendszerkép" későbbi használatra, vagy a csapat más tagjai számára. Ezután egy új virtuális gép indításakor az alaplemezképből, nem pedig az Azure Marketplace-rendszerképből.

Egy gyors összegzés: Használja a Rendszerelőkészítő eszközt (Sysprep), és állítsa le a futó virtuális gép leállítását, majd rögzítse *(1. ábra)* a virtuális gép lemezképként az Azure Portalon keresztül. Az Azure `.vhd` menti a fájlt, amely tartalmazza a lemezképet a kiválasztott tárfiókba. Az új kép ezután megjelenik egy image erőforrás az előfizetés erőforráslistájában.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Készítsen egy képet az Azure Portal felhasználói felületén keresztül.*</center>

További információ: [Felügyelt lemezkép létrehozása általánosgépről az Azure-ban.](/azure/virtual-machines/windows/capture-image-resource)

> [!IMPORTANT]
> Ne felejtse el használni a Sysprep programot a virtuális gép előkészítéséhez. Ha kihagyja ezt a lépést, az Azure nem tud üzembe létesíteni egy virtuális gép a rendszerképből.

> [!NOTE]
> Továbbra is felmerülő költségek tárolása a rendszerképek, de ez a növekményes költség jelentéktelen lehet, mint a rezsi költségek a virtuális gép rebuild a semmiből minden csapattag, akinek szüksége van egy. Például néhány dollárba kerül létrehozni és tárolni egy 127 GB-os képet egy hónapra, amelyet az egész csapat újrafelhasználható. Ezek a költségek azonban jelentéktelenek az egyes alkalmazottak által befektetett órákhoz képest, hogy kiépítsenek és érvényesítsenek egy megfelelően konfigurált fejlesztői keretet az egyéni használatra.

Emellett a fejlesztési feladatok vagy technológiák szükség lehet több méretezési, például a fejlesztési konfigurációk és több gép konfigurációk. Az Azure DevTest Labs segítségével _olyan recepteket_ hozhat létre, amelyek automatizálják az "aranyszínű lemezkép" felépítését. A DevTest Labs használatával is kezelheti a csapat futó virtuális gépek szabályzatait. [Az Azure DevTest Labs fejlesztőknek használata](/azure/devtest-lab/devtest-lab-developer-lab) a devtest labsekkel kapcsolatos további információk legjobb forrása.

## <a name="next-steps"></a>További lépések
Most, hogy már tud az előre konfigurált Visual Studio-lemezképekről, a következő lépés egy új virtuális gép létrehozása:

* [Virtuális gép létrehozása az Azure Portalon keresztül](quick-create-portal.md)
* [Windows virtuális gépek – áttekintés](overview.md)
