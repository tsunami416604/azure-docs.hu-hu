---
title: Válassza ki a megfelelő tarifacsomagot | Microsoft Azure Maps
description: Ebben a cikkben a Microsoft Azure Maps által kínált tarifacsomagokról olvashat.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335660"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Válassza ki a megfelelő tarifacsomagot az Azure Mapsben

Az Azure Maps két tarifacsomagot kínál, az S0 és az S1. A cikk célja, hogy segítsen kiválasztani az igényeinek megfelelő tarifacsomagot. A megfelelő tarifacsomag kiválasztásához tegye fel magának a következő két kérdést.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hány egyidejű felhasználót tervezek támogatni? 
Az S0 és S1 tarifacsomagok különböző mennyiségű adatátviteli szintet kezelnek. Az S0 tarifacsomag másodpercenként legfeljebb **50 lekérdezést**kezel. Míg az S1 réteg **másodpercenként több mint 50 lekérdezést**kezel.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Milyen térinformatikai képességeket tervezek használni?
Az S0 tarifacsomag megfelelő az Ön számára, ha az alapvető térinformatikai API-k megfelelnek a szolgáltatási követelményeknek. Ha azt szeretné, hogy az alkalmazás fejlettebb képességek, fontolja meg az S1 tarifacsomag választása. A speciális képességek a következők: Légi felvétel és hibrid képek, útvonaltartomány és kötegelt geokódolás. Tekintse át a **tarifacsomag-képességek** táblázatot az alkalmazásnak leginkább megfelelő tarifacsomag kiválasztásához.

### <a name="pricing-tier-capabilities"></a>Tarifacsomag-képességek

| Képesség                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Leképezési renderelés                              | ✓                   | ✓       |
| Műholdképek                       |                     | ✓        |
| Keresés                                  | ✓                    | ✓        |
| Kötegelt keresés                            |                     | ✓        |
| Útválasztás                                   | ✓                    |✓        |
| Kötegelt műveletvezetés                            |                    | ✓        |
| Mátrix-útválasztás                          |                     | ✓        |
| Útvonaltartomány (Iochronok)                |                     | ✓        |
| Adatforgalom                                |✓                    |✓        |
| Időzóna                               |✓                    |✓        |
| Földrajzi helymeghatározás (előzetes verzió)                    |✓                   |✓        |
| Térbeli műveletek                        |                    |✓        |
| Geokerítések kezelése                                |                    |✓        |
| Azure Maps-adatok (előzetes verzió)                |                     | ✓        |
| Mobilitás (előzetes verzió)                       |                     | ✓        |
| Időjárás (előzetes verzió)                        |✓                    |✓        |

Tekintsük ezeket a további pontokat:
* Milyen típusú vállalkozása van?
* Mennyire kritikus az alkalmazás?

### <a name="pricing-tier-targeted-customers"></a>Tarifaszintcélzott ügyfelek

Tekintse meg a **tarifacsomag célzott ügyfelek** táblázat, hogy jobban megismerjék az S0 és S1 tarifacsomagok. További információ: [Azure Maps díjszabás.](https://azure.microsoft.com/pricing/details/azure-maps/) 

| Tarifacsomag  |     Megcélzott ügyfelek                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Az S0 tarifacsomag az alkalmazások számára működik az éles környezet minden szakaszában: a koncepció igazolásától és a korai szakaszban történő teszteléstől az alkalmazás élesg és üzembe helyezéséig. Ez a szint azonban kis léptékű fejlesztésre, vagy alacsony egyidejű felhasználókkal rendelkező ügyfelekre, vagy mindkettőre készült. <p>|
| S1            |    <p>Az S1 tarifacsomag nagyméretű nagyvállalati alkalmazásokkal, kritikus fontosságú alkalmazásokkal vagy nagy mennyiségű egyidejű felhasználóval rendelkező ügyfelek számára készült. Ez azoknak az ügyfeleknek is szól, akik fejlett térinformatikai szolgáltatásokat igényelnek.</p>|

## <a name="next-steps"></a>További lépések

További információ a tarifacsomagok megtekintéséről és módosításáról:

> [!div class="nextstepaction"] 
> [Tarifacsomag kezelése](how-to-manage-pricing-tier.md)
