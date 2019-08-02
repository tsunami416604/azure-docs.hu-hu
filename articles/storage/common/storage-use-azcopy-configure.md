---
title: AzCopy konfigurálása, optimalizálása és hibáinak megoldása az Azure Storage szolgáltatással | Microsoft Docs
description: AzCopy konfigurálása, optimalizálása és megoldása.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3773f9a8464dc94436d6d2503b173d4674033ab1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565043"
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
| **Windows** | A parancssorban használja a következőket:`set https_proxy=<proxy IP>:<proxy port>`<br> A PowerShellben használja a következőket:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

A AzCopy jelenleg nem támogatja az NTLM-vagy Kerberos-hitelesítést igénylő proxykat.

## <a name="optimize-throughput"></a>Teljesítmény optimalizálása

A `cap-mbps` jelzővel egy felső korlátot helyezhet el az adatátviteli sebességen. Például a következő parancs Caps- `10` átviteli sebessége másodpercenként megabitre (MB).

```azcopy
azcopy cap-mbps 10
```

Az átviteli sebesség kisebb fájlok átvitele esetén is csökkenhet. A környezeti változó beállításával növelheti az `AZCOPY_CONCURRENCY_VALUE` átviteli sebességet is. Ez a változó határozza meg az egyidejű kérések számát.  Ha a számítógép kevesebb mint 5 processzorral rendelkezik, akkor a változó értéke a következő lesz `32`:. Ellenkező esetben az alapértelmezett érték 16, szorozva a processzorok számával. Ennek a változónak `300`a maximális alapértelmezett értéke a, de ezt az értéket manuálisan vagy lejjebb is állíthatja.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

A változó `azcopy env` aktuális értékének megadásához használja a következőt:.  Ha az érték üres, akkor a `AZCOPY_CONCURRENCY_VALUE` változó az alapértelmezett `300`értékre van állítva.

## <a name="change-the-location-of-the-log-files"></a>A naplófájlok helyének módosítása

Alapértelmezés szerint a naplófájlok a `%USERPROFILE\\.azcopy` Windows könyvtárban vagy `$HOME\\.azcopy` a Mac és Linux rendszerű könyvtárban találhatók. Ezt a helyet akkor módosíthatja, ha ezeket a parancsokat kell használnia.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

A változó `azcopy env` aktuális értékének megadásához használja a következőt:. Ha az érték üres, akkor a naplók az alapértelmezett helyre íródnak.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosítása

Alapértelmezés szerint a AzCopy naplózási szintje a `INFO`következőre van beállítva:. Ha csökkenteni szeretné a napló részletességét, hogy lemezterületet szabadítson fel, írja felül ezt a beállítást a ``--log-level`` kapcsoló használatával. 

A rendelkezésre álló naplózási `DEBUG`szintek `INFO`a `WARNING`következők `ERROR`: `PANIC`,, `FATAL`,, és.

## <a name="troubleshoot-issues"></a>Problémák elhárítása

A AzCopy minden feladattípus esetében létrehozza a naplófájlokat és megtervezi a fájlokat. A naplók segítségével megvizsgálhatja és elháríthatja a lehetséges problémákat. 

A naplók a hiba (`UPLOADFAILED`, `COPYFAILED`, és `DOWNLOADFAILED`) állapotát, a teljes elérési utat és a hiba okát fogják tartalmazni.

Alapértelmezés szerint a napló-és a megtervezési fájlok a `%USERPROFILE\\.azcopy` Windows vagy `$HOME\\.azcopy` a címtár Mac és Linux rendszerű könyvtárában találhatók.

> [!IMPORTANT]
> Amikor kérelmet küld a Microsoft ügyfélszolgálata (vagy bármely harmadik féltől származó probléma elhárítása), ossza meg a futtatni kívánt parancs kivont verzióját. Ez biztosítja, hogy az SAS véletlenül senkivel se legyen megosztva. A leválasztott verziót a naplófájl elején találja.

### <a name="review-the-logs-for-errors"></a>A hibákkal kapcsolatos naplók áttekintése

A következő parancs az állapottal kapcsolatos `UPLOADFAILED` összes hibát a `04dc9ca9-158f-7945-5933-564021086c79` naplóból fogja kapni:

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