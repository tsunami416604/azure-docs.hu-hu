---
title: Egy Azure virtuális gépen a Visual Studio használatával |} A Microsoft Docs
description: A Visual Studio használatával egy Azure virtuális gépen.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 04/02/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: df91ba12c1ee74b8e28f75344a5cd55af018d3cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884873"
---
# <a name="visual-studio-images-on-azure"></a>Azure Visual Studio-rendszerképek
A Visual Studio használatával egy előre konfigurált Azure virtuális gépen (VM) módja a gyors és egyszerű nyissa meg a művelet egy felfelé és a futó fejlesztési környezetben. Visual Studio különböző konfigurációival rendszerképek érhetők el a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=visual%20studio&page=1).

Most ismerkedik az Azure-ral? [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Mely konfigurációkat és verziói érhetők el?
A legutóbbi főbb verziók, Visual Studio 2019, a Visual Studio 2017 és a Visual Studio 2015-öt, a lemezképek az Azure Marketplace-en található.  Az egyes kiadott főverzió, megjelenik a "eredetileg webes" (RTW) verziójához és a legújabb frissített.  Ezek a fájlok mindegyike kínál a Visual Studio Enterprise és a Visual Studio Community kiadása.  Ezek a lemezképek legalább havonta frissül a legújabb Visual Studio és a Windows-frissítéseket tartalmaznak.  A rendszerképek nevei változatlanok maradnak, amíg minden Képleírás tartalmazza a telepített verzió és a kép "megfelelő".

| Verzió                                              | Kiadások                     |     Termék verziószáma      |
|:------------------------------------------------------------:|:----------------------------:|:------------------------:|
|     Visual Studio 2019: RTW                                  |    Enterprise, Community     |      Verzió 16.0.0      |
| Visual Studio 2017: Legújabb (15.9 verzió)                    |    Enterprise, Community     |      Verzió 15.9.10     |
|         Visual Studio 2017: RTW                              |    Enterprise, Community     |      Verzió 15.0.22     |
|   Visual Studio 2015: Legújabb (3. frissítés)                      |    Enterprise, Community     |  Verzió 14.0.25431.01   |
|         Visual Studio 2015: RTW                              |             None             | (Karbantartási lejárt)  |

> [!NOTE]
> Fogja a providerhez kiadott Microsoft karbantartási szabályzat a Visual Studio 2015 (RTW) eredetileg kiadott verzióját karbantartási érvényessége lejárt. A Visual Studio 2015 Update 3 az egyetlen fennmaradó verzió érhető el a Visual Studio 2015-öt termék sor.

További információkért lásd: a [Visual Studio karbantartási szabályzat](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Telepített szolgáltatások?
Az egyes rendszerképek tartalmának a Visual Studio-kiadás ajánlott funkciókészlethez. A telepítés általában a következőket tartalmazza:

* Az összes rendelkezésre álló számítási feladatokat, beleértve az egyes számítási feladatok ajánlott választható összetevők
* .NET 4.6.2-es verziójára és a .NET 4.7 SDK-k, a célcsoport-kezelési csomagokat és a fejlesztői eszközök
* Visual F#
* GitHub-bővítmény a Visual Studio
* LINQ to SQL-eszközök

A Visual Studio telepítése a rendszerképek készítése során használt parancssor a következőképpen történik:

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

Ha a lemezképek nem tartalmaznak, amelyekre szüksége van a Visual Studio szolgáltatás, az oldal jobb felső sarkában a visszajelzés eszközzel visszajelzést.

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű virtuális gép válasszam?
Az Azure virtuálisgép-méretek széles kínál. Mivel a Visual Studio egy hatékony, több szálon futó alkalmazást, azt szeretné, amely tartalmazza a processzorok és a 7 GB memória, legalább két virtuális gép mérete. A Visual Studio-lemezképek a következő virtuális gépek méretei javasoljuk:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
A legújabb gépméretek további információkért lásd: [méretek a Windows virtuális gépek az Azure-ban](/azure/virtual-machines/windows/sizes).

Az Azure-ban fürtméret növelése a kezdeti beállítás szerint a virtuális gép átméretezése. Egy új virtuális gép létrehozása több megfelelő méretű, vagy más mögöttes hardver a meglévő virtuális gép átméretezése. További információkért lásd: [Windows virtuális gép átméretezése](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Miután a virtuális gép fut, mit tartogat a jövő?
A Visual Studio követi a "saját licenc használata" modellt az Azure-ban. Csakúgy, mint egy saját hardver-telepítést, az első lépések a Visual Studio telepítésekor van licencelése. A Visual Studio vagy feloldásához:
- A Visual Studio-előfizetéssel társított Microsoft-fiókkal 
- A kezdeti vásárlásakor kapott termékkulcsot a Visual Studio feloldása

További információkért lásd: [jelentkezzen be a Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) és [feloldásához a Visual Studio hogyan](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hogyan tudok mentse a jövőben a virtuális gép fejlesztési vagy csapatot használni?

A különböző fejlesztési környezetek hatalmas, és valódi költséggel az összetett környezetek kialakítására. A környezet konfigurációját, függetlenül mentheti, vagy rögzítése, a konfigurált virtuális gép "alap lemezképeként" későbbi használat céljából, vagy a csoport egyéb tagjai számára. Ezt követően az új virtuális gép indításakor annak üzembe helyezésekor az alaprendszerképet az Azure Marketplace-beli rendszerképét ahelyett.

Rövid összefoglalás: A rendszer-előkészítő eszköz (Sysprep) és állítsa le a futó virtuális Gépre, és ezután rögzítheti *(1. ábra)* a virtuális Gépet rendszerképként az Azure Portalon a felhasználói felületen. Az Azure menti a `.vhd` fájlt, amely tartalmazza a rendszerkép tetszőleges storage-fiókban. Az új rendszerképet, majd az előfizetéshez tartozó erőforrások listájában kép erőforrásként jelenik meg.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Rögzíthet egy képet, az Azure portal felhasználói felületén keresztül.*</center>

További információkért lásd: [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Ne felejtse el a virtuális gép előkészítése a Sysprep eszközt használja. Hagyja ki ezt a lépést, ha az Azure nem tud kiépíteni a rendszerképből egy virtuális Gépet.

> [!NOTE]
> Továbbra is díjat kell néhány költsége a lemezképek tárolását, de a növekményes költségekkel, hogy a jelentés minden egyes csapattag számára szüksége van rá a virtuális gép teljesen új újraépítése költségek képest. Például létrehozni és tárolni egy 127 GB-os rendszerképet, amely a teljes csapata által újrafelhasználható havi pár dollárért költségei. Azonban ezeket a díjakat is jelentéktelen minden alkalmazott fektet építse ki, és a egy megfelelően konfigurált fejlesztési mező saját használatra érvényesítéséhez órához képest.

Ezenkívül a fejlesztési feladatokat vagy technológiák előfordulhat, hogy kell több méretezési, például a fejlesztői konfigurációk és több gép konfigurációja. Az Azure DevTest Labs használatával hozzon létre _receptek_ , amely automatizálja a "mesterlemezzel." rekordcsoportjának létrehozása DevTest Labs használatával házirendjeinek kezelése a csapat futó virtuális gépek. [Azure DevTest Labs használatával a fejlesztők](/azure/devtest-lab/devtest-lab-developer-lab) práci s DevTest Labs további információt a legjobb forrása.

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az előre konfigurált Visual Studio-lemezképek, a következő lépésben egy új virtuális Gépet hoz létre:

* [Hozzon létre egy virtuális Gépet az Azure Portalon keresztül](quick-create-portal.md)
* [Windows Virtual Machines – áttekintés](overview.md)
