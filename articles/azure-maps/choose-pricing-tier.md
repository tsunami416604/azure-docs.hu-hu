---
title: Válassza ki a tarifacsomagot az Azure Maps jobb |} A Microsoft Docs
description: További tudnivalók az Azure Maps által kínált tarifacsomagok
author: walsehgal
ms.author: v-musehg
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 20c59f95dcdb47ef3e457f177d7ad1a300981907
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855965"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>A jobb oldalon, az Azure Maps-tarifacsomag kiválasztása

Az Azure Maps két tarifacsomag kínál. Ez a cikk célja segítségével válassza ki a tarifacsomagot az igényeinek jobb. Válassza ki a tarifacsomagot jobb érdekében tegye fel magának két kérdéseket:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Milyen térinformatikai funkciókat tervezhetem használni?
Ha úgy érzi, hogy a core API-k térinformatikai teljesíti-e a követelményei, akkor a S0 tarifacsomagot az Önnek megfelelő. Speciális képességek az alkalmazás közötti + hibrid képeken például azt szeretné, ha az útválasztási tartomány, az első batch-geokódolás stb., vegye figyelembe, térségen az S1 tarifacsomagot. Az alábbi táblázatban **szolgáltatásszintek lehetőségei díjszabás** fog biztosítanak az alkalmazás igényeinek jobban megismerhesse és azt is választja, a tarifacsomag az alkalmazásához leginkább megfelelő súgó.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Egyidejű felhasználók tervezhetem támogatásához? 
S0 és S1 szintű tarifacsomagban is kapható különböző mennyiségű adat kezelésére is alkalmas. Az Azure Maps tarifacsomag kiválasztása, előtt gondolja át, a kérdés hasonló kérdéseket, hogy számos egyidejű felhasználót szeretne biztosítani? Akár kezelhetik a tarifacsomag az S0 **50 lekérdezések másodpercenkénti** és az S1 tarifacsomagot képes kezelni **másodpercenként több mint 50 lekérdezések**.

### <a name="pricing-tier-capabilities"></a>Díjszabási szint képességek

| Képesség                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Keresés                                  |        ✓           |     ✓    |
| Útválasztás                                 |        ✓           |     ✓    |
| Megjelenítés                                  |        ✓           |     ✓    |
| Adatforgalom                                 |        ✓           |     ✓    |
| Időzóna                              |        ✓           |     ✓    |
| Képeken + hibrid képeken (előzetes verzió)      |                    |     ✓    |
| Útválasztási tartomány (előzetes verzió)                   |                    |     ✓    |
| IP-2 helye (előzetes verzió)                 |                    |     ✓    |
| Sokszög keresés (előzetes verzió)          |                    |     ✓    |
| A Batch Geokódolás (előzetes verzió)               |                    |     ✓    |
| Batch-útválasztás (előzetes verzió)                 |                    |     ✓    |
| Mátrix útválasztás (előzetes verzió)                |                    |     ✓    |


Néhány további adatpontokat mellett szóló érvek, milyen típusú vállalati rendelkezik, vagy hogyan kritikus fontosságú az alkalmazás a készülő?

Lásd a táblázatot az **tarifacsomag ügyfelek célzott** jobban érti az S0 és S1 szintű tarifacsomagban is kapható beolvasásához. A díjszabás az Azure Maps, további információt talál, [Azure Maps szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>A tarifacsomag megcélzott felhasználók

| Tarifacsomag  |        Megcélzott felhasználók                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Az S0 tarifacsomag ügyfelek esetében, akik kis és közepes méretű vállalkozások van. A tarifacsomag az Ön számára, ha nem várható, és az egyidejű felhasználók nagy mennyiségű és szolgáltatás igényeinek teljesíti-e a core API-k, amint az alábbi táblázat azt a földrajzi jobb legyen. Ez a szint általánosan elérhető és alkalmazható alkalmazások az összes éles környezetben a megvalósítási próbától fogalom fejlesztési fázisában és az alkalmazás éles és a központi telepítés tesztelése korai szakaszában.<p>|
| S1            |    <p>Az S1 tarifacsomagot nagy méretű nagyvállalati, alapvető fontosságú alkalmazásokat, és az egyidejű felhasználók nagy mennyiségű támogatása ellenőrzést igénylő ügyfelek számára, vagy speciális térinformatikai szolgáltatások igényel.</p>|


## <a name="next-steps"></a>További lépések

További információk megtekintéséhez és a tarifacsomag módosítása:

> [!div class="nextstepaction"]
> [Tarifacsomag kezelése](how-to-manage-pricing-tier.md)