---
title: "Az Azure utáni telepítési feladatok OpenShift |} Microsoft Docs"
description: "OpenShift utáni telepítési feladatok"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>Telepítés utáni feladatok

A OpenShift fürt telepítése után további elemek vannak, amelyek képesek. Ez a cikk a következőkre terjed ki:

- Egyszeri bejelentkezés Azure Active Directory (AAD) használó konfigurálása
- Konfigurálja az OMS OpenShift figyelése
- Metrikáinak megadása és a naplózást

## <a name="single-sign-on-using-aad"></a>Az AAD használja az egyszeri bejelentkezés

Ahhoz, hogy az AAD-hitelesítéshez, az Azure AD alkalmazás regisztrációs először kell létrehozni. Ez a folyamat tartalmaz, amely két lépésben - alkalmazás regisztrációjának létrehozása, és a majd konfigurálni az engedélyeket.

### <a name="create-app-registration"></a>Alkalmazás regisztrálása létrehozása

Az Azure parancssori felület használjuk az engedélyek beállítása az alkalmazás regisztrálása és a grafikus felhasználói felület (portál) létrehozásához. Az alkalmazás regisztrálása létrehozásához öt adatokra lesz szükség.

- Megjelenített név: Alkalmazás regisztrációja nevét (pl.: OCPAzureAD)
- Kezdőlap: OpenShift konzol URL-címe (pl.: https://masterdns343khhde.westus.cloudapp.azure.com:8443/konzol)
- URI-azonosító: OpenShift konzol URL-címe (pl.: https://masterdns343khhde.westus.cloudapp.azure.com:8443/konzol)
- Válasz URL-címe: Fő nyilvános URL-cím és a regisztrációs alkalmazásnév (pl.: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Jelszó: Biztonságos jelszó (erős jelszó használata)

Az alábbi példa létrehoz egy alkalmazás regisztrálása a fenti információk segítségével.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Ha a parancs végrehajtása sikeres, elérhetővé válik egy JSON-kimenetét hasonló:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Jegyezze fel az egy későbbi lépésben parancs által visszaadott appId tulajdonság.

Az a **Azure-portálon**:

1.  Válassza ki **az Azure Active Directory** --> **alkalmazás regisztrálása**
2.  Keresse meg az alkalmazás regisztrálása (pl.: OCPAzureAD)
3.  Eredmény elérése érdekében kattintson az alkalmazás regisztrálása
4.  A beállítások panelen válassza ki **szükséges engedélyek**
5.  A szükséges engedélyek paneljén kattintson **hozzáadása**

  ![Alkalmazás regisztrálása](media/openshift-post-deployment/app-registration.png)

6.  Kattintson az 1. lépés: az API lehetőséget választhatja, és kattintson a **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)** kattintson **válasszon** lap alján

  ![Alkalmazás regisztrálása az API kiválasztása](media/openshift-post-deployment/app-registration-select-api.png)

7.  A 2. lépés: Jelölje be az engedélyeket, válassza ki **jelentkezzen be a felhasználói profil olvasása és** alatt **delegált engedélyek** kattintson **kiválasztása**

  ![Alkalmazás regisztrálása hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8.  Kattintson a **kész**

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift konfigurálása az Azure AD-alapú hitelesítés

Egy hitelesítésszolgáltató, az Azure AD használandó OpenShift konfigurálása a **/etc/origin/master/master-config.yaml** fájl összes fő csomóponton kell szerkeszteni.

A bérlői azonosító található a következő parancssori paranccsal:

```azurecli
az account show
```

A yam fájlban található a következő sorokat:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

A fenti sorok után azonnal, szúrja be a következő sorokat:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

A bérlői azonosító található a következő parancssori paranccsal:```az account show```

Indítsa újra a OpenShift Master services összes fő csomóponton

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift tároló Platform minták**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Az egyetlen fő OpenShift tároló Platform**

```bash
sudo systemctl restart atomic-openshift-master
```

OpenShift konzolon ekkor két lehetőség hitelesítéshez - htpasswd_auth és **[App regisztrációs]**.

## <a name="monitor-openshift-with-oms"></a>Az OMS Szolgáltatáshoz figyelőt OpenShift

Az OMS OpenShift figyelési elérhető két lehetőség - OMS-ügynök telepítése a Virtuálisgép-gazda, vagy az OMS-tároló egyikével. Ez a cikk útmutatást az OMS-tároló üzembe helyezni.

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>Az OMS OpenShift projekt létrehozása és beállítása a felhasználói hozzáférés

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>Démon set yam-fájl létrehozása

Az ocp-omsagent.yml nevű fájl létrehozása.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-secret-yaml-file"></a>Titkos yam-fájl létrehozása

Ahhoz, hogy a titkos yam-fájl létrehozására, két adatra van szükség - OMS-munkaterület azonosítója és az OMS-munkaterület megosztott kulcsot. 

Az ocp-secret.yml mintafájl 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Csere wsid_data rendelkező a Base64 kódolású OMS-munkaterület azonosítója és a név felülírandó key_data rendelkező a Base64 kódolású OMS-munkaterület megosztott kulcs.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>Titkos kulcs és a démont készlet létrehozása

A titkos fájl központi telepítése

```bash
oc create -f ocp-secret.yml
```

Az OMS-ügynök démon Set telepítése

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Metrikák és a naplózás konfigurálása

A OpenShift tároló Platform (OCP) Resource Manager-sablon bemeneti paramétereket biztosít metrikák engedélyezése és a naplózást. A OpenShift tároló Platform Piactéri ajánlat és OpenShift Origin Resource Manager sablon viszont nem.

Ha a OCP Resource Manager-sablon lett megadva, és metrikákat és naplózási nem érhető el a telepítéshez szükséges idő, vagy a OCP Piactéri ajánlat lett megadva, ezeket egyszerűen engedélyezhető bekövetkeztek. Ha a OpenShift Origin Resource Manager-sablonnal, néhány előtti feladata.

### <a name="openshift-origin-template-pre-work"></a>Sablon előtti munkahelyi OpenShift forrása

SSH-kapcsolatot a az első fő csomópont port 2200 használatával

Példa

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

Szerkessze a **/etc/ansible/hosts fájl** , és adja hozzá a következő sorokat követően az Identity Provider szakasz (# HTPasswdPasswordIdentityProvider engedélyezése)

```yaml
# Setup metrics
openshift_hosted_metrics_deploy=false
openshift_metrics_cassandra_storage_type=dynamic
openshift_metrics_start_cluster=true
openshift_metrics_hawkular_nodeselector={"type":"infra"}
openshift_metrics_cassandra_nodeselector={"type":"infra"}
openshift_metrics_heapster_nodeselector={"type":"infra"}
openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

# Setup logging
openshift_hosted_logging_deploy=false
openshift_hosted_logging_storage_kind=dynamic
openshift_logging_fluentd_nodeselector={"logging":"true"}
openshift_logging_es_nodeselector={"type":"infra"}
openshift_logging_kibana_nodeselector={"type":"infra"}
openshift_logging_curator_nodeselector={"type":"infra"}
openshift_master_logging_public_url=https://kibana.$ROUTING
```

Cserélje le $ROUTING használt karakterlánc **openshift_master_default_subdomain** beállítás ugyanazon **/etc/ansible/hosts** fájl.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider használatban

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), a telepítés során megadott hitelesítő adatok használatával SSH. Adja ki a következő parancsot:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider nincsenek használatban

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), a telepítés során megadott hitelesítő adatok használatával SSH. Adja ki a következő parancsot:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés a OpenShift tároló Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Bevezetés az OpenShift Origin használatába](https://docs.openshift.org/latest/getting_started/index.html)