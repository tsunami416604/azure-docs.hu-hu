---
title: Hozzáférési házirendek hozzárendelése az Azure Service Fabric Szolgáltatásvégpontok |} Microsoft Docs
description: Megtudhatja, hogyan rendelje hozzá a biztonsági hozzáférési házirendeket HTTP vagy HTTPS végpontok a Service Fabric-szolgáltatás.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 65f47d238d4e591ddde8937d0eb3c55931c01c3f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Rendelje hozzá a biztonsági házirend HTTP és HTTPS-végpontok
Ha egy futtató házirend alkalmazása, és a szolgáltatás jegyzékfájl deklarálja a HTTP-végpont erőforrást, meg kell adnia egy **SecurityAccessPolicy**.  **SecurityAccessPolicy** biztosítja, hogy ezeket a végpontokat rendelt portok megfelelően korlátozódik, amely a szolgáltatás fut, a felhasználói fiók. Ellenkező esetben **http.sys** nem rendelkezik hozzáféréssel a szolgáltatáshoz, és sikertelen hívások kapott az ügyféltől. A következő példa a Customer1 fiók vonatkozik nevű végpont **EndpointName**, amely azt teszi teljes körű hozzáférési jogosultságokat.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Egy HTTPS-végpont is utalhat vissza az ügyfélnek a tanúsítvány neve. A tanúsítvány használatával hivatkozik **EndpointBindingPolicy**.  A tanúsítvány van definiálva a **tanúsítványok** szakasza az alkalmazás jegyzékében.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépésekkel olvassa el a következő cikkeket:
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
