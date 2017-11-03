---
title: "Alkalmazások közzététele a Visual Studio távoli fürthöz |} Microsoft Docs"
description: "Útmutató: a távoli service fabric-fürt az alkalmazás közzététele a Visual Studio használatával."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: faecd892-eb54-4d9c-8023-c67442afb8e8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/29/2016
ms.author: cawa
ms.openlocfilehash: c440c520d84fc503ff9e705555449e92555d4721
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-remove-applications-using-visual-studio"></a>Központi telepítése, és távolítsa el az alkalmazásokat a Visual Studio használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Az Azure Service Fabric-bővítmény, a Visual Studio segítségével könnyen, megismételhető és parancsfájlok futtatására alkalmas közzétenni egy alkalmazást, a Service Fabric-fürt.

## <a name="the-artifacts-required-for-publishing"></a>A közzététel a szükséges összetevők
### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 központi telepítése
Ez azért, amely a közzétételi profil elérési út használ paraméterként alkalmazás-közzététel a Service Fabric PowerShell-parancsfájlt. Mivel ez a parancsfájl az alkalmazás a része, azonban nyugodtan megadhatnak alkalmazás szükség szerint módosíthatja azt.

### <a name="publish-profiles"></a>Profilok közzététele
Egy mappa a Service Fabric-alkalmazás projekt neve a **PublishProfiles** XML-fájlok, például egy alkalmazás-közzététel alapvető információt tároló tartalmazza:

* A Service Fabric-fürt kapcsolódási paraméterek
* Egy alkalmazás paraméter fájl elérési útja
* Beállítások frissítése

Alapértelmezés szerint az alkalmazás tartalmazza három profilok közzététele: Local.1Node.xml Local.5Node.xml és Cloud.xml. Is hozzáadhat további profilok másolása és beillesztése egy alapértelmezett fájlt.

### <a name="application-parameter-files"></a>Alkalmazásparaméter-fájlokat
Egy mappa a Service Fabric-alkalmazás projekt neve a **ApplicationParameters** XML-fájlok az alkalmazás a felhasználó által megadott jegyzék paramétereket tartalmaz. Application manifest-fájlok is paraméteres, így a központi telepítési beállításokra eltérő értékeket is használhat. Az alkalmazás paraméterezése kapcsolatos további információkért lásd: [kezelése a Service Fabric több környezet](service-fabric-manage-multiple-environment-app-configuration.md).

> [!NOTE]
> Aktorszolgáltatások a projekt először leskálázást szerkeszteni a fájlt valamelyik szerkesztőben vagy a közzététel párbeszédpanelen keresztül kell létrehozni. Ennek oka az, a jegyzékfájlt része az összeállítás közbeni jön létre.

## <a name="to-publish-an-application-using-the-publish-service-fabric-application-dialog-box"></a>A Fabric-alkalmazás közzététele párbeszédpanelen alkalmazás közzététele
A következő lépések bemutatják, hogyan lehet közzétenni egy alkalmazást, amely a **Fabric-alkalmazás közzététele** a Visual Studio Service Fabric-eszközök által biztosított párbeszédpanel.

1. Válassza ki a helyi menüben a Service Fabric-alkalmazás projekt **közzététel...** megtekintéséhez a **Fabric-alkalmazás közzététele** párbeszédpanel megnyitásához.
   
    ![A ** közzététele Service Fabric Application ** párbeszédpanel][0]
   
    A kiválasztott fájl a **profil cél** legördülő lista akkor, ha a beállítások, kivéve **verziók Manifest**, menti. Ismét felhasználni, egy meglévő profilt, vagy hozzon létre egy újat kiválasztásával **< profilok kezelése... >** a a **céloz profil** legördülő listában. Ha úgy dönt, hogy a közzétételi profilt, annak tartalmát a megfelelő mezőket a párbeszédpanel jelenik meg. A módosítások mentéséhez tetszőleges időpontban, válassza ki a **profil mentése** hivatkozásra.    
2. Az a **csatlakozási végpont** területen adjon meg egy helyi vagy távoli Service Fabric fürt közzétételi végpont. Adja hozzá, vagy módosítsa a csatlakozási végpont, kattintson a **csatlakozási végpont** legördülő listából. A lista mutatja azokat a rendelkezésre álló Service Fabric-fürt kapcsolati végpontok is közzéteheti a társított Azure-előfizetéseit alapján. Vegye figyelembe, hogy ha még nem jelentkezett Visual Studio, kérni fogja erre.
   
    A fürt kiválasztása párbeszédpanel használatával adja meg az elérhető előfizetések és-fürtök a készletből.
   
    ![A ** Válasszon Service Fabric fürt ** párbeszédpanel][1]
   
   > [!NOTE]
   > Ha szeretné közzétenni egy tetszőleges végpontot (például egy entitás fürt), tekintse meg a **közzététele egy tetszőleges a fürt végpontja** az alábbi szakasz.
   > 
   > 
   
    Ha úgy dönt, hogy a végpont, a Visual Studio ellenőrzi a kapcsolatot a kijelölt Service Fabric-fürt. Ha a fürt nem biztonságos, a Visual Studio csatlakozhat hozzá azonnal. Azonban ha a fürt biztonságos, lesz szüksége a tanúsítvány telepítése a helyi számítógépen, a folytatás előtt. Lásd: [biztonságos kapcsolatok konfigurálása](service-fabric-visualstudio-configure-secure-connections.md) további információt. Amikor elkészült, válassza ki a **OK** gombra. A kijelölt fürt megjelenik a **Fabric-alkalmazás közzététele** párbeszédpanel megnyitásához.
