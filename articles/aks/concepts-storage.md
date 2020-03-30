---
title: Fogalmak – Tárolás az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg az Azure Kubernetes-szolgáltatás (AKS) tárolását, beleértve a köteteket, az állandó köteteket, a tárolási osztályokat és a jogcímeket
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595994"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) alkalmazásaitárolási lehetőségek

Az Azure Kubernetes-szolgáltatásban (AKS) futó alkalmazásoknak adatokat kell tárolniuk és letölteniük. Egyes alkalmazás-munkaterhelések, ez az adattároló használhatja a helyi, gyors tároló a csomóponton, amely már nincs szükség, ha a podok törlődnek. Más alkalmazás-munkaterhelések szükség lehet a tárolás, amely továbbra is megmarad az Azure platformon több rendszeres adatkötetek. Előfordulhat, hogy több podnak is meg kell osztania ugyanazokat az adatköteteket, vagy újra kell csatolnia az adatköteteket, ha a pod át van ütemezve egy másik csomóponton. Végül előfordulhat, hogy bizalmas adatokat vagy alkalmazáskonfigurációs adatokat kell beadnia a podokba.

![Az Azure Kubernetes-szolgáltatások (AKS) fürtjében lévő alkalmazások tárolási lehetőségei](media/concepts-storage/aks-storage-options.png)

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek tárolót biztosítanak az Alkalmazások Számára az AKS-ben:

