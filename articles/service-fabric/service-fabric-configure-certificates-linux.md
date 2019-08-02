---
title: Azure Service Fabric-alkalmazások tanúsítványainak konfigurálása Linux rendszeren | Microsoft Docs
description: Tanúsítványok konfigurálása az alkalmazáshoz a Linux-fürtön futó Service Fabric futtatókörnyezettel
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: chackdan
ms.openlocfilehash: 4a5a67133d52a0cdc0cc082ab85c1cc791c13ad5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876566"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Tanúsítványok és biztonság Linux-fürtökön

Ez a cikk az X. 509 tanúsítványok Linux-fürtökön való konfigurálásával kapcsolatos információkat tartalmaz.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>X. 509 tanúsítványok helye és formátuma Linux-csomópontokon

A Service Fabric általában X. 509 tanúsítványokat vár a Linux-fürtcsomópontok */var/lib/sfcerts* könyvtárában. Ez a fürt tanúsítványainak, Ügyféltanúsítványok stb. esetében igaz. Bizonyos esetekben a *var/lib/sfcerts* mappától eltérő helyet is megadhat a tanúsítványokhoz. A Service Fabric Java SDK-val létrehozott Reliable Services például megadhat egy másik helyet a konfigurációs csomagban (Settings. xml) az alkalmazásspecifikus tanúsítványok számára. További információ: [a konfigurációs csomagban hivatkozott tanúsítványok (Settings. xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Linux-fürtök esetén Service Fabric a tanúsítványokat olyan. PEM fájlként kell megadnia, amely tartalmazza a tanúsítványt és a titkos kulcsot, vagy egy. CRT-fájlt, amely tartalmazza a tanúsítványt és a titkos kulcsot tartalmazó. Key fájlt. Minden fájlnak PEM formátumúnak kell lennie. 

Ha a tanúsítványt egy [Resource Manager-sablonnal](./service-fabric-cluster-creation-create-template.md) vagy [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) -parancsokkal telepíti Azure Key Vaultból, a tanúsítványt a */var/lib/sfcerts* könyvtárban, az egyes csomópontokon a megfelelő formátumban kell telepíteni. Ha a tanúsítványt egy másik módszerrel telepíti, meg kell győződnie arról, hogy a tanúsítvány megfelelően van telepítve a fürtcsomópontokon.

## <a name="certificates-referenced-in-the-application-manifest"></a>Az alkalmazás jegyzékfájljában hivatkozott tanúsítványok

Az alkalmazás jegyzékfájljában megadott tanúsítványoknak (például a [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) vagy a [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) elemnek) szerepelniük kell a */var/lib/sfcerts* könyvtárban. Az alkalmazás jegyzékfájljában a tanúsítványok megadásához használt elemek nem rendelkeznek elérésiút-attribútummal, ezért a tanúsítványoknak jelen kell lenniük az alapértelmezett könyvtárban. Ezek az elemek nem kötelező **X509StoreName** attribútumot is igénybe vehetnek. Az alapértelmezett érték a "My", amely a Linux-csomópontok */var/lib/sfcerts* könyvtárára mutat. Bármely más érték nem definiált Linux-fürtön. Azt javasoljuk, hogy hagyja ki a **X509StoreName** attribútumot a Linux-fürtökön futó alkalmazásokhoz. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>A konfigurációs csomagban hivatkozott tanúsítványok (Settings. xml)

Egyes szolgáltatások esetében X. 509 tanúsítványokat állíthat be a [ConfigPackage](./service-fabric-application-and-service-manifests.md) (alapértelmezés szerint a Settings. xml fájl). Ilyen eset például, ha deklarálja a Service Fabric .NET Core vagy Java SDK-val létrehozott Reliable Services szolgáltatások RPC-csatornáinak biztonságossá tételéhez használt tanúsítványokat. A konfigurációs csomagban kétféleképpen hivatkozhat tanúsítványokra. A támogatás a .NET Core és a Java SDK-k között változhat.

### <a name="using-x509-securitycredentialstype"></a>A X509 SecurityCredentialsType használata

A .NET-vagy Java SDK-k segítségével megadhatja a **SecurityCredentialsType** **X509** . Ez `X509Credentials` megfelel[](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)a/[](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)[](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)[](https://docs.microsoft.com/java/api/system.fabric.x509credentials) `SecurityCredentials` (.net Java) típusának (.net Java)./

A **X509** -hivatkozás megkeresi a tanúsítványt a tanúsítványtárolóban. A következő XML a tanúsítvány helyének megadásához használt paramétereket mutatja be:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

A Linux rendszeren futó szolgáltatások esetében a **LocalMachine**/a tanúsítványok alapértelmezett helyére, a */var/lib/sfcerts* könyvtárra**mutatnak** . Linux esetén a **CertificateStoreLocation** és a **CertificateStoreName** bármely más kombinációja nincs meghatározva. 

Mindig **LocalMachine** megadása a **CertificateStoreLocation** paraméterhez. Nem kell megadnia a **CertificateStoreName** paramétert, mert az alapértelmezett értéke "My". **X509** -hivatkozás esetén a tanúsítványfájl a fürtcsomópont */var/lib/sfcerts* könyvtárában kell, hogy legyen.  

A következő XML egy **TransportSettings** szakaszt mutat be a stílus alapján:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>A X509_2 SecurityCredentialsType használata

A Java SDK segítségével megadhatja a **SecurityCredentialsType** **X509_2** . Ez megfelel a `X509Credentials2` (Java) ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) `SecurityCredentials` típusának.[](https://docs.microsoft.com/java/api/system.fabric.securitycredentials) 

**X509_2** -hivatkozás esetén meg kell adnia egy Path paramétert, így a tanúsítvány a */var/lib/sfcerts*-től eltérő könyvtárban is megkereshető.  A következő XML a tanúsítvány helyének megadásához használt paramétereket mutatja be: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

A következő XML egy **TransportSettings** szakaszt mutat be ezen stílus alapján.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> A tanúsítvány. CRT-fájlként van megadva az előző XML-fájlban. Ez azt jelenti, hogy létezik egy. key fájl is, amely tartalmazza a titkos kulcsot ugyanazon a helyen.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatásra

A Service Fabric SDK-k lehetővé teszik a Service Fabric Runtime API-kkal való kommunikációt a platform kihasználása érdekében. Ha olyan alkalmazást futtat, amely ezt a funkciót a biztonságos Linux-fürtökön használja, konfigurálnia kell az alkalmazást egy olyan tanúsítvánnyal, amelyet a Service Fabric futtatókörnyezettel való ellenőrzéshez használhat. A .NET Core vagy Java SDK-k használatával írt Service Fabric megbízható szolgáltatási szolgáltatásokat tartalmazó alkalmazások esetén ehhez a konfigurációhoz szükség van. 

Egy alkalmazás konfigurálásához adjon hozzá egy [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elemet a **tanúsítványok** címkében, amely a *ApplicationManifest. XML* fájl **ApplicationManifest** címkéjén található. A következő XML az ujjlenyomata által hivatkozott tanúsítványt jeleníti meg: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Hivatkozhat a fürt tanúsítványára vagy a fürt egyes csomópontjaira telepített tanúsítványokra. Linux rendszeren a tanúsítvány fájljainak jelen kell lenniük a */var/lib/sfcerts* könyvtárban. További információért lásd: [X. 509 tanúsítványok helye és formátuma Linux](#location-and-format-of-x509-certificates-on-linux-nodes)-csomópontokon.



