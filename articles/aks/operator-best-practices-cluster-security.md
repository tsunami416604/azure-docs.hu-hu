---
title: Ajánlott eljárások operátor - Fürtbiztonság az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a fürt operátor ajánlott eljárást a fürt biztonsági és az Azure Kubernetes Service (AKS) frissítések kezelése
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 46e44804ddbabd8bf5620ad9516f1ca2d5017bfa
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019311"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások Fürtbiztonság és frissítése az Azure Kubernetes Service (AKS)

Kezelheti a fürtöket az Azure Kubernetes Service (AKS), a számítási feladatok és az adatok biztonságát a legfontosabb szempont. Különösen akkor, amikor több-bérlős fürtökhöz logikai elkülönítés használatával futtatja, biztonságos hozzáférés az erőforrásokhoz és a számítási feladatokat szeretne. A támadás veszélyét minimalizálása érdekében is kell mindenképpen alkalmazza a legújabb Kubernetes, a csomópont operációs rendszer biztonsági frissítéseit.

Ez a cikk az AKS-fürt biztonságossá tétele összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Active Directory és a szerepköralapú hozzáférés-vezérlés biztonságos API-t server hozzáférést
> * Biztonságos tároló hozzáférés a csomópont-erőforrásokhoz
> * A legújabb Kubernetes AKS-fürt frissítése
> * A mai napig tartani a csomópontok frissítése, és automatikusan alkalmazza a biztonsági javítások

Olvassa el a tárolók képkezelésével [][best-practices-container-image-management] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés API-t kiszolgáló és fürt csomópontjain

**Ajánlott eljárásokkal kapcsolatos útmutatás** – biztonságos hozzáférés a Kubernetes API-kiszolgáló a fürt biztonságossá teheti a legfontosabb témák egyikét. Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) integrálása az Azure Active Directory, az API-kiszolgálóhoz való hozzáférés szabályozásához. Ezek a vezérlők segítségével biztonságos AKS ugyanolyan módon, hogy biztonságos-e hozzáférést az Azure-előfizetést.

A Kubernetes API-t kiszolgáló biztosítja a kéréseket a fürtön belül műveletek elvégzésére egy egyetlen szolgáltatáskapcsolati pont. Biztonságos, és az API-kiszolgálóhoz való hozzáférést, korlátozhatja a hozzáférést, és adja meg a legalacsonyabb jogosultsági szintű hozzáférés szükséges engedéllyel. Ez a módszer nem egyedi a Kubernetes, de akkor különösen fontos, ha az AKS-fürtöt logikailag el különítve több-bérlős használatra.

Az Azure Active Directory (AD) biztosít egy nagyvállalati szintű identitáskezelési megoldás, amely integrálható az AKS-fürt. Kubernetes-identitáskezelési megoldás nem biztosít, mivel ellenkező esetben nehézkes lehet gondoskodhat a részletes az API-kiszolgálóhoz való hozzáférés korlátozásához. Az Azure Active Directoryba integrált fürtökhöz az aks-ben használhatja a meglévő felhasználói és csoportfiókok hitelesítheti a felhasználókat az API-kiszolgálóhoz.

![AKS-fürtök az Azure Active Directory-integráció](media/operator-best-practices-cluster-security/aad-integration.png)

Használja a Kubernetes RBAC és az Azure AD-integráció az API-kiszolgáló biztonságát, és adja meg a legkevésbé számára egy hatókörrel rendelkező erőforrások, például egyetlen szükséges engedélyek száma. Különböző felhasználók vagy csoportok Azure AD-ben is megadható különböző RBAC-szerepkörökhöz. Részletes engedélyek lehetővé teszik az API-kiszolgálóhoz való hozzáférés korlátozása, és adjon meg egy tiszta auditnapló végrehajtott műveleteket.

Az ajánlott eljárás, hogy a csoportok használatával adja meg a fájlok és mappák egyéni identitást és a hozzáférést, az Azure AD használata *csoport* kötést létrehozni a felhasználók helyett egyéni RBAC-szerepkörök tagsági *felhasználók*. A felhasználó csoporttagsági változások, mint a hozzáférési engedélyeik az AKS-fürtön szeretné módosítsa ennek megfelelően. Ha a felhasználó közvetlenül a szerepkör kötött, a feladat függvény változhatnak. Az Azure ad-ben csoporttagságok frissítené, de az AKS-fürtöt az engedélyeket, amelyek nem tükrözik. Ebben a forgatókönyvben a felhasználó említi engedély megadása a felhasználónak van szüksége több engedélyt.

Az Azure AD-integrációval és a RBAC kapcsolatos további információkért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Biztonságos tároló erőforrásokhoz való hozzáférés

**Ajánlott eljárásokkal kapcsolatos útmutatás** -tárolók által elvégezhető műveletek való hozzáférés korlátozásához. Adja meg a legkevésbé száma engedélyeket, és elkerülheti a legfelső szintű használatát / emelt szintű kiterjesztés.

