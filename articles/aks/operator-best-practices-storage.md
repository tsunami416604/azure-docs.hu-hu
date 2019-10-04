---
title: Ajánlott eljárások operátor - tároló az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárások tárolás, az adattitkosítás és a biztonsági másolatokat az Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: mlearned
ms.openlocfilehash: b42cdae634a6c2d8d994225d4cb6b440a99918e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614582"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Gyakorlati tanácsok a tárolási és biztonsági másolatokat az Azure Kubernetes Service (AKS)

Hozzon létre és kezelheti a fürtöket az Azure Kubernetes Service (AKS), az alkalmazások gyakran kell storage. Fontos megérteni a teljesítményigények és módszerek a podok eléréséhez, így megadhatja, hogy az alkalmazások megfelelő tárolási. Az AKS csomópontméret befolyásolhatják ezeket tárolási lehetőségeket. Tervezze meg a biztonsági mentése és tesztelése a visszaállítási folyamat csatolt Storage módszereket is.

Ajánlott eljárások a cikkben a tárterülettel kapcsolatos szempontok a fürt operátorok összpontosít. Ez a cikk bemutatja:

> [!div class="checklist"]
> * Milyen tárolási típust kínál érhetők el
> * Hogyan kell megfelelően méret az AKS-csomópontok tároló-teljesítményre
> * Dinamikus és statikus kiépítés kötetek közötti különbségek
> * Biztonsági mentése és védelme az adatkötetek módjai

## <a name="choose-the-appropriate-storage-type"></a>A megfelelő tárolótípus kiválasztása

**Ajánlott eljárásokkal kapcsolatos útmutatás** – megismerheti az alkalmazás számára a megfelelő tárolási igényeit. Nagy teljesítményű, SSD-alapú tárolást éles számítási feladatokra használja. Tervezze meg a neurálishálózat-alapú tárolási, ha több egyidejű kapcsolat szüksége van.

Alkalmazások gyakran a különböző típusú és sebességű tárhelyet igényelnek. Szükség van az alkalmazások egyes podok csatlakozik, vagy több podok között megosztott tárolás? Az a tároló csak olvasható hozzáférést adatokhoz, vagy nagy mennyiségű strukturált adatok számára? Ezek a storage kell meghatározni a legmegfelelőbb storage használatára.

Az alábbi táblázat ismerteti a rendelkezésre álló tárhely és azok képességeinek:

| Használati eset | Kötet beépülő modul | Olvasási/írási egyszer | Csak olvasható több | Olvasási/írási számos | A Windows Server-tároló támogatása |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| A megosztott konfiguráció       | Azure Files   | Igen | Igen | Igen | Igen |
| Strukturált adatokat        | Azure Disks   | Igen | Nem  | Nem  | Igen |
| Teljes strukturálatlan adatmennyiséget tárolni rendszerműveletekről fájl | [BlobFuse (előzetes verzió)][blobfuse] | Igen | Igen | Igen | Nem |

A két elsődleges típusú kötetek az aks-ben biztosított Azure-lemezek vagy az Azure Files biztonsági. A biztonság növelése érdekében mindkét tárolási típust kínál, amely titkosítja az inaktív adatok alapértelmezés szerint az Azure Storage Service Encryption (SSE) használja. Lemezek jelenleg nem lehet a az AKS csomópont szintjén az Azure Disk Encryption használatával titkosított.

Az Azure Files jelenleg elérhetők a Standard teljesítményszint. Azure-lemezek a Standard és prémium teljesítményszintek érhetők el:

- *Prémium szintű* lemezek élvezik nagy teljesítményű SSD-lemez (SSD-kkel). Az összes éles számítási feladatokhoz a prémium szintű lemezek használata akkor javasolt.
- *Standard szintű* lemezek élvezik rendszeres tartalomfogyasztás lemezek (HDD) és archiválási vagy ritkán használt adatok megfelelőek.

