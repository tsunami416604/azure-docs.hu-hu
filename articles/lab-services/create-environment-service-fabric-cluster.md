---
title: Környezet létrehozása a Service Fabric-fürtön az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg a környezet egy önálló Service Fabric-fürt létrehozása és elindítása és leállítása a fürthöz ütemezések használatával.
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
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318788"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Önálló Service Fabric-fürtön az Azure DevTest Labs-környezet létrehozása
Ez a cikk információt nyújt az olyan környezetet hozhat létre az önálló Service Fabric-fürtön az Azure DevTest Labs szolgáltatásban. 

## <a name="overview"></a>Áttekintés
DevTest Labs szolgáltatásban is létrehozhat önálló tesztelési környezetek határozzák meg az Azure Resource Management-sablonokat. Ezekben a környezetekben egyaránt IaaS-erőforrások, például a virtuális gépek és PaaS-erőforrásokhoz, például Service Fabric tartalmaz. DevTest Labs lehetővé teszi egy környezetet a virtuális gépek kezelését azáltal, hogy a virtuális gépek vezérlő parancsokat. Ezek a parancsok lehetővé teszik, indítása vagy leállítása a virtuális gépek ütemezés szerint. Hasonlóképpen DevTest Labs is segíthet környezetben a Service Fabric-fürtök kezelése. Indítsa el, vagy állítsa le a Service Fabric-fürt egy környezetben, manuális vagy ütemezett keresztül.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása
Környezetek használata a DevTest Labs szolgáltatásban létrehozott Service Fabric-fürtök jönnek létre. Minden környezet egy Azure Resource Manager-sablon egy Git-adattárban határozza meg. A [nyilvános Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) DevTest Labs tartalmaz a Service Fabric-fürt létrehozása a Resource Manager-sablon a [ServiceFabric-fürt](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) mappát. 

1. Első lépésként labor létrehozása az Azure DevTest Labs a következő cikk utasításai szerint: [Labor létrehozása](devtest-lab-create-lab.md). Figyelje meg, hogy a **nyilvános környezetben** beállítás **a** alapértelmezés szerint. 
2. Győződjön meg arról, hogy a Service Fabric-szolgáltató regisztrálva van az előfizetéshez az alábbi lépéseket:
    1. Válassza ki **előfizetések** elemre a bal oldali navigációs menüben, majd válassza a **előfizetés**
    2. Az a **előfizetés** lapon jelölje be **erőforrás-szolgáltatók** a a **beállítások** szakaszban a bal oldali menüben. 
    3. Ha **Microsoft.ServiecFabric** nem regisztrált, válassza ki **regisztrálása**. 
3. Az a **fejlesztési és tesztelési labor** a tesztkörnyezethez, válassza a lap **+ Hozzáadás** az eszköztáron. 
    
    ![Hozzáadás gomb az eszköztáron](./media/create-environment-service-fabric-cluster/add-button.png)
