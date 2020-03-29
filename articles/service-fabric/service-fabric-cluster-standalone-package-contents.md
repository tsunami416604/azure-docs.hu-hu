---
title: Az Azure Service Fabric önálló csomagja a Windows Server rendszerhez
description: A Windows Server Azure Service Fabric önálló csomagjának leírása és tartalma.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451843"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>A Windows Server szolgáltatásfabric önálló csomagjának tartalma
A [letöltött](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric önálló csomagban a következő fájlokat találja:

| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Egy PowerShell-parancsfájl, amely a ClusterConfig.json beállításaival hozza létre a fürtöt. |
| RemoveServiceFabricCluster.ps1 |Egy PowerShell-parancsfájl, amely eltávolítja a fürta fürt config.json beállításait. |
| AddNode.ps1 |PowerShell-parancsfájl egy csomópont hozzáadása egy meglévő üzembe helyezett fürthöz az aktuális gépen. |
| EltávolításNode.ps1 |PowerShell-parancsfájl egy csomópont eltávolításához egy meglévő üzembe helyezett fürtből az aktuális gépről. |
| CleanFabric.ps1 |PowerShell-parancsfájl egy önálló Service Fabric-telepítés tisztításához az aktuális gépről. A korábbi MSI-telepítéseket el kell távolítani a saját társított eltávolítóikkal. |
| TestConfiguration.ps1 |PowerShell-parancsfájl a Cluster.jsonban megadott infrastruktúra elemzéséhez. |
| DownloadServiceFabricRuntimePackage.ps1 |A PowerShell-parancsfájl a legújabb futásidejű csomag sávon kívül letöltéséhez, olyan esetekben, amikor a központi üzembe helyező gép nem csatlakozik az internethez. |
| DeploymentComponentsAutoextractor.exe |Önkicsomagoló archívum, amely az önálló csomagparancsfájlok által használt központi telepítési összetevőket tartalmazza. |
| EULA_ENU.txt |A Microsoft Azure Service Fabric önálló Windows Server-csomag használatának licencfeltételei. Most [már letöltheti a eula egy példányát.](https://go.microsoft.com/fwlink/?LinkID=733084) |
| Readme.txt |A kibocsátási megjegyzésekre és az alapvető telepítési utasításokra mutató hivatkozás. Ez a dokumentum utasításainak egy része. |
| Harmadik félértesítés.rtf |Értesítés a csomagban lévő, harmadik féltől származó szoftverekről. |
| Eszközök\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, amely igény szerint fut, hogy összegyűjtse és feltöltse a nyomkövetési naplókat a Microsoft nak támogatási célból. |
| Eszközök\ServiceFabricUpdateService.zip |Az internet-hozzáféréssel nem rendelkező fürtök automatikus kódfrissítésének engedélyezésére szolgáló eszköz. További részletek [itt](service-fabric-cluster-upgrade-windows-server.md) találhatók|

**Sablonok** 

| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Fürtkonfigurációs mintafájl, amely egy nem biztonságos, hárompontos, egygépes (vagy virtuálisgépes) fejlesztői fürt beállításait tartalmazza, beleértve a fürt egyes csomópontjainak adatait is. |
| ClusterConfig.Unsecure.MultiMachine.json |Fürtkonfigurációs mintafájl, amely egy nem biztonságos, többgépes (vagy virtuálisgép-) fürt beállításait tartalmazza, beleértve a fürt egyes gépeire vonatkozó információkat is. |
| FürtConfig.Windows.DevCluster.json |Fürtkonfigurációs mintafájl, amely egy biztonságos, hárompontos, egygépes (vagy virtuálisgépes) fejlesztői fürt összes beállítását tartalmazza, beleértve a fürtben lévő csomópontok adatait is. A fürt [windowsos identitások](https://msdn.microsoft.com/library/ff649396.aspx)használatával védett. |
| ClusterConfig.Windows.MultiMachine.json |Fürtkonfigurációs mintafájl, amely a Windows biztonságával egy biztonságos, többgépes (vagy virtuális gépes) fürt összes beállítását tartalmazza, beleértve a biztonságos fürtben lévő gépek adatait is. A fürt [windowsos identitások](https://msdn.microsoft.com/library/ff649396.aspx)használatával védett. |
| ClusterConfig.x509.DevCluster.json |Fürtkonfigurációs mintafájl, amely egy biztonságos, hárompontos, egygépes (vagy virtuálisgépes) fejlesztői fürt összes beállítását tartalmazza, beleértve a fürt egyes csomópontjainak adatait is. A fürt x509-es tanúsítványokkal védett. |
| ClusterConfig.x509.MultiMachine.json |Fürtkonfigurációs mintafájl, amely a biztonságos, többgépes (vagy virtuálisgép-) fürt összes beállítását tartalmazza, beleértve a biztonságos fürt egyes csomópontjainak adatait is. A fürt x509-es tanúsítványokkal védett. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Fürtkonfigurációs mintafájl, amely a biztonságos, többgépes (vagy virtuálisgép-) fürt összes beállítását tartalmazza, beleértve a biztonságos fürt egyes csomópontjainak adatait is. A fürt a [csoportkezelt szolgáltatásfiókok](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx)használatával védett. |

## <a name="cluster-configuration-samples"></a>Fürtkonfigurációs minták
A fürtkonfigurációs sablonok legújabb verziói a GitHub-lapon találhatók: [Önálló fürtkonfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Független futásidejű csomag
A rendszer automatikusan letölti a legújabb futásidejű csomagot a fürt telepítési ideje során [a Letöltési linkről – Service Fabric futásidő - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Kapcsolódó témakörök
* [Önálló Azure Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* [A Service Fabric fürtjének biztonsági forgatókönyvei](service-fabric-windows-cluster-windows-security.md)
