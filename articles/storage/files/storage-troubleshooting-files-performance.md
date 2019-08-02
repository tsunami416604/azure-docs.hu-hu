---
title: Azure Files teljesítmény hibaelhárítási útmutatója
description: Az Azure-fájlmegosztás és a kapcsolódó megkerülő megoldásokkal kapcsolatos ismert teljesítményproblémák.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6b28d004ceacda3cec13e96ceae84d5d1ff1a2e5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699175"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>A teljesítménnyel kapcsolatos problémák elhárítása Azure Files

Ez a cikk az Azure-fájlmegosztás szolgáltatással kapcsolatos gyakori problémákat sorolja fel. A probléma előfordulásakor lehetséges okokat és megkerülő megoldásokat biztosít.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Nagy késés, alacsony átviteli sebesség és általános teljesítménnyel kapcsolatos problémák

### <a name="cause-1-share-experiencing-throttling"></a>1\. ok: Megosztási sávszélesség szabályozása

A prémium megosztás alapértelmezett kvótája 100 GiB, amely 100 alapszintű IOPS biztosít (amely egy órán át akár 300-ra is feltörte). A IOPS-vel való kiépítésével és kapcsolatával kapcsolatos további [](storage-files-planning.md#provisioned-shares) információkért tekintse meg a tervezési útmutató kiépített megosztások című szakaszát.

Annak ellenőrzéséhez, hogy a megosztás szabályozása folyamatban van-e, kihasználhatja az Azure-mérőszámokat a portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **minden szolgáltatás** lehetőséget, és keressen **mérőszámokat**.

1. Válassza a **Metrika** lehetőséget.

1. Erőforrásként válassza ki a Storage-fiókját.

1. Válassza a **fájl** elemet metrikus névtérként.

1. Metrikaként válassza a **tranzakciók** lehetőséget.

1. Adjon hozzá egy szűrőt a **ResponseType** , és ellenőrizze, hogy vannak-e olyan kérelmek, amelyek **SuccessWithThrottling** (SMB) vagy **ClientThrottlingError** (REST esetén) rendelkeznek.

![A prémium szintű állnak metrikáinak beállításai](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Megoldás

- Növelje meg a megosztás kiosztott kapacitását egy magasabb kvóta megadásával a megosztáson.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>2\. ok: Metaadatok/névtér – nagy munkaterhelés

Ha a kérések többsége metaadat-központú (például CreateFile/OpenFile/closefile/QueryInfo/querydirectory), akkor a késés az írási/olvasási műveletekhez képest rosszabb lesz.

Annak ellenőrzéséhez, hogy a kérések többsége metaadat-központú, használhatja a fenti lépéseket. A **ResponseType**szűrő hozzáadása helyett adjon hozzá egy szűrőt az **API-névhez**.

![Az API-név szűrése a metrikákban](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Áthidaló megoldás

- Ellenőrizze, hogy az alkalmazás módosítható-e a metaadat-műveletek számának csökkentése érdekében.

### <a name="cause-3-single-threaded-application"></a>3\. ok: Egyszálas alkalmazás

Ha az ügyfél által használt alkalmazás egyetlen szálból áll, ez jelentős mértékben csökkentheti a IOPS/átviteli sebességet, mint a kiosztott megosztási méret alapján lehetséges maximális érték.

### <a name="solution"></a>Megoldás

- Növelje az alkalmazások párhuzamosságát a szálak számának növelésével.
- Váltson olyan alkalmazásokra, ahol a párhuzamosság lehetséges. A másolási műveletek esetében például az ügyfelek a AzCopy vagy a RoboCopy szolgáltatást használhatják a Windows-ügyfelektől vagy a Linux-ügyfeleken **futó Parallel** parancs használatával.

## <a name="very-high-latency-for-requests"></a>Nagyon nagy késés a kérelmek esetében

### <a name="cause"></a>Ok

Az ügyfél virtuális gépe a fájlmegosztás eltérő régiójában található.

### <a name="solution"></a>Megoldás

- Futtassa az alkalmazást egy olyan virtuális gépről, amely ugyanabban a régióban található, mint a fájlmegosztás.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Az ügyfél nem tudja elérni a hálózat által támogatott maximális átviteli sebességet

Ennek egyik lehetséges oka a többcsatornás SMB-támogatás hiánya. Az Azure-fájlmegosztás jelenleg csak egyetlen csatornát támogat, így az ügyfél virtuális gépe csak egyetlen kapcsolatban áll a-kiszolgálóval. Ez az egyetlen kapcsolódás az ügyfél virtuális gépe egyetlen magját, így a virtuális gépről elérhető maximális átviteli sebességet egyetlen mag köti.

### <a name="workaround"></a>Áthidaló megoldás

- A nagyobb mag-t tartalmazó virtuális gépek beszerzése segíthet az átviteli sebesség növelésében.
- Az ügyfélalkalmazás több virtuális gépről való futtatása növeli az átviteli sebességet.
- Ha lehetséges, használja a REST API-kat.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A Linux-ügyfelek átviteli sebessége jelentősen alacsonyabb a Windows-ügyfelekhez képest.

### <a name="cause"></a>Ok

Ez egy ismert probléma az SMB-ügyfél Linux rendszeren történő megvalósításával kapcsolatban.

### <a name="workaround"></a>Áthidaló megoldás

- A terhelés elosztása több virtuális gépen
- Ugyanazon a virtuális gépen használjon több csatlakoztatási pontot a **nosharesock** kapcsolóval, majd a terhelést a csatlakoztatási pontok között.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Magas késés a metaadatok nagy számítási feladataihoz, amelyek kiterjedt nyitott/zárási műveleteket foglalnak magukban.

### <a name="cause"></a>Ok

A címtár-bérletek támogatásának hiánya.

### <a name="workaround"></a>Áthidaló megoldás

- Ha lehetséges, kerülje a túlzott nyitó/záró leírót egy rövid időn belül ugyanazon a címtáron.
- Linux rendszerű virtuális gépek esetén növelje a címtár-bejegyzés gyorsítótárának időtúllépését úgy, hogy a **actimeo =\<sec >** , csatlakoztatási lehetőségként van megadva. Alapértelmezés szerint ez egy másodperc, így egy nagyobb érték, például három vagy öt, segítségre lehet.
- Linux rendszerű virtuális gépek esetén a kernelt 4,20-re vagy újabbra kell frissíteni.

## <a name="low-iops-on-centosrhel"></a>Alacsony IOPS a CentOS/RHEL

### <a name="cause"></a>Ok

A CentOS/RHEL esetében a nagyobb IO-mélység nem támogatott.

### <a name="workaround"></a>Áthidaló megoldás

- Frissítsen a CentOS 8/RHEL 8 verzióra.
- Váltson Ubuntu-ra.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú a másolás a Linux rendszerbe és Azure Filesba

Ha lassú másolást végez a Azure Filesba és a-ból, akkor tekintse meg a Linux-alapú hibaelhárítási útmutatóban a [lassú fájl másolása a következőre és a Azure Files Linux rendszeren](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) című szakaszát.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Idegesség/fűrész-Tooth mintázat a IOPS

### <a name="cause"></a>Ok

Az ügyfélalkalmazás konzisztensen meghaladja az alapkonfiguráció IOPS. Jelenleg a kérések terhelése nem zökkenőmentes, így ha az ügyfél túllépi az alapkonfiguráció IOPS, a szolgáltatás szabályozza a szolgáltatást. Ez a szabályozás azt eredményezheti, hogy az ügyfél vibrálás vagy fűrészes IOPS mintát észlelt. Ebben az esetben az ügyfél által elért átlagos IOPS alacsonyabb lehet az alapkonfiguráció IOPS.

### <a name="workaround"></a>Áthidaló megoldás

- Csökkentse a kérelmek terhelését az ügyfélalkalmazás alapján, hogy a megosztás ne legyen szabályozva.
- Növelje a megosztás kvótáját úgy, hogy a megosztás ne legyen szabályozva.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Túlzott DirectoryOpen/DirectoryClose-hívások

### <a name="cause"></a>Ok

Ha a DirectoryOpen/DirectoryClose hívások száma a leggyakoribb API-hívások egyike, és nem várja meg, hogy az ügyfél sok hívást hajtson fel, akkor előfordulhat, hogy az Azure-ügyfél virtuális gépre telepített víruskereső probléma merülhet fel.

### <a name="workaround"></a>Áthidaló megoldás

- A probléma megoldása a [Windows áprilisi platformjának frissítésében](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)érhető el.

## <a name="file-creation-is-slower-than-expected"></a>A fájl létrehozása a vártnál lassabb

### <a name="cause"></a>Ok

A nagy mennyiségű fájl létrehozására támaszkodó munkaterhelések nem fogják látni a prémium fájlmegosztás és a normál fájlmegosztás teljesítményének jelentős különbségét.

### <a name="workaround"></a>Áthidaló megoldás

- Nincs.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Lassú teljesítmény a Windows 8,1 vagy a Server 2012 R2 rendszerből

### <a name="cause"></a>Ok

Nagyobb, mint a várt késés a Azure Files IO-igényű számítási feladatokhoz való hozzáféréskor.

### <a name="workaround"></a>Áthidaló megoldás

- Telepítse az elérhető [gyorsjavítást](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).
