---
title: "Az Azure Service Fabric Eclipse beépülő modulja használatának első lépései | Microsoft Docs"
description: "Az Azure Service Fabric Eclipse beépülő modulja használatának első lépései."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Az Eclipse beépülő modul használatának első lépései Service Fabric Java-alkalmazás fejlesztéséhez
Az Eclipse a Java-fejlesztők által egyik leggyakrabban használt IDE. Ebben a cikkben bemutatjuk, hogyan állíthatja be az Eclipse fejlesztői környezetet a Service Fabrickel való használathoz. A cikk segítséget nyújt a beépülő modul telepítéséhez, Service Fabric-alkalmazások létrehozásához és a Service Fabric-alkalmazás helyi vagy távoli Service Fabric-fürtön való üzembe helyezéséhez.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>A Service Fabric beépülő modul telepítése vagy frissítése az Eclipse Neonon
A Service Fabric egy beépülő modult biztosít a **Java-fejlesztőknek készült Eclipse IDE-hez**, amely leegyszerűsítheti a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

1. Győződjön meg arról, hogy telepítve van a legújabb Eclipse **Neon** és a Buildship&1;.0.17-es vagy újabb verziója. A telepített összetevők verzióját a ``Help => Installation Details`` lehetőség kiválasztásával ellenőrizheti. Az [itt][buildship-update] található utasítások alapján frissítheti a Buildshipet. Annak ellenőrzéséhez, hogy az Eclipse Neon verziója a legújabb-e, lépjen a következő helyre: ``Help => Check for Updates``.

2. A Service Fabric beépülő modul telepítéséhez válassza a ``Help => Install New Software...`` lehetőséget.
  1. A Work with (Használat) szövegbeviteli mezőbe írja be a következőt: ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Kattintson az Add (Hozzáadás) parancsra.

  ![Eclipse Neon beépülő modul a Service Fabrichez][sf-eclipse-plugin-install]

  3. Válassza ki a Service Fabric beépülő modult, majd kattintson a Next (Tovább) gombra.
  4. Folytassa a telepítést, és fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verziót használja. Ellenőrizze, hogy a program tovább frissíthető-e: lépjen a ``Help => Installation Details`` hivatkozásra, keresse meg a Service Fabric elemet a telepített beépülő modulok listájában, és kattintson a frissítés parancsra. Ha van függőben lévő frissítés, a rendszer lekéri és telepíti.

> [!NOTE]
> Ha a Service Fabric Eclipse beépülő moduljának telepítése vagy frissítése túl sok időt vesz igénybe az Eclipse-en, annak az az oka, hogy a mindenkori Eclipse megpróbálja lekérni az összes, az Eclipse-példánnyal regisztrált frissítési helyen történt új módosítás metaadatait. A gyorsításhoz használja ezt az apró trükköt: lépjen az `Available Software Sites` helyre, és törölje az összes beállítás jelölését a Service Fabric beépülő modul helyére (`http://dl.windowsazure.com/eclipse/servicefabric`) mutatón kívül.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse segítségével

1. Nyissa meg a következőt: ``File => New => Other``. Válassza a ``Service Fabric Project`` elemet. Kattintson a ``Next`` gombra.

    ![Service Fabric – Új projekt, 1. oldal][create-application/p1]

2. Nevezze el a projektet. Kattintson a ``Next`` gombra.

    ![Service Fabric – Új projekt, 2. oldal][create-application/p2]

3. Az elérhető sablonok közül válasszon szolgáltatássablont (aktor, állapot nélküli, tároló vagy vendég által futtatható). Kattintson a ``Next`` gombra.

    ![Service Fabric – Új projekt, 3. oldal][create-application/p3]

4. Ezen az oldalon adja meg a szolgáltatás nevét és/vagy a szükséges szolgáltatásadatokat, majd kattintson a ``Finish`` gombra.

    ![Service Fabric – Új projekt, 4. oldal][create-application/p4]

5. Az első Service Fabric-projekt létrehozásakor a rendszer rákérdez, hogy szeretné-e beállítani a Service Fabric-perspektívát. A folytatáshoz válassza a ``yes`` lehetőséget.

    ![Service Fabric – Új projekt, 5. oldal][create-application/p5]

