---
title: WebSocket-kiszolgáló közzététele Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan tehet elérhetővé egy WebSocket-kiszolgálót az AK-fürtök bejövő adatvezérlési szolgáltatásával Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297832"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>WebSocket-kiszolgáló közzététele Application Gateway

A Application Gateway v2 dokumentációjában leírtaknak megfelelően [a WebSocket és a http/2 protokollok natív támogatást biztosítanak](features.md#websocket-and-http2-traffic). Vegye figyelembe, hogy mind a Application Gateway, mind a Kubernetes beáramlása esetén nincs felhasználó által konfigurálható beállítás a WebSocket-támogatás szelektív engedélyezéséhez vagy letiltásához.

Az alábbi Kubernetes-telepítési YAML a WebSocket-kiszolgáló üzembe helyezésének minimális konfigurációját mutatja be, amely megegyezik egy normál webkiszolgáló üzembe helyezésével:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Tekintettel arra, hogy minden előfeltétel teljesült, és rendelkezik egy Application Gateway Kubernetes bevezetéssel, a fenti üzembe helyezés a Application Gateway nyilvános IP-címének és a `ws.contoso.com` tartományának 80-as portján elérhető WebSocket-kiszolgálóval fog rendelkezni.

A következő cURL-parancs teszteli a WebSocket-kiszolgáló telepítését:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket-állapotú tesztek

Ha az üzemelő példány nem határozza meg explicit módon az állapot-mintavételt, Application Gateway megkísérli egy HTTP GET-t a WebSocket Server-végponton.
A kiszolgáló megvalósításának függvényében ([itt az egyik szeretjük](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) WebSocket-specifikus fejlécekre lehet szükség`Sec-Websocket-Version` (például).
Mivel Application Gateway nem ad hozzá WebSocket-fejléceket, a WebSocket-kiszolgálóról érkező Application Gateway állapot-mintavételi válasz valószínűleg a `400 Bad Request`következő lesz:.
Ennek eredményeképpen Application Gateway a hüvelyét nem `502 Bad Gateway` megfelelő állapotba fogja megjelölni, ami végül a WebSocket-kiszolgáló felhasználói számára eredményez.
Ennek elkerüléséhez előfordulhat, hogy egy HTTP GET kezelőt kell hozzáadnia az állapot-ellenőrzési kiszolgálóhoz (`/health` például a visszaadott értékhez `200 OK`).
