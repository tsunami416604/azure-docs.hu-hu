---
title: Azure Monitor beállítása az élő adattárolók számára (előzetes verzió) | Microsoft Docs
description: Ez a cikk bemutatja, hogyan állíthatja be a tároló-naplók valós idejű nézetét (StdOut/stderr) és az eseményeket anélkül, hogy a kubectl-t használja a tárolók Azure Monitor.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459922"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Az élő adatszolgáltatások (előzetes verzió) beállításának beállítása

Ha szeretné megtekinteni az Azure Kubernetes Service-(ak-) fürtökön Azure Monitorekkel rendelkező élő adatok (előzetes verzió) megtekintését, konfigurálnia kell a hitelesítést, hogy engedélyt adjon a Kubernetes adataihoz való hozzáférésre. Ez a biztonsági konfiguráció lehetővé teszi a valós idejű hozzáférést az adataihoz a Kubernetes API-n keresztül közvetlenül a Azure Portal.

Ez a szolgáltatás a következő módszereket támogatja a naplók, események és metrikák elérésének vezérléséhez:

- Az AKS nélküli Kubernetes RBAC-hitelesítés engedélyezve
- Az AKS Kubernetes RBAC-hitelesítés engedélyezve
    - A fürt szerepkörének kötési  **[CLUSTERMONITORINGUSER](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0) konfigurált AK**
- Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel engedélyezett AK

Ezek az utasítások rendszergazdai hozzáférést igényelnek a Kubernetes-fürthöz, és ha úgy konfigurálja, hogy az Azure Active Directory (AD) felhasználó-hitelesítésre, rendszergazdai hozzáférés az Azure AD-hoz.  

Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítést az élő adatok (előzetes verzió) szolgáltatáshoz való hozzáférés vezérléséhez a fürtből:

- Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezett AK-alapú fürt
- Azure Active Directory integrált AK-fürt. 

>[!NOTE]
>Ez a funkció nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AK-fürtöket. Ez a funkció arra támaszkodik, hogy közvetlenül a böngészőből egy proxykiszolgálón keresztül éri el a Kubernetes API-t. A hálózati biztonság engedélyezésével letilthatja a Kubernetes API-t ebből a proxyból, és letiltja a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure USA kormányzati szerveiben.

## <a name="authentication-model"></a>Hitelesítési modell

Az élő adatszolgáltatások (előzetes verzió) funkciói a Kubernetes API-t használják, amely megegyezik a `kubectl` parancssori eszközzel. A Kubernetes API-végpontok olyan önaláírt tanúsítványt használnak, amelyet a böngésző nem tud érvényesíteni. Ez a szolgáltatás egy belső proxy használatával ellenőrzi a tanúsítványt az AK szolgáltatással, így biztosítva a forgalom megbízhatóságát.

A Azure Portal megkéri, hogy ellenőrizze a Azure Active Directory-fürt bejelentkezési hitelesítő adatait, és átirányítsa Önt az ügyfél-regisztráció beállítására a fürt létrehozásakor (és ebben a cikkben újra konfigurálva). Ez a viselkedés hasonló a `kubectl`által igényelt hitelesítési folyamathoz. 

>[!NOTE]
>A fürthöz való engedélyezést a Kubernetes és a szolgáltatással konfigurált biztonsági modell kezeli. A szolgáltatáshoz hozzáférő felhasználóknak engedélyre van szükségük a Kubernetes-konfiguráció (*kubeconfig*) letöltéséhez, hasonlóan a futó `az aks get-credentials -n {your cluster name} -g {your resource group}`hoz. Ez a konfigurációs fájl az **Azure Kubernetes Service cluster felhasználói szerepkör**engedélyezési és hitelesítési jogkivonatát tartalmazza, az Azure RBAC-kompatibilis és az AK-alapú fürtök esetében, ha nincs engedélyezve a RBAC-hitelesítés. Az Azure AD-vel és az ügyfél-regisztrációs adatokkal kapcsolatos információkat tartalmaz, ha az AK Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel van engedélyezve.

