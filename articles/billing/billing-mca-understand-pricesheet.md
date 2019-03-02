---
title: A feltételek az árlista megismerheti a Microsoft vevői szerződés – Azure |} A Microsoft Docs
description: Ismerje meg a használati és az Azure-előfizetés számlájának értelmezése
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 96a2281368b7a2180dbe2136a253a8b1a13a8731
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248943"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>A Microsoft vevői szerződés feltételeit az árlista ismertetése

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

Ha Ön a tulajdonos, közreműködő, olvasó vagy számla ellenében Manager számlázási profilja letöltheti a szervezet árlista az Azure Portalról. Lásd: [megtekintése és letöltése a szervezet céljai díjszabás](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Részletes használati és a Microsoft Ügyfélszerződéséhez árlista leírása

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
| unitOfMeasure  | A szolgáltatás a számlázási mértékegység azonosítja. Például a számítási szolgáltatások díjszabása óradíjalapú. |
| meterId  | A mérő egyedi azonosítója. |
| meterName  | A mérő neve. Az érték jelzi az Azure-szolgáltatás üzembe helyezhető erőforrásra. |
| meterCategory  | A mérőszám a besorolási kategória neve. Ha például _Cloud services_, _hálózatkezelés_stb. |
| meterType  |  A mérőműszer típusának neve. |
| meterSubCategory  | A mérőszám alárendelt besorolási kategória neve.  |
| meterRegion  | Neve az a régió, ahol a szolgáltatás a fogyasztásmérő érhető el. Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.    |
| tierId  | Azonosítja a tarifacsomagot, ha alkalmazható. Ez a beállítás rétegzett árak amikor díjak alapján a felhasznált kapacitásegységek száma tierMinimumUnits együtt működik.    |
| tierMinimumUnits  | Határozza meg az alsó határ értékének a réteg tartomány, amelyhez árak vannak definiálva. Például ha a tartomány 0 és 100 közötti, tierMinimumUnits lehet 0.  |
| effectiveStartDate  | Kezdő dátum, amikor az ár életbe lép. |
| effectiveEndDate  | A tényleges árat záró dátuma. |
| Egységár  | Ár / egység (nem a tényleges erősségét fejezi ki ár) számlázási időpontjában, adott sorrendben mérőszám és a termék nevét.  Megjegyzés: Az ár a lehetőség nem azonos szolgáltatásszinten különbözeti árak szolgáltatások esetén letölti az hatékony árát a használat részleteiről.  Esetén a díjszabás a többrétegű szolgáltatások a tényleges árat erősségét fejezi ki arány szolgáltatásszinten, és nem jelenik meg a vonatkozó szint egységárat. A kevert ára vagy hatékony ára (ahol minden szint tartalmaz egy adott egységár) több szolgáltatásszinten átfedés felhasznált mennyiség nettó ára. |
| basePrice  | A ár időpontjában az ügyfél bejelentkezik, vagy ha a bejelentkezés után elindítja a időben ár szolgáltatás mérőszám.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Megtekintheti és letöltheti a szervezet díjszabása](billing-ea-pricing.md)
