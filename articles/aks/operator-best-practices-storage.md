---
title: Gyakorlati tanácsok a tároláshoz és a biztonsági mentéshez
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürtfelelősök nek a tárolással, adattitkosítással és biztonsági mentésekkel kapcsolatos gyakorlati tanácsait az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 843b775f7761af7cd40140c9bf34768d63eb5a50
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877898"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) tárolásával és biztonsági másolatával kapcsolatos gyakorlati tanácsok

A fürtök létrehozása és kezelése az Azure Kubernetes Szolgáltatás (AKS), az alkalmazások gyakran kell tárolni. Fontos, hogy tisztában legyen a podok teljesítményigényeivel és hozzáférési módszereivel, hogy megfelelő tárhelyet biztosíthasson az alkalmazások számára. Az AKS-csomópont mérete hatással lehet ezekre a tárolási lehetőségekre. Azt is meg kell terveznie, hogyan lehet biztonsági másolatot tartani, és tesztelje a csatlakoztatott tárolás visszaállítási folyamatát.

Ez az ajánlott eljárások cikk a fürtoperátorok tárolási szempontjaira összpontosít. Ebben a cikkben a következőket ismerheti meg:

> [!div class="checklist"]
> * Milyen típusú tárhely áll rendelkezésre?
> * Az AKS-csomópontok megfelelő méretezése a tárolási teljesítmény érdekében
> * A kötetek dinamikus és statikus kiépítési tartományai közötti különbségek
> * Ways to back up and secure your data volumes

## <a name="choose-the-appropriate-storage-type"></a>Válassza ki a megfelelő tárolási típust

**Ajánlott eljárásokra vonatkozó útmutatás** – Ismerje meg az alkalmazás igényeit a megfelelő tárhely kiválasztásához. Nagy teljesítményű, SSD-alapú tárolót használhat az éles számítási feladatokhoz. Tervezze meg a hálózati alapú tárolást, ha több egyidejű kapcsolatra van szükség.

Az alkalmazások gyakran különböző típusú és sebességű tárolást igényelnek. Az alkalmazások nak szüksége van olyan tárhelyre, amely egyes podokhoz csatlakozik, vagy több pod között van megosztva? A tároló csak olvasható hozzáférést biztosít az adatokhoz, vagy nagy mennyiségű strukturált adatok at ír? Ezek a tárolási igények határozzák meg a legmegfelelőbb tárolási típust.

Az alábbi táblázat ismerteti a rendelkezésre álló tárolási típusokat és azok képességeit:

| Használati eset | Hangerő beépülő modul | Olvasás/írás egyszer | Csak olvasható sok | Sok olvasása/írása | A Windows Server tárolók támogatása |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Megosztott konfiguráció       | Azure Files   | Igen | Igen | Igen | Igen |
| Strukturált alkalmazásadatok        | Azure Disks   | Igen | Nem  | Nem  | Igen |
| Strukturálatlan adatok, fájlrendszer-műveletek | [BlobFuse][blobfuse] | Igen | Igen | Igen | Nem |

Az AKS-ben a kötetek számára biztosított két elsődleges tárolótípust az Azure Disks vagy az Azure Files támogatja. A biztonság növelése érdekében mindkét típusú tárhely az Azure Storage Service Encryption (SSE) alapértelmezés szerint, amely titkosítja az adatokat inaktív. A lemezek jelenleg nem titkosíthatók az AKS-csomópont szintjén az Azure Disk Encryption használatával.

Az Azure Files és az Azure Disks standard és prémium szintű teljesítményszinteken is elérhetők:

- *A prémium szintű* lemezeket nagy teljesítményű SSD-lemezek (SSD) támogatja. Prémium szintű lemezek ajánlott minden éles számítási feladatokhoz.
- *A szabványos* lemezeket rendszeres forgó lemezek (HDD-k) szolgálják, és jó archiválási vagy ritkán használt adatokhoz.

