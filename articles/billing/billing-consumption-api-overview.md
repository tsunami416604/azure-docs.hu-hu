---
title: Azure-használatát API áttekintése |} Microsoft Docs
description: Ismerje meg, hogyan Azure felhasználási API-k adjon meg programozott hozzáférést költségeket, és az Azure-erőforrások vonatkozó használati adatokat.
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
ms.openlocfilehash: becc6d6e86fd292cfbc919f1eee36a4f5edb711c
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851277"
---
# <a name="azure-consumption-api-overview"></a>Azure-használatát API – áttekintés 

Az Azure felhasználási API-k adjon meg programozott hozzáférést költségeket, és a használati adatokat az Azure-erőforrások. Ezen API-k jelenleg csak támogatja a vállalati regisztráció és a webes közvetlen előfizetések (néhány kivételtől eltekintve). Az API-kat támogató Azure-előfizetések más típusú folyamatosan frissülnek.

Az Azure felhasználási API-k eléréséhez:
- Vállalati és a webes egy közvetlen használó ügyfelek számára. 
    - Használat részletei 
    - Piactér díjak 
    - Foglalási javaslatok 
    - Foglalás részletei 
    - Foglalási az oldalon 
- Csak a vállalati ügyfelek 
    - Árlista 
    - Költségkeret 
    - Kiegyensúlyozza 

## <a name="usage-details-api"></a>Használat részletei API

Használat részletei API-t használó díjmentesen és használati adatok lekérése az összes Azure-1-jétől gyártók erőforrásait. Információ van jelenleg egyszer kibocsátott száma naponta erőforrásonként mérési használati részletes rekordok formájában. Adatok használhatók a költségek egyezzen az erőforrások között, vagy vizsgálja meg a költségek /-használat a konkrét (oka) t. 

Az API-t tartalmazza:

-   **Szint adatokkal mérni** – tekintse meg adatokat, többek között a költség, a mérő az elsők között kibocsátó használati és milyen az Azure erőforrás az elsők között vonatkozik. Képezze le az összes részletes rekordok napi gyűjtőjét.
-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Szűrés** -Trim az API-eredményéhez meg kevesebb részletes rekordok használatával a következő szűrőket értékre:
    - Használati célból / használati indítása
    - Erőforráscsoport
    - Erőforrás neve
-   **Adatösszesítés** -használata OData kifejezések használati adatainak címkék alapján vagy szűrő tulajdonságai
-   **A különböző típusú használati** -információ a használat részleteiről érhető el jelenleg a vállalati és a webes közvetlen használó ügyfelek számára.

