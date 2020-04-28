---
title: Azure Service Fabric önálló csomag a Windows Serverhez
description: A Windows Server rendszerhez készült Azure Service Fabric önálló csomagja leírása és tartalma.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451843"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>A Windows Server önálló csomagjának Service Fabric tartalma
A [letöltött](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric önálló csomag a következő fájlokat fogja megtalálni:

| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| CreateServiceFabricCluster. ps1 |Egy PowerShell-parancsfájl, amely létrehozza a fürtöt a ClusterConfig. JSON beállításainak használatával. |
| RemoveServiceFabricCluster. ps1 |Egy PowerShell-parancsfájl, amely eltávolítja a fürtöt a ClusterConfig. JSON beállításainak használatával. |
| Hozzáadási. ps1 |PowerShell-parancsfájl, amely egy csomópontnak egy meglévő üzembe helyezett fürthöz való hozzáadását végzi az aktuális gépen. |
| Eltávolítási. ps1 |PowerShell-parancsfájl egy csomópontnak egy meglévő telepített fürtből a jelenlegi gépről való eltávolításához. |
| CleanFabric. ps1 |PowerShell-parancsfájl egy önálló Service Fabric telepítésének a jelenlegi gépről való kitakarításához. A korábbi MSI-telepítéseket a saját társított eltávolításaik használatával kell eltávolítani. |
| TestConfiguration. ps1 |Egy PowerShell-parancsfájl a cluster. JSON fájlban megadott infrastruktúra elemzéséhez. |
| DownloadServiceFabricRuntimePackage. ps1 |A legújabb futásidejű csomag sávon kívüli letöltéséhez használt PowerShell-szkript olyan helyzetekben, amikor a központi telepítésű gép nincs csatlakoztatva az internethez. |
| DeploymentComponentsAutoextractor. exe |Az önálló csomag parancsfájljai által használt üzembe helyezési összetevőket tartalmazó önkicsomagoló Archívum. |
| EULA_ENU. txt |A Microsoft Azure Service Fabric önálló Windows Server-csomag használatára vonatkozó licencfeltételek. [A végfelhasználói licencszerződés másolatát most letöltheti](https://go.microsoft.com/fwlink/?LinkID=733084) . |
| Readme. txt |A kibocsátási megjegyzésekre és az alapszintű telepítési utasításokra mutató hivatkozás. Ez a dokumentum útmutatásának részhalmaza. |
| ThirdPartyNotice. rtf |A csomagban található harmadik féltől származó szoftverek megjegyzése. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector. exe, amely igény szerint a nyomkövetési naplók gyűjtésére és feltöltésére szolgál a Microsoftnak támogatási célból. |
| Tools\ServiceFabricUpdateService.zip |Egy eszköz, amely lehetővé teszi az automatikus kód frissítését olyan fürtökön, amelyek nem rendelkeznek internet-hozzáféréssel. További részletek [itt](service-fabric-cluster-upgrade-windows-server.md) találhatók|

**Sablonok** 

| **Fájlnév** | **Rövid leírás** |
| --- | --- |
| ClusterConfig. unsecure. DevCluster. JSON |Egy, a nem biztonságos, három csomópontos, egyszámítógépes (vagy virtuálisgép-) fejlesztési fürtre vonatkozó beállításokat tartalmazó fürtözött konfigurációs minta, amely tartalmazza a fürt egyes csomópontjainak információit. |
| ClusterConfig. unsecure. MultiMachine. JSON |A fürt konfigurációs mintája, amely egy nem védett, többszámítógépes (vagy virtuális gép) fürt beállításait tartalmazza, beleértve a fürtben lévő egyes gépek adatait is. |
| ClusterConfig. Windows. DevCluster. JSON |Egy, a biztonságos, három csomópontos, egyszámítógépes (vagy virtuálisgép-) fejlesztési fürtre vonatkozó összes beállítást tartalmazó fürtözött konfigurációs minta, amely tartalmazza a fürtben található egyes csomópontok információit. A fürt a [Windows-identitások](https://msdn.microsoft.com/library/ff649396.aspx)használatával védett. |
| ClusterConfig. Windows. MultiMachine. JSON |Egy, a Windows-biztonságot használó biztonságos, többszámítógépes (vagy virtuálisgép-) fürt összes beállítását tartalmazó fürtkonfiguráció, beleértve a biztonságos fürtben található minden egyes gép adatait. A fürt a [Windows-identitások](https://msdn.microsoft.com/library/ff649396.aspx)használatával védett. |
| ClusterConfig. x509. DevCluster. JSON |Egy, a biztonságos, három csomópontos, egyszámítógépes (vagy virtuálisgép-) fejlesztési fürtre vonatkozó összes beállítást tartalmazó fürtözött konfigurációs minta, amely tartalmazza a fürt egyes csomópontjainak információit. A fürt x509-tanúsítványok használatával van védve. |
| ClusterConfig. x509. MultiMachine. JSON |A fürt konfigurációs mintája, amely tartalmazza a biztonságos, többszámítógépes (vagy virtuálisgép-) fürt összes beállítását, beleértve a biztonságos fürt egyes csomópontjainak adatait is. A fürt x509-tanúsítványok használatával van védve. |
| ClusterConfig. gMSA. Windows. MultiMachine. JSON |A fürt konfigurációs mintája, amely tartalmazza a biztonságos, többszámítógépes (vagy virtuálisgép-) fürt összes beállítását, beleértve a biztonságos fürt egyes csomópontjainak adatait is. A fürt a [csoportosan felügyelt szolgáltatásfiókok](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx)használatával védett. |

## <a name="cluster-configuration-samples"></a>Fürt konfigurációs mintái
A fürt konfigurációs sablonjainak legújabb verziói a GitHub oldalon találhatók: [önálló fürt konfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Független futtatókörnyezeti csomag
A rendszer automatikusan letölti a legújabb futtatókörnyezetet a fürt üzembe helyezése során a [letöltési hivatkozás – Service Fabric futtatókörnyezet – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Kapcsolódó témakörök
* [Önálló Azure Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric fürt biztonsági forgatókönyvei](service-fabric-windows-cluster-windows-security.md)
