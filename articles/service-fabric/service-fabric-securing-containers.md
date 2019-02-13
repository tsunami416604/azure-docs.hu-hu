---
title: Tanúsítványok importálása egy tárolóba az Azure Service fabricen futó |} A Microsoft Docs
description: Ismerje meg, most már a tanúsítvány-fájlok importálása a Service Fabric-tároló szolgáltatást.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: twhitney, subramar
ms.openlocfilehash: deb8eacb1e9c55feba6b356eedc61ba57c3a6566
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110319"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Tanúsítvány fájl importálása a Service Fabric futó tárolók

A container Service egy tanúsítvány megadásával gondoskodhat. A Service Fabric lehetővé teszi a szolgáltatás hozzáférjen a tanúsítványhoz, amely a csomópontok egy Windows vagy Linux rendszerű fürt (5.7-es vagy újabb verzió) telepítve van a tárolóban. A tanúsítvány a a fürt összes csomópontjára a LocalMachine tanúsítványtárolóban kell telepíteni. A titkos kulcs megfelelő tanúsítványnak érhető el, elérhető és – a Windows - exportálhatónak kell lennie. A tanúsítvány információ az alkalmazásjegyzékben alatt a `ContainerHostPolicies` címke az alábbi kódrészletben látható módon:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

A Windows fürtök esetén az alkalmazás indításakor a futásidejű exportálja egy véletlenszerűen előállított jelszóval védett PFX-fájl minden egyes hivatkozott tanúsítvány és a hozzá tartozó titkos kulcs. A PFX és jelszó, osztályban, fájlokhoz a tárolóban, használja az alábbi környezeti változókat: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

A Linux-fürtök esetén (PEM) tanúsítványok másolja át az alakzatot a tároló X509StoreName által megadott tároló. A Linux rendszerre vonatkozó környezeti változókat a következők:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Azt is megteheti, ha már rendelkezik a tanúsítványok a szükséges formátumban, és szeretné-e férni a tárolóban, létrehozhat egy adat-csomag belül az alkalmazáscsomag és adja meg a következőket az alkalmazásjegyzék belül:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A container Service-ben vagy a folyamat feladata az tanúsítvány-fájlok importálása a tárolóba. A tanúsítvány importálásához használhatja `setupentrypoint.sh` parancsfájlok, vagy hajtsa végre a tároló folyamaton belül egyéni kódot. Itt látható mintakód a C# a PFX-fájl importálása:

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
A PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás, vagy más szolgáltatásokkal biztonságos kommunikáció hitelesítéséhez. Alapértelmezés szerint a fájlok ACLed csak a rendszer. ACL-t is, hogy a szolgáltatás által kért egyéb fiókokat.

A következő lépésben olvassa el a következő cikkeket:

* [Egy Windows-tároló üzembe helyezése a Service Fabric Windows Server 2016-on](service-fabric-get-started-containers.md)
* [Egy Docker-tároló üzembe helyezése a Service fabric Linux rendszeren](service-fabric-get-started-containers-linux.md)
