---
title: Azure Cloud Services def. NetworkTrafficRules séma | Microsoft Docs
description: Ismerje meg a NetworkTrafficRules, amely korlátozza azokat a szerepköröket, amelyek hozzáférhetnek egy szerepkör belső végpontokhoz. Egy szolgáltatás-definíciós fájlban lévő szerepkörökkel kombinálja.
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
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534728"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules séma
A `NetworkTrafficRules` csomópont egy opcionális elem a szolgáltatás-definíciós fájlban, amely megadja, hogy a szerepkörök hogyan kommunikáljanak egymással. Ez korlátozza, hogy mely szerepkörök férhetnek hozzá az adott szerepkör belső végpontjaihoz. A `NetworkTrafficRules` nem önálló elem; egy szolgáltatás-definíciós fájl két vagy több szerepkörével együtt.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

> [!NOTE]
>  A `NetworkTrafficRules` csomópont csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Alapszintű szolgáltatás-definíciós séma a hálózati forgalom szabályaihoz
A hálózati forgalmi definíciókat tartalmazó szolgáltatási definíciós fájlok alapformátuma a következő.

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
A `NetworkTrafficRules` szolgáltatás definíciós fájljának csomópontja tartalmazza ezeket az elemeket, amelyeket a jelen témakör következő részeiben talál:

[NetworkTrafficRules elem](#NetworkTrafficRules)

[OnlyAllowTrafficTo elem](#OnlyAllowTrafficTo)

[Célhelyek elem](#Destinations)

[RoleEndpoint elem](#RoleEndpoint)

AllowAllTraffic elem

[WhenSource elem](#WhenSource)

[FromRole elem](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>NetworkTrafficRules elem
Az `NetworkTrafficRules` elem határozza meg, hogy mely szerepkörök kommunikálhatnak egymással egy másik szerepkörhöz tartozó végponttal. Egy szolgáltatás egy `NetworkTrafficRules` definíciót is tartalmazhat.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo elem
Az `OnlyAllowTrafficTo` elem ismerteti a cél végpontok gyűjteményét, valamint azokat a szerepköröket, amelyek kommunikálni tudnak velük. Több `OnlyAllowTrafficTo` csomópontot is megadhat.

##  <a name="destinations-element"></a><a name="Destinations"></a>Célhelyek elem
Az `Destinations` elem a RoleEndpoints gyűjteményét írja le, amely a használatával kommunikálhat.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RoleEndpoint elem
Az `RoleEndpoint` elem egy olyan szerepkörhöz tartozó végpontot ismertet, amely lehetővé teszi a kommunikációt a szolgáltatással. Több `RoleEndpoint` elemet is megadhat, ha a szerepkörhöz több végpont is van.

| Attribútum      | Típus     | Leírás |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. Annak a végpontnak a neve, amely engedélyezi a forgalmat.|
| `roleName`     | `string` | Kötelező. Annak a webes szerepkörnek a neve, amely lehetővé teszi a kommunikációt.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic elem
Az `AllowAllTraffic` elem egy olyan szabály, amely lehetővé teszi, hogy az összes szerepkör kommunikáljon a `Destinations` csomópontban definiált végpontokkal.

##  <a name="whensource-element"></a><a name="WhenSource"></a>WhenSource elem
Az `WhenSource` elem olyan szerepkörök gyűjteményét írja le, amelyek nem tudnak kommunikálni a `Destinations` csomópontban definiált végpontokkal.

| Attribútum | Típus     | Leírás |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Meghatározza a kommunikáció engedélyezésekor alkalmazandó szabályt. Az egyetlen érvényes érték jelenleg `AnyRule`.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>FromRole elem
Az `FromRole` elem meghatározza azokat a szerepköröket, amelyek kommunikálhatnak a `Destinations` csomópontban definiált végpontokkal. Több `FromRole` elemet is megadhat, ha több olyan szerepkör is van, amely képes kommunikálni a végpontokkal.

| Attribútum  | Típus     | Leírás |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. Annak a szerepkörnek a neve, amelyről engedélyezni szeretné a kommunikációt.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)




