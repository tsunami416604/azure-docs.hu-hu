---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031123"
---
Ha nem tudja használni a bővítményeket, a Azure Functions Core Tools helyileg is telepítheti a projekthez szükséges adott kiterjesztési csomagokat. 

> [!NOTE]
> A bővítmények a Core Tools használatával történő manuális telepítéséhez telepíteni kell a .NET Core 2. x SDK-t.

Ha explicit módon telepíti a bővítményeket, a rendszer hozzáad egy Extensions. csproj nevű .NET-projektfájlt a projekt gyökeréhez. Ez a fájl határozza meg a függvények által igényelt NuGet-csomagok készletét. Habár a fájlban található NuGet- [csomagokra mutató hivatkozásokat](/nuget/consume-packages/package-references-in-project-files) is használhatja, a Core Tools lehetővé teszi a bővítmények telepítését anélkül, hogy manuálisan kellene szerkesztenie a fájlt.

A szükséges bővítmények a helyi projektben való telepítéséhez többféleképpen is használhatja a központi eszközöket. 

#### <a name="install-all-extensions"></a>Az összes bővítmény telepítése 

A következő parancs használatával automatikusan hozzáadhatja a helyi projekt kötései által használt összes kiterjesztési csomagot:

```dotnetcli
func extensions install
```
A parancs beolvassa a *function.jsa* fájlon, hogy megtekintse a szükséges csomagokat, telepíti őket, és újraépíti a bővítmények projektet (Extensions. csproj). Az új kötéseket a jelenlegi verzióban adja hozzá, de nem frissíti a meglévő kötéseket. `--force`Új telepítések esetén a meglévő kötések frissítése a legújabb verzióra lehetőség használatával.

#### <a name="install-a-specific-extension"></a>Egy adott bővítmény telepítése

A következő paranccsal telepítheti az adott kiterjesztésű csomagot egy adott verzióra, ebben az esetben a tárolási bővítményt:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```