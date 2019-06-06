---
title: Konfigurálja, optimalizálhat és hibaelhárítása az Azure Storage AzCopy |} A Microsoft Docs
description: Konfigurálja, optimalizálhat és AzCopy hibaelhárítása.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 18dc3e224df18c900653e4549badcdd93f0df6ec
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688016"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurálja, optimalizálhat és AzCopy hibaelhárítása

Az AzCopy parancssori segédprogram, amely a blobok és a fájlok másolása, illetve a storage-fiók használatával. Ez a cikk segítséget nyújt a speciális konfigurációs feladatok végrehajtásához, és segít az AzCopy használata során felmerülő problémák hibaelhárításához.

> [!NOTE]
> Ha a keresett tartalom segítséget nyújt a Bevezetés az AzCopy használatába, jelennek meg a következő cikkeket:
> - [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
> - [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)
> - [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)
> - [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Konfigurálja a proxybeállításokat az AzCopy, állítsa be a `https_proxy` környezeti változót.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Az AzCopy jelenleg nem támogatja az NTLM vagy Kerberos hitelesítést igénylő proxyt.

## <a name="optimize-throughput"></a>Teljesítmény optimalizálása

Állítsa be a `AZCOPY_CONCURRENCY_VALUE` környezeti változó konfigurálása az egyidejű kérelmek száma, valamint a átviteli teljesítmény- és erőforrás-használat szabályozása. Ha 5-nél kevesebb processzort a számítógépben, akkor a változó értéke `32`. Ellenkező esetben az alapértelmezett érték a processzorok száma szorozva 16 egyenlő. Az alapértelmezett maximális érték a változó `300`, de manuálisan is magasabb vagy alacsonyabb érték beállítása.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Használja a `azcopy env` Ez a változó aktuális értéke ellenőrzéséhez.  Ha az érték üres, akkor a `AZCOPY_CONCURRENCY_VALUE` változó értéke az alapértelmezett érték `300`.

## <a name="change-the-location-of-the-log-files"></a>A naplófájlok helyének módosítása

Alapértelmezés szerint a naplófájlok találhatók a `%USERPROFILE\\.azcopy` Windows vagy a könyvtárban a `$HOME\\.azcopy` Macre és Linuxra könyvtárába. Ez a hely módosíthatja, ha ezek a parancsok használatával kell.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Használja a `azcopy env` Ez a változó aktuális értéke ellenőrzéséhez. Ha az érték üres, majd naplók kerüljenek az alapértelmezett helyet.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosításához

Alapértelmezés szerint az AzCopy naplózási szintje `INFO`. Ha szeretné, hogy lemezterületet a napló részletességi csökkentése érdekében, az felülírja ezt a beállítást a ``--log-level`` lehetőséget. 

Elérhető naplózási szintek a következők: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, és `FATAL`.

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Az AzCopy minden feladat napló és a terv fájlt hoz létre. A naplók segítségével megvizsgálhatja és az esetleges problémák elhárításában. 

A naplók tartalmazni fogja a sikertelen állapotát (`UPLOADFAILED`, `COPYFAILED`, és `DOWNLOADFAILED`), a teljes elérési útja, és a hiba okát.

Alapértelmezés szerint a napló és a terv fájlok találhatók a `%USERPROFILE\\.azcopy` Windows könyvtárába vagy `$HOME\\.azcopy` Macre és Linuxra könyvtárába.

> [!IMPORTANT]
> Amikor egy kérés elküldése Support (vagy a hiba elhárításához bármely harmadik féltől származó használata esetén), megoszthatja a kivonatosan verzióját szeretné végrehajtani a parancsot. Ez biztosítja a SAS véletlenül ne oszthassák meg olyan bárki. A kivont verzió elején. a naplófájl található.

### <a name="review-the-logs-for-errors"></a>Tekintse át a hibákat a naplók

A következő parancsot az összes hibát kap `UPLOADFAILED` állapota a `04dc9ca9-158f-7945-5933-564021086c79` napló:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden adatátviteli műveletet egy AzCopy feladatot hoz létre. A következő paranccsal-feladatok előzményeinek megtekintése:

```
azcopy jobs list
```

A feladat statisztikai adatok megtekintéséhez használja a következő parancsot:

```
azcopy jobs show <job-id>
```

Szűrés állapot szerint átvitelek, használja a következő parancsot:

```
azcopy jobs show <job-id> --with-status=Failed
```

A következő paranccsal egy sikertelen vagy megszakítva feladat folytatása. Állandó biztonsági okokból nem ezt a parancsot használja az azonosítót és a SAS-jogkivonat:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

A feladat folytatásakor AzCopy megvizsgálja a feladat terv fájlt. A terv fájl felsorolja a meghatározott feldolgozásához, amikor a feladat létrehozásakor. A feladat folytatásakor AzCopy megkísérli átvitelét, hogy a csomag fájlt, amely már átvitt nem találhatók fájlok.