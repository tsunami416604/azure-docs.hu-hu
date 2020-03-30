---
title: Gyakori kérdések - GYAKORI KÉRDÉSEK
titleSuffix: Microsoft Genomics
description: Válaszok a Microsoft Genomics szolgáltatás használatával kapcsolatos gyakori kérdésekre, beleértve a technikai információkat, az SLA-t és a számlázást.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986036"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Gyakori kérdések

Ez a cikk a Microsoft Genomics-hoz kapcsolódó leggyakoribb lekérdezéseket sorolja fel. A Microsoft Genomics szolgáltatásról a [Mi a Microsoft Genomics?](overview-what-is-genomics.md). A hibaelhárításról további információt a [Hibaelhárítási útmutatóban](troubleshooting-guide-genomics.md)talál. 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Hogyan futtathatom a GATK4 munkafolyamatokat a Microsoft Genomics-on?
A Microsoft Genomics szolgáltatás config.txt fájljában adja `gatk4`meg a process_name. Felhívjuk figyelmét, hogy a számlázás a szokásos számlázási díjakat fogja kifizetni.

## <a name="how-do-i-enable-output-compression"></a>Hogyan engedélyezhetem a kimeneti tömörítést?
A kimeneti vcf vagy gvcf tömörítése a kimeneti tömörítés opcionális argumentuma használatával. Ez egyenértékű a `-bgzip` futással, majd `-tabix` a vcf vagy `.gz` gvcf kimenettel, hogy (bgzip kimenet) és `.tbi` (tabix kimenet) fájlokat készítsen. `bgzip`tömöríti a vcf vagy gvcf fájlt, és `tabix` létrehoz egy indexet a tömörített fájlhoz. Az argumentum egy logikai érték, amely `false` alapértelmezés szerint a `true` vcf kimenetre, a gcvf kimenetre pedig alapértelmezés szerint van beállítva. A parancssorban való használathoz `--bgzip-output` `true` adja meg `-bz` vagy futtassa `false`a (futtatási bgzip és tabix) vagy a . Ha ezt az argumentumot szeretné használni `bgzip_output: true` a `bgzip_output: false` config.txt fájlban, adja hozzá vagy vegye fel a fájlt.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Mi a Microsoft Genomics SLA-ja?
Garantáljuk, hogy a Microsoft Genomics szolgáltatás az idő 99,9%-ában elérhető lesz a munkafolyamat API-kéréseinek fogadására. További információ: [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hogyan jelenik meg a Microsoft Genomics használata a számlámon?
A Microsoft Genomics számlái a munkafolyamatonként feldolgozott gigabázisok száma alapján. További információ: [Pricing](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Hol találok listát az ügyfél összes lehetséges `msgen` parancsáról és argumentumáról?
Az elérhető parancsok és argumentumok teljes `msgen help`listáját a futva kaphatja meg. Ha nincs megadva további argumentum, akkor a rendelkezésre álló `submit`súgószakaszok `cancel`listáját `status`jeleníti meg, mindegyikhez egy- `list`és . Ha egy adott parancshoz `msgen help command`szeretne segítséget kérni, írja be a következőt: ; például `msgen help submit` az összes beküldési lehetőség felsorasztása.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Melyek az `msgen` ügyfél leggyakrabban használt parancsai?
A leggyakrabban használt parancsok az ügyfél `msgen` argumentumai: 

 |**Parancs**          |  **Mező leírása** |
 |:--------------------|:-------------         |
 |`list`               |A beküldött feladatok listáját adja vissza. Az argumentumokat `msgen help list`lásd: .  |
 |`submit`             |Munkafolyamat-kérelmet küld a szolgáltatásnak. Az argumentumokat `msgen help submit`lásd: .|
 |`status`             |A megadott munkafolyamat állapotát adja `--workflow-id`eredményül. Lásd `msgen help status`még: . |
 |`cancel`             |A megadott `--workflow-id`munkafolyamat feldolgozásának megszakítására vonatkozó kérés küldése. Lásd `msgen help cancel`még: . |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Hol kapom meg `--api-url-base`az értéket?
Nyissa meg az Azure Portalt, és nyissa meg genomics-fiókját. A **Kezelés** fejlécalatt válassza az **Access-kulcsok lehetőséget.** Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

## <a name="where-do-i-get-the-value-for---access-key"></a>Hol kapom meg `--access-key`az értéket?
Nyissa meg az Azure Portalt, és nyissa meg genomics-fiókját. A **Kezelés** fejlécalatt válassza az **Access-kulcsok lehetőséget.** Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

## <a name="why-do-i-need-two-access-keys"></a>Miért van szükségem két hozzáférési kulcsra?
Két hozzáférési kulcsra van szüksége abban az esetben, ha a szolgáltatás használatának megszakítása nélkül szeretné frissíteni (újragenerálni). Ha például frissíteni szeretné az első kulcsot, akkor az összes új munkafolyamatnak a második kulcsot kell használnia. Ezután várja meg, amíg az első kulccsal az összes munkafolyamat befejeződik, mielőtt frissítené az első kulcsot.

## <a name="do-you-save-my-storage-account-keys"></a>Menti a tárfiók kulcsait?
A tárfiók kulcs a Microsoft Genomics szolgáltatás rövid távú hozzáférési jogkivonatai létrehozásához használható a bemeneti fájlok olvasásához és a kimeneti fájlok írásához. A token alapértelmezett időtartama 48 óra. A token időtartama a `-sas/--sas-duration` küldési parancs lehetőségével módosítható; az érték órában van.

## <a name="what-genome-references-can-i-use"></a>Milyen genomreferenciákat használhatok?

Ezek a hivatkozások támogatottak:

 |Referencia              | Érték`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nincs alt analízis) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hogyan formázhatom a parancssori argumentumokat konfigurációs fájlként? 

Az msgen a következő formátumban ismeri meg a konfigurációs fájlokat:
* Minden beállítás kulcs-érték párként van megadva, a kulcsoktól kettősponttal elválasztott értékekkel.
  A program figyelmen kívül hagyja a szóközt.
* A rendszer `#` figyelmen kívül hagyja a kezdő vonalakat.
* A hosszú formátumú parancssori argumentumok kulcssá alakíthatók a vezető kötőjelek eltávolításával és a szavak közötti kötőjelek aláhúzásával. Íme néhány példa a konverziós példákra:

  |Parancssori argumentum            | Konfigurációs fájl sora |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:KULCS*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>További lépések

A Microsoft Genomics használatának első lépéseiaz alábbi forrásokból szerezhető be:
- Első lépéseként futasd le az első munkafolyamatot a Microsoft Genomics szolgáltatáson keresztül. [Munkafolyamat futtatása a Microsoft Genomics szolgáltatáson keresztül](quickstart-run-genomics-workflow-portal.md)
- Küldje el saját adatait a Microsoft Genomics szolgáltatás általi feldolgozásra: [párosított FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ vagy BAM](quickstart-input-multiple.md) 

