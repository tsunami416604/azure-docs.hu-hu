---
title: Hozzáférési szabályzatok hozzárendelése az Azure Service Fabric szolgáltatási végpontjainak |} A Microsoft Docs
description: Ismerje meg, hogyan rendelje hozzá a biztonsági hozzáférési szabályzatok a Service Fabric-szolgáltatást a HTTP vagy HTTPS-végpontokra irányuló.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670404"
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Következő lépések olvassa el a következő cikkeket:
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