6. A sikeres létrehozás után a projekt így jelenik meg:

    ![Service Fabric – Új projekt, 6. oldal][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Service Fabric-alkalmazás felépítése és üzembe helyezése az Eclipse segítségével

* Kattintson a jobb gombbal a fentiekben létrehozott Service Fabric-alkalmazásra. A helyi menüben válassza a ``Service Fabric`` lehetőséget. Ekkor megjelenik egy almenü több lehetőséggel. Ez a következőképpen néz ki:

    ![Service Fabric helyi menü][publish/RightClick]

  Ha a felépítési, újraépítési és tisztítási beállításokra kattint, a rendszer végrehajtja a kívánt műveleteket.
  - A ``Build Application`` felépíti az alkalmazást tisztítás nélkül
  - A ``Rebuild Application`` kezdetektől felépíti újra az alkalmazást
  - A ``Clean Application`` tisztítja a létrehozott összetevők alkalmazását


* Ebből a menüből üzembe helyezheti, vagy közzéteheti az alkalmazását, illetve megszüntetheti annak üzembe helyezését.
  - A ``Deploy Application`` a helyi fürtön végzi el az üzembe helyezést
  - A ``Publish Application...`` rákérdez, hogy a ``Local.json`` vagy a ``Cloud.json`` közzétételi profilt választja. Ezek a JSON-fájlok a helyi vagy a felhőbeli (Azure-) fürthöz való csatlakozáshoz szükséges információk (például csatlakozási végpontok és biztonsági információk) tárolására szolgálnak.

  ![Service Fabric helyi menü][publish/Publish]

* Egy másik módszerrel is üzembe helyezheti Service Fabric-alkalmazását Eclipse futtatási konfigurációkkal.

  1. Válassza a ``Run => Run Configurations`` elemet. Válassza a ``ServiceFabricDeployer`` futtatási konfigurációt a ``Grade Project`` területen.
  2. A jobb oldali ablaktábla ``Arguments`` lapján a ``publishProfile`` értékeként adja meg a **local** (helyi) vagy a **cloud** (felhő) értéket. Az alapértelmezett beállítás a **local** (helyi). Távoli/felhőbeli fürtre történő üzembe helyezéshez válassza a **cloud** (felhő) beállítást.
  3. Töltse be a megfelelő információkat a közzétételi profilokba a `Local.json` vagy a `Cloud.json` megfelelő módon történő szerkesztésével, végponti adatok és biztonsági hitelesítő adatok megadásával, ha vannak.
  4. Győződjön meg arról, hogy a jobb oldali panelen a ``Grade Project`` területen található ``Working Directory`` az üzembe helyezni kívánt alkalmazásra mutat. Ha nem, kattintson a ``Workspace...`` gombra, és válassza ki a kívánt alkalmazást.
  5. Kattintson az **Apply** (Alkalmaz), majd a **Run** (Futtatás) lehetőségre.

Néhány másodpercen belül megtörténik az alkalmazás felépítése és üzembe helyezése. Az állapotát megfigyelheti a Service Fabric Explorerből.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Új Service Fabric-szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz

Az alábbi lépésekkel adhat hozzá új Service Fabric-szolgáltatást meglévő Service Fabric-alkalmazáshoz:

1. Kattintson a jobb gombbal a projektre, amelyhez hozzá kíván adni egy szolgáltatást. A megnyíló helyi menüből válassza a Service Fabric lehetőséget. Ekkor megjelenik egy almenü több lehetőséggel.

    ![Service Fabric – Szolgáltatás hozzáadása, 1. oldal][add-service/p1]

2. Válassza az `Add ServiceFabric Service` lehetőséget. Ez végigvezeti a szolgáltatás a projekthez adásának lépésein.
3. Válassza ki a projekthez hozzáadni kívánt szolgáltatássablont, majd kattintson a Next (Tovább) gombra.

    ![Service Fabric – Szolgáltatás hozzáadása, 2. oldal][add-service/p2]

4. Adja meg a szolgáltatás nevét (és a többi szükséges adatot), majd kattintson a lenti Add Service (Szolgáltatás hozzáadása) gombra.  

    ![Service Fabric – Szolgáltatás hozzáadása, 3. oldal][add-service/p3]

5. A szolgáltatás sikeres hozzáadása után a teljes projektstruktúra a következőhöz hasonló:

    ![Service Fabric – Szolgáltatás hozzáadása, 4. oldal][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazásának frissítése

Tegyük fel, hogy a Service Fabric Eclipse beépülő moduljával létrehozta az ``App1`` projektet, üzembe is helyezte a beépülő modullal, és létrehozta az ``App1AppicationType`` alkalmazástípusú és 1.0-s alkalmazásverziójú ``fabric:/App1Application`` nevű alkalmazást. Most leállítás nélkül szeretné frissíteni az alkalmazást.

Végezze el a módosítást az alkalmazáson, és építse újra a módosított szolgáltatást.  Frissítse a módosított szolgáltatás jegyzékfájlját (``ServiceManifest.xml``) a szolgáltatás frissített verzióival (és a megfelelő Code, Config vagy Data értékkel). Módosítsa az alkalmazás jegyzékfájlját is (``ApplicationManifest.xml``) az alkalmazás frissített verziószámával és a módosított szolgáltatással.  

Az alkalmazás Eclipse-szel való frissítéséhez létrehozhat egy duplikált futtatási konfigurációt, és a segítségével frissítheti az alkalmazását akkor és úgy, ahogy szeretné. Ehhez kövesse az alábbi lépéseket:
1. Válassza a ``Run => Run Configurations`` elemet. A bal oldali ablaktáblán kattintson a ``Grade Project`` bal oldalán lévő kis nyílra.
2. Kattintson a jobb gombbal a ``ServiceFabricDeployer`` elemre, és válassza a ``Duplicate`` lehetőséget. Adjon egy új nevet a konfigurációnak, például: ``ServiceFabricUpgrader``.
3. A jobb oldali ablaktáblán, a ``Arguments`` lapon módosítsa a ``-Pconfig='deploy'`` értéket ``-Pconfig=upgrade`` értékre, majd kattintson az ``Apply`` elemre.
4. Ezzel létrehozott és mentett egy futtatási konfigurációt az alkalmazása frissítéséhez, amelyen bármikor végrehajthatja a ``Run`` parancsot. Ez biztosítja, hogy a legújabb frissített alkalmazástípus-verzió legyen lekérve az alkalmazás jegyzékfájljából.

Mostantól megfigyelheti az alkalmazásfrissítést a Service Fabric Explorer segítségével. Az alkalmazás néhány percen belül frissült volna.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

