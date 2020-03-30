---
title: Azure Cloud Services Def. NetworkTrafficRules séma | Microsoft dokumentumok
description: Ismerje meg a NetworkTrafficRules, amely korlátozza a szerepkörök, amelyek hozzáférhetnek a belső végpontok egy szerepkör. Egy szolgáltatásdefiníciós fájlszerepkörökkel kombinálva.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534728"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Az Azure Cloud Services definíciója HálózatiTrafficRules séma
A `NetworkTrafficRules` csomópont a szolgáltatásdefiníciós fájl egy választható eleme, amely meghatározza, hogy a szerepkörök hogyan kommunikálnak egymással. Korlátozza, hogy mely szerepkörök férhetnek hozzá az adott szerepkör belső végpontjaihoz. Az `NetworkTrafficRules` nem önálló elem; egy szolgáltatásdefiníciós fájlban két vagy több szerepkörrel kombinálódik.

A szolgáltatásdefiníciós fájl alapértelmezett kiterjesztése .csdef.

> [!NOTE]
>  A `NetworkTrafficRules` csomópont csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Alapvető szolgáltatásdefiníciós séma a hálózati forgalmi szabályokhoz
A hálózati forgalom definícióit tartalmazó szolgáltatásdefiníciós fájl alapformátuma a következő.

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

## <a name="schema-elements"></a>Sémaelemek
A `NetworkTrafficRules` szolgáltatásdefiníciós fájl csomópontja tartalmazza ezeket az elemeket, amelyeket a témakör későbbi szakaszai részletesen ismertetett:

[NetworkTrafficRules elem](#NetworkTrafficRules)

[OnlyAllowTrafficTo elem](#OnlyAllowTrafficTo)

[Úti célok elem](#Destinations)

[RoleEndpoint elem](#RoleEndpoint)

AllowAllTraffic elem

[WhenSource elem](#WhenSource)

[FromRole elem](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>NetworkTrafficRules elem
Az `NetworkTrafficRules` elem határozza meg, hogy mely szerepkörök kommunikálhatnak egy másik szerepkör melyik végpontjával. Egy szolgáltatás egy `NetworkTrafficRules` definíciót tartalmazhat.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo elem
Az `OnlyAllowTrafficTo` elem a célvégpontok és a velük kommunikáló szerepkörök gyűjteményét írja le. Több `OnlyAllowTrafficTo` csomópontot is megadhat.

##  <a name="destinations-element"></a><a name="Destinations"></a>Úti célok elem
Az `Destinations` elem a RoleEndpoints gyűjteményét írja le, mint amivel kommunikálható.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RoleEndpoint elem
Az `RoleEndpoint` elem egy szerepkör végpontját írja le, amely lehetővé teszi a kommunikációt. Több `RoleEndpoint` elemet is megadhat, ha egynél több végpont van a szerepkörben.

| Attribútum      | Típus     | Leírás |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. Annak a végpontnak a neve, amelybe a forgalmat engedélyezni szeretné.|
| `roleName`     | `string` | Kötelező. A kommunikációt engedélyezni lehetővé lehetővé.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic elem
Az `AllowAllTraffic` elem egy olyan szabály, amely lehetővé teszi, `Destinations` hogy minden szerepkör kommunikáljon a csomópontban meghatározott végpontokkal.

##  <a name="whensource-element"></a><a name="WhenSource"></a>WhenSource elem
Az `WhenSource` elem a szerepkörök gyűjteményét írja le, mint `Destinations` a csomópontban definiált végpontokkal való kommunikáció.

| Attribútum | Típus     | Leírás |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Megadja a kommunikáció engedélyezésekor alkalmazandó szabályt. Az egyetlen érvényes `AnyRule`érték jelenleg .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>FromRole elem
Az `FromRole` elem határozza meg azokat a szerepköröket, `Destinations` amelyek kommunikálhatnak a csomópontban definiált végpontokkal. Több `FromRole` elemet is megadhat, ha egynél több olyan szerepkör van, amely képes kommunikálni a végpontokkal.

| Attribútum  | Típus     | Leírás |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. A kommunikációt engedélyező szerepkör neve.|

## <a name="see-also"></a>Lásd még:
[Felhőszolgáltatás (klasszikus) definícióséma](schema-csdef-file.md)




