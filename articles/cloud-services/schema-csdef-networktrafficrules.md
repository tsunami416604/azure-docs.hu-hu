---
title: Az Azure Cloud Services def NetworkTrafficRules séma |} A Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821682"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services – definíciós NetworkTrafficRules séma
A `NetworkTrafficRules` csomópont egy nem kötelező eleme a szolgáltatásdefiníciós fájlt, amely meghatározza, milyen szerepkörök kommunikálnak egymással. Mely szerepkörök hozzáférhet az adott szerepkör a belső végpontok korlátozza. A `NetworkTrafficRules` nem egy önálló elemet; a szolgáltatásdefiníciós fájlban két vagy több szerepkör kombinálva.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef esetében.

> [!NOTE]
>  A `NetworkTrafficRules` csomópont csak az elérhető az Azure SDK verzióval 1.3-as vagy újabb.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Alapszintű szolgáltatásdefiníciós sémában a hálózati forgalomra vonatkozó szabályok
A hálózati forgalom definíciókat tartalmazó szolgáltatásdefiníciós fájl alapvető formátuma a következő.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A `NetworkTrafficRules` a szolgáltatásdefiníciós fájlt a csomópont tartalmazza ezeket az elemeket, ez a témakör későbbi részeiben részletesen ismertetjük:

[NetworkTrafficRules elem](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Célok elem](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

AllowAllTraffic elem

[WhenSource elem](#WhenSource)

[FromRole elem](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules elem
A `NetworkTrafficRules` elem azt határozza meg, mely szerepkörök képes kommunikálni egy másik szerepkör melyik végponthoz. Egy szolgáltatás egyet tartalmazhat `NetworkTrafficRules` definíciója.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo elem
A `OnlyAllowTrafficTo` elem cél végpontok és a velük kommunikáló szerepkörök gyűjteménye, írja le. Megadhatja, hogy több `OnlyAllowTrafficTo` csomópontok.

##  <a name="Destinations"></a> Célok elem
A `Destinations` elem RoleEndpoints gyűjteménye írja le, mint a továbbíthatók.

##  <a name="RoleEndpoint"></a> RoleEndpoint elem
A `RoleEndpoint` elem az a végpont egy engedélyezik a kommunikációt a szerepkör írja le. Megadhatja, hogy több `RoleEndpoint` elemeket, ha vannak a szerepkör több végpont.

| Attribútum      | Typo     | Leírás |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. Engedélyezi az adatforgalmat a végpont neve.|
| `roleName`     | `string` | Kötelező. Engedélyezi a kommunikációt, a webes szerepkör neve.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic elem
A `AllowAllTraffic` elem egy szabályt, amely lehetővé teszi, hogy az összes szerepkör a meghatározott végpontok kommunikálni a `Destinations` csomópont.

##  <a name="WhenSource"></a> WhenSource elem
A `WhenSource` elem szerepkörök gyűjteménye írja le, és a meghatározott végpontok közötti kommunikációhoz, mint a `Destinations` csomópont.

| Attribútum | Typo     | Leírás |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Megadja a szabály a alkalmazni, ha engedélyezi a kommunikációt. Jelenleg az egyetlen érvényes érték `AnyRule`.|
  
##  <a name="FromRole"></a> FromRole elem
A `FromRole` elem azt határozza meg a szerepköröket, amely képes kommunikálni a meghatározott végpontok a `Destinations` csomópont. Megadhatja, hogy több `FromRole` elemeket, ha több szerepkör, amely képes kommunikálni a végpontok.

| Attribútum  | Typo     | Leírás |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. Szerepkör, amely engedélyezi a kommunikációt a nevét.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
