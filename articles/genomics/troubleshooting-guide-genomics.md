---
title: 'Microsoft genomika: hibaelhárítási útmutatója |} Microsoft Docs'
titleSuffix: Azure
description: További tudnivalók stratégiák hibaelhárítása
keywords: hiba elhárítása, hibakeresés
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Ez az Áttekintés a Microsoft Genomics szolgáltatással stratégiák gyakori problémákat ismerteti. Általános – gyakori kérdések, lásd: [gyakori kérdéseket](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Hogyan ellenőrizhető a feladat állapota?
A munkafolyamat állapotát ellenőrizheti meghívásával `msgen status` a parancssorból, látható módon. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Három kötelező argumentuma van:
* URL - alap URI-Azonosítójának a API-hoz.
* KULCS - genomika fiókját a hozzáférési kulcsot. 
* Azonosító: a munkafolyamat azonosítója

Az URL-cím és a kulcs kereséséhez nyissa meg az Azure-portálhoz, és nyissa meg a genomika lapra. Az a **felügyeleti** elemcsoportban válasszon **hívóbetűk**. Itt megtalálhatja API URL-CÍMÉT, mind a tárelérési kulcsok.

Másik lehetőségként megadhat közvetlenül a az URL-cím és a kulcs megadása helyett a konfigurációs fájl elérési útját. Vegye figyelembe, hogy ha ezekkel az argumentumokkal adja meg a parancssort, valamint a konfigurációs fájl, a parancssori argumentumok élvez elsőbbséget. 

Hívása után `msgen status`, felhasználóbarát üzenet jelenik meg, amely leírja, hogy a munkafolyamat sikeresen végrehajtotta, vagy jogosultságot ad a feladat sikertelenségének okát. 


## <a name="get-more-information-about-my-workflow-status"></a>A munkafolyamat állapotával kapcsolatos további információért

További információért arról, hogy miért egy feladat lehet, hogy sikeres volt, ismerje meg a munkafolyamat során létrehozott naplófájlokat. A kimeneti tárolóban kell megjelennie a `[youroutputfilename].logs.zip` mappát.  Ez a mappa kicsomagolás, jelenik meg a következő elemek:

* a munkafolyamat során előállított outputFileList.txt – a kimeneti fájlok listája
* StandardError.txt – a fájl üres.
* StandardOutput.txt - a munkafolyamat legfelső szintű naplózását tartalmazza. 
* GATK naplófájlok - minden más fájlnál a `logs` mappa

A `standardoutput.txt` fájl egy remek kezdőpont meghatározni, miért a munkafolyamat sikertelen volt, a munkafolyamat több alacsony szintű információkat tartalmaz. 

## <a name="common-issues-and-how-to-resolve-them"></a>Gyakori problémákat és azok megoldása
Ez a szakasz röviden kiemeli a gyakori problémákat és azok megoldását.

### <a name="fastq-files-are-unmatched"></a>Fastq fájljai nem egyező
Fastq fájlok kell csak térnek el egymástól a záró /1 vagy /2 az minta azonosító. Ha véletlenül nyújtott páratlan FASTQ fájlok, előfordulhat, hogy hibaüzenetek jelennek meg a következő meghívásakor `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

A probléma megoldásához, tekintse át a Ha a munkafolyamat küldve fastq-fájlokat ténylegesen egyező készletet. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Hiba történt a .bam fájl feltöltése. Kimeneti blob már létezik, és a felülírási beállítás False értékre lett állítva.
Ha a következő hibaüzenet jelenik meg `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, a kimeneti mappa már tartalmaz egy azonos nevű kimeneti fájl.  Törölje a meglévő kimeneti fájlt, vagy kapcsolja be a felülírási beállítás a konfigurációs fájlban. Majd küldje el újra a munkafolyamatot.

### <a name="when-to-contact-microsoft-genomics-support"></a>Kapcsolatfelvétel a Microsoft Genomics támogatása
A következő hibaüzenetek jelennek meg, ha egy belső hiba. 

* `Error locating input files on worker machine`
* `Process management failure`

Próbálja meg újra elküldeni a munkafolyamat. Ha továbbra is a sikertelen feladatok vagy bármilyen más kérdése van, forduljon a Microsoft genomika támogatási Azure-portálról.

![Forduljon a támogatási szolgálathoz Azure-portál](./media/troubleshooting-guide/genomics-contact-support.png "forduljon a támogatási szolgálathoz Azure-portál")

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, és a Microsoft Genomics szolgáltatással kapcsolatos gyakori problémák megoldása. További információk és további általános – gyakori kérdések: [gyakori kérdéseket](frequently-asked-questions-genomics.md). 