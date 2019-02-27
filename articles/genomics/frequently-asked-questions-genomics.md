---
title: 'A Microsoft Genomics: Gyakori kérdések – gyakori kérdések |} A Microsoft Docs'
titleSuffix: Azure
description: Gyakori kérdések ügyfeleknek adott válaszok a Microsoft Genomics érdeklődjön.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: df329d42022f4e56b3d6d4d26519acd42caa1ae4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881821"
---
# <a name="microsoft-genomics-common-questions"></a>A Microsoft Genomics: Gyakori kérdések

Ez a cikk a leggyakoribb lekérdezések, előfordulhat, hogy rendelkezik a Microsoft Genomics kapcsolódó sorolja fel. A Microsoft Genomics szolgáltatásba további információkért lásd: [Mi a Microsoft Genomics?](overview-what-is-genomics.md). Hibaelhárítással kapcsolatos további információkért lásd: a [hibaelhárítási útmutatója](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Mi a Microsoft Genomics szolgáltatás GATK 4 előléptetés?
2018 a naptári év végéig 20 WGS fut GATK4 költségek nélkül kínál a Microsoft Genomics szolgáltatással. Ez az ajánlat register részt [Itt](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Mik azok a gyakori problémák jutok előfordulhat, hogy a futtatásakor a Microsoft Genomics szolgáltatás GATK4 támogatása
Itt látható az esetleg felmerülő gyakori hibák és a megoldást ajánlott alkalmazni:

| **üzenet**                                                                                                                                                                                    | **OK**                                                                                                    | **Felbontás**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` a fiók nincs engedélyezve. További információkért lásd: https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | Próbált GATK4 a munkafolyamatok futtatásához a Microsoft Genomics szolgáltatással aktiválás nélkül.       | Látogasson el [Itt](https://aka.ms/msgatk4) a fiók aktiválásához. Vegye figyelembe, hogy a próbaidőszak végén található a 2018-as év. Nem tudja aktiválni a saját fiókját a promóciós fut ezen időpont utáni. |
| Köszönjük, hogy közben `gatk4-promo`. A próbaidőszak véget ért. További információ https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | Az év végén a GATK4 próbaverzió lejárt, és szeretne meghívni a `gatk4-promo` folyamat_neve.  | Váltson a folyamat_neve paraméter, `gatk4`, hanem `gatk4-promo`. Ez a hivatalos gatk4 verziót, és a munkafolyamat számítjuk fel, ha ezt a paramétert használja.                                         |
| Köszönjük, hogy közben `gatk4-promo` minden, a lefoglalt futtatások használja. További információkért lásd: https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | Sikeresen elküldte az összes a promóciós 20 GATK4 futtat.                               | Küldje el minden olyan új gatk4 fut, folyamat_neve argumentum értéke `gatk4` helyett `gatk4-promo`. Ez a paraméter használata esetén a munkafolyamat lesznek számlázva.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Futtathatók GATK4 munkafolyamatokat a Microsoft Genomics GATK4 elősegítő regisztráció nélkül?
Igen, a Microsoft Genomics szolgáltatás a config.txt fájlban adja meg, a folyamat_neve `gatk4`. Vegye figyelembe, hogy számlázása normál díjszabása nem módosul, és az ingyenes 20 fut. nem vonatkozik a Microsoft Genomics-fiók.



## <a name="what-is-the-sla-for-microsoft-genomics"></a>Mit jelent az SLA-t a Microsoft Genomics?
Garantáljuk, hogy az idő a Microsoft Genomics szolgáltatás 99,9 %-át lesz-e elérhető munkafolyamat API-kérelmek fogadásához. További információkért lásd: [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hogyan nem a Microsoft Genomics használatát meg a számlámon?
A Microsoft Genomics számlázása a munkafolyamatonként feldolgozott gigabázisok száma alapján. További információkért lásd: [díjszabási](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Hol található az összes lehetséges parancsok és az argumentumok listájának a `msgen` ügyfél?
Elérhető parancsok és argumentumok teljes listáját megtekintheti a futó `msgen help`. Ha nincsenek további argumentum megadott érhető el súgó listáját jeleníti meg szakaszokban: az egyik minden `submit`, `list`, `cancel`, és `status`. Egy adott parancs súgójának, írja be a `msgen help command`; például `msgen help submit` a küldési beállításokat sorolja fel.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Mik azok a leggyakrabban használt parancsok a `msgen` ügyfél?
A leggyakrabban használt parancsok argumentumai a `msgen` ügyfél tartalmazza: 

 |**Parancs**          |  **Mező leírása** |
 |:--------------------|:-------------         |
 |`list`               |Elküldött feladatok listáját adja vissza. További argumentumok: `msgen help list`.  |
 |`submit`             |A szolgáltatás a munkafolyamat kérést küld. További argumentumok: `msgen help submit`.|
 |`status`             |A munkafolyamat által meghatározott állapotát adja vissza `--workflow-id`. Lásd még: `msgen help status`. |
 |`cancel`             |Megszakítja a munkafolyamat által meghatározott feldolgozási kérést küld `--workflow-id`. Lásd még: `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Hol találom ezt az értéket `--api-url-base`?
Nyissa meg az Azure Portalon, és a Genomics-fiók lap megnyitásához. Alatt a **felügyeleti** fejléc kiválasztása **hozzáférési kulcsok**. Itt keresse meg az API URL-cím és a hozzáférési kulcsokat is.

## <a name="where-do-i-get-the-value-for---access-key"></a>Hol találom ezt az értéket `--access-key`?
Nyissa meg az Azure Portalon, és a Genomics-fiók lap megnyitásához. Alatt a **felügyeleti** fejléc kiválasztása **hozzáférési kulcsok**. Itt keresse meg az API URL-cím és a hozzáférési kulcsokat is.

## <a name="why-do-i-need-two-access-keys"></a>Miért kell a két hozzáférési kulcsot?
Abban az esetben, ha frissíti a (regenerate) két kulcsot kell azokat a szolgáltatás használatának megszakítása nélkül. Például ha meg szeretné frissíteni az első kulcsot, kell minden olyan új munkafolyamatot, amely a második kulcsot használja. Ezután Várjon, amíg befejeződik, mielőtt frissítené az első kulcsot az első kulcsot használó összes munkafolyamatot.

## <a name="do-you-save-my-storage-account-keys"></a>Menti a tárfiók kulcsait?
A tárfiók kulcsát a Microsoft Genomics szolgáltatás a bemeneti fájlok olvasása és írása a kimeneti fájlok rövid távú hozzáférési jogkivonatok létrehozására szolgál. A jogkivonat alapértelmezett időtartama érték 48 óra. A jogkivonat időtartama is módosítható a `-sas/--sas-duration` a Küldés parancsot; a beállítás értéke (óra).

## <a name="what-genome-references-can-i-use"></a>Milyen genom hivatkozik használható?

Ezek a hivatkozások támogatottak:
 |Leírások              | Az érték `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nincs helyettesítő elemzés) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hogyan formázza a parancssori argumentumokat egy konfigurációs fájlba? 

msgen tisztában van azzal a konfigurációs fájlok a következő formátumban:
* Minden beállítás, a kulcs-érték párok rendelkezésre álló értékek, kulcsok egymástól kettősponttal elválasztva.
Szóközöket a rendszer figyelmen kívül hagyja.
* Kezdődő sorokat `#` figyelmen kívül hagyja.
* A hosszú formátumban bármely parancssori argumentum ezért a vezető kötőjelek és kötőjeleket, aláhúzásjeleket a szavak közötti cseréje egy kulcs alakítható ki. Íme néhány átalakítás példa:

 |Parancssori argumentum            | A konfigurációs fájl sora |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>További lépések

Ismerkedés a Microsoft Genomics használja az alábbi forrásanyagokat:
- Első lépésként az első munkafolyamat futtatása a Microsoft Genomics szolgáltatással. [Munkafolyamat futtatása a Microsoft Genomics szolgáltatással](quickstart-run-genomics-workflow-portal.md)
- Küldje el a Microsoft Genomics szolgáltatásba az a saját adatokat a feldolgozáshoz: [párosított FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [több FASTQ vagy BAM](quickstart-input-multiple.md) 

