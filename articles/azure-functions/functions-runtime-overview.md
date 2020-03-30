---
title: Az Azure Functions futásidejű áttekintése
description: Az Azure Functions futásidejű előzetes verziójának áttekintése
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226632"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions futásidejű áttekintése (előzetes verzió)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Az Azure Functions Runtime (előzetes verzió) egy új módja annak, hogy kihasználják az Azure Functions programozási modell egyszerűségét és rugalmasságát a helyszínen. Az Azure Functions-funkciókkal azonos nyílt forráskódú gyökerekre épülő Azure Functions Runtime a helyszínen van telepítve, hogy közel azonos fejlesztési élményt nyújtson, mint a felhőszolgáltatás.

![Az Azure Functions futásidejű előzetes portálja][1]

Az Azure Functions Runtime lehetővé teszi, hogy az Azure Functions a felhőbe való véglegesítés előtt tapasztalja meg az Azure Functions-t. Ily módon a kód eszközök létre majd magával viheti a felhőbe, amikor áttelepíti.  A futásidejű is új lehetőségeket nyit meg, például a helyszíni számítógépek tartalék számítási teljesítményének használatával kötegelt folyamatok egyik napról a másikra történő futtatásához. A szervezeten belüli eszközök használatával feltételesen is küldhet adatokat más rendszereknek, mind a helyszínen, mind a felhőben.

Az Azure Functions Runtime két darabból áll:

* Az Azure Functions futásidejű felügyeleti szerepköre
* Azure Functions futásidejű feldolgozói szerepkör

## <a name="azure-functions-management-role"></a>Azure functions felügyeleti szerepkör

Az Azure Functions felügyeleti szerepkör a helyszíni függvények felügyeletéhez egy gazdagép. Ez a szerepkör a következő feladatokat hajtja végre:

* Az Azure Functions Management Portal üzemeltetése, amely megegyezik az [Azure Portalon](https://portal.azure.com)láthatóval. A portál egységes élményt nyújt, amely lehetővé teszi a függvények ugyanúgy való fejlesztését, mint az Azure Portalon.
* Függvények elosztása több Függvénydolgozó között.
* Közzétételi végpont biztosítása, hogy a funkciókat közvetlenül a Microsoft Visual Studio-ból tehesse közzé a közzétételi profil letöltésével és importálásával.

## <a name="azure-functions-worker-role"></a>Azure Functions feldolgozói szerepkör

Az Azure Functions feldolgozói szerepkörök a Windows-tárolókban vannak telepítve, és ahol a függvénykód végrehajtása.  Több feldolgozói szerepkört is üzembe helyezhet a szervezetben, és ez a beállítás kulcsfontosságú módja annak, hogy az ügyfelek kihasználhassák a tartalék számítási teljesítményt.  Egy példa arra, ahol a tartalék számítási létezik számos szervezetben a gépek folyamatosan működő, de nem használják a nagy ideig.

## <a name="minimum-requirements"></a>Minimumkövetelmények

Az Azure Functions Runtime használatával való ismerkedéshez rendelkeznie kell egy Sql Server-példányhoz hozzáféréssel rendelkező számítógéppel.

## <a name="next-steps"></a>Következő lépések

Az [Azure Functions Runtime előzetes verziójának](https://aka.ms/azafrdoc) telepítése

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
