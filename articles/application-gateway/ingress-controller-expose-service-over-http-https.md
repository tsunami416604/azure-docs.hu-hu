---
title: AKS-szolgáltatás felfedése HTTP-n vagy HTTPS-en keresztül az Application Gateway használatával
description: Ez a cikk arról nyújt tájékoztatást, hogyan teheti elérhetővé az AKS-szolgáltatást HTTP-n vagy HTTPS-en keresztül az Application Gateway használatával.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795568"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>AKS-szolgáltatás felfedése HTTP-n vagy HTTPS-en keresztül az Application Gateway használatával 

Ezek az oktatóanyagok segítenek szemléltetni a [Kubernetes ingress resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) használatát egy példa Kubernetes szolgáltatás elérhetővé az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) HTTP-n vagy HTTPS-en keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Telepített `ingress-azure` kormányrúd diagram.
  - [**Zöldmezős telepítés:**](ingress-controller-install-new.md)Ha teljesen újonnan indul, olvassa el ezeket a telepítési utasításokat, amelyek ismertetik az AKS-fürt alkalmazásátjáróval történő központi telepítésének lépéseit, és telepítik az alkalmazásátjáró-vezérlőt az AKS-fürtön.
  - [**Brownfield Deployment**](ingress-controller-install-existing.md): Ha egy meglévő AKS-fürt és alkalmazásátjáró, olvassa el ezeket az utasításokat telepíteni alkalmazásátjáró be- ésbevezető vezérlő az AKS-fürt.
- Ha https-t szeretne használni ezen az alkalmazáson, szüksége lesz egy x509-es tanúsítványra és annak titkos kulcsára.

## <a name="deploy-guestbook-application"></a>Alkalmazás `guestbook` telepítése

A vendégkönyv-alkalmazás egy kanonikus Kubernetes alkalmazás, amely egy webes felhasználói felület, egy háttér- és egy Redis-adatbázis ból áll. Alapértelmezés szerint `guestbook` az alkalmazást egy porton `frontend` `80`lévő nevű szolgáltatáson keresztül teszi elérhetővé. Kubernetes ingress erőforrás nélkül a szolgáltatás nem érhető el az AKS-fürtön kívülről. Az alkalmazást és a be- és telepítési erőforrásokat http-n és HTTPS-en keresztül fogjuk használni az alkalmazás eléréséhez.

Kövesse az alábbi utasításokat a vendégkönyv-alkalmazás üzembe helyezéséhez.

1. Letöltés `guestbook-all-in-one.yaml` [innen](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Üzembe `guestbook-all-in-one.yaml` helyezés az AKS-fürtben a futással

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Most az `guestbook` alkalmazás telepítve van.

## <a name="expose-services-over-http"></a>Szolgáltatások felfedése HTTP-n keresztül

A vendégkönyv-alkalmazás felfedéséhez a következő bejövő adatokat használjuk:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Ez a be-ésbejuttatás `frontend` az `guestbook-all-in-one` alkalmazásátjáró alapértelmezett háttérrendszerként elérhetővé teszi a központi telepítés szolgáltatását.

Mentse a fenti be- `ing-guestbook.yaml`ésres erőforrást a módon.

1. Üzembe `ing-guestbook.yaml` helyezés a következő futtatásával:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Ellenőrizze a be- és écses telepítés vezérlőnaplójában a központi telepítési állapotot.

Most `guestbook` az alkalmazásnak elérhetőnek kell lennie. Ezt az Application Gateway nyilvános címének felkeresésével ellenőrizheti.

## <a name="expose-services-over-https"></a>Szolgáltatások https-en keresztüli felfedése

### <a name="without-specified-hostname"></a>Megadott állomásnév nélkül

Hostname megadása nélkül a vendégkönyv szolgáltatás elérhető lesz az összes állomás-nevek mutatva az alkalmazás átjáró.

1. A be- és visszaküldés telepítése előtt létre kell hoznia egy kubernetes titkos kulcsot a tanúsítvány és a személyes kulcs üzemeltetéséhez. Kubernetes titkos kulcsot hozhat létre a

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Adja meg a következő be-és be- és énekkövetést. A bejövő kapcsolatban adja meg a titkos `secretName` kulcsot a szakaszban.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Cserélje `<guestbook-secret-name>` le a fenti bejövő erőforrás a neve a titkos. A fenti bejövő adatokat fájlnévben `ing-guestbook-tls.yaml`tárolja.

1. Az ing-guestbook-tls.yaml telepítése a

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Ellenőrizze a be- és écses telepítés vezérlőnaplójában a központi telepítési állapotot.

Most `guestbook` az alkalmazás http-n és HTTPS-en is elérhető lesz.

### <a name="with-specified-hostname"></a>Megadott állomásnévvel

Megadhatja a állomásnevet a bejövő kapcsolaton is a TLS-konfigurációk és -szolgáltatások multiplexeléséhez.
A hostname megadásával a vendégkönyv-szolgáltatás csak a megadott állomáson lesz elérhető.

1. Adja meg a következő be-és be- és énekkövetést.
    A bejövő kapcsolatban adja meg a titkos `secretName` kulcsot a szakaszban, `hosts` és ennek megfelelően cserélje le a gazdanevet a szakaszban.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Üzembe `ing-guestbook-tls-sni.yaml` helyezés futással

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Ellenőrizze a be- és écses telepítés vezérlőnaplójában a központi telepítési állapotot.

Most `guestbook` az alkalmazás csak a megadott állomáson lesz elérhető`<guestbook.contoso.com>` HTTP és HTTPS protokollon (ebben a példában).

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

A következő be- és ress lehetővé teszi, hogy további elérési utakat adjon hozzá a be- és ébasatóba, és ezeket az elérési utakat más szolgáltatásokba irányítsa át:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
