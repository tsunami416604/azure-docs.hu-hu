---
title: Az Azure Service Fabric-alkalmazások tanúsítványok konfigurálása Linux rendszeren |} A Microsoft Docs
description: Az alkalmazás tanúsítványok konfigurálása a Service Fabric-futtatókörnyezet, a Linux-fürt
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
ms.author: v-jamebr
ms.openlocfilehash: c0580b75544a9613bc8caf2faaac11ba1ba6708e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667140"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Tanúsítványok és biztonság Linux-fürtökön

Ez a cikk ismerteti, X.509-tanúsítványok konfigurálása Linux-fürtökön.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Hely és a Linux-csomópontokat az X.509-tanúsítványokat formátumát

A Service Fabric általánosan vár X.509 tanúsítványok megtalálható a */var/lib/sfcerts* Linux-fürtcsomópontokon könyvtárába. Ez a fürt tanúsítványok, az ügyféltanúsítványok, stb. Néhány esetben megadhat egy helyet más, a *var/lib/sfcerts* tanúsítványok mappáját. Ha például a Reliable Services a Service Fabric Java SDK használatával jönnek létre, megadhatja egy másik helyet a egyes alkalmazás-specifikus tanúsítványokat a konfigurációs csomagot (Settings.xml) keresztül. További tudnivalókért lásd: [a konfigurációs csomag (Settings.xml) hivatkozott tanúsítványok](#certificates-referenced-in-the-configuration-package-settingsxml).

A Linux-fürtök esetén a Service Fabric vár a tanúsítványok jelen, vagy egy .pem-fájlt, amely tartalmazza a tanúsítványt és a titkos kulcsot, vagy .crt fájl tartalmaz, a tanúsítvány és a egy .key a titkos kulcsot tartalmazó fájlt. Minden fájl PEM formátumban kell megadni. 

Ha a tanúsítvány Azure Key vault használatával telepíti egy [Resource Manager-sablon](./service-fabric-cluster-creation-create-template.md) vagy [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) parancsokat, a tanúsítvány telepítve van a megfelelő formátumban vannak a */var/ lib/sfcerts* könyvtárat minden egyes csomóponton. Ha egy tanúsítvány egy másik módszerrel telepít, győződjön meg arról, hogy a tanúsítvány helyesen van-e telepítve a fürtcsomópontokon.

## <a name="certificates-referenced-in-the-application-manifest"></a>Az alkalmazásjegyzékben hivatkozott tanúsítványok

A kérelemben megadott tanúsítványok manifest, például keresztül a [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) vagy [ **EndpointCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)elemek, a jelen kell lennie a */var/lib/sfcerts* könyvtár. Az elemek, amelyek adja meg a tanúsítványok az alkalmazásjegyzékben, a tanúsítványok szerepelnie kell az alapértelmezett címtár nem lépnek egy elérési út attribútumban. Ezek az elemek igénybe egy nem kötelező **X509StoreName** attribútum. Az alapértelmezett érték a "My" kifejezést, amely a */var/lib/sfcerts* Linux-csomópontok könyvtárába. Bármely más érték nincs definiálva egy Linux-fürtön. Azt javasoljuk, hogy kihagyja a **X509StoreName** attribútum a Linux-fürtökön futó alkalmazások. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>A konfigurációs csomag (Settings.xml) hivatkozott tanúsítványok

Bizonyos szolgáltatások esetén az X.509-tanúsítványokat konfigurálhatja a [ConfigPackage](./service-fabric-application-and-service-manifests.md) (alapértelmezés szerint Settings.xml). Például ez a helyzet RPC csatornák Reliable Services szolgáltatásokhoz készült Service Fabric .NET Core vagy a Java SDK-k védelmére szolgáló tanúsítványok deklarálásakor. A referencia-tanúsítványok a konfigurációs csomag két módja van. Támogatja a .NET Core és a Java SDK-k közé esik.

### <a name="using-x509-securitycredentialstype"></a>X509 használatával SecurityCredentialsType

A .NET vagy Java SDK-k is megadhat **X509** számára a **SecurityCredentialsType**. Ez megfelel a `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) típusú `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

A **X509** referencia megkeresi a tanúsítványt a tanúsítványtárolóban. A következő XML formátumú a tanúsítvány helyének megadásához használt paraméterek látható:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

A Linux rendszeren futó szolgáltatásban **LocalMachine**/**saját** a tanúsítványok esetében az alapértelmezett helyre mutat a */var/lib/sfcerts* könyvtár. A Linux rendszerre, bármely egyéb kombinációit **CertificateStoreLocation** és **Tanúsítványtároló_neve** nincs definiálva. 

Mindig adja meg **LocalMachine** számára a **CertificateStoreLocation** paraméter. Nem kell megadnia a **Tanúsítványtároló_neve** paraméter, mert a rendszer alapértelmezés szerint a "My"kifejezést. Az egy **X509** hivatkozást, a fájlokat tartalmazó tárolójában kell lennie a */var/lib/sfcerts* a fürtcsomópont könyvtárába.  

A következő XML-mutatja egy **TransportSettings** szakasz alapján ez a stílus:

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

### <a name="using-x5092-securitycredentialstype"></a>X509_2 SecurityCredentialsType használatával

A Java SDK-t is megadhat **X509_2** számára a **SecurityCredentialsType**. Ez megfelel a `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) típusú `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Az egy **X509_2** hivatkozás, megad egy elérésiút-paraméter, így megkeresheti a tanúsítványt más, a címtárban */var/lib/sfcerts*.  A következő XML formátumú a tanúsítvány helyének megadásához használt paraméterek látható: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

A következő XML-mutatja egy **TransportSettings** szakasz alapján ez a stílus.

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
> A tanúsítvány előző XML-fájlként .crt van megadva. Ez azt jelenti, hogy nincs-e is ugyanazon a helyen a titkos kulcsot tartalmazó .key fájlt.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>A Reliable Services-alkalmazás Linux-fürtökön futó konfigurálása

A Service Fabric SDK-k lehetővé teszik a Service Fabric-futtatókörnyezet API-kat használhatja a platform folytatott kommunikációhoz. Biztonságos Linux-fürtökön ezt a funkciót használó összes alkalmazásra futtatásakor kell az alkalmazás konfigurálása egy tanúsítvánnyal, ezáltal az ellenőrzés a Service Fabric-futtatókörnyezet. Service Fabric Reliable Services-szolgáltatások, a .NET Core vagy a Java SDK-k használatával írt tartalmazó alkalmazásoknak ezt a konfigurációt. 

Alkalmazások konfigurálása, adjon hozzá egy [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elemet a **tanúsítványok** címke, amely alatt található a **ApplicationManifest**  a címke a *ApplicationManifest.xml* fájlt. A következő XML-kódot mutatja be egy tanúsítványt az ujjlenyomat által hivatkozott: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

A fürttanúsítvány és a egy tanúsítványt, amely a fürt minden csomópontján telepítenie hivatkozhat. Linux rendszeren a tanúsítványfájlokat szerepelniük kell a */var/lib/sfcerts* könyvtár. További tudnivalókért lásd: [helyét és a Linux-csomópontokat az X.509-tanúsítványokat formátumát](#location-and-format-of-x509-certificates-on-linux-nodes).



