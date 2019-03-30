---
title: A Visual Studióban az Azure Service Fabric-alkalmazások kezelése |} A Microsoft Docs
description: A Visual Studio használatával hozzon létre, fejleszthet, csomagolását, üzembe helyezése és hibakeresése az Azure Service Fabric-alkalmazások és szolgáltatások.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 4744858869e10094389be58ddd3960cb8cc2773a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664845"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Írása, és a Service Fabric-alkalmazások kezelése a Visual Studio használatával
Az Azure Service Fabric-alkalmazások és szolgáltatások Visual Studión keresztül kezelheti. Miután [a fejlesztési környezet beállítása](service-fabric-get-started.md), a Visual Studio segítségével Service Fabric-alkalmazások létrehozása, adja hozzá a szolgáltatások vagy a csomag regisztrálása és alkalmazások üzembe helyezése a helyi fejlesztési fürtön.

## <a name="deploy-your-service-fabric-application"></a>A Service Fabric-alkalmazás üzembe helyezése
Alapértelmezés szerint az alkalmazás üzembe helyezése kombinálja egy egyszerű műveletben figyelembe az alábbi lépéseket:

1. Az alkalmazáscsomag létrehozása
2. Az alkalmazáscsomag feltöltése a lemezképtároló
3. Az alkalmazástípus regisztrációja
4. Eltávolítja az összes futó alkalmazáspéldányok
5. Egy alkalmazás-példány létrehozása

A Visual Studióban nyomja **F5** telepíti az alkalmazást, és csatolja a hibakeresőt alkalmazás összes példányán. Használhat **Ctrl + F5** -alkalmazások üzembe helyezése nélkül a hibakeresés, vagy közzéteheti az egy helyi vagy távoli fürtön a közzétételi profil használatával.

### <a name="application-debug-mode"></a>Alkalmazás hibakeresési módban
A Visual Studio meg tulajdonsággal **alkalmazás hibakeresési módban**, amely szabályozza, hogyan szeretné kezelni az alkalmazás központi telepítésének részeként Hibakeresés Visual Studios.

#### <a name="to-set-the-application-debug-mode-property"></a>Az alkalmazás hibakeresési módban tulajdonság beállítása
1. A Service Fabric-alkalmazásprojektet a (*.sfproj) a helyi menüben válassza a **tulajdonságok** (vagy nyomja le a **F4** kulcs).
2. Az a **tulajdonságok** ablakban állítsa be a **alkalmazás hibakeresési módban** tulajdonság.

![Állítsa be az alkalmazás hibakeresési módban tulajdonság][debugmodeproperty]

#### <a name="application-debug-modes"></a>Alkalmazás hibakeresési üzemmód

1. **Aktualizovat Aplikaci** ebben a módban lehetővé teszi, hogy gyorsan módosítása és hibakeresése a kód és a statikus fájlok szerkesztése a hibakeresés során támogatja. Ebben az üzemmódban csak akkor működik, ha a helyi fejlesztési fürtöt 1 csomópontos üzemmódban van. Ez az alkalmazás hibakeresési mód alapértelmezés szerint.
2. **Alkalmazás eltávolítása** távolítható el, ha a hibakeresési munkamenet véget ér az alkalmazást.
3. **Automatikus frissítés** az alkalmazás továbbra is fut, amikor a hibakeresési munkamenet befejeződik. A következő hibakeresési munkamenet kezeli az üzembe helyezés frissítéseként. A frissítési folyamat megőrzi a korábbi hibakeresési munkamenetben megadott adatokat.
4. **Zachovat Aplikaci** az alkalmazás a fürt továbbra is működik, ha a hibakeresési munkamenet azért ér véget. A következő hibakeresési munkamenet elején, az alkalmazás törlődni fog.

A **automatikus frissítése** adatok őrződnek meg az alkalmazás frissítési képességeit a Service Fabric alkalmazásával. Alkalmazások, és hogyan lehet, hogy végre frissítést a valós környezetben történő frissítéssel kapcsolatos további információkért lásd: [Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Szolgáltatás hozzáadása a Service Fabric-alkalmazás
Az alkalmazás a funkciójának bővítése új szolgáltatásokkal adhat hozzá. Győződjön meg arról, hogy a szolgáltatás tartalmazza az alkalmazáscsomagot, vegye fel a szolgáltatás keretében a **új Fabric-szolgáltatás...**  menüpontot.

![Adjon hozzá egy új Service Fabric-szolgáltatás][newservice]

Jelöljön ki egy Service Fabric project hozzáadása az alkalmazáshoz, és adja meg a szolgáltatás nevét.  Lásd: [olyan keretrendszer, a szolgáltatás kiválasztása](service-fabric-choose-framework.md) , segít eldönteni, hogy a szolgáltatás típusának.

![Jelöljön ki egy Service Fabric service projektben adja hozzá az alkalmazáshoz][addserviceproject]

Az új szolgáltatáshoz a megoldás és a meglévő alkalmazáscsomag kerül. A szolgáltatási hivatkozást és a egy alapértelmezett szolgáltatáspéldány hozzáadódik az alkalmazásjegyzékben, okoz a szolgáltatás létrehozása és a következő alkalommal, amikor az alkalmazás telepítése elindult.

![Az új szolgáltatást hozzáadta az application manifest][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>A Service Fabric-alkalmazás becsomagolása
Az alkalmazás és a hozzá tartozó szolgáltatások üzembe helyezése egy fürtön, hogy szeretne létrehozni egy alkalmazáscsomagot.  A csomag rendszerezi az alkalmazásjegyzékben, szolgáltatásjegyzékek és egyéb szükséges fájlok egy adott elrendezésben.  A Visual Studio állít be, és kezeli a csomag az alkalmazásprojekt mappában, a "csomag" könyvtárban.  Kattintson a **csomag** származó a **alkalmazás** létrehozása vagy frissítése az alkalmazáscsomag egy helyi menü.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Távolítsa el az alkalmazások és alkalmazás-típusok Cloud Explorer használatával
Alapszintű fürt felügyeleti műveleteket végezhet a Visual Studio Cloud Explorer, amely már el is indíthatja a használatával a **nézet** menü. Például törölje az alkalmazást, és leépíteni a következőt: alkalmazástípusok helyi vagy távoli fürtökön.

![Alkalmazás eltávolítása][removeapplication]

> [!TIP]
> Több fürt kezelési funkcióját, lásd: [a Service Fabric Explorerrel a fürt megjelenítése](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Service Fabric-alkalmazásmodell](service-fabric-application-model.md)
* [A Service Fabric-alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md)
* [Több környezethez alkalmazásparaméterek kezelése](service-fabric-manage-multiple-environment-app-configuration.md)
* [A Service Fabric-alkalmazás hibakeresése](service-fabric-debugging-your-application.md)
* [A fürt megjelenítése a Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
