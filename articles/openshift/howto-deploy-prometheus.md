---
title: Helyezhet üzembe egy önálló Prometheus Azure Red Hat OpenShift-fürtben |} A Microsoft Docs
description: Íme egy Prometheus-példány létrehozása az Azure Red Hat OpenShift-fürtön az alkalmazás-metrikák figyelése.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, az openshift, a metrikák, a red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342635"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Helyezhet üzembe egy önálló Prometheus Azure Red Hat OpenShift-fürtben

Ez az útmutató azt ismerteti, hogyan Prometheus önálló konfigurálása az Azure Red Hat OpenShift-fürtben felderítése.  "Ügyfél-rendszergazda" hozzáférést a fürthöz nincs szükség.

A cél beállítása a következőképpen történik:

- egy projekt (prometheus-projekt) Prometheus és Alertmanager tartalmazó
- két projekt (alkalmazás-project1 és alkalmazás-– project2), az alkalmazások figyeléséhez tartalmazó

Néhány Prometheus konfigurációs fájlt helyileg fogja előkészíteni. Hozzon létre egy új mappát tárolja őket.
A konfigurációs fájlok tárolódik a fürtben lévő titkos kódként abban az esetben titkos jogkivonatok rájuk később hozzáadják.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>1\. lépés: Jelentkezzen be a fürt használata a `oc` eszköz
Egy böngészőben navigáljon a webkonzol, a fürt (https://openshift. *véletlenszerű azonosítót*. *régió*. azmosa.io).
Jelentkezzen be az Azure-beli hitelesítő adataival.
Kattintson a jobb felső sarokban a felhasználónevére, és válassza ki a "Másolás Login paranccsal". Illessze be a terminál fogja használni.

Ha van bejelentkezve, a megfelelő fürtöt a következővel ellenőrizheti a `oc whoami -c` parancsot.

## <a name="step-2-prepare-the-projects"></a>2\. lépés: A projektek előkészítése

Hozzon létre projekteket.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Használhatja a `-n` vagy `--namespace` paraméter, vagy válassza ki a projektet az aktív a `oc project` parancs

## <a name="step-3-prepare-prometheus-config"></a>3\. lépés: Prometheus konfiguráció előkészítése
Az alábbi tartalommal prometheus.yml nevű fájl létrehozása.
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
A konfiguráció "prom" nevű titkos kulcs létrehozása.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

A fent felsorolt fájlt egy olyan alapszintű Prometheus konfigurációs fájl.
Ez a időközöket, és konfigurálja az automatikus észlelés három projektekben (prometheus-projekt, alkalmazás-project1, alkalmazás-– project2).
Ebben a példában az automatikus felderítése végpontok fog lekaparták HTTP protokollon keresztüli hitelesítés nélkül.
A Pro automatizované získávání dat végpontok további információkért lásd: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>4\. lépés: Alertmanager konfiguráció előkészítése
Az alábbi tartalommal alertmanager.yml nevű fájl létrehozása.
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
"Prom-figyelmeztetések" konfigurációval nevű titkos kulcs létrehozása.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

A fent felsorolt fájlja a riasztási Manager konfigurációs fájlt.

> [!NOTE]
> Az előző két lépést ellenőrizheti. `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>5\. lépés: Indítsa el a Prometheus és Alertmanager
Töltse le a [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) sablont a [openshift/forrástárházba](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) , és alkalmazza azt a prometheus-projektben
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus-standalone.yaml fájl az OpenShift sablon, amely egy Prometheus példányt hoz létre, az oauth-proxy elé, és a egy Alertmanager példány, oauth-proxy is védi.  Ez a sablon az oauth-proxy van konfigurálva, bármely felhasználó, aki is "get" a "prometheus-projekt" névtérben (lásd a `-openshift-sar` jelző).

> [!NOTE]
> Ellenőrizheti, ha nem rendelkezik-e a "prom" StatefulSet *DESIRED* és *aktuális* a replikák száma a `oc get statefulset -n prometheus-project` parancsot.
> Ellenőrizheti a projektben az összes erőforrást is `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>6\. lépés: A Szolgáltatásészlelés engedélyezése az engedélyek hozzáadása
Hozzon létre prometheus-sdrole.yml az alábbi tartalommal.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
Ha szeretné a szolgáltatásészlelés engedélyezése az összes projekt alkalmazza a sablont.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Ha Prometheus lehessen a metrikák begyűjtéséhez a saját maga is szeretné, ne felejtse el túl alkalmazni az engedélyeket prometheus-projektben.

> [!NOTE]
> Ellenőrizheti, ha a szerepkör és a RoleBinding megfelelően együtt létrehozott a `oc get role` és `oc get rolebinding` parancsokat jelölik

## <a name="optional-deploy-example-application"></a>Nem kötelező: A példában üzembe helyezése
Minden jól működik, de nincsenek metrikák források. Nyissa meg a Prometheus URL-cím (https://prom-prometheus-project.apps. *véletlenszerű azonosítót*. *régió*.azmosa.io/), amely a következő paranccsal található.
```
oc get route prom -n prometheus-project
```
Ne felejtse el az állomásnevet a https:// előtagot.

A **állapot > Szolgáltatásészlelés** lapon jelennek meg 0/0 aktív célokat.

A mintaalkalmazás, amely alapszintű python-metrikák alapján a /metrics végpont a következő parancsok futtatásával telepítse.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Az új alkalmazásokat üzembe helyezés után megjelenik a Szolgáltatásészlelés oldalon belüli 30s érvényes célként. További részleteket is megjeleníthet a **állapot > célok** lapot.

> [!NOTE]
> Minden sikeresen scraped cél Prometheus hozzáadása egy datapoint a "maximum" metrikát. Kattintson a **Prometheus** bal felső sarokban, és adja meg a "be" a kifejezés, kattintson **Execute**.

## <a name="next-steps"></a>További lépések

Az alkalmazások saját Prometheus üzemállapot-mérési adhat hozzá.

A Prometheus ügyféloldali kódtár, amely leegyszerűsíti előkészítés Prometheus metrikák, készen áll a különböző programozási nyelvekhez.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Ugrás https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
