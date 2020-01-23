---
title: Kezelői ajánlott eljárások – a fürt biztonsága az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes Service-ben (ak) a fürt biztonságának és frissítéseinek kezelésével kapcsolatos ajánlott eljárásokat.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 2377cc2e0824911217fbc2783fdf8e86fd7b9405
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549206"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürtök biztonságához és frissítéséhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök Azure Kubernetes szolgáltatásban (ak) való kezelése során a munkaterhelések és az adatok biztonsága kulcsfontosságú szempont. Különösen akkor, ha a több-bérlős fürtöket logikai elkülönítéssel futtatja, biztonságos hozzáférést kell biztosítania az erőforrásokhoz és a munkaterhelésekhez. A támadás kockázatának csökkentése érdekében meg kell győződnie arról, hogy a legújabb Kubernetes és a csomópont operációs rendszerének biztonsági frissítéseit is alkalmazza.

Ebből a cikkből megtudhatja, hogyan védheti meg az AK-fürtöt. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az API Server-hozzáférés biztonságossá tétele Azure Active Directory és szerepköralapú hozzáférés-vezérléssel
> * Biztonságos tároló hozzáférése a csomópont erőforrásaihoz
> * AK-fürt frissítése a legújabb Kubernetes-verzióra
> * A csomópontok naprakészen tartása és a biztonsági javítások automatikus alkalmazása

Olvassa el a [tárolók képkezelésével][best-practices-container-image-management] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

Az [Azure Kubernetes Services integrációját a Security Center][security-center-aks] segítségével is felhasználhatja a fenyegetések észlelésére és az AK-fürtök biztonságossá tételére vonatkozó javaslatok megtekintésére.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés az API-kiszolgálóhoz és a fürtcsomópontokhöz

**Ajánlott eljárás – útmutató** – a Kubernetes API-hoz való hozzáférés biztonságossá tétele az egyik legfontosabb dolog, amit a fürt biztonságossá tételéhez használhat. Integrálja a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) az API-kiszolgáló elérésének vezérléséhez Azure Active Directory használatával. Ezek a vezérlőelemek lehetővé teszik az ak-nak az Azure-előfizetésekhez való biztonságos hozzáférését.

A Kubernetes API-kiszolgáló egyetlen kapcsolódási pontot biztosít a fürtökön belüli műveletek végrehajtásához. Az API-kiszolgálóhoz való hozzáférés biztonságossá tételéhez és naplózásához korlátozza a hozzáférést, és adja meg a minimálisan szükséges jogosultsági szintű hozzáférési engedélyeket. Ez a megközelítés nem egyedi a Kubernetes, de különösen fontos, ha az AK-fürt logikailag el van különítve a több-bérlős használatra.

A Azure Active Directory (AD) egy nagyvállalati használatra kész identitáskezelési megoldást biztosít, amely az AK-fürtökkel integrálható. Mivel a Kubernetes nem biztosít Identitáskezelés-kezelési megoldást, máskülönben nehéz lehet megszabni az API-kiszolgálóhoz való hozzáférés korlátozásának részletes módját. Az Azure AD-vel integrált, az AK-ban működő fürtökkel a meglévő felhasználói és csoportfiókok használatával hitelesítheti a felhasználókat az API-kiszolgálón.

![Azure Active Directory-integráció az AK-fürtökhöz](media/operator-best-practices-cluster-security/aad-integration.png)

Az Kubernetes RBAC és az Azure AD-Integration használatával biztosíthatja az API-kiszolgáló védelmét, és megadhatja a hatókörön belüli erőforrások (például egyetlen névtér) számára szükséges engedélyek minimális számát. Az Azure AD különböző felhasználói vagy csoportjai különböző RBAC szerepköröket biztosíthatnak. Ezek a részletes engedélyek lehetővé teszik az API-kiszolgálóhoz való hozzáférés korlátozását, valamint a végrehajtott műveletek egyértelmű naplózását.

