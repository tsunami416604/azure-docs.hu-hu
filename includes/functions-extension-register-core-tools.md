---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689556"
---
Ha nem tudja használni a bővítményeket, a Azure Functions Core Tools helyileg is telepítheti a projekthez szükséges adott kiterjesztési csomagokat.

> [!IMPORTANT]
> Bővítményeket nem telepíthet explicit módon olyan Function alkalmazásban, amely bővítő csomagokat használ. A `extensionBundle` bővítmények explicit telepítése előtt távolítsa el a *host.js* szakaszát.

A következő elemek bizonyos okokat ismertetnek, amelyekkel manuálisan lehet telepíteni a bővítményeket:

* Hozzá kell férnie egy bővítmény egy adott verziójához, amely nem érhető el egy csomagban.
* Egy csomagban nem elérhető egyéni bővítménynek kell hozzáférnie.
* Hozzá kell férnie a bővítmények egy adott kombinációjában, amely egyetlen csomagban nem érhető el.

> [!NOTE]
> A bővítmények a Core Tools használatával történő manuális telepítéséhez telepíteni kell a [.net Core 2. x SDK](https://dotnet.microsoft.com/download) -t. A Azure Functions Core Tools a bővítmények NuGet-ból való telepítésére használja a .NET Core SDK. A .NET-et nem kell tudnia Azure Functions-bővítmények használatára.

Ha explicit módon telepíti a bővítményeket, a rendszer hozzáad egy Extensions. csproj nevű .NET-projektfájlt a projekt gyökeréhez. Ez a fájl határozza meg a függvények által igényelt NuGet-csomagok készletét. Habár a fájlban található NuGet- [csomagokra mutató hivatkozásokat](/nuget/consume-packages/package-references-in-project-files) is használhatja, a Core Tools lehetővé teszi a bővítmények telepítését anélkül, hogy manuálisan kellene szerkesztenie a fájlt.

A szükséges bővítmények a helyi projektben való telepítéséhez többféleképpen is használhatja a központi eszközöket. 

#### <a name="install-all-extensions"></a>Az összes bővítmény telepítése 

A következő parancs használatával automatikusan hozzáadhatja a helyi projekt kötései által használt összes kiterjesztési csomagot:

```dotnetcli
func extensions install
```
A parancs beolvassa a *function.jsa* fájlon, hogy megtekintse a szükséges csomagokat, telepíti őket, és újraépíti a bővítmények projektet (Extensions. csproj). Az új kötéseket a jelenlegi verzióban adja hozzá, de nem frissíti a meglévő kötéseket. `--force`Új telepítések esetén a meglévő kötések frissítése a legújabb verzióra lehetőség használatával.

Ha a Function alkalmazás olyan kötéseket használ, amelyeket az alapvető eszközök nem ismernek fel, manuálisan kell telepítenie az adott bővítményt.

#### <a name="install-a-specific-extension"></a>Egy adott bővítmény telepítése

A következő paranccsal telepítheti az adott kiterjesztésű csomagot egy adott verzióra, ebben az esetben a tárolási bővítményt:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
