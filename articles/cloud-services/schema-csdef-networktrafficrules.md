---
title: Azure Cloud Services alapértelmezett NetworkTrafficRules séma |} Microsoft Docs
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 779d3b42aeab04bb93756439a0482f32ade6557e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360036"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services – Definition NetworkTrafficRules séma
A `NetworkTrafficRules` csomópont egy választható elem, amely meghatározza, hogyan szerepkörök kommunikálnak egymással a szolgáltatásdefiníciós fájlban. Mely szerepkörök férhetnek hozzá az adott szerepkör a belső végpontok korlátozza. A `NetworkTrafficRules` van nem egy önálló elem; kombinálva van két vagy több szerepkört a szolgáltatásdefiníciós fájlban.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef.

> [!NOTE]
>  A `NetworkTrafficRules` csomópont csak az elérhető az Azure SDK-verzió 1.3 vagy újabb.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Alapszintű service definition sémáját a hálózati forgalomra vonatkozó szabályok
A hálózati forgalom definícióit tartalmazó szolgáltatásdefiníciós fájl alapszintű formátuma a következő.

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
A `NetworkTrafficRules` a szolgáltatásdefiníciós fájlban a csomópont tartalmazza ezeket az elemeket, ez a témakör későbbi részeiben részletesen:

[NetworkTrafficRules elem](#NetworkTrafficRules)

[OnlyAllowTrafficTo elem](#OnlyAllowTrafficTo)

[A célok elem](#Destinations)

[RoleEndpoint elem](#RoleEndpoint)

[AllowAllTraffic elem](#AllowAllTraffic)

[WhenSource elem](#WhenSource)

[FromRole elem](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules elem
A `NetworkTrafficRules` elem határozza meg, mely szerepköröket egy másik szerepkör melyik végponthoz kommunikálhatnak. A szolgáltatás egyet tartalmazhat `NetworkTrafficRules` definíciója.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo elem
A `OnlyAllowTrafficTo` elem cél végpontok és a szerepköröket, amely képes kommunikálni őket gyűjteménye írja le. Megadhatja, hogy több `OnlyAllowTrafficTo` csomópontok.

##  <a name="Destinations"></a> A célok elem
A `Destinations` elem RoleEndpoints gyűjteménye írja le, mint a továbbíthatók.

##  <a name="RoleEndpoint"></a> RoleEndpoint elem
A `RoleEndpoint` elem engedélyezik a kommunikációt a szerepkör a végpont írja le. Megadhatja, hogy több `RoleEndpoint` elemek, ha vannak a szerepkör több végpont.

| Attribútum      | Típus     | Leírás |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. A végpont forgalom való neve.|
| `roleName`     | `string` | Kötelező. A webes szerepkör engedélyezi a kommunikációt a neve.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic elem
A `AllowAllTraffic` elem egy szabályt, amely lehetővé teszi, hogy minden szerepkör kommunikálni a végpontok definiálva a `Destinations` csomópont.

##  <a name="WhenSource"></a> WhenSource elem
A `WhenSource` elem szerepkörök gyűjteménye írja le, mint a végpontok definiálva képes kommunikálni a `Destinations` csomópont.

| Attribútum | Típus     | Leírás |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Megadja a szabályt alkalmazni, ha engedélyezi a kommunikációt. Az egyetlen érvényes érték jelenleg `AnyRule`.|
  
##  <a name="FromRole"></a> FromRole elem
A `FromRole` elem azt adja meg a szerepköröket, amely képes kommunikálni a végpontok definiálva a `Destinations` csomópont. Megadhatja, hogy több `FromRole` elemek, ha több szerepkör, amely képes kommunikálni a végpontok.

| Attribútum  | Típus     | Leírás |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. Engedélyezi a kommunikációt a szerepkör nevét.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) Definition séma](schema-csdef-file.md)