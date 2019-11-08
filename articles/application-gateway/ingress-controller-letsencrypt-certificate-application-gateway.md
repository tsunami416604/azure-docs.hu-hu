---
title: LetsEncrypt.org-tanúsítványok használata Application Gateway
description: Ez a cikk azt ismerteti, hogyan szerezhet be tanúsítványt a LetsEncrypt.org, és hogyan használhatja azt a Application Gateway AK-fürtökhöz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2e91a888d0dc98a4f94b956e15336d75291f733e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795921"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>LetsEncrypt.org-t használó tanúsítványok használata a Application Gateway AK-fürtökhöz

Ez a szakasz úgy konfigurálja az AK-t, hogy kihasználja a [LetsEncrypt.org](https://letsencrypt.org/) , és automatikusan beszerezze a tartományhoz tartozó TLS/SSL-tanúsítványt. A tanúsítvány Application Gateway lesz telepítve, amely SSL/TLS-megszakítást hajt végre az AK-fürtön. Az itt ismertetett beállítás a [CERT-Manager](https://github.com/jetstack/cert-manager) Kubernetes bővítményt használja, amely automatizálja a tanúsítványok létrehozását és felügyeletét.

Az alábbi lépéseket követve telepítse a [tanúsítvány-kezelőt](https://docs.cert-manager.io) a meglévő AK-fürtre.

1. Helm-diagram

    Futtassa az alábbi szkriptet a `cert-manager` Helm diagram telepítéséhez. Ez a következő lesz:

    - hozzon létre egy új `cert-manager` névteret az AK-ban
    - hozza létre a következő CRDs: tanúsítvány, kérdés, ClusterIssuer, kiállító, rendelés
    - a CERT-Manager diagram telepítése (a docs.cert-manager.io-ből [)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

    `ClusterIssuer` erőforrás létrehozása. A `cert-manager` ahhoz szükséges, hogy az `Lets Encrypt` hitelesítésszolgáltató legyen, ahol az aláírt tanúsítványok beszerezhetők.

    A nem névteret `ClusterIssuer` erőforrás használatával a tanúsítvány-kezelő olyan tanúsítványokat állít ki, amelyek több névtérből is felhasználhatók. `Let’s Encrypt` az ACME protokollt használva ellenőrzi, hogy egy adott tartománynevet és egy tanúsítványt ad ki. A `ClusterIssuer` tulajdonságainak konfigurálásáról [itt talál](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)további információt. `ClusterIssuer` arra utasítja a `cert-manager`ot, hogy a teszteléshez használt `Lets Encrypt` átmeneti környezet használatával tanúsítványokat állítson ki (a főtanúsítvány nem található meg a böngésző-és az ügyfél-megbízhatósági tárolóban).

    Az alábbi YAML lévő alapértelmezett Challenge típus `http01`. Egyéb kihívások dokumentálva vannak a [Letsencrypt.org típusaival](https://letsencrypt.org/docs/challenge-types/) kapcsolatban

    > [!IMPORTANT] 
    > `<YOUR.EMAIL@ADDRESS>` frissítése az alábbi YAML

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

3. Alkalmazás üzembe helyezése

    Hozzon létre egy bejövő erőforrást, hogy a Application Gateway használatával tegye elérhetővé a `guestbook` alkalmazást a tanúsítvány titkosításának lehetővé tétele érdekében.

    Győződjön meg arról, hogy Application Gateway rendelkezik egy DNS-névvel rendelkező nyilvános előtérbeli IP-konfigurációval (az alapértelmezett `azure.com` tartomány használatával vagy egy `Azure DNS Zone` szolgáltatás kiépítésével és saját egyéni tartományának hozzárendelésével).
    Jegyezze fel a Megjegyzés `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, amely azt jelzi, hogy a tanúsítvány-kezelő feldolgozza a címkézett bejövő erőforrásokat.

    > [!IMPORTANT] 
    > Frissítse `<PLACEHOLDERS.COM>` az alábbi YAML a saját tartományával (vagy a Application Gateway egy, például "kh-aks-ingress.westeurope.cloudapp.azure.com")

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

    Néhány másodperc elteltével a Application Gateway HTTPS URL-címen keresztül férhet hozzá a `guestbook` szolgáltatáshoz az automatikusan kiadott **átmeneti** `Lets Encrypt` tanúsítvány használatával.
    Előfordulhat, hogy a böngésző egy érvénytelen hitelesítésszolgáltatótól figyelmeztet. Az átmeneti tanúsítványt a `CN=Fake LE Intermediate X1`állítja ki. Ez azt jelzi, hogy a rendszer a vártnak megfelelően működött, és készen áll az éles tanúsítványra.

4. Éles tanúsítvány az előkészítési tanúsítvány sikeres beállítása után az éles környezetű ACME kiszolgálóra válthat:
    1. Cserélje le az előkészítési jegyzetet a bejövő erőforrásra a következővel: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Törölje az előző lépésben létrehozott meglévő előkészítési `ClusterIssuer`, és hozzon létre egy újat úgy, hogy lecseréli az ACME-kiszolgálót a fenti ClusterIssuer-YAML az `https://acme-v02.api.letsencrypt.org/directory`

5. A tanúsítvány lejárata és megújítása az `Lets Encrypt`-tanúsítvány lejárta előtt `cert-manager` automatikusan frissíti a tanúsítványt a Kubernetes titkos tárolóban. Ezen a Application Gateway ponton a bejövő adatforgalom-vezérlő a Application Gateway konfigurálásához az általa használt bejövő erőforrások által hivatkozott frissített titkos kulcsot fogja alkalmazni.
