---
title: Ajánlott eljárások operátor - Fürtbiztonság az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a fürt operátor ajánlott eljárást a fürt biztonsági és az Azure Kubernetes Service (AKS) frissítések kezelése
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: d9ce2661fbdca0a28f917e27e27a3e3f954a9999
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488382"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások Fürtbiztonság és frissítése az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), a számítási feladatok és az adatok biztonságát a legfontosabb szempont. Különösen akkor, amikor több-bérlős fürtökhöz logikai elkülönítés használatával futtatja, biztonságos hozzáférés az erőforrásokhoz és a számítási feladatokat szeretne. A támadás veszélyét minimalizálása érdekében is kell mindenképpen alkalmazza a legújabb Kubernetes, a csomópont operációs rendszer biztonsági frissítéseit.

Ez a cikk az AKS-fürt biztonságossá tétele összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Active Directory és a szerepköralapú hozzáférés-vezérlés biztonságos API-t server hozzáférést
> * Biztonságos tároló hozzáférés a csomópont-erőforrásokhoz
> * A legújabb Kubernetes AKS-fürt frissítése
> * A mai napig tartani a csomópontok frissítése, és automatikusan alkalmazza a biztonsági javítások

Ajánlott eljárást is olvashatja [kép tárolókezelés] [ best-practices-container-image-management] és [pod biztonsági][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés API-t kiszolgáló és fürt csomópontjain

**Ajánlott eljárásokkal kapcsolatos útmutatás** – biztonságos hozzáférés a Kubernetes API-kiszolgáló a fürt biztonságossá teheti a legfontosabb témák egyikét. Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) integrálása az Azure Active Directory, az API-kiszolgálóhoz való hozzáférés szabályozásához. Ezek a vezérlők segítségével biztonságos AKS ugyanolyan módon, hogy biztonságos-e hozzáférést az Azure-előfizetést.

A Kubernetes API-t kiszolgáló biztosítja a kéréseket a fürtön belül műveletek elvégzésére egy egyetlen szolgáltatáskapcsolati pont. Biztonságos, és az API-kiszolgálóhoz való hozzáférést, korlátozhatja a hozzáférést, és adja meg a legalacsonyabb jogosultsági szintű hozzáférés szükséges engedéllyel. Ez a módszer nem egyedi a Kubernetes, de akkor különösen fontos, ha az AKS-fürtöt logikailag el különítve több-bérlős használatra.

Az Azure Active Directory (AD) biztosít egy nagyvállalati szintű identitáskezelési megoldás, amely integrálható az AKS-fürt. Kubernetes-identitáskezelési megoldás nem biztosít, mivel ellenkező esetben nehézkes lehet gondoskodhat a részletes az API-kiszolgálóhoz való hozzáférés korlátozásához. Az Azure Active Directoryba integrált fürtökhöz az aks-ben használhatja a meglévő felhasználói és csoportfiókok hitelesítheti a felhasználókat az API-kiszolgálóhoz.

![AKS-fürtök az Azure Active Directory-integráció](media/operator-best-practices-cluster-security/aad-integration.png)

Használja a Kubernetes RBAC és az Azure AD-integráció az API-kiszolgáló biztonságát, és adja meg a legkevésbé számára egy hatókörrel rendelkező erőforrások, például egyetlen szükséges engedélyek száma. Különböző felhasználók vagy csoportok Azure AD-ben is megadható különböző RBAC-szerepkörökhöz. Részletes engedélyek lehetővé teszik az API-kiszolgálóhoz való hozzáférés korlátozása, és adjon meg egy tiszta auditnapló végrehajtott műveleteket.