Az ajánlott eljárás az, ha csoportok használatával biztosít hozzáférést a fájlokhoz és mappákhoz, illetve az egyéni identitásokhoz, az *Azure ad-csoporttagság használatával* a felhasználókat az egyéni *felhasználók*helyett RBAC-szerepkörökhöz kötheti. A felhasználó csoporttagság-változása miatt az AK-fürtön való hozzáférési engedélyeik ennek megfelelően változnak. Ha a felhasználót közvetlenül egy szerepkörhöz köti, a feladat funkciója változhat. Az Azure AD-csoporttagságok frissítése megtörténne, de az AK-fürt engedélyei nem tükrözik ezt. Ebben az esetben a felhasználó a felhasználó által igényelt több engedélyt kap.

Az Azure AD-integrációval és a RBAC kapcsolatos további információkért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Biztonságos tároló hozzáférése az erőforrásokhoz

**Ajánlott eljárási útmutató** – korlátozza a tárolók által végrehajtható műveletekhez való hozzáférést. Adja meg a legkevesebb engedélyt, és kerülje a gyökér/privilegizált eszkaláció használatának elkerülését.

Ugyanúgy, ahogy a lehető legkevesebb jogosultsággal rendelkező felhasználókat vagy csoportokat kell megadnia, a tárolókat csak a szükséges műveletekre és folyamatokra kell korlátozni. A támadás kockázatának csökkentése érdekében ne konfigurálja az olyan alkalmazásokat és tárolókat, amelyek nem igényelnek kiterjesztésű jogosultságokat vagy rendszergazdai hozzáférést. Például állítsa be a `allowPrivilegeEscalation: false`t a pod manifestban. Ezek a *Pod biztonsági környezetek* a Kubernetes-be vannak építve, és lehetővé teszik további engedélyek megadását, például a felhasználó vagy csoport számára a futtatását, illetve a Linux-képességek elérhetővé tétele érdekében. További ajánlott eljárások: [biztonságos Pod-hozzáférés az erőforrásokhoz][pod-security-contexts].

A tárolók műveleteinek részletesebb szabályozása érdekében a beépített linuxos biztonsági funkciókat, például a *AppArmor* és a *seccompot*is használhatja. Ezek a funkciók a csomópont szintjén vannak meghatározva, majd egy Pod manifest használatával valósíthatók meg. A beépített linuxos biztonsági funkciók csak Linux-csomópontokon és hüvelyeken érhetők el.

> [!NOTE]
> A Kubernetes-környezetek (ak-ban vagy máshol) nem teljesen biztonságosak az ellenséges, több-bérlős használatra. A csomópontok további biztonsági funkciói, például a *AppArmor*, a *seccompot*, a *Pod biztonsági házirendek*, vagy a csomópontok részletes, SZEREPKÖRALAPÚ hozzáférés-vezérlése (RBAC) nehezebbé teszik a kiaknázást. Azonban az ellenséges, több-bérlős számítási feladatok futtatásakor a megfelelő biztonság érdekében a hypervisor az egyetlen biztonsági szint, amelyet megbízhatónak tart. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia.

### <a name="app-armor"></a>Alkalmazás-Armor

A tárolók által elvégezhető műveletek korlátozásához használhatja a [AppArmor][k8s-apparmor] Linux kernel biztonsági modulját. A AppArmor a mögöttes AK Node operációs rendszer részeként érhető el, és alapértelmezés szerint engedélyezve van. Olyan AppArmor-profilokat hozhat létre, amelyek korlátozzák az olyan műveleteket, mint például az olvasás, az írás, a végrehajtás vagy a rendszerfüggvények, például a fájlrendszerek csatlakoztatása. Az alapértelmezett AppArmor-profilok a különböző `/proc` és `/sys` helyekhez való hozzáférést korlátozzák, és lehetővé teszik a tárolók logikai elkülönítését az alapul szolgáló csomópontból. A AppArmor minden Linux rendszeren futó alkalmazás esetében működik, nem csak Kubernetes hüvelyekben.

![AppArmor-profilok használata egy AK-fürtben a tároló műveleteinek korlátozásához](media/operator-best-practices-container-security/apparmor.png)

