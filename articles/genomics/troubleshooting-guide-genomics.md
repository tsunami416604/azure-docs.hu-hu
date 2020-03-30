---
title: Hibaelhárítási útmutató
titleSuffix: Microsoft Genomics
description: További információ a Microsoft Genomics használatára vonatkozó hibaelhárítási stratégiákról, beleértve a hibaüzeneteket és azok megoldását.
keywords: hibaelhárítás, hiba, hibakeresés
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73485999"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Íme néhány hibaelhárítási tipp a Microsoft Genomics szolgáltatás msgen szolgáltatásának használatakor felmerülő gyakori problémákhoz.

 A hibaelhárítással nem kapcsolatos gyakori kérdések a Gyakori kérdések című [témakörben talál.](frequently-asked-questions-genomics.md)
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>1. lépés: Keresse meg a munkafolyamathoz társított hibakódokat

A munkafolyamathoz társított hibaüzenetek a következők szerint kereshetők fel:

1. A parancssor használata és a gépelés`msgen status`
2. A standardoutput.txt fájl tartalmának vizsgálata.

### <a name="1-using-the-command-line-msgen-status"></a>1. A parancssor használata`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Három szükséges argumentum van:

* URL - az API alap URI-ja
* KEY - a genomikai fiók hozzáférési kulcsa
    * Az URL-cím és a KULCS megkereséséhez nyissa meg az Azure Portalt, és nyissa meg a Microsoft Genomics-fiókoldalát. A **Kezelés** fejlécalatt válassza az **Access-kulcsok lehetőséget.** Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

  
* Azonosító - a munkafolyamat azonosítója
    * A munkafolyamat-azonosító típusának `msgen list` megkeresése a parancsban. Feltételezve, hogy a konfigurációs fájl tartalmazza az URL-címet és a hozzáférési kulcsokat, és ugyanazon a helyen található, mint az msgen exe, a parancs így fog kinézni: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Kimenet ebből a parancsból így fog kinézni:
        
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
  >  Másik lehetőségként a konfigurációs fájl elérési útját is megadhatja ahelyett, hogy közvetlenül beírnák az URL-címet és a KULCSOT. Ha ezeket az argumentumokat a parancssorba és a konfigurációs fájlba is belefoglalja, a parancssori argumentumok élveznek elsőbbséget.  

Az 1001-es munkafolyamat-azonosító és az msgen végrehajtható fájllal azonos elérési úton elhelyezett config.txt fájl esetében a parancs a következőképpen fog kinézni:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Vizsgálja meg a standardoutput.txt 
Keresse meg a szóban forgó munkafolyamat kimeneti tárolóját. Az MSGEN `[workflowfilename].logs.zip` minden munkafolyamat-végrehajtás után létrehoz egy mappát. Csomagolja ki a mappát a mappa tartalmának megtekintéséhez:

* outputFileList.txt - a munkafolyamat során létrehozott kimeneti fájlok listája
* standarderror.txt - ez a fájl üres.
* standardoutput.txt - naplózza az összes legfelső szintű állapotüzenetet, beleértve a hibákat is, amelyek a munkafolyamat futtatása közben történtek.
* GATK naplófájlok - a `logs` mappában lévő összes többi fájl

Hibaelhárításhoz vizsgálja meg a standardoutput.txt fájl tartalmát, és jegyezze fel a megjelenő hibaüzeneteket.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>2. lépés: A gyakori hibák javasolt lépéseinek kipróbálása

Ez a szakasz röviden kiemeli a Microsoft Genomics szolgáltatás (msgen) által kikényszerített gyakori hibákat, valamint a megoldásukhoz használható stratégiákat. 

A Microsoft Genomics szolgáltatás (msgen) a következő kétféle hibát okozhat:

1. Belső szolgáltatási hibák: Olyan hibák, amelyek a szolgáltatáson belül vannak, és amelyek nem oldhatók meg paraméterek vagy bemeneti fájlok javításával. Előfordulhat, hogy a munkafolyamat újbóli elküldése megoldja ezeket a hibákat.
2. Beviteli hibák: A megfelelő argumentumokkal vagy fájlformátumok javításával megoldható hibák.

### <a name="1-internal-service-errors"></a>1. Belső szolgáltatási hibák

A belső szolgáltatáshiba nem használható a felhasználó által végrehajtható. Újra elküldheti a munkafolyamatot, de ha ez nem működik, forduljon a Microsoft Genomics támogatási

| Hibaüzenet                                                                                                                            | Javasolt hibaelhárítási lépések                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Belső hiba történt. Próbálja meg újra beküldeni a munkafolyamatot. Ha ismét megjelenik ez a hiba, kérjen segítséget a Microsoft Genomics ügyfélszolgálatátél. | Küldje el újra a munkafolyamatot. Ha a probléma továbbra is fennáll, kérjen segítséget a Microsoft Genomics támogatási [támogatásához](file-support-ticket-genomics.md )támogatási jegy létrehozásával. |

