---
title: Az Azure Monitor konfigurálása tárolóügynök-adatgyűjtéshez | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Azure Monitor tárolók ügynök stdout/stderr és környezeti változók naplógyűjtemény.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933021"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Ügynök-adatgyűjtés konfigurálása az Azure Monitor tárolókhoz

Az Azure Monitor tárolók gyűjti stdout, stderr és környezeti változók a tároló számítási feladatok a felügyelt Kubernetes-fürtök a tárolóügynökből. Az ügynökadatgyűjtési beállításokat egyéni Kubernetes ConfigMaps-beállítással konfigurálhatja, amely ezt a felhasználói élményt szabályozza. 

Ez a cikk bemutatja, hogyan hozhat létre ConfigMap és konfigurálhatja az adatgyűjtést az Ön igényei nek megfelelően.

>[!NOTE]
>Az Azure Red Hat OpenShift esetében egy sablon ConfigMap fájl jön létre az *openshift-azure-logging* névtérben. 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap fájlbeállítások – áttekintés

A sablon ConfigMap fájl áll rendelkezésre, amely lehetővé teszi, hogy könnyen szerkesztheti azt a testreszabások anélkül, hogy hozzon létre a semmiből. Mielőtt elkezdené, tekintse át a [ConfigMaps-ról](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) szóló Kubernetes dokumentációt, és ismerkedjen meg a ConfigMaps létrehozásának, konfigurálásának és telepítésének módjával. Ez lehetővé teszi az stderr és stdout névtérenként vagy a teljes fürtön, és a fürt összes podján/csomópontján futó tárolók környezeti változóinak szűrése.

>[!IMPORTANT]
>A tárolószámítási feladatok stderr, stderr és környezeti változók gyűjtésére támogatott minimális ügynökverzió ciprod06142019 vagy újabb. Az ügynök verziójának ellenőrzéséhez a **Csomópont** lapon jelöljön ki egy csomópontot, és a Tulajdonságok ablaktáblán az **Agent Image Tag** tulajdonság tulajdonságai jegyzetértékében. Az ügynökverziókról és az egyes kiadásokban szereplő tudnivalókról az [ügyintézői kiadási megjegyzések című témakörben talál](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)további információt.

### <a name="data-collection-settings"></a>Adatgyűjtési beállítások

Az adatgyűjtés vezérlésére konfigurálható beállítások az alábbiakban láthatók.