Ugyanolyan módon, hogy adja meg az felhasználókat és csoportokat a legalacsonyabb jogosultságok száma szükséges tárolókat is kell korlátozni, csak a műveletek és a szükséges folyamatokat. A támadás veszélyét minimalizálása érdekében ne konfiguráljon alkalmazásokat és tárolókat, amelyek eszkalált jogosultságokat igénylő, vagy a legfelső szintű hozzáférést. Ha például `allowPrivilegeEscalation: false` a pod-jegyzékfájlban. Ezek *pod biztonsági környezetben* beépített Kubernetes és a segítségével meghatározhatja, például a felhasználó vagy csoport futtathat, további engedélyeket, vagy milyen Linux funkciók elérhetővé. További ajánlott eljárások: [biztonságos Pod-hozzáférés az erőforrásokhoz][pod-security-contexts].

Részletesebb vezérléshez tároló műveleteket, használhatja a beépített Linux biztonsági funkciók például *AppArmor* és *seccompot*. Ezeket a funkciókat a csomópont szintjén megadott, és ezután a pod jegyzékfájl keresztül megvalósított. A beépített linuxos biztonsági funkciók csak Linux-csomópontokon és hüvelyeken érhetők el.

> [!NOTE]
> A Kubernetes-környezetek (ak-ban vagy máshol) nem teljesen biztonságosak az ellenséges, több-bérlős használatra. A csomópontok további biztonsági funkciói, például a *AppArmor*, a *seccompot*, a *Pod biztonsági házirendek*, vagy a csomópontok részletes, SZEREPKÖRALAPÚ hozzáférés-vezérlése (RBAC) nehezebbé teszik a kiaknázást. Azonban az ellenséges, több-bérlős számítási feladatok futtatásakor a megfelelő biztonság érdekében a hypervisor az egyetlen biztonsági szint, amelyet megbízhatónak tart. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia.

### <a name="app-armor"></a>Alkalmazás-motor

A tárolók által elvégezhető műveletek korlátozásához használhatja a [AppArmor][k8s-apparmor] Linux kernel biztonsági modulját. AppArmor érhető el az alapul szolgáló operációs rendszer, az AKS csomópont részeként, és alapértelmezés szerint engedélyezve van. Létrehozhat AppArmor profilok, amelyek korlátozzák a műveletek például olvasási, írási, vagy hajtsa végre, vagy a rendszer funkciók, például fájlrendszerekre csatlakoztatására. Alapértelmezett AppArmor profilok különféle való hozzáférés korlátozása `/proc` és `/sys` helyeket, és adja meg az alapul szolgáló csomópontból tárolók logikailag el azt. AppArmor azzal a Linux rendszeren, nem csak a Kubernetes-podok futó alkalmazások esetében működik.

![Az AKS-fürt tároló műveletek korlátozására használt AppArmor profilok](media/operator-best-practices-container-security/apparmor.png)

Művelet AppArmor megtekintéséhez a következő példában létrehozunk egy profilt, amely megakadályozza, hogy a fájlok írása. [SSH][aks-ssh] -t egy AK-csomópontra, majd hozzon létre egy *deny-Write. profil* nevű fájlt, és illessze be az alábbi tartalmat:

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

A minta Pod üzembe helyezése a [kubectl Apply][kubectl-apply] parancs használatával:

```console
kubectl apply -f aks-apparmor.yaml
```

Az üzembe helyezett Pod használatával a [kubectl exec][kubectl-exec] paranccsal írhat fájlba. A parancs nem hajtható végre, az alábbi példa kimenetében látható módon:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

További információ a AppArmor: [AppArmor-profilok a Kubernetes-ben][k8s-apparmor].

### <a name="secure-computing"></a>Biztonságos számítástechnika

Míg a AppArmor bármely Linux-alkalmazás esetében működik, a [seccompot (*mp*ure *comp*uting)][seccomp] a folyamat szintjén működik. Seccompot is egy Linux-kernel biztonsági modulra, és az AKS-csomópontok által használt Docker-modul által natívan támogatott. A seccompot a folyamat hívások tárolók által elvégezhető korlátozódnak. Szűrők, amelyek meghatározzák, milyen műveletek engedélyezéséhez vagy letiltásához hozzon létre, és a egy pod YAML-jegyzékfájlban jegyzetek használatával rendelje hozzá a seccompot szűrő. Ez az ajánlott eljárás szerint csak a tároló futtatásához szükséges minimális engedélyeket biztosítása, és nincs több igazítja.

Seccompot működés közben látni, hozzon létre egy szűrőt, amely megakadályozza, hogy a módosítás a fájl engedélyeit. [SSH][aks-ssh] -t egy AK-csomópontra, majd hozzon létre egy */var/lib/kubelet/seccomp/Prevent-chmod* nevű seccompot-szűrőt, és illessze be az alábbi tartalmat:

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

A minta Pod üzembe helyezése a [kubectl Apply][kubectl-apply] parancs használatával:

```console
kubectl apply -f ./aks-seccomp.yaml
```

A hüvelyek állapotát a [kubectl Get hüvely][kubectl-get] parancs használatával tekintheti meg. A pod hibát jelez. A `chmod` parancs nem futtathatók a seccompot szűrővel az alábbi példa kimenetében látható módon:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

