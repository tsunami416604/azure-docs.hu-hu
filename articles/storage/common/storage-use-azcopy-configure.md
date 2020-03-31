---
title: Az AzCopy konfigurálása, optimalizálása és hibaelhárítása az Azure Storage szolgáltatással | Microsoft dokumentumok
description: Konfigurálja, optimalizálja és elhárítsa az AzCopy-t.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d2cb40d7510e46539db46bdb61ec2d64c0fd1ec7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526495"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Az AzCopy konfigurálása, optimalizálása és hibaelhárítása

Az AzCopy egy parancssori segédprogram, amely segítségével blobok vagy fájlok másolása egy tárfiókba. Ez a cikk segítséget nyújt a speciális konfigurációs feladatok végrehajtásához, és segítséget nyújt az AzCopy használata során felmerülő problémák elhárításához.

> [!NOTE]
> Ha olyan tartalmat keres, amely segít az AzCopy használata megkezdéséhez, olvassa el az alábbi cikkek egyikét:
> - [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
> - [Adatok átvitele az AzCopy és blob tárházával](storage-use-azcopy-blobs.md)
> - [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
> - [Adatátvitel az AzCopy és az Amazon S3 gyűjtőkkel](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Az AzCopy proxybeállításainak konfigurálásához `https_proxy` állítsa be a környezeti változót. Ha az AzCopy programot Windows rendszeren futtatja, az AzCopy automatikusan észleli a proxybeállításokat, így nem kell használnia ezt a beállítást a Windows rendszerben. Ha úgy dönt, hogy ezt a beállítást használja a Windows rendszerben, az felülírja az automatikus észlelést.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | Parancssorban használja a következőket:`set https_proxy=<proxy IP>:<proxy port>`<br> PowerShell-használat esetén:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

Az AzCopy jelenleg nem támogatja az NTLM vagy Kerberos hitelesítést igénylő proxykat.

## <a name="optimize-performance"></a>Teljesítmény optimalizálása

Teljesítményteljesítmény-teljesítmény, majd a parancsok és a környezeti változók segítségével megtalálja az optimális kompromisszumot a teljesítmény és az erőforrás-felhasználás között.

Ez a szakasz az alábbi optimalizálási feladatok elvégzését segíti elő:

> [!div class="checklist"]
> * Teljesítményteszt-tesztek futtatása
> * Átviteli hang optimalizálása
> * A memóriahasználat optimalizálása 
> * Fájlszinkronizálás optimalizálása

### <a name="run-benchmark-tests"></a>Teljesítményteszt-tesztek futtatása

Teljesítmény-teljesítménytesztet futtathat adott blobtárolókon az általános teljesítménystatisztikák és az identitásteljesítmény-szűk keresztmetszetek megtekintéséhez. 

> [!NOTE]
> A jelenlegi kiadásban ez a funkció csak a Blob Storage-tárolók érhető el.

A következő paranccsal teljesítménytesztet futtat.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Példa** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Ez a példa az elérési út argumentumait egyszeres idézőjelekkel ('') zárja. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

Ez a parancs teljesítmény-referenciaértéket futtat a tesztadatok adott helyre való feltöltésével. A tesztadatok a memóriában jönnek létre, feltöltésre kerülnek a célhelyre, majd a teszt befejezése után törlődnek a célhelyről. A választható parancsparaméterek kel megadható, hogy hány fájlt és milyen méretűfájlokat szeretne létrehozni.

A részletes referencia-dokumentumokért lásd [az azcopy padot](storage-ref-azcopy-bench.md).

A parancs részletes súgóútmutatójának `azcopy bench -h` megtekintéséhez írja be, majd nyomja le az ENTER billentyűt.

### <a name="optimize-throughput"></a>Átviteli hang optimalizálása

A parancsokban `cap-mbps` lévő jelző segítségével felső határt helyezhet el az átviteli sebesség sebességén. A következő parancs például folytatja a feladatot, és másodpercenként `10` iramban (MB) korlátozza az átviteli terhelést. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Az átviteli fény kis fájlok átvitelekekekénte csökkenhet. Növelheti az átviteli szintet a `AZCOPY_CONCURRENCY_VALUE` környezeti változó beállításával. Ez a változó az egyidejű kérelmek számát adja meg.  

Ha a számítógép 5-nél kevesebb processzort rendelkezik, `32`akkor a változó értéke . Ellenkező esetben az alapértelmezett érték 16 és a PROCESSZORok számának szorzata. A változó maximális alapértelmezett `3000`értéke a , de manuálisan is beállíthatja ezt az értéket magasabbra vagy alacsonyabbra. 

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env` A segítségével ellenőrizze a változó aktuális értékét. Ha az érték üres, akkor az AzCopy naplófájl elejének megnézésével elolvashatja, hogy melyik értéket használja. A kijelölt érték és a kijelölés oka ott lesz jelentve.

A változó beállítása előtt azt javasoljuk, hogy futtasson egy benchmark tesztet. A benchmark-teszt folyamat jelenti az ajánlott egyidejűségi értéket. Másik lehetőségként, ha a hálózati feltételek és a `AUTO` hasznos terhelések eltérőek, állítsa ezt a változót a szó helyett egy adott számot. Ez azt eredményezi, hogy az AzCopy mindig ugyanazt az automatikus hangolási folyamatot futtatja, amelyet a benchmark tesztekben használ.

### <a name="optimize-memory-use"></a>A memóriahasználat optimalizálása

Állítsa `AZCOPY_BUFFER_GB` be a környezeti változót, hogy megadja a rendszermemória maximális mennyiségét, amelyet az AzCopy használni szeretne a fájlok letöltésekénél és feltöltésekénél.
Ezt az értéket gigabájtban (GB) fejezzük ki.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Fájlszinkronizálás optimalizálása

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs azonosítja a célhelyen lévő összes fájlt, majd összehasonlítja a fájlneveket és az utolsó módosított időbélyegeket a szinkronizálási művelet megkezdése előtt. Ha nagy számú fájlja van, akkor javíthatja a teljesítményt az előzetes feldolgozás megszüntetésével. 

Ehhez használja inkább az [azcopy copy parancsot,](storage-ref-azcopy-copy.md) és állítsa a `--overwrite` jelzőt a értékre. `ifSourceNewer` Az AzCopy összehasonlítja a fájlokat, mivel azok másolása nélkül előzetes vizsgálatok és összehasonlítások. Ez teljesítményélezést biztosít azokban az esetekben, amikor nagy számú fájlt kell összehasonlítani.

Az [azcopy copy copy](storage-ref-azcopy-copy.md) parancs nem törli a fájlokat a célhelyről, ezért ha törölni szeretné a fájlokat a célhelyen, amikor már nem léteznek a forrásnál, akkor használja az [azcopy sync](storage-ref-azcopy-sync.md) parancsot a `--delete-destination` `true` vagy `prompt`értékre beállított jelzővel. 

## <a name="troubleshoot-issues"></a>Problémák elhárítása

AzCopy napló- és tervfájlokat hoz létre minden feladathoz. A naplók segítségével vizsgálja meg és hárítsa el az esetleges problémákat. 

A naplók tartalmazzák a hiba`UPLOADFAILED` `COPYFAILED`állapotát `DOWNLOADFAILED`( , , és a ) a teljes elérési utat, és a hiba okát.

Alapértelmezés szerint a napló- és csomagfájlok a Windows vagy a `%USERPROFILE%\.azcopy` Mac és `$HOME$\.azcopy` Linux könyvtárában találhatók, de ezt a helyet megváltoztathatja, ha szeretné.

A releváns hiba nem feltétlenül az első hiba, amely megjelenik a fájlban. Olyan hibák esetén, mint a hálózati hibák, az időkitöltések és a kiszolgáló foglaltsági hibák, az AzCopy legfeljebb 20 alkalommal próbálja meg újra, és általában az újrapróbálkozási folyamat sikeres.  Az első látható hiba lehet valami ártalmatlan, hogy sikeresen újra.  Tehát ahelyett, hogy a fájl első hibáját nézene, `COPYFAILED`keresse `DOWNLOADFAILED`meg a közelében `UPLOADFAILED`lévő hibákat, vagy . 

> [!IMPORTANT]
> Amikor kérelmet küld a Microsoft támogatási szolgálatához (vagy elhárítja a harmadik feleket érintő problémát), ossza meg a végrehajtani kívánt parancs szerkesztett verzióját. Ez biztosítja, hogy a SAS véletlenül nem osztotta meg senkivel. A szerkesztett verzió a naplófájl elején található.

### <a name="review-the-logs-for-errors"></a>Hibák naplózásának áttekintése

A következő parancs a `UPLOADFAILED` `04dc9ca9-158f-7945-5933-564021086c79` naplóból az összes állapotú hibát beszerzi:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden átviteli művelet létrehoz egy AzCopy-feladatot. A feladatok előzményeinek megtekintéséhez használja a következő parancsot:

```
azcopy jobs list
```

A feladatstatisztikák megtekintéséhez használja a következő parancsot:

```
azcopy jobs show <job-id>
```

Az átvitelek állapot szerinti szűréséhez használja a következő parancsot:

```
azcopy jobs show <job-id> --with-status=Failed
```

A következő paranccsal folytathatja a sikertelen/megszakított feladatot. Ez a parancs az azonosítóját és a SAS-jogkivonatot használja, mivel biztonsági okokból nem állandó:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Az elérési út argumentumait, például a SAS-tokent egyszeres idézőjelekkel ('') csatolja. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

Amikor folytatja a feladatot, az AzCopy megvizsgálja a feladatterv fájlját. A tervfájl felsorolja az összes olyan fájlt, amelyet a feladat első létrehozásakor feldolgozásra azonosítottak. Amikor folytatja a feladatot, az AzCopy megpróbálja átvinni a tervfájlban felsorolt összes fájlt, amely még nem lett átruházva.

## <a name="change-the-location-of-the-plan-and-log-files"></a>A terv és a naplófájlok helyének módosítása

Alapértelmezés szerint a terv- és `%USERPROFILE%\.azcopy` naplófájlok a Windows `$HOME$\.azcopy` könyvtárában, illetve maces és Linuxos könyvtárban találhatók. Ezt a helyet módosíthatja.

### <a name="change-the-location-of-plan-files"></a>A tervfájlok helyének módosítása

Használja ezeket a parancsokat.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env` A segítségével ellenőrizze a változó aktuális értékét. Ha az érték üres, akkor a tervfájlok az alapértelmezett helyre kerülnek.

### <a name="change-the-location-of-log-files"></a>A naplófájlok helyének módosítása

Használja ezeket a parancsokat.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env` A segítségével ellenőrizze a változó aktuális értékét. Ha az érték üres, akkor a naplók az alapértelmezett helyre kerülnek.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplószint módosítása

Alapértelmezés szerint az AzCopy naplószintje `INFO`a . Ha csökkenteni szeretné a napló részletességét, hogy lemezterületet takarítson ``--log-level`` meg, írja felül ezt a beállítást a beállítás használatával. 

A rendelkezésre `NONE`álló `DEBUG` `INFO`naplószintek a következők: , `WARNING`, , `ERROR`, , `PANIC`, és `FATAL`.

## <a name="remove-plan-and-log-files"></a>Terv- és naplófájlok eltávolítása

Ha az összes terv- és naplófájlt el szeretné távolítani a `azcopy jobs clean` helyi számítógépről, hogy lemezterületet takarítson meg, használja a parancsot.

Ha csak egy feladathoz tartozó terv- `azcopy jobs rm <job-id>`és naplófájlokat szeretne eltávolítani, használja a használatát. Cserélje `<job-id>` le a helyőrzőebben ebben a példában a feladat azonosítóját.


