---
title: Gyakorlati tanácsok a fürtbiztonsággal kapcsolatosan
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürtfelelősök ajánlott eljárásait a fürtbiztonság és -frissítések kezeléséhez az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 3d4e8577116ba1d78aaa881887f64e71c04af4f2
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668329"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Gyakorlati tanácsok a fürtbiztonsághoz és az Azure Kubernetes-szolgáltatás (AKS) frissítéseihez

A fürtök kezelése az Azure Kubernetes Szolgáltatás (AKS), a számítási feladatok és az adatok biztonsága kulcsfontosságú szempont. Különösen akkor, ha több-bérlős fürtök logikai elkülönítés használatával fut, erőforrásokhoz és számítási feladatokhoz való hozzáférés biztosítása szükséges. A támadás veszélyének minimalizálása érdekében győződjön meg arról is, hogy a legújabb Kubernetes és a csomópont operációs rendszer biztonsági frissítéseit alkalmazza.

Ez a cikk az AKS-fürt biztonságossá tételével kapcsolatos. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Active Directory és a szerepköralapú hozzáférés-vezérlők használata az API-kiszolgálók biztonságos eléréséhez
> * Biztonságos tárolóhozzáférés a csomópont-erőforrásokhoz
> * AKS-fürt frissítése a legújabb Kubernetes-verzióra
> * A csomópontok naprakészen tartása és automatikus alkalmazása a biztonsági javításokon

A [tárolórendszerkép-kezelés][best-practices-container-image-management] és a [podbiztonság][best-practices-pod-security]ajánlott eljárásait is elolvashatja.

Az Azure [Kubernetes Services biztonsági központtal való integrációja][security-center-aks] segítségével észlelheti a fenyegetéseket, és javaslatokat kaphat az AKS-fürtök védelmére.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés az API-kiszolgálóhoz és a fürtcsomópontokhoz

**Ajánlott eljárások –** A Kubernetes API-Server elérésének biztonságossá tétele az egyik legfontosabb dolog, amit a fürt biztonságossá tétele érdekében tehet. Integrálja a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) az Azure Active Directoryval az API-kiszolgálóhoz való hozzáférés szabályozásához. Ezek a vezérlők lehetővé teszik az AKS-t ugyanúgy biztonságossá, mint az Azure-előfizetésekhez való hozzáférést.

A Kubernetes API-kiszolgáló egyetlen csatlakozási pontot biztosít a fürtön belüli műveletek végrehajtására irányuló kérelmek számára. Az API-kiszolgálóhoz való hozzáférés biztonságossá tétele és naplózása érdekében korlátozza a hozzáférést, és adja meg a szükséges legkevésbé kiemelt hozzáférési engedélyeket. Ez a megközelítés nem egyedi a Kubernetes számára, de különösen fontos, ha az AKS-fürt logikailag elkülönül a több-bérlős használathoz.

Az Azure Active Directory (AD) egy nagyvállalati használatra kész identitáskezelési megoldást biztosít, amely integrálható az AKS-fürtökkel. Mivel a Kubernetes nem biztosít identitáskezelési megoldást, egyébként nehéz lehet részletes módot biztosítani az API-kiszolgálóhoz való hozzáférés korlátozására. Az AKS-ben az Azure AD-be integrált fürtökkel a meglévő felhasználói és csoportfiókok segítségével hitelesítheti a felhasználókat az API-kiszolgálón.

![Az Azure Active Directory integrációja AKS-fürtökhöz](media/operator-best-practices-cluster-security/aad-integration.png)

A Kubernetes RBAC és az Azure AD-integráció használatával biztonságossá teszi az API-kiszolgálót, és adja meg a hatókörrel rendelkező erőforrások, például egy névtér szükséges legkevesebb engedélyeket. Az Azure AD különböző felhasználói vagy csoportjai különböző RBAC-szerepköröket kaphatnak. Ezek a részletes engedélyek lehetővé teszik az API-kiszolgálóhoz való hozzáférés korlátozását, és a végrehajtott műveletek egyértelmű naplózási nyomvonalát.

