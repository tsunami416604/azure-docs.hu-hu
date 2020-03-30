---
title: Kiemelt tárolók futtatása Azure Red Hat OpenShift-fürtben | Microsoft dokumentumok
description: A biztonság és a megfelelőség figyeléséhez futtasson kiemelt tárolókat.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, piros kalap
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271377"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Kiemelt tárolók futtatása Azure Red Hat OpenShift-fürtön

Az Azure Red Hat OpenShift-fürtökön nem futtathat tetszőleges kiemelt tárolókat.
Két biztonsági figyelési és megfelelőségi megoldás futtathat ARO-fürtökön.
Ez a dokumentum a biztonsági termékek szállítóinak általános OpenShift központi telepítési dokumentációjához való különbségeket ismerteti.


Olvassa el ezeket az utasításokat, mielőtt követené a szállító utasításait.
Az alábbi termékspecifikus lépésekben szereplő szakaszcímek közvetlenül a szállítók dokumentációjában szereplő szakaszcímekre vonatkoznak.

## <a name="before-you-begin"></a>Előkészületek

A legtöbb biztonsági termék dokumentációja fürt-rendszergazdai jogosultságokat feltételez.
Az ügyfélgazdák nem rendelkeznek minden jogosultsággal az Azure Red Hat OpenShift szolgáltatásban. A fürtszintű erőforrások módosításához szükséges engedélyek korlátozottak.

Először győződjön meg arról, hogy a felhasználó `oc get scc`ügyfél-rendszergazdaként van bejelentkezve a fürtbe a rendszer futtatásával. Minden felhasználó, aki tagja az ügyfél-rendszergazdai csoportnak, jogosult a fürt biztonsági környezetbeli megkötéseinek (SCC) megtekintésére.

Ezután győződjön `oc` meg `3.11.154`arról, hogy a bináris verzió .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Termékspecifikus lépések az Aqua Security számára
A módosítandó alaputasítások az Aqua Security [telepítési dokumentációjában](https://docs.aquasec.com/docs/openshift-red-hat)találhatók. Az itt futó lépések az Aqua telepítési dokumentációjával együtt fognak futni.

Az első lépés a frissítendő szükséges SCC-k jegyzetekkel való elkészülése. Ezek a jegyzetek megakadályozzák, hogy a fürt Szinkronizáló Pod jain visszaállítja az SSC-k módosításait.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>1. lépés: Előfeltételek előkészítése
Ne felejtsen el a fürtbe ARO-ügyfél-rendszergazdaként bejelentkezni a fürt-rendszergazdai szerepkör helyett.

Hozza létre a projektet és a szolgáltatásfiókot.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Ahelyett, hogy a fürt-olvasó szerepkört rendelne hozzá, rendelje hozzá az ügyfél-admin-fürt szerepkört az aqua-fiókhoz a következő paranccsal.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Folytassa az 1.  Ezek az utasítások leírják az Aqua regiszter titkának beállítását.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>2. lépés: Az Aqua Server, adatbázis és átjáró telepítése
Kövesse az Aqua dokumentációjában az aqua-console.yaml telepítéséhez mellékelt lépéseket.

Módosítsa a `aqua-console.yaml`megadott at.  Távolítsa el a két `kind: ClusterRole` tetejére feliratozott objektumot, és `kind: ClusterRoleBinding`a .  Ezek az erőforrások nem jönnek létre, mivel az ügyfél-rendszergazda `ClusterRole` `ClusterRoleBinding` jelenleg nem rendelkezik módosítási engedéllyel és objektumokkal.

A második módosítás a `kind: Route` `aqua-console.yaml`. Cserélje le a következő `kind: Route` yaml `aqua-console.yaml` az objektumot a fájlban.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Kövesse a többi utasítást.

### <a name="step-3-login-to-the-aqua-server"></a>3. lépés: Bejelentkezés az Aqua Szerverre
Ez a szakasz semmilyen módon nem módosul.  Kövesse az Aqua dokumentációját.

Az Aqua Console címének leéséhez használja a következő parancsot.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>4. lépés: Aqua enforcers telepítése
A kényszerítők telepítésekor állítsa be a következő mezőket:

| Mező          | Érték         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| Szolgáltatásfiók | aqua-számla  |
| Project        | aqua-biztonság |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Termékspecifikus lépések a Prisma Cloud / Twistlock-hoz

Az alaputasítások, amelyeket módosítani fogunk, megtalálhatók a [Prisma Cloud telepítési dokumentációjában](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Először telepítse `twistcli` az eszközt a "Prisma Cloud telepítése" és a "Prisma Cloud szoftver letöltése" című részben leírtak szerint.

Új OpenShift-projekt létrehozása
```
oc new-project twistlock
```

Ugorja át a "Nyomja meg a Prisma Cloud-lemezképeket egy privát beállításjegyzékbe" című opcionális szakaszt. Az Azure Red Hat Openshift nem fog működni. Használja inkább az online rendszerleíró adatbázist.

A hivatalos dokumentációt az alábbiakban ismertetett javítások alkalmazása közben követheti.
Kezdje a "Konzol telepítése" című részével.

### <a name="install-console"></a>Konzol telepítése

Lépés `oc create -f twistlock_console.yaml` során a 2.
Nyugodtan figyelmen kívül hagyhatja, a névtér már korábban létre a `oc new-project` parancsot.

Tárolótípushoz használható. `azure-disk`

### <a name="create-an-external-route-to-console"></a>Külső útvonal létrehozása a konzolhoz

Akkor sem követi a dokumentációt, vagy az alábbi utasításokat, ha inkább az oc parancsot.
Másolja a következő útvonaldefiníciót egy twistlock_route.yaml nevű fájlba a számítógépen
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
majd fuss:
```
oc create -f twistlock_route.yaml
```

Ezzel a paranccsal kaphatja meg a Twistlock konzolhoz rendelt URL-címet:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konzol konfigurálása

Kövesse a Twistlock dokumentációját.

### <a name="install-defender"></a>Defender telepítése

A `oc create -f defender.yaml` 2.
Figyelmen kívül hagyhatja őket.

A jogvédők csak számítási csomópontokon lesznek telepítve. Nem kell korlátozni őket egy csomópont választó.
