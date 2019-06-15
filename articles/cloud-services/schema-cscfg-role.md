---
title: Azure Cloud Services – szerepkör séma |} A Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: aa6f8a821edea6261d64bb411154e82fdf212a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130245"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services – Config Szerepkörséma

A `Role` elemet a konfigurációs fájl a szolgáltatás bármely konfigurációs beállítás értékét az egyes szerepkörökhöz telepíthető a szerepkörpéldányok számát adja meg, és a tanúsítványok ujjlenyomatait szerepkörhöz rendelt.

Az Azure szolgáltatás konfigurációs sémáját kapcsolatos további információkért lásd: [Felhőszolgáltatás (klasszikus) konfigurációs séma](schema-cscfg-file.md). Az Azure szolgáltatásdefiníciós sémában kapcsolatos további információkért lásd: [Felhőszolgáltatás (klasszikus) – definíciós séma](schema-csdef-file.md).

##  <a name="Role"></a> Szerepkör-elem
A következő példa bemutatja a `Role` elem és az alárendelt elemei.

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

A következő táblázat ismerteti a attribútumait a `Role` elemet.

| Attribútum | Leírás |
| --------- | ----------- |
| name   | Kötelező. Megadja a szerepkör nevét. Neve a szerepkör a szolgáltatásdefiníciós fájlban megadott névnek egyeznie kell.|
| vmName | Választható. Megadja a virtuális gép DNS-nevét. A névnek kell lennie a 10 karakterből állhat.|

A következő táblázat ismerteti az alárendelt elemei a `Role` elemet.

| Elem | Leírás |
| ------- | ----------- |
| példányok | Kötelező. Meghatározza a szerepkör üzembe helyezendő példányok száma. A példányok számát határozza meg az egész a `count` attribútum.|
| Beállítás   | Választható. A beállítások egy adott szerepkör esetében adja meg a beállítás nevét és értékét. Egy karakterláncot határozza meg a beállítás neve a `name` attribútum és a beállítás értéke határozza meg egy karakterláncot a `value` attribútum.|
| Tanúsítvány | Választható. Megadja a neve, ujjlenyomat és algoritmus kell lennie a szerepkörhöz társított szolgáltatási tanúsítvány. A tanúsítvány nevét határozza meg egy karakterláncot a `name` attribútum. Hexadecimális szám nem a szóközöket tartalmazó karakterláncot határozza meg a tanúsítvány ujjlenyomatát a `thumbprint` attribútum. A hexadecimális szám meg kell jelennie, számjegyek és nagybetűs alfanumerikus karaktereket tartalmazhat. Egy karakterláncot határozza meg a tanúsítvány algoritmus a `thumbprintAlgorithm` attribútum.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)