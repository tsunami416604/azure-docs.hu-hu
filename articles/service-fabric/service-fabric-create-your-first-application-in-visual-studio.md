---
title: "Azure Service Fabric Reliable Service-szolgáltatás létrehozása C#-környezettel"
description: "Azure Service Fabric-alapú Reliable Services-alkalmazás létrehozása, üzembe helyezése és hibakeresése a Visual Studio használatával."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 43f77a1a2e1bbe28bb646aa23c28c253c20e8dda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Az első Service Fabric állapotalapú Reliable Services-alkalmazás létrehozása C#-környezettel

Megtudhatja, hogyan helyezheti üzembe mindössze néhány perc alatt első .NET-es Azure Service Fabric-alkalmazását Windows rendszeren. Ha elkészült, rendelkezni fog egy Reliable Services-alkalmazással futó helyi fürttel.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md). Ebbe a folyamatba beletartozik a Service Fabric SDK és a Visual Studio 2017 vagy 2015 telepítése is.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Indítsa el a Visual Studiót rendszergazdaként.

2. Hozzon létre egy projektet a Ctrl+Shift+N billentyűkombinációval.

3. Az **Új projekt** párbeszédpanelen válassza a **Felhő** > **Service Fabric-alkalmazás** elemet.

4. Adja a **MyApplication** nevet az alkalmazásnak. Ezután kattintson az **OK** gombra.

   ![A Visual Studio Új projekt párbeszédpanelje][1]

5. A következő párbeszédpanelen bármilyen típusú Service Fabric-alkalmazást létrehozhat. Ebben a rövid útmutatóban válassza az **Állapotalapú szolgáltatás** lehetőséget.

6. Adja a szolgáltatásnak a **MyStatefulService** nevet. Ezután kattintson az **OK** gombra.

    ![A Visual Studio Új szolgáltatás párbeszédpanelje][2]

    A Visual Studio létrehozza az alkalmazás és az állapotalapú szolgáltatás projektjét, amelyeket megjelenít a Megoldáskezelőben.

    ![A Megoldáskezelő az alkalmazás és az állapotalapú szolgáltatás létrehozását követően][3]

    Az alkalmazásprojekt (**MyApplication**) nem tartalmaz semmilyen kódot. Helyette számos szolgáltatási projektre hivatkozik. Ezenfelül három más típusú tartalom is megtalálható benne:

    * **Profilok közzététele**  
    Más környezetekben való üzembe helyezésre szolgáló profilok.

    * **Szkriptek**  
    Az alkalmazás üzembe helyezéséhez vagy frissítéséhez szükséges PowerShell-szkriptek.

    * **Alkalmazásdefiníció**  
Tartalmazza az *ApplicationPackageRoot* területen található ApplicationManifest.xml fájlt, amely az alkalmazás összeállítását ismerteti. A társított alkalmazásparaméter-fájlok az *ApplicationParameters* területen találhatók, és a környezetspecifikus paraméterek megadására használhatók. A Visual Studio kiválasztja azt az alkalmazásparaméter-fájlt, amelyet a kapcsolódó közzétételi profilban adott meg.
    