A AppArmor működés közbeni megtekintéséhez a következő példa egy olyan profilt hoz létre, amely megakadályozza a fájlok írását. [SSH][aks-ssh] -t egy AK-csomópontra, majd hozzon létre egy *deny-Write. profil* nevű fájlt, és illessze be az alábbi tartalmat:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

A AppArmor-profilok a `apparmor_parser` parancs használatával lesznek hozzáadva. Adja hozzá a profilt a AppArmor-hez, és adja meg az előző lépésben létrehozott profil nevét:

```console
sudo apparmor_parser deny-write.profile
```

Ha a profilt helyesen elemezték, és a AppArmor alkalmazza, a rendszer nem adja vissza a kimenetet. Visszatér a parancssorba.

A helyi gépről hozzon létre egy *AppArmor. YAML* nevű Pod manifest-et, és illessze be az alábbi tartalmat. Ez a jegyzékfájl az előző lépésekben létrehozott *deny-Write* profilra mutató hivatkozásokat definiáló `container.apparmor.security.beta.kubernetes` hozzáadási megjegyzését határozza meg:

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

Az üzembe helyezett Pod használatával a [kubectl exec][kubectl-exec] paranccsal írhat fájlba. A parancs nem hajtható végre, ahogy az a következő példában látható:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

További információ a AppArmor: [AppArmor-profilok a Kubernetes-ben][k8s-apparmor].

### <a name="secure-computing"></a>Biztonságos számítástechnika

Míg a AppArmor bármely Linux-alkalmazás esetében működik, a [seccompot (*mp*ure *comp*uting)][seccomp] a folyamat szintjén működik. A seccompot egy Linux kernel biztonsági modul is, és natív módon támogatja az AK-csomópontok által használt Docker-futtatókörnyezet. A seccompot esetében a tárolók által végrehajtható folyamat meghívása korlátozott. Olyan szűrőket hozhat létre, amelyek meghatározzák, hogy milyen műveleteket lehet engedélyezni vagy megtagadni, majd a YAML-jegyzékfájlon belüli megjegyzések használatával társítsa a seccompot-szűrőt. Ez arra az ajánlott eljárásra illeszkedik, hogy csak a tárolót adja meg a minimálisan szükséges engedélyekkel, és nem több.

A seccompot működés közbeni megtekintéséhez hozzon létre egy szűrőt, amely megakadályozza a fájlok engedélyeinek módosítását. [SSH][aks-ssh] -t egy AK-csomópontra, majd hozzon létre egy */var/lib/kubelet/seccomp/Prevent-chmod* nevű seccompot-szűrőt, és illessze be az alábbi tartalmat:

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

A helyi gépről hozzon létre egy *seccompot. YAML* nevű Pod manifest-et, és illessze be az alábbi tartalmat. Ez a jegyzékfájl meghatározza a `seccomp.security.alpha.kubernetes.io` jegyzetét, és hivatkozik az előző lépésben létrehozott *megakadályozó-chmod* szűrőre:

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

A hüvelyek állapotát a [kubectl Get hüvely][kubectl-get] parancs használatával tekintheti meg. A pod hibát jelez. A seccompot szűrő nem futtatja a `chmod` parancsot, ahogy az a következő példában látható:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

