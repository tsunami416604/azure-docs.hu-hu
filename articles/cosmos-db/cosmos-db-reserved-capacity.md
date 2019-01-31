---
title: Lefoglalt kapacitás optimalizálása az Azure Cosmos DB-erőforrások költségeit
description: Ismerje meg, hogyan vásárolhat Azure Cosmos DB szolgáltatás számára fenntartott kapacitás a számítási költségek mentéséhez.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b37aa2eb24fe79d322839b4fb5c3c3a8b1296033
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454097"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizálhatja költségeit az Azure Cosmos DB-ben lefoglalt kapacitás

Az Azure Cosmos DB kapacitás révén pénzt takaríthat meg előre kellene fizetnie, az Azure Cosmos DB-erőforrásokat akár egy vagy három év fenntartva. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás a Cosmos DB-erőforrásokat a létesített átviteli sebesség a kérheti le a kedvezményeket is. Erőforrásokat például az adatbázisok és a tárolók (táblák, gyűjtemények és grafikonok).

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás jelentősen csökkentheti a Cosmos DB költségeket&mdash;akár 65 %-a – egyéves vagy hároméves előzetes kötelezettségvállalás a normál árakhoz. Lefoglalt kapacitás egy számlázási-os kedvezményt biztosít, és nem befolyásolja az Azure Cosmos DB-erőforrások futási állapotát.

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás átviteli sebesség kiosztott részéért, az erőforrások ismerteti. A tárolási és hálózati díjak nem fedi le. Amint vásárol egy foglalást, az átviteli díjakat, amelyek megfelelnek a Foglalás attribútumok már nem számoljuk el a használatalapú – mint-akkor lépjen a díjszabás szerint. A foglalások további információkért lásd: a [Azure foglalások](../billing/billing-save-compute-costs-reservations.md) cikk. 

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás is elérhetőek a [az Azure portal](https://portal.azure.com). Lefoglalt kapacitás vásárlása:

* A tulajdonos szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre kell lennie.  
* Az Enterprise-előfizetések, a foglalást az Azure-beli vásárlásokra engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com/).  
* A Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás.

## <a name="determine-the-required-throughput-before-purchase"></a>A vásárlás előtt szükséges átviteli sebesség meghatározása

A Foglalás mérete átviteli sebességet, amelyet a fogja használni a meglévő vagy hamarosan –--telepíteni az Azure Cosmos DB-erőforrások teljes mennyisége alapján. Megadhatja, hogy a szükséges átviteli sebesség a következő módon:

* Korábbi adatainak lekérése a teljes kiosztott átviteli kapacitás az Azure Cosmos DB fiókok, adatbázisok és gyűjtemények között minden régióban. Például kiértékelheti a napi átlagos kiosztott átviteli sebesség a napi használati utasítás a letöltésével `https://account.azure.com`.

* Ha az ügyfél egy nagyvállalati szerződés (EA), letöltheti az Azure Cosmos DB-átviteli részletes a használati fájlban. Tekintse meg a **szolgáltatástípus** értékét a **további információ** a használatot részletező fájl szakaszában.

* Az átlagos átviteli sebességet a számítási feladatokhoz az Azure Cosmos DB-fiókjához, futtassa a következő egy vagy három év a várt összeg. A Foglalás ezután használhatja ezt a mennyiséget.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  

2. Válassza ki **minden szolgáltatás** > **foglalások** > **Hozzáadás**.  

3. Az a **terméktípus kiválasztása** panelen válassza a **Azure Cosmos DB** > **válassza** új foglalást vásárolhat.  

4. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   ![A lefoglalt kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Mező  |Leírás  |
   |---------|---------|
   |Name (Név)   |    A Foglalás nevét. Ez a mező automatikusan megjelenik `CosmosDB_Reservation_<timeStamp>`. Megadhat egy másik nevet a foglalás létrehozása során. Vagy átnevezheti a foglalás létrehozása után.      |
   |Előfizetés  |   Az Azure Cosmos DB fizetnie használt előfizetés fenntartott kapacitás. A fizetési módot, a kijelölt előfizetéshez az előzetes költségek díjszabási használatos. Az előfizetés típusa a következők egyikének kell lennie: <br/><br/>  [Nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) (csomag száma: MS-AZR-0017P): Az Enterprise-előfizetésre a díjakat a regisztráció pénzügyi kötelezettségvállalási egyenleg vonni vagy számlázva a. <br/><br/> [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) (csomag száma: MS-AZR-0003P): Használatalapú fizetéses előfizetésre az előfizetés bankkártyával vagy számla ellenében fizetési módot, a díjakat számlázzuk ki.    |
   |Hatókör   |   Ez a beállítás szabályozza, hogy hány előfizetés használhatja az társított a Foglalás számlázási ellátás. Azt is meghatározza, hogyan kell alkalmazni a Foglalás meghatározott előfizetések.   <br/><br/>  Ha **egy előfizetés**, a foglalási kedvezményt a kijelölt előfizetésben az Azure Cosmos DB-példányra van alkalmazva. <br/><br/>  Ha **megosztott**, a foglalási kedvezményt van alkalmazva minden előfizetésre a számlázási környezetben futó Azure Cosmos DB-példányokhoz. A számlázási környezetben alapján hogyan jelentkezett az Azure-hoz. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és előfizetéseken belül a regisztráció (kivéve a fejlesztési és tesztelési előfizetések) magában foglalja. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.  <br/><br/> A lefoglalt kapacitás vásárlás után módosíthatja a Foglalás hatóköre.  |
   |Fenntartott kapacitás típusa   |  Átviteli sebesség kiosztott kérelemegységek.|
   |Fenntartott kapacitási egységek  |      Átviteli sebesség a lefoglalni kívánt mennyisége. Ez az érték meghatározása az átviteli sebesség a Cosmos DB erőforrásokat (például adatbázisok és tárolók) szükséges régiónként kiszámítása. Majd akkor azt a Cosmos DB-adatbázissal társító régiók száma alapján.  <br/><br/> Példa: Ha öt régió 1 millió RU/s, minden régióban, válassza ki az 5 millió RU/s kapacitás a foglalásvásárlás esetén.    |
   |Időtartam  |   Egy vagy három év.   |

5. Tekintse át a kedvezményeket és a Foglalás az ára a **költségek** szakaszban. Átviteli sebesség kiosztott részéért, minden régióban az Azure Cosmos DB-erőforrásokat foglalás fizetendő díj vonatkozik.  

6. Válassza a **Beszerzés** lehetőséget. A következő oldal jelenik meg, ha a vásárlás sikeres volt: 

   ![A lefoglalt kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Miután vásárol egy foglalást, alkalmazva azonnal minden meglévő Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a Foglalás feltételeit. Ha nem rendelkezik olyan meglévő Azure Cosmos DB-erőforrások, a Foglalás érvényes lesz, ha telepít egy új Cosmos DB-példányra, amely megfelel a Foglalás feltételeit. Mindkét esetben a Foglalás időtartama a sikeres vásárlás után azonnal elkezdődik. 

Ha a Foglalás lejár, az Azure Cosmos DB üzemelő példányok továbbra is futnak, és a normál használatalapú díjakat számítjuk.

## <a name="next-steps"></a>További lépések

Rendszer automatikusan alkalmazza a foglalási kedvezményt az Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a Foglalás hatóköre és attribútumokat. Az Azure portal, PowerShell, Azure CLI-vel vagy az API-t a Foglalás hatóköre frissítheti.

*  Ismerje meg, hogyan tartalékkapacitást kedvezmények az Azure Cosmos DB-hez, lásd: [megismerheti a Azure foglalási kedvezményt](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Azure foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

   * [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md)  
   * [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)  
   * [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)
   * [A Partner Center CSP program Azure-foglalásokat](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

