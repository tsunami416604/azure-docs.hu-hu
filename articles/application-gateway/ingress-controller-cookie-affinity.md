---
title: Cookie-alapú affinitás engedélyezése Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan engedélyezhető a cookie-alapú affinitás egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795978"
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
