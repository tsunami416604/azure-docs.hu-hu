---
title: Alkalmazások kezelése több környezethez
description: Az Azure Service Fabric-alkalmazások olyan fürtökön futtathatók, amelyek mérete egy géptől több ezer gépig terjed. Bizonyos esetekben érdemes beállítani az alkalmazást másképpen a különböző környezetekben. Ez a cikk bemutatja, hogyan definiálhatja a környezetenként különböző alkalmazásparamétereket.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196979"
---
# <a name="manage-applications-for-multiple-environments"></a>Alkalmazások kezelése több környezethez

Az Azure Service Fabric-fürtök lehetővé teszik, hogy fürtöket hozzon létre egy és több ezer gép között. A legtöbb esetben úgy találja, hogy az alkalmazást több fürtkonfigurációra is telepítenie kell: a helyi fejlesztési fürtre, egy megosztott fejlesztési fürtre és az éles fürtre. Ezek a fürtök különböző környezeteknek minősülnek, amelyekben a kódot futtatnia kell. Az alkalmazás bináris fájljai módosítás nélkül futtathatók ezen a széles spektrumon, de gyakran másképp szeretné konfigurálni az alkalmazást.

Vegyünk két egyszerű példát:
  - a szolgáltatás egy meghatározott porton figyel, de a portnak a környezetben eltérőnek kell lennie
  - különböző kötési hitelesítő adatokat kell megadnia egy adatbázishoz a környezetekben

## <a name="specifying-configuration"></a>Konfiguráció megadása

A megadott konfiguráció két kategóriába sorolható:

- A szolgáltatások futására vonatkozó konfiguráció
  - Például egy végpont portszáma vagy egy szolgáltatás példányainak száma
  - Ez a konfiguráció az alkalmazás- vagy szolgáltatásjegyzékfájlban van megadva.
- Az alkalmazáskódra vonatkozó konfiguráció
  - Például egy adatbázis kötési információi
  - Ez a konfiguráció konfigurációs fájlokon vagy környezeti változókon keresztül biztosítható

> [!NOTE]
> Az alkalmazás és a szolgáltatásjegyzékfájl támogatási paramétereinek nem minden attribútuma.
> Ezekben az esetekben a telepítési munkafolyamat részeként karakterláncok helyettesítésére kell támaszkodnia. Az Azure DevOps-ban használhat egy bővítményt, például a Tokenek cseréje: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens vagy a Jenkins-ben futtathat egy parancsfájlfeladatot az értékek lecseréléséhez.
>

## <a name="specifying-parameters-during-application-creation"></a>Paraméterek megadása az alkalmazás létrehozása során

Ha egy elnevezett alkalmazáspéldányok a Service Fabric, lehetősége van a paraméterek et. A hogyan kell csinálni attól függ, hogyan hozza létre az alkalmazáspéldányt.

  - A PowerShellben [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) a parancsmag az alkalmazás paramétereit kivonatként veszi fel.
  - Az sfctl [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) használatával a parancs a paramétereket JSON-karakterláncként veszi fel. A install.sh parancsfájl sfctl-t használ.
  - A Visual Studio paraméterfájlokat biztosít az alkalmazásprojekt Paraméterek mappájában. Ezek a paraméterfájlok akkor használatosak, amikor a Visual Studio-ból az Azure DevOps Services vagy az Azure DevOps Server használatával teszik közzé. A Visual Studio programban a paraméterfájlok átkerülnek a Deploy-FabricApplication.ps1 parancsfájlba.

## <a name="next-steps"></a>További lépések
Az alábbi cikkek bemutatják, hogyan kell használni az itt leírt fogalmakat:

- [Környezeti változók megadása a Service Fabric szolgáltatásához](service-fabric-how-to-specify-environment-variables.md)
- [Szolgáltatás portszámának megadása a Service Fabric paramétereivel](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Konfigurációs fájlok paraméterezése](service-fabric-how-to-parameterize-configuration-files.md)

- [Környezeti változó hivatkozása](service-fabric-environment-variables-reference.md)
