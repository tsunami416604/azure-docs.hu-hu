---
title: "Az Azure Service Fabric önálló csomag a Windows Server |} Microsoft Docs"
description: "Leírás és a Windows Server az Azure Service Fabric önálló csomag tartalmát."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik
ms.openlocfilehash: 6a6bacedde04419449a061554d5ce9ad37259a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>A Windows Server a Service Fabric önálló csomag tartalma
Az a [letöltött](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric önálló csomag található a következő fájlokat:

| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Egy PowerShell-parancsfájlt, amely létrehozza a fürtöt, a beállítások használatával található nyomkövetési naplókat. |
| RemoveServiceFabricCluster.ps1 |Egy PowerShell-parancsfájlt, amely a fürt a beállításokat a művelet eltávolítja. |
| AddNode.ps1 |A PowerShell-parancsfájl a csomópont hozzáadása egy meglévő fürt az aktuális számítógépen telepített. |
| RemoveNode.ps1 |Egy PowerShell-parancsfájl a csomópont eltávolítása egy létező központi telepítése a fürt erről a számítógépről. |
| CleanFabric.ps1 |A Service Fabric telepítési ki az aktuális gépet önálló tisztításhoz PowerShell-parancsfájlt. Előző MSI-telepítések el kell távolítani a saját társított uninstallers használatával. |
| TestConfiguration.ps1 |Az infrastruktúra a Cluster.json megadott elemzése szükséges PowerShell-parancsfájlt. |
| DownloadServiceFabricRuntimePackage.ps1 |A legújabb futásidejű csomag helyzetekben, amikor a központi telepítés számítógép nem csatlakozik az internethez a sávon kívüli használt PowerShell-parancsfájlt. |
| DeploymentComponentsAutoextractor.exe |Az önálló csomag parancsfájlok által használt telepítési összetevők tartalmazó önkicsomagoló archív. |
| EULA_ENU.txt |A Microsoft Azure Service Fabric önálló Windows Server csomag használatára vonatkozó licencfeltételeket. Is [töltse le a végfelhasználói licencszerződés](http://go.microsoft.com/fwlink/?LinkID=733084) most. |
| Readme.txt |A kibocsátási megjegyzések és alapvető telepítési utasításokat mutató hivatkozást. Ez a dokumentum utasításait egy részét is. |
| ThirdPartyNotice.rtf |Figyelje meg a harmadik felek szoftvereivel, hogy a csomag megtalálható. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |Az igény szerinti összegyűjtésére és a nyomkövetési naplók feltöltése a Microsoft támogatási célra futtatva StandaloneLogCollector.exe. |
| Tools\ServiceFabricUpdateService.zip |Egy eszköz automatikus kód verziófrissítés fürtök, amelyekre vonatkozóan nincs engedélyezve az internet-hozzáférés engedélyezése. További részleteket talál [Itt](service-fabric-cluster-upgrade-windows-server.md)|

**Sablonok** 
| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |A fürt mintát tartalmazó konfigurációs fájlt a beállításokat egy nem biztonságos, három csomópontos egyszámítógépes (vagy a virtuális gép) fejlesztési fürthöz, a fürt minden csomópontja számára információkat is beleértve. |
| ClusterConfig.Unsecure.MultiMachine.json |A fürt mintát tartalmazó konfigurációs fájlt egy nem biztonságos, több számítógép (vagy virtuális gép) fürthöz, beleértve az egyes gépek információkat a fürt beállításait. |
| ClusterConfig.Windows.DevCluster.json |A fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, három csomópontos egyszámítógépes (vagy a virtuális gép) fejlesztési fürtöt, beleértve a információt, amely a fürt minden csomópontja számára az összes beállítás. A fürt használatával lett biztonságossá [Windows identitások](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |A fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, több számítógép (vagy virtuális gép) fürtöt Windows biztonsági a párbeszédeket minden olyan gép, amely a biztonságos tagja az összes beállítás. A fürt használatával lett biztonságossá [Windows identitások](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |A fürt mintát tartalmazó konfigurációs fájlt egy biztonságos, három csomópontos egyszámítógépes (vagy a virtuális gép) fejlesztési fürtöt, a fürt minden csomópontja számára információkat is beleértve az összes beállítás. A fürt használatával lett biztonságossá téve x509 tanúsítványokat. |
| ClusterConfig.x509.MultiMachine.json |A fürt mintát tartalmazó konfigurációs fájlt a biztonságos, több számítógép (vagy virtuális gép) fürt, beleértve az egyes csomópontok információkat a biztonságos fürt az összes beállítás. A fürt használatával lett biztonságossá téve x509 tanúsítványokat. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |A fürt mintát tartalmazó konfigurációs fájlt a biztonságos, több számítógép (vagy virtuális gép) fürt, beleértve az egyes csomópontok információkat a biztonságos fürt az összes beállítás. A fürt használatával lett biztonságossá téve [csoportosan felügyelt szolgáltatásfiókok](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Fürt konfigurációs minták
A GitHub-oldalon fürt konfigurációs sablonok legújabb verziói találhatók: [önálló fürt konfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Független futásidejű csomag
A legújabb futásidejű csomag automatikusan letölti a fürt telepítésekor [- Service Fabric-futtatókörnyezet - hivatkozás töltse le a Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Kapcsolódó
* [Önálló Azure Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* [A Service Fabric-fürt biztonsági forgatókönyvek](service-fabric-windows-cluster-windows-security.md)