|Kulcs |Adattípus |Érték |Leírás |
|----|----------|------|------------|
|`schema-version` |Karakterlánc (kis- és nagybetűk megkülönböztetése) |v1 |Ez az ügynök által a ConfigMap elemzéséneknél használt sémaverzió. A jelenleg támogatott sémaverzió v1. Az érték módosítása nem támogatott, és a ConfigMap kiértékelésekor elutasításra kerül.|
|`config-version` |Sztring | | Támogatja a konfigurációs fájl verziójának nyomon követését a forrásellenőrző rendszerben/tárházban. A megengedett maximális karakterek 10, és az összes többi karakter csonkolva lesz. |
|`[log_collection_settings.stdout] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ez szabályozza, ha stdout tároló naplógyűjtése engedélyezve van. Ha a `true` beállítás, és nem névterek ki vannak`log_collection_settings.stdout.exclude_namespaces` zárva a stdout napló gyűjtemény ( beállítás alább), stdout naplók gyűjtik az összes tárolók között az összes podok/csomópontok a fürtben. Ha nincs megadva a ConfigMaps `enabled = true`alkalmazásban, az alapértelmezett érték a . |
|`[log_collection_settings.stdout] exclude_namespaces =`|Sztring | Vesszővel tagolt tömb |Kubernetes-névterek tömbje, amelyekhez a rendszer nem gyűjt iménizált logikai naplókat. Ez a beállítás `log_collection_settings.stdout.enabled` csak akkor `true`érvényes, ha a . Ha nincs megadva a ConfigMap `exclude_namespaces = ["kube-system"]`alkalmazásban, az alapértelmezett érték a .|
|`[log_collection_settings.stderr] enabled =` |Logikai | true (igaz) vagy false (hamis) |Ez szabályozza, ha stderr tároló naplógyűjtése engedélyezve van. Ha be `true` van állítva, és nincs névterek ki`log_collection_settings.stderr.exclude_namespaces` vannak zárva a stdout naplógyűjtemény ( beállítás), stderr naplók lesznek gyűjtve az összes tárolók között az összes podok/csomópontok a fürtben. Ha nincs megadva a ConfigMaps `enabled = true`alkalmazásban, az alapértelmezett érték a . |
|`[log_collection_settings.stderr] exclude_namespaces =` |Sztring |Vesszővel tagolt tömb |Kubernetes-névterek tömbje, amelyekhez az stderr-naplók nem lesznek gyűjtve. Ez a beállítás `log_collection_settings.stdout.enabled` csak akkor `true`érvényes, ha a . Ha nincs megadva a ConfigMap `exclude_namespaces = ["kube-system"]`alkalmazásban, az alapértelmezett érték a . |
| `[log_collection_settings.env_var] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ez a beállítás szabályozza a környezeti változók gyűjtését a `enabled = true` fürt összes podja/csomópontja között, és alapértelmezés szerint, ha nincs megadva a ConfigMaps. Ha a környezeti változók gyűjtése globálisan engedélyezve van, letilthatja `AZMON_COLLECT_ENV` azt egy adott tárolóhoz, ha a környezeti változót **Hamis** értékre állítja egy Dockerfile-beállítással vagy [a Pod konfigurációs fájljában](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) az **env:** szakasz alatt. Ha a környezeti változók gyűjtése globálisan le van tiltva, akkor nem engedélyezheti egy adott tároló gyűjtését (azaz a tároló szintjén egyetlen felülbírálat, hogy letiltsa a gyűjteményt, ha az globálisan már engedélyezve van).). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Logikai | true (igaz) vagy false (hamis) | Ez a beállítás szabályozza a tárolónapló-bővítést, hogy feltöltse a Name és Image tulajdonságértékeket a ContainerLog táblába írt minden naplórekordhoz a fürt összes tárolónaplója számára. Alapértelmezés szerint, `enabled = false` ha nincs megadva a ConfigMap. |

A ConfigMaps egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. Nem lehet másik ConfigMaps felülbírálja a gyűjtemények.

## <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

Hajtsa végre az alábbi lépéseket a ConfigMap konfigurációs fájl konfigurálásához és fürtbe való telepítéséhez.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) a sablonconfigMap yaml fájlt, és mentse a container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Ez a lépés nem szükséges az Azure Red Hat OpenShift használatakor, mivel a ConfigMap sablon már létezik a fürtön.

2. A ConfigMap yaml fájlt a testreszabásokkal szerkesztheti az stdout, stderr és/vagy környezeti változók összegyűjtéséhez. Ha az Azure Red Hat OpenShift ConfigMap yaml fájlját `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` szerkeszti, először futtassa a parancsot a fájl szövegszerkesztőben való megnyitásához.

    - Ha ki szeretne zárni bizonyos névtereket az stdout naplógyűjteményhez, a kulcsot/értéket a következő példával kell konfigurálnia: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Egy adott tároló környezeti változók gyűjtésének letiltásához állítsa be a kulcsot/értéket `[log_collection_settings.env_var] enabled = true` a változók globális gyűjtésének engedélyezéséhez, majd kövesse az [itt](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) leírt lépéseket az adott tároló konfigurációjának befejezéséhez.
    
    - Az stderr log collection cluster-wide letiltásához a kulcsot/értéket a következő példával kell konfigurálnia: `[log_collection_settings.stderr] enabled = false`.

