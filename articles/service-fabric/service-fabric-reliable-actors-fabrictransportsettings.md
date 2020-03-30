---
title: FabricTransport-beállítások módosítása
description: Ismerje meg az Azure Service Fabric aktor kommunikációs beállításainak konfigurálását a különböző aktorkonfigurációkhoz.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: 182b35194a647f5de706f9ada07b8e9db51bb4c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645582"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>FabricTransport-beállítások konfigurálása megbízható szereplőkhez

Az alábbiakat a következő beállításokkal állíthatja be:
- C#: [FabricTransportRemotingBeállítások](
https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingBeállítások](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

A FabricTransport alapértelmezett konfigurációját a következő módokon módosíthatja.

## <a name="assembly-attribute"></a>Összeállítás attribútum

A [FabricTransportActorRemotingProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN) attribútumot az aktor ügyfél- és aktor szolgáltatásszerelvényekre kell alkalmazni.

A következő példa bemutatja, hogyan módosíthatja a FabricTransport OperationTimeout beállításainak alapértelmezett értékét:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   A második példa módosítja a FabricTransport MaxMessageSize és az OperationTimeoutInSeconds alapértelmezett értékeit.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Konfigurációs csomag

Az alapértelmezett konfiguráció módosításához [konfigurációs csomagot](service-fabric-application-and-service-manifests.md) is használhat.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM-formátumúnak kell lenniük. A tanúsítványok Linuxhoz való megkereséséről és konfigurálásáról a [Tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakörben olvashat bővebben. 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>FabricTransport-beállítások konfigurálása az aktorszolgáltatáshoz

Adjon hozzá egy TransportSettings szakaszt a settings.xml fájlhoz.

Alapértelmezés szerint az aktorkód&lt;a&gt;SectionName (SectionName) nevet "ActorName TransportSettings" néven keresi. Ha ez nem található, a SectionName (SectionName) (TransportSettings) (SectionName" (TransportSettings) (SectionName" (TransportSettings) (SectionName" (TransportSettings) (A hivatkozási),.If that's not found, it checks

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>FabricTransport-beállítások konfigurálása az aktor ügyfélszerelvényhez

Ha az ügyfél nem egy szolgáltatás részeként fut,&lt;létrehozhat egy&gt;"Ügyfél exe név .settings.xml" fájlt ugyanazon a helyen, mint az ügyfél .exe fájlját. Ezután adjon hozzá egy TransportSettings szakaszt a fájlhoz. A SectionName legyen a "TransportSettings".

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* FabricTransport-beállítások konfigurálása a biztonságos aktor szolgáltatáshoz/ügyfélhez másodlagos tanúsítvánnyal.
  A másodlagos tanúsítványadatok a CertificateFindValuebySecondary paraméter hozzáadásával adhatók hozzá.
  Az alábbiakban a figyelő TransportSettings példáját találja.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Az alábbiakban a client transportsettings példáját találja.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * FabricTransport-beállítások konfigurálása az aktorszolgáltatás/ügyfél védelméhez a tulajdonos neve használatával.
    A felhasználónak findBySubjectName-ként kell megadnia a findType értéket, hozzá kell adnia a CertificateIssuerThumbprints és certificateRemoteCommonNames értékeket.
    Az alábbiakban a figyelő TransportSettings példáját találja.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Az alábbiakban a client transportsettings példáját találja.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
