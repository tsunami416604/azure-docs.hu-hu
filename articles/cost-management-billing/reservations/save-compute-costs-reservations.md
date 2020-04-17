---
title: Mi az az Azure Reservations?
description: Megismerheti az Azure Reservationst és annak díjszabását a virtuális gépek, az SQL Database-adatbázisok, az Azure Cosmos DB és más erőforrások költségeinek csökkentéséhez.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: banders
ms.openlocfilehash: 4baa06ad0011b06da50fd7a83aaa3e87db62c6ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417528"
---
# <a name="what-are-azure-reservations"></a>Mi az az Azure Reservations?

Az Azure Reservationszel csökkentheti költségeit, ha több termékre is egy vagy három évre előre kötelezettséget vállal. A kötelezettségvállalással kedvezményt kaphat a felhasznált erőforrásokra. A Reservationsszel a használatalapú fizetéshez képest jelentősen, akár 72%-kal csökkentheti az erőforrások költségeit. A Reservations számlázási kedvezményt nyújt, és nincs hatással az erőforrások futtatási állapotára. A foglalás megvásárlása után a rendszer automatikusan alkalmazza a kedvezményt az annak megfelelő erőforrásokra.

A foglalásért fizethet előre vagy havonta. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie. A havi fizetés csak az Azure Reservations esetében érhető el, harmadik féltől származó termékek esetében nem.

