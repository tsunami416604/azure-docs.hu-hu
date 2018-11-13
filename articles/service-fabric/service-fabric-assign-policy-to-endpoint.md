---
title: Hozzáférési szabályzatok hozzárendelése az Azure Service Fabric szolgáltatási végpontjainak |} A Microsoft Docs
description: Ismerje meg, hogyan rendelje hozzá a biztonsági hozzáférési szabályzatok a Service Fabric-szolgáltatást a HTTP vagy HTTPS-végpontokra irányuló.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: dac15f0b96e9e295f92f250fe387e5b6ba9ae000
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567604"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>A HTTP vagy HTTPS-végpontokat biztonsági hozzáférési szabályzat hozzárendelése
Ha futtató szabályzat alkalmazásához, és a szolgáltatásjegyzék deklarálja a HTTP-végpont erőforrásokat, meg kell adnia egy **SecurityAccessPolicy**.  **SecurityAccessPolicy** biztosítja, hogy ezeket a végpontokat hozzárendelt portok megfelelően korlátozva vannak, amelyek a szolgáltatás fut, a felhasználói fiókhoz. Ellenkező esetben **http.sys** nem rendelkezik a szolgáltatáshoz való hozzáférést, és sikertelen hívások kap az ügyfélről. Az alábbi példa a Customer1 fiók érvényes nevű végpont **Végpontneve**, amely teljes körű hozzáférési jogosultságokat biztosít azt.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

HTTPS-végpont is utalhat térjen vissza az ügyfél a tanúsítvány nevét jelöli. A tanúsítvány használatával hivatkozik **EndpointBindingPolicy**.  A tanúsítvány van definiálva a **tanúsítványok** az alkalmazásjegyzékben szakaszában.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> HTTPS használata esetén ne használjon port és a különböző szolgáltatáspéldányok (amely független az alkalmazás) ugyanazon a csomóponton telepített tanúsítványt. Két különböző szolgáltatások, a különböző alkalmazáspéldányok ugyanazt a portot használja a frissítés frissítési hibát eredményez. További információkért lásd: [frissítése több alkalmazás, a HTTPS-végpontok ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged--> Következő lépések olvassa el a következő cikkeket:
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
