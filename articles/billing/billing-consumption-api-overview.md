---
title: Az Azure felhasználási API áttekintése | Microsoft Docs
description: Ismerje meg, hogy az Azure-beli felhasználási API-k programozott hozzáférést biztosítanak az Azure-erőforrások díjszabási és használati adataihoz.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443275"
---
# <a name="azure-consumption-api-overview"></a>Az Azure felhasználási API áttekintése 

Az Azure használati API-k programozott hozzáférést biztosítanak az Azure-erőforrások költség- és használati adataihoz. Ezek az API-k jelenleg csak a vállalati regisztrációkat és a web Direct-előfizetéseket támogatják (néhány kivétellel). Az API-k folyamatosan frissülnek, és más típusú Azure-előfizetéseket is támogatnak.

Az Azure használati API-k a következőkhöz biztosítanak hozzáférést:
- Vállalati és Web Direct-ügyfelek 
    - Használat részletei 
    - A Marketplace díjai 
    - Foglalási javaslatok 
    - Foglalási adatok 
    - Foglalási összegzések 
- Csak vállalati ügyfelek számára 
    - Árlista 
    - Költségvetések 
    - Egyenlegek 

## <a name="usage-details-api"></a>Használati adatok API

Használja a használati adatok API-t az összes Azure-beli fél erőforrásának felszámított díjainak és használati adatainak megszerzéséhez. Az információk a használati adatokból álló rekordok formájában jelennek meg, amelyek napi egységenként, óránként, óránként vannak kibocsátva. Az információk használatával felveheti a költségeket az összes erőforrásra, illetve megvizsgálhatja az adott erőforrás (ok) költségeinek/használatának vizsgálatát. 

Az API-t tartalmazza:

-   **Mérési szint használati adatok** – Itt láthatja az adatokat, beleértve a használati költségeket, a díjat kibocsátó mérőszámot, valamint azt, hogy milyen Azure-erőforrásra vonatkozik a díj. Az összes használati részlet rekord a napi gyűjtőre mutat.
-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** – az API-eredmény levágása a használati részletességi rekordok kisebb készletére a következő szűrőkkel:
    - Használat vége/használat kezdete
    - Erőforráscsoport
    - Erőforrás neve
-   **Adatösszesítés** – a OData használatával kifejezéseket alkalmazhat a használati adatok összesítésére címkék vagy szűrési tulajdonságok alapján
-   **Különböző ajánlati típusok használata** – jelenleg a vállalati és a webes Direct-ügyfelek számára érhető el a használati adatokra vonatkozó információ.