További információkért lásd: a műszaki előírása a [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Piactér díjak API

Piactér díjak API-t használó összes piactér erőforrásra kell fizetni és használati adatok eléréséhez (Azure 3. fél ajánlatok). Ezen adatok segítségével költségek egyezzen piactér erőforrások között, vagy vizsgálja meg a költségek /-használat a konkrét (oka) t. 

Az API-t tartalmazza:

-   **Szint adatokkal mérni** – tekintse meg adatokat, többek között a piactér használati költség, a mérő kibocsátó az elsők között, és milyen erőforrás az elsők között vonatkozik. Képezze le az összes részletes rekordok napi gyűjtőjét.
-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Szűrés** -vághatja a API eredménykészlet egy kisebb csoportjának piactér rögzíti a következő szűrők használata:
    - Használati start / használati végén
    - Erőforráscsoport
    - Erőforrás neve
-   **A különböző típusú használati** -Marketplace-információ érhető el jelenleg a vállalati és a webes közvetlen használó ügyfelek számára.

További információkért lásd: a műszaki előírása a [piactér díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Kiegyensúlyozza API

A vállalati ügyfelek felhasználhat a kiegyensúlyozza a API havi összefoglaló információk kiegyensúlyozza, új, Azure piactér szolgáltatási díjak, módosítását, és keretét díjakat. Az információk az aktuális elszámolási időszak vagy egy az elmúlt időszakban. A vállalatok ezek az adatok segítségével hajtsa végre kézzel számított összefoglaló díjak összehasonlítása. Ez az API nem erőforrás-specifikus információkat nyújtson és költségek az összesített nézet. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Csak a vállalati ügyfelek** Ez az API felület csak az elérhető EA ügyfelek. 
    - Az ügyfelek az API hívása vállalati rendszergazdai jogosultságokkal kell rendelkeznie 

További információkért lásd: a műszaki előírása a [kiegyensúlyozza API](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount).

## <a name="budgets-api"></a>Költségvetések API

Vállalati ügyfelek használhatják az API költség vagy a használati költségvetések erőforrások, erőforráscsoport-sablonok vagy számlázási mérőszámok létrehozásához. Ha ezt az információt a rendszer megállapította, riasztás beállítható úgy, hogy értesítése, ha a felhasználó által definiált költségvetési küszöbérték túllépése. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Csak a vállalati ügyfelek** -Ez az API felület csak az elérhető EA ügyfelek.
-   **Konfigurálható értesítések** -adja meg a felhasználókat, a költségvetési azokat van kioldják értesítést.
-   **Használati és költség alapú költségvetések** -hozzon létre a költségvetést, igényei szerint a forgatókönyv fogyasztás vagy a költsége alapján.
-   **Szűrés** -a költségvetést, az erőforrásokat a következő konfigurálható szűrők használatával kisebb részhalmazát szűrése
    - Erőforráscsoport
    - Erőforrás neve
    - Forgalmi díj
-   **Konfigurálható költségvetési időtartamot-** -adja meg, milyen gyakran, a költségvetési állítsa vissza, és mennyi ideig a keret esetén érvényes.

További információkért lásd: a műszaki előírása a [költségvetések API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Foglalási javaslatok API

Ez az API segítségével megvásárlásáról a virtuális gép fenntartott példányok javaslatok beszerezni. Javaslatok célja, hogy lehetővé teszi az ügyfelek várt költséghatékony elemzése és a beszerzési összegek. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Szűrés** -testre szabni a javaslat eredmény a következő szűrők használata:
    - Hatókör
    - Lookback időszak
-   **Foglalási adatokat a különböző típusú** -foglalási információkat érhető el jelenleg a vállalati és a webes közvetlen használó ügyfelek számára.

További információkért lásd: a műszaki előírása a [foglalás javaslatok API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API foglalás részletei

Használja a Foglalás részletei API információ megjelenítéséhez a korábban beszerzett VM foglalások például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatban van. Megtekintheti az adatokat egy virtuális gépenként. részletességi szint. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Szűrés** -vághatja a API eredménykészlet egy kisebb csoportjának foglalások használatával a következő szűrőt:
    - Dátumtartomány
-   **Foglalási adatokat a különböző típusú** -foglalási információkat érhető el jelenleg a vállalati és a webes közvetlen használó ügyfelek számára.

További információkért lásd: a műszaki előírása a [foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Foglalási ezekkel az API

Ez az API összesített információk megjelenítéséhez a korábban beszerzett VM foglalások például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatos aggregált használja. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Szűrés** -testre szabni az eredményeket, ha a napi bontásban használja a következő szűrőt:
    - Usage Date (Használat dátuma)
-   **Foglalási adatokat a különböző típusú** -foglalási információkat érhető el jelenleg a vállalati és a webes közvetlen használó ügyfelek számára.
-   **Napi vagy havi összesítések** – hívóknak adhat meg, hogy meg kívánják a Foglalás a napi vagy havi bontásban állapotösszegzőjének adatai.

További információkért lásd: a műszaki előírása a [foglalás ezekkel az API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Price Sheet API
Vállalati ügyfél az API használatával az egyéni árképzési minden beolvasása. A vállalatok ezzel és használati adatokat és a piactér használati adatok együttes használatával használati és piactér számítások végrehajtásához. 

Az API-t tartalmazza:

-   **Szerepköralapú hozzáférés-vezérlés az Azure** -konfigurálja a hozzáférési házirendek a [Azure-portálon](https://portal.azure.com), a [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview) határozza meg, melyik felhasználók vagy alkalmazások ingyenesen juthatnak az előfizetés használati adatok. Hívóknak kell használnia szabványos Azure Active Directory-jogkivonatokat a hitelesítéshez. A hívó hozzáadása a számlázási olvasó, olvasó, tulajdonosi vagy közreműködői szerepkör egy adott Azure-előfizetés a használati adatok eléréséhez. 
-   **Csak a vállalati ügyfelek** -Ez az API felület csak az elérhető EA ügyfelek. Webes közvetlen ügyfelek a RateCard API használatával kell beolvasni díjszabási. 

További információkért lásd: a műszaki előírása a [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Forgatókönyvek

Az alábbiakban néhány lehetséges a fogyasztás API-k segítségével végrehajtott forgatókönyv:

-   **Egyeztetés számla** -volt Microsoft díjat számítanak, kérek a megfelelő összeg?  A számlázási és I számolhatja azt saját?
-   **Kereszt-díjak** - most, hogy tudható, hogy milyen mértékű I vagyok felszámított, akik a saját szervezeti kell fizetnie kell?
-   **Optimalizálás költség** -tudható, hogy mennyi felszámított már van szükségem... Hogyan kaphatok további a pénz I vagyok ebből az Azure-on kívül?
-   **Költségek nyomon követése** -mennyi I vagyok költségeik és az Azure használatával időbeli megtekintése. Mik a trendek? Hogyan sikerült I kell módon jobban?
-   **A hónap során töltött Azure** -mekkora az aktuális hónap dátumhoz tartozó töltött? Van a kiadások és/vagy Azure használatát végezze el a módosításokat? A hónap során mikor vagyok I fel Azure a legtöbb?
-   **Riasztások beállítása** – erőforrás-alapú felhasználás beállítása szeretnék vagy költségvetést pénzügyi alapú riasztások alapján.

## <a name="next-steps"></a>További lépések

- Az Azure használatának programozott módon webhelynaplókat Azure számlázási API-k használatával kapcsolatos információkért lásd: [Azure számlázási API – áttekintés](billing-usage-rate-card-overview.md).



