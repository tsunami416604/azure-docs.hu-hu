---
title: Fizessen elő az Azure Cosmos DB-erőforrásokat pénzt takaríthat meg |} A Microsoft Docs
description: Ismerje meg, hogyan vásárolhat Azure Cosmos DB szolgáltatás számára fenntartott kapacitás a számítási költségek mentéséhez.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2291b2429e6c5c25e051c8f3eca30e1cc3f64611
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247329"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Fizessen elő az Azure Cosmos DB-erőforrásokat, hogy a lefoglalt kapacitás

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás révén pénzt takaríthat meg előre erőforrásokért kellene fizetnie Azure Cosmos DB – egyéves vagy hároméves időszakra. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás kedvezményes szerezni a Cosmos DB-erőforrások, például adatbázisok, tárolók (táblák/gyűjtemények/diagramok) a létesített átviteli sebesség teszi lehetővé. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás jelentősen csökkentheti a Cosmos DB költségeket – akár 65 %-a normál árakhoz – egyéves vagy hároméves előzetes kötelezettségvállalás. Lefoglalt kapacitás egy számlázási-os kedvezményt biztosít, és nincs hatással a Cosmos DB-erőforrások futási állapotát.

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás terjed ki az átviteli sebesség kiosztott részéért, az erőforrások, nem terjed ki a tárolási és hálózatkezelési díjakat. Amint vásárol egy foglalást, az átviteli díjakat, amelyek megfelelnek a Foglalás attribútumok már nem számoljuk el a használatalapú – mint-akkor lépjen a díjszabás szerint. A foglalások további információkért lásd: [Azure foglalások](../billing/billing-save-compute-costs-reservations.md) cikk. 

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás is elérhetőek a [az Azure portal](https://portal.azure.com). Lefoglalt kapacitás vásárlása:

* A tulajdonos szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre kell lennie.  
* Az Enterprise-előfizetések, a foglalást az Azure-beli vásárlásokra engedélyezve kell lennie a [nagyvállalati szerződések portálján.](https://ea.azure.com/)  
* A Cloud Solution Provider (CSP) program keretében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhat Azure Cosmos DB szolgáltatás számára fenntartott kapacitás.

## <a name="determine-the-required-throughput-before-purchase"></a>A vásárlás előtt szükséges átviteli sebesség meghatározása

Foglalási mérete alapján egy meglévő vagy hamarosan –--telepíteni az Azure Cosmos DB erőforrásokat (például adatbázisok és a tárolók - gyűjteményeket, táblák, grafikonok) által használt átviteli sebesség teljes mennyisége. Megadhatja, hogy a szükséges átviteli sebesség a következő módon:

* Korábbi adatainak lekérése a teljes kiosztott átviteli kapacitás az Azure Cosmos DB fiókok, adatbázisok és gyűjtemények között minden régióban. Ha például kiértékelheti a napi átlagos kiosztott átviteli sebesség úgy, hogy letölti a napi használati utasítás a `https://account.azure.com`

* Azt is megteheti, ha egy nagyvállalati szerződés (EA), letöltheti a használati fájlt és tekintse meg **szolgáltatástípus** értékét a **további információ** a használati fájlban az Azure Cosmos DB első szakasza átviteli adatokat.

* Az átlagos átviteli sebességet a számítási feladatok modult összeg a az Azure Cosmos DB-fiókokhoz, hogy fut várhatóan a következő egy vagy három évre, és használja ezt a mennyiséget foglaláshoz is.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  

2. Válassza ki **minden szolgáltatás** > **foglalások** > **Hozzáadás**.  

3. A a **terméktípus kiválasztása** panelen válassza a **Azure Cosmos DB**, majd **válassza** , egy új foglalást vásárolhat.  

4. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   ![A lefoglalt kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Mező  |Leírás  |
   |---------|---------|
   |Name (Név)   |    A Foglalás nevét. Ez a mező automatikusan megjelenik a `CosmosDB_Reservation_<timeStamp>`. Adjon meg egy másik nevet a foglalás létrehozása során, vagy nevezze át a foglalás létrehozása után.      |
   |Előfizetés  |   Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás fizetnie használt előfizetés. A fizetési módot, a kijelölt előfizetéshez az előzetes költségek díjszabási használatos. Az előfizetés típusa a következők egyikének kell lennie: <br/><br/>  [Nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) (csomag száma: MS-AZR - 0017 P) – egy vállalati előfizetés a költségek vonni a regisztráció pénzügyi kötelezettségvállalási egyenleg vagy számlázva a. <br/><br/> [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) (csomag száma: MS-AZR - 0003 P) – a használatalapú fizetéses előfizetésre, a díjakat számlázzuk, a bankkártyával vagy számla ellenében fizetési módot, az előfizetésben.    |
   |Hatókör   |   A Foglalás hatóköre szabályozza, hogy hány előfizetés kihasználhatják az társított a Foglalás számlázási ellátás, és azt szabályozza, hogyan kell alkalmazni a Foglalás meghatározott előfizetések. A Foglalás hatóköre egyetlen vagy közös előfizetést. Ha ki:   <br/><br/>  **Egy előfizetés** – a foglalási kedvezményt a kijelölt előfizetésben az Azure Cosmos DB-példányra van alkalmazva. <br/><br/>  **A megosztott** – a foglalási kedvezményt a számlázási környezetben egyetlen előfizetéshez az Azure Cosmos DB példányai vonatkozik. A számlázási környezetben hogyan jelentkezett az Azure alapján határozza meg. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és előfizetéseken belül a regisztráció (kivéve a fejlesztési és tesztelési előfizetések) magában foglalja. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.  <br/><br/> A lefoglalt kapacitás megvásárlása után módosíthatja a Foglalás hatóköre.  |
   |Fenntartott kapacitás típusa   |  Lefoglalt kapacitás típus az átviteli sebesség kiosztott kérelemegységek tekintetében.|
   |Fenntartott kapacitási egységek  |      Átviteli sebesség a lefoglalni kívánt mennyisége. Ez az érték kiszámításához az átviteli sebesség a Cosmos DB-erőforrásokat (például adatbázisok és tárolók) minden szükséges meghatározásával régiónként, és ezután akkor azt a régiók, amelyek fog társítani a Cosmos DB-adatbázissal számával.  <br/> Példa: Ha 1 millió RU/s öt régióban, minden régióban, majd válassza ki 5 millió RU/s kapacitás a foglalásvásárlás esetén.    |
   |Időtartam  |   Egy vagy három év.   |

5. Tekintse át a kedvezményeket és a Foglalás az ára a **költségek** szakaszban. Foglalás fizetendő díj kell alkalmazni az Azure Cosmos DB-erőforrásokat az átviteli sebesség kiosztott részéért, minden régióban.  

6. Válassza a **Beszerzés** lehetőséget. Az alábbi képernyőképen láthatja, ha a vásárlás sikeres volt. 

   ![A lefoglalt kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Egy foglalás megvásárlása után, azonnal alkalmazott bármely meglévő Azure Cosmos DB erőforrás, amely megfelel a Foglalás feltételeinek. Ha nem rendelkezik olyan meglévő Azure Cosmos DB-erőforrások, a Foglalás érvényes lesz, ha telepít egy új Cosmos DB-példányra, amely megfelel a Foglalás feltételeit. Mindkét esetben a Foglalás időtartama a sikeres vásárlás után azonnal elkezdődik. 

Ha a Foglalás lejár, az Azure Cosmos DB üzemelő példányok továbbra is futni fog, és a normál használatalapú díjakat számítjuk.

## <a name="next-steps"></a>További lépések

Rendszer automatikusan alkalmazza a foglalási kedvezményt az Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a Foglalás hatóköre és attribútumokat. Az Azure portal, PowerShell, CLI, vagy az API-n keresztül a Foglalás hatóköre frissítheti.

*  Ismerje meg, hogyan tartalékkapacitást kedvezmények az Azure Cosmos DB-hez, lásd: [kedvezményes Azure-foglalások ismertetése](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Azure foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

   * [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md)  
   * [Az Azure-fenntartások kezelése](../billing/billing-manage-reserved-vm-instance.md).  
   * [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)
   * [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