További információ az elérhető szűrőkkel kapcsolatban: [seccompot biztonsági profilok a Docker-hez][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeresen frissíti a legújabb verzióra, kubernetes

**Ajánlott eljárásokkal kapcsolatos útmutatás** – az új funkciókat és hibajavításokat tartalmaz, a rendszeres frissítés az AKS-fürt a Kubernetes-verzió legyen naprakész.

Kubernetes-kiadások új szolgáltatások gyorsabb ütemben, mint a hagyományos infrastruktúra-platformok. Kubernetes-frissítések új funkciók és hibajavításokat és biztonsági javításokat tartalmazzák. Új funkciók általában haladjon át egy *alpha* , majd *béta* állapotát, mielőtt azok *stabil* és általánosan elérhető, és éles környezetben ajánlott. A kiadási ciklus lehetővé teszi a Kubernetes frissítése rendszeresen hajt végre, amikor a kompatibilitástörő változásokat, és a központi telepítések és sablonok módosítása nélkül.

Az AKS Kubernetes négy kisebb verzióit támogatja. Ez azt jelenti, hogy amikor egy új alverzió javításverzió bemutatott, a legrégebbi kisebb verziója és a javítás kiadások támogatott kivezettük. Kubernetes kisebb frissítései rendszeres időközönként történik. Győződjön meg arról, hogy egy cégirányítási folyamat és a frissítést, mert így Ön nem esik a rendelkezésre állás támogatása szükséges. További információ: [támogatott Kubernetes-verziók (ak][aks-supported-versions] )

A fürthöz elérhető verziók ellenőrzését a következő példában látható módon tekintheti meg az az [AK Get-Upgrades][az-aks-get-upgrades] parancs használatával:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután az az [AK upgrade][az-aks-upgrade] paranccsal frissítheti az AK-fürtöt. A frissítési folyamat biztonságosan cordons és egyszerre csak egy csomópont kiüríti, ütemezi a többi csomópont podok és majd üzembe helyezi a legújabb operációs rendszer és a Kubernetes-verziókat futtató új csomópontot.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

További információ az AK-beli Frissítésekről: az [AK-ban támogatott Kubernetes-verziók][aks-supported-versions] és az AK- [fürtök frissítése][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-csomópontok frissítésének és újraindításának feldolgozása a kured használatával

**Ajánlott eljárási útmutató** – az AK automatikusan letölti és telepíti a biztonsági javításokat minden Linux-csomóponton, de szükség esetén nem indul újra automatikusan. Használat `kured` tekintse meg a függőben lévő újraindul, majd biztonságosan Ez a három csomópontunk, és, hogy újraindítja a csomópontot a csomópont kiürítési, alkalmazza a frissítéseket, és az operációs rendszer megállapodást lehető legbiztonságosabb lehet. A Windows Server-csomópontok (jelenleg előzetes verzióban, az AK-ban) rendszeresen végrehajtanak egy AK-os frissítési műveletet, amely biztonságosan kiüríti a hüvelyeket, és telepíti a frissített csomópontokat

Minden este az AK-ban található Linux-csomópontok a disztribúció frissítési csatornáján keresztül elérhető biztonsági javításokat kapnak. Mivel a csomópontok az AKS-fürt üzembe helyezése a rendszer automatikusan konfigurálja ezt a viselkedést. Megszakítás és a számítási feladatok lehetséges hatás minimalizálása érdekében csomópontok nem automatikusan indulnak újra, ha egy biztonsági javítás, vagy kernelfrissítés írja elő.

A nyílt forráskódú [kured (KUbernetes reboot Daemon)][kured] Project by Weaveworks figyeli a függőben lévő csomópontok újraindítását. Ha egy Linux-csomópont újraindítást igénylő frissítéseket alkalmaz, a csomópont biztonságos kiosztása és kivonása a fürt más csomópontjain lévő hüvelyek áthelyezésére és bevezetésére történik. A csomópont újraindul, miután felvettük a fürt és a Kubernetes folytatja a podok ütemezés be újra. Egyszerre csak egy csomópont leskálázáskor, újra kell indítani a megengedett `kured`.

![Az AKS csomópont újraindítási folyamatot kured használatával](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha azt szeretné, hogy részletesebben időfelbontási szint vezérelheti az újraindítások mikor `kured` integrálható Prometheus, hogy az újraindítások, ha más karbantartási események vagy a fürt problémák folyamatban van. Ez az integráció további komplikációk minimálisra csökkenti a csomópontok újraindítással kapcsolatos egyéb problémák aktívan hibaelhárítás során.

A csomópont-újraindítások kezelésével kapcsolatos további információkért lásd: [biztonsági és kernel-frissítések alkalmazása a csomópontokra az AK-ban][aks-kured].

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS-fürt biztonságossá tétele összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Azure Active Directory integrálása AK-val][aks-aad]
* [AK-fürt frissítése a Kubernetes legújabb verziójára][aks-upgrade]
* [Biztonsági frissítések és csomópont-újraindítások feldolgozása a kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
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
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
