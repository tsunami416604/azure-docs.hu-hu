---
title: Cookie-alapú affinitás engedélyezése Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan engedélyezhető a cookie-alapú affinitás egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807971"
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
