---
title: Service Fabric-fürtkörnyezet létrehozása az Azure DevTest Labsben
description: Megtudhatja, hogyan hozhat létre egy környezetet egy önálló Service Fabric-fürttel, és hogyan indíthatja el és állíthatja le a fürtet ütemezések használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170341"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Környezet létrehozása önálló Service Fabric-fürttel az Azure DevTest Labs ben
Ez a cikk az Azure DevTest Labs önálló Service Fabric-fürttel rendelkező környezet létrehozásáról nyújt tájékoztatást. 

## <a name="overview"></a>Áttekintés
A DevTest Labs önálló tesztkörnyezeteket hozhat létre az Azure Resource Management-sablonok által meghatározottak szerint. Ezek a környezetek iaaS-erőforrásokat is tartalmaznak, például a virtuális gépeket és a PaaS-erőforrásokat, például a Service Fabric-et. DevTest Labs lehetővé teszi, hogy a virtuális gépek kezelése egy környezetben a virtuális gépek vezérlésére szolgáló parancsok biztosításával. Ezek a parancsok lehetővé teszik a virtuális gépek ütemezés szerinti indítását vagy leállítását. Hasonlóképpen a DevTest Labs is segíthet a Service Fabric-fürtök környezetben kezelése. A Service Fabric-fürt indítása vagy leállítása egy környezetben manuálisan vagy ütemezés sel.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása
A Service Fabric-fürtök a DevTest Labs környezetei vel jönnek létre. Minden környezetet egy Git-tárházban található Azure Resource Manager-sablon határoz meg. A DevTest Labs [nyilvános Git-tárháza](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) tartalmazza az Erőforrás-kezelő sablont egy Service Fabric-fürt létrehozásához a [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) mappában. 

1. Először hozzon létre egy labort az Azure DevTest Labs-ben a következő cikkben található utasítások használatával: [Labor létrehozása.](devtest-lab-create-lab.md) Figyelje meg, hogy a **Nyilvános környezetek** beállítás alapértelmezés szerint be van **kapcsolva.** 
2. Az alábbi lépésekkel ellenőrizze, hogy a Service Fabric-szolgáltató regisztrálva van-e az előfizetéséhez:
    1. Válassza az **Előfizetések** lehetőséget a bal oldali navigációs menüben, és válassza ki az **Előfizetést**
    2. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** lehetőséget a bal oldali menü **Beállítások** szakaszában. 
    3. Ha a **Microsoft.ServiecFabric** nincs regisztrálva, válassza a **Regisztráció**lehetőséget. 
3. A **labor DevTest** lab lapján válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 
    
    ![A Hozzáadás gomb az eszköztáron](./media/create-environment-service-fabric-cluster/add-button.png)
