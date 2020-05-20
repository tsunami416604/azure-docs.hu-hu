---
title: Az Azure arc-kompatibilis fürtkonfiguráció GitOps használata (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az Azure arc-kompatibilis fürtkonfiguráció GitOps használata (előzetes verzió)
keywords: GitOps, Kubernetes, K8s, Azure, arc, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: e945a1d39edb6dad43e66ac492eb1e5c36ff58e1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684198"
---
# <a name="use-gitops-for-an-azure-arc-enabled--configuration-preview"></a>GitOps használata Azure-beli arc-kompatibilis konfigurációhoz (előzetes verzió)

Ez az architektúra egy GitOps munkafolyamat használatával konfigurálja a fürtöt, és telepíti az alkalmazásokat. A konfigurációt a. YAML-fájlokban és a git-ben tárolt módon írja le. Az ügynök figyeli a git-tárházat a változásokhoz, és alkalmazza azokat.  Ugyanez az ügynök rendszeres időközönként azt is biztosítja, hogy a fürt állapota megfelel a git-tárházban deklarált állapotnak, és visszaadja a fürtöt a kívánt állapotnak, ha nem felügyelt módosítások történtek.

A fürt és egy vagy több git-tárház közötti kapcsolat a Azure Resource Manager `sourceControlConfiguration` kiterjesztési erőforrásként van követve. Az `sourceControlConfiguration` Erőforrás-tulajdonságok határozzák meg, hogy a Kubernetes-erőforrások hol és hogyan folynak a git és a fürt között. A `sourceControlConfiguration` rendszer az adatok titkosságának biztosítása érdekében a CosmosDb adatbázisában tárolja az adatok titkosítását.

A fürtben futó Azure arc-kompatibilis Kubernetes `config-agent` feladata az új vagy frissített erőforrások figyelése, `sourceControlConfiguration` és a git-tárház hivatkozásainak automatikus hozzáadása, frissítése vagy eltávolítása.

Ugyanazokat a mintázatokat használhatja a fürtök nagyobb gyűjteményének kezeléséhez, amelyek több heterogén környezetben is üzembe helyezhetők. Előfordulhat például, hogy rendelkezik egy adattárral, amely meghatározza a szervezet alapkonfigurációját, és egyszerre több tízezer Kubernetes-fürtöt alkalmaz.

A git-tárház bármilyen érvényes Kubernetes-erőforrást tartalmazhat, beleértve a névtereket, a ConfigMaps, a központi telepítéseket, a DaemonSets stb.  Az alkalmazások üzembe helyezéséhez is tartozhat Helm-diagramok. A gyakori forgatókönyvek közé tartozik a szervezet alapkonfigurációjának meghatározása, amely magában foglalhatja a közös RBAC-szerepköröket és-kötéseket, a figyelési vagy naplózási ügynököket, illetve a fürtre kiterjedő szolgáltatásokat is.

Ez az első lépéseket ismertető útmutató végigvezeti a konfigurációk fürt-rendszergazdai hatókörrel való alkalmazásának lépésein.

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

- Példa adattárra:<https://github.com/slack/cluster-config>

A példában szereplő adattár egy olyan fürt munkatársai köré épül fel, akik néhány névtér kiépítését szeretnék kiépíteni, közös számítási feladatok üzembe helyezését, és a csoportra jellemző konfigurálást is biztosítják. Az adattár használata a következő erőforrásokat hozza létre a fürtön:

**Névterek:** `cluster-config` , `team-a` , `team-b` 
 **üzembe helyezés:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

Az `config-agent` Azure új vagy frissített lekérdezése `sourceControlConfiguration` 30 másodpercenként.  Ez a maximális idő, ameddig az `config-agent` új vagy frissített konfiguráció felvételére kerül.
Ha privát tárházat társít, gondoskodjon arról, hogy a [konfiguráció alkalmazása privát git-tárházból](https://github.com/Azure/azure-arc-kubernetes-preview/blob/master/docs/use-gitops-in-connected-cluster.md#apply-configuration-from-a-private-git-repository) című témakör lépéseit is végrehajtsa.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

A-hez készült Azure CLI-bővítmény használatával `k8sconfiguration` csatoljuk a csatlakoztatott fürtöt egy [példa git-tárházhoz](https://github.com/Azure/arc-k8s-demo). Ennek a konfigurációnak a nevét kell megadnia `cluster-config` , arra utasítja az ügynököt, hogy telepítse az operátort a `cluster-config` névtérben, és adja meg az operátor `cluster-admin` engedélyeit.

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
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

| Forgatókönyv | Formátum | Description |
| ------------- | ------------- | ------------- |
| Privát GitHub-tárház – SSH | git@github.com:username/repo | A Flux által generált SSH-kulcspár.  A felhasználónak hozzá kell adnia a nyilvános kulcsot a GitHub-fiókhoz üzembe helyezési kulcsként. |
| Nyilvános GitHub-tárház | `http://github.com/username/repo`vagy git://github.com/username/repo   | Nyilvános git-tárház  |

Ezeket a forgatókönyveket a Flux támogatja, de még nem sourceControlConfiguration. 

| Forgatókönyv | Formátum | Description |
| ------------- | ------------- | ------------- |
| Privát GitHub-adattár – HTTPS | `https://github.com/username/repo` | A Flux nem állít elő SSH-kulcspárt.  [Utasítások](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Privát git-gazdagép | user@githost:path/to/repo | [Utasítások](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Privát GitHub-tárház – SSH (saját kulcs használata) | git@github.com:username/repo | [Saját SSH-kulcspár használata](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>További paraméterek

A konfiguráció létrehozásához a következő néhány további paramétert kell megszabni:

`--enable-helm-operator`: *Opcionális* kapcsoló a Helm chart központi telepítések támogatásának engedélyezéséhez. A beállítás alapértelmezés szerint le van tiltva.

`--helm-operator-chart-values`: Nem *kötelező* diagram-értékek a Helm-kezelőhöz (ha engedélyezve van).  Például: "--set Helm. Versions = v3".

`--helm-operator-chart-version`: Nem *kötelező* diagram-verzió a Helm-kezelőhöz (ha engedélyezve van). Alapértelmezett: "0.6.0".

`--operator-namespace`: Az operátori névtér neve nem *kötelező* . Alapértelmezett: "default"

`--operator-params`: Nem *kötelező* paraméterek a kezelőhöz. Egy idézőjelek között kell megadni. Például: ```--operator-params='--git-readonly --git-path=releases/prod' ```

A-operátor-params támogatott beállításai

| Beállítás | Description |
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

További információ: [Flux-dokumentáció](https://aka.ms/FluxcdReadme).

## <a name="validate-the-sourcecontrolconfiguration"></a>A sourceControlConfiguration ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a `sourceControlConfiguration` sikeresen létrejött-e.

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

Vegye figyelembe, hogy az `sourceControlConfiguration` erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
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
1. `config-agent`észreveszi az új `Pending` konfigurációt
1. `config-agent`beolvassa a konfigurációs tulajdonságokat, és előkészíti a felügyelt példányának telepítését`flux`
    * `config-agent`létrehozza a célhely névterét
    * `config-agent`Kubernetes-szolgáltatásfiók előkészítése a megfelelő engedélyekkel ( `cluster` vagy `namespace` hatókörrel)
    * `config-agent`Üzembe helyez egy példányt a`flux`
    * `flux`létrehoz egy SSH-kulcsot, és naplózza a nyilvános kulcsot.
1. `config-agent`a jelentések állapota vissza a következőre`sourceControlConfiguration`

A kiépítési folyamat során a `sourceControlConfiguration` átkerül néhány állapotba. A folyamat figyelése a `az k8sconfiguration show ...` fenti paranccsal:

1. `complianceStatus` -> `Pending`: a kezdeti és a folyamatban lévő állapotot jelöli.
1. `complianceStatus` -> `Compliant`: sikerült `config-agent` konfigurálni a fürtöt, és `flux` hiba nélkül üzembe helyezheti a telepítést
1. `complianceStatus` -> `Noncompliant`: `config-agent` hiba történt az üzembe helyezés során `flux` , a részleteknek elérhetőnek kell lenniük a `complianceStatus.message` Válasz törzsében.

## <a name="apply-configuration-from-a-private-git-repository"></a>Konfiguráció alkalmazása privát git-tárházból

Ha privát git-tárházat használ, a hurok bezárásához még egy feladatot kell elvégeznie: hozzá kell adnia azt a nyilvános kulcsot, amelyet `flux` **üzembe helyezési kulcsként** generált a tárházban.

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

**A nyilvános kulcs hozzáadása üzembe helyezési kulcsként a git-tárházhoz**

1. Nyissa meg a GitHubot, navigáljon az elágazásához, a **beállításokhoz**, majd a **kulcsok üzembe helyezéséhez**
2. Kattintson a **központi telepítés kulcsának hozzáadása** elemre.
3. Adja meg a címet
4. **Írási hozzáférés engedélyezése**
5. A nyilvános kulcs beillesztése (a környező idézőjelek mínusz)
6. Kattintson a **Kulcs hozzáadása** lehetőségre.

Az üzembe helyezési kulcsok kezelésével kapcsolatos további információkért tekintse meg a GitHub-dokumentációt.

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

`sourceControlConfiguration`Az Azure CLI vagy Azure Portal használatával törölheti a t.  Miután elindította a DELETE parancsot, az `sourceControlConfiguration` erőforrás azonnal törlődik az Azure-ban, de akár 1 óráig is eltarthat a társított objektumok teljes törlése a fürtből (a rendszer lerövidíti az elemet). Ha a `sourceControlConfiguration` névterek hatókörével lett létrehozva, akkor a rendszer nem törli a névteret a fürtből (Ezzel elkerülhető a névtérben esetlegesen létrehozott egyéb erőforrások feltörése).

Vegye figyelembe, hogy a fürt azon módosításait, amelyek a követett git-tárházból történő központi telepítések eredményeként történtek, a törléskor nem törlődnek `sourceControlConfiguration` .

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**Kimeneti**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>További lépések

- [A GitOps és a Helm használata a fürt konfigurációjához](./use-gitops-with-helm.md)
- [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
