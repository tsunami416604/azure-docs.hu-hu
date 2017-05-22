---
title: "Új bérlők kiépítése több-bérlős alkalmazásban az Azure SQL Database használatával | Microsoft Docs"
description: "Új bérlők kiépítése és katalógusba vétele a Wingtip Tickets (WTP) minta SQL Database SaaS-alkalmazásban"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: aae5d85a18f93b7821a6ef8fc7161dd9a6ebe533
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Új bérlők kiépítése és regisztrálása a katalógusban

Ebben az oktatóanyagban új bérlőket fog kiépíteni a Wingtip Tickets Platform (WTP) SaaS-alkalmazásban. Bérlőket és bérlői adatbázisokat fog létrehozni, és regisztrálni fogja a bérlőket a katalógusban. A *katalógus* egy olyan adatbázis, amely fenntartja az SaaS-alkalmazások bérlői és az adataik közötti leképezéseket. Ezen szkriptek használatával megismerheti a felhasznált kiépítési és katalógusmintákat, valamint azt, hogyan regisztrálhatja az új bérlőket a katalógusban. A katalógus fontos szerepet játszik abban, hogy az alkalmazások kéréseit a megfelelő adatbázisokhoz irányítsa.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése
> * Az új bérlők kiépítésére és katalógusban való regisztrálásukra vonatkozó részletek vizsgálata


Az oktatóanyag teljesítéséhez a következő előfeltételeknek kell teljesülnie:

* A WTP alkalmazás üzembe van helyezve. A kevesebb mint öt perc alatt történő üzembe helyezéshez lásd [a WTP SaaS-alkalmazás üzembe helyezésével és megismerésével](sql-database-saas-tutorial.md) foglalkozó cikket.
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Az SaaS-katalógusminta ismertetése

Egy adatbázis-alapú, több bérlős SaaS-alkalmazás esetében fontos tudni, hogy az egyes bérlőkre vonatkozó adatok hol találhatók. Az SaaS-katalógusmintában a katalógus-adatbázis tárolja a bérlők és az adataik tárolási helyei közötti társításokat. A WTP alkalmazás egy egybérlős adatbázis-architektúrát használ, de a bérlők és adatbázisok közötti társítások katalógusban való tárolásának alapvető mintája egybérlős és több bérlős adatbázisok esetén egyaránt érvényes.

Minden bérlőhöz hozzá van rendelve egy kulcs, amely elkülöníti az adott bérlő adatait a többitől a katalóguson belül. A WTP alkalmazásban a kulcs a bérlő nevének egy kivonatából készül. Ez a minta lehetővé teszi, hogy az alkalmazás URL-címének bérlőnév része felhasználható legyen a kulcs létrehozásakor és az egyes bérlők kapcsolatainak lekérésekor. Egyéb azonosítási sémák is használhatók, ez nincs hatással a minta egészére.

A WTP alkalmazás katalógusa szilánkkezelési technológiával van megvalósítva az [Elastic Database-ügyfélkódtárban (EDCL)](sql-database-elastic-database-client-library.md). Az EDCL felelős az adatbázis-alapú _katalógus_ létrehozásáért és kezeléséért, amely _szilánkleképezést_ használ. A katalógus tartalmazza a kulcsok (bérlők) és az adatbázisaik (szilánkok) közötti társításokat.

> [!IMPORTANT]
> A társítási adatok hozzáférhetők a katalógus-adatbázisban, de *ne módosítsa őket*! A társítási adatokat kizárólag az Elastic Database-ügyfélkódtár API-jaival szerkessze. A társítási adatok közvetlen módosítása a katalógus sérülésének kockázatát hordozza magában, ezért nem támogatott.

A Wingtip SaaS-alkalmazás az új bérlőket egy *alapul szolgáló* adatbázis másolásával építi ki.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazás szkriptjeinek lekérése

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Learning Modules** (Tanulási modulok) mappát a helyi számítógépére a mappaszerkezet megtartásával.

