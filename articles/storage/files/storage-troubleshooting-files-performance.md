---
title: Az Azure Files teljesítményhibaelhárítási útmutatója
description: Ismert teljesítményproblémák az Azure-fájlmegosztásokkal és a kapcsolódó kerülő megoldásokkal.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598085"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Az Azure Files teljesítményével kapcsolatos problémák elhárítása

Ez a cikk az Azure-fájlmegosztásokkal kapcsolatos néhány gyakori problémát sorol fel. Ez biztosítja a lehetséges okokat és kerülő megoldásokat, amikor ezek a problémák merülnek fel.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Nagy késleltetés, alacsony átviteli teljesítmény és általános teljesítményproblémák

### <a name="cause-1-share-experiencing-throttling"></a>1. ok: Megosztás, amely szabályozást tapasztal

A prémium megosztás alapértelmezett kvótája 100 GiB, amely 100 alapkonfigurációi IOPS-t biztosít (amely egy órára akár 300-ig is felrobbanhat). A kiépítésről és az IOPS-szal való kapcsolatáról a tervezési útmutató [Kiépített megosztások](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) szakaszában talál további információt.

Annak ellenőrzéséhez, hogy a megosztás szabályozás alatt áll-e, használhatja az Azure Metrics-t a portálon.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatás** lehetőséget, majd keresse meg a **Metrikák kifejezést.**

1. Válassza a **Metrika** lehetőséget.

1. Válassza ki a tárfiókot erőforrásként.

1. Válassza a **Fájl** a metrika névtere lehetőséget.

1. Válassza a **Tranzakciók** mutatóként lehetőséget.

1. Adjon hozzá egy szűrőt a **ResponseType-hoz,** és ellenőrizze, hogy a kérelmek rendelkeznek-e **SuccessWithThrottling** (SMB esetén) vagy **ClientThrottlingError** (REST esetén) válaszkóddal.

