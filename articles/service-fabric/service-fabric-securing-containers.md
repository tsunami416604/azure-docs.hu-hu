---
title: Tanúsítványok importálása az Azure Service Fabric-on futó tárolóba | Microsoft Docs
description: Ismerkedjen meg a tanúsítványfájl Service Fabric Container Service-be történő importálásával.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 496d47c88636b11375d97cc4bc50efd9d468d645
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170468"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Tanúsítványfájl importálása Service Fabricon futó tárolóba

Egy tanúsítvány megadásával biztonságossá teheti a tároló szolgáltatásokat. A Service Fabric a tárolón belüli szolgáltatások számára biztosít olyan tanúsítványt, amely a Windows vagy Linux rendszerű fürt csomópontjain telepített tanúsítványokhoz fér hozzá (5,7-es vagy újabb verzió). A tanúsítványt a fürt összes csomópontján lévő LocalMachine területen lévő tanúsítványtárolóba kell telepíteni. A tanúsítványnak megfelelő titkos kulcsnak elérhetőnek, elérhetőnek és Windows-exportálható kell lennie. A tanúsítvány információi az alkalmazás jegyzékfájljában, a `ContainerHostPolicies` címkében jelennek meg az alábbi kódrészletben látható módon:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows-fürtök esetén az alkalmazás indításakor a futtatókörnyezet minden hivatkozott tanúsítványt és a hozzá tartozó titkos kulcsot exportál egy PFX-fájlba, amelyet véletlenszerűen generált jelszóval kell védeni. A PFX-és jelszavas fájlok a tárolón belül elérhetők a következő környezeti változók használatával: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux-fürtök esetében a tanúsítványok (PEM) át lesznek másolva a X509StoreName által megadott tárolóból a tárolóba. A Linuxon a megfelelő környezeti változók a következők:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Ha már rendelkezik a tanúsítványokkal a szükséges űrlapon, és a tárolón belül szeretné elérni azt, létrehozhat egy adatcsomagot az alkalmazáscsomag belül, és a következőket adhatja meg az alkalmazási jegyzékfájlban:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A tároló szolgáltatás vagy folyamat feladata a tanúsítványfájl tárolóba való importálása. A tanúsítvány importálásához `setupentrypoint.sh` parancsfájlokat használhat, vagy egyéni kódot futtathat a tároló folyamatán belül. Az alábbi mintakód a PFX C# -fájl importálásához használható:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Ez a PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás hitelesítésére vagy más szolgáltatásokkal való biztonságos kommunikációra. Alapértelmezés szerint a fájlok csak a rendszer ACLed. A szolgáltatás által igénybe veheti az ACL-t más fiókokba.

Következő lépésként olvassa el a következő cikkeket:

* [Windows-tároló üzembe helyezése Service Fabric Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése Linuxon Service Fabric](service-fabric-get-started-containers-linux.md)