## <a name="provision-a-new-tenant"></a>Új bérlő kiépítése

Ha már létrehozott egy bérlőt az első WTP-oktatóanyagban, akkor lépjen a következő szakaszra: [Bérlők kötegelt kiépítése](#provision-a-batch-of-tenants).

Futtassa a *Demo-ProvisionAndCatalog* szkriptet egy bérlő gyors létrehozásához és regisztrálásához a katalógusban:

1. Nyissa meg a **Demo-ProvisionAndCatalog.ps1** fájlt a PowerShell ISE-ben, és állítsa be a következő értékeket:
   * **$TenantName** = az új helyszín neve (például *Bushwillow Blues*). 
   * **$VenueType** = az előre beállított helyszíntípusok egyike: blues, klasszikus zene, tánc, jazz, dzsúdó, motorsport, többcélú, opera, rockzene, futball.
   * **$DemoScenario** = 1. Hagyja meg a beállított _1_ értéket **egyetlen új bérlő kiépítéséhez**.

1. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

A szkript elkészültével az új bérlő kiépül, és megnyílik a bérlő *Events* (Események) alkalmazása a böngészőben:

![Új bérlő](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegelt kiépítése

Ez a gyakorlat további bérlők egy kötegét építi ki. Ajánlott elvégezni ezt a feladatot, mielőtt továbblépne más WTP-oktatóanyagokra.

1. Nyissa meg a ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* fájlt a *PowerShell ISE*-ben, és állítsa be a következő értéket:
   * **$DemoScenario** = **3**. Állítsa be a **3** értéket **bérlők kötegelt létrehozásához**.
1. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

A szkript üzembe helyezi a további bérlők kötegét. Ehhez egy [Azure Resource Manager-sablont](../azure-resource-manager/resource-manager-template-walkthrough.md) használ, amely a köteget irányítja, majd meghatalmazást ad egy-egy hivatkozott sablonnak az egyes adatbázisok kiépítésére. A sablonok ily módon való alkalmazása lehetővé teszi, hogy az Azure Resource Manager közvetítse a szkriptnek a kiépítési folyamatot. A sablonok párhuzamosan építik ki az adatbázisokat (ha ez lehetséges), és szükség esetén elvégzik az újbóli próbálkozásokat is, ezzel optimalizálva a folyamatot. A szkript idempotens, ezért ha megszakad, futtassa újra.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>A sikeresen kiépített bérlők kötegének ellenőrzése

* Nyissa meg a *tenants1* kiszolgálót az [Azure Portalon](https://portal.azure.com), és kattintson az **SQL-adatbázisok** elemre:

   ![adatbázislista](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Részletes útmutató a kiépítéshez és katalógusba vételhez

Ha szeretné jobban megérteni, hogy a Wingtip alkalmazás hogyan építi ki az új bérlőket, futtassa újra a *Demo-ProvisionAndCatalog* szkriptet, és hozzon létre egy újabb bérlőt. Ezúttal adjon hozzá egy töréspontot, és lépegessen végig a munkafolyamaton:

1. Nyissa meg a ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ fájlt, és állítsa be a következőket az új, a katalógusban még nem szereplő bérlőértékekhez:
   * **$TenantName** = állítson be egy új nevet (például *Hackberry Hitters*).
   * **$VenueType** = használja valamelyik előre beállított helyszíntípust (például *dzsúdó*).
   * **$DemoScenario** = 1. Állítsa be az **1** értéket **egyetlen új bérlő kiépítéséhez**.

1. Adjon meg egy töréspontot. Ehhez vigye a kurzort a *New-Tenant `* sor bármely pontja fölé, és nyomja le az **F9** billentyűt.

   ![töréspont](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. A szkript futtatásához nyomja le az **F5** billentyűt. Ha elérte a töréspontot, nyomja le az **F11** billentyűt a közbelépéshez. Kövesse a szkript végrehajtását az **F10** és **F11** billentyűkkel, hogy át- vagy belépjen a meghívott függvényeken vagy függvényekbe. [Tippek a PowerShell-szkriptek használatához és hibakereséséhez](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>A kiépítés és katalógusba vétel megvalósításának vizsgálata a szkript lépéseinek áttekintésével

A szkript a következő lépésekkel építi ki és veszi katalógusba az új bérlőket:

1. **Importálja a SubscriptionManagement.psm1 modult**, amely az Azure-bejelentkezésre és a használt Azure-előfizetés kiválasztására szolgáló függvényeket tartalmaz.
1. **Importálja a CatalogAndDatabaseManagement.psm1 modult**, amely katalógus- és bérlőszintű absztrakciót biztosít a [szilánkkezelési](sql-database-elastic-scale-shard-map-management.md) függvényekhez. Ez egy fontos modul, amely magába foglalja a katalógusminta nagy részét, ezért érdemes megismerkedni vele.
1. **Lekéri a konfigurációs részleteket**. Lépjen be a _Get-Configuration_ elembe (az **F11** lenyomásával), és nézze meg, hogyan van beállítva az alkalmazás konfigurációja. Itt vannak megadva az erőforrásnevek és egyéb alkalmazásspecifikus értékek, de ezeket az értékeket ne módosítsa addig, amíg ki nem ismerte a szkriptet.
1. **Lekéri a katalógusobjektumot**. Lépjen be a *Get-Catalog* elembe, és nézze meg, hogyan történik a katalógus inicializálása az **AzureShardManagement.psm1** fájlból importált szilánkkezelési függvények használatával. A katalógus a következő objektumokból áll:
   * $catalogServerFullyQualifiedName – a megszokott törzs és a felhasználónév alkotja: _catalog-\<felhasználó\>.database.windows.net_.
   * $catalogDatabaseName – a *tenantcatalog* konfigurációból származik.
   * $shardMapManager – ez az objektum a katalógus-adatbázisból van inicializálva.
   * $shardMap – ez az objektum a katalógus-adatbázisban található *tenantcatalog* szilánkleképezésből van inicializálva.
   A katalógusobjektumot a felsőbb szintű szkript állítja össze, adja vissza és használja.
1. **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
1. **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A rendszer ellenőrzi a katalógusban, hogy a kulcs használható-e.
1. **A bérlői adatbázis kiépítése a New-TenantDatabase használatával történik.** Nyomja le az **F11** billentyűt a belelépéshez, és nézze meg, ahogy az adatbázis egy Resource Manager-sablon használatával kiépül.
    
Az adatbázis neve a bérlő nevéből jön létre, hogy egyértelmű legyen, melyik szilánk melyik bérlőhöz tartozik. (Az adatbázisok más megfontolások alapján is elnevezhetők.)

Egy Resource Manager-sablon **létrehozza az adatbázist, mégpedig egy *alapul szolgáló* adatbázis (baseTenantDB) másolásával** a katalóguskiszolgálón.  Egy másik lehetséges megoldás egy üres adatbázis létrehozása, majd inicializálása egy bacpac importálásával.

A Resource Manager-sablon a ...\\Learning Modules\\Common\\ mappában található: *tenantdatabasecopytemplate.json*

A bérlői adatbázis létrejötte után további inicializálásra kerül sor a helyszín (a bérlő) nevének és a helyszín típusának felhasználásával. Ezen a ponton további inicializálások is elvégezhetők.

A bérlői adatbázis regisztrálása a katalógusban az *Add-TenantDatabaseToCatalog* és a bérlőkulcs használatával történik. Nyomja le az **F11** billentyűt a részletek megtekintéséhez:

* A katalógus-adatbázis bekerül a szilánkleképezésbe (az ismert adatbázisok listájába).
* Létrejön a kulcsérték és a szilánk társítása.
* Hozzáadásra kerülnek a bérlővel kapcsolatos további metaadatok (a helyszín neve).

A kiépítés befejezése után a végrehajtás visszatér az eredeti *Demo-ProvisionAndCatalog* szkripthez, és a böngészőben megnyílik az új bérlő **események** oldala:

   ![események](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Az ebben az oktatóanyagban nem ismertetett egyéb kiépítési minták közé tartoznak a következők:

**Adatbázisok előzetes kiépítése.** Ez a minta azt a tényt használja ki, hogy a rugalmas készletekben található adatbázisok nem járnak többletköltségekkel (a számlázás a rugalmas készletre vonatkozik, nem az adatbázisokra), és hogy a tétlen adatbázisoknak nincs erőforrás-fogyasztása. Ha az adatbázisokat előzetesen kiépíti egy készletben, majd szükség esetén osztja ki őket, azzal a bérlők bevezetésének ideje jelentős mértékben csökkenthető. Az előzetesen kiépített adatbázisok száma szükség szerint igazítható, hogy rendelkezésre álljon egy, a várható kiépítési aránynak megfelelő puffer.

**Automatikus kiépítés.** Ebben a mintában egy dedikált kiépítő szolgáltatás szükség esetén automatikusan épít ki kiszolgálókat, készleteket és adatbázisokat, igény esetén rugalmas készletekben található előzetesen kiépített adatbázisokat is. Ha az adatbázisok leszerelésre és törlésre kerülnek, a rugalmas készletekben keletkező réseket a kiépítő szolgáltatás igény szerint betöltheti. Egy ilyen szolgáltatás lehet egyszerű vagy összetett is – például egyszerre több földrajzi régióban is végezhet kiépítéseket, vagy automatikus georeplikációt állíthat be, ha ezt a vészhelyreállítási stratégiát használja. Az automatikus kiépítési mintában egy ügyfélalkalmazás vagy szkript elküld egy kiépítési kérést egy üzenetsorba, amelyet a kiépítő szolgáltatás feldolgoz, majd lekérdezi a kiszolgálót a kiépítés elvégzésével kapcsolatban. Az előzetes kiépítés használatakor a szolgáltatás gyorsan reagál a kérésekre, és kiépít egy, a háttérben futó helyettesítő adatbázist.


## <a name="stopping-wingtip-saas-application-related-billing"></a>A Wingtip SaaS-alkalmazásokkal kapcsolatos számlázás leállítása

Ha nem tervez több oktatóanyagot elvégezni, ajánlott az összes erőforrás törlése, nehogy további költségekről kapjon számlát. Törölje az erőforráscsoportot, amelyen a WTP alkalmazás üzembe lett helyezve. Ezzel az alkalmazás összes erőforrása törlődik.

* Tallózással nyissa meg az alkalmazás erőforráscsoportját a portálon, és törölje az adott WTP üzemelő példányhoz kapcsolódó összes számlázási művelet leállításához.

## <a name="tips"></a>Tippek

* Az EDCL olyan fontos funkciókat is biztosít, amelyek lehetővé teszik ügyfélalkalmazások számára a katalógushoz való csatlakozást és a katalógus módosítását. Az EDCL arra is használható, hogy lekérjen egy ADO.NET-kapcsolatot egy adott kulcsértékhez, ezáltal engedélyezve az alkalmazás számára a megfelelő adatbázishoz való csatlakozást. Az ügyfél ennek a kapcsolatnak az információt gyorsítótárban tárolja a katalógus-adatbázis forgalmának csökkentése és az alkalmazás sebességének növelése érdekében.



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése
> * Az új bérlők kiépítésére és katalógusban való regisztrálásukra vonatkozó részletek vizsgálata

[Teljesítményfigyelési oktatóanyag](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek az eredetileg üzembe helyezett Wingtip Tickets Platform (WTP) alkalmazásra épülnek](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Elastic Database-ügyfélkódtár](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