>[!IMPORTANT]
>Ennek a funkciónak a felhasználóinak az [Azure Kubernetes-fürt felhasználói szerepkört](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) kell használniuk a fürthöz a `kubeconfig` letöltéséhez és a szolgáltatás használatához. A szolgáltatás használatához a felhasználóknak **nincs** szükségük közreműködői hozzáférésre a fürthöz. 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>ClusterMonitoringUser használata RBAC-kompatibilis fürtökkel

Annak érdekében, hogy ne kelljen további konfigurációs módosításokat alkalmaznia ahhoz, hogy a Kubernetes felhasználói szerepköre **clusterUser** a RBAC engedélyezésének [engedélyezése](#configure-kubernetes-rbac-authorization) után az élő adat (előzetes verzió) szolgáltatáshoz való hozzáférést, az AK új Kubernetes-clusterMonitoringUser kötést kapott anéven. Ez a fürtcsomópont-kötés minden szükséges engedéllyel rendelkezik a Kubernetes API és a végpontok eléréséhez az élő adat (előzetes verzió) funkció kihasználása érdekében.

Ahhoz, hogy az élő adat (előzetes verzió) funkciót ezzel az új felhasználóval is használni lehessen, a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör tagjának kell lennie az AK-fürt erőforrásán. A tárolók Azure Monitor, ha engedélyezve van, alapértelmezés szerint ez a felhasználó használja a hitelesítést. Ha a clusterMonitoringUser szerepkör-kötés nem létezik a fürtön, a rendszer a **clusterUser** használja a hitelesítéshez.

Az AK január 2020-én kiadta ezt az új szerepkör-kötést, így a január 2020 előtt létrehozott fürtök nem rendelkeznek. Ha rendelkezik egy, a január 2020 előtt létrehozott fürttel, akkor az új **clusterMonitoringUser** hozzáadhatók egy meglévő fürthöz egy Put művelet a fürtön való végrehajtásával, vagy a THA-fürt bármely más műveletének végrehajtása a fürtön, például a fürt verziójának frissítése.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-fürt nélkül RBAC engedélyezve

Ha egy Kubernetes-fürtöt, amely nincs beállítva a Kubernetes RBAC-hitelesítés vagy az Azure AD egyszeri bejelentkezéses integrálva van, nem kell az alábbi lépésekkel. Ennek az az oka, hogy a rendszergazdai engedélyek alapértelmezés szerint nem RBAC konfigurációban vannak.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC-hitelesítés konfigurálása

Ha engedélyezi a Kubernetes RBAC-engedélyezést, a rendszer két felhasználót használ: a **clusterUser** és a **CLUSTERADMIN** a Kubernetes API eléréséhez. Ez hasonló a `az aks get-credentials -n {cluster_name} -g {rg_name}` futtatásához a felügyeleti beállítás nélkül. Ez azt jelenti, hogy a **clusterUser** hozzáférést kell biztosítani a Kubernetes API végponti pontjaihoz.

A következő példa lépései bemutatják, hogyan konfigurálhatja a fürt szerepkör-kötés yaml konfigurációs sablon alapján.

1. Másolja és illessze be a yaml-fájlt, és mentse LogReaderRBAC.yaml.  

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

2. A konfiguráció frissítéséhez futtassa a következő parancsot: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> Ha a `LogReaderRBAC.yaml` fájl egy korábbi verzióját alkalmazza a fürtre, frissítse azt a fenti 1. lépésben bemutatott új kód másolásával és beillesztésével, majd futtassa a 2. lépésben bemutatott parancsot a fürtön való alkalmazásához.

## <a name="configure-ad-integrated-authentication"></a>AD-integrált hitelesítés konfigurálása 

A felhasználói hitelesítéshez Azure Active Directory (AD) használatára konfigurált AK-fürt a szolgáltatáshoz hozzáférő személy bejelentkezési hitelesítő adatait használja. Ebben a konfigurációban az Azure AD-hitelesítési token használatával tud bejelentkezni egy AK-fürtbe.

Az Azure AD-ügyfél regisztrációját újra be kell állítani, hogy a Azure Portal átirányítsa az engedélyezési lapokat megbízható átirányítási URL-címként. Az Azure AD-felhasználók közvetlenül a **ClusterRoles** és a **ClusterRoleBindings**-en keresztül kapnak hozzáférést ugyanahhoz a Kubernetes API-végponthoz. 

A Kubernetes speciális biztonsági beállításaival kapcsolatos további információkért tekintse át a [Kubernetes dokumentációját](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). 

>[!NOTE]
>Ha új RBAC-kompatibilis fürtöt hoz létre, tekintse meg a [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../../aks/azure-ad-integration.md) című témakört, és kövesse az Azure ad-hitelesítés konfigurálásának lépéseit. Az ügyfélalkalmazás létrehozásának lépései során az ebben a szakaszban található Megjegyzés kiemeli a két átirányítási URL-címet, amelyeket az alábbi 3. lépésben megadott tárolók Azure Monitorához kell létrehoznia.

### <a name="client-registration-reconfiguration"></a>Ügyfél-regisztráció újrakonfigurálása

1. Keresse meg a Kubernetes-fürthöz tartozó ügyfél-regisztrációt az Azure AD-ben, **Azure Active Directory > Alkalmazásregisztrációk** a Azure Portalban.

2. A bal oldali ablaktáblán válassza a **hitelesítés** lehetőséget. 

3. Adjon hozzá két átirányítási URL-címet **a listához webalkalmazás-** típusokként. Az első URL-cím értékének `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`nak kell lennie, és a második alap URL-címnek `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`nak kell lennie.

    >[!NOTE]
    >Ha ezt a funkciót az Azure China-ban használja, az első alap URL-címet `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` kell megadni, és a második alap URL-címet `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`kell megadni. 
    
4. Az átirányítási URL-címek regisztrálását követően az **implicit engedélyezés**területen válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** lehetőséget, majd mentse a módosításokat.

>[!NOTE]
>Az egyszeri bejelentkezés Azure Active Directory használatával történő hitelesítés konfigurálása csak az új AK-fürtök kezdeti telepítése során hajtható végre. Egy már üzembe helyezte az AKS-fürtöt az egyszeri bejelentkezés nem állíthatja be.
  
>[!IMPORTANT]
>Ha a frissített URI használatával újrakonfigurálta az Azure AD-t a felhasználói hitelesítéshez, törölje a böngésző gyorsítótárát, és győződjön meg arról, hogy a frissített hitelesítési jogkivonat letöltése és alkalmazása megtörtént.

## <a name="grant-permission"></a>Engedély megadása

Minden Azure AD-fióknak engedéllyel kell rendelkeznie a megfelelő API-khoz a Kubernetes-ben az élő adat(előzetes verzió) funkció eléréséhez. A Azure Active Directory fiók megadásának lépései hasonlóak a [KUBERNETES RBAC hitelesítés](#configure-kubernetes-rbac-authorization) szakaszban leírt lépésekhez. Mielőtt alkalmazza a YAML-konfigurációs sablont a fürtre, cserélje le a **clusterUser** a **ClusterRoleBinding** elemre a kívánt felhasználóval. 

>[!IMPORTANT]
>Ha az RBAC-kötést megadó felhasználó ugyanabban az Azure AD-bérlőben található, akkor a userPrincipalName alapján rendeljen engedélyeket. Ha a felhasználó egy másik Azure AD-bérlőben található, a objectId tulajdonság lekérdezése és használata.

Az AK-fürt **ClusterRoleBinding**konfigurálásával kapcsolatos további segítségért lásd: [RBAC-kötés létrehozása](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a hitelesítést, a fürtből valós időben megtekintheti a [metrikákat](container-insights-livedata-metrics.md), a [központi telepítéseket](container-insights-livedata-deployments.md), valamint az [eseményeket és a naplókat](container-insights-livedata-overview.md) .
