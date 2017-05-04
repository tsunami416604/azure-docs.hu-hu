---
title: "Önálló Azure Service Fabric-fürt telepítése | Microsoft Docs"
description: "Hozzon létre egy önálló fejlesztési fürtöt három csomóponttal, amelyek ugyanazon a számítógépen futnak. A telepítés befejezése után készen áll majd egy többgépes fürt létrehozására."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a96150a6a7f83d1c220e0450f7704634a1f6ed1b
ms.lasthandoff: 04/27/2017


---

# <a name="create-your-first-service-fabric-standalone-cluster"></a>Az első önálló Service Fabric-fürt létrehozása
Bármilyen, Windows Server 2012 R2 vagy Windows Server 2016 rendszerű, helyszíni vagy a felhőben futó virtuális gépen vagy számítógépen létrehozhat önálló Service Fabric-fürtöt. A rövid útmutató segítségével csupán pár perc alatt létrehozhat egy önálló fejlesztési fürtöt.  Amikor végzett, egy egyetlen számítógépen futó, három csomópontot tartalmazó fürttel fog rendelkezni, amelyre appokat telepíthet.

## <a name="before-you-begin"></a>Előkészületek
A Service Fabric egy telepítőcsomagot biztosít az önálló Service Fabric-fürtök telepítéséhez.  [Töltse le a telepítőcsomagot](http://go.microsoft.com/fwlink/?LinkId=730690).  Tömörítse ki a telepítőcsomagot annak a számítógépnek vagy virtuális gépnek az egyik mappájába, ahová a fejlesztési fürtöt telepíteni kívánja.  A telepítőcsomag tartalmának részletes leírását [itt](service-fabric-cluster-standalone-package-contents.md) találja.

A fürt üzembe helyezését és konfigurálását végző fürtrendszergazdának rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. A Service Fabric tartományvezérlőn nem telepíthető.

## <a name="validate-the-environment"></a>A környezet érvényesítése
Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>A fürt létrehozása
Több fürtkonfigurációs mintafájl is települ a telepítőcsomaggal. A *ClusterConfig.Unsecure.DevCluster.json* a legegyszerűbb fürtkonfiguráció: egy nem védett, egyetlen számítógépen futó, három csomóponttal rendelkező fürt.  A többi konfigurációs fájl X.509 tanúsítványokkal vagy a Windows biztonsági szolgáltatásaival védett egy- vagy többgépes fürtöket ír le.  Ehhez az oktatóanyaghoz az alapértelmezett konfigurációs beállítások egyikét sem kell módosítania, csak át kell tekintenie a konfigurációs fájlt, és meg kell ismerkednie a beállításokkal.  A **csomópontok** szakasz ismerteti a fürt három csomópontját: név, IP-cím, [csomóponttípus, tartalék tartomány és frissítési tartomány](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A **tulajdonságok** szakasz definiálja a fürt [biztonságát, megbízhatósági szintjét, diagnosztikai gyűjteményét és a benne lévő csomópontok típusait](service-fabric-cluster-manifest.md#cluster-properties).

Ez a fürt nem biztonságos.  Bárki csatlakozhat hozzá névtelenül és végrehajthat kezelési műveleteket, ezért az üzemben lévő fürtöket mindig X.509 tanúsítványok vagy a Windows rendszerbiztonság használatával kell védeni.  A biztonság konfigurálására csak a fürt létrehozásakor van lehetőség, a fürt létrehozása után már nem lehet engedélyezni.  A Service Fabric-fürtök védelmével kapcsolatos további tudnivalókért lásd: [Fürt biztonságossá tétele](service-fabric-cluster-security.md).  

A három csomóponttal rendelkező fejlesztési fürt létrehozásához futtassa a *CreateServiceFabricCluster.ps1* szkriptet egy rendszergazdai PowerShell-munkamenetből:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

A fürt létrehozásakor a rendszer automatikusan letölti és telepíti a Service Fabric futtatókörnyezet-csomagot.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
A három csomópontot tartalmazó fejlesztési fürt most már üzemel. A ServiceFabric PowerShell-modul a futtatókörnyezettel együtt települ.  A fürt futását ugyanarról a számítógépről vagy egy másik olyan számítógépről is ellenőrizheti, amelyen van Service Fabric-futtatókörnyezet.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) parancsmag kiépít egy kapcsolatot a fürttel.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
A fürtökhöz való csatlakozást bemutató egyéb példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md). Miután csatlakozott a fürthöz, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) parancsmaggal listázza a fürtben lévő csomópontokat és az egyes csomópontok állapotinformációit. A **HealthState** tulajdonságnak *OK* értékűnek kell lennie minden csomópont esetében.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.5.216.0   0                     Up 03:00:07   00:00:00              Ok
                     vm1      localhost       NodeType1 5.5.216.0   0                     Up 03:00:02   00:00:00              Ok
                     vm0      localhost       NodeType0 5.5.216.0   0                     Up 03:00:01   00:00:00              Ok
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  A Service Fabric Explorer a fürtben futó szolgáltatás, amelyet böngészőből érhet el a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) címen. 

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>A fürt eltávolítása
Egy fürt eltávolításához futtassa a *RemoveServiceFabricCluster.ps1* PowerShell-szkriptet a csomag mappájából, és adja meg a JSON-konfigurációs fájl elérési útját. Megadhatja a törlés naplójának mentési helyét is.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Ha törölni kívánja a Service Fabric-futtatókörnyezetet a számítógépről, futtassa a következő PowerShell-szkriptet a csomag mappájából.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Következő lépések
Most, hogy telepített egy önálló fejlesztési fürtöt, tekintse meg az alábbi cikkeket:
* [Telepíthet egy többgépes önálló fürtöt](service-fabric-cluster-creation-for-windows-server.md), és engedélyezheti a védelmet.
* [Appokat helyezhet üzembe a PowerShell használatával](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png

