---
title: "Azure Service Fabric tároló biztonsági |} Microsoft Docs"
description: "Ismerje meg, most tároló szolgáltatások biztosításához."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 7c18c2b3b0d271f2dbe4f247c132850b49d8f1d9
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="container-security"></a>Tároló biztonsági

A Service Fabric lehetővé teszi a szolgáltatások eléréséhez a Windows vagy Linux rendszerű fürt (5.7-es vagy újabb verzió) csomópontján telepített tanúsítvány tárolója belül. Emellett a Service Fabric is támogatja csoportosan felügyelt szolgáltatásfiók (csoportosan felügyelt szolgáltatásfiókok) Windows tárolók. 

## <a name="certificate-management-for-containers"></a>Az tárolókat Tanúsítványkezelő

A tároló szolgáltatások tanúsítvány megadásával biztosíthatja. A tanúsítvány telepítenie kell a LocalMachine a fürt összes csomópontján. A tanúsítvány információkat az alkalmazás jegyzékében alatt a `ContainerHostPolicies` címke az alábbi kódrészletben látható módon:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Az windows-fürtök esetén az alkalmazás indításakor a futtatókörnyezet beolvassa a tanúsítványokat, és a PFX-fájlt, és minden tanúsítvány jelszava. A PFX-fájlt és a jelszó belül a tárolóhely a következő környezeti változók érhetők el: 

* **Certificates_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificates_ServicePackageName_CodePackageName_CertName_Password**

Linux-fürtök esetén a certificates(PEM) egyszerűen kerülnek keresztül, a tároló X509StoreName által megadott áruházból. A megfelelő környezeti változók linux rendszeren a következők:

* **Certificates_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey**

Azt is megteheti, ha már a tanúsítványok szükséges formájában és egyszerűen szeretné-e férni a tároló belül, létrehozhat egy adatcsomag belül az alkalmazáscsomag és adja meg a következőket az alkalmazásjegyzék belül:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A tároló szolgáltatás vagy folyamat feladata a tanúsítvány fájlok importálása a tárolóba. A tanúsítvány importálásához használhatja `setupentrypoint.sh` a parancsfájlok, vagy hajtsa végre a tároló folyamat egyéni kódot. A következő példakód C# nyelven íródtak a PFX-fájl importálása:

```c#
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


## <a name="set-up-gmsa-for-windows-containers"></a>Windows-tárolók csoportosan felügyelt szolgáltatásfiók beállítása

Csoportosan felügyelt szolgáltatásfiók (felügyelt szolgáltatásfiókok. csoport), a hitelesítő adatok megadását fájlját beállítása (`credspec`) helyezkedik el a fürt összes csomópontján. A fájl az összes olyan csomóponton, a Virtuálisgép-bővítmény használatával lehet másolni.  A `credspec` fájl tartalmaznia kell a csoportosan felügyelt fiók adatait. További információ a `credspec` fájl című [szolgáltatásfiókok](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). A hitelesítő adatok megadását és a `Hostname` címke meg van határozva a az alkalmazás jegyzékében. A `Hostname` címke meg kell egyeznie a tárolóban fut, a csoportosan felügyelt szolgáltatásfiók fióknevet.  A `Hostname` címke lehetővé teszi, hogy a tárolót, hogy hitelesítse magát a Kerberos-hitelesítés tartományban más szolgáltatásokhoz.  Adja meg a minta a `Hostname` és a `credspec` az alkalmazás a jegyzékben megjelenik-e a a következő kódrészletet:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Következő lépések

* [A Service Fabric Windows Server 2016 egy Windows-tároló telepítése](service-fabric-get-started-containers.md)
* [Telepítse a Service Fabric Linux egy Docker-tároló](service-fabric-get-started-containers-linux.md)
