---
title: Prometheus-példány üzembe helyezése az Azure Red Hat OpenShift-fürtben
description: Hozzon létre egy Prometheus-példányt egy Azure Red Hat OpenShift-fürtben az alkalmazás metrikáinak figyeléséhez.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, mérőszámok, piros kalap
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886888"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Önálló Prometheus-példány üzembe helyezése Egy Azure Red Hat OpenShift-fürtben

Ez a cikk ismerteti, hogyan konfigurálhat egy önálló Prometheus-példány, amely egy Azure Red Hat OpenShift fürt szolgáltatásfelderítését használja.

> [!NOTE]
> Az Azure Red Hat OpenShift-fürthöz való ügyfélrendszergazdai hozzáférés nem szükséges.

Cél beállítása:

- Egy projekt (prometheus-projekt), amely tartalmazza a Prometheus és alertmanager.
- Két projekt (alkalmazás-projekt1 és alkalmazás-projekt2), amelyek tartalmazzák a figyelni t.

A Prometheus config fájlokat helyben készíti elő. Hozzon létre egy új mappát a tárolásukhoz. A konfigurációs fájlok titkos kulcsokként tárolódnak a fürtben, abban az esetben, ha titkos jogkivonatokat adnak hozzá később a fürthöz.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Bejelentkezés a fürtbe az OC eszközzel

1. Nyisson meg egy webböngészőt, majd lépjenhttps://openshifta fürt webkonzoljára ( .* random-id*. *régió*.azmosa.io).
2. Jelentkezzen be az Azure-beli hitelesítő adataival.
3. A jobb felső sarokban jelölje ki a felhasználónevét, majd válassza **a Bejelentkezési parancs másolása parancsot**.
4. Illessze be a felhasználónevét a használni kívánt terminálba.

> [!NOTE]
> Ha meg szeretné tudni, hogy be van-e jelentkezve a megfelelő fürtbe, futtassa a `oc whoami -c` parancsot.

## <a name="prepare-the-projects"></a>A projektek előkészítése

A projektek létrehozásához futtassa a következő parancsokat:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Használhatja a `-n` vagy `--namespace` paramétert, vagy kijelölhet `oc project` egy aktív projektet a parancs futtatásával.

## <a name="prepare-the-prometheus-configuration-file"></a>A Prometheus konfigurációs fájl előkészítése
Hozzon létre egy prometheus.yml fájlt a következő tartalom megadásával:
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
Hozzon létre egy Prom nevű titkos kulcsot a következő konfiguráció megadásával:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

A prometheus.yml fájl egy alapvető Prometheus konfigurációs fájl. Három projektben (prometheus-project, app-project1, app-project2) állítja be az automatikus felderítést. Az előző konfigurációs fájlban az automatikusan felderített végpontok kaparják HTTP-n keresztül hitelesítés nélkül.

A végpontok kaparásáról a [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)című témakörben talál további információt.


## <a name="prepare-the-alertmanager-config-file"></a>Az Alertmanager konfigurációs fájl előkészítése
Hozzon létre egy alertmanager.yml fájlt a következő tartalom megadásával:
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
Hozzon létre egy Szalagértesítések nevű titkos kulcsot a következő konfiguráció megadásával:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Az Alertmanager.yml a Riasztáskezelő konfigurációs fájlja.

> [!NOTE]
> Az előző két lépés ellenőrzéséhez futtassa a `oc get secret -n prometheus-project` parancsot.

## <a name="start-prometheus-and-alertmanager"></a>A Prometheus és az Alertmanager indítása
Nyissa meg az [openshift/origin adattárat,](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) és töltse le a [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) sablont. Alkalmazza a sablont a prometheus-projektre a következő konfiguráció megadásával:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
A prometheus-standalone.yaml fájl egy OpenShift-sablon. Létrehoz egy Prometheus példányt, előtte oauth-proxyval, és egy Alertmanager példányt, amely et is biztonságosnak kell lennie az oauth-proxyval. Ebben a sablonban az oauth-proxy úgy van beállítva, hogy minden olyan felhasználó, `-openshift-sar` aki "beszerezheti" a prometheus-projekt névteret (lásd a jelzőt).

> [!NOTE]
> Annak ellenőrzéséhez, hogy a prom StatefulSet rendelkezik-e a DESIRED és a CURRENT szám replikákkal, futtassa a `oc get statefulset -n prometheus-project` parancsot. A projekt összes erőforrásának ellenőrzéséhez futtassa a `oc get all -n prometheus-project` parancsot.

## <a name="add-permissions-to-allow-service-discovery"></a>Engedélyek hozzáadása a szolgáltatásfelderítés engedélyezéséhez

Hozzon létre egy prometheus-sdrole.yml fájlt a következő tartalom megadásával:
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
Ha a sablont minden olyan projektre alkalmazni szeretné, amelyről engedélyezni szeretné a szolgáltatásfelderítést, futtassa a következő parancsokat:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Annak ellenőrzéséhez, hogy a Szerepkör és `oc get role` a `oc get rolebinding` RoleBinding megfelelően lett-e létrehozva, futtassa a és a parancsokat.

## <a name="optional-deploy-example-application"></a>Nem kötelező: Példaalkalmazás telepítése

Minden működik, de nincsenek metrikaforrások. Ugrás a Prometheushttps://prom-prometheus-project.appsURL-jére ( .* random-id*. *régió*.azmosa.io/). A következő paranccsal találhatja meg:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ne felejtse el hozzáadni a https:// előtagot az állomásnév elejéhez.

Az **Állapot > szolgáltatásfelderítése** lapon 0/0 aktív cél jelenik meg.

Egy példaalkalmazás üzembe helyezéséhez, amely elérhetővé teszi az alapvető Python-metrikákat a /metrics végpont alatt, futtassa a következő parancsokat:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Az új alkalmazásoknak a telepítést követő 30 másodpercen belül érvényes példányként kell megjelenniük a Szolgáltatásfelderítés lapon.

További részletekért válassza **az Állapotcélok** > **lehetőséget.**

> [!NOTE]
> Minden sikeresen lekapart cél, Prometheus hozzáad egy adatpontot a felmérő metrika. Válassza a **Prometheus** elemet a bal felső sarokban, írja **be a kifejezést,** majd válassza a **Végrehajtás lehetőséget.**

## <a name="next-steps"></a>További lépések

Egyéni Prometheus-műszereket adhat hozzá alkalmazásaihoz. A Prometheus Ügyfél könyvtár, amely leegyszerűsíti a Prometheus metrikák előkészítését, készen áll a különböző programozási nyelvekre.

További információt a következő GitHub-tárakban talál:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Ugrás](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