Az ajánlott eljárás, hogy a csoportok használatával adja meg a fájlok és mappák egyéni identitást és a hozzáférést, az Azure AD használata *csoport* kötést létrehozni a felhasználók helyett egyéni RBAC-szerepkörök tagsági *felhasználók*. A felhasználó csoporttagsági változások, mint a hozzáférési engedélyeik az AKS-fürtön szeretné módosítsa ennek megfelelően. Ha a felhasználó közvetlenül a szerepkör kötött, a feladat függvény változhatnak. Az Azure ad-ben csoporttagságok frissítené, de az AKS-fürtöt az engedélyeket, amelyek nem tükrözik. Ebben a forgatókönyvben a felhasználó említi engedély megadása a felhasználónak van szüksége több engedélyt.

Az Azure AD-integrációs és RBAC kapcsolatos további információkért lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Biztonságos tároló erőforrásokhoz való hozzáférés

**Ajánlott eljárásokkal kapcsolatos útmutatás** -tárolók által elvégezhető műveletek való hozzáférés korlátozásához. Adja meg a legkevésbé száma engedélyeket, és elkerülheti a legfelső szintű használatát / emelt szintű kiterjesztés.

Ugyanolyan módon, hogy adja meg az felhasználókat és csoportokat a legalacsonyabb jogosultságok száma szükséges tárolókat is kell korlátozni, csak a műveletek és a szükséges folyamatokat. A támadás veszélyét minimalizálása érdekében ne konfiguráljon alkalmazásokat és tárolókat, amelyek eszkalált jogosultságokat igénylő, vagy a legfelső szintű hozzáférést. Ha például `allowPrivilegeEscalation: false` a pod-jegyzékfájlban. Ezek *pod biztonsági környezetben* beépített Kubernetes és a segítségével meghatározhatja, például a felhasználó vagy csoport futtathat, további engedélyeket, vagy milyen Linux funkciók elérhetővé. További információ az ajánlott eljárásokról,: [erőforrásokhoz való hozzáférés biztonságos pod][pod-security-contexts].

Részletesebb vezérléshez tároló műveleteket, használhatja a beépített Linux biztonsági funkciók például *AppArmor* és *seccompot*. Ezeket a funkciókat a csomópont szintjén megadott, és ezután a pod jegyzékfájl keresztül megvalósított.

> [!NOTE]
> Kubernetes-környezetekből AKS vagy máshol, nem teljesen biztonságos megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. További biztonsági szolgáltatások, például a *AppArmor*, *seccompot*, *Pod biztonsági házirendek*, vagy további részletes szerepköralapú hozzáférés-vezérlést (RBAC) csomópontok azokat kihasználó támadások ellen sokkal nehezebb. Igaz biztonsági megerősítve a rosszindulatú több-bérlős-alapú számítási feladatait, azonban a hipervizort, hogy bízzon meg biztonsági csak szintjét. A Kubernetes esetében a biztonsági tartományához lesz a teljes fürtöt, nem az egyes csomópontok. Az ilyen típusú megerősítve a rosszindulatú több-bérlős számítási feladatokhoz fizikailag elkülönített fürtök kell használnia.

### <a name="app-armor"></a>Alkalmazás-motor

A tárolók által elvégezhető műveletek korlátozásához használja a [AppArmor] [ k8s-apparmor] Linux kernel biztonsági modult. AppArmor érhető el az alapul szolgáló operációs rendszer, az AKS csomópont részeként, és alapértelmezés szerint engedélyezve van. Létrehozhat AppArmor profilok, amelyek korlátozzák a műveletek például olvasási, írási, vagy hajtsa végre, vagy a rendszer funkciók, például fájlrendszerekre csatlakoztatására. Alapértelmezett AppArmor profilok különféle való hozzáférés korlátozása `/proc` és `/sys` helyeket, és adja meg az alapul szolgáló csomópontból tárolók logikailag el azt. AppArmor azzal a Linux rendszeren, nem csak a Kubernetes-podok futó alkalmazások esetében működik.

![Az AKS-fürt tároló műveletek korlátozására használt AppArmor profilok](media/operator-best-practices-container-security/apparmor.png)

