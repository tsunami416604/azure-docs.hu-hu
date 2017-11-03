---
title: "Azure Cloud Services szerepkör séma |} Microsoft Docs"
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: "12"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: c25e7d139c7b7fd7c5da6bde8cfb9050eec8a88e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services – Config szerepkör séma

A `Role` elemet a konfigurációs fájl központi telepítése az egyes szerepkörökhöz, a szolgáltatás, a konfigurációs beállítások értékének a szerepkörpéldányok számát adja meg, és a tanúsítványok ujjlenyomatait szerepkörhöz rendelt.

Az Azure szolgáltatás konfigurációs sémán kapcsolatos további információkért lásd: [Felhőszolgáltatás (klasszikus) konfigurációs séma](schema-cscfg-file.md). Az Azure szolgáltatás definíciós séma kapcsolatos további információkért lásd: [Felhőszolgáltatás (klasszikus) Definition séma](schema-csdef-file.md).

##  <a name="Role"></a>Szerepkör elem
Az alábbi példa azt mutatja meg a `Role` elem és gyermekelemeinek.

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

A következő táblázat ismerteti az attribútumai a `Role` elemet.

| Attribútum | Leírás |
| --------- | ----------- |
| név   | Kötelező. Adja meg annak a szerepkörnek a nevét. A nevének egyeznie kell a szerepkörhöz a szolgáltatásdefiníciós fájlban megadott névvel.|
| vmName | Választható. Megadja a DNS-nevet a virtuális gép. A név 10 karakterből kell állnia, vagy kisebb.|

A következő táblázat ismerteti az alárendelt elemei a `Role` elemet.

| Elem | Leírás |
| ------- | ----------- |
| Példányok | Kötelező. Meghatározza, hogy a szerepkör telepítése számát. Egy egész számot határozza meg a példányok száma a `count` attribútum.|
| Beállítás   | Választható. Adja meg a beállítás neve és értéke a szerepkör beállításainak gyűjteménye. Egy karakterláncot határozza meg a beállítás neve a `name` attribútum és a beállítás értéke határozza meg egy karakterláncot a `value` attribútum.|
| Tanúsítvány | Választható. Megadja a neve, a ujjlenyomat és az algoritmus legyen a szerepkörhöz társított szolgáltatás tanúsítvány. Egy karakterláncot határozza meg a tanúsítvány neve a `name` attribútum. Hexadecimális szám nem a szóközöket tartalmazó karakterláncot határozza meg a tanúsítvány ujjlenyomatát a `thumbprint` attribútum. A hexadecimális szám meg kell jelennie, számjegyek és a nagybetűk alfanumerikus karaktereket tartalmazhat. Egy karakterláncot határozza meg a tanúsítvány algoritmus a `thumbprintAlgorithm` attribútum.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)