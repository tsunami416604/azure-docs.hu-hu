---
title: A Visual Studio Azure szolgáltatás Fabric-alkalmazások kezelése |} Microsoft Docs
description: Visual Studio használatával hozzon létre fejlesztése, a csomag, telepítése, illetve hibakeresése az Azure Service Fabric-alkalmazások és szolgáltatások.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 25c7f0e8d6ebc31121e29870026a735495ef7900
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206499"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Visual Studio segítségével egyszerűsítheti az írást, és a Service Fabric-alkalmazások kezelése
Az Azure Service Fabric-alkalmazások és szolgáltatások Visual Studio alkalmazással kezelheti. Miután megismerte [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md), használhatja a Visual Studio Service Fabric-alkalmazások létrehozása, adja hozzá a szolgáltatásokhoz, vagy a csomag, regisztrálása és alkalmazások telepítése a helyi fejlesztési fürtöt.

## <a name="deploy-your-service-fabric-application"></a>A Service Fabric-alkalmazás központi telepítése
Alapértelmezés szerint az alkalmazás központi telepítése egyesíti be egy egyszerű feladat a következő lépéseket:

1. Az alkalmazáscsomag létrehozása
2. Az image store az alkalmazáscsomag feltöltése
3. Az alkalmazástípus regisztrálása
4. Eltávolítani a alkalmazáspéldányok fut
5. Az alkalmazáspéldány létrehozása

A Visual Studio lenyomásával **F5** telepíti az alkalmazást, és csatolja a hibakereső alkalmazás összes példányát. Használhat **Ctrl + F5** nem hibakeresés, illetve hogy az alkalmazás központi telepítéséről is közzéteheti a helyi vagy távoli fürt a közzétételi profil használatával.

### <a name="application-debug-mode"></a>Alkalmazás hibakeresési módban
A Visual Studio adja meg a tulajdonságot, **alkalmazás hibakeresési módban**, amely meghatározza, hogyan szeretné kezelni az alkalmazás központi telepítésének részeként a Hibakeresés Visual Studios.

#### <a name="to-set-the-application-debug-mode-property"></a>Az alkalmazás hibakeresési módban tulajdonság beállítása
1. A Service Fabric application projekt (*.sfproj) a helyi menüben válassza a **tulajdonságok** (vagy nyomja le az ENTER a **F4** kulcs).
2. Az a **tulajdonságok** ablakban a **alkalmazás hibakeresési módban** tulajdonság.

![Állítsa be az alkalmazás hibakeresési módban tulajdonság][debugmodeproperty]

#### <a name="application-debug-modes"></a>Alkalmazás hibakeresési üzemmód

1. **Frissítse az alkalmazás** ebben a módban lehetővé teszi, hogy gyorsan módosítása és hibakeresése a kód és a statikus webes fájlok szerkesztésének hibakeresés során támogatja. Ebben az üzemmódban csak akkor működik, ha a helyi fejlesztési fürtök [1-csomópont üzemmódban]. Ez az alkalmazás hibakeresési mód alapértelmezés szerint. (/ service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Távolítsa el az alkalmazás** az alkalmazás távolítható el, amikor a hibakeresési munkamenet befejeződik.
3. **Automatikus frissítés** az alkalmazás továbbra is fut, amikor a hibakeresési munkamenet befejeződik. A következő hibakeresési munkamenetben kezeli az üzemelő példány frissítése. A frissítési folyamat megőrzi a korábbi hibakeresési munkamenetben megadott adatokat.
4. **Alkalmazás megtartása** az alkalmazás a fürt folyamatosan működik, amikor a hibakeresési munkamenet befejeződik. A következő hibakeresési munkamenet elején, az alkalmazás törlődni fog.

A **automatikus frissítése** adatok megmaradjanak a alkalmazás Service Fabric frissítési lehetőségeit alkalmazásával. Alkalmazások, és hogyan lehet, hogy frissíti az valós környezetben történő frissítéssel kapcsolatos további információkért lásd: [Service Fabric az alkalmazásfrissítés](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Szolgáltatás hozzáadása a Service Fabric-alkalmazás
Új szolgáltatások adhat hozzá az alkalmazás a bővíthetők. Győződjön meg arról, hogy a szolgáltatás része az alkalmazáscsomag, vegye fel a szolgáltatásba a **új Fabric-szolgáltatás...**  menüpont.

![Egy új Service Fabric-szolgáltatás hozzáadása][newservice]

Jelöljön ki egy Service Fabric projekt hozzá az alkalmazáshoz, és adja meg a szolgáltatás nevét.  Lásd: [keretrendszere, amely a szolgáltatás kiválasztása](service-fabric-choose-framework.md) , hogy eldönthesse, melyik szolgáltatás típust használ.

![Jelöljön ki egy Service Fabric szolgáltatás projekt hozzá az alkalmazáshoz][addserviceproject]

Az új szolgáltatás a megoldás és a meglévő alkalmazáscsomag kerül. A szolgáltatási hivatkozást lekérni, és egy alapértelmezett szolgáltatáspéldány kerülnek be az alkalmazás jegyzékében, amely a szolgáltatás létrehozása és a következő alkalommal, az alkalmazás központi telepítése megkezdődött.

![Az új szolgáltatást hozzáadta az alkalmazásjegyzék][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>A Service Fabric-alkalmazás becsomagolása
Az alkalmazás- és a fürt telepítéséhez szüksége egy alkalmazáscsomag létrehozásához.  A csomag rendszerezi az alkalmazás jegyzékében szolgáltatás jegyzékfájlban és egyéb szükséges fájlok egy adott elrendezésben.  A Visual Studio állít be, és az alkalmazási projekt mappában, a "pkg" könyvtárban csomag kezeli.  Kattintson a **csomag** a a **alkalmazás** helyi menü hoz létre, vagy az alkalmazás-csomag frissítése.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Távolítsa el az alkalmazások és a Cloud Explorer használatával alkalmazástípusok
Műveleteket hajthat végre alapszintű fürt felügyeleti belül a Visual Studio használatával indítja el a Cloud Explorerben a **nézet** menü. Például alkalmazások törlése és leépíteni a következőt: alkalmazástípusok helyi vagy távoli fürtökön.

![Alkalmazások eltávolítása][removeapplication]

> [!TIP]
> Több fürt kezelési funkcióját, lásd: [a fürt megjelenítése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [A Service Fabric alkalmazásmodellt.](service-fabric-application-model.md)
* [A Service Fabric-alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md)
* [Alkalmazás paramétereinek több környezet kezelése](service-fabric-manage-multiple-environment-app-configuration.md)
* [A Service Fabric-alkalmazás hibakeresés](service-fabric-debugging-your-application.md)
* [A fürt megjelenítése a Service Fabric Explorer használatával](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png