![A prémium fájlmegosztások metrikabeállításai](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Ha riasztást szeretne kapni, ha egy fájlmegosztás szabályozott, olvassa [el a Riasztás létrehozása a fájlmegosztás szabályozása esetén című témakört.](#how-to-create-an-alert-if-a-file-share-is-throttled)

### <a name="solution"></a>Megoldás

- Növelje a megosztott kapacitást a megosztás magasabb kvótájának megadásával.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>2. ok: A metaadatok/névtér nagy munkaterhelés

Ha a kérelmek többsége metaadat-központú, (például createfile/openfile/closefile/queryinfo/querydirectory), akkor a késés rosszabb lesz az olvasási/írási műveletekhez képest.

Annak ellenőrzéséhez, hogy a legtöbb kérelem metaadat-központú-e, ugyanazokat a lépéseket használhatja, mint fent. A **ResponseType**szűrő hozzáadása nélkül adjon hozzá egy szűrőt az **API-névhez.**

![API-név szűrése a mérőszámokban](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Áthidaló megoldás

- Ellenőrizze, hogy az alkalmazás módosítható-e a metaadat-műveletek számának csökkentése érdekében.
- Adjon hozzá egy virtuális merevlemezt a fájlmegosztáshoz, és csatlakoztassa a VHD-t az ÜgyfélSMB-jéhez, és fájlokat hajtson végre az adatokon. Ez a megközelítés egyírós és több olvasós forgatókönyvesetén működik, és lehetővé teszi, hogy a metaadat-műveletek helyiek legyenek, és a helyi közvetlen ületos tárolóhoz hasonló teljesítményt kínálnak.

### <a name="cause-3-single-threaded-application"></a>3. ok: Egyszálas alkalmazás

Ha az ügyfél által használt alkalmazás egyszálas, ez azt eredményezheti, hogy jelentősen alacsonyabb IOPS/átviteli teljesítmény, mint a maximális lehetséges a kiosztott megosztás mérete alapján.

### <a name="solution"></a>Megoldás

- Növelje az alkalmazás párhuzamosságát a szálak számának növelésével.
- Váltson olyan alkalmazásokra, ahol a párhuzamosság lehetséges. Másolási műveletek esetén például az ügyfelek használhatják az AzCopy vagy RoboCopy windowsos ügyfelekről vagy a **párhuzamos** parancsot Linux-ügyfeleken.

## <a name="very-high-latency-for-requests"></a>Nagyon nagy késleltetés a kérelmekhez

### <a name="cause"></a>Ok

Az ügyfél virtuális gép e fájlmegosztástól eltérő régióban is elhelyezkedhet.

### <a name="solution"></a>Megoldás

- Futtassa az alkalmazást egy virtuális gépről, amely ugyanabban a régióban található, mint a fájlmegosztás.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Az ügyfél nem tudja elérni a hálózat által támogatott maximális átviteli értéket

Ennek egyik lehetséges oka az SMB többcsatornás támogatás hiánya. Jelenleg az Azure-fájlmegosztások csak egycsatornás, így csak egy kapcsolat az ügyfél virtuális gép a kiszolgálóhoz. Ez az egyetlen kapcsolat az ügyfél virtuális gépének egyetlen magjához van rögzítve, így a virtuális gépről elérhető maximális átviteli értéket egyetlen mag köti.

### <a name="workaround"></a>Áthidaló megoldás

- Egy nagyobb maggal rendelkező virtuális gép beszerzése javíthatja az átviteli hálózatot.
- Az ügyfélalkalmazás több virtuális gépről való futtatása növeli az átviteli szintet.

- Ahol lehetséges, használja a REST API-kat.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A Linux-ügyfelek átviteli hatása jelentősen alacsonyabb, mint a Windows-ügyfelek.

### <a name="cause"></a>Ok

Ez egy ismert probléma az SMB kliens linuxos megvalósításával.

### <a name="workaround"></a>Áthidaló megoldás

- A terhelés táganik több virtuális gépközött.
- Ugyanazon a virtuális gépen használjon több csatlakoztatási pontot **a nincsmegosztási** beállítással, és elosztva a terhelést ezeken a csatlakoztatási pontokon.
- Linux on próbálja szerelés **nostrictsync** opciót, hogy ne kényszerítse SMB flush minden **fsync** hívás. Az Azure Files esetében ez a beállítás nem zavarja az adatok konzisztenciáját, de elavult fájlmetaadatokat eredményezhet a címtárlistában (**ls -l** parancs). A fájl metaadatainak **(stat** parancs) közvetlen lekérdezése a legfrissebb fájl metaadatait adja vissza.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Nagy késés a metaadat-nagy számítási feladatok kiterjedt nyílt/záró műveleteket.

### <a name="cause"></a>Ok

A címtárbérletek támogatásának hiánya.

### <a name="workaround"></a>Áthidaló megoldás

- Ha lehetséges, rövid időn belül kerülje el a túlságosan nyitott/záró leírót ugyanazon a könyvtáron.
- Linuxos virtuális gépek esetén növelje a címtárbejegyzési gyorsítótár időidejét az **\<actimeo= sec>** csatlakoztatási beállításként megadásával. Alapértelmezés szerint ez egy másodperc, így egy nagyobb érték, mint a három vagy öt segíthet.
- Linuxos virtuális gépek esetén frissítse a kernelt 4.20-as vagy magasabb ra.

## <a name="low-iops-on-centosrhel"></a>Alacsony IOPS a CentOS/RHEL

### <a name="cause"></a>Ok

Az egynél nagyobb IO-mélység nem támogatott a CentOS/RHEL-en.

### <a name="workaround"></a>Áthidaló megoldás

- Frissítés a CentOS 8 / RHEL 8-ra.
- Váltás ubuntura.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Lassú fájlmásolás az Azure Files-ba és -ból Linux alatt

Ha lassú fájlmásolást tapasztal az Azure Files-ba és az Azure Files-ból, tekintse meg a Lassú fájl másolását a [Linux-alapú Azure-fájlokés linuxos fájlokból](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) szakasza a Linux hibaelhárítási útmutatójában.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Ideges/fűrészfogminta az IOPS-hoz

### <a name="cause"></a>Ok

Az ügyfélalkalmazás következetesen meghaladja az alapkonfigurációi IOPS-t. Jelenleg nincs a kérelem terhelésének szolgáltatási oldali simítása, így ha az ügyfél túllépi az alapkonfigurációi IOPS-t, a szolgáltatás szabályozza. Ez a szabályozás azt eredményezheti, hogy az ügyfél tapasztalt ideges/fűrészfogIOPS-minta. Ebben az esetben az ügyfél által elért átlagos IOPS alacsonyabb lehet, mint az alapszintű IOPS.

### <a name="workaround"></a>Áthidaló megoldás

- Csökkentse az ügyfélalkalmazásból érkező kérelemterhelést, hogy a megosztás ne kapjon szabályozott.
- Növelje a megosztás kvótáját, hogy a megosztás ne kapjon szabályozott.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Túlzott DirectoryOpen/DirectoryClose hívások

### <a name="cause"></a>Ok

Ha a DirectoryOpen/DirectoryClose hívások száma a legnépszerűbb API-hívások közé tartozik, és nem számít arra, hogy az ügyfél ennyi hívást kezdeményez, akkor lehet, hogy az Azure-ügyfél virtuális gépére telepített víruskereső vel van probléma.

### <a name="workaround"></a>Áthidaló megoldás

- A probléma javítása a [Windows áprilisi platformfrissítésében](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)érhető el.

## <a name="file-creation-is-slower-than-expected"></a>A fájl létrehozása a vártnál lassabb

### <a name="cause"></a>Ok

A nagy számú fájl létrehozására támaszkodó munkaterhelések nem fognak lényeges különbséget látni a prémium szintű fájlmegosztások és a szabványos fájlmegosztások teljesítménye között.

### <a name="workaround"></a>Áthidaló megoldás

- Nincs.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Lassú teljesítmény Windows 8.1 vagy Server 2012 R2 rendszerből

### <a name="cause"></a>Ok

A vártnál nagyobb késés az Azure Files az Io-intenzív számítási feladatok hoz.

### <a name="workaround"></a>Áthidaló megoldás

- Telepítse az elérhető [gyorsjavítást](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Riasztás létrehozása fájlmegosztás esetén

1. Az [Azure Portalon](https://portal.azure.com)kattintson a **Figyelő**elemre. 

2. Kattintson **a Riasztások,** majd **a + Új riasztási szabály**elemre.

3. A **Kijelölés** gombra kattintva jelölje ki azt a **tárfiókot/fájlerőforrást,** amely tartalmazza azt a fájlmegosztást, amelyről értesítést szeretne kapni, majd kattintson a **Kész**gombra. Ha például a tárfiók neve contoso, válassza ki a contoso/file erőforrást.

4. Feltétel hozzáadásához kattintson a **Hozzáadás** gombra.

5. Megjelenik a tárfiókhoz támogatott jelek listája, válassza ki a **Tranzakciók metrikát.**

6. A **Jellogika konfigurálása** panelen lépjen a **Választípus** dimenzióra, kattintson a **Dimenzióértékek** legördülő menüre, és válassza a **SuccessWithThrottling** (SMB esetén) vagy **az ClientThrottlingError** (REST esetén) lehetőséget. 

  > [!NOTE]
  > Ha a SuccessWithThrottling vagy clientThrottlingError dimenzió értéke nem szerepel a listában, ez azt jelenti, hogy az erőforrás nincs szabályozva.  A dimenzióérték hozzáadásához **+** kattintson a **Dimenzióértékek** legördülő menüre, írja be a **SuccessWithThrottling** vagy **a ClientThrottlingError parancsot,** kattintson az **OK** gombra, majd ismételje meg a #6 lépést.

7. Nyissa meg a **Fájlmegosztás** dimenziót, kattintson a **Dimenzió értékek** legördülő menüre, és válassza ki a riasztást tartalmazó fájlmegosztás(oka)t. 

  > [!NOTE]
  > Ha a fájlmegosztás szabványos fájlmegosztás, a dimenzióértékek legördülő lista üres lesz, mert az egy részvényre jutó mutatók nem érhetők el a szabványos fájlmegosztások esetében. A normál fájlmegosztások szabályozására vonatkozó riasztások akkor aktiválódnak, ha a tárfiókon belül bármilyen fájlmegosztás van szabályozva, és a riasztás nem azonosítja, hogy melyik fájlmegosztás takarta. Mivel az egy megosztáson kénti metrikák nem érhetők el a szabványos fájlmegosztások, a javaslat az, hogy egy fájlmegosztás takar-fiókonként. 

8. Adja meg azokat a **riasztási paramétereket** (küszöbérték, operátor, összesítési részletesség és gyakoriság), amelyek a metrikariasztási szabály kiértékelésére szolgálnak, és a Kész gombra kattintanak. **Done**

  > [!TIP]
  > Statikus küszöbérték használata esetén a metrikadiagram segíthet meghatározni egy ésszerű küszöbértéket, ha a fájlmegosztás jelenleg szabályozás alatt áll. Ha dinamikus küszöbértéket használ, a metrikadiagram a számított küszöbértékeket jeleníti meg a legutóbbi adatok alapján.

9. Adjon hozzá egy **műveletcsoportot** (e-mail, SMS stb.) a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy egy új műveletcsoport létrehozásával.

10. Töltse ki a **riasztás részleteit,** **például a riasztási szabály nevét**, a leírás **és** **a súlyosság .**

11. Kattintson **a Figyelmeztetési szabály létrehozása** gombra a riasztás létrehozásához.

Ha többet szeretne tudni a riasztások Azure Monitorban történő konfigurálásáról, [olvassa el a Riasztások áttekintése a Microsoft Azure-ban című témakört.]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