További információ az elérhető szűrőkkel kapcsolatban: [seccompot biztonsági profilok a Docker-hez][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeres frissítés a Kubernetes legújabb verziójára

**Ajánlott eljárási útmutató** – ha naprakészen szeretne maradni az új funkciók és hibajavítások terén, rendszeresen frissítsen az Kubernetes-verzióra az AK-fürtben.

A Kubernetes a hagyományos infrastruktúra-platformoknál gyorsabb ütemben bocsátja ki az új funkciókat. A Kubernetes-frissítések közé tartoznak az új funkciók, valamint a hibák vagy biztonsági javítások. Az új funkciók általában az *alfa* , majd a *Beta* állapotba kerülnek, mielőtt azok *stabilak* lesznek, és általánosan elérhetők és éles használatra ajánlottak. Ennek a kiadási ciklusnak lehetővé kell tennie a Kubernetes frissítését anélkül, hogy rendszeresen megtapasztalja a módosításokat, vagy módosítania kell az üzembe helyezéseket és a sablonokat.

Az AK támogatja a Kubernetes négy kisebb verzióját. Ez azt jelenti, hogy amikor új, kisebb javításokat tartalmazó verziót vezet be, a rendszer kivonja a legrégebbi másodlagos verziót és a javítási kiadásokat. A Kubernetes kisebb frissítései rendszeres időközönként történnek. Ellenőrizze, hogy van-e irányítási folyamata, és szükség szerint frissítsen, hogy ne érje el a támogatást. További információ: [támogatott Kubernetes-verziók (ak][aks-supported-versions] )

A fürthöz elérhető verziók ellenőrzését a következő példában látható módon tekintheti meg az az [AK Get-Upgrades][az-aks-get-upgrades] parancs használatával:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután az az [AK upgrade][az-aks-upgrade] paranccsal frissítheti az AK-fürtöt. A frissítési folyamat biztonságosan kiüríti, és egyszerre egy csomópontot helyez el, ütemezi a megmaradt csomópontokon a hüvelyt, majd telepíti a legújabb operációs rendszert és Kubernetes-verziót futtató új csomópontot.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

További információ az AK-beli Frissítésekről: az [AK-ban támogatott Kubernetes-verziók][aks-supported-versions] és az AK- [fürtök frissítése][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-csomópontok frissítésének és újraindításának feldolgozása a kured használatával

**Ajánlott eljárási útmutató** – az AK automatikusan letölti és telepíti a biztonsági javításokat minden Linux-csomóponton, de szükség esetén nem indul újra automatikusan. A függőben lévő újraindítások figyeléséhez `kured` használatával tekintse meg a csomópontot, majd biztonságosan rögzítse és ürítse ki a csomópontot, hogy a csomópont újrainduljon, alkalmazza a frissítéseket, és a lehető legbiztonságosabb legyen az operációs rendszer tekintetében. A Windows Server-csomópontok (jelenleg előzetes verzióban, az AK-ban) rendszeresen végrehajtanak egy AK-os frissítési műveletet, amely biztonságosan kiüríti a hüvelyeket, és telepíti a frissített csomópontokat

Minden este az AK-ban található Linux-csomópontok a disztribúció frissítési csatornáján keresztül elérhető biztonsági javításokat kapnak. Ez a viselkedés automatikusan konfigurálva van, mivel a csomópontok egy AK-fürtön vannak telepítve. A munkaterhelések megszakadásának és lehetséges hatásának csökkentése érdekében a csomópontok nem indulnak el automatikusan, ha biztonsági javítást vagy kernel-frissítést igényel.

A nyílt forráskódú [kured (KUbernetes reboot Daemon)][kured] Project by Weaveworks figyeli a függőben lévő csomópontok újraindítását. Ha egy Linux-csomópont újraindítást igénylő frissítéseket alkalmaz, a csomópont biztonságos kiosztása és kivonása a fürt más csomópontjain lévő hüvelyek áthelyezésére és bevezetésére történik. A csomópont újraindítása után visszakerül a fürtbe, és a Kubernetes folytatja az ütemezési hüvelyek futtatását. A fennakadások csökkentése érdekében a `kured`egyszerre csak egy csomópontot lehet újraindítani.

![Az AK-csomópont újraindítási folyamata a kured használatával](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha az újraindítások során finomabb gabona-szabályozásra van szükség, `kured` integrálható a Prometheus-vel, hogy megakadályozza az újraindítást, ha más karbantartási események vagy fürtbeli problémák vannak folyamatban. Ez az integráció a csomópontok újraindításával csökkentheti a további bonyodalmakat, amikor aktívan elhárít más problémákat.

A csomópont-újraindítások kezelésével kapcsolatos további információkért lásd: [biztonsági és kernel-frissítések alkalmazása a csomópontokra az AK-ban][aks-kured].

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AK-fürt biztonságossá tételére koncentrál. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

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
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
