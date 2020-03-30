---
title: WebSocket-kiszolgáló felfedése az Application Gateway számára
description: Ez a cikk arról nyújt tájékoztatást, hogyan teheti elérhetővé a WebSocket-kiszolgálót az Application Gateway számára az AKS-fürtök be- és éivel rendelkező vezérlővel.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297832"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>WebSocket-kiszolgáló felfedése az Application Gateway számára

Az Application Gateway v2 dokumentációjában felvázolt [módon natív támogatást nyújt a WebSocket és a HTTP/2 protokollokhoz.](features.md#websocket-and-http2-traffic) Kérjük, vegye figyelembe, hogy mind az Application Gateway, mind a Kubernetes ingress esetében nincs felhasználó által konfigurálható beállítás a WebSocket-támogatás szelektív engedélyezéséhez vagy letiltásához.

A Kubernetes telepítési YAML az alábbi mutatja a websocket-kiszolgáló telepítéséhez használt minimális konfigurációt, amely megegyezik egy normál webkiszolgáló telepítésével:
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

Tekintettel arra, hogy az összes feltétel teljesül, és rendelkezik egy Kubernetes ingress által ellenőrzött Alkalmazásátjáró az AKS-ben, a fenti központi telepítés azt eredményezné, hogy a WebSockets-kiszolgáló elérhető portján 80-as porton az Application Gateway nyilvános IP-cím és a `ws.contoso.com` domain.

A következő cURL parancs tesztelné a WebSocket-kiszolgáló telepítését:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket állapotpróbák

Ha a központi telepítés nem határozza meg kifejezetten az állapotmintákat, az Application Gateway http GET-t kísérel meg a WebSocket-kiszolgáló végpontján.
Attól függően, hogy a szerver végrehajtása[(itt van egy szeretjük)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)WebSocket konkrét fejlécek lehet szükség (például).`Sec-Websocket-Version`
Mivel az Application Gateway nem ad hozzá WebSocket-fejléceket, az Application Gateway `400 Bad Request`állapotminta válasza a WebSocket-kiszolgálóról valószínűleg .
Ennek eredményeképpen az Application Gateway a podokat nem megfelelő `502 Bad Gateway` állapotúként jelöli meg, ami végül a WebSocket-kiszolgáló fogyasztói számára egy.
Ennek elkerülése érdekében előfordulhat, hogy hozzá kell adnia egy`/health` HTTP GET-kezelőt az állapot-ellenőrzéshez a kiszolgálóhoz (például, amely visszaadja). `200 OK`
