---
title: OpenShift az Azure-telepítés utáni feladatok |} A Microsoft Docs
description: Az OpenShift fürt után további feladatai lett telepítve.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: fba29cd55f2d765faa107de3a8961032ef44deec
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997399"
---
# <a name="post-deployment-tasks"></a>Üzembe helyezés utáni feladatok

Miután az OpenShift fürtöt telepít, konfigurálhatja úgy a további elemek. Ez a cikk ismerteti:

- Az Azure Active Directory (Azure AD) használatával az egyszeri bejelentkezés konfigurálása
- OpenShift figyelése az Azure Monitor konfigurálásáról
- Metrikák és naplózás konfigurálása
- Open Service Broker for Azure (OSBA) telepítése

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Az Azure Active Directoryval egyszeri bejelentkezés konfigurálása

Azure Active Directory-hitelesítéshez használandó, először hozzon létre egy Azure AD-alkalmazás regisztrációjának. Ez a folyamat két lépésből áll: az alkalmazás regisztrációját létrehozását és engedélyek konfigurálását.

### <a name="create-an-app-registration"></a>Hozzon létre egy alkalmazásregisztráció

Ezeket a lépéseket az Azure CLI használatával hozzon létre az alkalmazás regisztrációját, és a grafikus felhasználói Felülettel (portál) állítsa be az engedélyeket. Szeretne létrehozni az alkalmazás regisztrációját, az alábbi öt adatokra van szükség:

- Megjelenített név: Alkalmazás regisztrálása nevét (például OCPAzureAD)
- Kezdőlap: OpenShift konzol URL-címe (például https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Identifier URI: OpenShift konzol URL-címe (például https://masterdns343khhde.westus.cloudapp.azure.com/console)
- Válasz URL-címe: Nyilvános URL-CÍMÉT és az alkalmazás regisztrációs nevét (például fő https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- Jelszó: Biztonságos jelszó (erős jelszó használata)

A következő példában létrehozunk egy alkalmazásregisztráció az előző információk alapján:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Ha a parancs végrehajtása sikeres, megjelenik egy JSON-kimenetet hasonló:

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

Jegyezze fel az appId-tulajdonság az egy későbbi lépésben a parancs által visszaadott.

Az Azure Portalon:

1. Válassza ki **az Azure Active Directory** > **Alkalmazásregisztráció**.
2. Keresse meg az alkalmazás regisztrációját (például OCPAzureAD).
3. Az eredmények között kattintson az alkalmazás regisztrációját.
4. A **beállítások**válassza **szükséges engedélyek**.
5. A **szükséges engedélyek**válassza **Hozzáadás**.

   ![Alkalmazásregisztráció](media/openshift-post-deployment/app-registration.png)

6. Kattintson az 1. lépés: Válassza ki az API-t, és kattintson a **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Kattintson a **kiválasztása** alján.

   ![Alkalmazásregisztráció kijelölt API](media/openshift-post-deployment/app-registration-select-api.png)

7. On Step 2: Jelölje be az engedélyeket, jelölje be **jelentkezzen be és felhasználói profil olvasása** alatt **delegált engedélyek**, és kattintson a **válassza**.

   ![Alkalmazásregisztráció-hozzáférés](media/openshift-post-deployment/app-registration-access.png)

8. Válassza a **Done** (Kész) lehetőséget.

### <a name="configure-openshift-for-azure-ad-authentication"></a>OpenShift az Azure AD-hitelesítés konfigurálása

OpenShift az Azure AD-hitelesítési szolgáltató használatára konfigurálja, hogy a /etc/origin/master/master-config.yaml fájlt az összes fő csomóponttal kell szerkeszteni.

Keresse meg a bérlő Azonosítóját a következő CLI-parancs használatával:

```azurecli
az account show
```

A yaml-fájlt keresse meg a következő sorokat:

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

Ellenőrizze, hogy a szöveg igazítja megfelelően identityProviders alatt. Keresse meg a bérlő Azonosítóját a következő CLI-parancs használatával: ```az account show```

Indítsa újra az összes fő csomóponton az OpenShift fő szolgáltatások:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

Az OpenShift-konzolon, most már két lehetőség jelenik meg a hitelesítéshez: htpasswd_auth és [Alkalmazásregisztráció].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>OpenShift monitorozása az Azure Monitor naplóira

A Log Analytics-ügynök hozzáadása az OpenShift három módja van.
- A Linuxhoz készült Log Analytics-ügynök telepítése minden egyes OpenShift csomóponton közvetlenül
- Az Azure Monitor Virtuálisgép-bővítmény engedélyezése a OpenShift csomópontokon
- A Log Analytics-ügynököket telepítse az OpenShift démon beállított

Olvassa el a teljes [utasításokat](https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift) további részletekért.

## <a name="configure-metrics-and-logging"></a>Metrikák és naplózás konfigurálása

Az ág alapján, az Azure Resource Manager-sablonok az OpenShift Tárolóplatform és OKD rendelkezhetnek mérőszámainak engedélyezése és a telepítés részeként naplózási bemeneti paramétereit.

Az OpenShift Container Platform Marketplace-ajánlat is lehetőséget biztosít, hogy engedélyezze a metrikák és naplózás fürt telepítése során.

Ha a metrikák / naplózás nincs engedélyezve a fürt telepítésekor, azok egyszerűen engedélyezhető bekövetkeztek.

### <a name="azure-cloud-provider-in-use"></a>Használja az Azure Felhőszolgáltató

Ssh-KAPCSOLATOT a megerősített vagy első fő csomópontot (a sablon és a használatban lévő ág alapján) a telepítés során megadott hitelesítő adatok használatával. A következő parancs kiadásával:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Az Azure Cloud-szolgáltató nincs használatban

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Az Open Service Broker for Azure (OSBA) telepítése

Nyissa meg a Service Broker for Azure-ban vagy az OSBA, lehetővé teszi, hogy közvetlenül az OpenShift az Azure Cloud Services üzembe helyezi. Az Azure Open Service Broker API megvalósítása az OSBA. Az Open Service Broker API egy specifikációja, amelyek egy közös nyelvvel felhő szolgáltatókat, felhőbeli natív alkalmazásokat nélkül zár a felhőszolgáltatások kezelésére használható.

Az OpenShift OSBA telepítéséhez kövesse az itt található utasításokat: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Csak a lépéseket az OpenShift projektsablon és nem a teljes telepítése szakaszban.

## <a name="next-steps"></a>További lépések

- [Ismerkedés az OpenShift Tárolóplatform](https://docs.openshift.com/container-platform)