3. Az a **vyberte bázi** lapon jelölje be **Service Fabric-fürt labor** a listában. 

    ![A listában válassza ki a Service Fabric-fürt Lab](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Az a **beállításainak konfigurálása** lapon, tegye a következőket: 
    1. Adjon meg egy **neve** a fürt **környezet**. Ez az az Azure-ban, amelyben a Service Fabric-fürtöt fog létrehozni az erőforráscsoport nevét. 
    2. Válassza ki a **operációs rendszer (OS)** a fürt virtuális gépek. Az alapértelmezett érték a következő: **Windows**.
    3. Adjon meg egy nevet a **rendszergazda** a fürt számára. 
    4. Adjon meg egy **jelszó** a rendszergazda számára. 
    5. Az a **tanúsítvány**, adja meg a tanúsítvány adatait a Base64-kódolású karakterláncként. Hozzon létre egy tanúsítványt, hajtsa végre a következő lépéseket:
        1. Töltse le a **létrehozás-ClusterCertificate.ps1** fájlt a [Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Azt is megteheti klónozza a tárházat a gépén. 
        2. Indítsa el a **PowerShellt**. 
        3. Futtassa a **ps1** fájlt, a parancs `.\Create-ClusterCertificate.ps1`. Töltse ki a tanúsítvánnyal kapcsolatos mezőket ezen az oldalon a szükséges információkat a Jegyzettömbben megnyitott szövegfájl láthatja. . 
    6. Adja meg a **jelszót a tanúsítványhoz**.
    7. Adja meg a **ujjlenyomat** a tanúsítványhoz.
    8. Válassza ki **Hozzáadás** a a **beállításainak konfigurálása** lapot. 

        ![Fürt-beállítások konfigurálása](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. A fürt létrehozása után megjelenik egy erőforráscsoportot a környezet az előző lépésben megadott névvel. Bontsa ki, láthatja, a Service Fabric-fürthöz. Ha az erőforráscsoport állapota megakadt **létrehozása**válassza **frissítése** az eszköztáron. A **Service Fabric-fürt** környezetben létrehoz egy 1 – NodeType csomóponttípust 5 csomópontos fürt Linux vagy Windows rendszeren.

    A következő példában **mysfabricclusterrg** a kifejezetten a Service Fabric-fürthöz létrehozott erőforráscsoport neve. Fontos megjegyezni, hogy az erőforráscsoport, amelyben létre belül önálló tesztlabor-környezetekben. Ez azt jelenti, hogy a sablon, amely meghatározza a környezetre, amely csak az újonnan létrehozott erőforráscsoport lévő erőforrások eléréséhez vagy [a Lab használható konfigurált virtuális hálózatok](devtest-lab-configure-vnet.md). A fenti példában az összes szükséges erőforrást hoz létre ugyanabban az erőforráscsoportban.

    ![Fürt létrehozása](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Indítsa el, vagy a fürt leállítása
Indítsa el, vagy állítsa le a fürtöt, vagy a fejlesztési és tesztelési labor oldaláról saját maga, vagy a DevTest Labs által biztosított Service Fabric-fürt oldaláról. 

### <a name="from-the-devtest-lab-page"></a>A DevTest Lab oldaláról
Indítsa el, vagy állítsa le a fürtöt a DevTest Lab oldalon a tesztkörnyezethez. 

1. Válassza ki **három pontra (...)**  for a Service Fabric-fürtöt az alábbi képen látható módon: 

    ![Elindíthatja és leállíthatja a parancsokat a fürt](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Két parancsot a helyi menü megjelenik **start** és **leállítása** a fürthöz. Az indítási parancsot a fürt összes csomópontja elindul. A leállítási parancsot leállítja a fürt összes csomópontján. Egy fürt leállítását követően a Service Fabric-fürthöz egy kész állapotban marad, de a csomópontok nem érhetők el addig, amíg az indítási parancsot újból van-e ki a lab-ben a fürtön.

    Néhány szempontot figyelembe kell venni a Service Fabric-fürtök használatával a tesztkörnyezetben. A Service Fabric-fürtöt, teljes körűen rehidratálási után újraindították a csomópontok egy kis ideig is eltarthat. Indítási leállítási adatainak megőrzéséhez adatokat egy felügyelt lemezt a virtuális géphez csatolt lehet menteni. Nincsenek teljesítményre gyakorolt hatása egy csatolt felügyelt lemez használata csak tesztkörnyezetekhez ajánlott. Ha nincs biztonsági adatok tárolására használt lemezre, majd adat elvész a leállítási parancs kiadásakor a fürtön.

### <a name="from-the-service-fabric-cluster-page"></a>A Service Fabric-fürt oldaláról 
Indítása és leállítása a fürt másik módja is van. 

1. A DevTest Lab oldalon fanézetben jelölje ki a Service Fabric-fürt. 

    ![Válassza ki a fürtöt](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Az a **Service Fabric-fürt** lap a fürt, az eszköztáron, indítása vagy leállítása a fürt látja parancsokat. 

    ![Indítása és leállítása a Service Fabric-fürt lapján parancsok](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Az automatikus indítási és automatikus leállítási ütemezés konfigurálása
Service Fabric-fürtöket is el vagy ütemezés szerint leállt. Ez hasonlít a labor virtuális gépek számára a felhasználói élményt. Pénz, alapértelmezés szerint a labor által meghatározott időben leáll minden fürt automatikusan létrehoz egy tesztkörnyezetben [leállítási házirend](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Adja meg, hogy a fürt is le kell állítani, vagy a fürt leállítása idő megadásával bírálhatja felül. 

![Automatikus indítás és automatikus leállítás meglévő ütemezések](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Az automatikus indítási ütemezés engedélyezése
Jelentkezzen be az indítási ütemezés, tegye a következőket:

1. Válassza ki **automatikusan elinduló** a bal oldali menü
2. Válassza ki **a** a **lehetővé teszik a service fabric-fürt automatikus indításra kell ütemezni**. Ez a lap csak akkor, ha a labor tulajdonos engedélyezte a felhasználók autostart, a virtuális gépek vagy a Service Fabric-fürtök engedélyezve van.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Automatikus csillag lap](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Automatikus leállítási beállítások konfigurálása 
A leállítási beállítások módosításához tegye a következőket:

1. Válassza ki **automatikus leállítási** a bal oldali menüben. 
2. Ezen a lapon is kikapcsolja az automatikus leállítás kiválasztásával **ki** a **engedélyezve**. 
3. Ha a kiválasztott **a** a **engedélyezve**, kövesse az alábbi lépéseket:
    1. Adja meg a **idő** a Leállítás.
    2. Adja meg a **időzóna** alkalommal. 
    3. Adja meg, hogy kívánja-e küldeni a DevTest Labs **értesítések** automatikus leállítása előtt. 
    4. Ha a kiválasztott **Igen** az értesítési beállítást is választja, adja meg a **Webhook URL-cím** és/vagy **e-mail-cím** értesítések küldéséhez. 
    5. Válassza az eszköztár **Save** (Mentés) elemét.

        ![Az automatikus leállítás lap](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>A Service Fabric-fürt csomópontok megtekintése
A Service Fabric-fürt lapján, látott a lépéseket a korábban csak a DevTest Labs-lapon. Nem jelenik meg, a csomópontok a fürtben. A fürttel kapcsolatos további információk megtekintéséhez kövesse az alábbi lépéseket:

1. A a **fejlesztési és tesztelési labor** lap a tesztkörnyezethez, válassza a **erőforráscsoport** a faszerkezetes nézetben a **a virtual machines** szakaszban.

    ![Erőforráscsoport kiválasztása](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Az a **erőforráscsoport** lapon láthatja a listában az erőforráscsoport összes erőforrását. Válassza ki a **Service Fabric-fürt** a listából. 

    ![Válassza ki a fürtöt a listában](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Megjelenik a **Service Fabric-fürt** lap a fürt számára. Ez az az oldallal, amely a Service Fabric biztosít. Láthatja a fürtök, például a csomópontok, típusok és egyéb kapcsolatos összes információt tartalmazza.

    ![Service Fabric-fürt kezdőlapja](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>További lépések
Részletes információ a környezetekről a következő cikkekben talál: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-use-public-environments.md)
