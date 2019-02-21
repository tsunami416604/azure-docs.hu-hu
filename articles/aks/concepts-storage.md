---
title: Alapelvei – a Kubernetes az Azure Storage Services-(AKS)
description: Tárolás az Azure Kubernetes Service (AKS), beleértve a köteteket, állandó köteteket, storage osztályai és jogcímek
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: fd301967800f67d95c12f1689981b2dfd8eb2d80
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452763"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) az alkalmazások tárolási lehetőségeket

Az Azure Kubernetes Service (AKS) futó alkalmazásokhoz szükség lehet adatok tárolására és lekérésére. Egyes alkalmazások és szolgáltatások számára az adatok tárolási helyi, gyors tárolás használhatja, amely már nincs szükség a podok törlése esetén a csomóponton. Más alkalmazások és szolgáltatások használ, amely az több normál adatokon az Azure platformon lévő kötetek tároló lehet szükség. Több podok megoszthatja az ugyanazon az adatkötetek, vagy csatlakoztassa újból az adatkötetek, ha a pod átütemezése egy másik csomóponton szükség lehet. Végül szükség lehet beszúrása a bizalmas adatok vagy felskálázzuk az alkalmazás konfigurációs adatait.

![Az Azure Kubernetes szolgáltatás (AKS)-fürt az alkalmazások tárolási lehetőségeket](media/concepts-storage/aks-storage-options.png)

Ez a cikk bemutatja az alapfogalmakat, amelyek az aks-ben az alkalmazások tárolási megoldás biztosítása:

