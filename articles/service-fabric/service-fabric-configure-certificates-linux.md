---
title: Azure Service Fabric-alkalmazások tanúsítványok konfigurálása Linux |} Microsoft Docs
description: Az alkalmazás tanúsítványok konfigurálása a Service Fabric-futtatókörnyezet egy Linux-fürt
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: 2d6d387ed12e7261d09669686c0710786a4302dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025812"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Tanúsítványok és a Linux fürtökön biztonsági

Ez a cikk konfigurálásának X.509-tanúsítványokat Linux fürtökön.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Hely és a Linux csomópontján X.509-tanúsítvány formátuma

A Service Fabric általában vár X.509 tanúsítvány megtalálható a */var/lib/sfcerts* Linux fürtcsomópontok könyvtárába. Ez igaz fürt tanúsítványok, ügyféltanúsítványokat, stb. Néhány esetben megadhat egy helyet eltérő a *var/lib/sfcerts* tanúsítványok mappát. Például a Reliable Services, a Service Fabric Java SDK használatával készített, megadhatja az egyes alkalmazás-specifikus tanúsítványokat a konfigurációs csomagot (Settings.xml) keresztül egy másik helyre. További tudnivalókért lásd: [a konfigurációs csomag (Settings.xml) hivatkozott tanúsítványok](#certificates-referenced-in-the-configuration-package-settingsxml).

Linux-fürtök esetén a Service Fabric vár a tanúsítványok jelen, vagy a .pem fájl, amely tartalmazza a tanúsítványt és a titkos kulcsot vagy egy .crt fájl, a tanúsítványt és a titkos kulcsot tartalmazó .key fájl. Minden fájl PEM formátumúnak kell lennie. 

Ha telepíti a tanúsítványt, az Azure Key Vault használatával vagy egy [Resource Manager-sablon](./service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template) vagy [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) parancsok, a tanúsítvány telepítve van a helyes formátumban az */var/ lib/sfcerts* könyvtárhoz, minden egyes csomóponton. Ha egy tanúsítvány egy másik módszerrel telepít, győződjön meg arról, hogy a tanúsítvány helyesen van-e telepítve a fürtcsomópontokon.

## <a name="certificates-referenced-in-the-application-manifest"></a>Az alkalmazásjegyzékben hivatkozott tanúsítványok

A kérelemben megadott tanúsítványok manifest, például keresztül a [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) vagy [ **EndpointCertificate** ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)elemek, a jelen kell lennie a */var/lib/sfcerts* könyvtár. Adja meg a tanúsítványok az alkalmazásjegyzékben használt elemek nem hajtja végre egy elérési út attribútumban, a tanúsítványok az alapértelmezett címtár jelen kell lennie. Ezek az elemek eltarthat egy nem kötelező **X509StoreName** attribútum. Az alapértelmezett érték "A", amely mutat a */var/lib/sfcerts* Linux csomópontok könyvtárába. Semmilyen más érték nincs van definiálva, a Linux-fürt. Azt javasoljuk, hogy kihagyja a **X509StoreName** Linux fürtökön futó alkalmazások attribútuma. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>A konfigurációs csomag (Settings.xml) hivatkozott tanúsítványok

Bizonyos szolgáltatások esetén konfigurálhatja az X.509 tanúsítvány a [ConfigPackage](./service-fabric-application-and-service-manifests.md) (ami alapértelmezés szerint a Settings.xml). Például ez a helyzet amikor deklarálhatja RPC-csatornák a Service Fabric .NET Core vagy a Java SDK-val készült Reliable Services szolgáltatások biztosításához használt tanúsítványok. Két módon lehet a konfigurációs csomag hivatkozás tanúsítványokat. Támogatja a .NET Core és a Java SDK-k között függően változik.

### <a name="using-x509-securitycredentialstype"></a>X509 használatával SecurityCredentialsType

A .NET vagy a Java SDK-k, megadhatja a **X509** a a **SecurityCredentialsType**. Ez megfelel a `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials)) típusú `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)).

A **X509** hivatkozás megkeresi a tanúsítványt a tanúsítványtárolóban. A következő XML jeleníti meg a paraméterek használatával adja meg a tanúsítvány helyét:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

A futó Linux, a szolgáltatás **LocalMachine**/**a** -tanúsítványok esetén az alapértelmezett helyre mutat a */var/lib/sfcerts* könyvtár. A Linux bármely más kombinációja **CertificateStoreLocation** és **Tanúsítványtároló_neve** nincs definiálva. 

Mindig adja meg **LocalMachine** a a **CertificateStoreLocation** paraméter. Adja meg, hogy nincs szükség a **Tanúsítványtároló_neve** paraméter mert rendszer alapértelmezés szerint az "A". Az egy **X509** referenciaként a tanúsítványfájlokat kell elhelyezni, a */var/lib/sfcerts* a fürtcsomópont könyvtárába.  

A következő XML-mutatja egy **TransportSettings** szakasz az ilyen stílusú alapján:

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

A Java SDK-val is megadhat **X509_2** a a **SecurityCredentialsType**. Ez megfelel a `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials2)) típusú `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)). 

Az egy **X509_2** referenciaként egy elérési út paraméter megadása, így megkeresheti a tanúsítványt a könyvtárban található eltérő */var/lib/sfcerts*.  A következő XML jeleníti meg a paraméterek használatával adja meg a tanúsítvány helyét: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

A következő XML-mutatja egy **TransportSettings** szakasz alapján az ilyen stílusú.

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
> A tanúsítvány az előző XML .crt fájlban van megadva. Ez azt jelenti, hogy nincs-e is ugyanazon a helyen a titkos kulcsot tartalmazó .key fájlt.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>A Linux fürtökön futtatásához a Reliable Services alkalmazás konfigurálása

A Service Fabric SDK-k lehetővé teszik a Service Fabric-futtatókörnyezet API-k, hogy kihasználja a platform folytatott kommunikációhoz. Biztonságos Linux fürtökön ezt a funkciót használó bármely alkalmazás futtatásakor az alkalmazás konfigurálása egy tanúsítvánnyal, akkor a Service Fabric-futtatókörnyezet lemezeit segítségével kell. A .NET Core vagy a Java SDK-k használatával készítettek Service Fabric megbízható szolgáltatás szolgáltatásokat tartalmazó alkalmazásoknak ezt a konfigurációt. 

Alkalmazások konfigurálása, vegye fel a [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elem alatt a **tanúsítványok** címke, amely alatt található a **applicationmanifest jegyzékben**  lévő a *ApplicationManifest.xml* fájlt. A következő XML annak ujjlenyomata által hivatkozott tanúsítvány látható: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

A fürt tanúsítvány vagy egy tanúsítványt, amely a fürt minden csomópontján telepítenie hivatkozhat. Linux, a tanúsítványfájlokat jelen kell lennie a */var/lib/sfcerts* könyvtár. További tudnivalókért lásd: [helyét és a Linux csomópontján X.509-tanúsítvány formátuma](#location-and-format-of-x509-certificates-on-linux-nodes).



