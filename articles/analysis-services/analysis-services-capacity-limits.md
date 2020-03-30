---
title: Az Azure Analysis Services erőforrás- és objektumkorlátai | Microsoft dokumentumok
description: Ez a cikk az Azure Analysis Services-kiszolgáló erőforrás- és objektumkorlátait ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573203"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services erőforrás- és objektumkorlátai

Ez a cikk az erőforrás- és modellobjektum-korlátokat ismerteti.

## <a name="tier-limits"></a>Szinthatárok

A fejlesztői, alapszintű és standard szintű csomagok QPU- és memóriakorlátait az [Azure Analysis Services díjszabási oldalán találja.](https://azure.microsoft.com/pricing/details/analysis-services/)

## <a name="object-limits"></a>Objektumkorlátok

Ezek a határok elméletiek. A teljesítmény alacsonyabb számban csökken.

|Objektum|Maximális méretek/számok|  
|------------|----------------------------|  
|Adatbázisok egy példányban|16000|  
|Táblák és oszlopok együttes száma az adatbázisban|16000|  
|Táblázat sorai|Korlátlan<br /><br /> **Figyelmeztetés:** Azzal a korlátozással, hogy a táblázat egyetlen oszlopa sem rendelkezhet 1 999 999 997 különböző értéknél több elévülési értékkel.|  
|Hierarchiák egy táblázatban|15,999|  
|Szintek a hierarchiában|15,999|  
|Kapcsolatok|8,000|  
|Kulcsoszlopok az összes táblázatban|15,999|  
|Intézkedések a táblázatokban|2^31-1 = 2 147 483 647|  
|Lekérdezés által visszaadott cellák|2^31-1 = 2 147 483 647|  
|A forráslekérdezés rekordmérete|64 Ezer|  
|Az objektumnevek hossza|512 karakter|  


