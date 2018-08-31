---
title: Azure-használati API – áttekintés |} A Microsoft Docs
description: Ismerje meg, hogyan Azure szolgáltatáshasználati API-jai, programozás alapú hozzáférést biztosít költségek és az Azure-erőforrások használati adatait.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 88b7909e78f8bd36ce456eee60587acbbb94b6cd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286748"
---
# <a name="azure-consumption-api-overview"></a>Azure-használati API – áttekintés 

Az Azure szolgáltatáshasználati API-jai programozás alapú hozzáférést költségeihez és használati adatok biztosítanak az Azure-erőforrások. Ezen API-k jelenleg csak támogatja a vállalati Belépéseket és a Web Direct-előfizetésekhez (néhány kivételtől eltekintve). Az API-kat folyamatosan frissülnek, hogy más típusú Azure-előfizetések támogatják.

Az Azure szolgáltatáshasználati API-jai hozzáférést biztosítanak:
- Enterprise és a webes közvetlen ügyfelek 
    - Használat részletei 
    - Piactér-díjak 
    - Foglalás javaslatok 
    - Foglalás részletei 
    - Foglalás összefoglaló 
- Csak a vállalati felhasználók 
    - Árlista 
    - Költségkeret 
    - Egyenlegek 

## <a name="usage-details-api"></a>Használat részletei API

A használati részletei API használatával az összes Azure-ban ingyenesen és használati adatok lekérése 1. fél erőforrásokat. Információk el vannak jelenleg kibocsátott egyszer mérni / erőforrás / nap / használati részletes rekordok formájában. Adatok használhatók fel a költségeket az összes erőforrás között hozzáadásához, vagy vizsgálja meg a költségek és az adott erőforrás(ok) használat. 

Az API-t tartalmazza:

-   **Mérőszám szintű használati adatokat** – beleértve a felhasználási költség, a díjat úgy mérőszám az adatok megjelenítése és milyen Azure-erőforrás a díj vonatkozik. Képezze le az összes részletes rekordok napi kérelemegységeket.
-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** -Trim az API-eredményéhez körét az alábbi szűrők használatával részletes rekordok értékre:
    - Használat záró / használati indítása
    - Erőforráscsoport
    - Erőforrás neve
-   **Adatösszesítés** -használat OData-kifejezések hajtja végre összesített használatról címkék alapján, vagy szűrő tulajdonságai
-   **A különböző típusú használati** -használat részletes adatai jelenleg a vállalati és a Web Direct ügyfeleink számára érhető el.

