---
title: 'A Microsoft Genomics: hibaelhárítási útmutató |} A Microsoft Docs'
titleSuffix: Azure
description: További tudnivalók a hibaelhárítási stratégiák
keywords: hiba elhárítása, hibakeresés
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 01f4cca8e177d8f9c9325a7920eb7e72c005bbd6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760938"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Íme néhány hibaelhárítási tippek a gyakori problémák, amelyek a Microsoft Genomics MSGEN szolgáltatás használata esetén előfordulhat, hogy között.

 A GYIK, hibaelhárítás, nem kapcsolódó témakör [gyakori kérdésekre](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>1. lépés: Keresse meg a munkafolyamathoz társított hibakódok

A munkafolyamat által társított hibaüzenetek keresheti meg:

1. A parancssor használatával, majd írja be a  `msgen status`
2. Standardoutput.txt tartalmának vizsgálata.

### <a name="1-using-the-command-line-msgen-status"></a>1. A parancssor használatával `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Nincsenek három szükséges argumentumok:

* Adresa URL – az alap URI az API-hoz
* KULCS – a Genomics-fiók elérési kulcsának
    * Keresse meg az URL-CÍMÉT és KULCSÁT, nyissa meg az Azure Portalra, és nyissa meg a Microsoft Genomics-fiók oldalon. Alatt a **felügyeleti** fejléc kiválasztása **hozzáférési kulcsok**. Itt keresse meg az API URL-cím és a hozzáférési kulcsokat is.

  
* Azonosító – a munkafolyamat-Azonosítót
    * A munkafolyamat azonosító típusa a található `msgen list` parancsot. Feltéve, hogy a konfigurációs fájl tartalmazza az URL-cím és a hozzáférési kulcsokat, és ugyanazon a helyen az msgen exe szerepel a parancs a következőképpen jelenik meg: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Ez a parancs kimenete a következőképpen jelenik meg:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

 > [!NOTE]
 >  Másik megoldásként közvetlenül az az URL-cím és a kulcs megadása helyett a konfigurációs fájl elérési útját is megadhat. Ha ezek az argumentumok adja meg a parancssort, valamint a konfigurációs fájlban, a parancssori argumentumok elsőbbséget élvez.  

A munkafolyamat azonosítója 1001, és a végrehajtható fájl elérési útja megegyezik a msgen kerülnek, a config.txt fájlhoz a parancs a következőképpen jelenik meg:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Standardoutput.txt tartalmának vizsgálata 
Keresse meg a szóban forgó munkafolyamat a kimeneti tárolóhoz. MSGEN létrehoz egy, `[workflowfilename].logs.zip` mappa minden munkafolyamat végrehajtása után. Csomagolja ki a mappa tartalmának megtekintéséhez:

* a munkafolyamat során előállított outputFileList.txt – a kimeneti fájlok listája
* StandardError.txt – Ez a fájl, akkor üres.
* StandardOutput.txt – beleértve a munkafolyamat futtatása közben fellépő hibák minden felső szintű állapotüzenetek naplózza.
* GATK naplófájlok – az összes többi fájl a `logs` mappa

Hibaelhárítási, vizsgálja meg a standardoutput.txt tartalmát, és jegyezze fel a megjelenő hibaüzeneteket.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>2. lépés: Próbálja ki a gyakori hibák elhárításához ajánlott lépéseket

Ez a szakasz röviden kiemeli a gyakori hibák kimeneti (msgen) a Microsoft Genomics szolgáltatásba, és a problémák megoldásához használható stratégiákról. 

A Microsoft Genomics szolgáltatással (msgen) a következő két típusú hibákat nagyvállalat:

1. Belső hiba: Előfordulhat, hogy nem oldható fel paraméterek vagy a bemeneti fájlok kijavítását, a szolgáltatás belső hibákat. A munkafolyamat néha újraküldése előfordulhat, hogy javítsa ki a hibákat.
2. Beviteli hibák: A hibákat, javítása vagy megfelelő argumentumokkal megoldhatók a fájlformátumokat.

### <a name="1-internal-service-errors"></a>1. Belső hiba

Egy belső szolgáltatáshiba nem felhasználó gyakorlatban is használható. Előfordulhat, hogy küldje el újra a munkafolyamatot, de nem működik, ha forduljon a Microsoft Genomics-támogatás

| Hibaüzenet                                                                                                                            | Javasolt hibaelhárítási lépések                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Belső hiba történt. Próbálja meg ismét a munkafolyamatot. Ha újra megjelenik ez a hiba, forduljon segítségért a Microsoft Genomics-támogatási szolgálatához | Küldje el újra a munkafolyamatot. Forduljon a Microsoft Genomics segítségért támogatja, ha a probléma továbbra is fennáll, hozzon létre egy támogatási [jegy](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Beviteli hibák

Ezek a hibák a gyakorlatban hasznosítható felhasználói. Fájl és hibakód alapján, a Microsoft Genomics szolgáltatás különböző hibakódok jelenít meg. Kövesse az alábbi javasolt hibaelhárítási lépéseket.

| Fájltípus | Hibakód | Hibaüzenet                                                                           | Javasolt hibaelhárítási lépések                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Bármelyik          | 701        | Olvassa el [readId] [numberOfBases] bázisok rendelkezik, de a korlát [maxReadLength]           | Ez a hiba leggyakoribb oka két olvasási összefűzésén vezető fájl sérülése. Ellenőrizze a bemeneti fájlokat. |                                |
| BAM          | 200        |   Nem sikerült beolvasni a fájlt a(z) "[yourFileName]".                                                                                       | Ellenőrizze a BAM-fájl formátumát. Küldje el a munkafolyamat újra egy megfelelően formázott fájllal.                                                                           |
| BAM          | 201        |  Nem sikerült beolvasni a BAM-fájlból [fájlnév].                                                                                      |Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                            |
| BAM          | 202        | Nem sikerült beolvasni a BAM-fájlból [fájlnév]. Túl kicsi és a hiányzó fejléc.                                                                                        | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                            |
| BAM          | 203        |   Nem sikerült beolvasni a BAM-fájlból [fájlnév]. Fájl fejléce sérült volt.                                                                                      |Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                           |
| BAM          | 204        |    Nem sikerült beolvasni a BAM-fájlból [fájlnév]. Fájl fejléce sérült volt.                                                                                     | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                           |
| BAM          | 205        |    Nem sikerült beolvasni a BAM-fájlból [fájlnév]. Fájl fejléce sérült volt.                                                                                     | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                            |
| BAM          | 206        |   Nem sikerült beolvasni a BAM-fájlból [fájlnév]. Fájl fejléce sérült volt.                                                                                      | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                            |
| BAM          | 207        |  Nem sikerült beolvasni a BAM-fájlból [fájlnév]. A fájl csonkolva [offset] eltolás közelében.                                                                                       | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                            |
| BAM          | 208        |   Érvénytelen BAM-fájl. A ReadID [Read_Id] [fájlnév]-fájlban nem feladatütemezési rendelkezik.                                                                                      | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelő formátumú fájlt.                                                                             |
| FASTQ        | 300        |  Nem sikerült beolvasni a FASTQ-fájlt. [Fájlnév] nem végződhet egy új sor.                                                                                     | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                           |
| FASTQ        | 301        |   Nem sikerült beolvasni a [fájlnév] FASTQ-fájlt. FASTQ-rekord mérete nagyobb, mint a puffer mérete eltolásnál: [_offset]                                                                                      | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 302        |     FASTQ szintaktikai hiba. Fájl [fájlnév] egy üres sort tartalmaz.                                                                                    | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 303        |       FASTQ szintaktikai hiba. Fájl [fájlnév] kiindulási érvénytelen karaktert tartalmaz eltolásnál: [_offset], sor írja be: [line_type], karakter: [_char]                                                                                  | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 304      |  [_ReadID] readID FASTQ szintaktikai hiba.  Először olvassa el a Batch nem rendelkezik a [fájlnév]-fájlban /1 végződésű readID                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 305        |  [_ReadID] readID FASTQ szintaktikai hiba. Második olvassa el a Batch nem rendelkezik a [fájlnév]-fájlban /2 végződésű readID                                                                                      | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 306        |  [_ReadID] readID FASTQ szintaktikai hiba. Első olvasási pár nem rendelkezik, amely /1 fájlban [fájlnév]-azonosítója                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 307        |   [_ReadID] readID FASTQ szintaktikai hiba. ReadID nem végződhet /1 vagy / 2. [Fájlnév] fájl nem használható a párosított FASTQ-fájlként.                                                                                      |Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 308        |  FASTQ olvasási hiba. Mindkét olvasási eltérően válaszolt. Döntött a megfelelő FASTQ-fájlokat?                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>3. lépés: Forduljon a Microsoft Genomics-támogatás

Ha továbbra is a sikertelen feladatok rendelkezik, vagy ha más kérdése van, forduljon a Microsoft Genomics-támogatás az Azure Portalról. Egy támogatási kérést szeretne beküldeni kapcsolatos további információ található [Itt](file-support-ticket-genomics.md).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a Microsoft Genomics szolgáltatással kapcsolatos leggyakoribb problémák elhárítására. További információ és további általános – gyakori kérdések: [gyakori kérdésekre](frequently-asked-questions-genomics.md). 