Művelet AppArmor megtekintéséhez a következő példában létrehozunk egy profilt, amely megakadályozza, hogy a fájlok írása. [SSH] [ aks-ssh] egy AKS-csomópontra, majd hozzon létre egy fájlt *megtagadása write.profile* , és illessze be az alábbi tartalommal:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profilok kerülnek, használja a `apparmor_parser` parancsot. A profil hozzá AppArmor, és adja meg az előző lépésben létrehozott profil neve:

```console
sudo apparmor_parser deny-write.profile
```

Nincs semmilyen kimenet ad vissza, ha a profil megfelelően elemzi és AppArmor a alkalmazni. A parancssor használatával küld vissza.

A helyi gépen, most hozzon létre egy pod jegyzékfájlt nevű *aks-apparmor.yaml* , és illessze be az alábbi tartalommal. A jegyzékfájl meghatározza a jegyzet `container.apparmor.security.beta.kubernetes` mutató hivatkozásokat tudjon felvenni a *megtagadása írási* az előző lépésekben létrehozott profilban:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Üzembe helyezés a minta pod a [a kubectl a alkalmazni] [ kubectl-apply] parancsot:

```console
kubectl apply -f aks-apparmor.yaml
```

A pod üzembe helyezve, a használatakor a [kubectl exec] [ kubectl-exec] parancs egy fájlba írni. A parancs nem hajtható végre, az alábbi példa kimenetében látható módon:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

