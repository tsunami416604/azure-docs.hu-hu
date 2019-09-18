---
title: A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések értelmezése – Azure
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a Microsoft-ügyfélszerződésre vonatkozó használati adatok és a hozzá tartozó számlák.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490665"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó Azure számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

Ha Ön egy számlázási profil tulajdonosa, közreműködője, olvasója vagy számlakezelője, letöltheti a szervezet árlistáját az Azure Portalról. Lásd [A cég vagy intézmény díjszabásának megtekintése és letöltése](billing-ea-pricing.md) című részt.

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Az árlistában szereplő kifejezések és leírásaik

A következő szakasz ismerteti a Microsoft-ügyfélszerződés árlistáján szereplő fontos kifejezéseket.

| **Mezőnév**   | **Leírás**   |
| --- | --- |
| billingAccountId  | A számlázási fiók egyedi azonosítója.   |
| billingAccountName  | A számlázási fiók neve.  |
| billingProfileId  | A számlázási profil egyedi azonosítója.   |
| billingProfileName  | Annak a számlázási profilnak a neve, amely a számlák fogadására lett beállítva. Az árlista árai ehhez a számlázási profilhoz vannak társítva. |
| productOrderName  | A megvásárolt termékcsomag neve. |
| serviceFamily  | Az Azure-szolgáltatás típusa. Például: Compute, Analytics, Security |
| Product  | Annak a terméknek a neve, amelyre vonatkozóan a díjak keletkeznek. Pl.: Basic SQL DB vagy Standard SQL DB  |
| productId  | Annak a terméknek az egyedi azonosítója, amelynek fogyasztásmérője igénybe lett véve. |
| unitOfMeasure  | A szolgáltatás számlázásának mértékegységét adja meg. A számítási szolgáltatások számlázása például óraalapú. |
| meterId  | A fogyasztásmérő egyedi azonosítója. |
| meterName  | A fogyasztásmérő neve. A fogyasztásmérő egy Azure-szolgáltatás üzembe helyezhető erőforrását jelöli. |
| meterCategory  | A fogyasztásmérő osztályozási kategóriájának neve. Például _Felhőszolgáltatások_, _Hálózat_ stb. |
| meterType  |  A fogyasztásmérő típusának neve. |
| meterSubCategory  | A fogyasztásmérő osztályozási alkategóriájának neve.  |
| meterRegion  | Azon régió neve, ahol a szolgáltatáshoz tartozó fogyasztásmérő elérhető. Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.    |
| tierId  | Megadja a tarifacsomagot, ha van ilyen. A rendszer ezt a tierMinimumUnits értékével együtt használja a többszintű árképzés beállításakor, amikor az árak a felhasznált egységek számától függően változnak.    |
| tierMinimumUnits  | Meghatározza a szintek azon tartományának alsó határát, amelyek alapján az árakat meghatározták. Ha például a tartomány 0-tól 100-ig terjed, a tierMinimumUnits 0 lenne.  |
| effectiveStartDate  | Az ár érvényességi idejének kezdődátuma. |
| effectiveEndDate  | Az ár érvényességi idejének záródátuma. |
| unitPrice  | A számlázás időpontjában érvényes egységár (nem a tényleges kevert ár), amely egy fogyasztásmérőre és egy termékrendelésnévre vonatkozik.  Megjegyzés: Az egységár nem azonos a használat részleteit tartalmazó letöltött adatokban szereplő érvényes árral olyan szolgáltatások esetén, amelyek eltérő díjszabással rendelkeznek a különböző szinteken.  A többszintes árképzésű szolgáltatások esetében a tényleges díj a különböző szintekre vonatkozó kevert összeg, és nem jelenít meg az egyes szintekre vonatkozó egységárat. A kevert ár vagy a tényleges ár a felhasznált mennyiség több szintre kiterjedő nettó díja (ahol minden egyes szinthez adott egységár tartozik). |
| basePrice  | Az ügyfél bejelentkezésekor vagy a szolgáltatásmérő elindításakor (ha ez a bejelentkezés után történik) aktuális piaci ár.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A cég vagy intézmény díjszabásának megtekintése és letöltése](billing-ea-pricing.md)