További információ: technikai specifikáció a [használati adatok API](https://docs.microsoft.com/rest/api/consumption/usagedetails)-hoz.

## <a name="marketplace-charges-api"></a>Marketplace-díjak API

A Piactéri díjak API-val megtekintheti az összes Piactéri erőforrás díját és használati adatait (az Azure harmadik féltől származó ajánlatai). Ezek az adatok felhasználhatók az összes Piactéri erőforrással kapcsolatos költségek hozzáadására, illetve az adott erőforrás (ok) költségeinek és használatának kivizsgálására. 

Az API-t tartalmazza:

-   **Mérési szint használati adatok** – lásd: az adatok, beleértve a piactér használatának költségeit, a díjat kibocsátó mérőszámot, valamint azt az erőforrást, amelyre a díj vonatkozik. Az összes használati részlet rekord a napi gyűjtőre mutat.
-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** – az API-eredmény beállítása a Piactéri rekordok egy kisebb készletére van beállítva a következő szűrők használatával:
    - Használat kezdő/használati vége
    - Erőforráscsoport
    - Erőforrás neve
-   **Különböző ajánlati típusok használata** – a piactérsel kapcsolatos információk jelenleg a vállalati és a webes közvetlen ügyfelek számára érhetők el.

További információkért lásd a [Piactéri díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces)technikai specifikációját.

## <a name="balances-api"></a>Egyenlegek API

A nagyvállalati ügyfelek az egyenlegek API használatával havi összefoglaló információkat kaphatnak a egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a beállításokról és a túlterhelésről. Ezt az információt az aktuális számlázási időszakra vagy a múltbeli időszakra vonatkozóan szerezheti be. A vállalatok ezekkel az adatokkal összehasonlítást végezhetnek manuálisan számított összefoglaló díjakkal. Ez az API nem biztosít erőforrás-specifikus információkat és összesített nézetet a költségekről. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** Ez az API csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el. 
    - A felhasználóknak vállalati rendszergazdai engedélyekkel kell rendelkezniük az API meghívásához 

További információkért tekintse meg az [egyenlegek API](https://docs.microsoft.com/rest/api/consumption/balances)technikai specifikációját.

## <a name="budgets-api"></a>Költségvetési API

A nagyvállalati ügyfelek ezt az API-t használhatják az erőforrások, erőforráscsoportok vagy számlázási mérőszámok költség-és használati költségvetésének létrehozásához. Miután megtörtént az információ meghatározása, a riasztás beállítható úgy, hogy értesítést kapjon a felhasználó által meghatározott költségvetési küszöbértékek túllépése esetén. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** – ez az API csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el.
-   **Konfigurálható értesítések** – Itt adhatja meg, hogy a rendszer mely felhasználó (ka) t kapja meg, ha a költségvetést megoldják.
-   **Használati vagy Cost-alapú költségkeret** – a saját költségkerete alapján hozhatja létre a költségvetést.
-   **Szűrés** – a költségvetést a következő konfigurálható szűrőket használó erőforrások kisebb részhalmazára szűrheti
    - Erőforráscsoport
    - Erőforrás neve
    - Mérő
-   **Konfigurálható költségvetési időszakok** – Itt adhatja meg, hogy milyen gyakran kell alaphelyzetbe állítani a költségvetést, és hogy mennyi ideig érvényes a költségvetés.

További információ: a [költségvetési API](https://docs.microsoft.com/rest/api/consumption/budgets)technikai specifikációja.

## <a name="reservation-recommendations-api"></a>Foglalás Recommendations API

Ezzel az API-val javaslatokat kaphat a fenntartott VM-példányok vásárlásához. A javaslatok célja, hogy lehetővé tegye az ügyfeleknek a várható költségmegtakarítás és a vásárlási összegek elemzését. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** – a javaslatok eredményeit a következő szűrőkkel szabhatja testre:
    - Scope
    - Lookback időszak
-   **A különböző ajánlati típusok foglalási** adatai – a foglalási információk jelenleg a vállalati és a webes közvetlen ügyfelek számára érhetők el.

További információkért tekintse meg a [foglalás Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations)technikai specifikációját.

## <a name="reservation-details-api"></a>Foglalás részletei API

A foglalás részletei API-val megtekintheti a korábban megvásárolt virtuális gépek foglalásának adatait, például azt, hogy mekkora mennyiségű felhasználás van fenntartva, és milyen mértékben van ténylegesen használatban. A virtuális gép szintjének részletes adataiban láthatja az adatokat. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** – az API-eredmény beállítása kisebb foglalások esetén a következő szűrővel:
    - Dátumtartomány
-   **A különböző ajánlati típusok foglalási** adatai – a foglalási információk jelenleg a vállalati és a webes közvetlen ügyfelek számára érhetők el.

További információkért lásd a [foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails)technikai specifikációját.

## <a name="reservation-summaries-api"></a>Foglalási összefoglalók API

Ezzel az API-val megtekintheti a korábban megvásárolt virtuális gépek foglalásának összesített információit, például a lefoglalt felhasználás mennyiségét, valamint azt, hogy mennyit használják ténylegesen az összesítésben. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Szűrés** – az eredmények személyre szabása a napi gabona a következő szűrővel való használata esetén:
    - Usage Date (Használat dátuma)
-   **A különböző ajánlati típusok foglalási** adatai – a foglalási információk jelenleg a vállalati és a webes közvetlen ügyfelek számára érhetők el.
-   **Napi vagy havi összesítések** – a hívók megadhatják, hogy a foglalási összesítő adatokat a napi vagy a havi gabonában szeretné-e használni.

További információkért lásd a [foglalási összefoglalók API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries)technikai specifikációját.

## <a name="price-sheet-api"></a>Árlista API
A nagyvállalati ügyfelek ezt az API-t használhatják az egyéni díjszabás beolvasására az összes mérőszám esetében. A vállalatok ezt a használati adatokkal és a piactér használati adataival együtt használva a használati és a Piactéri adatokkal is elvégezhetik a kalkulációt. 

Az API-t tartalmazza:

-   **Azure szerepköralapú Access Control** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) vagy a [Azure PowerShell parancsmagok](https://docs.microsoft.com/powershell/azure/overview) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. Hívó standard szintű Azure Active Directory-jogkivonatok hitelesítést kell használnia. Adja hozzá a hívó érhet el a használati adatokat egy adott Azure-előfizetés vagy a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörrel. 
-   **Csak vállalati ügyfelek** – ez az API csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el. A web Direct-ügyfeleknek a RateCard API-t kell használniuk a díjszabás beszerzéséhez. 

További információkért tekintse meg az [árlista API](https://docs.microsoft.com/rest/api/consumption/pricesheet)technikai specifikációját.

## <a name="scenarios"></a>Forgatókönyvek

Íme néhány példa a felhasználási API-k használatával:

-   **Számla egyeztetése** – a Microsoft a megfelelő összeget terhelte nekem?  Mi a számla, és tudom kiszámítani magam?
-   **Több költség** – most, hogy tudom, mennyit kell fizetnem?
-   **Költség optimalizálása** – tudom, hogy mennyit töltöttem fel... Hogyan tudhatok meg többet a pénzért az Azure-ban?
-   **Cost Tracking** – szeretném megtekinteni, hogy mennyit töltöttem és használom az Azure-t az idő múlásával. Mik azok a trendek? Hogyan lehetne jobban csinálni?
-   **Az Azure-t a hónap folyamán költi** – Mennyibe kerül az aktuális hónap napja? Szükség van-e bármilyen módosításra az Azure-beli kiadások és/vagy az Azure-használat tekintetében? Ha a hónapban használom az Azure-t?
-   **Riasztások beállítása** – erőforrás-alapú vagy pénzügyi alapú riasztást szeretne beállítani egy költségkeret alapján.

## <a name="next-steps"></a>További lépések

- További információ a Azure-számlázási API-k használatáról az Azure-beli használatra való programozott betekintéshez: az [Azure számlázási API áttekintése](billing-usage-rate-card-overview.md).



