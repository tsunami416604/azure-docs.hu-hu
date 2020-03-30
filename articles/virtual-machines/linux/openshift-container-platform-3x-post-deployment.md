---
title: OpenShift Container Platform 3.11 az Azure üzembe helyezés utáni feladataiban
description: További feladatok az OpenShift Container Platform 3.11-es fürt telepítését követően.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1635589b282dc33f6a1e9c2552dc8a73c67b9004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294751"
---
# <a name="post-deployment-tasks"></a>Telepítés utáni feladatok

Az OpenShift-fürt telepítése után további elemeket is konfigurálhat. Ez a cikk a következőket tartalmazza:

- Egyszeri bejelentkezés konfigurálása az Azure Active Directory (Azure AD) használatával
- Az Azure Monitor naplóinak konfigurálása az OpenShift figyelésére
- A mérőszámok és a naplózás konfigurálása
- Az Open Service Broker for Azure (OSBA) telepítése

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Egyszeri bejelentkezés konfigurálása az Azure Active Directory használatával

Az Azure Active Directory hitelesítéshez való használatához először létre kell hoznia egy Azure AD-alkalmazás regisztrációját. Ez a folyamat két lépést foglal magában: az alkalmazásregisztráció létrehozása és az engedélyek konfigurálása.

### <a name="create-an-app-registration"></a>Alkalmazásregisztráció létrehozása

Ezek a lépések az Azure CLI segítségével hozza létre az alkalmazás regisztrációját, és a GUI (portál) az engedélyek beállításához. Az alkalmazásregisztráció létrehozásához a következő öt adatra van szükség:

- Megjelenítendő név: Alkalmazás regisztrációs neve (például OCPAzureAD)
- Kezdőlap: OpenShift konzol URL-címe `https://masterdns343khhde.westus.cloudapp.azure.com/console`(például )
- Azonosító URI: OpenShift konzol URL-címe (például `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- Válasz URL-címe: A nyilvános fő URL-cím és az alkalmazás regisztrációs neve (például `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`)
- Jelszó: Biztonságos jelszó (erős jelszó használata)

A következő példa az előző adatok használatával hoz létre egy alkalmazásregisztrációt:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Ha a parancs sikeres, a json-kimenet a következőhöz hasonló:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Vegye figyelembe a parancsból egy későbbi lépésre visszaadott appId tulajdonságot.

Az Azure Portalon:

1. Válassza az **Azure Active Directory** > **alkalmazásregisztráció lehetőséget.**
2. Keresse meg az alkalmazás regisztrációját (például OCPAzureAD).
3. Az eredmények között kattintson az alkalmazás regisztrációjára.
4. A **Beállítások csoportban**válassza a **Kötelező engedélyek lehetőséget.**
5. A **Szükséges engedélyek csoportban**válassza a **Hozzáadás**lehetőséget.

   ![Alkalmazás regisztrációja](media/openshift-post-deployment/app-registration.png)

6. Kattintson az **1.** Kattintson alul a **Kijelölés** gombra.

   ![Alkalmazásregisztrációs alkalmazás kiválasztása API](media/openshift-post-deployment/app-registration-select-api.png)

7. 2. lépés: Válassza az Engedélyek lehetőséget, válassza **a Bejelentkezés és a felhasználói profil olvasása** lehetőséget a **Delegált engedélyek csoportban,** majd kattintson a Kijelölés **gombra.**

   ![Alkalmazásregisztrációs hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8. Válassza a **Done** (Kész) lehetőséget.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift konfigurálása az Azure AD-hitelesítéshez

Az OpenShift hitelesítésszolgáltatóként való használatához az /etc/origin/master/master-config.yaml fájlt minden főcsomóponton szerkesztve kell használni.

Keresse meg a bérlőazonosítót a következő CLI-paranccsal:

```azurecli
az account show
```

A yaml fájlban keresse meg a következő sorokat:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
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

Az előző sorok után közvetlenül szúrja be a következő sorokat:

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

Győződjön meg arról, hogy a szöveg megfelelően igazodik az identityProviders alatt. Keresse meg a bérlőazonosítót a következő CLI-paranccsal:```az account show```

Indítsa újra az OpenShift főkiszolgálószolgáltatásokat az összes főcsomóponton:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

Az OpenShift konzolon két hitelesítési lehetőség jelenik meg: htpasswd_auth és [Alkalmazásregisztráció].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>OpenShift figyelése az Azure Monitor-naplókkal

A Log Analytics-ügynök háromféleképpen veheti fel az OpenShift-hez.
- Telepítse a Log Analytics ügynök Linuxhoz közvetlenül minden OpenShift-csomópontra
- Az Azure Monitor virtuálisgép-bővítményének engedélyezése minden OpenShift-csomóponton
- A Log Analytics-ügynök telepítése OpenShift démonkészletként

További részletekért olvassa el a teljes [útmutatót.](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift)

## <a name="configure-metrics-and-logging"></a>Mérőszámok és naplózás konfigurálása

Az ág alapján az Azure Resource Manager-sablonok OpenShift Container Platform és AZ OKD rendelkezhetnek bemeneti paramétereket a metrikák engedélyezéséhez és a naplózáshoz a telepítés részeként.

Az OpenShift Container Platform Marketplace ajánlat is lehetőséget biztosít a metrikák és a naplózás engedélyezéséhez a fürt telepítése során.

Ha metrikák / naplózás nem volt engedélyezve a fürt telepítése során, akkor könnyen engedélyezhető a tény után.

### <a name="azure-cloud-provider-in-use"></a>Használatban lévő Azure felhőszolgáltató

SSH a megerősített csomópontvagy az első fő csomópont (sablon és a használatban lévő ág alapján) a telepítés során megadott hitelesítő adatok használatával. Adja ki a következő parancsot:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Nem használt Azure felhőszolgáltató

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Open Service Broker for Azure (OSBA) telepítése

Az Azure-hoz vagy az OSBA-hoz való open Service Broker lehetővé teszi az Azure Cloud Services kiépítését közvetlenül az OpenShifttől. OSBA egy Nyílt szolgáltatásközvetítő API-implementációban az Azure-hoz. Az Open Service Broker API egy olyan specifikáció, amely meghatározza a felhőszolgáltatók közös nyelvét, amelyet a natív felhőalapú alkalmazások a felhőszolgáltatások zárolás nélküli kezelésére használhatnak.

Az OSBA OpenShift rendszerre történő https://github.com/Azure/open-service-broker-azure#openshift-project-templatetelepítéséhez kövesse az alábbi utasításokat: . 
> [!NOTE]
> Csak az OpenShift projektsablon szakasz lépéseit hajtsa végre, a teljes Telepítés szakaszban ne.

## <a name="next-steps"></a>További lépések

- [Az OpenShift container platform első lépései](https://docs.openshift.com)
