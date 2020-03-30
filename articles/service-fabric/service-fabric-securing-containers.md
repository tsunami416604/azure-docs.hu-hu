---
title: Tanúsítványok importálása tárolóba
description: Ismerje meg most, hogy importálja a tanúsítványfájlokat egy Service Fabric tárolószolgáltatásba.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614111"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Tanúsítványfájl importálása a Service Fabric en futó tárolóba

A tárolószolgáltatások tanúsítvány megadásával biztosíthatók. A Service Fabric egy olyan mechanizmust biztosít a tárolón belüli szolgáltatások számára, amelyek egy Windows vagy Linux-fürt (5.7-es vagy újabb verzió) csomópontjaira telepített tanúsítványok eléréséhez érhetők el. A tanúsítványt a localmachine alatti tanúsítványtárolóban kell telepíteni a fürt összes csomópontján. A tanúsítványnak megfelelő személyes kulcsnak elérhetőnek, hozzáférhetőnek és - Windows rendszeren - exportálhatónak kell lennie. A tanúsítvány adatai az alkalmazás jegyzékfájljában a címke alatt jelennek meg, amint `ContainerHostPolicies` azt a következő kódrészlet mutatja:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows-fürtök esetén az alkalmazás indításakor a futásidejű minden hivatkozott tanúsítványt és a hozzá tartozó személyes kulcsot egy PFX-fájlba exportál, véletlenszerűen generált jelszóval biztosítva. A PFX- és jelszófájlok a következő környezeti változók használatával érhetők el a tárolóban: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux-fürtök esetén a tanúsítványok (PEM) az X509StoreName által megadott tárolóból kerülnek át a tárolóba. A megfelelő környezeti változók Linuxon a következők:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Másik lehetőségként, ha már rendelkezik a szükséges formában a tanúsítványokkal, és szeretné elérni a tárolón belül, létrehozhat egy adatcsomagot az alkalmazáscsomagon belül, és megadhatja a következőket az alkalmazásjegyzéken belül:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A tárolószolgáltatás vagy -folyamat felelős a tanúsítványfájlok tárolóba importálásáért. A tanúsítvány importálásához parancsfájlokat használhat, `setupentrypoint.sh` vagy egyéni kódot hajthat végre a tárolófolyamaton belül. Itt van a C# mintakód a PFX fájl importálásához:

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
Ez a PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás hitelesítésére vagy más szolgáltatásokkal való biztonságos kommunikációra. Alapértelmezés szerint a fájlok csak a SYSTEM-hez vannak adva. ACL-t a szolgáltatás által megkövetelt más fiókokhoz is átveheti.

Következő lépésként olvassa el a következő cikkeket:

* [Windows-tároló központi telepítése a Service Fabric szolgáltatásba Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése linuxos Service Fabric-re](service-fabric-get-started-containers-linux.md)