Foglalásokat az Azure Portalon vásárolhat, a következő címen: [https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Miért érdemes foglalást vásárolnia?

Ha az erőforrások használata következetes mintát mutat, amely lehetővé teszi a foglalások alkalmazását, akkor a foglalások vásárlásával csökkentheti a költségeit. Ha például egy szolgáltatás több példányát futtatja folyamatosan foglalás nélkül, akkor használatalapú fizetéses díjakat fizet. Ha vásárol egy foglalást, azonnal megkapja a foglalási kedvezményt. Az erőforrásokért ezután nem használatalapú fizetéses díjakat kell fizetnie.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A vásárlást követően a foglalási kedvezmény automatikusan érvényesül a foglalás megvásárlásakor kiválasztott attribútumoknak megfelelő erőforrások használatára. Az attribútumok közé tartozik a termékváltozat, (adott esetben) a régiók és a hatókör. A foglalás hatóköre megadja a foglalási kedvezmények alkalmazásának helyét.

A kedvezmények alkalmazásáról további információt [a fenntartott példányokra vonatkozó kedvezmény alkalmazását](reservation-discount-application.md) ismertető oldalon talál.

A foglalási hatókörök működésére vonatkozó további információkat a [foglalási hatókörökkel](prepare-buy-reservation.md#scope-reservations) foglalkozó témakör ismerteti.

## <a name="determine-what-to-purchase"></a>Kiválaszthatja, hogy mit szeretne megvásárolni 

Az Azure Databricksen kívül minden foglalási díj óraalapú. A foglalásokat az alapján érdemes megvásárolni, hogy általában milyen használat jellemzi a rendszerét. A használati adatok elemzésével vagy a foglalási javaslatokkal határozhatja meg, hogy milyen foglalást vásároljon. A javaslatok a következő helyeken érhetők el:

- Azure Advisor (csak virtuális gépek)
- Foglalásvásárlási folyamat az Azure Portalon
- Cost Management Power BI-alkalmazás
- API-k 

További információkért lásd:  [A megvásárolni kívánt foglalás kiválasztása](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>Foglalás vásárlása 

Foglalásokat az Azure Portalon, API-kkal, a PowerShellben és a parancssori felületen vásárolhat. 

Lépjen az Azure Portalra a vásárláshoz (https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) 

További információkért lásd:  [Foglalás vásárlása](prepare-buy-reservation.md) 

## <a name="how-is-a-reservation-billed"></a>Hogy történik a foglalás számlázása? 

A foglalás számlázása az előfizetésnél rögzített fizetési móddal történik. Ha elérhető, a foglalás költsége a pénzügyi keret egyenlegéből lesz levonva. Ha a pénzügyi keret egyenlege nem fedezi a foglalás költségét, akkor a többletköltségek ki lesznek Önnek számlázva. Ha használatalapú díjas, egyéni csomagban rendelkezik előfizetéssel, akkor a rendszer az előre fizetendő vásárlással azonnal megterheli a fiókjához tartozó hitelkártyát. A havi kifizetések a számlán jelennek még, és a rendszer havonta terheli meg a hitelkártyáját. Számlás fizetés esetén a díjak a következő számlán jelennek meg. 

## <a name="permissions-to-view-and-manage-reservations"></a>Engedélyek a foglalások megtekintésére és kezelésére 

A foglalást vásárló felhasználó és a foglalás számlázásához használt előfizetés fiókadminisztrátora a Tulajdonos szerepkört kapja meg a foglalási rendelés és a foglalás tekintetében.

Delegálhatja a foglalás kezelését, ha személyeket ad hozzá szerepkörökhöz a foglalási rendelésben vagy a foglalásban. Az Azure Portalon, illetve API-k és a PowerShell használatával rendelhet hozzá szerepköröket. 

További információk  [a foglalást kezelő felhasználók hozzáadásáról vagy módosításáról](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Foglalásrészletek és kihasználtság lekérése a vásárlás után

Ha van engedélye a foglalás megtekintésére, az Azure Portalon tekintheti meg és használhatja. Az adatokat API-kkal is lekérheti. 

A foglalások Azure Portalon való megtekintéséről további információért lásd:  [Foglalások megtekintése az Azure Portalon](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>Foglalások kezelése vásárlás után 

Egy Azure-foglalás megvásárlása után frissítheti a hatókört, hogy a foglalást egy másik előfizetésre alkalmazza, módosítsa, hogy ki kezelheti a foglalást, kisebb részekre ossza a foglalást, vagy módosítsa a példányméret rugalmasságát. 

További információk:  [Foglalások kezelése Azure-erőforrásokhoz](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Rugalmasság az Azure Reservations segítségével

Az Azure Reservations biztosítja a rugalmasságot a változó igények kielégítéséhez. A foglalásokat le tudja cserélni azonos típusú foglalásokra. Kérheti egy foglalás díjának visszatérítését is 50 000 USD összegig az elmúlt 12 hónapra vonatkozóan, ha már nincs rá szüksége. A visszatérítések felső korlátja a Microsofttal kötött szerződése hatálya alá tartozó összes foglalásra vonatkozik.

További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>A foglalás által fedezett költségek

- **Fenntartott virtuálisgép-példány** – A foglalás csak a virtuális gép számítási költségeit fedezi. Nem fedezi azonban a további szoftverek, a Windows, a hálózatkezelés és a tárolás költségeit.
- **Fenntartott kapacitás az Azure Storage-ban** – A foglalás a standard Blob Storage- vagy Azure Data Lake Gen2-tárfiókok tárolási kapacitását fedezi. A foglalás nem fedezi a sávszélességgel és a tranzakciókkal járó díjakat.
- **Fenntartott Azure Cosmos DB-kapacitás** – A foglalás az erőforrások számára kiosztott átviteli sebességet fedezi. Nem fedezi azonban a tárolás és a hálózatkezelés költségeit.
- **Fenntartott SQL Database virtuális mag** – A foglalás csak a számítási költségeket foglalja magában. Az SQL-licenc számlázása külön történik.
- **SQL Data Warehouse** – A foglalás a cDWU-használatot fedezi. Nem fedezi azonban az SQL Data Warehouse használatával kapcsolatos tárolási és hálózatkezelési költségeket.
- **Azure Databricks** – A foglalás csak a DBU-használatot fedezi. Az egyéb – például számítási, tárolási és hálózati – díjakat külön számítjuk fel.
- **App Service-bélyeg díja** – A foglalás a bélyeghasználatot fedezi. Nem érvényes azonban a feldolgozókra, így a bélyeghez társított egyéb erőforrásokért külön kell fizetnie.
- **Azure Database for MySQL** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a MySQL-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Database for PostgreSQL** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a PostgreSQL-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Database for MariaDB** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a MariaDB-adatbáziskiszolgálóhoz kapcsolódó szoftverek, hálózatkezelés és tárolás költségeit.
- **Azure Data Explorer** – A foglalás magában foglalja az árrés díját. A foglalás nem vonatkozik a fürtökhöz kapcsolódó számítási feladatok, hálózatkezelés és tárolás költségeire.
- **Azure Cache for Redis** – A foglalás csak a számítási költségeket foglalja magában. A foglalás nem fedezi a Redis Cache-példányokhoz kapcsolódó hálózatkezelés és tárolás költségeit.
- **Azure Dedicated Host** – A dedikált gazdagép csak a számítási költségeket foglalja magában.
- **Azure Disk Storage-foglalások** – A foglalás csak a P30-as vagy annál nagyobb méretű, prémium szintű SSD-k esetében érvényes. Az egyéb lemeztípusokat és a P30-asnál kisebb méretű lemezeket nem fedi le.

Szoftvercsomagok:

- **SUSE Linux** – A foglalás a szoftvercsomag költségeit foglalja magában. A kedvezmények csak a SUSE-mérőszámokra vonatkoznak, a virtuálisgép-használatra nem.
- **Red Hat-csomagok** – A foglalás a szoftvercsomag költségeit foglalja magában. A kedvezmények csak a RedHat-mérőszámokra vonatkoznak, a virtuálisgép-használatra nem.
- **VMware Solution by CloudSimple** – A foglalás a VMWare CloudSimple-csomópontokat foglalja magában. További szoftverköltségek is felmerülhetnek.
- **Azure Red Hat OpenShift** – A foglalás az OpenShift-költségeket foglalja magában, az Azure-infrastruktúra költségeit nem.

A Windows rendszerű virtuális gépek és az SQL Database esetében a foglalási kedvezmény nem vonatkozik a szoftverek költségeire. A licencelési költségeket az [Azure Hybrid Benefittel](https://azure.microsoft.com/pricing/hybrid-benefit/) fedezheti.


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:
    - [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
    - [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
    - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
    - [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)
    - [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)

- További információk a szolgáltatáscsomagok foglalásaival kapcsolatban:
    - [Azure Reserved VM Instancesszel rendelkező virtuális gépek](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Fenntartott Azure Cosmos DB-kapacitással rendelkező Azure Cosmos DB-erőforrások](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrások](../../sql-database/sql-database-reserved-capacity.md) További információk a szoftvercsomagok foglalásaival kapcsolatban:
    - [Azure Reservations-beli Red Hat-szoftvercsomagok](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure Reservations-beli SUSE-szoftvercsomagok](../../virtual-machines/linux/prepay-suse-software-charges.md)
