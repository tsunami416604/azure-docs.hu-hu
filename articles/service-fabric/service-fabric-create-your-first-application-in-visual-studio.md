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
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: f93298e6483fd8c9dfda835964aeebd1a430af69
ms.contentlocale: hu-hu
ms.lasthandoff: 07/15/2017

---

# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Az első Service Fabric Stateful Reliable Services-alkalmazás létrehozása C#-környezettel

Megtudhatja, hogyan helyezheti üzembe mindössze néhány perc alatt első .NET-es Service Fabric-alkalmazását Windows rendszeren. Ha elkészült, rendelkezni fog egy Reliable Services-alkalmazással futó helyi fürttel.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy [beállította a fejlesztőkörnyezetet](service-fabric-get-started.md). Ebbe beletartozik a Service Fabric SDK és a Visual Studio 2017 vagy 2015 telepítése is.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

Indítsa el a Visual Studiót **rendszergazdaként**.

Projekt létrehozása `CTRL`+`SHIFT`+`N` használatával

Az **Új projekt** párbeszédpanelen válassza a **Felhő > Service Fabric-alkalmazás** elemet.

Adja a **MyApplication** nevet az alkalmazásnak, majd kattintson az **OK** gombra.

   
![A Visual Studio Új projekt párbeszédpanelje][1]

A következő párbeszédpanelen bármilyen típusú Service Fabric-alkalmazást létrehozhat. Ebben a rövid útmutatóban válassza az **Állapotalapú szolgáltatás** lehetőséget.

Adja a **MyStatefulService** nevet a szolgáltatásnak, majd kattintson az **OK** gombra.

![A Visual Studio Új szolgáltatás párbeszédpanelje][2]


A Visual Studio létrehozza az alkalmazási projektet és az állapotalapú szolgáltatási projektet, és megjeleníti őket a Megoldáskezelőben.

![A Megoldáskezelő folytatja az alkalmazás létrehozását állapotalapú szolgáltatással][3]

Az alkalmazásprojekt (**MyApplication**) nem tartalmaz közvetlenül semmilyen kódot. Helyette számos szolgáltatási projektre hivatkozik. Ezenfelül három egyéb típusú tartalmat is tartalmaz:

* **Profilok közzététele**  
Más környezetekben való üzembe helyezésre szolgáló profilok.

* **Szkriptek**  
Az alkalmazás üzembe helyezéséhez/frissítéséhez szükséges PowerShell-szkript.

* **Alkalmazásdefiníció**  
Tartalmazza az *ApplicationPackageRoot* területen található ApplicationManifest.xml fájlt, amely leírja az alkalmazás összeállítását. A társított alkalmazásparaméter-fájlok az *ApplicationParameters* területen találhatók, és a környezetspecifikus paraméterek megadására használhatók. A Visual Studio kiválaszt alkalmazásparaméter-fájlt, amelyet egy adott környezetben való üzembe helyezéskor adott meg a kapcsolódó közzétételi profilban.
    
A szolgáltatási projekt tartalmának áttekintéséhez lásd: [Bevezetés a Reliable Services használatába](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Az alkalmazás üzembe helyezése és hibakeresése

Most, hogy már van egy alkalmazása, futtassa.

Nyomja le az `F5` billentyűt a Visual Studióban, hogy üzembe helyezze az alkalmazást a hibakereséshez.

>[!NOTE]
>Az alkalmazás első helyi történő üzembe helyezésekor a Visual Studio létrehoz egy helyi hibakeresési fürtöt. Ez eltarthat egy ideig. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

Amikor a fürt létrejött, értesítést kap a helyi fürt SDK-hoz tartozó rendszertálca-kezelő alkalmazásától.
   
![A helyifürt-rendszertálca értesítése][4]

Az alkalmazás elindításakor a Visual Studio automatikusan megjeleníti a **Diagnosztikai eseménynaplót**, ahol az Ön szolgáltatásainak nyomkövetési kimenetei tekinthetők meg.
   
![Diagnosztikai eseménynapló][5]

Az általunk használt állapotalapúszolgáltatás-sablon esetében egyszerűen a **MyStatefulService.cs** `RunAsync` metódusához tartozó számláló növekvő értékei jelennek meg.

Bontsa ki az egyik eseményt, hogy további részleteket tekinthessen meg, beleértve azt a csomópontot is, amelyben a kód fut. Ebben az esetben ez a \_Node\_2, de az Ön számítógépén ez eltérő lehet.
   
![A diagnosztikai eseménynapló részletei][6]

A helyi fürt egyetlen gépen üzemeltetett öt csomópontot tartalmaz. Éles környezetben minden egyes csomópont más fizikai vagy virtuális gépen üzemel. Állítsuk le a helyi fürt egyik csomópontját, hogy szimuláljuk egy gép elvesztését, és kipróbáljuk a Visual Studio hibakereső funkcióját.

A **Megoldáskezelő** ablakában nyissa meg a **MyStatefulService.cs** fájlt. 

Keresse meg a(z) `RunAsync` metódust, és az első sorában állítson be egy töréspontot.

![Töréspont az állapotalapú szolgáltatás RunAsync metódusában][7]

A **Service Fabric Explorer** eszköz elindításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt kezelése** lehetőséget.

![A Manage Local Cluster alkalmazásból indítsa el a Service Fabric Explorert.][systray-launch-sfx]

A [**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) vizuálisan is megjeleníti a fürtöket, beleértve a rajtuk üzembe helyezett alkalmazáskészletet és az őket felépítő fizikai csomópontokat is.

A bal oldali panelen bontsa ki a **Cluster > Nodes** (Fürt > Csomópontok) elemet, és keresse meg azt csomópont, amelyikben a kódja fut.

Kattintson az **Actions > Deactivate (Restart)** (Műveletek > Inaktiválás (Újraindítás)) elemre a számítógép-újraindítás szimulálásához.

![Csomópont leállítása a Service Fabric Explorerben][sfx-stop-node]

Pillanatnyilag a töréspont megjelenését tekintheti meg a Visual Studióban, mivel az egyik csomóponton korábban végzett számítása zökkenőmentesen átadja a feladatokat egy másiknak.


Ezután térjen vissza a Diagnosztikai eseménynaplóhoz, és vizsgálja meg az üzeneteket. A számláló értéke továbbra is növekszik, annak ellenére, hogy az események valójában egy másik csomópontról érkeznek.

![A diagnosztikai eseménynapló a feladatátvétel után][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>A helyi fürt törlése (nem kötelező)

Ne feledje, hogy ez a helyi fürt valós. A hibakereső leállításával eltávolítja az adott alkalmazáspéldányt, és törli az alkalmazástípus regisztrációját. A fürt futtatása azonban tovább folytatódik a háttérben. Ha felkészült a helyi fürt leállítására, erre többféle lehetőség is rendelkezésre áll.

### <a name="keep-application-and-trace-data"></a>Alkalmazás- és nyomkövetési adatok megtartása

A fürt leállításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt leállítása** lehetőséget.

### <a name="delete-the-cluster-and-all-data"></a>A fürt és minden adat törlése

A fürt eltávolításához kattintson a jobb gombbal a **Local Cluster Manager** rendszertálca-alkalmazásra, és válassza a **Helyi fürt eltávolítása** lehetőséget. 

Ha ezt a lehetőséget választja, a Visual Studio az alkalmazás legközelebbi futtatásakor ismét üzembe helyezi a fürtöt. Akkor válassza ezt a beállítást, ha egy ideig nem kívánja használni a helyi fürtöt, vagy ha erőforrásokat kíván felszabadítani.

## <a name="next-steps"></a>Következő lépések
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

