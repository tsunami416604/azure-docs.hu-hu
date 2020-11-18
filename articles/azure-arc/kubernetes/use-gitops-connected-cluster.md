---
title: Konfigurációk üzembe helyezése a GitOps használatával az arc-kompatibilis Kubernetes-fürtön (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az Azure arc-kompatibilis fürtkonfiguráció GitOps használata (előzetes verzió)
keywords: GitOps, Kubernetes, K8s, Azure, arc, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659794"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Konfigurációk üzembe helyezése a GitOps használatával az arc-kompatibilis Kubernetes-fürtön (előzetes verzió)

A GitOps a Kubernetes-konfiguráció (központi telepítések, névterek stb.) kívánt állapotának deklarálása a git-tárházban, majd a konfigurációk lekérdezése és lekéréses telepítése a fürtön egy operátor használatával. Ez a dokumentum az Azure arc-kompatibilis Kubernetes-fürtökön futó ilyen munkafolyamatok beállítását ismerteti.

A fürt és egy vagy több git-tárház közötti kapcsolat a Azure Resource Manager `sourceControlConfiguration` kiterjesztési erőforrásként van követve. Az `sourceControlConfiguration` Erőforrás-tulajdonságok határozzák meg, hogy a Kubernetes-erőforrások hol és hogyan folynak a git és a fürt között. A `sourceControlConfiguration` rendszer az adatok titkosságának biztosítása érdekében a Azure Cosmos db adatbázisban titkosítva tárolja az adattárolást.

A `config-agent` fürtben való futtatás az `sourceControlConfiguration` Azure arc-kompatibilis Kubernetes-erőforrás új vagy frissített bővítményi erőforrásainak figyelésére, valamint a git-tárház figyelésére, valamint a által végzett frissítések propagálására szolgál `sourceControlConfiguration` . Még több erőforrás is létrehozható `sourceControlConfiguration` `namespace` hatókörrel ugyanazon az Azure arc-kompatibilis Kubernetes-fürtön a több-bérlős szolgáltatás eléréséhez. Ilyen esetben az egyes operátorok csak a megfelelő névtérbe telepíthetik a konfigurációkat.

A git-tárház bármilyen érvényes Kubernetes-erőforrást tartalmazhat, beleértve a névtereket, a ConfigMaps, a központi telepítéseket, a DaemonSets stb.  Az alkalmazások üzembe helyezéséhez is tartozhat Helm-diagramok. A gyakori forgatókönyvek közé tartozik a szervezet alapkonfigurációjának meghatározása, amely közös Azure-szerepköröket és-kötéseket, figyelési vagy naplózási ügynököket, vagy a fürtre kiterjedő szolgáltatásokat is magában foglalhat.

Ugyanez a minta használható a fürtök nagyobb gyűjteményének kezelésére, amely heterogén környezetekben is üzembe helyezhető. Előfordulhat például, hogy rendelkezik egy adattárral, amely meghatározza a szervezet alapkonfigurációját, és egyszerre több tízezer Kubernetes-fürtöt alkalmaz. Az [Azure Policy](use-azure-policy.md) a `sourceControlConfiguration` hatókör (előfizetés vagy erőforráscsoport) alá tartozó összes Azure arc-Kubernetes erőforrás-készlettel automatizálhatja a-t egy adott paraméterekkel.

Ez az első lépéseket ismertető útmutató végigvezeti a konfigurációk fürt-rendszergazdai hatókörrel való alkalmazásának lépésein.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy rendelkezik egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürttel. Ha csatlakoztatott fürtre van szüksége, tekintse meg a [fürt csatlakoztatása](./connect-cluster.md)rövid útmutató című témakört.

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az ebben a dokumentumban használt [példában szereplő adattár](https://github.com/Azure/arc-k8s-demo) egy olyan fürt munkatársai köré épül fel, akik néhány névteret szeretnének kiépíteni, közös munkaterhelést telepíteni, és a csoportra jellemző konfigurációt biztosítanak. Az adattár használata a következő erőforrásokat hozza létre a fürtön:

**Névterek:** `cluster-config` , `team-a` , `team-b` 
 **üzembe helyezés:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

A `config-agent` lekérdezi az Azure-t az új vagy frissített `sourceControlConfiguration` 30 másodpercenként, ami `config-agent` egy új vagy frissített konfiguráció kiválasztásához szükséges maximális idő.
Ha privát tárházat társít a `sourceControlConfiguration` alkalmazáshoz, győződjön meg arról, hogy elvégezte a [konfiguráció alkalmazása privát git-tárházból](#apply-configuration-from-a-private-git-repository)című témakör lépéseit is.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az Azure CLI-bővítményének használatával `k8sconfiguration` csatlakoztassa a csatlakoztatott fürtöt egy [példa git-tárházhoz](https://github.com/Azure/arc-k8s-demo). Ennek a konfigurációnak a nevét kell megadnia `cluster-config` , arra utasítja az ügynököt, hogy telepítse az operátort a `cluster-config` névtérben, és adja meg az operátor `cluster-admin` engedélyeit.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>adattár – URL-paraméter

Itt láthatók a--adattár-URL paraméter értékeként támogatott forgatókönyvek.

| Használati eset | Formátum | Leírás |
| ------------- | ------------- | ------------- |
| Nyilvános git-tárház | http [s]://Server/repo.git vagy git://server/repo.git   | Nyilvános git-tárház  |
| Privát git-tárház – SSH – fluxus által létrehozott kulcsok | SSH://[user@] Server/repo. git vagy [user@] Server: repo. git | A flow által generált nyilvános kulcsot hozzá kell adni a git-szolgáltató felhasználói fiókjához. Ha a központi telepítés kulcsa felhasználói fiók helyett a tárházba kerül, használja a `git@` következőt: `user@` . További részleteket [itt](#apply-configuration-from-a-private-git-repository) talál. |

Ezeket a forgatókönyveket a Flux támogatja, de a sourceControlConfiguration még nem.

| Használati eset | Formátum | Leírás |
| ------------- | ------------- | ------------- |
| Privát git-tárház – HTTPS | https://server/repo.git | Hamarosan (támogatni fogja a felhasználónevet/jelszót, a felhasználónevet/jogkivonatot, a tanúsítványt) |
| Privát git-tárház – SSH – felhasználó által megadott kulcsok | SSH://[user@] Server/repo. git vagy [user@] Server: repo. git | Hamarosan elérhető |
| Privát git-gazdagép – SSH – egyéni known_hosts | SSH://[user@] Server/repo. git vagy [user@] Server: repo. git | Hamarosan elérhető |

#### <a name="additional-parameters"></a>További paraméterek

A konfiguráció létrehozásához a következő néhány további paramétert kell megszabni:

`--enable-helm-operator` : *Opcionális* kapcsoló a Helm chart központi telepítések támogatásának engedélyezéséhez.

`--helm-operator-chart-values` : Nem *kötelező* diagram-értékek a Helm-kezelőhöz (ha engedélyezve van).  Például: "--set Helm. Versions = v3".

`--helm-operator-chart-version` : Nem *kötelező* diagram-verzió a Helm-kezelőhöz (ha engedélyezve van). Alapértelmezett: "0.6.0".

`--operator-namespace` : Az operátori névtér neve nem *kötelező* . Alapértelmezett: "default"

`--operator-params` : Nem *kötelező* paraméterek a kezelőhöz. Egy idézőjelek között kell megadni. Például: ```--operator-params='--git-readonly --git-path=releases' ```

A-operátor-params támogatott beállításai

| Beállítás | Leírás |
| ------------- | ------------- |
| --git-ág  | A git-tárház Kubernetes-jegyzékekhez használt ága. Az alapértelmezett érték a "Master". |
| – git – elérési út  | Relatív elérési út a git-tárházon belül a Flux számára a Kubernetes-jegyzékek megkereséséhez. |
| --git-ReadOnly | A git-adattárat csak olvashatónak tekinti a rendszer. A Flux nem próbál meg írni. |
| --manifest-Generation  | Ha engedélyezve van, a Flux a. Flux. YAML és a Kustomize vagy más manifest-generátorok futtatására fog keresni. |
| --git-Poll-intervallum  | Az az időszak, amikor a git-tárházat új véglegesíteni kívánja lekérdezni. Az alapértelmezett érték az "5m" (5 perc). |
| --Sync-Garbage-Collection  | Ha engedélyezve van, a Flux törli a létrehozott erőforrásokat, de már nem jelennek meg a git-ben. |
| --git-Label  | A szinkronizálási folyamat nyomon követésére szolgáló címke, amely a git-ág címkézésére szolgál.  Az alapértelmezett érték a "Flux-Sync". |
| --git-User  | A git-véglegesítő felhasználóneve. |
| – git-e-mail  | A git-végrehajtáshoz használandó e-mail-cím. |

* Ha a "--git-user" vagy a "--git-email" nincs beállítva (ami azt jelenti, hogy nem szeretné, hogy a Flux írni a tárházba), akkor a--git-ReadOnly automatikusan be lesz állítva (ha még nem állította be).

* Ha a enableHelmOperator értéke igaz, akkor a operatorInstanceName + operatorNamespace karakterláncok nem haladhatják meg a 47 karaktert.  Ha nem sikerül betartania ezt a korlátot, a következő hibaüzenetet kapja:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

További információ: Flux- [dokumentáció](https://aka.ms/FluxcdReadme).

> [!TIP]
> SourceControlConfiguration hozhat létre a Azure Portalon, valamint az Azure arc-kompatibilis Kubernetes-erőforrás panel **konfigurációk** lapján.

## <a name="validate-the-sourcecontrolconfiguration"></a>A sourceControlConfiguration ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Vegye figyelembe, hogy az `sourceControlConfiguration` erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

A `sourceControlConfiguration` létrehozása után néhány dolog a motorháztető alatt történik:

1. Az Azure arc `config-agent` figyeli az új vagy frissített konfigurációkat ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` ) Azure Resource Manager
1. `config-agent` észreveszi az új `Pending` konfigurációt
1. `config-agent` beolvassa a konfigurációs tulajdonságokat, és előkészíti a felügyelt példányának telepítését `flux`
    * `config-agent` létrehozza a célhely névterét
    * `config-agent` Kubernetes-szolgáltatásfiók előkészítése a megfelelő engedélyekkel ( `cluster` vagy `namespace` hatókörrel)
    * `config-agent` Üzembe helyez egy példányt a `flux`
    * `flux` létrehoz egy SSH-kulcsot, és naplózza a nyilvános kulcsot.
1. `config-agent` a jelentések állapota vissza a következőre `sourceControlConfiguration`

A kiépítési folyamat során a `sourceControlConfiguration` átkerül néhány állapotba. A folyamat figyelése a `az k8sconfiguration show ...` fenti paranccsal:

1. `complianceStatus` -> `Pending`: a kezdeti és a folyamatban lévő állapotot jelöli.
1. `complianceStatus` -> `Installed`: sikerült `config-agent` konfigurálni a fürtöt, és `flux` hiba nélkül üzembe helyezheti a telepítést
1. `complianceStatus` -> `Failed`: `config-agent` hiba történt az üzembe helyezés során `flux` , a részleteknek elérhetőnek kell lenniük a `complianceStatus.message` Válasz törzsében.

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát git-tárházból

Ha privát git-tárházat használ, a hurok bezárásához még egy feladatot kell elvégeznie: adja hozzá a (z `flux` ) rendszerbe állítási **kulcsként** generált nyilvános kulcsot a tárházban.

**Nyilvános kulcs beszerzése az Azure CLI használatával**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**A Azure Portal nyilvános kulcsának beolvasása**

1. A Azure Portal navigáljon a csatlakoztatott fürt erőforrásához.
2. Az erőforrás lapon válassza a "konfigurációk" lehetőséget, és tekintse meg a fürthöz tartozó konfigurációk listáját.
3. Válassza ki a privát git-tárházat használó konfigurációt.
4. A megnyíló helyi ablakban az ablak alján másolja az **adattár nyilvános kulcsát**.

Ha GitHubot használ, használja a következő két lehetőség egyikét:

**1. lehetőség: a nyilvános kulcs hozzáadása a felhasználói fiókhoz**

1. Nyissa meg a GitHubot, kattintson a lap jobb felső sarkában található profil ikonjára.
2. Kattintson a **Beállítások** elemre.
3. Kattintson az **SSH és a GPG kulcsok** lehetőségre
4. Kattintson az **új SSH-kulcs** lehetőségre.
5. Adja meg a címet
6. A nyilvános kulcs beillesztése (a környező idézőjelek mínusz)
7. Kattintson az **SSH-kulcs hozzáadása** lehetőségre.

**2. lehetőség: a nyilvános kulcs hozzáadása üzembe helyezési kulcsként a git-tárházhoz**

1. Nyissa meg a GitHubot, navigáljon a tárházhoz, és válassza a beállítások, majd a **kulcsok telepítése** **lehetőséget**.
2. Kattintson az **üzembe helyezési kulcs hozzáadása** lehetőségre.
3. Adja meg a címet
4. **Írási hozzáférés engedélyezése**
5. A nyilvános kulcs beillesztése (a környező idézőjelek mínusz)
6. Kattintson a **Kulcs hozzáadása** lehetőségre.

**Ha Azure DevOps-tárházat használ, adja hozzá a kulcsot az SSH-kulcsokhoz**

1. A jobb felső sarokban lévő **felhasználói beállítások** alatt (a profil képe mellett) kattintson a **nyilvános ssh-kulcsok** elemre.
1. Válassza az **+ új kulcs** lehetőséget
1. Adja meg a nevet
1. A nyilvános kulcs beillesztése a környező idézőjelek nélkül
1. Kattintson a **Hozzáadás** gombra

## <a name="validate-the-kubernetes-configuration"></a>A Kubernetes konfigurációjának ellenőrzése

Miután `config-agent` telepítette a `flux` példányt, a git-tárházban tárolt erőforrásoknak futniuk kell a fürtön. Ellenőrizze, hogy a névterek, a központi telepítések és az erőforrások létrejöttek-e:

```console
kubectl get ns --show-labels
```

**Kimeneti**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Láthatjuk, hogy a, a, `team-a` `team-b` és a `itops` `cluster-config` névterek létre lettek hozva.

Az `flux` operátor a következő módon lett telepítve a `cluster-config` névtérre `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Kimeneti**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>További feltárás

A konfigurációs adattár részeként telepített egyéb erőforrásokat is megismerheti:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Törölje `sourceControlConfiguration` Az Azure CLI vagy Azure Portal használatával.  Miután elindította a DELETE parancsot, az `sourceControlConfiguration` erőforrás azonnal törlődik az Azure-ban, de akár 1 óráig is eltarthat a társított objektumok teljes törlése a fürtből (az időeltolódás csökkentése érdekében).

> [!NOTE]
> Miután létrehozta a sourceControlConfiguration a névtér hatókörével, lehetséges, hogy a `edit` névterek szerepkör-kötést használó felhasználók a munkaterheléseket ezen a névtéren helyezik üzembe. Ha a `sourceControlConfiguration` névtér hatóköre törölve lesz, a névtér érintetlen marad, és a rendszer nem törli a többi számítási feladat megszakítása érdekében.
> A rendszer nem törli a fürt azon módosításait, amelyek a nyomon követett git-tárházból való üzembe helyezések eredményeként lettek törölve `sourceControlConfiguration` .

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Következő lépések

- [A Helm használata a verziókövetés konfigurációjával](./use-gitops-with-helm.md)
- [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
