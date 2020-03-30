---
title: A fürt megjelenítése az Azure Service Fabric Explorer használatával
description: A Service Fabric Explorer egy olyan alkalmazás, amely a Microsoft Azure Service Fabric-fürt felhőalapú alkalmazások és csomópontok vizsgálatára és kezelésére szolgál.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258186"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel

A Service Fabric Explorer (SFX) egy nyílt forráskódú eszköz az Azure Service Fabric-fürtök vizsgálatához és kezeléséhez. Service Fabric Explorer egy asztali alkalmazás Windows, macOS és Linux.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer letöltése

A Service Fabric Explorer asztali alkalmazásként való letöltéséhez használja az alábbi hivatkozásokat:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A Service Fabric Explorer asztali verziója több vagy kevesebb szolgáltatással rendelkezhet, mint a fürt támogatása. A teljes szolgáltatáskompatibilitás biztosítása érdekében visszaléphet a fürtbe telepített Service Fabric Explorer-verzióra.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>A Service Fabric Explorer futtatása a fürtből

A Service Fabric Explorer egy Service Fabric-fürt HTTP-felügyeleti végpontjában is található. Az SFX webböngészőben való elindításához keresse meg a fürt HTTP-kezelési végpontját bármely böngészőből – például https:\//clusterFQDN:19080.

Fejlesztői munkaállomás-beállítás esetén a Service Fabric Explorer t https://localhost:19080/Explorera helyi fürtön indíthatja el a rendszerre navigálva. Tekintse meg ezt a cikket, hogy [előkészítse a fejlesztői környezetet](service-fabric-get-started.md).

> [!NOTE]
> Ha a fürtön önaláírt tanúsítvány biztosít, a következő hibaüzenet jelenik meg a következő webböngészőből: "Ez a webhely nem biztonságos". Egyszerűen haladjon végig a legtöbb modern böngészőn a figyelmeztetés felülbírálásával. Éles környezetben a fürtöt köznapi névvel és hitelesítésszolgáltató által kiállított tanúsítvánnyal kell biztosítani. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Kapcsolódás Service Fabric-fürthöz
Service Fabric-fürthöz való csatlakozáshoz a fürtkezelési végpontra (FQDN/IP) és a HTTP-kezelési végpontportra (alapértelmezés szerint 19080) van szükség. Például\:https //mysfcluster.westus.cloudapp.azure.com:19080. Használja a "Csatlakozás a helyi állomáshoz" jelölőnégyzetet, hogy csatlakozzon egy helyi fürthöz a munkaállomáson.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
Szabályozhatja az ügyfél hozzáférést a Service Fabric-fürthöz tanúsítványokkal vagy az Azure Active Directory (AAD) használatával.

Ha biztonságos fürthöz próbál csatlakozni, akkor a fürt konfigurációjától függően ügyféltanúsítványt kell bemutatnia, vagy AAD használatával kell bejelentkeznie.

## <a name="understand-the-service-fabric-explorer-layout"></a>A Service Fabric Explorer elrendezésének ismertetése
A Service Fabric Intézőben a bal oldali fa használatával navigálhat. A fa gyökerében a fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazás és a csomópont állapotának összegzését.