3. A **Válasszon egy alaplapot,** válassza a **Service Fabric Lab Cluster** a listában. 

    ![Válassza ki a Service Fabric Lab Cluster elemet a listában](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. A **Beállítások konfigurálása** lapon tegye a következő lépéseket: 
    1. Adja meg a **fürtkörnyezet** **nevét.** Ez annak az azure-beli erőforráscsoportnak a neve, amelyben a Service Fabric-fürt létre fog jön. 
    2. Válassza ki a fürt virtuális gépek **operációs rendszerét .** Az alapértelmezett érték a **következő: Windows**.
    3. Adja meg a fürt **rendszergazdájának** nevét. 
    4. Adja meg a rendszergazda **jelszavát.** 
    5. A **tanúsítványhoz**adja meg a tanúsítvány adatait Base64 kódolású karakterláncként. Tanúsítvány létrehozásához tegye a következő lépéseket:
        1. Töltse le a **Create-ClusterCertificate.ps1** fájlt a [Git-tárházból.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) Másik lehetőségként klónozhatja a számítógépen lévő adattárat. 
        2. Indítsa el a **PowerShellt**. 
        3. Futtassa a **ps1** fájlt a paranccsal. `.\Create-ClusterCertificate.ps1` Megjelenik egy szövegfájl a jegyzettömbben, amely tartalmazza az ezen a lapon található, a tanúsítvánnyal kapcsolatos mezők kitöltéséhez szükséges információkat. . 
    6. Adja meg **a tanúsítvány jelszavát**.
    7. Adja meg a tanúsítvány **ujjlenyomatát.**
    8. Válassza a **Hozzáadás** gombot a **Beállítások konfigurálása** lapon. 

        ![Fürtbeállítások konfigurálása](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. A fürt létrehozása után megjelenik egy erőforráscsoport az előző lépésben megadott környezet nevével. Amikor bontja, megjelenik a Service Fabric-fürt benne. Ha az erőforráscsoport állapota a **Létrehozás**helyen van megmaradva, válassza az eszköztár **Frissítés parancsát.** A **Service Fabric fürtkörnyezet** e Linuxon vagy Windowson létrehoz egy 5 csomópontos 1 csomópontos fürtöt.

    A következő példában **a mysfabricclusterrg** a kifejezetten a Service Fabric-fürthöz létrehozott erőforráscsoport neve. Fontos megjegyezni, hogy a tesztkörnyezetek önálló az erőforráscsoport, amelyben jönnek létre. Ez azt jelenti, hogy a sablon, amely meghatározza a környezetet, amely csak az újonnan létrehozott erőforráscsoporton vagy [a tesztkörnyezet által használt virtuális hálózatokon](devtest-lab-configure-vnet.md)belüli erőforrásokhoz férhet hozzá. Ez a fenti minta létrehozza az összes szükséges erőforrást ugyanabban az erőforráscsoportban.

    ![Fürt létrehozva](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>A fürt indítása vagy leállítása
A fürt elindítható vagy leállíthatja magát a DevTest Lab lapról vagy a DevTest Labs által biztosított Service Fabric Cluster lapról. 

### <a name="from-the-devtest-lab-page"></a>A DevTest Lab lapról
A fürt indítása vagy leállítása a DevTest Lab lapon a laborban. 

1. Jelöljön ki **három pontot (...)** a Service Fabric-fürthöz az alábbi képen látható módon: 

    ![A fürt indítási és leállítási parancsai](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. A helyi menüben két parancs jelenik meg a fürt **elindításához** és **leállításához.** A start parancs elindítja a fürt összes csomópontját. A stop parancs leállítja a fürt összes csomópontját. A fürt leállítása után a Service Fabric-fürt maga is készenléti állapotban marad, de nem érhetők el csomópontok, amíg a start parancs újra ki nem adja a fürtben a laborban.

    Néhány szempontot figyelembe kell venni, ha egy Service Fabric-fürt egy tesztkörnyezetben. A service fabric-fürt teljes rehidratálása a csomópontok újraindítása után eltarthat egy ideig. Az adatok leállítástól az indításig történő megőrzéséhez az adatokat a virtuális géphez csatlakoztatott felügyelt lemezre kell menteni. A csatlakoztatott felügyelt lemez használata teljesítménybeli következményekkel jár, ezért csak tesztkörnyezetekben ajánlott. Ha az adattároláshoz használt lemez nem készül el, akkor az adatok elvesznek, amikor a stop parancsot kiadják a fürtön.

### <a name="from-the-service-fabric-cluster-page"></a>A Service Fabric fürtlapról 
Van egy másik módja a fürt elindításának vagy leállításának. 

1. Válassza ki a Service Fabric-fürta fanézetben a DevTest Lab lapon. 

    ![A fürt kiválasztása](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. A fürt **Service Fabric cluster** lapján az eszköztáron a fürt indítására vagy leállítására vonatkozó parancsok jelennek meg. 

    ![Parancsok indítása vagy leállítása a Service Fabric fürtlapon](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Automatikus indítási és automatikus leállítási ütemezés konfigurálása
A Service Fabric-fürtök is elindítható vagy leállítható ütemezés szerint. Ez a tapasztalat hasonló a virtuális gépek egy laborban. A tesztkörnyezetben létrehozott összes fürt alapértelmezés szerint automatikusan leáll a tesztkörnyezet [leállítási házirendje](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)által meghatározott időpontban. Felülbírálhatja, ha megadja, hogy a fürtöt le kell-e állítani, vagy megadhatja a fürt leállításának idejét. 

![Az automatikus indítás és az automatikus leállítás meglévő ütemezése](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Feliratkozás az automatikus indításütemezésre
Az indítási ütemezés hez tegye a következő lépéseket:

1. Válassza az **Automatikus indítás lehetőséget** a bal oldali menüben
2. Válassza **a** **Be lehetőséget, ha a szolgáltatásháló-fürt automatikus indításra való ütemezését engedélyezi.** Ez a lap csak akkor engedélyezett, ha a tesztkörnyezet tulajdonosa lehetővé tette a felhasználók számára a virtuális gépek vagy a Service Fabric-fürtök automatikus indítását.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Automatikus csillagoldal](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Automatikus leállítási beállítások konfigurálása 
A leállítási beállítások módosításához tegye a következőket:

1. Válassza a bal oldali menü **Automatikus leállítás** parancsát. 
2. Ezen a lapon letilthatja az automatikus leállítást, ha a **Ki** lehetőséget **választja az Engedélyezve lehetőséget.** 
3. Ha **az** **Engedélyezve**lehetőséget választotta, kövesse az alábbi lépéseket:
    1. Adja meg a leállítás **idejét.**
    2. Adja meg az **időzónát.** 
    3. Adja meg, hogy a DevTest Labs küldjön-e **értesítéseket** az automatikus leállítás előtt. 
    4. Ha az **Értesítési** beállításhoz az Igen lehetőséget választotta, adja meg a **Webhook URL-címét** és/vagy **e-mail címét** az értesítések küldéséhez. 
    5. Válassza az eszköztár **Save** (Mentés) elemét.

        ![Automatikus leállítás lap](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Csomópontok megtekintése a Service Fabric-fürtben
A Service Fabric fürtlap, amelyet a korábban látott lépésekben, a DevTest Labs lapra jellemző. Nem jeleníti meg a csomópontait a fürtben. A fürtről további információt az alábbi lépésekkel kapcsolatban talál:

1. A **labor DevTest** lab lapján válassza ki az **erőforráscsoportot** a Fa nézetben a **Saját virtuális gépek** szakaszban.

    ![Erőforráscsoport kiválasztása](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Az **Erőforráscsoport** lapon az erőforráscsoport összes erőforrása látható egy listában. Válassza ki a **Service Fabric-fürtöt** a listából. 

    ![Válassza ki a fürtöt a listában](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. A **fürt szolgáltatásháló-fürtlapja** látható. Ez az az oldal, amelyet a Service Fabric biztosít. A fürtökkel kapcsolatos összes információ, például csomópontok, csomóponttípusok stb.

    ![A Service Fabric cluster kezdőlapja](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>További lépések
A környezetekkel kapcsolatos részleteket az alábbi cikkekben találja: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata az Azure DevTest Labsben](devtest-lab-configure-use-public-environments.md)
