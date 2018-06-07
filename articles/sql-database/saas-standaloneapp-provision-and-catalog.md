---
title: Több-bérlős Szolgáltatottszoftver-oktatóanyag – az Azure SQL Database |} Microsoft Docs
description: A különálló alkalmazás minta használatával biztosítása és a katalógus új bérlők
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: SaaS
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: 0f2495ddc5d5053582d67bd44cdf80d018f79e42
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646153"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>A Szolgáltatottszoftver-mintát bérlőnként alkalmazással biztosítása és a katalógus új bérlők

Ez a cikk ismerteti a kiépítés és a Szolgáltatottszoftver-mintát bérlőnként különálló alkalmazás használatával új bérlő katalogizálni.
Ez a cikk két fő részből áll:
* Fogalmi leírását az üzembe helyezési és az új bérlők katalogizálása
* Egy oktatóanyag, amely kiemeli a minta PowerShell-kódot, amely éri el a kiépítési és katalogizálása
    * Az oktatóprogram a Wingtip jegyek minta SaaS-alkalmazáshoz, az önálló app típusú bérlői mintában igazodó.

## <a name="standalone-application-per-tenant-pattern"></a>Bérlői minta egy önálló alkalmazás
Az önálló app típusú bérlői mintában a több-bérlős SaaS-alkalmazásokhoz több mintái egyike.  Ebben a mintában egy különálló alkalmazás ki van építve, az egyes bérlők számára. Az alkalmazás alkalmazás-szintű összetevők és az SQL-adatbázis foglalja magában.  Minden bérlő alkalmazás gyártója által biztosított előfizetés is telepíthető.  Másik lehetőségként az Azure kínál a [kezelt alkalmazások program](https://docs.microsoft.com/azure/managed-applications/overview) alkalmazás is lehet üzembe helyezett egy bérlői előfizetéshez és a bérlői nevében a szállító által felügyelt. 

   ![alkalmazás / bérlői minta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Amikor egy bérlő számára az alkalmazások központi telepítése, az alkalmazás- és rendszerbeli egy új erőforráscsoportot, a bérlő létre.  Elkülönített erőforrás-csoportok használata elkülöníti az egyes bérlők alkalmazás-erőforrásokat, és így függetlenül kezelhetők. Mindegyik erőforráscsoporton belül minden alkalmazáspéldány van konfigurálva a megfelelő adatbázishoz való közvetlen hozzáféréshez.  Ez a kapcsolat modell más broker kapcsolatok az alkalmazás és az adatbázis közötti katalógus használó mintákat ellenkezőjét szemlélteti.  És mivel nincs erőforrás-megosztás, minden egyes bérlői adatbázisához a csúcsterhelés kezeléséhez elegendő erőforrással rendelkező kell telepíthető. Ebben a mintában általában kevesebb bérlők, SaaS-alkalmazásokhoz használandó ahol nincs tenant nagy hangsúlyt, elkülönítési és kisebb erőforrás költségek hangsúlyt.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>A bérlői katalógussal típusú bérlői mintában az alkalmazással
Míg a bérlő az alkalmazás- és teljesen elkülönített, különböző felügyeleti és elemzés forgatókönyvek bérlők között működik.  Például egy alkalmazás új verziót a sémamódosítás alkalmazása módosítását igényli az egyes bérlői adatbázis sémája. Jelentéskészítés és elemzés forgatókönyvek is szüksége lehet függetlenül attól, ahol központilag telepítették őket összes bérlői adatbázis elérésére.

   ![alkalmazás / bérlői minta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

A bérlői katalógus tárolja a bérlő azonosítója és a bérlői adatbázist, így lesz hozzárendelve a kiszolgáló és az adatbázis nevét azonosítót közötti leképezést.  A Wingtip SaaS-alkalmazás a bérlő azonosítója számítja ki, hogy egy kivonatot bérlőjének a nevével, bár egyéb rendszerek is használható.  Önálló alkalmazások nincs szüksége a katalógus használatával kezelheti a kapcsolatokat, amíg a katalógus más műveletek bérlői adatbázisok készleteit hatókörének használható. Például rugalmas lekérdezés segítségével a katalógus-adatbázisok között, amelyek lekérdezéseket küld a jelentési kereszt-bérlő meg.

## <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár
A Wingtip mintaalkalmazást a katalógus megvalósítja a shard kezelési funkciókat a [Elastic Database ügyféloldali kódtár](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL).  A könyvtár lehetővé teszi az alkalmazás létrehozására, kezelésére és használja a-adatbázisban tárolt shard leképezését. A Wingtip jegyek mintában a katalógus tárolja a *bérlői katalógus* adatbázis.  A szilánkok a bérlőkulcsát, hogy a shard (adatbázis) szolgáltatástérképek adott bérlői adatokat tárolja.  EDCL funkciók kezelése egy *globális shard térkép* a táblákban tárolt a *bérlői katalógus* adatbázis és a *helyi shard térkép* minden shard tárolja.

EDCL függvények hívhatók alkalmazások vagy a PowerShell-parancsfájlok létrehozását és kezelését a shard térkép bejegyzései. Más EDCL funkciók segítségével kéri le a szilánkok készletét, vagy a megfelelő adatbázishoz csatlakozni, a megadott bérlőkulcs. 
    
> [!IMPORTANT] 
> Ne szerkessze az adatokat a katalógus adatbázisban vagy a helyi shard leképezés a bérlő adatbázisokban közvetlenül. Közvetlen frissítések nem támogatottak a magas kockázatú adatsérülés miatt. Ehelyett a hozzárendelési adatok szerkesztéséhez csak EDCL API-k használatával.

## <a name="tenant-provisioning"></a>Bérlő kiépítésének 
Mindegyik bérlő szükséges egy új Azure erőforráscsoport, amely erőforrások belül kiépítése előtt létre kell hozni. A csoport létezik, miután az Azure Resource Manager sablon segítségével az alkalmazás-összetevők és az adatbázis központi telepítése, és adja meg az adatbázis-kapcsolatot. Az adatbázisséma inicializálni a sablon bacpac fájlt importál.  Másik lehetőségként az adatbázis "template" adatbázis másolatának hozhatók létre.  Az adatbázis további majd kezdeti helyszínére adatok frissítése és a katalógus regisztrálni.

## <a name="tutorial"></a>Oktatóanyag

Ezen oktatóanyag segítségével megtanulhatja a következőket:
* A katalógus kiépítése
* A bérlői mintaadatbázisokat központilag telepített regisztrálása során korábban küldje el a katalógusban
* Egy további bérlői kiépíteni, és regisztrálja a katalógusban

Az Azure Resource Manager sablon telepítése és konfigurálja az alkalmazást, a bérlő adatbázis létrehozása és inicializálása azt bacpac fájl majd importálása szolgál. A kérést, előfordulhat, hogy több percig, mielőtt a műveletet kiváltó várósorba kerülnek.

Ez az oktatóanyag végén önálló bérlői alkalmazások, amelynek az egyes adatbázisok a katalógusban regisztrált rendelkezik.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek: 
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A három minta bérlői alkalmazások vannak telepítve. Ezen alkalmazások telepítését a öt percen belül, lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS önálló alkalmazásminta](https://docs.microsoft.com/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Az alkalmazáskatalógus létesítése
Ebben a feladatban megismerheti, hogyan regisztrálhatók a bérlő adatbázisok katalógus kiépítéséhez. Az alábbiakat fogja elvégezni: 
* **A katalógus adatbázis létesítéséhez** az Azure erőforrás-kezelés sablonnal. Az adatbázis bacpac fájl importálásával inicializálva van.  
* **Regisztrálja a bérlőt mintaalkalmazások** korábban telepített.  Mindegyik bérlő regisztrálva van, a bérlő neve kivonatát értékekből összeállított kulcs segítségével.  A bérlő nevét egy bővítmény tábla a katalógus is tárolódik.

1. Nyissa meg a PowerShell ISE *...\Learning Modules\UserConfig.psm* , és frissítse a **\<felhasználói\>** értéket a három minta alkalmazások központi telepítésekor használt érték.  **Mentse a fájlt**.  
1. Nyissa meg a PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* és **$Scenario = 1**. A bérlői katalógus telepíti, és regisztrálja az előre definiált bérlők.

1. A kurzor bárhol tegyen a sor, amely szerint, vegye fel a töréspont `& $PSScriptRoot\New-Catalog.ps1`, majd nyomja le az **F9**.

    ![a nyomkövetés Töréspont beállítása](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Futtassa a parancsfájlt billentyűkombináció lenyomásával **F5**. 
1.  Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépésre a New-Catalog.ps1 parancsfájlba.
1.  A parancsfájl végrehajtása a hibakeresési menüpontok, F10 és F11 segítségével nyomon követni, több mint vagy a lépés neve a funkciók.
    *   PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

A parancsfájl befejeztét követően a katalógus van jelen, és a minta-bérlő regisztrálva lesz. 

Most tekintse meg a létrehozott erőforrások.

1. Nyissa meg a [Azure-portálon](https://portal.azure.com/) , és keresse meg az erőforráscsoportokat.  Nyissa meg a **wingtip-sa-katalógus -\<felhasználói\>**  erőforrás csoportnak, és jegyezze fel a globáliskatalógus-kiszolgálónak és az adatbázis.
1. Nyissa meg az adatbázis a portálon, és válasszon *adatkezelő* a bal oldali menüből.  A bejelentkezési parancsra, és írja be a jelszót = **P@ssword1**.


1. Sémája megismerkedhet a *tenantcatalog* adatbázis.  
   * Az objektumok a `__ShardManagement` séma összes által biztosított a rugalmas adatbázis ügyféloldali Kódtárára.
   * A `Tenants` tábla és `TenantsExtended` nézet hozzáadva a mintában szereplő bővítmények, amelyek bemutatják, hogyan bővítheti a katalógus adni további értéket.
1. A lekérdezés futtatásához `SELECT * FROM dbo.TenantsExtended`.          

   ![adatkezelő](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Ahelyett, hogy az adatkezelő használatával csatlakozhat az adatbázishoz az SQL Server Management Studio. Ehhez csatlakoznia kell a kiszolgáló wingtip- 

    
    Vegye figyelembe, hogy ne módosítsa a katalógus - közvetlenül az adatok mindig használja a shard felügyeleti API-k. 

## <a name="provision-a-new-tenant-application"></a>Egy új bérlő alkalmazás telepítéséhez

Ebben a feladatban megismerheti, hogyan lehet kiépíteni egy bérlő egyetlen alkalmazást. Az alábbiakat fogja elvégezni:  
* **Hozzon létre egy új erőforráscsoportot** a bérlő számára. 
* **Az alkalmazás- és adatbázis kiépítése** be az új erőforráscsoportot az Azure erőforrás-kezelés sablonnal.  Ez a művelet magában foglalja, inicializálja a közös séma- és referenciaadatok adatbázis bacpac fájl importálásával. 
* **Alapszintű bérlői adatait adatbázis inicializálása**. Ez a művelet tartalmaz, amely megadja, hogy a háttereként az események webhelyen fénykép helyszínére típusának megadásával. 
* **Regisztrálja a katalógus-adatbázis az adatbázis**. 

1. Nyissa meg a PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* és **$Scenario = 2**. A bérlő katalógus telepíteni és regisztrálni az előre definiált bérlők

1. A kurzor bárhol tegyen sor 49, amely szerint, a parancsfájl töréspont adja hozzá `& $PSScriptRoot\New-TenantApp.ps1`, majd nyomja le az **F9**.
1. Futtassa a parancsfájlt billentyűkombináció lenyomásával **F5**. 
1.  Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépésre a New-Catalog.ps1 parancsfájlba.
1.  A parancsfájl végrehajtása a hibakeresési menüpontok, F10 és F11 segítségével nyomon követni, több mint vagy a lépés neve a funkciók.

Miután a bérlő van megadva, az új tenanthoz események webhely van megnyitva.

   ![piros maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Az új erőforrások az Azure-portálon létrehozott majd vizsgálhatja meg. 

   ![piros maple verseny erőforrások](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Számlázási leállításához erőforrás csoportok törlése ##

Ha befejezte a minta felfedezése, állítsa le a társított számlázási létrehozott összes erőforráscsoport törlése

## <a name="additional-resources"></a>További források

- Több-bérlős SaaS-adatbázis alkalmazások kapcsolatos további információkért lásd: [kialakítási minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * Ügyfélszoftverek központi telepítése a Wingtip jegyek SaaS önálló alkalmazás.
> * A kiszolgálók és adatbázisok, amelyek az alkalmazás alkotják.
> * Hogyan mintaerőforrásokat leállításához kapcsolódó számlázási törlése.

Ismerje meg az adatbázis-/-bérlő verziójával különböző több-bérlős forgatókönyvek támogatása céljából a katalógus használatáról az [Wingtip jegyek SaaS-alkalmazás](https://docs.microsoft.com/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