3. A a **alkalmazás paraméterfájl** legördülő listából válassza az paraméter fájlját. Egy alkalmazás paraméterfájl paraméter értékét a felhasználó által megadott az Alkalmazásjegyzék-fájl tartalmazza. Hozzáadása vagy módosítása egy paraméter, válassza ki a **szerkesztése** gombra. Adja meg vagy módosítsa a paraméter értéke a **paraméterek** rács. Amikor elkészült, válassza ki a **mentése** gombra.
   
    ![A ** szerkesztése paraméterek ** párbeszédpanel][2]
4. Használja a **az alkalmazás frissítése** jelölőnégyzetet, hogy adja meg, hogy ez közzététele művelet egy frissítés. Frissítés közzététele műveletek eltér a normál műveletek közzététele. Lásd: [Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md) eltérések listáját. Frissítési beállítások megadásához válassza ki a **beállítások konfigurálása** hivatkozásra. A frissítési paraméter-szerkesztő jelenik meg. Lásd: [konfigurálása a Service Fabric-alkalmazás frissítését](service-fabric-visualstudio-configure-upgrade.md) frissítési paraméterek tájékozódhat.
5. Válassza ki a **Manifest verzióit...** a gombra kattintva megtekintheti a **szerkesztése verziók** párbeszédpanel megnyitásához. Frissítenie kell alkalmazás és szolgáltatás verziók kerül sor történő frissítéshez. Lásd: [Service Fabric-alkalmazás frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md) megtudhatja, hogyan befolyásolja az alkalmazás és a service manifest verziók egy frissítési folyamat.
   
    ![A ** szerkesztése verziók ** párbeszédpanel][3]
   
    Ha az alkalmazás és szolgáltatás verziók szemantikai versioning 1.0.0 vagy numerikus értékek például 1.0.0.0 formátumban, válassza ki a **automatikus frissítése az alkalmazás és szolgáltatás verziók** lehetőséget. Ha ezt a beállítást, a szolgáltatás és alkalmazás verziószáma automatikusan frissülnek, amikor egy kódot, konfigurációs vagy adatok Csomagverzió frissül. Manuálisan szerkessze a verziók szeretne használni, ha a négyzet jelölésének Ez a funkció letiltásához.
   
   > [!NOTE]
   > Összes csomag-bejegyzéseket az aktor projekthez jelenik meg először a bejegyzéseket létrehozni a Service Manifest fájlt a projekt felépítéséhez.
   > 
   > 
6. Amikor végzett a szükséges beállítások megadása, válassza ki a **közzététel** gombra kattintva a kijelölt Service Fabric-fürt számára az alkalmazás közzétételére. A közzétételi folyamat a megadott beállítások érvényesek.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Egy tetszőleges fürt végpontja (beleértve az entitás-fürtök) közzététele
A Visual Studio közzététel élmény közzététele az Azure-előfizetésekkel társított távoli fürtökre van optimalizálva. Azonban úgy is közzététel a tetszőleges végpontot (például a Service Fabric-fürtök fél) a közzétételi profil XML közvetlen módosításával. Fent ismertetett három profilok közzététele alapértelmezett--által biztosított**Local.1Node.xml**, **Local.5Node.xml**, és **Cloud.xml**– de azonban nyugodtan megadhatnak a különböző környezetekhez tartozó további profilok létrehozásához. Például előfordulhat, hogy szeretne létrehozni a fél fürtök, lehet, hogy nevű közzétételi profil **Party.xml**.

Ha egy nem biztonságos fürthöz csatlakozik, van szükség a fürt csatlakozási végpont például `partycluster1.eastus.cloudapp.azure.com:19000`. Ebben az esetben a csatlakozási végpont a közzétételi profil hasonló lenne ehhez hasonló:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Ha egy védett fürthöz csatlakozik, is szüksége lesz arra, hogy az ügyféltanúsítványt a hitelesítéshez használandó helyi tároló részleteit. További részletekért lásd: [biztonságos kapcsolatokat a Service Fabric-fürt konfigurálása](service-fabric-visualstudio-configure-secure-connections.md).

  Ha a közzétételi profilt készen áll, hivatkozhasson rá a közzététel párbeszédpanelen alább látható módon.

  ![Új közzétételi profil közzé párbeszédpanel][4]

  Vegye figyelembe, hogy ebben az esetben az új közzétételi profil mutat egy alkalmazás alapértelmezett paraméter fájlt. Ez akkor megfelelő, ha a ugyanazon alkalmazás konfigurációja azon környezetek számára közzétenni kívánt. Ezzel szemben azokban az esetekben, ahol szeretné közzétenni kívánt minden környezet különböző konfigurációkkal rendelkező, akkor lenne értelme megfelelő alkalmazás paraméter-fájl létrehozásához.

## <a name="next-steps"></a>Következő lépések
A folyamatos integrációt környezetben közzétételi folyamat automatizálása, lásd: [beállíthat folyamatos integrációt a Service Fabric](service-fabric-set-up-continuous-integration.md).

[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
