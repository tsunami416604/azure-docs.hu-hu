---
title: AzCopy konfigurálása, optimalizálása és hibáinak megoldása az Azure Storage szolgáltatással | Microsoft Docs
description: AzCopy konfigurálása, optimalizálása és megoldása.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 42d2dae148b83687ff06d4ed321a881bcb9e7ae0
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273924"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy konfigurálása, optimalizálása és megoldása

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ez a cikk segítséget nyújt a speciális konfigurációs feladatok elvégzésében, valamint a AzCopy használata során felmerülő problémák elhárításában.

> [!NOTE]
> Ha olyan tartalmat keres, amely segítséget nyújt a AzCopy megkezdéséhez, tekintse meg a következő cikkeket:
> - [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
> - [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
> - [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
> - [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

A AzCopy proxybeállítások konfigurálásához állítsa be a `https_proxy` környezeti változót. Ha Windows rendszeren futtatja a AzCopy-t, a AzCopy automatikusan észleli a proxybeállításokat, így nem kell ezt a beállítást használnia a Windows rendszerben. Ha úgy dönt, hogy ezt a beállítást használja a Windows rendszerben, az automatikusan felülbírálja az automatikus észlelést.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | A parancssorban használja a következőket: `set https_proxy=<proxy IP>:<proxy port>`<br> A PowerShell használata: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

A AzCopy jelenleg nem támogatja az NTLM-vagy Kerberos-hitelesítést igénylő proxykat.

## <a name="optimize-performance"></a>A teljesítmény optimalizálása

A teljesítmény és az erőforrás-felhasználás közötti optimális kompromisszum megtalálásához használhatja a teljesítményt, majd parancsokat és környezeti változókat használhat.

### <a name="run-benchmark-tests"></a>Teljesítményteszt-tesztek futtatása

Adott blob-tárolók teljesítmény-teljesítményteszt-tesztjét futtatva megtekintheti az általános teljesítménnyel kapcsolatos statisztikákat és a teljesítmény szűk keresztmetszeteit. 

> [!NOTE]
> A jelenlegi kiadásban ez a funkció csak Blob Storage tárolók esetében érhető el.

A teljesítmény-teljesítményteszt teszt futtatásához használja a következő parancsot.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Példa** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Ez a parancs teljesítménytesztet futtat egy megadott célhelyre való feltöltéssel. A tesztelési adatok a memóriában jönnek létre, feltöltve a célhelyre, majd a teszt befejezése után törlődnek a célhelyről. Megadhatja, hogy hány fájl legyen létrehozva, és hogy milyen méretben szeretné őket használni a választható parancssori paraméterek használatával.

A parancs részletes súgójának megtekintéséhez írja be a `azcopy bench -h` értéket, majd nyomja le az ENTER billentyűt.

### <a name="optimize-throughput"></a>Teljesítmény optimalizálása

A `cap-mbps` jelzővel egy felső korlátot helyezhet el az adatátviteli sebességen. Például a következő parancs Caps átviteli sebessége `10` megabit (MB)/másodperc.

```azcopy
azcopy cap-mbps 10
```

Az átviteli sebesség kisebb fájlok átvitele esetén is csökkenhet. Az átviteli sebességet a `AZCOPY_CONCURRENCY_VALUE` környezeti változó beállításával növelheti. Ez a változó határozza meg az egyidejű kérések számát.  

Ha a számítógép kevesebb mint 5 processzorral rendelkezik, akkor a változó értéke `32`. Ellenkező esetben az alapértelmezett érték 16, szorozva a processzorok számával. A változó alapértelmezett értéke @no__t – 0, de ezt az értéket manuálisan vagy lejjebb is állíthatja. 

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

A változó aktuális értékének vizsgálatához használja a `azcopy env` értéket. Ha az érték üres, akkor megtekintheti, hogy melyik értéket használja a rendszer a AzCopy-naplófájl elején. A kijelölt érték és a kiválasztott ok a jelentésekben szerepelnek.

Mielőtt beállítja ezt a változót, javasoljuk, hogy futtasson egy teljesítményteszt-tesztet. A teljesítményteszt-tesztelési folyamat a javasolt Egyidejűség értékét fogja jelenteni. Ha a hálózati feltételek és a hasznos adatok eltérőek, a változót egy adott szám helyett a `AUTO` szót kell beállítania. Ez azt eredményezi, hogy a AzCopy mindig ugyanazt az Automatikus hangolási folyamatot fogja futtatni, amelyet a teljesítményteszt-tesztekben használ.

### <a name="optimize-memory-use"></a>Memória használatának optimalizálása

Állítsa be a `AZCOPY_BUFFER_GB` környezeti változót a fájlok letöltésekor és feltöltésekor használni kívánt AzCopy maximális mennyiségének megadásához.
Ez az érték gigabájtban (GB) kifejezve.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Problémák elhárítása

A AzCopy minden feladattípus esetében létrehozza a naplófájlokat és megtervezi a fájlokat. A naplók segítségével megvizsgálhatja és elháríthatja a lehetséges problémákat. 

A naplók a hiba állapotát (`UPLOADFAILED`, `COPYFAILED` és `DOWNLOADFAILED`) tartalmazzák, a teljes elérési utat és a hiba okát.

Alapértelmezés szerint a napló-és a terv fájlok a `%USERPROFILE$\.azcopy` könyvtárban találhatók a Windows vagy a `$HOME$\.azcopy` könyvtáron Mac és Linux rendszeren, de ha kívánja, módosíthatja ezt a helyet.

> [!IMPORTANT]
> Amikor kérelmet küld a Microsoft ügyfélszolgálata (vagy bármely harmadik féltől származó probléma elhárítása), ossza meg a futtatni kívánt parancs kivont verzióját. Ez biztosítja, hogy az SAS véletlenül senkivel se legyen megosztva. A leválasztott verziót a naplófájl elején találja.

### <a name="review-the-logs-for-errors"></a>A hibákkal kapcsolatos naplók áttekintése

A következő parancs az `UPLOADFAILED` állapotú összes hibát lekérdezi a `04dc9ca9-158f-7945-5933-564021086c79` naplóból:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden egyes átviteli művelet létrehoz egy AzCopy-feladatot. A következő parancs használatával tekintheti meg a feladatok előzményeit:

```
azcopy jobs list
```

A feladatok statisztikáinak megtekintéséhez használja a következő parancsot:

```
azcopy jobs show <job-id>
```

Az átvitelek állapot szerinti szűréséhez használja a következő parancsot:

```
azcopy jobs show <job-id> --with-status=Failed
```

A sikertelen/megszakított feladat folytatásához használja az alábbi parancsot. Ez a parancs az azonosítót az SAS-jogkivonattal együtt használja, mivel a biztonsági okokból nem állandó.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Amikor folytat egy feladatot, a AzCopy megtekinti a feladatütemezés fájlját. A terv fájl felsorolja a feladatok első létrehozásakor feldolgozásra azonosított összes fájlt. Ha folytatja a feladatot, a AzCopy megkísérli átvinni az összes olyan fájlt, amely a csomagban már nem lett áthelyezve.

## <a name="change-the-location-of-the-plan-and-log-files"></a>A csomag és a naplófájlok helyének módosítása

Alapértelmezés szerint a csomag-és naplófájlok a Windows `%USERPROFILE$\.azcopy` könyvtárában vagy a Mac és Linux rendszeren futó `$HOME$\.azcopy` könyvtárban találhatók. Ezt a helyet módosíthatja.

### <a name="change-the-location-of-plan-files"></a>A csomag fájljai helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

A változó aktuális értékének vizsgálatához használja a `azcopy env` értéket. Ha az érték üres, akkor a fájlok megtervezése az alapértelmezett helyre történik.

### <a name="change-the-location-of-log-files"></a>A naplófájlok helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

A változó aktuális értékének vizsgálatához használja a `azcopy env` értéket. Ha az érték üres, akkor a naplók az alapértelmezett helyre íródnak.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosítása

Alapértelmezés szerint a AzCopy naplózási szintje a következőre van beállítva: `INFO`. Ha csökkenteni szeretné a napló részletességét, hogy lemezterületet szabadítson fel, írja felül ezt a beállítást a ``--log-level`` kapcsoló használatával. 

A rendelkezésre álló naplózási szintek a következők: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` és `FATAL`.

## <a name="remove-plan-and-log-files"></a>Csomag-és naplófájlok eltávolítása

Ha el szeretné távolítani az összes csomagot és naplófájlt a helyi gépről, hogy lemezterületet szabadítson fel, használja a `azcopy jobs clean` parancsot.

A csak egy feladatokhoz társított csomag és naplófájlok eltávolításához használja a `azcopy jobs rm <job-id>` értéket. Cserélje le a példában szereplő `<job-id>` helyőrzőt a feladatokhoz tartozó feladattípusra.