- [Kötetek](#volumes)
- [Tartós kötetek](#persistent-volumes)
- [Tárolási osztályok](#storage-classes)
- [Tartóskötet-jogcímek](#persistent-volume-claims)

## <a name="volumes"></a>Kötetek

Az alkalmazásoknak gyakran képesnek kell lenniük az adatok tárolására és lekérésére. Mivel a Kubernetes általában az egyes podokat rövid élettartamú, eldobható erőforrásokként kezeli, különböző megközelítések érhetők el az alkalmazások számára az adatok szükség szerinti használatához és megőrzéséhez. A *kötet* az adatok podok és az alkalmazás életciklusa közötti tárolásának, lekérésének és megőrzésének módját jelenti.

Az adatok tárolására és beolvasására szolgáló hagyományos kötetek az Azure Storage által támogatott Kubernetes-erőforrásokként jönnek létre. Manuálisan létrehozhatja ezeket az adatköteteket, amelyekközvetlenül podokhoz vannak rendelve, vagy a Kubernetes automatikusan létrehozhatja őket. Ezek az adatkötetek azure-lemezeket vagy Azure-fájlokat használhatnak:

- *Az Azure Disks* kubernetes *datadisk* erőforrás létrehozásához használható. A lemezek használhatják az Azure Premium storage-t, amelyet nagy teljesítményű SSD-k vagy normál HDD-k által támogatott Azure Standard storage támogatnak. A legtöbb éles és fejlesztési számítási feladatok hoz, prémium szintű storage. Az Azure Disks *readWriteOnce-ként*van csatlakoztatva, így csak egyetlen podon érhető el. Több pod által egyidejűleg elérhető tárolókötetek esetén használja az Azure Files-t.
- *Az Azure Files* segítségével csatlakoztatható egy SMB 3.0-s megosztás, amelyet egy Azure Storage-fiók támogat a podokhoz. A fájlok lehetővé teszik az adatok megosztását több csomópont és pod között. A fájlok használhatják az Azure Standard storage-t, amelyet normál HDD-k vagy nagy teljesítményű SSD-k által támogatott Azure Premium-tárhely támogatnak.
> [!NOTE] 
> Az Azure Files támogatja a prémium szintű tárhelyet a Kubernetes 1.13-as vagy újabb verziót futtató AKS-fürtökben.

A Kubernetes kötetek több, mint egy hagyományos lemez, ahol az adatok tárolhatók és beolvasni. Kubernetes kötetek is használható, mint egy módja annak, hogy adatokat fecskendeznek egy pod a tárolók általi használatra. A Kubernetes gyakori további kötettípusai a következők:

- *emptyDir* – Ez a kötet általában egy pod ideiglenes helyeként használatos. A podon belüli összes tároló hozzáférhet a köteten lévő adatokhoz. A kötettípusra írt adatok csak a pod élettartamára maradnak meg – a pod törlésekor a kötet törlődik. Ez a kötet általában az alapul szolgáló helyi csomópontlemez-tárolót használja, bár csak a csomópont memóriájában is létezhet.
- *titkos* – Ez a kötet bizalmas adatok podokba, például jelszavakba való befecskendezésére szolgál. Először hozzon létre egy titkos kulcsot a Kubernetes API használatával. A pod vagy a központi telepítés definiálásakor egy adott titkos kulcsot lehet kérni. A titkos kulcsok csak olyan csomópontok számára vannak megadva, amelyekhez szükség van egy ütemezett podra, és a titkos kulcsot *tmpfs*tárolja, nem lemezre írva. Amikor az utolsó pod egy csomóponton, amely megköveteli a titkos kulcsot törlődik, a titkos kulcs törlődik a csomópont tmpfs törlődik. A titkos kulcsok egy adott névtérben tárolódnak, és csak az ugyanazon a névtéren belül lévő podok férhetnek hozzá.
- *configMap* – Ez a kötettípus kulcs-érték pár tulajdonságok podokba történő befecskendezésére szolgál, például az alkalmazás konfigurációs adatai. Ahelyett, hogy egy tárolórendszerképen belül definiálna alkalmazáskonfigurációs információkat, definiálhatja azt egy Kubernetes-erőforrásként, amely könnyen frissíthető és alkalmazható a podok új példányaira, ahogy üzembe helyezik őket. A titkos kulcs használatával először hozzon létre egy ConfigMap a Kubernetes API használatával. Ez a ConfigMap ezután kérhető, amikor definiál egy podot vagy üzembe helyezést. A ConfigMaps egy adott névtérben tárolódik, és csak az ugyanazon a névtéren belüli podok férhetnek hozzá.

## <a name="persistent-volumes"></a>Tartós kötetek

Kötetek, amelyek a pod életciklusa részeként definiált és létrehozott kötetek csak addig léteznek, amíg a pod törlődik. A podok gyakran elvárják, hogy a tároló megmaradjon, ha egy pod átvan ütemezve egy másik gazdagépen egy karbantartási esemény során, különösen a StatefulSets. Az *állandó kötet* (PV) a Kubernetes API által létrehozott és felügyelt tárolási erőforrás, amely létezhet egy adott pod élettartama után.

Az Azure Disks vagy files a persistentvolume biztosításához használható. Amint azt a Kötetek című korábbi szakaszban megjegyeztük, a lemezek vagy fájlok kiválasztását gyakran az adatokhoz vagy a teljesítményszinthez való egyidejű hozzáférés szükségessége határozza meg.

![Állandó kötetek egy Azure Kubernetes-szolgáltatások (AKS) fürtjében](media/concepts-storage/persistent-volumes.png)

A PersistentVolume *statikusan* létrehozható egy fürtrendszergazda által, vagy *dinamikusan* a Kubernetes API-kiszolgáló által. Ha egy pod van ütemezve, és a jelenleg nem elérhető tárhelyet kér, a Kubernetes létrehozhatja az alapul szolgáló Azure Disk vagy Files storage-t, és csatolhatja azt a podhoz. A dinamikus kiépítés egy *StorageClass-t* használ az Azure-tárhely létrehozásának azonosítására.

## <a name="storage-classes"></a>Tárolási osztályok

Különböző tárolási szintek, például prémium és standard szintek definiálásához létrehozhat egy *StorageClass osztályt.* A StorageClass a *reclaimPolicy programot is definiálja.* Ez a reclaimPolicy szabályozza az alapul szolgáló Azure storage-erőforrás viselkedését, amikor a pod törlődik, és az állandó kötet már nem szükséges. Az alapul szolgáló tárolási erőforrás törölhető, vagy megtartható egy jövőbeli pod.

Az AKS-ben két kezdeti StorageClasses jön létre:

- *alapértelmezett* – Az Azure Standard storage használatával hozzon létre egy felügyelt lemezt. A reclaim szabályzat azt jelzi, hogy az alapul szolgáló Azure Disk törlődik, ha az állandó kötetet használó törlődik.
- *managed-premium* - Azure Premium storage használatával hozzon létre felügyelt lemezt. A reclaim-szabályzat ismét azt jelzi, hogy az alapul szolgáló Azure Disk törlődik, ha az állandó kötetet használó törlődik.

Ha nincs megadva StorageClass egy állandó kötethez, a rendszer az alapértelmezett StorageClass-t használja. Az állandó kötetek kérésekor ügyelje arra, hogy azok a megfelelő tárhelyet használják, amire szüksége van. StorageClass-t a használatával `kubectl`további igényekhez hozhat létre. A következő példa prémium szintű felügyelt lemezeket használ, és meghatározza, hogy az alapul szolgáló Azure Disk *meg maradjon* a pod törlésekor:

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

## <a name="persistent-volume-claims"></a>Tartóskötet-jogcímek

PersistentVolumeClaim egy adott StorageClass lemez- vagy fájltárolását, hozzáférési módot és méretet kér. A Kubernetes API-kiszolgáló dinamikusan kiépítheti az alapul szolgáló tárolási erőforrást az Azure-ban, ha nincs meglévő erőforrás a megadott StorageClass alapján a jogcím teljesítéséhez. A pod definíció tartalmazza a kötet csatlakoztatása után a kötet.

![Állandó mennyiségi jogcímek egy Azure Kubernetes-szolgáltatás (AKS) fürtben](media/concepts-storage/persistent-volume-claims.png)

A persistentvolume *egy persistentvolumeclaim-hez van kötve,* miután egy elérhető tárolási erőforrás hozzá van rendelve a pod ot kérő podhoz. Az állandó kötetek 1:1 arányban megfeleltetik a jogcímeket.

A következő példa YAML-jegyzékfájl egy állandó kötetjogcímet mutat be, amely a *felügyelt prémium szintű StorageClass-t* használja, és *5Gi* lemezméretet kér:

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

Pod-definíció létrehozásakor az állandó kötet jogcím van megadva a kívánt tároló kérésére. Ezután adja meg a *volumeMount* az alkalmazások adatok olvasása és írása. A következő példa YAML-jegyzékfájl bemutatja, hogyan használható az előző állandó kötetjogcím a */mnt/azure*kötet csatlakoztatására:

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

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

Az Azure Disks vagy az Azure Files szolgáltatást használó dinamikus és statikus kötetek létrehozásáról az alábbi útmutatócikkekben található:

- [Statikus kötet létrehozása az Azure Disks használatával][aks-static-disks]
- [Statikus kötet létrehozása az Azure Files használatával][aks-static-files]
- [Dinamikus kötet létrehozása az Azure Disks használatával][aks-dynamic-disks]
- [Dinamikus kötet létrehozása az Azure Files használatával][aks-dynamic-files]

A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS-fürtök és munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identitás][aks-concepts-identity]
- [Kubernetes / AKS biztonság][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes / AKS skála][aks-concepts-scale]

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
[operator-best-practices-storage]: operator-best-practices-storage.md