![Service Fabric Explorer fürt irányítópultja][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>A fürt elrendezésének megtekintése
A Service Fabric-fürt csomópontjai a tartalék tartományok és a frissítési tartományok kétdimenziós rácsán keresztül kerülnek. Ez az elhelyezés biztosítja, hogy az alkalmazások hardverhibák és alkalmazásfrissítések esetén is elérhetők maradjanak. Az aktuális fürt lefektetésének módját a fürttérkép segítségével tekintheti meg.

![Service Fabric Explorer fürttérkép][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Alkalmazások és szolgáltatások megtekintése
A fürt két részfát tartalmaz: az egyik az alkalmazásokhoz, a másik a csomópontokhoz.

Az alkalmazásnézet segítségével navigálhat a Service Fabric logikai hierarchiájában: alkalmazások, szolgáltatások, partíciók és replikák.

Az alábbi példában a **MyApp** alkalmazás két szolgáltatásból áll: **a MyStatefulService** és a **WebService**. Mivel **a MyStatefulService** állapotalapú, egy elsődleges és két másodlagos replikával rendelkező partíciót tartalmaz. Ezzel szemben a WebSvcService állapot nélküli, és egyetlen példányt tartalmaz.

![Service Fabric Explorer alkalmazásnézet][sfx-application-tree]

A fa minden szintjén a fő ablaktábla az elemre vonatkozó információkat jelenít meg. Megtekintheti például egy adott szolgáltatás állapotát és verzióját.

![A Service Fabric Explorer essentials ablaktáblája][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>A fürt csomópontjainak megtekintése
A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton. Pontosabban láthatja, hogy mely replikák futnak jelenleg ott.

## <a name="actions"></a>Műveletek
A Service Fabric Explorer gyors anno a fürt öncsomópontjain, alkalmazásain és szolgáltatásaiban való műveletek meghívására szolgál.

Például egy alkalmazáspéldány törléséhez válassza ki az alkalmazást a bal oldali fából, majd válassza az Alkalmazás törlése **művelet parancsot.** > **Delete Application**

![Alkalmazás törlése a Service Fabric Intézőben][sfx-delete-application]

> [!TIP]
> Ugyanezeket a műveleteket az egyes elemek melletti három pontra kattintva hajthatja végre.
>
> A Service Fabric Exploreren keresztül elvégezhető minden művelet powershellen vagy REST API-n keresztül is elvégezhető az automatizálás engedélyezéséhez.
>
>

A Service Fabric Intéző segítségével is létrehozhat alkalmazáspéldányokat egy adott alkalmazástípushoz és -verzióhoz. Válassza ki az alkalmazás típusát a fanézetben, majd kattintson az **Alkalmazáspéldány létrehozása** hivatkozásra a kívánt verzió mellett a jobb oldali ablaktáblában.

![Alkalmazáspéldány létrehozása a Service Fabric Intézőben][sfx-create-app-instance]

> [!NOTE]
> A Service Fabric Explorer nem támogatja a paramétereket alkalmazáspéldányok létrehozásakor. Az alkalmazáspéldányok alapértelmezett paraméterértékeket használnak.
>
>

## <a name="event-store"></a>Eseménytároló
Az EventStore a platform által kínált szolgáltatás, amely a Service Fabric platformesemények et a Service Fabric Explorer ben és a REST API-n keresztül elérhető. Megtekintheti a pillanatkép nézet, hogy mi történik a fürtben az egyes entitások, például csomópont, szolgáltatás, alkalmazás és lekérdezés az esemény ideje alapján. Az EventStore áttekintése oldalon is olvashat bővebben az [EventStore áruházról.](service-fabric-diagnostics-eventstore.md)   

![EventStore (Rendezvény)][sfx-eventstore]

>[!NOTE]
>A Service Fabric 6.4-es verziójától. Az EventStore alapértelmezés szerint nincs engedélyezve, és az erőforrás-kezelő sablonban is engedélyezni kell.

>[!NOTE]
>A Service Fabric 6.4-es verziójától. az EventStore API-k csak az Azure-ban futó Windows-fürtök esetén érhetők el. Dolgozunk a portolás ezt a funkciót a Linux, valamint a mi önálló klaszterek.

## <a name="image-store-viewer"></a>Képtár-megjelenítő
A képtár-megjelenítő olyan szolgáltatás, amely a Natív képtároló használata esetén érhető el, amely lehetővé teszi a Képtároló aktuális tartalmának megtekintését, valamint a fájl- és mappaadatok betárolását, valamint a fájlok/ mappák eltávolítását.

![Service Fabric Explorer fürttérkép][sfx-imagestore]

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Service Fabric Explorer lehetővé teszi, hogy interfész [biztonsági mentés és visszaállítás](./service-fabric-reliable-services-backup-restore.md). Az SFX Biztonsági mentési és visszaállítási funkcióinak megtekintéséhez engedélyezni kell a speciális módot.

![Speciális mód engedélyezése][0]
 
A következő műveletek lehetségesek:

* Biztonságimásolat-házirend létrehozása, szerkesztése és törlése.
* Alkalmazás, szolgáltatás vagy partíció biztonsági mentésének engedélyezése és letiltása.
* Alkalmazás, szolgáltatás vagy partíció biztonsági mentésének felfüggesztése és folytatása.
* Partíció biztonsági másolatának aktiválása és nyomon követése.
* Indítsa el és kövesse nyomon a partíció visszaállítását.

A Biztonsági mentés és visszaállítás szolgáltatásról a [REST API referencia című témakörben](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)lehet további.
## <a name="next-steps"></a>További lépések
* [A Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric-alkalmazások központi telepítése a PowerShell használatával](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png