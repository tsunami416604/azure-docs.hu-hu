---
title: DNS-továbbítás konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
description: DNS-továbbítás konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232632"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>DNS-továbbítás konfigurálása Azure Red Hat OpenShift 4 fürtön

A DNS-továbbítás Azure Red Hat OpenShift-fürtön való konfigurálásához módosítania kell a DNS-kezelőt. Ez a módosítás lehetővé teszi, hogy az alkalmazás-hüvelyek a fürtön belül fussanak a fürtön kívüli privát DNS-kiszolgálón tárolt nevek feloldásához. Ezeket a lépéseket a 4,3-es OpenShift [itt](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)találja.

Ha például a *. example.com összes DNS-kérelmét továbbítani kívánja egy DNS-kiszolgáló 192.168.100.10, az operátor konfigurációját a következő futtatásával módosíthatja:
 
```bash
oc edit dns.operator/default
```
 
Ez elindítja a szerkesztőt, és a következővel cserélhető le `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="next-steps"></a>További lépések
A DNS-továbbítással kapcsolatos további információkért tekintse meg a következőt: [OpenShift 4,3.](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)