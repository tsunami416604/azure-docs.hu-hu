---
title: Azure Cloud Services szerepkör-séma | Microsoft Docs
description: A szolgáltatás konfigurációs fájljának szerepkör eleme határozza meg, hogy az egyes szerepkörök, konfigurációs értékek és tanúsítványok ujjlenyomatai megfelelnek hány szerepkör-példány telepíthető.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528421"
---
# <a name="azure-cloud-services-config-role-schema"></a>Az Azure Cloud Services konfigurációjának szerepkör-sémája

A `Role` konfigurációs fájl eleme határozza meg a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek.

További információ az Azure szolgáltatás konfigurációs sémájáról: [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md). Az Azure szolgáltatás definíciós sémájával kapcsolatos további információkért lásd: [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a>Szerepkör elem
Az alábbi példa az `Role` elemet és annak alárendelt elemeit mutatja be.

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
| név   | Kötelező. Megadja a szerepkör nevét. A névnek meg kell egyeznie a szolgáltatás definíciós fájljában szereplő szerepkörhöz megadott névvel.|
| vmName | Választható. Megadja a virtuális gép DNS-nevét. A névnek 10 karakternél rövidebbnek kell lennie.|

Az alábbi táblázat az `Role` elem alárendelt elemeit ismerteti.

| Elem | Leírás |
| ------- | ----------- |
| példányszám | Kötelező. A szerepkörhöz telepítendő példányok számát adja meg. A példányok számát az `count` attribútum egész szám határozza meg.|
| Beállítás   | Választható. Megadja a beállítás nevét és értékét egy szerepkörhöz tartozó beállítások gyűjteményében. A beállítás nevét az `name` attribútum egy karakterlánca határozza meg, a beállítás értékét pedig egy karakterlánc adja meg az `value` attribútumhoz.|
| Tanúsítvány | Választható. A szerepkörhöz társítandó szolgáltatás tanúsítványának nevét, ujjlenyomatát és algoritmusát adja meg. A tanúsítvány nevét az `name` attribútum egy karakterlánca határozza meg. A tanúsítvány ujjlenyomatát olyan hexadecimális számokból álló karakterlánc határozza meg, amely nem tartalmaz `thumbprint` szóközt az attribútumhoz. A hexadecimális számoknak számjegyek és nagybetűs alfanumerikus karakterek használatával kell szerepelniük. A tanúsítvány-algoritmust az `thumbprintAlgorithm` attribútum karakterlánca határozza meg.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)