További információkért lásd: a műszaki előírása a [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Piactér-díjak API

A piactér díjak API segítségével díj és használati adatok beolvasása a Marketplace-erőforrások (Azure 3. fél ajánlatok). Ezen adatok segítségével költségek összeadódhatnak minden Marketplace-erőforrások között, vagy vizsgálja meg a költségek és az adott erőforrás(ok) használat. 

Az API-t tartalmazza:

-   **Mérőszám szintű használati adatokat** – beleértve a piactér-felhasználási költség, a díjat úgy mérőszám az adatok megjelenítése és milyen erőforrást a díj vonatkozik. Képezze le az összes részletes rekordok napi kérelemegységeket.
-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** -Trim az API-eredményéhez kisebb rekordkészletet marketplace használatával a következő szűrőket értékre:
    - Használat kezdő / záró használat
    - Erőforráscsoport
    - Erőforrás neve
-   **A különböző típusú használati** -Piactérrel kapcsolatos információkat jelenleg a vállalati és a Web Direct ügyfeleink számára érhető el.

További információkért lásd: a műszaki előírása a [Marketplace-díjakat API](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Egyenlegek API

Nagyvállalati ügyfeleink egy-egy havi információk a jóváírásokat összefoglaló balances, új vásárlások, Azure Marketplace szolgáltatási díjai, módosítását és kerettúllépési díjfizetést tesz szükségessé összefoglalójának lekérése a elosztja a API használatával. Ez az aktuális elszámolási időszakban vagy bármelyik az elmúlt időszakban információkat szerezhet a. Vállalatok számára az adatok hajthat végre kézzel számított összefoglaló díjak összehasonlítása. Ez az API nem biztosít erőforrás-specifikus információkat és a egy összesített nézetet a költségek. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** Ez az API csak érhető el nagyvállalati szerződéssel rendelkező ügyfelek. 
    - Ügyfelek az API meghívásához vállalati rendszergazdai engedélyekkel kell rendelkeznie. 

További információkért lásd: a műszaki előírása a [jóváírásokat összefoglaló Balances API](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Költségvetés API

Vállalati ügyfelek használhatják az API erőforrások, erőforráscsoportok vagy a számlázási mérőszámok költség- és használati költségvetés létrehozására. Ez az információ meghatározása után riasztási beállítható úgy, hogy értesítése, ha a felhasználó által definiált költségvetés küszöbérték túllépése. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** – Ez az API csak érhető el nagyvállalati szerződéssel rendelkező ügyfelek.
-   **Konfigurálható értesítések** – adja meg a felhasználó a költségvetés azokat van kioldják értesítést.
-   **Használat vagy a költség-alapú költségvetése** – a költségvetés alapján használatalapú vagy a költségek igényei szerint a forgatókönyv létrehozásához.
-   **Szűrés** – a költségvetés egy kisebb részhalmazra használatával a következő konfigurálható szűrők erőforrások szűrése
    - Erőforráscsoport
    - Erőforrás neve
    - Forgalmi díj
-   **Konfigurálható költségvetés időtartományra** – adja meg, milyen gyakran a költségvetés vissza kell állítania és mennyi ideig a költségvetés érvényes.

További információkért lásd: a műszaki előírása a [költségvetése API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Foglalás javaslatok API

Ez az API segítségével Reserved VM Instances megvásárlásával kapcsolatos javaslatokat kaphat. Javaslatok célja, hogy ügyfeleink várt költségmegtakarítást elemzése és beszerzési összegeket. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** -testre szabni a javaslat eredmény a következő szűrők használatával:
    - Hatókör
    - Lookback időszak
-   **Foglalási adatokat a különböző típusú** -foglalási információkat jelenleg a vállalati és a Web Direct ügyfeleink számára érhető el.

További információkért lásd: a műszaki előírása a [foglalás ajánlatok API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Foglalás részletei API

A Foglalás részletei API adatai megjelenítéséhez a korábban beszerzett virtuális gép foglalások például mennyi fogyasztás van fenntartva és mennyi használja ténylegesen használatban van. Láthatja az adatokat a virtuális gépenkénti részletes szintű. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** -Trim az API eredményhalmazt kisebb körét a foglalások használatával a következő szűrőt:
    - Dátumtartomány
-   **Foglalási adatokat a különböző típusú** -foglalási információkat jelenleg a vállalati és a Web Direct ügyfeleink számára érhető el.

További információkért lásd: a műszaki előírása a [foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Foglalás összegzéseket API

Használja az API korábban összesített információinak megtekintéséhez vásárolt virtuális gép foglalások például mennyi felhasználási és mennyi ténylegesen használatban van az összesítésben van fenntartva. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** -testre szabni az eredmények a következő szűrő a napi aggregációs időköze használata esetén:
    - Usage Date (Használat dátuma)
-   **Foglalási adatokat a különböző típusú** -foglalási információkat jelenleg a vállalati és a Web Direct ügyfeleink számára érhető el.
-   **A napi és havi összesítések** – Hívóit adhatja meg szeretnének-e a Foglalás állapotösszegzőjének adatai a napi és havi bontásban.

További információkért lásd: a műszaki előírása a [foglalás összegzéseket API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Ár lap API
Nagyvállalati ügyfeleinek beolvasni az egyéni díjszabás minden mérőszámok az API használatával. Vállalatok ezzel együtt használati adatait és a piactér használati adatok-használat és a piactér adatok számítások végrehajtásához. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálása hozzáférési házirendek a a [az Azure portal](https://portal.azure.com), a [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások révén ingyenesen juthatnak az előfizetés használati adatokat. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** – Ez az API csak érhető el nagyvállalati szerződéssel rendelkező ügyfelek. Díjszabási a Web Direct ügyfelei számára a RateCard API-t használjon. 

További információkért lásd: a műszaki előírása a [ár lap API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Forgatókönyvek

Néhány lehetséges a fogyasztói API-n keresztül végzett forgatókönyv:

-   **Egyeztetés a számlához** -fejeződött Microsoft díja szerint számítjuk fel velem a megfelelő összeget?  Mi az a számlán, és szeretnék kiszámíthatja, saját magam?
-   **Adatbázisközi díjak** – most, hogy tudom, hogy mennyi vagyok éppen fizetnem, akik szervezeti kell fizetnem kell?
-   **Költségek optimalizálása** -tudom, hogy mennyivel korábban lett fizetnem... Hogyan kaphatok további kívül az Azure-beli töltök pénzt?
-   **Költségek nyomon követése** -mennyi vagyok kiadások és az Azure idővel látni. Mik azok a trendek? Hogyan sikerült I foglalják jobban?
-   **Azure-költések kezelése a hónap során** -Date mennyi van az aktuális hónap a költségek? Kell végezze el a módosításokat a költségkeret-beállítási és/vagy az Azure használatát? Ha a hónap során vagyok I használó Azure a legtöbbet?
-   **Riasztásokat állíthat be** – szeretnék kapni, állítsa be a használatalapú erőforrás-alapú vagy költségvetési-alapú pénzügyi riasztások alapján.

## <a name="next-steps"></a>További lépések

- További információ az Azure számlázási API-k használatával programozott módon juthat az Azure-használat: [Azure Billing API – áttekintés](billing-usage-rate-card-overview.md).



