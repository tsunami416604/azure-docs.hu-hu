---
title: Azure Cloud Services szerepkör-séma | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: 0f0e79b462726b1aa6a953a4b8c92334d6b16492
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449090"
---
# <a name="azure-cloud-services-config-role-schema"></a>Az Azure Cloud Services konfigurációjának szerepkör-sémája

A konfigurációs fájl `Role` eleme határozza meg a szolgáltatás egyes szerepköreihez telepítendő szerepkör-példányok számát, a konfigurációs beállítások értékeit, valamint a szerepkörhöz társított tanúsítványok ujjlenyomatai megfelelnek.

További információ az Azure szolgáltatás konfigurációs sémájáról: [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md). Az Azure szolgáltatás definíciós sémájával kapcsolatos további információkért lásd: [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md).

##  <a name="Role"></a>Szerepkör elem
Az alábbi példa a `Role` elemet és annak alárendelt elemeit mutatja be.

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

Az alábbi táblázat a `Role` elem attribútumait ismerteti.

| Attribútum | Leírás |
| --------- | ----------- |
| név   | Kötelező. Megadja a szerepkör nevét. A névnek meg kell egyeznie a szolgáltatás definíciós fájljában szereplő szerepkörhöz megadott névvel.|
| vmName | Választható. Megadja a virtuális gép DNS-nevét. A névnek 10 karakternél rövidebbnek kell lennie.|

Az alábbi táblázat a `Role` elem alárendelt elemeit ismerteti.

| Elem | Leírás |
| ------- | ----------- |
| Példányok | Kötelező. A szerepkörhöz telepítendő példányok számát adja meg. A példányok számát a `count` attribútum egész számával határozzák meg.|
| Beállítás   | Választható. Megadja a beállítás nevét és értékét egy szerepkörhöz tartozó beállítások gyűjteményében. A beállítás nevét a `name` attribútum karakterlánca határozza meg, a beállítás értékét pedig a `value` attribútum karakterlánca határozza meg.|
| Tanúsítvány | Választható. A szerepkörhöz társítandó szolgáltatás tanúsítványának nevét, ujjlenyomatát és algoritmusát adja meg. A tanúsítvány nevét a `name` attribútum karakterlánca határozza meg. A tanúsítvány ujjlenyomatát olyan hexadecimális számokból álló karakterlánc határozza meg, amely nem tartalmaz szóközt a `thumbprint` attribútumhoz. A hexadecimális számoknak számjegyek és nagybetűs alfanumerikus karakterek használatával kell szerepelniük. A tanúsítvány-algoritmust a `thumbprintAlgorithm` attribútum karakterlánca határozza meg.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)