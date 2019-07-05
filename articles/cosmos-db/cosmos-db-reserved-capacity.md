---
title: Lefoglalt kapacitás optimalizálása az Azure Cosmos DB-erőforrások költségeit
description: Ismerje meg, hogyan vásárolhat Azure Cosmos DB szolgáltatás számára fenntartott kapacitás a számítási költségek mentéséhez.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b74fcc2e08f02be7adeeab4cfee5f36d5194392c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508645"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optimalizálhatja költségeit az Azure Cosmos DB-ben lefoglalt kapacitás

Az Azure Cosmos DB kapacitás révén pénzt takaríthat meg előre kellene fizetnie, az Azure Cosmos DB-erőforrásokat akár egy vagy három év fenntartva. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás a Cosmos DB-erőforrásokat a létesített átviteli sebesség a kérheti le a kedvezményeket is. Erőforrásokat például az adatbázisok és a tárolók (táblák, gyűjtemények és grafikonok).

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás jelentősen csökkentheti a Cosmos DB költségeket&mdash;akár 65 %-a – egyéves vagy hároméves előzetes kötelezettségvállalás a normál árakhoz. Lefoglalt kapacitás egy számlázási-os kedvezményt biztosít, és nem befolyásolja az Azure Cosmos DB-erőforrások futási állapotát.

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás átviteli sebesség kiosztott részéért, az erőforrások ismerteti. A tárolási és hálózati díjak nem fedi le. Amint vásárol egy foglalást, az átviteli díjakat, amelyek megfelelnek a Foglalás attribútumok már nem számoljuk el a használatalapú – mint-akkor lépjen a díjszabás szerint. A foglalások további információkért lásd: a [Azure foglalások](../billing/billing-save-compute-costs-reservations.md) cikk.

Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás is elérhetőek a [az Azure portal](https://portal.azure.com). Lefoglalt kapacitás vásárlása:

* A tulajdonos szerepkör legalább egy vállalati vagy önálló-előfizetést, utólagos elszámolású díjszabás szerint kell lennie.  
* Vállalati előfizetés esetén **fenntartott példányok hozzáadása** engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com). Vagy, ha ez a beállítás le van tiltva, az előfizetés egy nagyvállalati szerződés rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás.

## <a name="determine-the-required-throughput-before-purchase"></a>A vásárlás előtt szükséges átviteli sebesség meghatározása

A Foglalás mérete átviteli sebességet, amelyet a fogja használni a meglévő vagy hamarosan –--telepíteni az Azure Cosmos DB-erőforrások teljes mennyisége alapján. Megadhatja, hogy a szükséges átviteli sebesség a következő módon:

* Korábbi adatainak lekérése a teljes kiosztott átviteli kapacitás az Azure Cosmos DB fiókok, adatbázisok és gyűjtemények között minden régióban. Például kiértékelheti a napi átlagos kiosztott átviteli sebesség a napi használati utasítás a letöltésével `https://account.azure.com`.

* Ha az ügyfél egy nagyvállalati szerződés (EA), letöltheti az Azure Cosmos DB-átviteli részletes a használati fájlban. Tekintse meg a **szolgáltatástípus** értékét a **további információ** a használatot részletező fájl szakaszában.

* Az átlagos átviteli sebességet a számítási feladatokhoz az Azure Cosmos DB-fiókjához, futtassa a következő egy vagy három év a várt összeg. A Foglalás ezután használhatja ezt a mennyiséget.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  

2. Válassza ki **minden szolgáltatás** > **foglalások** > **Hozzáadás**.  

3. Az a **foglalásokat vásárol** panelen válassza a **Azure Cosmos DB** új foglalást vásárolhat.  

4. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

   ![A lefoglalt kapacitás űrlap kitöltése](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Mező  |Leírás  |
   |---------|---------|
   |Scope   |   Ez a beállítás szabályozza, hogy hány előfizetés használhatja az társított a Foglalás számlázási ellátás. Azt is meghatározza, hogyan kell alkalmazni a Foglalás meghatározott előfizetések. <br/><br/>  Ha **megosztott**, a foglalási kedvezményt van alkalmazva minden előfizetésre a számlázási környezetben futó Azure Cosmos DB-példányokhoz. A számlázási környezetben alapján hogyan jelentkezett az Azure-hoz. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és a regisztráció belüli összes előfizetésre tartalmazza. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel az utólagos elszámolású díjszabás szerint a fiók rendszergazdája által létrehozott minden egyes előfizetés.  <br/><br/>  Ha **egy előfizetés**, a foglalási kedvezményt a kijelölt előfizetésben az Azure Cosmos DB-példányra van alkalmazva. <br/><br/> Ha **egyetlen erőforráscsoportra**, a foglalási kedvezményt a kijelölt előfizetésben és az adott előfizetésen belül a kiválasztott erőforráscsoportba tartozó Azure Cosmos DB-példányra van alkalmazva. <br/><br/> A lefoglalt kapacitás vásárlás után módosíthatja a Foglalás hatóköre.  |
   |Előfizetés  |   Az Azure Cosmos DB fizetnie használt előfizetés fenntartott kapacitás. A fizetési módot, a kijelölt előfizetéshez az előzetes költségek díjszabási használatos. Az előfizetés típusa a következők egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P): Az Enterprise-előfizetésre a díjakat a regisztráció pénzügyi kötelezettségvállalási egyenleg vonni vagy számlázva a. <br/><br/> Az utólagos elszámolású díjszabás szerint egyes előfizetés (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P): Az utólagos elszámolású díjszabás szerint az egyes előfizetésre az előfizetés bankkártyával vagy számla ellenében fizetési módot, a díjakat számlázzuk ki.    |
   | Erőforráscsoport | Az erőforráscsoport, amelyekre a lefoglalt kapacitás kedvezmény érvényes. |
   |Kifejezés  |   Egy vagy három év.   |
   |Átviteli típusa   |  Átviteli sebesség, a kérelemegységek van kiépítve. A kiosztott átviteli sebesség mindkét telepítéshez - foglalást vásárolhat egyetlen régióban, valamint több naplórekordjait ír. Az átviteli típus rendelkezik két érték közül választhat: 100 Kérelemegység/s az óránkénti egységszám és 100 több főkiszolgálós RU/s / óra.|
   | A kapacitás szolgáltatás számára fenntartott egységek| Átviteli sebesség a lefoglalni kívánt mennyisége. Ez az érték meghatározása az átviteli sebesség a Cosmos DB erőforrásokat (például adatbázisok és tárolók) szükséges régiónként kiszámítása. Majd akkor azt a Cosmos DB-adatbázissal társító régiók száma alapján. Példa: Ha öt régió 1 millió RU/s, minden régióban, válassza ki az 5 millió RU/s kapacitás a foglalásvásárlás esetén. |
 

5. Miután, töltse ki az űrlapot, megvásárolnia a lefoglalt kapacitás lesz kiszámítva. A kimenet is tartalmazza. a kedvezményt kap a kiválasztott beállításokkal. Ezután kattintson a **kiválasztása**

6. Az a **foglalásokat vásárol** panelen tekintse át a kedvezményeket és a Foglalás árát. Átviteli sebesség kiosztott részéért, minden régióban az Azure Cosmos DB-erőforrásokat foglalás fizetendő díj vonatkozik.  

   ![Lefoglalt kapacitás összegzése](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Válassza ki **felülvizsgálat + vételi** , majd **vásárlás**. A következő oldal jelenik meg, ha a vásárlás sikeres volt:

Miután vásárol egy foglalást, alkalmazva azonnal minden meglévő Azure Cosmos DB-erőforrásokat, amelyek megfelelnek a Foglalás feltételeit. Ha nem rendelkezik olyan meglévő Azure Cosmos DB-erőforrások, a Foglalás érvényes lesz, ha telepít egy új Cosmos DB-példányra, amely megfelel a Foglalás feltételeit. Mindkét esetben a Foglalás időtartama a sikeres vásárlás után azonnal elkezdődik.

Ha a Foglalás lejár, az Azure Cosmos DB üzemelő példányok továbbra is futnak, és a normál használatalapú díjakat számítjuk.

## <a name="cancellation-and-exchanges"></a>Megszakítás és cseréje

Segítség az azonosításához, a jobb oldali lefoglalt kapacitás: [megismerheti, hogyan kell alkalmazni a foglalási kedvezményt az Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Ha szeretné-e megszakítása vagy exchange-egy Azure Cosmos DB-foglalást, lásd: [foglalás cseréje és a visszatérítéseket](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

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