AppArmor kapcsolatos további információkért lásd: [AppArmor profilokat a Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Biztonságos számítástechnika

Amíg AppArmor működik minden olyan Linux-alkalmazás [seccompot (*mp*letek *comp*veletterv)] [ seccomp] a folyamat szintjén működik. Seccompot is egy Linux-kernel biztonsági modulra, és az AKS-csomópontok által használt Docker-modul által natívan támogatott. A seccompot a folyamat hívások tárolók által elvégezhető korlátozódnak. Szűrők, amelyek meghatározzák, milyen műveletek engedélyezéséhez vagy letiltásához hozzon létre, és a egy pod YAML-jegyzékfájlban jegyzetek használatával rendelje hozzá a seccompot szűrő. Ez az ajánlott eljárás szerint csak a tároló futtatásához szükséges minimális engedélyeket biztosítása, és nincs több igazítja.

Seccompot működés közben látni, hozzon létre egy szűrőt, amely megakadályozza, hogy a módosítás a fájl engedélyeit. [SSH] [ aks-ssh] egy AKS-csomópontra, majd hozzon létre nevű seccompot szűrő */var/lib/kubelet/seccomp/prevent-chmod* , és illessze be az alábbi tartalommal:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

A helyi gépen, most hozzon létre egy pod jegyzékfájlt nevű *aks-seccomp.yaml* , és illessze be az alábbi tartalommal. A jegyzékfájl meghatározza a jegyzet `seccomp.security.alpha.kubernetes.io` , és hivatkozik a *megakadályozása chmod* az előző lépésben létrehozott szűrő:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Üzembe helyezés a minta pod a [a kubectl a alkalmazni] [ kubectl-apply] parancsot:

```console
kubectl apply -f ./aks-seccomp.yaml
```

A podok használatával állapotának megtekintése a [kubectl get pods] [ kubectl-get] parancsot. A pod hibát jelez. A `chmod` parancs nem futtathatók a seccompot szűrővel az alábbi példa kimenetében látható módon:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Elérhető szűrők kapcsolatos további információkért lásd: [Seccompot biztonsági profilok Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeresen frissíti a legújabb verzióra, kubernetes

**Ajánlott eljárásokkal kapcsolatos útmutatás** – az új funkciókat és hibajavításokat tartalmaz, a rendszeres frissítés az AKS-fürt a Kubernetes-verzió legyen naprakész.

Kubernetes-kiadások új szolgáltatások gyorsabb ütemben, mint a hagyományos infrastruktúra-platformok. Kubernetes-frissítések új funkciók és hibajavításokat és biztonsági javításokat tartalmazzák. Új funkciók általában haladjon át egy *alpha* , majd *béta* állapotát, mielőtt azok *stabil* és általánosan elérhető, és éles környezetben ajánlott. A kiadási ciklus lehetővé teszi a Kubernetes frissítése rendszeresen hajt végre, amikor a kompatibilitástörő változásokat, és a központi telepítések és sablonok módosítása nélkül.

Az AKS Kubernetes négy kisebb verzióit támogatja. Ez azt jelenti, hogy amikor egy új alverzió javításverzió bemutatott, a legrégebbi kisebb verziója és a javítás kiadások támogatott kivezettük. Kubernetes kisebb frissítései rendszeres időközönként történik. Győződjön meg arról, hogy egy cégirányítási folyamat és a frissítést, mert így Ön nem esik a rendelkezésre állás támogatása szükséges. További információkért lásd: [AKS Kubernetes támogatott verziói][aks-supported-versions]

A fürt számára elérhető verziókról ellenőrzéséhez használja a [az aks get-frissítések] [ az-aks-get-upgrades] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután frissítheti az AKS fürt használata a [az aks frissítése] [ az-aks-upgrade] parancsot. A frissítési folyamat biztonságosan cordons és egyszerre csak egy csomópont kiüríti, ütemezi a többi csomópont podok és majd üzembe helyezi a legújabb operációs rendszer és a Kubernetes-verziókat futtató új csomópontot.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.3
```

További információ a frissítések az aks-ben: [az aks-ben a Kubernetes támogatott verziók] [ aks-supported-versions] és [AKS-fürt frissítése][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Folyamat csomópont frissíti, és újraindítja a kured használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** – AKS automatikusan letölti és telepíti biztonsági javításokat tartalmaz, mindegyik a feldolgozó csomópontok, de nem automatikusan indítja újra szükség esetén. Használat `kured` tekintse meg a függőben lévő újraindul, majd biztonságosan Ez a három csomópontunk, és, hogy újraindítja a csomópontot a csomópont kiürítési, alkalmazza a frissítéseket, és az operációs rendszer megállapodást lehető legbiztonságosabb lehet.

Minden este, az AKS-csomópontok lekérése a biztonsági javítások a disztribúció frissítés csatornán keresztül érhető el. Mivel a csomópontok az AKS-fürt üzembe helyezése a rendszer automatikusan konfigurálja ezt a viselkedést. Megszakítás és a számítási feladatok lehetséges hatás minimalizálása érdekében csomópontok nem automatikusan indulnak újra, ha egy biztonsági javítás, vagy kernelfrissítés írja elő.

A nyílt forráskódú [kured (démon a KUbernetes újraindítás)] [ kured] Weaveworks projektet figyeli a csomópont újraindítása függőben van. Egy csomópont újraindítását igénylő frissítések vonatkozik, amikor a csomópont biztonságosan szigetelve, és helyezze át, és a fürt többi csomópontjára a podok ütemezésének ürítve. A csomópont újraindul, miután felvettük a fürt és a Kubernetes folytatja a podok ütemezés be újra. Egyszerre csak egy csomópont leskálázáskor, újra kell indítani a megengedett `kured`.

![Az AKS csomópont újraindítási folyamatot kured használatával](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha azt szeretné, hogy részletesebben időfelbontási szint vezérelheti az újraindítások mikor `kured` integrálható Prometheus, hogy az újraindítások, ha más karbantartási események vagy a fürt problémák folyamatban van. Ez az integráció további komplikációk minimálisra csökkenti a csomópontok újraindítással kapcsolatos egyéb problémák aktívan hibaelhárítás során.

Csomópont újraindítása kezelése kapcsolatos további információkért lásd: [biztonsági és a kernel-frissítések alkalmazása a csomópontok az aks-ben][aks-kured].

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS-fürt biztonságossá tétele összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Azure Active Directory integrálása az aks-sel][aks-aad]
* [A legújabb verzióra a kubernetes AKS-fürt frissítése][aks-upgrade]
* [Folyamat biztonsági frissítések és a csomópont újraindul a kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: aad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