Az ajánlott eljárás az, hogy csoportok használatával hozzáférést biztosít a fájlokhoz és mappákhoz az egyes identitásokhoz képest, az Azure AD *csoporttagság* használatával a felhasználókat az RBAC-szerepkörökhöz kötik az egyes *felhasználók*helyett. Afelhasználó csoporttagságának változásakor az AKS-fürthöz való hozzáférési engedélyeik ennek megfelelően változnak. Ha a felhasználót közvetlenül egy szerepkörhöz köti, a feladatfunkciójuk megváltozhat. Az Azure AD-csoporttagságok frissülnének, de az AKS-fürtengedélyei ezt nem tükröznék. Ebben az esetben a felhasználó végül több engedélyt kap, mint amire a felhasználónak szüksége van.

Az Azure AD-integrációról és az RBAC-ról az [AKS-ben a hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok][aks-best-practices-identity]című témakörben talál további információt.

## <a name="secure-container-access-to-resources"></a>Tárolóhozzáférés biztonságossá tétele az erőforrásokhoz

**Ajánlott eljárásokra vonatkozó útmutatás** – Korlátozza a hozzáférést a tárolók által végrehajtható műveletekhez. Adja meg a legkevesebb engedélyeket, és kerülje a root / privilegizált eszkaláció használatát.

Ugyanúgy, ahogy a felhasználóknak vagy csoportoknak a szükséges legkevesebb jogosultságot kell biztosítani, a tárolókat csak a szükséges műveletekre és folyamatokra kell korlátozni. A támadás veszélyének minimalizálása érdekében ne konfigurálja az eszkalált jogosultságokat vagy root hozzáférést igénylő alkalmazásokat és tárolókat. Például a `allowPrivilegeEscalation: false` pod jegyzékfájlban beállított. Ezek *a pod biztonsági környezetek* vannak beépítve a Kubernetes, és lehetővé teszi, hogy további engedélyeket definiálhat, például a felhasználó vagy csoport futtatásához, vagy milyen Linux-képességek et elérhetővé tenni. További gyakorlati tanácsok: [Biztonságos pod hozzáférést biztosít az erőforrásokhoz.][pod-security-contexts]

A tárolóműveletek részletesebb vezérlése érdekében olyan beépített Linux-biztonsági funkciókat is használhat, mint az *AppArmor* és *a seccomp.* Ezek a funkciók a csomópont szintjén vannak definiálva, majd egy pod jegyzékfájlon keresztül valósítva meg. A beépített Linux biztonsági funkciók csak Linux-csomópontokon és podokon érhetők el.

> [!NOTE]
> Kubernetes környezetek, az AKS vagy máshol, nem teljesen biztonságos az ellenséges több-bérlős használat. További biztonsági funkciók, például *AppArmor,* *seccomp*, *Pod biztonsági házirendek*, vagy több részletes szerepkör-alapú hozzáférés-vezérlés (RBAC) a csomópontok megnehezítik a biztonsági réseket. Azonban a valódi biztonság ellenséges több-bérlős számítási feladatok futtatásakor, a hipervizor az egyetlen biztonsági szint, amelyben meg kell bíznia. A Kubernetes biztonsági tartománya a teljes fürtlesz, nem egy adott csomópont. Az ilyen típusú ellenséges több-bérlős számítási feladatok, fizikailag elkülönített fürtök használata kell használnia.

### <a name="app-armor"></a>App Páncél

A tárolók által végrehajtható műveletek korlátozásához használhatja az [AppArmor][k8s-apparmor] Linux kernel biztonsági modult. Az AppArmor az alapul szolgáló AKS-csomópont operációs rendszer részeként érhető el, és alapértelmezés szerint engedélyezve van. Olyan AppArmor-profilokat hozhat létre, amelyek korlátozzák az olyan műveleteket, mint az olvasás, az írás vagy a végrehajtás, illetve a rendszerfunkciók, például a fájlrendszerek csatlakoztatása. Az alapértelmezett AppArmor-profilok `/proc` `/sys` korlátozzák a különböző és helyekhez való hozzáférést, és leképezhetik a tárolók logikai elkülönítését az alapul szolgáló csomóponttól. AppArmor működik minden olyan alkalmazás, amely fut a Linux, nem csak Kubernetes hüvely.

![AKS-fürtben használt AppArmor-profilok a tárolóműveletek korlátozására](media/operator-best-practices-container-security/apparmor.png)