Ismerje meg az alkalmazás teljesítményigényeken, és minták a megfelelő tárolási szintek kiválasztásának eléréséhez. További információ a Managed Disks méretekről és a teljesítmény szintjeiről: az [Azure Managed Disks áttekintése][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Létrehozhat és használhat a storage osztályai meghatározásához az alkalmazás igényeinek megfelelően

A tárolásért kell fizetnie típusát van definiálva a Kubernetes használatával *storage osztályai*. A tárolási osztály majd hivatkozik a pod vagy a központi telepítési specifikációnak. Ezeket a definíciókat hozhat létre a megfelelő tárolót, és csatlakoztathatja azt podok egymással együttműködve. További információ: [tárolási osztályok az AK-ban][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Tárolási igényeinek megfelelően a csomópontok mérete

**Ajánlott eljárásokkal kapcsolatos útmutatás** – minden egyes csomópont mérete támogatja a lemezek maximális száma. Másik csomópontot is biztosít különböző mennyiségű helyi tárolási és hálózati sávszélességet. Tervezze meg a csomópontok megfelelő méretét telepítendő alkalmazások számára.

AKS-csomópontok futtató Azure virtuális gépeken. Különböző típusú és méretű virtuális gép érhetők el. Minden egyes Virtuálisgép-méretet biztosít egy másik alapvető erőforrásai, például a CPU és memória mennyisége. Ezek a Virtuálisgép-méretek rendelkezik csatolható lemezek maximális száma. Tároló-teljesítményre maximális helyi és csatolt lemezenkénti iops-t (bemeneti/kimeneti műveletek száma másodpercenként) a Virtuálisgép-méretek között is változik.

Ha az alkalmazások Azure-lemezek t, tervezze meg, és válassza ki a csomópontok megfelelő Virtuálisgép-méretet. A Processzor és memória mennyisége nem az egyetlen tényező, ha úgy dönt, hogy a virtuális gép méretét. A tárolási lehetőségeket is fontosak. Például mind a *Standard_B2ms* és *Standard_DS2_v2* Virtuálisgép-méretek egy hasonló mennyiségű Processzor és memória-erőforrásokat tartalmaznak. A lehetséges tároló-teljesítményre eltérő, az alábbi táblázatban látható módon:

| Csomópont típusa és méret | vCPU | Memória (GiB) | Adatlemezek max. száma | Nem gyorsítótárazott lemez maximális iops-érték | Maximális nem gyorsítótárazott átviteli sebesség (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Itt a *Standard_DS2_v2* double a csatlakoztatott lemezek számát, és a négyszer három az összeget IOPS és a lemez adatátviteli kapacitást biztosít. Ha csak tekintett meg az alapvető számítási erőforrásokat, és költségek képest, dönthet a *Standard_B2ms* virtuális gép mérete és gyenge tároló-teljesítményre és korlátozások. Az alkalmazás fejlesztői csapat tudni, hogy a tárolási kapacitás és teljesítmény igényeinek megfelelően dolgozhat. Válassza ki a megfelelő Virtuálisgép-méret az AKS-csomópontok elérik vagy túllépik a teljesítmény igényeiknek. Rendszeresen alapkonfiguráció alkalmazások szükség szerint módosítsa a Virtuálisgép-méretet.

További információ az elérhető virtuálisgép-méretekről: [a Linux rendszerű virtuális gépek méretei az Azure-ban][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dinamikusan kötetek kiépítéséhez

**Ajánlott eljárásokkal kapcsolatos útmutatás** - munkaterhelést, és lehetővé teszik, méretezhető, nem statikusan létrehozása és hozzárendelése állandó kötetek csökkentése érdekében. Használja a dinamikus kiépítést. A storage osztályai határoz meg a megfelelő visszaigénylési házirend felesleges tárolási költségek minimalizálása a podok törlése után.

Tároló csatlakoztatása a podok van szüksége, állandó köteteket használja. Ezek a kötetek állandó manuálisan vagy dinamikusan hozható létre. Állandó kötetek manuális létrehozásához hozzáadja a munkaterhelést, és korlátozza a méretezési képességét. Használja az adattárolás-felügyelet egyszerűsítését és teszi lehetővé az alkalmazásokban növekszik, és szükség szerint méretezheti való üzembe helyezést, a dinamikus tartós kötet.

![Tartós kötet jogcímek, az Azure Kubernetes szolgáltatás (AKS)-fürt](media/concepts-storage/persistent-volume-claims.png)

Tartós kötet jogcím (PVC) dinamikusan hozhat létre tárolási igény szerint teszi lehetővé. Az alapul szolgáló Azure-lemezek jönnek létre, a podok tanúsítványkérelmeket. A pod-definícióban és a tervezett csatlakoztatási útvonalra csatolhatók kötet kérése

A kötetek dinamikus létrehozásával és használatával kapcsolatos fogalmakat lásd: az [állandó kötetek][aks-concepts-storage-pvcs]jogcímei.

A kötetek működés közbeni megtekintéséhez tekintse meg az állandó kötetek [Azure][dynamic-disks] -lemezekkel vagy [Azure Files][dynamic-files]való dinamikus létrehozását és használatát ismertető témakört.

A storage osztálydefiníciókat részeként állítsa be a megfelelő *reclaimPolicy*. Ez reclaimPolicy az alapul szolgáló Azure storage-erőforrások viselkedését vezérlő, amikor a pod törlődik, és a tartós kötet már nem szükséges. Az alapul szolgáló tárolási erőforrás törölték, vagy a jövőbeli podot segítségével őrzi meg. A reclaimPolicy állíthatja *megőrzése* vagy *törlése*. Ismerje meg az alkalmazások igényeihez, és rendszeres ellenőrzéseket a nem használt tárolási megoldás, amely használja, és a számlázás a lehető legkevesebb megőrzött tárolás megvalósítása.

További információ a tárolási osztályok beállításairól: [tárolási visszaigénylési házirendek][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Biztonságos és az adatok biztonsági mentése

**Ajánlott eljárások – útmutató** – az adatairól biztonsági másolatot készíthet a tárolási típusának megfelelő eszköz használatával, például Velero vagy Azure site Recovery. Győződjön meg arról, és biztonsági, az ezeket a biztonsági mentéseket.

Amikor az alkalmazások tárolására és felhasználását adatait megőrzi a lemezen, vagy a fájlokat, kell tennie a rendszeres biztonsági mentést, vagy az adatok pillanatképeit. Az Azure Disks beépített pillanatkép technológiákat használhatnak fel. Szükség lehet az alkalmazások kiüríteni a lemezre, a pillanatkép-készítési művelet végrehajtása előtt ír hurok. A [Velero][velero] képes biztonsági másolatot készíteni az állandó kötetekről, valamint további fürterőforrás-és konfigurációkkal. Ha nem tudja [eltávolítani][remove-state]az állapotot az alkalmazásokból, biztonsági másolatot készíthet az állandó kötetek adatairól, és rendszeresen teszteli a visszaállítási műveleteket az adatok integritásának és a szükséges folyamatoknak az ellenőrzéséhez.

Ismerje meg, az adatok biztonsági mentése, és ha kell fokozatosan leválasztani az adatokat a pillanatkép más megközelítést vonatkozó korlátozások. Adatok biztonsági mentése nem feltétlenül lehetővé teszik a fürt üzembe helyezése az alkalmazás-környezet helyreállításához. További információ ezekről a forgatókönyvekről: [ajánlott eljárások az üzleti folytonosság és a vész-helyreállítás az AK-ban][best-practices-multi-region].

## <a name="next-steps"></a>További lépések

Storage ajánlott eljárások az aks-ben összpontosított ebben a cikkben. További információ a Kubernetes tárolásával kapcsolatos alapismeretekről: [az AK-beli alkalmazások tárolási fogalmai][aks-concepts-storage].

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
