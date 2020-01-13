---
title: Válassza ki a megfelelő árképzési szintet | Microsoft Azure térképek
description: Ebben a cikkben a Microsoft Azure Maps által kínált díjszabási csomagokat ismerheti meg.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911751"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Válassza ki a megfelelő díjszabási szintet Azure Maps

Az Azure Maps két tarifacsomaggal rendelkezik. Ennek a cikknek a célja, hogy segítséget nyújtson az igényeinek megfelelő díjszabási csomag kiválasztásában. A megfelelő árképzési szint kiválasztásához forduljon a következő két kérdéshez.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Milyen térinformatikai funkciókat kívánok használni?
A S0 díjszabási szintje abban az esetben megfelelő, ha az alapszintű Térinformatikai API-k megfelelnek a szolgáltatás követelményeinek. Ha fejlettebb funkciókat szeretne használni az alkalmazáshoz, érdemes megfontolnia az S1 árképzési szintet. Ilyenek például a funkciók, valamint a hibrid képek, az útvonal-tartomány és a Batch-helymeghatározáshoz. A következő **díjszabási szintű képességek** táblázata jobb ötletet nyújt az alkalmazás igényeinek. Emellett az alkalmazás számára legmegfelelőbb árképzési szintet is kiválaszthatja.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hány egyidejű felhasználót tervezek támogatni? 
A S0 és S1 díjszabási szintjei különböző mennyiségű adatátviteli sebességet kezelnek. Mielőtt kiválaszt egy Azure Maps árképzési szintet, kérdezzen rá néhány kérdést. Például: "hány egyidejű felhasználót szeretnék támogatni?" A S0 díjszabási szintje másodpercenként legfeljebb **50 lekérdezést**kezel. Az S1 díjszabási szintje **másodpercenként több mint 50 lekérdezést**kezel.

### <a name="pricing-tier-capabilities"></a>Díjszabási csomag képességei

| Szolgáltatás                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Keresés (fwd/Rev helymeghatározáshoz, érdekes pontok)  |        ✓           |     ✓    |
| Batch-helymeghatározáshoz (előzetes verzió)              |                   |     ✓    |
| Sokszögek a keresésből          |                   |     ✓    |
| Útvonaltervezés                                 |        ✓           |     ✓    |
| Útvonal tartománya                    |                   |     ✓    |
| Batch-útválasztás (előzetes verzió)                |                   |     ✓    |
| Matrix Routing (előzetes verzió)               |                   |     ✓    |
| Megjelenítés                                  |        ✓           |     ✓    |
| Képek és hibrid képek    |            |     ✓    |
| Forgalom                                 |        ✓           |     ✓    |
| Időzónák                              |        ✓           |     ✓    |
| Földrajzi hely (előzetes verzió)                |        ✓           |     ✓    |
| Adatértékek (előzetes verzió)               |                   |     ✓    |
| Térbeli (előzetes verzió)               |                   |     ✓    |
| Geokerítések (előzetes verzió)               |                   |     ✓    |



Ezeket a további adatpontokat érdemes figyelembe venni:
* Milyen típusú vállalatoknak van?
* Mennyire fontos az alkalmazás építése?

Tekintse meg a **díjszabási szinten megcélozott ügyfelek** táblázatát, hogy jobban érthető legyen a S0 és az S1 díjszabása. További információ: [Azure Maps díjszabása](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Árképzési szintek megcélzó ügyfelei

| Díjcsomag  |     Megcélzó ügyfelek                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>A S0 díjszabási szintje olyan ügyfelek számára készült, akik kis-és közepes méretű vállalatok. Ez a megfelelő díjszabási csomag, ha nem vár nagy mennyiségű egyidejű felhasználót. Emellett akkor is helyes, ha az előző táblázatban látható alapvető Térinformatikai API-k megfelelnek a szolgáltatás követelményeinek. Ez a szintet általánosan elérhető. Az alkalmazás az éles környezet összes fázisában működik: a megvalósíthatósági fejlesztéstől és a korai teszteléstől az alkalmazások éles és üzembe helyezéséhez.<p>|
| S1            |    <p>Az S1 díjszabási szintje olyan ügyfelek számára szükséges, akik nagyvállalati, kritikus fontosságú alkalmazások vagy nagy mennyiségű egyidejű felhasználó támogatására szorulnak. Azok az ügyfelek is, akik speciális térinformatikai szolgáltatásokat igényelnek.</p>|

## <a name="next-steps"></a>Következő lépések

További információ a díjszabási szintek megtekintéséről és módosításáról:

> [!div class="nextstepaction"] 
> [Árképzési szintek kezelése](how-to-manage-pricing-tier.md)
