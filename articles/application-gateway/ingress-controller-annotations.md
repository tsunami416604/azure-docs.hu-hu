---
title: Alkalmazásátjáró be- és visszaszólítási vezérlőjének jegyzetei
description: Ez a cikk az Application Gateway ingress controllerre jellemző jegyzetek dokumentációját tartalmazza.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335829"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Az Alkalmazásátjáró be- és élesztirtő jének jegyzetei 

## <a name="introductions"></a>Bemutatkozások

A Kubernetes ingress erőforrás tetszőleges kulcs/érték párral jegyzetelhető. Az AGIC az Application Gateway funkcióinak programozásához szükséges jegyzetekre támaszkodik, amelyek a be- és é-vissza YAML-en keresztül nem konfigurálhatók. A bejövő támadások minden HTTP-beállításra, háttérkészletekre és bejövő erőforrásokból származó figyelőkre vonatkozik.

## <a name="list-of-supported-annotations"></a>A támogatott jegyzetek listája

Ahhoz, hogy az AGIC egy ingress erőforrást megfigyelhessen, azt **a.** `kubernetes.io/ingress.class: azure/application-gateway` Az AGIC csak ekkor működik együtt a kérdéses ingress erőforrással.

| Jegyzetkulcs | Érték típusa | Alapértelmezett érték | Megengedett értékek
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(másodperc) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(másodperc) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Háttérelérési út előtagja

Ez a feliratozás lehetővé teszi, hogy a bejövő erőforrásokban megadott háttérelérési út újraírható legyen az ebben a jegyzetben megadott előtaggal. Ez lehetővé teszi a felhasználók számára, hogy tegye elérhetővé azokat a szolgáltatásokat, amelyek végpontjai eltérnek a végpontnevek, amelyek egy bejövő erőforrásban lévő szolgáltatás elérhetővé tesznek.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
A fenti példában egy jegyzeteléssel `go-server-ingress-bkprefix` `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` elnevezett bejövő erőforrást határoztunk meg. A jegyzet megmondja az alkalmazásátjárónak, hogy hozzon létre egy HTTP-beállítást, amelynek elérési útelőtagja felülírja a elérési `/hello` `/test/`utat.

> [!NOTE] 
> A fenti példában csak egy szabály van definiálva. A jegyzetek azonban a teljes be- éselő-erőforrásra vonatkoznak, így ha egy felhasználó több szabályt határozott meg, a háttérelérési út előtagja minden megadott elérési úthoz be lesz állítva. Így ha a felhasználó különböző szabályokat szeretne különböző elérési út előtagokkal (még ugyanazon a szolgáltatásesetében is), akkor különböző be- ésékérnék különböző adatokat kell meghatározniuk.

## <a name="tls-redirect"></a>TLS átirányítás

Az Application Gateway [beállítható úgy,](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) hogy automatikusan átirányítsa a HTTP URL-címeket a HTTPS-megfelelőikre. Ha ez a jegyzet jelen van, és a TLS megfelelően van konfigurálva, a Kubernetes ingress vezérlő létrehoz egy [átirányítási konfigurációval rendelkező útválasztási szabályt,](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) és alkalmazza a módosításokat az Application Gateway-re. A létrehozott átirányítás `301 Moved Permanently`HTTP lesz.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Kapcsolat kiürítése

`connection-draining`: Ez a megjegyzés lehetővé teszi a felhasználók számára, hogy meghatározzák, hogy engedélyezik-e a kapcsolat kiürítését.
`connection-draining-timeout`: Ez a megjegyzés lehetővé teszi a felhasználók számára, hogy adja meg az időtúltöltés, amely után az Application Gateway leállítja a kérelmeket, hogy a kiürítési háttérvégpont.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Ez a jegyzet lehetővé teszi annak megadását, hogy engedélyezze-e a cookie-alapú affinitást.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Kérelem időtúllépése

Ez a kommentár lehetővé teszi a kérelem időtúlnapjának megadását másodpercben, amely után az Application Gateway sikertelen lesz a kérés, ha nem érkezik válasz.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Privát IP-cím használata

Ez a jegyzet lehetővé teszi számunkra, hogy adja meg, hogy tegye ki ezt a végpontot a privát IP-alkalmazásátjáró.

> [!NOTE]
> * Az Application Gateway nem támogat több IP-t ugyanazon a porton (például: 80/443). Bejövő bejövő kapcsolaton keresztül, és `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` egy másik a bekapcsolva `appgw.ingress.kubernetes.io/use-private-ip: "false"` hatására AGIC nem frissíti az Application Gateway.
> * Az application gateway, amely nem rendelkezik privát IP-cím, a rendszer figyelmen kívül `appgw.ingress.kubernetes.io/use-private-ip: "true"` hagyja a be- és be- és be- és be- és behatással. Ez tükröződni fog a vezérlő naplók és bejövő események a bejövő `NoPrivateIP` adatok figyelmeztetéssel.


### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Háttér-protokoll

Ez a jegyzet lehetővé teszi számunkra, hogy adja meg a protokollt, amely et az Application Gateway kell használnia, miközben beszél a podok. Támogatott protokollok: `http`,`https`

> [!NOTE]
> * Bár az önaláírt tanúsítványok támogatottak az Application `https` Gateway,jelenleg, AGIC csak akkor támogatja, ha podok használata tanúsítvány által aláírt egy jól ismert hitelesítésszolgáltató által aláírt.
> * Győződjön meg arról, hogy ne használja a 80-as portot HTTPS-lel és a 443-as portot HTTP-vel a podokon.

### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```