### <a name="2-input-errors"></a>2. Beviteli hibák

Ezek a hibák a felhasználó által végrehajtható. A fájl típusától és a hibakódtól függően a Microsoft Genomics szolgáltatás különböző hibakódokat ad ki. Kövesse az alábbi hibaelhárítási lépéseket.

| Fájl típusa | Hibakód | Hibaüzenet                                                                           | Javasolt hibaelhárítási lépések                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Bármelyik          | 701        | Az Olvasási [readId] [numberOfBases] bázisokkal rendelkezik, de a korlát [maxReadLength]           | A hiba leggyakoribb oka a fájlsérülés, amely két olvasás összefűzéséhez vezet. Ellenőrizze a bemeneti fájlokat. |
| Bam          | 200        |   Nem lehet olvasni a következő fájlt: "[yourFileName]".                                                                                       | Ellenőrizze a BAM-fájl formátumát. Küldje el újra a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| Bam          | 201        |  Nem lehet olvasni a (File_name) BAM-fájlt.                                                                                      |Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| Bam          | 202        | Nem lehet olvasni a (File_name) BAM-fájlt. A fájl túl kicsi és hiányzik a fejléc.                                                                                        | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| Bam          | 203        |   Nem lehet olvasni a (File_name) BAM-fájlt. A fájl fejléce sérült.                                                                                      |Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| Bam          | 204        |    Nem lehet olvasni a (File_name) BAM-fájlt. A fájl fejléce sérült.                                                                                     | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| Bam          | 205        |    Nem lehet olvasni a (File_name) BAM-fájlt. A fájl fejléce sérült.                                                                                     | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| Bam          | 206        |   Nem lehet olvasni a (File_name) BAM-fájlt. A fájl fejléce sérült.                                                                                      | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| Bam          | 207        |  Nem lehet olvasni a (File_name) BAM-fájlt. A fájl csonkolva az eltolás közelében (eltolás].                                                                                       | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| Bam          | 208        |   Érvénytelen BAM-fájl. A [Read_Id] readid azonosítónak nincs szekvenciája a [File_name] fájlban.                                                                                      | Ellenőrizze a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                             |
| FASTQ        | 300        |  Nem lehet olvasni a FASTQ fájlt. [File_name] nem ér véget egy új vonallal.                                                                                     | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                           |
| FASTQ        | 301        |   Nem lehet olvasni a FASTQ fájlt [File_name]. A FASTQ rekord nagyobb, mint a pufferméret eltoláskor: [_offset]                                                                                      | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 302        |     FASTQ szintaxis hiba. A[File_name] fájlnak üres sora van.                                                                                    | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 303        |       FASTQ szintaxis hiba. A fájl[File_name] kezdő karaktere érvénytelen a következő eltoláskor: [_offset], sortípusa: [line_type], karakter: [_char]                                                                                  | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 304      |  FASTQ szintaxishiba a readID [_ReadID]  A köteg első olvasási száma nem végződik /1-re a fájlban [File_name]                                                                                       | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 305        |  FASTQ szintaxishiba a readID [_readID] A köteg második olvasása nem rendelkezik /2 végződéssel a fájlban [File_name]                                                                                      | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 306        |  FASTQ szintaxishiba a readID [_ReadID] A pár első olvasása nem rendelkezik /1 azonosítóval a fájlban [File_name]                                                                                       | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 307        |   FASTQ szintaxishiba a readID [_ReadID] A ReadID nem végződik /1 vagy/2-vel. A fájl [File_name] nem használható párosított FASTQ fájlként.                                                                                      |Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 308        |  FASTQ olvasási hiba. Mindkét vég olvasása másképp reagált. A megfelelő FASTQ fájlokat választotta?                                                                                       | Javítsa ki a FASTQ fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>3. lépés: Lépjen kapcsolatba a Microsoft Genomics ügyfélszolgálatával

Ha továbbra is sikertelen feladatokkal kapcsolatos hibák jelentkeznek, vagy ha bármilyen más kérdése van, forduljon a Microsoft Genomics ügyfélszolgálatához az Azure Portalon. A támogatási kérelem benyújtásának módjáról további információt [itt](file-support-ticket-genomics.md)talál.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatja, hogyan háríthatja el és oldhatja meg a Microsoft Genomics szolgáltatással kapcsolatos gyakori problémákat. További információkért és általánosabb gyakori kérdésekért lásd: [Gyakori kérdések](frequently-asked-questions-genomics.md). 
