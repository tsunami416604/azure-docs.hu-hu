---
title: Több-bérlős SaaS-oktatóanyag – Azure SQL Database |} A Microsoft Docs
description: Önálló alkalmazásminta használatával új bérlők kiépítése és katalógusba
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 57a8a89c73e2be51e54130d9c37194a7513a47d8
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562514"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Az alkalmazás bérlőnkénti SaaS-minta használatával új bérlők kiépítése és katalógusba

Ez a cikk ismerteti a kiépítés és katalogizálás bérlőnként SaaS-minta a különálló alkalmazás használatával új bérlők.
Ez a cikk két fő részből áll:
* Fogalmi vitafórum üzembe helyezésének és az új bérlők katalogizálása
* Oktatóanyag, amely kiemeli a minta PowerShell-kód, amely a kiépítés és katalogizálás feladatot el
    * Az oktatóanyag a Wingtip Tickets SaaS-mintaalkalmazás, az önálló app bérlői mintában igazodó használja.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás bérlői mintában

Az önálló app bérlői mintában az egyik több minták a több-bérlős SaaS-alkalmazásokhoz.  Ebben a mintában egy önálló alkalmazás minden egyes bérlőhöz van kiépítve. Az alkalmazás magában foglalja a szolgáltatói alkalmazás-összetevők és a egy SQL-adatbázist.  Minden egyes bérlő alkalmazást is üzembe helyezhetők a szállító előfizetésben.  Másik lehetőségként az Azure kínál egy [felügyelt alkalmazások program](https://docs.microsoft.com/azure/managed-applications/overview) , ahol egy alkalmazás is lehet egy bérlő előfizetésben telepített, illetve a bérlő nevében a szállító által kezelt. 

   ![alkalmazás bérlőnkénti minta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Amikor egy bérlő számára az alkalmazás telepítését a az alkalmazás- és vannak kiépítve egy új erőforráscsoportot létrehozni a bérlő számára.  Külön erőforráscsoportok használata elkülöníti az egyes bérlők alkalmazásokra vonatkozó erőforrásokhoz, és lehetővé teszi, hogy egymástól függetlenül kell kezelni. Mindegyik erőforráscsoporton belül az egyes alkalmazáspéldányokról közvetlenül elérni a megfelelő adatbázist van konfigurálva.  Ez a kapcsolat modell ellenkezőjét más mintákat, amelyek a katalógus átvitelszervező-kapcsolatokra az alkalmazás és az adatbázis között.  És mivel nincs erőforrás-megosztást, minden bérlői adatbázis ki kell építenie annak maximális terhelés kezeléséhez elegendő erőforrással rendelkező. Ez a minta általában kevesebb bérlő, SaaS-alkalmazásokhoz használható, ha van a bérlőn nagy hangsúlyt elkülönítési és erőforrás-használati díjak kevesebb figyelmet fordítva.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Bérlői katalógus használata az alkalmazás a bérlő mintában

Habár minden bérlő az alkalmazás- és teljes mértékben elkülönített, különböző kezelési és analitikai forgatókönyvet bérlők között működik.  Például az alkalmazás új kiadása séma változását alkalmazása sémáját, minden bérlői adatbázis módosítását igényli. Jelentéskészítési és elemzési forgatókönyvek is szüksége lehet függetlenül, melyekre telepítve vannak az összes bérlői adatbázison.

   ![alkalmazás bérlőnkénti minta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

A bérlői katalógus tartalmazza a bérlő azonosítóját és a egy bérlői adatbázis, így a kiszolgáló és az adatbázis nevének feloldása azonosítót közötti leképezést.  A Wingtip SaaS-alkalmazás, az a bérlőazonosító számítja ki, hogy egy kivonatot a bérlő nevéből, bár egyéb sémák is használhatók.  Önálló alkalmazásokat nem kell a katalógus kapcsolatok kezeléséhez, míg a katalógus más műveletek körét a bérlői adatbázisok hatókörének beállításához használható. Például rugalmas lekérdezés használatával a katalógus határozza meg a bérlők közötti jelentéskészítés elosztott lekérdezések között, amelyek adatbázisok körét.

## <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár

A Wingtip mintaalkalmazásban valósul meg a katalógusban lévő szegmensek kezelési funkciókat a [Elastic Database-Ügyfélkódtár](sql-database-elastic-database-client-library.md) (EDCL).  A kódtár lehetővé teszi, hogy az alkalmazás létrehozása, kezelése és használata egy szegmenstérképet, amely egy adatbázisban vannak tárolva. A Wingtip Tickets mintát, a katalógus tárolja a *bérlői katalógus* adatbázis.  A szilánkleképezések a bérlők adatainak tárolása a bérlőkulcsot a szegmenshez (adatbázis).  Az EDCL funkciók kezelése egy *globális szegmenstérkép* a táblákban tárolt a *bérlői katalógus* adatbázis és a egy *helyi szegmenstérkép* minden egyes szegmens tárolná.

Az EDCL funkciók alkalmazások vagy a PowerShell-parancsfájlok létrehozását és kezelését a szegmenstérkép bejegyzést is meghívható. Az EDCL függvényekkel használható kérje le a szegmensek készletét, vagy a megfelelő adatbázishoz csatlakozni, a megadott bérlői kulccsá. 

> [!IMPORTANT]
> Ne módosítsa a katalógus-adatbázisban lévő adatok vagy a bérlői adatbázisok a helyi horizontális skálázási térképet közvetlenül. Közvetlen frissítések nem támogatottak a magas kockázatú az adatsérülés miatt. Ehelyett szerkesztésével a társítási adatok csak az EDCL API-k használatával.

## <a name="tenant-provisioning"></a>A bérlő kiépítésének 

Minden egyes bérlőhöz van szükség egy új Azure-erőforráscsoportot, amely a benne lévő erőforrások kiépítése előtt létre kell hozni. Miután az erőforráscsoport létezik, egy Azure Resource Management-sablon használható az alkalmazás-összetevők és az adatbázis üzembe helyezése, és adja meg az adatbázis-kapcsolat. Az adatbázisséma inicializálása, a sablon importálhatja egy bacpac-fájlba.  Az adatbázis azt is megteheti, mint a "sablon" adatbázisának egy másolatát is létrehozható.  Adatbázis további majd kezdeti helyszín adatokkal frissíti, regisztrálása a katalógusban.

## <a name="tutorial"></a>Oktatóanyag

Ezen oktatóanyag segítségével megtanulhatja a következőket:

* A katalógus kiépítése
* A minta bérlői adatbázisok üzembe helyezett regisztrálása a katalógusban korábban
* Egy további új bérlő kiépítéséhez, és regisztrálja a katalógusban

Az Azure Resource Manager-sablon üzembe helyezése és az alkalmazás konfigurálása a, a bérlői adatbázis létrehozása és a egy bacpac-fájlba inicializálása, majd importálja szolgál. Az importálási kérelem előfordulhat, hogy nem helyezi várólistára, a több percig, mielőtt actioned.

Ez az oktatóanyag végén önálló bérlői alkalmazások, a katalógusban regisztrált minden adatbázissal rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek: 

* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A három minta bérlői alkalmazások üzembe vannak helyezve. Ezek az alkalmazások telepítéséről kevesebb mint öt perc alatt: [üzembe helyezés és Fedezze fel a Wingtip Tickets SaaS önálló alkalmazás minta](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>A katalógus kiépítése

Ebben a feladatban megismerheti, hogyan építheti ki a katalógus regisztrálható az összes bérlői adatbázison. Az alábbiakat fogja elvégezni: 

* **A katalógus-adatbázis kiépítése** az Azure resource management-sablonnal. Az adatbázis inicializálása egy bacpac-fájl importálásával.  
* **A mintaalkalmazások bérlői regisztrálásához** korábban üzembe helyezett.  Minden egyes bérlő regisztrálva lett a bérlő neve kivonatát értékekből összeállított kulcs használatával.  A bérlő nevét is tárolja, a katalógus-bővítmény táblában.

1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\UserConfig.psm* és frissítheti a **\<felhasználói\>** érték, a három minta-alkalmazás üzembe helyezésekor használt értékkel.  **Mentse a fájlt**.  
1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* és **$Scenario = 1**. A bérlői katalógus üzembe, és regisztrálja az előre definiált bérlők számára.

1. Adjon hozzá egy töréspontot ehhez vigye a kurzort bárhol, amely szerint, a sor `& $PSScriptRoot\New-Catalog.ps1`, és nyomja le az **F9**.

    ![egy töréspontot a nyomkövetés beállítása](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Futtassa a szkriptet lenyomásával **F5**. 
1.  Miután a parancsfájl végrehajtása a töréspont leáll, nyomja le az ENTER **F11** lépéssel a New-Catalog.ps1 parancsfájlba.
1.  Nyomon követheti a szkript végrehajtását, F10 és F11, a hibakeresési menüpontok segítségével vagy az meghívott függvényeken keresztül. lépésre.
    *   Hibakeresés a PowerShell-parancsfájlokkal kapcsolatos további információkért lásd: [tippekkel szolgál az használatához és hibakereséséhez PowerShell-parancsfájlok](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Miután a parancsfájl futása befejeződött, a katalógus fog létezik, és regisztrálva lesz az összes minta bérlővel. 

Most nézzük meg a létrehozott erőforrásokat.

1. Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg az erőforráscsoportot.  Nyissa meg a **wingtip-sa-katalógus -\<felhasználói\>**  erőforrás csoportot, és jegyezze fel a globáliskatalógus-kiszolgálónak és az adatbázis.
1. Nyissa meg az adatbázis a portálon, válassza a *adatkezelő* elemet a bal oldali menüben.  Kattintson a bejelentkezési parancsot, és írja be a jelszót = **P@ssword1**.


1. Ismerje meg, a sémát a *tenantcatalog* adatbázis.  
   * Az objektumok a `__ShardManagement` séma összes által biztosított az Elastic Database-Ügyfélkódtár.
   * A `Tenants` tábla és `TenantsExtended` nézet bővítmény hozzáadva a minta, amelyek bemutatják, hogyan terjesztheti ki a katalógust, ezzel további értéket adjon meg.
1. A lekérdezés futtatásához `SELECT * FROM dbo.TenantsExtended`.          

   ![adatkezelő](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Az adatkezelő segítségével alternatívájaként csatlakozhat az adatbázishoz az SQL Server Management Studióval. Ehhez a kiszolgáló wingtip-csatlakozás 

    
    Vegye figyelembe, hogy ne módosítsa a katalógus - közvetlenül az adatok mindig használja az API-k szilánkkezelési. 

## <a name="provision-a-new-tenant-application"></a>Egy új bérlő alkalmazás üzembe helyezése

Ebben a feladatban megismerheti, hogyan helyezhet üzembe egy egybérlős alkalmazást. Az alábbiakat fogja elvégezni:  

* **Hozzon létre egy új erőforráscsoportot** a bérlő számára. 
* **Az alkalmazás- és adatbázis üzembe helyezése** az Azure resource management-sablonnal új erőforrás-csoportba.  Ez a művelet magában foglalja az adatbázis közös sémával és referenciaadatokkal inicializálása egy bacpac-fájl importálásával. 
* **Az adatbázis az alapszintű bérlőinformációk inicializálása**. Ez a művelet magában foglalja a helyszín típusának felhasználásával, amely megadja, hogy a fényképet, a háttérben, az események webhelyen használt megadása. 
* **A katalógus-adatbázis az adatbázis regisztrálásához**. 

1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* és **$Scenario = 2**. A bérlői katalógus telepíti és regisztrálja az előre definiált bérlők

1. Adjon hozzá egy töréspontot a szkriptben ehhez vigye a kurzort bárhol feliratú, 49 sor `& $PSScriptRoot\New-TenantApp.ps1`, és nyomja le az **F9**.
1. Futtassa a szkriptet lenyomásával **F5**. 
1.  Miután a parancsfájl végrehajtása a töréspont leáll, nyomja le az ENTER **F11** lépéssel a New-Catalog.ps1 parancsfájlba.
1.  Nyomon követheti a szkript végrehajtását, F10 és F11, a hibakeresési menüpontok segítségével vagy az meghívott függvényeken keresztül. lépésre.

Után a bérlő kiépítését követően az új bérlő események webhely megnyitásakor.

   ![Red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Ezután vizsgálhatja meg az Azure Portalon létrehozott új erőforrásokat. 

   ![Red maple verseny erőforrások](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Számlázás megszüntetéséhez törölje az erőforráscsoportok

Miután végzett, a minta elemzésével, a kapcsolódó számlázások leállításához létrehozott összes erőforráscsoport törlése.

## <a name="additional-resources"></a>További források

- Több-bérlős SaaS-adatbázis alkalmazások kapcsolatos további információkért lásd: [tervezési minták több-bérlős SaaS-alkalmazások](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip Tickets SaaS önálló alkalmazás telepítésének módjáról.
> * A kiszolgálók és adatbázisok az alkalmazás alkotó.
> * Hogyan törlése a kapcsolódó számlázások leállításához.

Megismerheti a bérlőnkénti adatbázis verzióját használja, különféle több-bérlős forgatókönyvek támogatásához a katalógus használatáról az [Wingtip Tickets SaaS-alkalmazás](saas-dbpertenant-wingtip-app-overview.md).  