A szolgáltatási projekt tartalmának áttekintéséhez lásd: [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Az alkalmazás üzembe helyezése és hibakeresése

Most, hogy már van egy alkalmazása, az alábbi lépésekkel helyezheti üzembe, futtathatja, és végezhet rajta hibakeresést.

1. Nyomja le az F5 billentyűt a Visual Studióban, hogy üzembe helyezze az alkalmazást a hibakereséshez.

    >[!NOTE]
    >Az alkalmazás első helyi történő üzembe helyezésekor a Visual Studio létrehoz egy helyi hibakeresési fürtöt. Ez eltarthat egy ideig. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

    Amikor a fürt létrejött, értesítést kap a helyi fürt SDK-hoz tartozó rendszertálca-kezelő alkalmazásától.
    
    ![A helyifürt-rendszertálca értesítése][4]

    Az alkalmazás elindítása után a Visual Studio automatikusan megjeleníti a Diagnosztikai eseménynaplót, ahol az Ön szolgáltatásainak nyomkövetési kimenetei tekinthetők meg.
    
    ![Diagnosztikai eseménynapló][5]

    >[!NOTE]
    >Az eseményeknek automatikusan el kell kezdeniük a nyomkövetést a Diagnosztikai eseménynaplóban. Ha azonban manuálisan kell konfigurálnia a nyomkövetést, először nyissa meg a **MyStatefulService** projektben található `ServiceEventSource.cs` fájlt. Másolja a `ServiceEventSource` osztály tetején található `EventSource` attribútum értékét. Az alábbi példában az eseményforrás neve `"MyCompany-MyApplication-MyStatefulService"`, ami az Ön esetében eltérő lehet.
>
    >![A szolgáltatáshoz tartozó eseményforrás nevének helye][service-event-source-name]


2. Ezután nyissa meg az **ETW-szolgáltatók** párbeszédpanelét. Kattintson a **diagnosztikai események** lapján található fogaskerék ikonra. Illessze be a kimásolt eseményforrásnevet az **ETW-szolgáltatók** párbeszédpaneljének beviteli mezőjébe. Ezután válassza az **Alkalmaz** gombot. Ezzel automatikusan elindítja az események követését.

    ![A diagnosztikai eseményforrás nevének beállítása][setting-event-source-name]

    A diagnosztikai események ablakában meg kell jelennie az eseményeknek.

    Az állapotalapú szolgáltatás sablonjánál a **MyStatefulService.cs** `RunAsync` metódusához tartozó számláló növekvő értékei jelennek meg.

3. Bontsa ki az egyik eseményt, hogy további részleteket tekinthessen meg, beleértve azt a csomópontot is, amelyben a kód fut. Ebben az esetben ez a **\_Node\_0,** de az Ön számítógépén ez eltérő lehet.
   
    ![A diagnosztikai eseménynapló részletei][6]

4. A helyi fürt egyetlen számítógépen lévő öt csomópontot tartalmaz. Éles környezetben minden egyes csomópont más fizikai vagy virtuális gépen üzemel. Állítsa le a helyi fürt egyik csomópontját, hogy szimulálja egy gép elvesztését, és kipróbálhassa a Visual Studio hibakereső funkcióját.

5. A **Megoldáskezelő** ablakában nyissa meg a **MyStatefulService.cs** fájlt. 

6. Keresse meg a `RunAsync` metódust, és az első sorában állítson be egy töréspontot.

    ![Töréspont az állapotalapú szolgáltatás RunAsync metódusában][7]

7. A Service Fabric Explorer eszköz elindításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt kezelése** lehetőséget.

    ![A Fabric Explorer elindítása a Local Cluster Managerből][systray-launch-sfx]

    A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vizuálisan is megjeleníti a fürtöket, beleértve a rajtuk üzembe helyezett alkalmazáskészletet és az őket felépítő fizikai csomópontokat.

8. A bal oldali panelen bontsa ki a **Cluster** > **Nodes** (Fürt > Csomópontok) elemet, és keresse meg azt csomópont, amelyikben a kódja fut. Ezt követően kattintson az **Actions** > **Deactivate (Restart)** (Műveletek > Inaktiválás (Újraindítás)) elemre a számítógép újraindításának szimulálásához.

    ![Csomópont leállítása a Service Fabric Explorerben][sfx-stop-node]

    Pillanatnyilag a töréspont megjelenését tekintheti meg a Visual Studióban, mivel az egyik csomóponton korábban végzett számítása zökkenőmentesen átadja a feladatokat egy másiknak.

9. Ezután térjen vissza a Diagnosztikai eseménynaplóhoz, és vizsgálja meg az üzeneteket. A számláló értéke továbbra is növekszik, annak ellenére, hogy az események valójában egy másik csomópontról érkeznek.

    ![A diagnosztikai eseménynapló a feladatátvétel után][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>A helyi fürt törlése (nem kötelező)

Ne feledje, hogy ez a helyi fürt valós. A hibakereső leállításával eltávolítja az adott alkalmazáspéldányt, és törli az alkalmazástípus regisztrációját. A fürt futtatása azonban tovább folytatódik a háttérben. Ha felkészült a helyi fürt leállítására, erre többféle lehetőség is rendelkezésre áll.

### <a name="keep-application-and-trace-data"></a>Alkalmazás- és nyomkövetési adatok megtartása

A fürt leállításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt leállítása** lehetőséget.

### <a name="delete-the-cluster-and-all-data"></a>A fürt és minden adat törlése

A fürt eltávolításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt eltávolítása** lehetőséget. 

Ha ezt a lehetőséget választja, a Visual Studio az alkalmazás legközelebbi futtatásakor ismét üzembe helyezi a fürtöt. Akkor válassza ezt a beállítást, ha egy ideig nem kívánja használni a helyi fürtöt, vagy ha erőforrásokat szeretne felszabadítani.

## <a name="next-steps"></a>További lépések
További információk a [Reliable Services](service-fabric-reliable-services-introduction.md)-szolgáltatásokról.
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
