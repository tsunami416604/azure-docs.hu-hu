---
title: Tanúsítványok konfigurálása Linuxos alkalmazásokhoz
description: Tanúsítványok konfigurálása az alkalmazáshoz a Service Fabric futásidejű linuxos fürtön
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282574"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Tanúsítványok és biztonság Linux-fürtökön

Ez a cikk az X.509-es tanúsítványok Linux-fürtökön történő konfigurálásáról nyújt tájékoztatást.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Az X.509 tanúsítványok helye és formátuma Linux csomópontokon

A Service Fabric általában elvárja, hogy az X.509 tanúsítványok jelen legyenek a */var/lib/sfcerts* könyvtárban a Linux fürtcsomópontokon. Ez a fürttanúsítványokra, ügyféltanúsítványokra stb. Bizonyos esetekben a tanúsítványok *var/lib/sfcerts* mappától eltérő helyet is megadhat. Például a Service Fabric Java SDK használatával készített Megbízható szolgáltatások használatával megadhat egy másik helyet a konfigurációs csomagon (Settings.xml) keresztül néhány alkalmazásspecifikus tanúsítványhoz. További információ: [A konfigurációs csomagban hivatkozott tanúsítványok (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml)című témakörben olvashat.

Linux-fürtök esetén a Service Fabric elvárja, hogy a tanúsítványok a tanúsítványt és a személyes kulcsot egyaránt tartalmazó .pem fájlként, vagy a tanúsítványt tartalmazó .crt fájlként és a személyes kulcsot tartalmazó .cikt fájlként legyenek jelen. Minden fájlnak PEM formátumúnak kell lennie. 

Ha a tanúsítványt az Azure Key Vault ból telepíti [egy Resource Manager-sablon](./service-fabric-cluster-creation-create-template.md) vagy [PowerShell-parancs](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) használatával, a tanúsítvány a megfelelő formátumban lesz telepítve a */var/lib/sfcerts* könyvtárban minden csomóponton. Ha egy tanúsítványt más módszerrel telepít, meg kell győződnie arról, hogy a tanúsítvány megfelelően van telepítve a fürtcsomópontokon.

## <a name="certificates-referenced-in-the-application-manifest"></a>Az alkalmazásjegyzékben hivatkozott tanúsítványok

Az alkalmazásjegyzékben megadott tanúsítványoknak például a [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) vagy az [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) elemeken keresztül meg kell jelennie a */var/lib/sfcerts* könyvtárban. Az alkalmazásjegyzékben a tanúsítványok megadásához használt elemek nem használnak elérési utat, ezért a tanúsítványoknak meg kell jelenniük az alapértelmezett könyvtárban. Ezek az elemek nem vesz egy opcionális **X509StoreName** attribútum. Az alapértelmezett érték a "My", amely a */var/lib/sfcerts* könyvtárra mutat linuxos csomópontokon. Minden más érték nincs definiálva egy Linux-fürtön. Azt javasoljuk, hogy hagyja ki az **X509StoreName** attribútumot a Linux-fürtökön futó alkalmazásokhoz. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>A konfigurációs csomagban hivatkozott tanúsítványok (Settings.xml)

Egyes szolgáltatások esetében x.509-es tanúsítványokat konfigurálhat a [ConfigPackage csomagban](./service-fabric-application-and-service-manifests.md) (alapértelmezés szerint Settings.xml). Ez a helyzet például akkor, ha deklarálja a Service Fabric .NET Core vagy Java SDK-kkal készült Reliable Services szolgáltatások RPC-csatornáinak védelmére használt tanúsítványokat. A konfigurációs csomagban kétféleképpen hivatkozhat a tanúsítványokra. A támogatás a .NET Core és a Java SDK-k között változik.

### <a name="using-x509-securitycredentialstype"></a>Az X509 SecurityCredentialsType használata

WIth a .NET vagy Java SDKs, megadhatja **X509** a **SecurityCredentialsType**. Ez megfelel a `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)Java `SecurityCredentials` ) típusú ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)) típusnak.

Az **X509-es** hivatkozás egy tanúsítványtárolóban találja meg a tanúsítványt. A következő XML a tanúsítvány helyének megadásához használt paramétereket mutatja:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Linuxon futó szolgáltatás esetén a **LocalMachine**/**My** a tanúsítványok alapértelmezett helyére, a */var/lib/sfcerts* könyvtárra mutat. Linux esetén a **CertificateStoreLocation** és a **CertificateStoreName** bármely más kombinációja nincs definiálva. 

Mindig adja meg a **LocalMachine** paramétert a **CertificateStoreLocation** paraméterhez. Nincs szükség a **CertificateStoreName** paraméter megadására, mert alapértelmezés szerint "Saját" lesz. **X509-es** hivatkozás esetén a tanúsítványfájloknak a fürtcsomópont */var/lib/sfcerts* könyvtárában kell lenniük.  

A következő XML a **transportsettings** szakaszt mutatja az alábbi stílus alapján:

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

### <a name="using-x509_2-securitycredentialstype"></a>A X509_2 SecurityCredentialsType használata

A Java SDK segítségével **megadhatja** a X509_2 a **SecurityCredentialsType .** Ez megfelel a `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2) `SecurityCredentials` ) típusú ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

A **X509_2** hivatkozással megadhat egy elérési utat, így a tanúsítvány tankönyvtárban, a */var/lib/sfcerts*könyvtártól eltérő könyvtárban kereshető.  A következő XML a tanúsítvány helyének megadásához használt paramétereket mutatja: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

A következő XML a jelen stíluson alapuló **TransportSettings** szakaszt jeleníti meg.

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
> A tanúsítvány .crt fájlként van megadva az előző XML-ben. Ez azt jelenti, hogy a személyes kulcsot tartalmazó .key fájl is szerepel ugyanazon a helyen.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Megbízható szolgáltatások alkalmazás konfigurálása Linux-fürtökön való futtatáshoz

A Service Fabric SDK-k lehetővé teszik, hogy kommunikálni a Service Fabric futásidejű API-k a platform kihasználása érdekében. Ha olyan alkalmazást futtat, amely ezt a funkciót biztonságos Linux-fürtökön használja, konfigurálnia kell az alkalmazást egy tanúsítvánnyal, amelyet a Service Fabric futásidejű ellenőrzéséhez használhat. A .NET Core vagy Java SDK-k használatával írt Service Fabric reliable service szolgáltatásokat tartalmazó alkalmazások ezt a konfigurációt igénylik. 

Alkalmazás konfigurálásához adjon hozzá egy [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elemet a **Tanúsítványok** címke alá, amely az *ApplicationManifest.xml* fájl **ApplicationManifest** címkéje alatt található. A következő XML egy ujjlenyomattal hivatkozott tanúsítványt mutat be: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Hivatkozhat a fürttanúsítványra vagy az egyes fürtcsomópontokra telepített tanúsítványra. Linux on a tanúsítványfájloknak meg kell jelenniük a */var/lib/sfcerts* könyvtárban. További információ: [Az X.509 tanúsítványok helye és formátuma Linux csomópontokon](#location-and-format-of-x509-certificates-on-linux-nodes).