3. Az Azure Red Hat OpenShifttől eltérő fürtök esetén hozza létre a ConfigMap-et a következő kubectl parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml>` az Azure Red Hat OpenShifttől eltérő fürtökön. 
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Az Azure Red Hat OpenShift esetén mentse a módosításokat a szerkesztőben.

A konfiguráció módosítása néhány percet is igénybe vehet, mielőtt életbe lépne, és a fürt összes omsagent podja újraindul. Az újraindítás egy folyamatos újraindítás az összes omsagent podok, nem minden újraindítás egy időben. Amikor az újraindítások befejeződtek, egy üzenet jelenik meg, amely `configmap "container-azm-ms-agentconfig" created`hasonló a következőhöz, és tartalmazza az eredményt: .

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy a konfiguráció sikeresen lett-e alkalmazva az Azure Red Hat OpenShifttől `kubectl logs omsagent-fdf58 -n=kube-system`eltérő fürtre, a következő paranccsal tekintse át az ügynöki pod naplóit: . Ha az omsagent podok konfigurációs hibákat tartalmaznak, a kimenet a következőhöz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A konfigurációs módosítások alkalmazásával kapcsolatos hibák is ellenőrizhetővé vehetők. A konfigurációs módosítások további hibaelhárításához a következő lehetőségek állnak rendelkezésre:

- Egy ügynök pod naplók ugyanazt `kubectl logs` a parancsot. 

    >[!NOTE]
    >Ez a parancs nem alkalmazható az Azure Red Hat OpenShift fürtre.
    > 

- Élő naplókból. Az élő naplók a következőhöz hasonló hibákat jelenítek meg:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- A **KubeMonAgentEvents** táblából a Log Analytics-munkaterületen. Az adatok küldése óránként *hiba* súlyossága a konfigurációs hibák. Ha nincsenek hibák, a táblában szereplő bejegyzés súlyossági *adatokat tartalmaz,* amelyek nem jelentenek hibákat. A **Címkék** tulajdonság további információkat tartalmaz a pod és a tároló azonosítóját, amelyen a hiba történt, valamint az első előfordulás, az utolsó előfordulás és a számláló az elmúlt órában.

- Az Azure Red Hat OpenShift segítségével ellenőrizze az **ContainerLog** omsagent naplók at a ContainerLog-táblában, és ellenőrizze, hogy az openshift-azure-naplózás naplógyűjteménye engedélyezve van-e.

Miután kijavította a hibát(oka)t a ConfigMap-ben az Azure Red Hat OpenShift-től eltérő fürtökön, mentse a yaml fájlt, és alkalmazza a frissített ConfigMaps-ot a következő parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml`. Az Azure Red Hat OpenShift esetén a parancs futtatásával szerkesztheti és mentheti a frissített ConfigMaps-ot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap-et az Azure Red Hat OpenShift-től eltérő fürtökön, és újabb konfigurációval szeretné frissíteni, szerkesztheti a korábban használt ConfigMap-fájlt, majd ugyanazzal a paranccsal alkalmazhatja, mint korábban, `kubectl apply -f <configmap_yaml_file.yaml`. Az Azure Red Hat OpenShift esetén a parancs futtatásával szerkesztheti és mentheti a frissített ConfigMaps-ot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A konfiguráció módosítása néhány percet is igénybe vehet, mielőtt életbe lépne, és a fürt összes omsagent podja újraindul. Az újraindítás egy folyamatos újraindítás az összes omsagent podok, nem minden újraindítás egy időben. Amikor az újraindítások befejeződtek, egy üzenet jelenik meg, amely `configmap "container-azm-ms-agentconfig" updated`hasonló a következőhöz, és tartalmazza az eredményt: .

## <a name="verifying-schema-version"></a>Sémaverzió ellenőrzése

A támogatott konfigurációs sémaverziók pod-annotációként (sémaverzióként) érhetők el az omsagent podon. Láthatjuk őket a következő kubectl parancs:`kubectl describe pod omsagent-fdf58 -n=kube-system`

A kimenet a következőhöz hasonlóan jelenik meg a megjegyzésséma-verziókkal:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>További lépések

- A tárolók Azure Monitor nem tartalmaz egy előre meghatározott riasztások készletét. Tekintse át a teljesítményriasztások létrehozása az [Azure Monitor tárolók,](container-insights-alerts.md) hogyan hozhat létre ajánlott riasztásokat a magas CPU és a memória kihasználtsága a DevOps vagy az operatív folyamatok és eljárások támogatása érdekében.

- Ha a figyelés engedélyezve van az AKS vagy a hibrid fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-kihasználtságának összegyűjtéséhez, ismerje meg, [hogyan használhatja az](container-insights-analyze.md) Azure Monitort tárolókhoz.

- Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez vagy testreszabásához a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.
