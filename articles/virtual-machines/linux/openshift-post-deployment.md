---
title: OpenShift az Azure-telepítés utáni feladatok |} A Microsoft Docs
description: Az OpenShift fürt után további feladatai lett telepítve.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
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
ms.openlocfilehash: d400512c2e96e0e24bbf965b2e201adf92ccbb0f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434891"
---
# <a name="post-deployment-tasks"></a>Üzembe helyezés utáni feladatok

Miután az OpenShift fürtöt telepít, konfigurálhatja úgy a további elemek. Ez a cikk a következőket tartalmazza:

- Az Azure Active Directory (Azure AD) használatával az egyszeri bejelentkezés konfigurálása
- OpenShift figyelése a Log Analytics konfigurálása
- Metrikák és naplózás konfigurálása

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Az Azure Active Directoryval egyszeri bejelentkezés konfigurálása

Azure Active Directory-hitelesítéshez használandó, először hozzon létre egy Azure AD-alkalmazás regisztrációjának. Ez a folyamat két lépésből áll: az alkalmazás regisztrációját létrehozását és engedélyek konfigurálását.

### <a name="create-an-app-registration"></a>Hozzon létre egy alkalmazásregisztráció

Ezeket a lépéseket az Azure CLI használatával hozzon létre az alkalmazás regisztrációját, és a grafikus felhasználói Felülettel (portál) állítsa be az engedélyeket. Szeretne létrehozni az alkalmazás regisztrációját, az alábbi öt adatokra van szükség:

- Megjelenített név: alkalmazás regisztrációja nevét (például OCPAzureAD)
- Kezdőlap: OpenShift-konzol URL-címe (például https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URI-azonosító: OpenShift konzol URL-címe (például https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Válasz URL-cím: Fő nyilvános URL-CÍMÉT és az alkalmazás regisztrációs nevét (például https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Jelszó: Biztonságos jelszó (erős jelszó használata)

A következő példában létrehozunk egy alkalmazásregisztráció az előző információk alapján:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Ha a parancs végrehajtása sikeres, megjelenik egy JSON-kimenetet hasonló:

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
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Jegyezze fel az appId-tulajdonság az egy későbbi lépésben a parancs által visszaadott.

Az Azure Portalon:

1.  Válassza ki **az Azure Active Directory** > **Alkalmazásregisztráció**.
2.  Keresse meg az alkalmazás regisztrációját (például OCPAzureAD).
3.  Az eredmények között kattintson az alkalmazás regisztrációját.
4.  A **beállítások**válassza **szükséges engedélyek**.
5.  A **szükséges engedélyek**válassza **Hozzáadás**.

  ![Alkalmazásregisztráció](media/openshift-post-deployment/app-registration.png)

6.  Kattintson az 1. lépés: Válassza ki az API-t, és kattintson a **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Kattintson a **kiválasztása** alján.

  ![Alkalmazásregisztráció kijelölt API](media/openshift-post-deployment/app-registration-select-api.png)

7.  A 2. lépés: Válassza ki az engedélyeket, jelölje be **bejelentkezés és felhasználói profil olvasása** alatt **delegált engedélyek**, és kattintson a **válassza**.

  ![Alkalmazásregisztráció-hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8.  Válassza a **Done** (Kész) lehetőséget.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift az Azure AD-hitelesítés konfigurálása

OpenShift az Azure AD-hitelesítési szolgáltató használatára konfigurálja, hogy a /etc/origin/master/master-config.yaml fájlt az összes fő csomóponttal kell szerkeszteni.

Keresse meg a bérlő Azonosítóját a következő CLI-parancs használatával:

```azurecli
az account show
```

A yaml-fájlt keresse meg a következő sorokat:

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

Illessze be a következő sorokat a megelőző sorok után azonnal:

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

Keresse meg a bérlő Azonosítóját a következő CLI-parancs használatával: ```az account show```

Indítsa újra az összes fő csomóponton az OpenShift fő szolgáltatások:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**Az OpenShift Container Platform (OCP) minták**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**A fő egyetlen OpenShift Tárolóplatform**

```bash
sudo systemctl restart atomic-openshift-master
```

Az OpenShift-konzolon, most már két lehetőség jelenik meg a hitelesítéshez: htpasswd_auth és [Alkalmazásregisztráció].

## <a name="monitor-openshift-with-log-analytics"></a>A figyelő az OpenShift a Log Analytics használatával

OpenShift Log Analytics szolgáltatással figyelheti, két lehetőség egyikét használhatja: Virtuálisgép-gazda, vagy az OMS-tároló az OMS-ügynök telepítését. Ez a cikk az OMS-tároló üzembe helyezését ismerteti.

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>OpenShift projekt létrehozása a Log Analytics és a felhasználói hozzáférés beállítása

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Hozzon létre egy démon set yaml-fájlt

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

## <a name="create-a-secret-yaml-file"></a>Titkos kód yaml-fájl létrehozása

A titkos yaml-fájl létrehozásához szükséges kétféle információra: Log Analytics-munkaterület Azonosítójára és a Log Analytics munkaterület megosztott kulcsot. 

A következő egy minta ocp-secret.yml fájlt: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Cserélje le wsid_data a Base64-kódolású Log Analytics-munkaterület-azonosítót. Ezután cserélje le key_data a Base64-kódolású Log Analytics munkaterület megosztott kulcsot.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>A titkos kulcs és a démont készlet létrehozása

A titkos kód fájlját üzembe:

```bash
oc create -f ocp-secret.yml
```

Helyezze üzembe az OMS-ügynök démon beállítása:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Metrikák és naplózás konfigurálása

Az Azure Resource Manager-sablon az OpenShift Tárolóplatform mérőszámainak engedélyezése és a naplózás a bemeneti paramétereket biztosít. Az OpenShift Container Platform Piactéri ajánlat és az OpenShift Origin Resource Manager-sablon viszont nem.

Ha követte az OCP Resource Manager-sablon és a metrikák és naplózás a telepítés nem engedélyezett, vagy az OCP Piactéri ajánlat használata esetén biztos lehet egyszerűen engedélyezése ezek után az a tény. Az OpenShift Origin Resource Manager-sablon használata, néhány előtti munkahelyi szükség.

### <a name="openshift-origin-template-pre-work"></a>Az OpenShift Origin sablon előtti munka

1. Az SSH-port 2200 használatával az első fő csomópontot.

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

3. A karakterlánc, amellyel a openshift_master_default_subdomain beállítás /etc/ansible/hosts ugyanebben a fájlban cserélje le a $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Használja az Azure Felhőszolgáltató

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), az SSH a telepítés során megadott hitelesítő adatok használatával. A következő parancs kiadásával:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Az Azure Cloud-szolgáltató nincs használatban

Az első fő csomópont (forrás) vagy a megerősített csomópont (OCP), az SSH a telepítés során megadott hitelesítő adatok használatával. A következő parancs kiadásával:

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>További lépések

- [Ismerkedés az OpenShift Tárolóplatform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Bevezetés az OpenShift Origin használatába](https://docs.openshift.org/latest/getting_started/index.html)
