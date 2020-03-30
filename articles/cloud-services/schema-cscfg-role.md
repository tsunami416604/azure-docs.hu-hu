---
title: Azure Cloud Services szerepkörséma | Microsoft dokumentumok
description: A szolgáltatáskonfigurációs fájl szerepkör-eleme határozza meg, hogy hány szerepkörpéldányt kell telepíteni az egyes szerepkörökhöz, konfigurációs értékekhez és tanúsítványujjlenyomatokhoz.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528421"
---
# <a name="azure-cloud-services-config-role-schema"></a>Az Azure Cloud Services konfigurációs szerepkörsémája

A `Role` konfigurációs fájl eleme határozza meg a szerepkörpéldányok számát a szolgáltatás egyes szerepköreihez, a konfigurációs beállítások értékeit és a szerepkörhöz társított tanúsítványok ujjlenyomatait.

Az Azure Service konfigurációs sémájáról további információt a [Felhőszolgáltatás (klasszikus) konfigurációs séma című témakörben talál.](schema-cscfg-file.md) Az Azure Service Definition Sémáról további információt a [Cloud Service (klasszikus) definíciós séma című témakörben talál.](schema-csdef-file.md)

##  <a name="role-element"></a><a name="Role"></a>Szerepelem
A következő példa `Role` az elemet és annak gyermekelemeit mutatja be.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Az alábbi táblázat az `Role` elem attribútumait ismerteti.

| Attribútum | Leírás |
| --------- | ----------- |
| név   | Kötelező. Megadja a szerepkör nevét. A névnek meg kell egyeznie a szolgáltatásdefiníciós fájlban szereplő szerepkörhöz megadott névvel.|
| vmName | Választható. Megadja a virtuális gép DNS-nevét. A névnek legkisebbnek kell lennie.|

Az alábbi táblázat az `Role` elem gyermekelemeit ismerteti.

| Elem | Leírás |
| ------- | ----------- |
| példányszám | Kötelező. Megadja a szerepkörhöz telepítandó példányok számát. A példányok számát az `count` attribútum egész száma határozza meg.|
| Beállítás   | Választható. Megadja a beállítás nevét és értékét egy szerepkör beállításainak gyűjteményében. A beállítás nevét az `name` attribútum karakterlánca határozza meg, a beállítási értéket `value` pedig az attribútum karakterlánca.|
| Tanúsítvány | Választható. Megadja a szerepkörhöz társítandó szolgáltatástanúsítvány nevét, ujjlenyomatát és algoritmusát. A tanúsítvány nevét az `name` attribútum karakterlánca határozza meg. A tanúsítvány ujjlenyomatát hexadecimális számok sorozata határozza meg, `thumbprint` amelyek nem tartalmaznak szóközt az attribútumhoz. A hexadecimális számokat számjegyek és nagybetűs alfa karakterek használatával kell megjeleníteni. A tanúsítványalgoritmust az `thumbprintAlgorithm` attribútum karakterlánca határozza meg.|

## <a name="see-also"></a>Lásd még:
[Felhőszolgáltatás (klasszikus) konfigurációs séma](schema-cscfg-file.md)