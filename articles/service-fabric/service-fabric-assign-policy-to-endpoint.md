---
title: Hozzáférési házirendek hozzárendelése szolgáltatásvégpontokhoz
description: Ismerje meg, hogyan rendelhet biztonsági hozzáférési biztonsági biztonsági biztonsági rendszereket http- vagy HTTPS-végpontokhoz a Service Fabric szolgáltatásban.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614655"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Biztonsági hozzáférési házirend hozzárendelése HTTP- és HTTPS-végpontokhoz
Ha run-as házirendet alkalmaz, és a szolgáltatásjegyzék deklarálja a HTTP-végpont erőforrásait, meg kell adnia egy **SecurityAccessPolicy házirendet.**  **A SecurityAccessPolicy** biztosítja, hogy a végpontokhoz rendelt portok megfelelően korlátozódjanak arra a felhasználói fiókra, amelyen a szolgáltatás fut. Ellenkező esetben **a http.sys** nem fér hozzá a szolgáltatáshoz, és hibákat kap az ügyfél hívásaival. A következő példa a Customer1-fiókot alkalmazza az **EndpointName**nevű végpontra, amely teljes hozzáférési jogokat biztosít számára.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

HTTPS-végpont esetén adja meg az ügyfélnek visszaadandó tanúsítvány nevét is. Az **EndpointBindingPolicy**használatával hivatkozik a tanúsítványra.  A tanúsítvány az alkalmazásjegyzék **Tanúsítványok** szakaszában van definiálva.

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
> HTTPS használataesetén ne használja ugyanazt a portot és tanúsítványt az ugyanazon csomópontra telepített különböző szolgáltatáspéldányokhoz (az alkalmazástól függetlenül). Két különböző szolgáltatás frissítése ugyanazon port használatával különböző alkalmazáspéldányokban frissítési hibát eredményez. További információt a [Több alkalmazás frissítése HTTPS-végponttal ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)című témakörben talál.
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépéseket az alábbi cikkekben olvashatja:
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
