---
title: Több-bérlős SaaS-oktatóanyag
description: Új bérlők kiépítése és katalógusa az önálló alkalmazásmintával
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133131"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Új bérlők kiépítése és katalógusa bérlőnkénti SaaS-minta használatával

Ez a cikk ismerteti az új bérlők kiépítése és katalogizálása az önálló alkalmazás bérlőnként SaaS-minta használatával.
Ez a cikk két fő részből áll:
* Az új bérlők kiépítésének és katalogizálásának fogalmi megvitatása
* Egy oktatóanyag, amely kiemeli a minta PowerShell-kódot, amely megvalósítja a kiépítést és a katalogizálást
    * Az oktatóanyag a Wingtip jegyek minta SaaS-alkalmazást használja, amely bérlői mintánként az önálló alkalmazáshoz igazodik.

## <a name="standalone-application-per-tenant-pattern"></a>Önálló alkalmazás bérlői mintánként

Az önálló alkalmazás bérlői mintánként a több-bérlős SaaS-alkalmazások számos mintájának egyike.  Ebben a mintában egy önálló alkalmazás van kiépítve minden bérlő. Az alkalmazás alkalmazásszintű összetevőkből és EGY SQL-adatbázisból áll.  Minden bérlői alkalmazás telepíthető a szállító előfizetésében.  Másik lehetőségként az Azure egy [felügyelt alkalmazási programot](https://docs.microsoft.com/azure/managed-applications/overview) is kínál, amelyben egy alkalmazás üzembe helyezhető egy bérlő előfizetésében, és a bérlő nevében a szállító kezeli.

   ![bérlőnkénti alkalmazásminta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Egy bérlői alkalmazás üzembe helyezésekor az alkalmazás és az adatbázis a bérlő számára létrehozott új erőforráscsoportban van kiépítve.  Külön erőforráscsoportok használatával elkülöníti az egyes bérlői alkalmazás-erőforrásokat, és lehetővé teszi, hogy egymástól függetlenül kell kezelni. Az egyes erőforráscsoportokon belül minden alkalmazáspéldány úgy van konfigurálva, hogy közvetlenül férjen hozzá a megfelelő adatbázishoz.  Ez a kapcsolatmodell ellentétben áll más mintákkal, amelyek katalógust használnak az alkalmazás és az adatbázis közötti kapcsolatok közvetítéséhez.  És mivel nincs erőforrás-megosztás, minden bérlői adatbázist ki kell építeni elegendő erőforrással a maximális terhelés kezeléséhez. Ez a minta általában kevesebb bérlővel rendelkező SaaS-alkalmazásokhoz használatos, ahol nagy hangsúlyt fektet nek a bérlők elkülönítésére, és kevesebb hangsúlyt fektetnek az erőforrásköltségekre.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Bérlői katalógus használata bérlői mintánként az alkalmazással

Bár minden bérlő i app és adatbázis teljesen elkülönítve, különböző felügyeleti és elemzési forgatókönyvek működhet a bérlők között.  Például egy séma módosítása alkalmazása az alkalmazás új kiadásához az egyes bérlői adatbázisok sémájának módosítását igényli. Jelentéskészítési és elemzési forgatókönyvek is szükség lehet az összes bérlői adatbázishoz való hozzáférést, függetlenül attól, hogy hol vannak telepítve.

   ![bérlőnkénti alkalmazásminta](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

A bérlői katalógus rendelkezik egy bérlői azonosító és egy bérlői adatbázis közötti leképezéssel, amely lehetővé teszi az azonosító kiszolgálóra és adatbázisnévre való feloldását.  A Wingtip SaaS alkalmazásban a bérlői azonosító a bérlő nevének kivonataként kerül kiszámításra, bár más sémák is használhatók.  Míg az önálló alkalmazások nem kell a katalógus a kapcsolatok kezeléséhez, a katalógus segítségével más műveletek et a bérlői adatbázisok készletére. Például rugalmas lekérdezés használhatja a katalógust, hogy meghatározza az adatbázisok, amelyek között lekérdezések vannak elosztva a bérlők közötti jelentéskészítés.

## <a name="elastic-database-client-library"></a>Rugalmas adatbázis-ügyféltár

A Wingtip mintaalkalmazásban a katalógust a rugalmas [adatbázis-ügyfélkódtár](sql-database-elastic-database-client-library.md) (EDCL) shard felügyeleti funkciói valósítják meg.  A tár lehetővé teszi, hogy egy alkalmazás hozzon létre, kezelje és használja az adatbázisban tárolt szegmenstérképet. A Wingtip jegyek mintában a katalógus a *bérlői katalógus* adatbázisában tárolódik.  A shard leképezi a bérlői kulcsot a shard (adatbázis), amelyben a bérlő adatait tárolja.  AZ EDCL-függvények a *bérlői katalógus* adatbázistábláiban tárolt *globális szegmenstérképet* és az egyes szegmensekben tárolt *helyi szegmenstérképet* kezelik.

EDCL-függvények hívhatók meg alkalmazásokból vagy PowerShell-parancsfájlokból a shard térkép bejegyzéseinek létrehozásához és kezeléséhez. Más EDCL-függvények segítségével lekérheti a szegmensek készletét, vagy csatlakozhat az adott bérlői kulcs megfelelő adatbázisához.

> [!IMPORTANT]
> Ne szerkesztze az adatokat a katalógus-adatbázisban vagy a helyi shard térkép a bérlői adatbázisokban közvetlenül. A közvetlen frissítések nem támogatottak az adatsérülés magas kockázata miatt. Ehelyett csak EDCL API-k használatával szerkesztse a leképezési adatokat.

## <a name="tenant-provisioning"></a>Bérlői kiépítés

Minden bérlőnek szüksége van egy új Azure-erőforráscsoportra, amelyet létre kell hozni, mielőtt erőforrásokat lehetne kiépíteni rajta belül. Miután az erőforráscsoport létezik, egy Azure Resource Management sablon használható az alkalmazás-összetevők és az adatbázis üzembe helyezéséhez, majd konfigurálja az adatbázis-kapcsolatot. Az adatbázisséma inicializálásához a sablon importálhat egy bacpac fájlt.  Másik lehetőségként az adatbázis "sablon" adatbázis másolataként is létrehozható.  Az adatbázis ezután tovább frissül a kezdeti helyszín adatokkal, és regisztrálva van a katalógusban.

## <a name="tutorial"></a>Oktatóanyag

Ezen oktatóanyag segítségével megtanulhatja a következőket:

* Katalógus kiépítése
* A katalógusban korábban üzembe helyezett bérlői mintaadatbázisok regisztrálása
* További bérlő kiépítése és regisztrálása a katalógusban

Az Azure Resource Manager-sablon az alkalmazás üzembe helyezésére és konfigurálására, a bérlői adatbázis létrehozására, majd egy bacpac fájl importálására szolgál annak inicializálásához. Az importálási kérelem a művelet végrehajtása előtt néhány percig várakozhat.

Az oktatóanyag végén önálló bérlői alkalmazások készletével rendelkezik, és minden adatbázis regisztrálva van a katalógusban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* A három minta bérlői alkalmazások vannak telepítve. Ha ezekaz alkalmazásokat kevesebb mint öt perc alatt szeretné telepíteni, [olvassa el a Wingtip tickets SaaS önálló alkalmazásminta telepítése és feltárása című témakört.](saas-standaloneapp-get-started-deploy.md)

## <a name="provision-the-catalog"></a>A katalógus kiépítése

Ebben a feladatban megtudhatja, hogyan építheti ki az összes bérlői adatbázis regisztrálásához használt katalógust. Az alábbiakat fogja elvégezni:

* **A katalógus-adatbázis kiépítése** egy Azure erőforrás-kezelési sablon használatával. Az adatbázis inicializálása bacpac fájl importálásával történik.
* Regisztrálja a korábban üzembe helyezett **bérlői mintaalkalmazásokat.**  Minden bérlő regisztrálva van egy kulcs alapján a bérlő nevének kivonatából.  A bérlő neve is tárolja a bővítmény tábla a katalógusban.

1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\UserConfig.psm webhelyet,* és frissítse a ** \<felhasználói\> ** értéket a három mintaalkalmazás telepítésekor használt értékre.  **Mentse a fájlt**.
1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1 webhelyet,* és állítsa be **a $Scenario = 1**értéket. Telepítse a bérlői katalógust, és regisztrálja az előre definiált bérlők.

1. Adjon hozzá egy töréspontot úgy, hogy a `& $PSScriptRoot\New-Catalog.ps1`kurzort a sor tetszőleges pontjára helyezi, majd nyomja le az **F9 billentyűt.**

    ![töréspont beállítása a nyomkövetéshez](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Futtassa a parancsfájlt az **F5**billentyű lenyomásával.
1.  Miután a parancsfájlvégrehajtása leáll a töréspontnál, nyomja le az **F11 billentyűt** az Új katalógus.ps1 parancsfájlba való lépéshez.
1.  A parancsfájl végrehajtásának nyomon követése az F10 és F11 Hibakeresési menü beállításaival, hogy átlépjen a hívott függvények között.
    *   A PowerShell-parancsfájlok hibakereséséről a [PowerShell-parancsfájlok használatával és hibakeresésével](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)kapcsolatos tippek című témakörben olvashat bővebben.

A parancsfájl befejezése után a katalógus létezni fog, és az összes minta bérlő regisztrálva lesz.

Most tekintse meg a létrehozott erőforrásokat.

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com/) és böngésszen az erőforráscsoportok között.  Nyissa meg a **wingtip-sa-catalog-user\<\> ** erőforráscsoportot, és jegyezze fel a katalóguskiszolgálót és -adatbázist.
1. Nyissa meg az adatbázist a portálon, és válassza a bal oldali menü *Adatkezelő* parancsát.  Kattintson a Bejelentkezés parancsra, majd írja be a Password = **P\@ssword1 parancsot.**


1. Fedezze fel a *tenantcatalog-adatbázis* sémáját.
   * A séma objektumait a `__ShardManagement` rugalmas adatbázis-ügyféltár biztosítja.
   * A `Tenants` tábla `TenantsExtended` és a nézet olyan bővítmények, amelyeket a mintában adtak hozzá, amelyek bemutatják, hogyan bővítheti ki a katalógust további érték biztosításához.
1. Futtassa `SELECT * FROM dbo.TenantsExtended`a lekérdezést, .

   ![adatkezelő](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Az Adatkezelő használatának alternatívájaként az SQL Server Management Studio kiszolgálóról csatlakozhat az adatbázishoz. Ehhez csatlakozzon a szerver wingtip-


    Vegye figyelembe, hogy az adatokat ne közvetlenül a katalógusban – mindig használja a szegmensfelügyeleti API-kat.

## <a name="provision-a-new-tenant-application"></a>Új bérlői alkalmazás kiépítése

Ebben a feladatban megtudhatja, hogyan létesítheti ki az egyetlen bérlői alkalmazást. Az alábbiakat fogja elvégezni:

* **Hozzon létre egy új erőforráscsoportot** a bérlő számára.
* **Az alkalmazás és az adatbázis kiépítése** az új erőforráscsoportba egy Azure erőforrás-kezelési sablon használatával.  Ez a művelet magában foglalja az adatbázis inicializálását közös sémával és hivatkozási adatokkal egy bacpac fájl importálásával.
* **Az adatbázis inicializálása alapvető bérlői adatokkal.** Ez a művelet magában foglalja a helyszín típusának megadását, amely meghatározza az események webhelyén háttérként használt fényképet.
* **Regisztrálja az adatbázist a katalógusadatbázisban**.

1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1 parancsot,* és állítsa **be a $Scenario = 2**értéket. A bérlői katalógus üzembe helyezése és az előre definiált bérlők regisztrálása

1. Adjon hozzá egy töréspontot a szkripthez úgy, hogy a `& $PSScriptRoot\New-TenantApp.ps1`kurzort a 49-es vonalon bárhová behelyezi, amely azt mondja, majd nyomja le **az F9 billentyűt.**
1. Futtassa a parancsfájlt az **F5**billentyű lenyomásával.
1.  Miután a parancsfájlvégrehajtása leáll a töréspontnál, nyomja le az **F11 billentyűt** az Új katalógus.ps1 parancsfájlba való lépéshez.
1.  A parancsfájl végrehajtásának nyomon követése az F10 és F11 Hibakeresési menü beállításaival, hogy átlépjen a hívott függvények között.

A bérlő kiépítése után megnyílik az új bérlő eseménywebhelye.

   ![piros juhar verseny](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Ezután ellenőrizheti az Azure Portalon létrehozott új erőforrásokat.

   ![red juhar versenyautó források](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>A számlázás leállításához törölje az erőforráscsoportokat

Miután befejezte a minta feltárását, törölje az összes létrehozott erőforráscsoportot a társított számlázás leállításához.

## <a name="additional-resources"></a>További források

- Ha többet szeretne megtudni a több-bérlős SaaS-adatbázis-alkalmazásokról, olvassa el [a több-bérlős SaaS-alkalmazások tervezési mintái című témakört.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> * A Wingtip jegyek SaaS önálló alkalmazás üzembe helyezése.
> * Az alkalmazást kiszolgálókés adatbázisok.
> * Mintaforrások törlése a kapcsolódó számlázás leállításához.

Megismerheti, hogyan használják a katalógust a különböző bérlők közötti forgatókönyvek támogatására a [Wingtip Tickets SaaS alkalmazás](saas-dbpertenant-wingtip-app-overview.md)bérlőnkénti adatbázis-bérlős verziójával.
