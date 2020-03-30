---
title: Az Azure Monitor beállítása élő adatok tárolóihoz (előzetes verzió) | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan állíthatja be a valós idejű nézet tárolónaplók (stdout/stderr) és események használata nélkül kubectl az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275372"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>A Live Data (előzetes verzió) funkció beállítása

A Live Data (előzetes verzió) megtekintéséhez az Azure Kubernetes-szolgáltatás (AKS) fürtök tárolók, konfigurálnia kell a hitelesítést, hogy engedélyt adjon a Kubernetes-adatok hoz való hozzáféréshez. Ez a biztonsági konfiguráció lehetővé teszi az adatok valós idejű elérését a Kubernetes API-n keresztül közvetlenül az Azure Portalon.

Ez a funkció a következő módszereket támogatja a naplókhoz, eseményekhez és metrikákhoz való hozzáférés szabályozásához:

- AKS kubernetes RBAC-engedélyezés nélkül
- Az AKS engedélyezve kubernetes RBAC-engedéllyel
    - Fürtszerepkör-kötési ** [clusterMonitoringUser-rel](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0) konfigurált AKS**
- Az AKS engedélyezve van az Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel

Ezek az utasítások a Kubernetes-fürthöz való rendszergazdai hozzáférést, valamint az Azure Active Directory (AD) felhasználói hitelesítéshez való használatának konfigurálását, az Azure AD-hez való rendszergazdai hozzáférést is igénylik.  

Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítést a fürt élő adatokhoz (előzetes verzió) szolgáltatásához való hozzáférés szabályozására:

- Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezett AKS-fürt
- Az Azure Active Directory integrált AKS-fürtje. 

>[!NOTE]
>Ez a szolgáltatás nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AKS-fürtöket. Ez a szolgáltatás a Kubernetes API közvetlen elérésére támaszkodik a böngészőből származó proxykiszolgálón keresztül. Ha engedélyezi a hálózati biztonságot a Kubernetes API letiltásának a proxyról, az blokkolja ezt a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure US Government.

## <a name="authentication-model"></a>Hitelesítési modell

A Live Data (előzetes verzió) funkciók a Kubernetes API-t használják, amely megegyezik a `kubectl` parancssori eszközzel. A Kubernetes API-végpontok önaláírt tanúsítványt használnak, amelyet a böngésző nem fog tudni érvényesíteni. Ez a szolgáltatás egy belső proxyt használ a tanúsítvány a KS-szolgáltatással történő érvényesítéséhez, biztosítva a forgalom megbízhatóságát.

Az Azure Portalkéri, hogy érvényesítse a bejelentkezési hitelesítő adatokat egy Azure Active Directory-fürt, és átirányítja az ügyfél regisztrációs beállítása során fürt létrehozása (és újra konfigurált ebben a cikkben). Ez a viselkedés hasonló a. `kubectl`által igényelt hitelesítési folyamathoz. 

>[!NOTE]
>A fürt engedélyezését a Kubernetes kezeli, és a biztonsági modell, amelyhez konfigurálva van. A szolgáltatást elérő felhasználóknak a futtatáshoz `az aks get-credentials -n {your cluster name} -g {your resource group}`hasonlóan a Kubernetes konfiguráció *(kubeconfig)* letöltéséhez engedéllyel kell rendelkezniük. Ez a konfigurációs fájl tartalmazza az **Azure Kubernetes service cluster felhasználói szerepkör**engedélyezési és hitelesítési jogkivonatát, az Azure RBAC-kompatibilis és az RBAC-engedélyezés nélküli AKS-fürtök esetében. Információkat tartalmaz az Azure AD és az ügyfél regisztrációs adatait, ha az AKS engedélyezve van az Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezés.

>[!IMPORTANT]
>A szolgáltatások felhasználói nak szüksége van [az Azure Kubernetes fürt felhasználói szerepkör](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) a fürthöz, hogy töltse le a és használja ezt a `kubeconfig` funkciót. A felhasználóknak **nincs** szükségük közreműködői hozzáférésre a fürthöz a szolgáltatás használatához. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>ClusterMonitoringUser használata RBAC-kompatibilis fürtökkel

