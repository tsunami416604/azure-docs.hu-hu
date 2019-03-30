---
title: Az Azure Service Fabric különálló csomag a Windows Server |} A Microsoft Docs
description: Leírás és a Windows Server az Azure Service Fabric különálló csomag tartalma.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: facdcd162826e6f77ace098391459cba00061c4f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661615"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>A Windows Server a Service Fabric különálló csomag tartalma
Az a [letöltött](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric különálló csomag, talál a következő fájlokat:

| **Fájl neve** | **Rövid leírás** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Egy PowerShell-parancsprogram, amely létrehozza a fürtöt ClusterConfig.json beállításainak használatával. |
| RemoveServiceFabricCluster.ps1 |Egy PowerShell-parancsprogram, amely eltávolítja egy fürt ClusterConfig.json beállításainak használatával. |
| AddNode.ps1 |Egy PowerShell-szkript csomópontot ad egy meglévő fürt az aktuális gépen telepítve. |
| RemoveNode.ps1 |Egy PowerShell-parancsprogram egy csomópontot távolíthat egy meglévő üzembe helyezett fürt erről a számítógépről. |
| CleanFabric.ps1 |Önálló Service Fabric-telepítési ki az aktuális számítógép tisztítása szükséges PowerShell-parancsfájlt. Előző MSI-telepítések el kell távolítani a saját társított uninstallers használatával. |
| TestConfiguration.ps1 |Egy elemzése az infrastruktúra a Cluster.json megadott PowerShell-szkript. |
| DownloadServiceFabricRuntimePackage.ps1 |A forgatókönyvekhez, ahol a központi telepítés gép nem csatlakozik az internethez a sávon kívüli legfrissebb futtatókörnyezet-csomag letöltéséhez használt PowerShell-szkriptet. |
| DeploymentComponentsAutoextractor.exe |A különálló csomag parancsfájlok által használt üzembe helyezési összetevők tartalmazó önkicsomagoló archív. |
| EULA_ENU.txt |A használatát a Microsoft Azure Service Fabric önálló Windows Server-csomag licencfeltételeit. Is [töltse le a végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?LinkID=733084) most. |
| Readme.txt |A kibocsátási megjegyzések és alapszintű telepítési utasításokat mutató hivatkozást. Ez a dokumentum utasításait egy része. |
| ThirdPartyNotice.rtf |Figyelje meg a külső szoftver, amely a csomagban. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |Igény szerinti gyűjtése és a nyomkövetési naplók feltöltése a Microsoft támogatási célból futtatva StandaloneLogCollector.exe. |
| Tools\ServiceFabricUpdateService.zip |Automatikus kód frissítés fürtök, amely nem rendelkezik internet-hozzáférés engedélyezésére szolgáló eszköz. További információk találhatók [Itt](service-fabric-cluster-upgrade-windows-server.md)|

**Sablonok** 

| **Fájl neve** | **Rövid leírás** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Egy fürt mintát tartalmazó konfigurációs fájlt egy nem biztonságos, három csomópontos egygépes (vagy virtuális gép) fejlesztési fürtöt, az egyes csomópontok állapotinformációit a fürt beállításait. |
| ClusterConfig.Unsecure.MultiMachine.json |Egy fürt mintát tartalmazó konfigurációs fájlt a beállításokat, beleértve az egyes gépek információkat a fürt nem biztonságos, több (vagy virtuális gépen) fürt számára. |
| ClusterConfig.Windows.DevCluster.json |Egy fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, három csomópontos egygépes (vagy virtuális gép) fejlesztési fürtöt, beleértve a információt, amely a fürt minden csomópont esetében összes beállítását. A fürt védik [Windows identitások](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Egy fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, több gép (vagy virtuális gép) fürt Windows biztonsági, beleértve az egyes gépek a biztonságos fürthöz lévő információkat használó összes beállítását. A fürt védik [Windows identitások](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Egy fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, három csomópontos egygépes (vagy virtuális gép) fejlesztési fürtöt, beleértve az egyes csomópontok állapotinformációit a fürt összes beállítását. A fürt használatával lett biztonságossá téve x509 tanúsítványokat. |
| ClusterConfig.x509.MultiMachine.json |Egy fürt mintát tartalmazó konfigurációs fájlt a biztonságos, több (vagy virtuális gépen) fürtöt, az egyes csomópontok állapotinformációit a biztonságos fürt összes beállítását. A fürt használatával lett biztonságossá téve x509 tanúsítványokat. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Egy fürt mintát tartalmazó konfigurációs fájlt a biztonságos, több (vagy virtuális gépen) fürtöt, az egyes csomópontok állapotinformációit a biztonságos fürt összes beállítását. A fürt használatával lett biztonságossá téve [csoportosan felügyelt szolgáltatásfiókok](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Fürt-konfigurációs minták
Fürt konfigurációs sablonok legfrissebb verzióit a GitHub-oldalon talál: [Önálló fürtkonfiguráció minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Független futtatókörnyezet-csomag
A legfrissebb futtatókörnyezet-csomag automatikusan letölti a fürt üzembe helyezése során [letöltési hivatkozás - Service Fabric-futtatókörnyezet – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Kapcsolódó témakörök
* [Önálló Azure Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric-fürtök biztonsági forgatókönyveit](service-fabric-windows-cluster-windows-security.md)
