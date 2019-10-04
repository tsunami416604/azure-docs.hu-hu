---
title: Erőforrás-és objektum-korlátok Azure Analysis Servicesa | Microsoft Docs
description: Ismerteti Azure Analysis Services erőforrás-és objektum-korlátozásokat.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 40a5b68a12724f2574af19bb10c276c54c5afba0
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997586"
---
# <a name="analysis-services-resource-and-object-limits"></a>Erőforrás-és objektum-korlátok Analysis Services

Ez a cikk az erőforrás-és modell-objektumok korlátait ismerteti.

## <a name="tier-limits"></a>Rétegek korlátai

A fejlesztői, alapszintű és standard szintű QPU és a memória korlátozásait a [Azure Analysis Services díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/analysis-services/)tekintheti meg.

## <a name="object-limits"></a>Objektum korlátai

Ezek a korlátok elméletiak. A teljesítmény csökkenése alacsonyabb számokkal történik.

|Object|Maximális méretek/számok|  
|------------|----------------------------|  
|Példányban lévő adatbázisok|16,000|  
|Táblák és oszlopok együttes száma egy adatbázisban|16,000|  
|Táblázat sorai|Korlátlan<br /><br /> **Figyelmeztetés** Azzal a korlátozással, hogy a táblában egyetlen oszlop sem rendelkezhet több mint 1 999 999 997 különböző értékkel.|  
|Hierarchiák egy táblában|15 999|  
|Hierarchia szintjei|15 999|  
|Kapcsolatok|8,000|  
|A kulcsok oszlopai az összes táblában|15 999|  
|Mértékek a táblákban|2 ^ 31-1 = 2 147 483 647|  
|Lekérdezés által visszaadott cellák|2 ^ 31-1 = 2 147 483 647|  
|A forrás-lekérdezés rekordjának mérete|64 K|  
|Az objektumok neveinek hossza|512 karakter|  


