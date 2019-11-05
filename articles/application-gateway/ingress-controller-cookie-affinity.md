---
title: Cookie-alapú affinitás engedélyezése Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan engedélyezhető a cookie-alapú affinitás egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513574"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Cookie-alapú affinitás engedélyezése Application Gateway
Az [Azure Application Gateway dokumentációjában](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)leírtaknak megfelelően Application Gateway támogatja a cookie-alapú affinitást, ami azt jelenti, hogy a felhasználói munkamenetből egy adott kiszolgálóra irányuló, a feldolgozásra irányuló további forgalmat is átirányíthatja.

## <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
