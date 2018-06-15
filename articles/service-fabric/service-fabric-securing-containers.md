---
title: Tanúsítványok importálása egy tárolóba, Azure Service Fabric futó |} Microsoft Docs
description: Ismerje meg, ha folytatni szeretné az tanúsítvány-fájlok importálása a Service Fabric-tároló szolgáltatás.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208216"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Tanúsítványfájl importálása egy tárolóba, a Service Fabric fut

A tároló szolgáltatások tanúsítvány megadásával biztosíthatja. A Service Fabric lehetővé teszi a szolgáltatások eléréséhez a Windows vagy Linux rendszerű fürt (5.7-es vagy újabb verzió) csomópontján telepített tanúsítvány tárolója belül. A tanúsítvány telepítenie kell a LocalMachine a fürt összes csomópontján. A tanúsítvány információkat az alkalmazás jegyzékében alatt a `ContainerHostPolicies` címke az alábbi kódrészletben látható módon:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Az Windows-fürtök esetén az alkalmazás indításakor a futtatókörnyezet beolvassa a tanúsítványokat, és a PFX-fájlt, és minden tanúsítvány jelszava. A PFX-fájlt és a jelszó belül a tárolóhely a következő környezeti változók érhetők el: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux-fürtök esetén (PEM) tanúsítványok, a tároló X509StoreName által megadott tárolóból kerülnek keresztül. A megfelelő környezeti változók Linux rendszeren a következők:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Azt is megteheti, ha már rendelkezik a tanúsítványok szükséges formájában, és szeretnének hozzáférni a belül a tároló, létrehozhat egy adatcsomag belül az alkalmazáscsomag és adja meg a következőket az alkalmazásjegyzék belül:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A tároló szolgáltatás vagy folyamat feladata a tanúsítvány fájlok importálása a tárolóba. A tanúsítvány importálásához használhatja `setupentrypoint.sh` a parancsfájlok, vagy hajtsa végre a tároló folyamat egyéni kódot. Itt van a példakód C# PFX-fájl importálása:

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
A PFX-tanúsítvány az alkalmazás vagy szolgáltatás vagy egyéb szolgáltatások biztonságos kommunikációt hitelesítésére is használható. Alapértelmezés szerint a fájlok ACLed csak a rendszer. Hozzáférés-vezérlési lista beállíthatja úgy, hogy a szolgáltatás által megkövetelt más fiókokat.

A következő lépésben olvassa el a következő cikkeket:

* [A Service Fabric Windows Server 2016 egy Windows-tároló telepítése](service-fabric-get-started-containers.md)
* [Telepítse a Service Fabric Linux egy Docker-tároló](service-fabric-get-started-containers-linux.md)