- [Kötetek](#volumes)
- [Tartós kötet](#persistent-volumes)
- [Storage osztályai](#storage-classes)
- [Tartós kötet jogcímek](#persistent-volume-claims)

## <a name="volumes"></a>Kötetek

Alkalmazások gyakran kell tudni adatok tárolására és lekérésére. Kubernetes általában rövid élettartamú, rendelkezésre álló erőforrások egyedi podok kezeli, ahogy alkalmazásokhoz használhatja, és szükség szerint adatmegőrzésre többféle érhetők el. A *kötet* jelenti oly módon való tárolása, lekérése és adatmegőrzésre podok és az alkalmazás-életciklus során.

Hagyományos kötetek adatok tárolására és lekérésére, az Azure Storage-alapú Kubernetes-erőforrások jönnek létre. Manuálisan hozzá kell rendelni podok közvetlenül ezen adatok köteteket hozhat létre, vagy rendelkezik a Kubernetes automatikusan létrehozza. Ezek az adatkötetek az Azure-lemezek vagy az Azure Files használhatja:

- *Az Azure Disks* segítségével hozzon létre egy Kubernetes *DataDisk* erőforrás. Lemezek az Azure Premium storage nagy teljesítményű SSD-k által támogatott vagy standard szintű Azure storage normál meghajtókra. A legtöbb éles és fejlesztési feladatokhoz prémium szintű storage használata. Azure-lemezek csatlakoztatva vannak *ReadWriteOnce*, így elérhetők csak egyetlen csomópont. A tároló kötetek, amelyek több csomópontja egyidejűleg hozzáférhetők az Azure Files használatának.
- *Az Azure Files* csatlakoztatni az SMB 3.0-megosztás biztonsági készít egy Azure Storage-fiókot a podok is használható. Adatok megosztása több csomópontot és podok fájlok segítségével. Fájlok jelenleg csak használhatja a standard szintű Azure storage normál meghajtókra.

Kubernetes, a köteteket hozhat létre több, mint egy hagyományos lemez ahol információk tárolásának és lekérdezni. Kubernetes-köteteket is használhatják arra, hogy az adatok behelyezése egy pod használatra a tárolókat. A Kubernetes használt további kötet típusok a következők:

- *az emptyDir* – ezt a kötetet az általánosan használt ideiglenes terület méretét a podot. A pod belül az összes tárolót férhet hozzá az adatokhoz, a köteten. A kötettípus írt adatok csak továbbra is fennáll a pod - élettartamát az a pod törlésekor a köteten törlődik. A kötet általában az alapjául szolgáló helyi csomópont lemezes tárolót használ, azonban csak a csomópont memóriában is előfordulhatnak.
- *titkos kulcs* – ezt a kötetet a bizalmas adatok behelyezése podok, például a jelszavak szolgál. Először hozzon létre egy titkos kulcsot, a Kubernetes API-val. A pod vagy a központi telepítés határozza meg, ha egy adott titkos kód lehet igényelni. A titkos kulcs tárolása pedig a titkos kulcsok csupán útmutatóul szolgálnak a csomópontokra, amelyeken az ütemezett podot működéséhez szükség van rá *tmpfs*nem írt lemezre. A legutóbbi pod egy csomóponton, amely megköveteli a titkos kulcs törlése esetén a titkos kulcsot a csomópont tmpfs lesz törölve. Titkos kulcsok egy adott névtéren belül vannak tárolva, és csak az azonos névtérben podok hozzáférhet.
- *configMap* – a kötet típusának segítségével kulcs-érték pár tulajdonságok behelyezése podok, például az alkalmazás konfigurációs adatait. Ahelyett, hogy egy tárolórendszerképet belül alkalmazáskonfigurációs adatok meghatározása, egy Kubernetes-erőforrást, amely könnyedén frissíthető és alkalmazott podok új példányát, melyekre telepítve vannak, megadhatja azt. Például egy titkos kulcsot használja, először hoz létre egy ConfigMap a Kubernetes API-val. A pod vagy a központi telepítési meghatározása során ez ConfigMap majd lehet igényelni. ConfigMaps egy adott névtéren belül tárolják, és csak az azonos névtérben podok hozzáférhet.

## <a name="persistent-volumes"></a>Tartós kötet

Köteteket, és határozza meg a pod életciklusának létrehozott csak léteznek a pod törléséig. Podok gyakran várható maradnia, ha egy pod átütemezése egy másik gazdagépre a karbantartási események, különösen a StatefulSets táraknak. A *tartós kötet* (PV) egy olyan tárolási erőforrás, hozza létre és felügyeli a Kubernetes API-t, amely egy egyéni pod élettartama nem létezhet.

Adja meg a PersistentVolume Azure-lemezek vagy fájlok használhatók. A kötetek az előző szakaszban feljegyzett a választás a fájlok és lemezek gyakran határozza meg az adatokat, vagy a teljesítményszint való egyidejű hozzáférés szükséges.

![Az Azure Kubernetes szolgáltatás (AKS)-fürt állandó kötetek](media/concepts-storage/persistent-volumes.png)

Egy PersistentVolume lehet *statikusan* hozta létre a fürt rendszergazdája vagy *dinamikusan* hozta létre a Kubernetes API-kiszolgálóhoz. Ha podot van ütemezve, és kéri a tároló, amely jelenleg nem áll rendelkezésre, a Kubernetes az alapul szolgáló Azure-lemezek vagy fájlokat tároló létrehozása, és mellékelje a pod. Dinamikus kiépítése használ egy *StorageClass* azonosításához az Azure storage milyen típusú kell létrehozni.

## <a name="storage-classes"></a>Storage osztályai

Adja meg a különböző tárolási rétegből álló, például a prémium és standard szintű, létrehozhat egy *StorageClass*. A StorageClass is meghatározza a *reclaimPolicy*. Ez reclaimPolicy az alapul szolgáló Azure storage-erőforrások viselkedését vezérlő, amikor a pod törlődik, és a tartós kötet már nem szükséges. Az alapul szolgáló tárolási erőforrás törölték, vagy a jövőbeli podot segítségével őrzi meg.

Az aks-ben két kezdeti StorageClasses jönnek létre:

- *alapértelmezett* – felügyelt lemez létrehozásához használja a standard szintű Azure storage. A visszaigénylési szabályzat azt jelzi, hogy az alapul szolgáló Azure-lemezek törlődik a pod használó törlésekor.
- *felügyelt prémium szintű* – felügyelt lemez létrehozásához használja az Azure Premium storage. A visszaigénylési szabályzat újra azt jelzi, hogy az alapul szolgáló Azure-lemezek törlődik a pod használó törlésekor.

Ha a tartós kötet nincs StorageClass van megadva, az alapértelmezett StorageClass használatos. Körültekintően állandó kötetek kérésekor, így a megfelelő tárolási kell használni. Létrehozhat egy, az további igényeknek megfelelően StorageClass `kubectl`. Az alábbi példa prémium szintű felügyelt lemezeket használ, és megadhatja, hogy az alapul szolgáló Azure-lemezek *megőrzött* a pod törlésekor:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Tartós kötet jogcímek

Egy PersistentVolumeClaim kérelmek egy adott StorageClass, hozzáférési mód és méretű lemez- vagy fájl tárolására. Ha nincs meglévő erőforrás a jogcím alapján a meghatározott StorageClass teljesítéséhez a Kubernetes API-t kiszolgáló dinamikusan helyezhet üzembe az Azure-ban az alapul szolgáló tárolási erőforrás. A pod-definíció a kötet csatlakoztatási tartalmaz, ha a kötet csatlakoztatva van a pod.

![Tartós kötet jogcímek, az Azure Kubernetes szolgáltatás (AKS)-fürt](media/concepts-storage/persistent-volume-claims.png)

Van egy PersistentVolume *kötött* egy PersistentVolumeClaim, miután egy rendelkezésre álló tár erőforrás hozzá lett rendelve a pod jóváhagyást kérni, hogy. Nincs állandó kötetek jogcímeket egy 1:1 megfeleltetését.

A következő példa YAML jegyzékfájl használó tartós kötet jogcím jeleníti meg a *felügyelt prémium szintű* StorageClass és a egy lemezt igényel *5Gi* mérete:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

A pod-definíciót hoz létre, ha a tartós kötet jogcím kérése a kívánt tárolási van megadva. Akkor is, majd adja meg a *volumeMount* az alkalmazásokhoz, az adatok olvasását és írását. A következő példa YAML jegyzékfájl bemutatja, hogyan az előző tartós kötet jogcím segítségével Kötet csatlakoztatása */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>További lépések

Ellenőrizze, hogyan hozhat létre Azure-lemezek vagy az Azure Files dinamikus és statikus köteteket, tekintse meg az alábbi útmutatók:

- [Az Azure-lemezekkel statikus kötet létrehozása][aks-static-disks]
- [Azure Files használatával statikus kötet létrehozása][aks-static-files]
- [Hozzon létre egy Azure-lemezek használata a dinamikus kötetet][aks-dynamic-disks]
- [Azure Files használatával dinamikus kötet létrehozása][aks-dynamic-files]

És további információkat az alapvető Kubernetes AKS fogalmait tekintse meg a következő cikkeket:

- [Kubernetes AKS-fürtök / és a számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identitásnak][aks-concepts-identity]
- [Kubernetes / AKS biztonsági][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes AKS méretezése /][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
