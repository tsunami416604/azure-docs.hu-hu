---
title: Az Azure telepítés utáni feladatok OpenShift |} Microsoft Docs
description: További feladatok után egy OpenShift fürt telepítve van.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: bdfd075b9438ee12e940f3ec4fddebf467c93ca8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="post-deployment-tasks"></a>Telepítés utáni feladatok

Egy OpenShift fürt telepítése után beállíthatja a további elemeket. Ez a cikk a következőket tartalmazza:

- Azure Active Directory (Azure AD) segítségével az egyszeri bejelentkezés konfigurálása
- Naplóelemzési OpenShift figyelése konfigurálása
- Metrikák és a naplózás konfigurálása

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Egyszeri bejelentkezés konfigurálása az Azure Active Directoryval

Azure Active Directory használatára a hitelesítéshez, először meg kell létrehoznia egy az Azure AD-alkalmazás regisztrációja. Ez a folyamat két lépésből áll: az alkalmazás regisztrálása létrehozásához, és engedélyek konfigurálásához.

### <a name="create-an-app-registration"></a>Hozzon létre egy alkalmazás regisztrálása

Ezeket a lépéseket az Azure parancssori felület használatával hozza létre az alkalmazás regisztrálása és az engedélyek beállítása a grafikus felhasználói (portál). Az alkalmazás regisztrálásának létrehozása a következő öt adatot kell:

- Megjelenített név: alkalmazás regisztrációja neve (például OCPAzureAD)
- Kezdőlap: OpenShift (például konzol URL-címe https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI-azonosító: OpenShift konzol URL-címe (például https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Válasz URL-címe: Fő URL-címe és az alkalmazás regisztrálása nevére (például https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Jelszó: Biztonságos jelszó (erős jelszó használata)

A következő példa egy alkalmazás regisztrálása a fenti adatokat használatával hozza létre:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Ha a parancs végrehajtása sikeres, kap egy JSON-kimenetét hasonló:

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

Az Azure Portalon:

1.  Válassza ki **az Azure Active Directory** > **App regisztrációs**.
2.  Keresse meg az alkalmazás regisztrálása (például OCPAzureAD).
3.  Az eredmények között kattintson az alkalmazás regisztrálása.
4.  A **beállítások**, jelölje be **szükséges engedélyek**.
5.  A **szükséges engedélyek**, jelölje be **Hozzáadás**.

  ![Alkalmazás regisztrálása](media/openshift-post-deployment/app-registration.png)

6.  Kattintson az 1. lépés: Az API lehetőséget választhatja, és kattintson **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Kattintson a **válasszon** alján.

  ![Alkalmazás regisztrálása az API kiválasztása](media/openshift-post-deployment/app-registration-select-api.png)

7.  A 2. lépés: Jelölje be az engedélyeket, válassza ki **jelentkezzen be a felhasználói profil olvasása és** alatt **delegált engedélyek**, és kattintson a **kiválasztása**.

  ![Alkalmazás regisztrálása hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8.  Válassza ki **végzett**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift konfigurálása az Azure AD-alapú hitelesítés

OpenShift kíván használni az Azure AD egy hitelesítésszolgáltató konfigurálásához a /etc/origin/master/master-config.yaml fájl összes fő csomóponton kell szerkeszteni.

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

Szúrja be a következő sorokat közvetlenül az előző sorok után:

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

A bérlői azonosító található a következő parancssori paranccsal: ```az account show```

Indítsa újra a OpenShift fő services összes fő csomópontján:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift tároló Platform (OCP) minták**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift tároló Platform a fő egyetlen**

```bash
sudo systemctl restart atomic-openshift-master
```

A OpenShift konzolon ekkor megjelenik az hitelesítéshez két lehetőség közül választhat: htpasswd_auth és [App regisztrációs].

## <a name="monitor-openshift-with-log-analytics"></a>A Naplóelemzési figyelő OpenShift

A Naplóelemzési OpenShift figyeléséhez használhatja a két lehetőség közül: OMS-ügynök telepítése a Virtuálisgép-gazda, vagy az OMS-tároló. A cikkben megtudhatja, hogyan telepítheti az OMS-tárolóban.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>A Naplóelemzési OpenShift projekt létrehozása és beállítása a felhasználói hozzáférés

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Hozzon létre egy démon set yam fájlt

Hozzon létre egy fájlt ocp-omsagent.yml:

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

## <a name="create-a-secret-yaml-file"></a>Hozzon létre egy titkos yam fájlt

A titkos yam-fájl létrehozására, kétféle információt kell: napló Analytics munkaterület azonosítója és a napló Analytics megosztott kulcsát. 

Az ocp-secret.yml mintafájl a következőképpen: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

A név felülírandó wsid_data rendelkező a Base64 kódolású Naplóelemzési munkaterület azonosítója. Ezután cserélje le key_data a Base64-kódolású napló Analytics megosztott kulcsát.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>A titkos kulcs és a démont készlet létrehozása

A titkos fájl központi telepítése:

```bash
oc create -f ocp-secret.yml
```

Telepítse az OMS-ügynököt démon beállítása:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Metrikák és a naplózás konfigurálása

Az Azure Resource Manager-sablon OpenShift tároló platform engedélyezése metrikákat és naplózási bemeneti paramétereinek biztosít. A OpenShift tároló Platform Piactéri ajánlat és a OpenShift Origin Resource Manager-sablon azonban nem.

Ha használta az OCP Resource Manager-sablon és a metrikák és naplózás nem érhető el a telepítéshez szükséges idő, illetve a OCP Piactéri ajánlat használatakor lehet könnyen engedélyezése ezek bekövetkeztek. A OpenShift Origin Resource Manager-sablon használata, néhány előtti feladata.

### <a name="openshift-origin-template-pre-work"></a>Sablon előtti munkahelyi OpenShift forrása

1. SSH-port 2200 használatával első fő csomópontra.

   Példa:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Szerkessze a /etc/ansible/hosts fájlt, és adja hozzá a következő sorokat a Identity Provider (# HTPasswdPasswordIdentityProvider engedélyezése) szakasz után:

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

3. $ROUTING cserélje le a openshift_master_default_subdomain beállítás /etc/ansible/hosts ugyanabban a fájlban használt karakterlánc.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider használatban

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), a telepítés során megadott hitelesítő adatok használatával SSH. Adja ki a következő parancsot:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider nincsenek használatban

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), a telepítés során megadott hitelesítő adatok használatával SSH. Adja ki a következő parancsot:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>További lépések

- [Ismerkedés a OpenShift tároló Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Bevezetés az OpenShift Origin használatába](https://docs.openshift.org/latest/getting_started/index.html)
