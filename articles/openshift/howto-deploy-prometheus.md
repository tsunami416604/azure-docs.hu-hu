---
title: Egy önálló Azure Red Hat OpenShift-fürtben Prometheus-példány üzembe helyezése |} A Microsoft Docs
description: Hozzon létre egy Prometheus példányt az alkalmazás-metrikák figyelése az Azure Red Hat OpenShift-fürtben.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, az openshift, a metrikák, a red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827019"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Egy önálló Azure Red Hat OpenShift-fürtben Prometheus-példány üzembe helyezése

Ez a cikk ismerteti egy különálló Prometheus példány által használt szolgáltatás felderítése az Azure Red Hat OpenShift fürt konfigurálása.

> [!NOTE]
> Ügyfél rendszergazdai hozzáférés az Azure Red Hat OpenShift fürthöz nem szükséges.

Cél beállításai:

- Egy projekt (prometheus-projekt), amely Prometheus és Alertmanager tartalmaz.
- Két projekt (alkalmazás-project1 és alkalmazás-– project2), az alkalmazások figyeléséhez tartalmaznak.

Néhány Prometheus konfigurációs fájlt helyileg fogja előkészíteni. Hozzon létre egy új mappát tárolja őket. Konfigurációs fájlok a fürtben lévő titkos kódként tárolja, abban az esetben, ha a titkos jogkivonatok később hozzáadják a fürthöz.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Jelentkezzen be a fürthöz a c eszköz használatával

1. Nyisson meg egy webböngészőt, és keresse meg a webkonzol, a fürt (https://openshift. *véletlenszerű azonosítót*. *régió*. azmosa.io).
2. Jelentkezzen be az Azure-beli hitelesítő adataival.
3. Kattintson a jobb felső sarokban a felhasználónevére, és válassza **másolási Login paranccsal**.
4. Illessze be a felhasználónevet a terminál fog használni.

> [!NOTE]
> Ha van bejelentkezve a megfelelő fürthöz megtekintéséhez futtassa a `oc whoami -c` parancsot.

## <a name="prepare-the-projects"></a>A projektek előkészítése

A projektek létrehozásához futtassa a következő parancsokat:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Használhatja a `-n` vagy `--namespace` paraméter, vagy válassza ki az aktív projekt futtatásával a `oc project` parancsot.

## <a name="prepare-the-prometheus-configuration-file"></a>A Prometheus konfigurációs fájl előkészítése
Hozzon létre egy prometheus.yml fájlt írja be az alábbi tartalommal:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Hozzon létre egy titkos kulcsot Prom nevű írja be a következő konfigurációt:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml fájl Prometheus alapszintű konfigurációs fájlt. Ez a időközöket, és konfigurálja az automatikus észlelés három projektekben (prometheus-projekt, alkalmazás-project1, alkalmazás-– project2). Az előző konfigurációs fájlban az automatikusan felderített végpontok vannak lekaparták HTTP protokollon keresztüli hitelesítés nélkül.

Automatizované získávání dat végpontok kapcsolatos további információkért lásd: [Prometheus beírása vezérlőkarakterrel config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Készítse elő a Alertmanager konfigurációs fájl
Hozzon létre egy alertmanager.yml fájlt írja be az alábbi tartalommal:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Hozzon létre egy titkos kulcsot Prom-riasztások nevű írja be a következő konfigurációt:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml a riasztási Manager konfigurációs fájlt.

> [!NOTE]
> Az előző két lépést ellenőrzéséhez futtassa a `oc get secret -n prometheus-project` parancsot.

## <a name="start-prometheus-and-alertmanager"></a>Indítsa el a Prometheus és Alertmanager
Lépjen a [openshift/forrástárházba](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) , és töltse le a [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) sablont. A sablon alkalmazása prometheus-projekt írja be a következő konfigurációt:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Ez az OpenShift sablon prometheus-standalone.yaml fájl. Az oauth-alkalmazásproxyval elé, és a egy Alertmanager példány, is biztosított, oauth-proxy egy Prometheus példányt hoz létre. Ez a sablon az oauth-proxy van konfigurálva, bármely felhasználó, aki is "get" prometheus-projekt névtér (lásd a `-openshift-sar` jelző).

> [!NOTE]
> Ha a prom StatefulSet rendelkezik, egyenlő DESIRED jelenlegi replikák száma ellenőrzéséhez futtassa a `oc get statefulset -n prometheus-project` parancsot. A projekt összes erőforrás ellenőrzéséhez futtassa a `oc get all -n prometheus-project` parancsot.

## <a name="add-permissions-to-allow-service-discovery"></a>A szolgáltatásészlelés engedélyezése az engedélyek hozzáadása

Hozzon létre egy prometheus-sdrole.yml fájlt írja be az alábbi tartalommal:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
A projektek szolgáltatásészlelés engedélyezése kívánt sablon alkalmazása, futtassa a következő parancsokat:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Ahhoz, hogy a metrikák begyűjtéséhez ze sebe sama Prometheus, alkalmazza az engedélyek prometheus-projektben.

> [!NOTE]
> Ellenőrizze, hogy a szerepkör és RoleBinding helyesen lettek létrehozása, futtassa a `oc get role` és `oc get rolebinding` parancsokat.

## <a name="optional-deploy-example-application"></a>Nem kötelező: A példában üzembe helyezése

Minden jól működik, de nincsenek metrikák források. Nyissa meg a Prometheus URL-cím (https://prom-prometheus-project.apps.*véletlenszerű azonosítót*.*régió*.azmosa.io/). Az alábbi parancs használatával találja:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ne felejtse el a https:// előtag hozzáadása a gazdagép nevének elején.

A **állapot > Szolgáltatásészlelés** lapon jelennek meg 0/0 aktív célokat.

Mintaalkalmazás, amely alatt a /metrics végpont alapmetrikák Python, üzembe helyezéséhez futtassa a következő parancsokat:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Az új alkalmazásokat üzembe helyezés után 30 másodpercen belül a Szolgáltatásészlelés oldalon érvényes célként jelenjenek meg.

További részletekért válassza **állapot** > **célok**.

> [!NOTE]
> Minden sikeresen scraped célként Prometheus hozzáadása egy adatpontot a felfelé metrika. Válassza ki **Prometheus** adja meg a bal felső sarokban látható **mentése** a kifejezést, és válassza ki, **Execute**.

## <a name="next-steps"></a>További lépések

Az alkalmazások saját Prometheus üzemállapot-mérési adhat hozzá. A Prometheus ügyféloldali kódtár, amely egyszerűbbé teszi a Prometheus metrikák előkészítése, készen áll a különböző programozási nyelvekhez.

További információkért lásd az alábbi GitHub-kódtárak:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
