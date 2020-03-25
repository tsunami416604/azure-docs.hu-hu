---
title: A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések – Azure
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a Microsoft-ügyfélszerződésre vonatkozó használati adatok és a hozzá tartozó számlák.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1e776e351fe723f0fbc5b1d6a8f5593a9fc975b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199619"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>A Microsoft-ügyfélszerződés árlistáján szereplő kifejezések

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó Azure számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

Ha Ön egy számlázási profil tulajdonosa, közreműködője, olvasója vagy számlakezelője, letöltheti a szervezet árlistáját az Azure Portalról. Lásd [A cég vagy intézmény díjszabásának megtekintése és letöltése](ea-pricing.md) című részt.

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Az árlistában szereplő kifejezések és leírásaik

A következő szakasz ismerteti a Microsoft-ügyfélszerződés árlistáján szereplő fontos kifejezéseket.

| **Mezőnév**   | **Leírás**   |
| --- | --- |
| basePrice  | Az ügyfél bejelentkezésekor vagy a szolgáltatásmérő elindításakor (ha ez a bejelentkezés után történik) aktuális piaci ár.   |
| billingAccountId  | A számlázási fiók egyedi azonosítója.   |
| billingAccountName  | A számlázási fiók neve.  |
| billingCurrency | Az a pénznem, amelyben a díjakat közzétesszük. |
| billingProfileId  | A számlázási profil egyedi azonosítója.   |
| billingProfileName  | Annak a számlázási profilnak a neve, amely a számlák fogadására lett beállítva. Az árlista árai ehhez a számlázási profilhoz vannak társítva. |
| currency | Az a pénznem, amelyben az árak megjelennek. |
| discount | A fokozatos szintbeállításért, az ingyenes szintért és a szolgáltatási keretért járó árkedvezmények, valamint a kialkudott kedvezmények, ha vannak. Százalékos értékként jelenik meg. |
| effectiveEndDate  | Az ár érvényességi idejének záródátuma. |
| effectiveStartDate  | Az ár érvényességi idejének kezdődátuma. |
| includedQuantity | Egy adott szolgáltatás azon mennyisége, amennyit az ügyfél növekményes költségek nélkül jogosult felhasználni. |
| marketPrice | Egy adott szolgáltatás aktuális, érvényes piaci ára. |
| meterId  | A fogyasztásmérő egyedi azonosítója. |
| meterCategory  | A fogyasztásmérő osztályozási kategóriájának neve. Például _Felhőszolgáltatások_, _Hálózat_ stb. |
| meterName  | A fogyasztásmérő neve. A fogyasztásmérő egy Azure-szolgáltatás üzembe helyezhető erőforrását jelöli. |
| meterSubCategory  | A fogyasztásmérő osztályozási alkategóriájának neve.  |
| meterType  |  A fogyasztásmérő típusának neve. |
| meterRegion  | Azon régió neve, ahol a szolgáltatáshoz tartozó fogyasztásmérő elérhető. Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.    |
| Product  | Annak a terméknek a neve, amelyre vonatkozóan a díjak keletkeznek. Pl.: Basic SQL DB vagy Standard SQL DB  |
| productId  | Annak a terméknek az egyedi azonosítója, amelynek fogyasztásmérője igénybe lett véve. |
| productOrderName  | A megvásárolt termékcsomag neve. |
| serviceFamily  | Az Azure-szolgáltatás típusa. Például: Compute, Analytics, Security |
| tierMinimumUnits  | Meghatározza a szintek azon tartományának alsó határát, amelyek alapján az árakat meghatározták. Ha például a tartomány 0-tól 100-ig terjed, a tierMinimumUnits 0 lenne.  |
| unitOfMeasure  | A szolgáltatás számlázásának mértékegységét adja meg. A számítási szolgáltatások számlázása például óraalapú. |
| unitPrice  | A számlázás időpontjában érvényes egységár (nem a tényleges kevert ár), amely egy fogyasztásmérőre és egy termékrendelésnévre vonatkozik.  Megjegyzés: Az egységár nem azonos a használat részleteit tartalmazó letöltött adatokban szereplő érvényes árral olyan szolgáltatások esetén, amelyek eltérő díjszabással rendelkeznek a különböző szinteken.  A többszintes árképzésű szolgáltatások esetében a tényleges díj a különböző szintekre vonatkozó kevert összeg, és nem jelenít meg az egyes szintekre vonatkozó egységárat. A kevert ár vagy a tényleges ár a felhasznált mennyiség több szintre kiterjedő nettó díja (ahol minden egyes szinthez adott egységár tartozik). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A cég vagy intézmény díjszabásának megtekintése és letöltése](ea-pricing.md)