Annak érdekében, hogy az [RBAC-engedélyezés engedélyezése](#configure-kubernetes-rbac-authorization) után ne kelljen további konfigurációs módosításokat alkalmaznia ahhoz, hogy a Kubernetes felhasználói szerepkör **kötése fürtfelhasználó** hozzáférést biztosíthasson az Élő adatok (előzetes verzió) szolgáltatáshoz, az AKS hozzáadott egy új Kubernetes-fürtszerepkör-kötést **clusterMonitoringUser**néven. Ez a fürtszerepkör-kötés rendelkezik az összes szükséges engedélyekkel a Kubernetes API és a Live Data (előzetes verzió) szolgáltatás kihasználásához szükséges hozzáférési pontok eléréséhez.

A Live Data (előzetes verzió) funkció ezen új felhasználóval való használatához az AKS-fürterőforrás [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkörének tagjának kell lennie. Az Azure Monitor tárolók, ha engedélyezve van, úgy van konfigurálva, hogy ezzel a felhasználóval alapértelmezés szerint hitelesítésre van konfigurálva. Ha a clusterMonitoringUser szerepkör-kötés nem létezik egy fürtön, a **fürtuser** hitelesítésre szolgál.

Az AKS 2020 januárjában tette közzé ezt az új szerepkör-kötést, így a 2020 januárja előtt létrehozott fürtök nem rendelkeznek ezzel. Ha olyan fürttel rendelkezik, amely 2020 januárja előtt jött létre, az új **clusterMonitoringUser** hozzáadható egy meglévő fürthöz egy PUT-művelet végrehajtásával a fürtön, vagy bármely más művelet végrehajtása a fürtön, vagy put műveletet hajt végre a fürtön, például a fürt verziójának frissítése.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-fürt RBAC nélkül

Ha egy Kubernetes-fürt, amely nincs konfigurálva a Kubernetes RBAC-hitelesítéssel, vagy integrált Az Azure AD egyszeri bejelentkezés, nem kell végrehajtani a következő lépéseket. Ennek az az oka, hogy alapértelmezés szerint nem RBAC-konfigurációban rendelkezik rendszergazdai engedélyekkel.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC-hitelesítés konfigurálása

Ha engedélyezi a Kubernetes RBAC-hitelesítést, a rendszer két felhasználót használ: **a clusterUser** t és a **clusterAdmin-t** a Kubernetes API eléréséhez. Ez hasonló a `az aks get-credentials -n {cluster_name} -g {rg_name}` felügyeleti beállítás nélküli futtatáshoz. Ez azt jelenti, hogy a **fürtfelhasználónak** hozzáférést kell biztosítani a Kubernetes API végpontjaihoz.

A következő példalépések bemutatják, hogyan konfigurálhatja a fürtszerepkör-kötést ebből a yaml konfigurációs sablonból.

1. Másolja és illessze be a yaml fájlt, és mentse logReaderRBAC.yaml néven.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. A konfiguráció frissítéséhez futtassa `kubectl apply -f LogReaderRBAC.yaml`a következő parancsot: .

>[!NOTE] 
> Ha a fájl egy korábbi `LogReaderRBAC.yaml` verzióját alkalmazta a fürtre, frissítse azt a fenti 1.

## <a name="configure-ad-integrated-authentication"></a>AD-be integrált hitelesítés konfigurálása 

Az Azure Active Directory (AD) felhasználói hitelesítéshez való használatára konfigurált AKS-fürt a szolgáltatáshoz hozzáférő személy bejelentkezési hitelesítő adatait használja. Ebben a konfigurációban az Azure AD-hitelesítési jogkivonat használatával bejelentkezhet egy AKS-fürtbe.

Az Azure AD-ügyfélregisztrációt újra kell konfigurálni, hogy az Azure Portal megbízható átirányítási URL-címként átirányíthassa az engedélyezési lapokat. Az Azure AD felhasználói ezután közvetlenül ugyanahhoz a Kubernetes API-végpontokhoz kapnak hozzáférést **a ClusterRoles** és **a ClusterRoleBindings segítségével.** 

A Kubernetes speciális biztonsági beállításairól a [Kubernetes dokumentációjában](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)olvashat bővebben. 

>[!NOTE]
>Ha új RBAC-kompatibilis fürtöt hoz létre, olvassa el [az Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../../aks/azure-ad-integration.md) című témakört, és kövesse az Azure AD-hitelesítés konfigurálásának lépéseit. Az ügyfélalkalmazás létrehozásához szükséges lépések során az ebben a szakaszban található megjegyzés kiemeli az Azure Monitorhoz létrehozandó két átirányítási URL-t az alábbi 3.

### <a name="client-registration-reconfiguration"></a>Ügyfélregisztráció újrakonfigurálása

1. Keresse meg a Kubernetes-fürt ügyfélregisztrációját az Azure AD-ben az **Azure Active Directory > alkalmazásregisztrációk** alatt az Azure Portalon.

2. Válassza a **hitelesítés** lehetőséget a bal oldali ablaktáblából. 

3. Adjon hozzá két átirányítási URL-t a listához webalkalmazás-típusként. **Web** Az első alap URL-értéknek, `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a második `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`alap URL-értéknek pedig a legyen .

    >[!NOTE]
    >Ha ezt a funkciót az Azure China-ban használja, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` az első alap URL-értéknek kell lennie, és a második alap URL-értéknek kell lennie. `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 
    
4. Az átirányítási URL-címek regisztrálása után az **Implicit támogatás**csoportban válassza ki a **Hozzáférési jogkivonatok** és **azonosító tokenek beállításait,** majd mentse a módosításokat.

>[!NOTE]
>A hitelesítés konfigurálása az Azure Active Directory egyszeri bejelentkezéshez csak egy új AKS-fürt kezdeti üzembe helyezése során végezhető el. Nem konfigurálható egyszeri bejelentkezés már telepített AKS-fürthöz.
  
>[!IMPORTANT]
>Ha újrakonfigurálta az Azure AD-t a frissített URI-val történő felhasználói hitelesítéshez, törölje a böngésző gyorsítótárát, hogy a frissített hitelesítési jogkivonat letöltődjön és alkalmazva legyen.

## <a name="grant-permission"></a>Engedély megadása

Minden Azure AD-fiók nak engedélyt kell adni a megfelelő API-k kubernetes eléréséhez a Live Data (előzetes verzió) funkció eléréséhez. Az Azure Active Directory-fiók megadásának lépései hasonlóak a [Kubernetes RBAC hitelesítési](#configure-kubernetes-rbac-authorization) szakaszban ismertetett lépésekhez. Mielőtt a yaml konfigurációs sablont a fürtre, cserélje **le clusterUser** **csoportban ClusterRoleBinding** a kívánt felhasználóra. 

>[!IMPORTANT]
>Ha a felhasználó, amiért megadja az RBAC-kötés ugyanabban az Azure AD-bérlőben van, rendeljen hozzá engedélyeket a userPrincipalName alapján. Ha a felhasználó egy másik Azure AD-bérlő, lekérdezése és használata az objectId tulajdonság.

Az AKS cluster **ClusterRoleBinding**konfigurálásával kapcsolatos további segítséget az [RBAC-kötés létrehozása című](../../aks/azure-ad-integration-cli.md#create-rbac-binding)témakörben talál.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik a beállítási hitelesítéssel, valós időben megtekintheti a [metrikákat,](container-insights-livedata-metrics.md) [a központi telepítéseket,](container-insights-livedata-deployments.md) [valamint az eseményeket és a naplókat](container-insights-livedata-overview.md) a fürtből.
