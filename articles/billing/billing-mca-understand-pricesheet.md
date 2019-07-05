---
title: A feltételek az árlista megismerheti a Microsoft vevői szerződés – Azure
description: Útmutató a Microsoft vevői szerződés a használatának és számlájának értelmezése.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490665"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>A Microsoft Ügyfélszerződéséhez árlista feltételek

Ez a cikk a Microsoft vevői szerződés vonatkozik Azure-számlázási fiókba. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

Ha Ön a tulajdonos, közreműködő, olvasó vagy számla ellenében Manager számlázási profilja letöltheti a szervezet árlista az Azure Portalról. Lásd: [megtekintése és letöltése a szervezet céljai díjszabás](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Feltételek és az árlista leírása

Az alábbi szakasz ismerteti a Microsoft Ügyfélszerződéséhez árlista látható a fontos fogalmakat.

| **Mező neve**   | **Leírás**   |
| --- | --- |
| billingAccountId  | A számlázási fiók egyedi azonosítója.   |
| billingAccountName  | Számlázási fiók nevét.  |
| billingProfileId  | A számlázási profil egyedi azonosítója.   |
| billingProfileName  | A számlák fogadására beállított számlázási profil neve. Az árlistán szereplő árak a számlázási-profilhoz társítva. |
| productOrderName  | A megvásárolt termékek csomag neve. |
| serviceFamily  | Az Azure szolgáltatás típusa. Például: Elemzési, számítási biztonság |
| Product  | A díjakat halmoz fel a termék nevét. Például: Basic SQL DB vs Standard SQL DB  |
| productId  | A termék, amelynek mérőszám felhasznált egyedi azonosítója. |
| UnitOfMeasure  | A szolgáltatás a számlázási mértékegység azonosítja. Például a számítási szolgáltatások díjszabása óradíjalapú. |
| meterId  | A mérő egyedi azonosítója. |
| MeterName  | A mérő neve. Az érték jelzi az Azure-szolgáltatás üzembe helyezhető erőforrásra. |
| MeterCategory  | A mérőszám a besorolási kategória neve. Ha például _Cloud services_, _hálózatkezelés_stb. |
| meterType  |  A mérőműszer típusának neve. |
| meterSubCategory  | A mérőszám alárendelt besorolási kategória neve.  |
| MeterRegion  | Neve az a régió, ahol a szolgáltatás a fogyasztásmérő érhető el. Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.    |
| tierId  | Azonosítja a tarifacsomagot, ha alkalmazható. Ez a beállítás rétegzett árak amikor díjak alapján a felhasznált kapacitásegységek száma tierMinimumUnits együtt működik.    |
| tierMinimumUnits  | Határozza meg az alsó határ értékének a réteg tartomány, amelyhez árak vannak definiálva. Például ha a tartomány 0 és 100 közötti, tierMinimumUnits lehet 0.  |
| effectiveStartDate  | Kezdő dátum, amikor az ár életbe lép. |
| effectiveEndDate  | A tényleges árat záró dátuma. |
| Egységár  | Ár / egység (nem a tényleges erősségét fejezi ki ár) számlázási időpontjában, adott sorrendben mérőszám és a termék nevét.  Megjegyzés: Az ár a lehetőség nem azonos szolgáltatásszinten különbözeti árak szolgáltatások esetén letölti az hatékony árát a használat részleteiről.  Esetén a díjszabás a többrétegű szolgáltatások a tényleges árat erősségét fejezi ki arány szolgáltatásszinten, és nem jelenik meg a vonatkozó szint egységárat. A kevert ára vagy hatékony ára (ahol minden szint tartalmaz egy adott egységár) több szolgáltatásszinten átfedés felhasznált mennyiség nettó ára. |
| basePrice  | A ár időpontjában az ügyfél bejelentkezik, vagy ha a bejelentkezés után elindítja a időben ár szolgáltatás mérőszám.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Megtekintheti és letöltheti a szervezet díjszabása](billing-ea-pricing.md)
