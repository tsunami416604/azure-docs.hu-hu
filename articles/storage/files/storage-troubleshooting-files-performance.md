---
title: Hibaelhárítási útmutató az Azure Files teljesítmény
description: A prémium szintű Azure-fájlmegosztásokat (előzetes verzió) és a kapcsolódó megoldások teljesítményproblémák ismert.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190053"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Az Azure Files teljesítménnyel kapcsolatos problémáinak elhárítása

Ez a cikk felsorolja a prémium szintű Azure-fájlmegosztások (előzetes verzió) kapcsolatos néhány gyakori problémát. Amikor ezeket a problémákat észlelt vannak lehetséges okait és megkerülő megoldások közöl.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Nagy a késés, az alacsony átviteli sebesség és az általános, teljesítménnyel kapcsolatos problémák

### <a name="cause-1-share-experiencing-throttling"></a>1\. ok: Megosztás kiszolgálnia szabályozása

A megosztáson található alapértelmezett kvóta 100 GB, amely biztosít 100 alapkonfiguráció iops-érték (legfeljebb 300 eszközcsomag egy óránál potenciális). Kiépítés és IOPS fennálló kapcsolatot további információkért lásd: a [megosztások kiosztott](storage-files-planning.md#provisioned-shares) szakasz a tervezési útmutató.

Győződjön meg arról, ha a megosztás szabályozás alatt áll, az használhatja az Azure-mérőszámok a portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** és keressen **metrikák**.

1. Válassza a **Metrika** lehetőséget.

1. Válassza ki a tárfiókját, és az erőforrásnak.

1. Válassza ki **fájl** , a metrika névtérben.

1. Válassza ki **tranzakciók** kívánt mérőszámként.

1. Szűrheti a **ResponseType** , és ellenőrizze, hogy egyetlen kérést van-e válaszkód **SuccessWithThrottling**.

![Prémium szintű fileshares metrikák lehetőségei](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Megoldás

- Növekedés kiosztott kapacitást ossza meg a megosztáson magasabb kvótát megadásával.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>2\. ok: Metaadatok és névterének nagy munkaterhelést

Ha a kérések többségét metaadat-központú, (például createfile/openfile/closefile/queryinfo/querydirectory) lesz, hogy a késés rosszabb olvasási/írási műveleteket képest.

Ellenőrizze, hogy ha a kérések többségét metaadat-központú, használhatja a fenti ugyanazokat a lépéseket. Szűrő hozzáadása helyett kivételével **ResponseType**, szűrheti a **API neve**.

![A metrikák az API neve szűrő](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Áthidaló megoldás

- Ellenőrizze, hogy ha az alkalmazás a metaadat-műveletek számának csökkentése módosíthatók.

### <a name="cause-3-single-threaded-application"></a>3\. ok: Alkalmazás egyszálas

Ha az ügyfél által használt alkalmazás egyszálas, ez jelentősen alacsonyabb IOPS/teljesítményt nyújt, mint a maximális lehetséges a létesített megosztás méretének alapján eredményezhet.

### <a name="solution"></a>Megoldás

- Alkalmazás párhuzamosság növeli a szálak számának megnövelése.
- Váltás alkalmazások, a párhuzamosság lehetőség. Például a másolási műveletek ügyfelek használhat AzCopy vagy a RoboCopy Windows-ügyfelek vagy a **párhuzamos** parancsot a Linux-ügyfelek.

## <a name="very-high-latency-for-requests"></a>Kérések késése nagyon magas

### <a name="cause"></a>Ok

Az ügyfél virtuális gép található, mint a prémium szintű fájlmegosztáshoz egy másik régióban.

### <a name="solution"></a>Megoldás

- Futtassa az alkalmazást, amely a prémium szintű fájlmegosztás megegyező régióban található virtuális gép.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Az ügyfél nem tudja elérni a hálózat által támogatott maximális átviteli sebesség

Ennek egyik lehetséges oka hiányát fo SMB többcsatornás támogatása. Jelenleg Azure-fájlmegosztások csak támogatja egyetlen csatorna, így az ügyfél virtuális gép csak egy kapcsolat a kiszolgálóval. Ez egyetlen kapcsolat van rögzítve, és az ügyfél virtuális Gépet, egyetlen mag, a virtuális gép elérhető maximális átviteli sebesség szerint egyetlen mag van kötve.

### <a name="workaround"></a>Áthidaló megoldás

- Beszerzése egy virtuális Gépet egy nagyobb méretű core használatával növelheti teljesítményt.
- Az ügyfélalkalmazás futtatása több virtuális gép növeli a teljesítményt.
- Ahol lehetséges, használjon REST API-k.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux-ügyfelek az átviteli sebesség a Windows-ügyfelek képest jelentősen alacsonyabb.

### <a name="cause"></a>Ok

Ez az egy ismert probléma az SMB-ügyfél Linux megvalósítását.

### <a name="workaround"></a>Áthidaló megoldás

- A terhelés elosztását több virtuális gépen
- Az azonos virtuális gépen, használjon több csatlakoztatási pontot a **nosharesock** lehetőséget, és ezek között a terhelés megállítását csatlakoztatási pontoktól.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>A nagy késleltetésű metaadatok használata esetén a széles körű megnyitása vagy bezárása az operations (nagy erőforrásigényű) munkaterhelések esetében.

### <a name="cause"></a>Ok

Könyvtár-címbérletek támogatása hiánya.

### <a name="workaround"></a>Áthidaló megoldás

- Ha lehetséges kerülje a túl sok Znak levé nebo pravé leíró ugyanabban a címtárban lévő rövid időn belül.
- Linux rendszerű virtuális gépekhez, növelje a directory bejegyzés gyorsítótárazás időkorlátja megadásával **actimeo =<sec>**  csatlakoztatási beállításként. Alapértelmezés szerint egy második, így előfordulhat, hogy például három vagy öt nagyobb értéket.
- Linux rendszerű virtuális gépekhez frissítse a kernel 4.20 vagy újabb verziója.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL az alacsony IOPS

### <a name="cause"></a>Ok

A CentOS vagy RHEL i/o-mélysége nagyobb, mint egy nem támogatott.

### <a name="workaround"></a>Áthidaló megoldás

- Frissítés a CentOS 8 / RHEL 8.
- Ubuntu módosítsa.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>A minta érzékeny vagy saw-tooth iops

### <a name="cause"></a>Ok

Ügyfélalkalmazás rendszeresen túllépi a referenciakonfiguráció iops-t. Jelenleg nincsenek Szolgáltatásoldali simítás, a kérelmek által generált terhelést, így ha az ügyfél túllépi a referenciakonfiguráció IOPS, azt leszabályozza a rendszer a szolgáltatás által. A szabályozás azt eredményezheti, hogy az ügyfél érzékeny vagy saw-tooth IOPS mintát tapasztal. Ebben az esetben az ügyfél által elért átlagos IOPS alacsonyabb, mint az alapkonfiguráció iops-érték lehet.

### <a name="workaround"></a>Áthidaló megoldás

- Csökkentse az ügyfélalkalmazás, a kérelmek által generált terhelést, úgy, hogy a megosztás nem leszabályozza.
- Növelje meg a megosztás úgy, hogy a megosztás nem leszabályozza.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Túl sok DirectoryOpen/DirectoryClose hívás

### <a name="cause"></a>Ok

Ha DirectoryOpen/DirectoryClose hívások száma a felső API-hívások között van, és várhatóan nem az ügyfél számára, hogy sok hívás, a virtuális gép Azure-ügyfél telepítve víruskereső probléma lehet is teszi.

### <a name="workaround"></a>Áthidaló megoldás

- A probléma megoldását érhető el a [április Platform frissítése a Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Fájl létrehozása a vártnál lassabban.

### <a name="cause"></a>Ok

Számítási feladatok által használt nagy mennyiségű fájlt létrehozása, nem jelennek meg lényeges különbség a prémium szintű fájlmegosztások teljesítményének és a standard szintű fájlmegosztások.

### <a name="workaround"></a>Áthidaló megoldás

- Nincs.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>A Windows 8.1 vagy Server 2012 R2 lassú teljesítmény

### <a name="cause"></a>Ok

Nagyobb, mint a várható késés i/o-igényű számítási feladatokhoz az Azure-fájlokhoz fér hozzá.

### <a name="workaround"></a>Áthidaló megoldás

- Telepítse a rendelkezésre álló [gyorsjavítás](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).