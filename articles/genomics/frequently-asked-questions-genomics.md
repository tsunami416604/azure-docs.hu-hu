---
title: "Microsoft Genomics: Gyakori kérdések |} Microsoft Docs"
titleSuffix: Azure
description: "Gyakori kérdések az ügyfelek adott válaszok kérdezze Microsoft Genomics."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2077eeb5177b07c458476ae900f81b72e35f0dc3
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Gyakori kérdések

Ez a cikk az első lehetséges, hogy lekérdezések kapcsolódik a Microsoft Genomics sorolja fel. A Microsoft Genomics szolgáltatás további információkért lásd: [Újdonságok a Microsoft Genomics?](overview-what-is-genomics.md) 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Mi az a szolgáltatásiszint-szerződés, Microsoft Genomics?
Garantáljuk, hogy a Microsoft Genomics időszolgáltatás 99,9 %-os munkafolyamat API-kérelmek fogadásához elérhető lesz-e. További információkért lásd: [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>A Microsoft Genomics használatát hogyan nem jelennek meg a saját számlázási?
Microsoft Genomics váltók egy munkafolyamat feldolgozott gigabases száma alapján. További információkért lásd: [árazás](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Hol található az összes lehetséges parancsok és az argumentumok listájának a `msgen` ügyfél?
Elérhető parancsok és argumentumok teljes listájának lekéréséhez futtassa `msgen help`. Ha nincsenek további argumentum megadott érhető el súgó listája szakaszokban: az egyik az egyes `submit`, `list`, `cancel`, és `status`. Egy adott parancs súgójának megjelenítéséhez írja be a következőt `msgen help command`, például `msgen help submit` felsorolja a küldési beállításokat.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Mi a leggyakrabban használt parancsok a `msgen` ügyfél?
A leggyakrabban használt parancsok argumentumai a `msgen` ügyfél tartalmazza: 

 |**A parancs**          |  **Mező leírása** |
 |:--------------------|:-------------         |
 |`list`               |Elküldött feladatok listáját adja vissza. Argumentumok, lásd: `msgen help list`.  |
 |`submit`             |Munkafolyamat kérést küld a szolgáltatást. Argumentumok, lásd: `msgen help submit`.|
 |`status`             |A munkafolyamat által megadott állapotának visszaadása `--workflow-id`. Lásd még: `msgen help status`. |
 |`cancel`             |Szakítsa meg a munkafolyamat által megadott feldolgozása egy kérést küld `--workflow-id`. Lásd még: `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Hol találhatok értéke `--api-url-base`?
Nyissa meg az Azure-portálhoz, és nyissa meg a genomika lapra. Az a **felügyeleti** elemcsoportban válasszon **hívóbetűk**. Itt megtalálhatja API URL-CÍMÉT, mind a tárelérési kulcsok.

## <a name="where-do-i-get-the-value-for---access-key"></a>Hol találhatok értéke `--access-key`?
Nyissa meg az Azure-portálhoz, és nyissa meg a genomika lapra. Az a **felügyeleti** elemcsoportban válasszon **hívóbetűk**. Itt megtalálhatja API URL-CÍMÉT, mind a tárelérési kulcsok.

## <a name="why-do-i-need-two-access-keys"></a>Miért kell két kulcsot?
Abban az esetben, ha frissíti a (regenerate) két kulcsot kell őket a szolgáltatás használatának megszakítása nélkül. Például szeretné frissíteni az első kulcsot. Ebben az esetben vált minden új munkafolyamat a második kulcs használatával. Ezután Várjon, amíg az első kulcs használatával már futó munkafolyamat befejezte volna. Csak ezután frissítse a kulcsot.

## <a name="do-you-save-my-storage-account-keys"></a>Menti a tárfiók kulcsait?
A bemeneti fájlok olvasását és írását a kimeneti fájlok a Microsoft Genomics szolgáltatás rövid távú hozzáférési jogkivonatok létrehozásához használt a tárfiók kulcsára. Az alapértelmezett jogkivonat időtartama 48 óra. A jogkivonat időtartama használatával módosítható a `-sas/--sas-duration` beállítást, a Küldés parancs; órában értéke.

## <a name="what-genome-references-can-i-use"></a>Milyen genom hivatkozik használhatok?

Ezek a hivatkozások támogatottak:
 |Referencia              | Az érték`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nincs alt elemzés) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>A parancssori argumentumok formázása konfigurációs fájlba 

msgen együttműködik a konfigurációs fájlokat a következő formátumban:
* Minden beállítások vannak megadva, a kulcs-érték párok értékekkel kulcsok alapján kettősponttal elválasztva.
Elválasztó karakter a rendszer figyelmen kívül hagyja.
* Kezdődő sorok `#` figyelmen kívül lesznek hagyva.
* A hosszú formátumban bármelyik parancssori argumentum konvertálható kulcs annak a vezető kötőjelek és gondolatjelek aláhúzásjelek a szavak közötti cseréje. Íme néhány átalakítás példa:

 |Parancssori argumentumot            | A konfigurációs fájl sora |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://URL*    |
 |`-k/--access-key KEY`            | *access_key:key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Következő lépések

Ismerkedés a Microsoft Genomics használja a következőket:
- Ismerkedés a Microsoft Genomics szolgáltatáson keresztül az első munkafolyamat futtatásával. [Egy munkafolyamat futtatásához a Microsoft Genomics szolgáltatáson keresztül](quickstart-run-genomics-workflow-portal.md)
- A saját adatok feldolgozásra küldése a Microsoft Genomics szolgáltatás: [FASTQ párosítva](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [több FASTQ vagy BAM](quickstart-input-multiple.md) 

