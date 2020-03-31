---
title: LetsEncrypt.org tanúsítványok használata az Application Gateway alkalmazással
description: Ez a cikk arról nyújt tájékoztatást, hogy miként szerezhet be tanúsítványt LetsEncrypt.org, és hogyan használhatja azt az Alkalmazásátjáró AKS-fürtökhöz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957982"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Tanúsítványok használata LetsEncrypt.org alkalmazásátjáróval AKS-fürtökhöz

Ez a szakasz úgy konfigurálja az AKS-t, hogy [kihasználja LetsEncrypt.org,](https://letsencrypt.org/) és automatikusan beszerezze a tartományt egy TLS/SSL tanúsítványt. A tanúsítvány az Application Gateway alkalmazásátjáróra lesz telepítve, amely SSL/TLS-végződést hajt végre az AKS-fürtön. Az itt leírt beállítás a [cert-manager](https://github.com/jetstack/cert-manager) Kubernetes bővítményt használja, amely automatizálja a tanúsítványok létrehozását és kezelését.

Kövesse az alábbi lépéseket a [cert-manager](https://docs.cert-manager.io) meglévő AKS-fürtre való telepítéséhez.

1. Helm diagram

    Futtassa a következő `cert-manager` parancsfájlt a helm diagram telepítéséhez. Ez:

    - hozzon `cert-manager` létre egy új névteret az AKS-en
    - hozzon létre a következő CRD-ket: Tanúsítvány, Kihívás, Fürtkibocsátó, Kiállító, Rendelés
    - cert-manager diagram telepítése [(docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer erőforrás

    Erőforrás `ClusterIssuer` létrehozása. `cert-manager` Az aláírt tanúsítványok megszerzésének helye szerinti `Lets Encrypt` hitelesítésszolgáltatót kell képviselnie.

    A nem névszerint használt `ClusterIssuer` erőforrás használatával a cert-manager olyan tanúsítványokat állít ki, amelyek több névtérből is felhasználhatók. `Let’s Encrypt`az ACME protokoll segítségével ellenőrzi, hogy ön szabályoz-e egy adott tartománynevet, és tanúsítványt állít ki Önnek. A tulajdonságok konfigurálásával `ClusterIssuer` kapcsolatos további részletek [itt.](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer`a `cert-manager` teszteléshez használt `Lets Encrypt` átmeneti környezet (a böngésző/ügyfél megbízhatósági tárolóiban nem található főtanúsítvány) használatával kell tanúsítványokat kiadni.

    Az alábbi YAML alapértelmezett kihívástípusa a `http01`. Az egyéb kihívásokat a [letsencrypt.org - Challenge Types](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Frissítés `<YOUR.EMAIL@ADDRESS>` az alábbi YAML-ben

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Alkalmazás telepítése

    Hozzon létre egy be- `guestbook` éselődési erőforrást az alkalmazás elérhetővé teszi az alkalmazásátjáró használatával a Lets Encrypt Certificate használatával.

    Győződjön meg arról, hogy az Application Gateway nyilvános fronthálózati IP-konfigurációval rendelkezik DNS-névvel (vagy az alapértelmezett `azure.com` tartomány használatával, vagy egy `Azure DNS Zone` szolgáltatás kiépítése, és rendelje hozzá a saját egyéni tartomány).
    Vegye figyelembe a `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`jegyzetet, amely arra utasítja a tanúsítványkezelőt, hogy dolgozza fel a címkézett ingress erőforrást.

    > [!IMPORTANT] 
    > Frissítés `<PLACEHOLDERS.COM>` az alábbi YAML-ben a saját tartományával (vagy az Application Gateway-rel, például "kh-aks-ingress.westeurope.cloudapp.azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Néhány másodperc múlva elérheti `guestbook` a szolgáltatást az Application Gateway HTTPS URL-címén keresztül az automatikusan kiállított **átmeneti** `Lets Encrypt` tanúsítvány használatával.
    A böngésző jein figyelmeztetheti az érvénytelen tanúsítványra. Az átmeneti tanúsítványt `CN=Fake LE Intermediate X1`a. Ez azt jelzi, hogy a rendszer a várt módon működött, és készen áll a termelési tanúsítványra.

4. Gyártási igazolás

    Az átmeneti tanúsítvány sikeres beállítása után átválthat egy éles ACME-kiszolgálóra:
    1. Cserélje le a be- ésécsoport-erőforrás átmeneti megjegyzését a következőkre:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Törölje az `ClusterIssuer` előző lépésben létrehozott meglévő átmeneti állapotot, és hozzon létre egy újat az ACME-kiszolgáló nak a fenti Fürtkiállító YAML-ről való lecserélésével`https://acme-v02.api.letsencrypt.org/directory`

5. Tanúsítvány lejárata és megújítása

    A `Lets Encrypt` tanúsítvány lejárta `cert-manager` előtt automatikusan frissíti a tanúsítványt a Kubernetes titkos tárolóban. Ezen a ponton az Application Gateway ingress controller alkalmazza a frissített titkos hivatkozott a bejövő adatok at az Application Gateway konfigurálása érdekében használt titkos adatkapcsolatban.