Az AppArmor működés közbeni megtekintéséhez a következő példa létrehoz egy profilt, amely megakadályozza a fájlokba írást. [SSH-t][aks-ssh] egy AKS-csomóponthoz, majd hozzon létre egy *deny-write.profile* nevű fájlt, és illessze be a következő tartalmat:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Az AppArmor-profilok `apparmor_parser` a parancs segítségével kerülnek hozzáadásra. Adja hozzá a profilt az AppArmorhoz, és adja meg az előző lépésben létrehozott profil nevét:

```console
sudo apparmor_parser deny-write.profile
```

Nincs visszaadott kimenet, ha a profil megfelelően van elemezve, és alkalmazza az AppArmor. Visszatér a parancssorba.

A helyi gépről most hozzon létre egy pod manifest nevű *aks-apparmor.yaml* és illessze be a következő tartalmat. Ez a jegyzékfájl az `container.apparmor.security.beta.kubernetes` előző lépésekben létrehozott *deny-write* profilhivatkozásaihoz egy megjegyzéssel rendelkezik:

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

Telepítse a mintapodot a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f aks-apparmor.yaml
```

A pod telepítése esetén használja a [kubectl exec parancsot][kubectl-exec] egy fájlba való íráshoz. A parancs nem hajtható végre, ahogy az a következő példa kimenetben látható:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Az AppArmorról további információt a [Kubernetes AppArmor-profiljai című témakörben][k8s-apparmor]talál.

### <a name="secure-computing"></a>Biztonságos számítástechnika

Míg AppArmor működik minden Linux alkalmazás, [seccomp *(sec*ure *comp*uting)][seccomp] működik a folyamat szintjén. Seccomp is egy Linux kernel biztonsági modul, és natívan támogatja a Docker futásidejű által használt AKS-csomópontok. A seccomp, a folyamat hívásokat, hogy a tárolók végrehajtható korlátozott. Hozzon létre szűrőket, amelyek meghatározzák, hogy milyen műveleteket engedélyezni vagy tagadni, majd a pod YAML-jegyzékfájlon belüli kommentárok használatával társítja a seccomp szűrőt. Ez igazodik az ajánlott eljárás csak a tároló a minimális engedélyeket, amelyek futtatásához szükséges, és nem több.

A seccomp működés közbeni megtekintéséhez hozzon létre egy szűrőt, amely megakadályozza a fájl engedélyeinek módosítását. [SSH-t][aks-ssh] egy AKS-csomóponthoz, majd hozzon létre egy */var/lib/kubelet/seccomp/prevent-chmod* nevű seccomp szűrőt, és illessze be a következő tartalmat:

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

A helyi gépről most hozzon létre egy *aks-seccomp.yaml* nevű pod jegyzékfájlt, és illessze be a következő tartalmat. Ez a jegyzékfájl az `seccomp.security.alpha.kubernetes.io` előző lépésben létrehozott *prevent-chmod* szűrőre vonatkozó annotációt határoz meg, és hivatkozik azokra:

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

Telepítse a mintapodot a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Tekintse meg a podok állapotát a [kubectl get pods][kubectl-get] paranccsal. A pod hibát jelez. A `chmod` seccomp szűrő nem futtatja a parancsot, ahogy az a következő példa kimenetben látható:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Az elérhető szűrőkről a [Seccomp biztonsági profiljai a Dockerhez][seccomp]című témakörben talál további információt.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeresen frissítsen a Kubernetes legújabb verziójára

**Ajánlott eljárások –** Az új funkciók és hibajavítások naprakészen maradásához rendszeresen frissítsen az AKS-fürt Kubernetes verziójára.

A Kubernetes gyorsabb ütemben bocsátki új funkciókat, mint a hagyományos infrastruktúraplatformok. A Kubernetes-frissítések új funkciókat, valamint hiba- vagy biztonsági javításokat tartalmaznak. Az új funkciók általában egy *alfa,* majd *béta* állapoton haladnak át, mielőtt *stabilak* lennének, és általánosan elérhetők és éles használatra ajánlottak. Ez a kiadási ciklus lehetővé teszi a Kubernetes frissítését anélkül, hogy rendszeresen találkozna a törési módosításokkal, vagy módosítsa a központi telepítéseket és a sablonokat.

Az AKS a Kubernetes négy alverzióját támogatja. Ez azt jelenti, hogy egy új alverzió bevezetésekor a legrégebbi verzió- és javítási verzió kitágítja a támogatott verziót. A Kubernetes kisebb frissítései rendszeres időközönként történnek. Győződjön meg arról, hogy szükség szerint ellenőrizheti és frissítheti a cégirányítási folyamatot, hogy ne essen ki a támogatásból. További információ: [Támogatott Kubernetes-verziók AKS][aks-supported-versions]

A fürthöz rendelkezésre álló verziók ellenőrzéséhez használja az [aks get-upgrades parancsot][az-aks-get-upgrades] az alábbi példában látható módon:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután frissítheti az AKS-fürtöt az [aks frissítési][az-aks-upgrade] paranccsal. A frissítési folyamat biztonságosan kordonok és kiüríti egy csomópont egy időben, ütemezése podok a fennmaradó csomópontokon, majd telepíti az új csomópont fut a legújabb operációs rendszer és a Kubernetes verziók.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Az AKS-frissítésekről az [AKS Támogatott Kubernetes-verziói][aks-supported-versions] és [az AKS-fürt frissítése][aks-upgrade]című témakörben talál további információt.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-csomópont-frissítések és újraindítások feldolgozása kured használatával

**Gyakorlati tanácsok –** Az AKS automatikusan letölti és telepíti a biztonsági javításokat az egyes Linux-csomópontokon, de szükség esetén nem indul újra automatikusan. Használja `kured` a függőben lévő újraindítások figyelésére, majd biztonságosan kordont és a csomópont kiürítésére, hogy a csomópont újraindulhasson, alkalmazhassa a frissítéseket, és a lehető legbiztonságosabb legyen az operációs rendszer tekintetében. Windows Server-csomópontok (jelenleg előzetes verzióban az AKS- ben, rendszeresen hajtson végre egy AKS-frissítési művelet biztonságoskordon és a leeresztő podok és a frissített csomópontok üzembe helyezése.

Az AKS-ben lévő Linux-csomópontok minden este biztonsági javításokat kapnak a disztribúciófrissítési csatornáján keresztül. Ez a viselkedés automatikusan konfigurálva van, amikor a csomópontok egy AKS-fürtben vannak telepítve. A megszakítások minimalizálása és a futó számítási feladatokra gyakorolt lehetséges hatás minimalizálása érdekében a csomópontok nem indulnak újra automatikusan, ha egy biztonsági javítás vagy kernelfrissítés ezt igényli.

A Weaveworks nyílt forráskódú [kured (KUbernetes REboot Démon)][kured] projektje figyeli a függőben lévő csomópont-újraindításokat. Amikor egy Linux-csomópont újraindítást igénylő frissítéseket alkalmaz, a csomópont biztonságosan kordonnal van elhelyezve, és lemerül vetheti a fürt más csomópontjaira való áthelyezéshez és ütemezéshez. A csomópont újraindítása után újraadódik, majd visszakerül a fürtbe, és a Kubernetes folytatja a podok ütemezését. A fennakadások minimalizálása érdekében egyszerre csak egy `kured`csomópont ot indíthat újra a.

![Az AKS-csomópont újraindítási folyamata kured használatával](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha azt szeretné, hogy a finomabb `kured` szemcsevezérlés, amikor újraindul, integrálható prometheus, hogy megakadályozzák az újraindítások, ha más karbantartási események vagy fürt problémák folyamatban vannak. Ez az integráció minimálisra csökkenti a további komplikációkat a csomópontok újraindításával, miközben aktívan elhárít más problémákat.

A csomópontok újraindításának kezeléséről további információt a [Biztonsági és kernelfrissítések alkalmazása az AKS csomópontjaira című témakörben talál.][aks-kured]

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS-fürt biztonságossá tételével foglalkozott. Az alábbi témakörcikkekben az alábbi cikkekben valósíthatja meg a területeket:

* [Az Azure Active Directory integrálása az AKS-sel][aks-aad]
* [AKS-fürt frissítése a Kubernetes legújabb verziójára][aks-upgrade]
* [Biztonsági frissítések és csomópont-újraindítások feldolgozása kured][aks-kured]

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
