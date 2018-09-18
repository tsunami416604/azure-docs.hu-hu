---
title: A Microsoft Genomics – hibaelhárítási útmutató
titleSuffix: Azure
description: További tudnivalók a hibaelhárítási stratégiák
keywords: hiba elhárítása, hibakeresés
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: bd946f84023345c68a01a48a4dc310b7afb68397
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735386"
---
# <a name="troubleshooting-guide-for-microsoft-genomics"></a>Hibaelhárítási útmutató a Microsoft Genomics
Ez az Áttekintés a Microsoft Genomics szolgáltatással gyakori hibáinak megoldásához stratégiákat ismerteti. Általános – gyakori kérdések, lásd: [gyakori kérdésekre](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Hogyan tudom ellenőrizni, a feladat állapota?
A munkafolyamat állapotát ellenőrizheti meghívásával `msgen status` a parancssorból, látható módon. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Nincsenek három szükséges argumentumok:
* Adresa URL – az alap URI az API-hoz
* KULCS – a Genomics-fiók hozzáférési kulcsára. 
* Azonosító – a munkafolyamat-Azonosítót

Keresse meg az URL-cím és a kulcs, az Azure portal megnyitásához és a Genomics-fiók lap megnyitásához. Alatt a **felügyeleti** fejléc kiválasztása **hozzáférési kulcsok**. Itt keresse meg az API URL-cím és a hozzáférési kulcsokat is.

Azt is megteheti közvetlenül a az URL-cím és a kulcs megadása helyett a konfigurációs fájl elérési útját is megadhat. Vegye figyelembe, hogy ezek az argumentumok a parancssorból, valamint a konfigurációs fájlt is, ha a parancssori argumentumok elsőbbséget. 

Hívása után `msgen status`, egy felhasználóbarát üzenet jelenik meg, amely leírja, hogy sikerült-e a munkafolyamat vagy jogosultságot ad a feladat hiba okát. 


## <a name="get-more-information-about-my-workflow-status"></a>További információ a munkafolyamat-állapot beolvasása

További információért arról, hogy miért feladat lehet, hogy sikeres volt, áttekintheti a munkafolyamat során létrehozott naplófájlokat. A kimeneti tárolóhoz látnia kell egy `[youroutputfilename].logs.zip` mappát.  Ez a mappa kicsomagolta, jelenik meg a következő elemek:

* a munkafolyamat során előállított outputFileList.txt – a kimeneti fájlok listája
* StandardError.txt – Ez a fájl, akkor üres.
* StandardOutput.txt – tartalmazza a munkafolyamat legfelső szintű naplózását. 
* GATK naplófájlok – az összes többi fájl a `logs` mappa

A `standardoutput.txt` fájl egy nagyszerű hely az induláshoz megállapítani, miért érdemes a munkafolyamatot nem sikerült, mivel a munkafolyamat több alacsony szintű információkat tartalmaz. 

## <a name="common-issues-and-how-to-resolve-them"></a>Gyakori problémákat és azok megoldását
Ez a szakasz röviden kiemeli a gyakori problémákat és azok megoldását.

### <a name="fastq-files-are-unmatched"></a>Fastq-fájloknak is páratlan
Fastq-fájloknak csak a záró /1 vagy a minta azonosítójában /2 kell eltérőek. Ha véletlenül elküldte páratlan FASTQ-fájlt, előfordulhat, hogy hibaüzenetek jelennek meg a következő hívásakor `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Ennek megoldásához tekintse át a Ha a munkafolyamat elküldése fastq fájlokat ténylegesen egy egyező csoportot. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Hiba történt a .bam fájl feltöltését. Kimeneti blob már létezik, és a felülírási beállítást hamis értékre lett beállítva.
Ha a következő hibaüzenet jelenik meg `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, a kimeneti mappa már tartalmaz egy azonos nevű kimeneti fájl.  Vagy törölje a meglévő kimeneti fájlt, vagy kapcsolja be a felülírási beállítást a konfigurációs fájlban. Ezután küldje el újra a munkafolyamatot.

### <a name="when-to-contact-microsoft-genomics-support"></a>Kapcsolatfelvétel a Microsoft Genomics-támogatás
A következő hibaüzenetek jelennek meg, ha egy belső hiba történt. 

* `Error locating input files on worker machine`
* `Process management failure`

Próbálja meg újra elküldeni a munkafolyamatot. Ha továbbra is a sikertelen feladatok rendelkezik, vagy ha más kérdése van, forduljon a Microsoft Genomics-támogatás az Azure Portalról. Egy támogatási kérést szeretne beküldeni kapcsolatos további információ található [Itt](file-support-ticket-genomics.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a Microsoft Genomics szolgáltatással kapcsolatos leggyakoribb problémák elhárítására. További információ és további általános – gyakori kérdések: [gyakori kérdésekre](frequently-asked-questions-genomics.md). 