Ismerje meg az alkalmazás teljesítményigényeit és a hozzáférési mintákat a megfelelő tárolási szint kiválasztásához. A felügyelt lemezek méretéről és teljesítményrétegeiről az [Azure felügyelt lemezek áttekintése című témakörben olvashat bővebben.][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Tárolóosztályok létrehozása és használata az alkalmazásigények meghatározásához

A használt tároló típusa a Kubernetes *tárolóosztályai*segítségével van definiálva. A tárolási osztály ezután hivatkozik a pod vagy a központi telepítési specifikáció. Ezek a definíciók együttműködnek a megfelelő tároló létrehozásához és a podokhoz való csatlakoztatásához. További információ: [Storage classes in AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>A tárolási igényekhez szükséges csomópontok méretezése

**Ajánlott eljárások –** Minden csomópont mérete támogatja a lemezek maximális számát. A különböző csomópontméretek különböző mennyiségű helyi tárhelyet és hálózati sávszélességet is biztosítanak. Tervezze meg az alkalmazás igények üzembe helyezéséhez a megfelelő méretű csomópontok.

Az AKS-csomópontok Azure-beli virtuális gépekként futnak. A virtuális gép különböző típusai és méretei érhetők el. Minden virtuális gép mérete különböző mennyiségű alapvető erőforrásokat biztosít, például a PROCESSZOR és a memória. Ezek a virtuális gép méretek rendelkeznek a lemez, amely csatlakoztatható a lemezek maximális száma. Tárolási teljesítmény is változik a virtuális gép mérete a maximális helyi és csatlakoztatott lemez IOPS (bemeneti/kimeneti műveletek másodpercenként).

Ha az alkalmazások tárolási megoldásként Azure Disks-et igényelnek, tervezze meg és válassza ki a megfelelő csomópontvirtuális gép méretét. A processzor és a memória mennyisége nem az egyetlen tényező, ha a virtuális gép méretét választja. A tárolási képességek is fontosak. Például mind a *Standard_B2ms,* mind *a Standard_DS2_v2* virtuális gép mérete hasonló mennyiségű PROCESSZOR- és memória-erőforrást tartalmaz. Potenciális tárolási teljesítményük eltérő, ahogy az a következő táblázatban látható:

| Csomópont típusa és mérete | vCPU | Memória (GiB) | Adatlemezek max. száma | Maximális nem gyorsítótárazott lemez IOPS | Gyorsítótárazatlan átviteli sebesség maximális |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Itt a *Standard_DS2_v2* lehetővé teszi a csatlakoztatott lemezek számának kétszeresét, és az IOPS és a lemezátviteli szint három-négyszeresét biztosítja. Ha csak az alapvető számítási erőforrásokat és az összehasonlított költségeket nézte meg, kiválaszthatja a *Standard_B2ms* virtuális gép méretét, és gyenge tárolási teljesítménnyel és korlátozásokkal rendelkezik. Az alkalmazásfejlesztő imáta segítségével megismerheti a tárolókapacitást és a teljesítményigényeket. Válassza ki a megfelelő virtuális gép méretét az AKS-csomópontok, hogy megfeleljen, vagy meghaladja a teljesítményigényeket. Rendszeresen alapkonfigurációs alkalmazások a virtuális gép méretének szükség szerint való beállításához.

Az elérhető virtuálisgép-méretekről az [Azure-beli Linuxos virtuális gépek méretei című témakörben][vm-sizes]talál további információt.

## <a name="dynamically-provision-volumes"></a>Dinamikusan üzembe helyezhető kötetek

**Ajánlott eljárások –** A felügyeleti terhelés csökkentése és a méretezés lehetővé teszi, ne hozzon létre statikusan állandó köteteket. Dinamikus kiépítés használata. A tárolási osztályok, határozza meg a megfelelő reclaim házirendet a szükségtelen tárolási költségek minimalizálása podok törlése után.

Ha tárolót kell csatolnia a podokhoz, állandó köteteket kell használnia. Ezek az állandó kötetek manuálisan vagy dinamikusan hozhatók létre. Az állandó kötetek manuális létrehozása felügyeleti többletterhelést jelent, és korlátozza a méretezési képességet. A dinamikus állandó kötetek kiépítésével egyszerűsítheti a tárhelykezelést, és lehetővé teheti az alkalmazások szükség szerint történő növekedését és méretezését.

![Állandó mennyiségi jogcímek egy Azure Kubernetes-szolgáltatás (AKS) fürtben](media/concepts-storage/persistent-volume-claims.png)

Az állandó kötetjogcím (PVC) lehetővé teszi, hogy szükség szerint dinamikusan hozzon létre tárhelyet. Az alapul szolgáló Azure-lemezek jönnek létre podok kérésére őket. A pod-definícióban egy kötet et kell létrehozni, és egy kijelölt csatlakoztatási útvonalhoz kell csatolni.

A kötetek dinamikus létrehozásának és használatának fogalmait az [Állandó kötetjogcímek][aks-concepts-storage-pvcs]című témakörben tésszet.

Ezek nek a kötetek működés közben, olvassa el, hogyan hozhat létre és használhat dinamikusan egy állandó kötet et [az Azure Disks][dynamic-disks] vagy az [Azure Files.][dynamic-files]

A tárolási osztálydefiníciók részeként állítsa be a megfelelő *reclaimPolicy*. Ez a reclaimPolicy szabályozza az alapul szolgáló Azure storage-erőforrás viselkedését, amikor a pod törlődik, és az állandó kötet már nem szükséges. Az alapul szolgáló tárolási erőforrás törölhető, vagy megtartható egy jövőbeli pod. A reclaimPolicy *beállíthatja,* hogy megtartja vagy *törölje*. Ismerje meg az alkalmazás igényeit, és valósítsa meg a rendszeres ellenőrzéseket a tárolt tároló, hogy minimálisra csökkentsék a felhasznált és számlázott nem használt tárterület mennyiségét.

A tárolási osztály beállításairól a [Tárolási igénylési házirendek című][reclaim-policy]témakörben talál további információt.

## <a name="secure-and-back-up-your-data"></a>Az adatok biztonsága és biztonsági leépítése

**Ajánlott eljárásokra vonatkozó útmutatás** – Biztonsági másolatot kell adniaz adatokról a tárhelytípusnak megfelelő eszközzel, például a Velero vagy az Azure Site Recovery használatával. Ellenőrizze a biztonsági mentések integritását és biztonságát.

Ha az alkalmazások lemezeken vagy fájlokban tárolt adatokat tárolnak és használnak fel, rendszeres biztonsági mentéseket vagy pillanatképeket kell készítenie az adatokról. Az Azure Disks beépített pillanatkép-technológiákat használhat. Előfordulhat, hogy a pillanatkép-művelet végrehajtása előtt meg kell keresnie az alkalmazásokat az írások lemezre való kiürítéséhez. [A Velero][velero] további fürterőforrásokkal és konfigurációkkal együtt biztonsági másolatot tud fésülni az állandó kötetekről. Ha nem tudja [eltávolítani az állapotot az alkalmazásokból,][remove-state]biztonsági másolatot kell létrehoznia az adatokról az állandó kötetekről, és rendszeresen tesztelheti a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak a ellenőrzéséhez.

Ismerje meg az adatbiztonsági mentések különböző megközelítéseinek korlátait, és ha a pillanatkép előtt meg kell határoznia az adatokat. Az adatbiztonsági mentések nem feltétlenül teszik lehetővé a fürt központi telepítésének alkalmazáskörnyezetének visszaállítását. Ezekről a forgatókönyvekről az [AKS-ben az üzletmenet folytonosságával és a vészhelyreállítással][best-practices-multi-region]kapcsolatos gyakorlati tanácsok című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ez a cikk az AKS tárolási gyakorlati tanácsaira összpontosított. A Kubernetes tárolási alapjairól az [AKS-ben lévő alkalmazások tárolási fogalmai][aks-concepts-storage]című témakörben talál további információt.

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
