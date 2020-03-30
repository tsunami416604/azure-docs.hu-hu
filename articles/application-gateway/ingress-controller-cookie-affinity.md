---
title: Cookie-alapú affinitás engedélyezése az Application Gateway-rel
description: Ez a cikk acookie-alapú affinitás engedélyezéséről nyújt tájékoztatást az Application Gateway-rel.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795978"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Cookie-alapú affinitás engedélyezése alkalmazásátjáróval
Az [Azure Application Gateway dokumentációjában](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)vázolt a Application Gateway támogatja a cookie-alapú affinitást, ami azt jelenti, hogy a felhasználói munkamenetből származó további forgalmat ugyanarra a kiszolgálóra irányíthatja feldolgozásra.

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
