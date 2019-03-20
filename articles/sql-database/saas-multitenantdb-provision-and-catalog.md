---
title: Üzembe helyezés SaaS több-bérlős Azure rendszerben |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database több-bérlős SaaS-alkalmazásokban az új bérlők kiépítése és katalógusba'
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: d29baaad6090cea5eb31f5f50bba444cb3771155
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835781"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Egy több-bérlős Azure SQL-szilánkokra osztott adatbázis használó SaaS-alkalmazás az új bérlők kiépítése és katalógusba

Ez a cikk ismerteti a kiépítés és az új bérlők katalogizálása egy *több-bérlős, szilánkokra osztott adatbázis* modellt vagy minta.

Ez a cikk két fő részből áll:

- [Általános vitafórum](#goto_2_conceptual) a kiépítés és katalogizálás új bérlők.

- [Az oktatóanyag](#goto_1_tutorial) , amely kiemeli a PowerShell-szkriptkódot, hogy a kiépítés és katalogizálás műveleteket hajtja végre.
  - Az oktatóanyag a Wingtip Tickets SaaS-alkalmazást, igazított, a több-bérlős, szilánkokra osztott adatbázis minta használja.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Adatbázis-minta

Ebben a szakaszban, valamint néhány további, hajtsa végre a, a több-bérlős, szilánkokra osztott adatbázis minta fogalmakat tárgyalják.

A több-bérlős horizontálisan skálázott modellben a táblasémákat belül minden adatbázis az elsődleges kulcsot, amelyen a bérlők adatainak tárolása bérlői kulcsot tartalmaznak. A bérlői kulcs lehetővé teszi, hogy minden egyes adatbázis 0, 1 vagy több bérlő tárolja. Szilánkokra osztott adatbázisok használata megkönnyíti a bérlők nagyon nagy számú támogatásához az alkalmazás rendszerhez. Minden olyan egyetlen bérlő számára az adatok egy adatbázisban tárolódik. A nagy méretű bérlők száma a sok szilánkokra osztott adatbázisok vannak elosztva. A katalógus-adatbázis a hozzárendelés az egyes bérlők adatbázis tárolja.

#### <a name="isolation-versus-lower-cost"></a>Elszigetelés és alacsonyabb költség

Az adatbázis összes maga rendelkező bérlő élvez elkülönítés előnyeit. A bérlő az adatbázis visszaállítása korábbi folyamatban korlátozza a többi bérlő gyakorolt hatás nélkül is rendelkezik. Adatbázis teljesítményének optimalizálása a egy bérlő más bérlők felhasználóival megalkuvások nélkül hangolásával. A probléma oka, hogy elkülönítési költségesebb, mint az adatbázis megosztása más bérlők felhasználóival való költségei.

Amikor egy új bérlő van kiépítve, egy adatbázis megoszthatja más bérlők, vagy is elhelyezhetők a saját új adatbázist. Később meggondolja magát, és az adatbázist áthelyezi az adott helyzethez.

Több bérlő és a egy bérlői adatbázisok vegyes a azonos SaaS-alkalmazás optimalizálása díj és elkülönítés az egyes bérlők számára.

   ![Bérlői katalógus több-bérlős szilánkokra osztott adatbázis-alkalmazás](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Bérlői katalógus minta

Ha két vagy több adatbázist, hogy minden egyes tartalmaz legalább egy bérlőt, az alkalmazás felderítheti, mely adatbázis tárolja a bérlő a jelenlegi lényeges módon kell rendelkeznie. A katalógus-adatbázisban tárolja ezt a hozzárendelést.

#### <a name="tenant-key"></a>bérlőkulcs

Az egyes bérlők számára a Wingtip alkalmazás egyedi kulcs, amely a bérlői kulcs is származik. Az alkalmazás a bérlő neve kiolvassa a weblap URL-CÍMÉT. Az alkalmazás kivonatolja a nevét, a kulcs beszerzése. Az alkalmazás a kulccsal is hozzáférhet a katalógushoz. A katalógus kereszthivatkozások információ az adatbázis, amely a bérlő tárolja. Az alkalmazás használja az adatbázis-információ való csatlakozáshoz. Más bérlői kulcs sémák is használható.

A katalógus használatával lehetővé teszi a nevét vagy a kérelem megszakítása nélkül üzembe helyezés után lehet módosítani a bérlői adatbázis helyét. Az adatbázis több-bérlős modellben a katalógus alkalmazkodik a bérlői adatbázisok közötti áthelyezése.

#### <a name="tenant-metadata-beyond-location"></a>Bérlői metaadatok túli helye

A katalógus e karbantartási és egyéb műveletek offline állapotban-e a bérlőt is utalhat. És a katalógus bővíthetők további bérlő vagy az adatbázis metaadatait, például a következők tárolására:
- A szolgáltatási rétegben vagy az adatbázis kiadásának.
- Az adatbázis-séma verziója.
- A bérlő nevét és az SLA (szolgáltatásiszint-szerződés).
- Információk az Alkalmazáskezelés, ügyfél-támogatási és devops-folyamatokkal engedélyezése.  

A katalógus is használható jelentéskészítési, több-bérlős séma-kezelés engedélyezése, és az adatok kinyerése, elemzési célokra. 

### <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár 

A Wingtip, a katalógus implementálva van a *tenantcatalog* adatbázis. A *tenantcatalog* Szilánkkezelési funkciójának használatával hozható létre a [Elastic Database-Ügyfélkódtárban (EDCL)](sql-database-elastic-database-client-library.md). A kódtár lehetővé teszi az alkalmazás létrehozása, kezelése és használata egy *szegmenstérkép* , amely egy adatbázis tárolja. Horizontálispartíció-térkép a bérlőkulcsot a szegmensben, azaz a szilánkokra osztott adatbázis a kereszthivatkozásokat.

A bérlő kiépítésének során EDCL függvények használhatók alkalmazások vagy a PowerShell-parancsprogramok a bejegyzéseket létrehozni a szegmenstérkép. Később az EDCL függvények használhatók a megfelelő adatbázishoz való csatlakozáshoz. Az EDCL gyorsítótárazza a katalógus-adatbázis forgalmának csökkentése és a határidők kapcsolódáskor való kapcsolódáshoz szükséges adatokat.

> [!IMPORTANT]
> Tegye *nem* szerkesztheti a közvetlen hozzáférés a katalógus-adatbázis adatait! Közvetlen frissítések nem támogatottak a magas kockázatú az adatsérülés miatt. Ehelyett szerkesztésével a társítási adatok csak az EDCL API-k használatával.

## <a name="tenant-provisioning-pattern"></a>Bérlő üzembe helyezési minta

#### <a name="checklist"></a>Ellenőrzőlista

Ha szeretne egy új bérlő kiépítése egy meglévő megosztott adatbázisba, a megosztott adatbázis meg kell kérnie a következő kérdéseket:
- Nem rendelkezik elegendő lemezterület az új bérlő?
- Nem rendelkezik a szükséges referenciaadatok rendelkező táblák az új bérlő számára, vagy az adatok adhatók hozzá?
- Rendelkezik a megfelelő változatát, az alap séma az új bérlő?
- Ennyi az egész a megfelelő földrajzi helyen közel az új bérlő?
- Ennyi az egész a megfelelő szolgáltatásszinten az új bérlő?

Ha azt szeretné, hogy az új bérlőt különíthető el a saját adatbázisát, hozhat létre a bérlő a specifikációk teljesítéséhez.

A kiépítés befejezése után a bérlő kell regisztrálni a katalógusban. Végül a bérlő leképezési való hivatkozáshoz a megfelelő szegmensre is hozzáadhatók.

#### <a name="template-database"></a>Sablon adatbázis

Az adatbázis kiépítése SQL-parancsfájlok végrehajtása, üzembe helyezése egy bacpac vagy sablon adatbázis másolása. A Wingtip alkalmazás adatbázis másolása egy sablon létrehozása új bérlői adatbázisokat.

Minden alkalmazás, például a Wingtip idővel fejlődik. A Wingtip időnként az adatbázis-módosítások szükségesek. Módosítások a következő elemek a következők lehetnek:
- Új vagy módosított séma.
- Új vagy módosított referenciaadatok.
- Rutin adatbázis-karbantartási feladatok optimális teljesítményének biztosítása érdekében.

A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. A módosítások későbbi bérlői adatbázisok lehet szükségük be kell építeni az üzembe helyezési folyamat. Mutatták van megvizsgálta a további a [séma oktatóanyaggal](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Ebben az oktatóanyagban a bérlő üzembe helyezési parancsfájlok támogatja mind a következő esetekben:
- Más bérlők felhasználóival megosztott egy meglévő adatbázist kiépíteni egy bérlőt.
- Bérlők kiépítése a saját adatbázisába.

Bérlői adatok ezután inicializálva, és a katalógus szegmenstérkép regisztrálva. A mintaalkalmazás több bérlő tartalmazó adatbázisok kapnak egy általános nevet, például *tenants1* vagy *tenants2*. Egyetlen új bérlő tartalmazó adatbázisok vannak megadva a bérlő nevét. A mintában használt az adott elnevezési konvencióinak nem részei egy kritikus fontosságú a mintát, a katalógus használata lehetővé teszi, hogy minden hozzá kell rendelni az adatbázis nevét.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Az oktatóanyag megkezdése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új bérlő kiépítéséhez egy több-bérlős adatbázisba
> * Új bérlő kiépítéséhez egy egybérlős adatbázisba
> * Bérlők kötegelt kiépítése több-bérlős és a egybérlős adatbázisba
> * Egy adatbázis és a bérlői leképezést a katalógus regisztrálása

#### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- A Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezés a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás és megismerése](saas-multitenantdb-get-started-deploy.md)

- A Wingtip parancsfájlok és forráskód beszerzéséhez:
    - A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-adattárban.
    - Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip parancsfájlok feloldása. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Új bérlő kiépítéséhez egy adatbázisba *megosztott* más bérlők felhasználóival

Ebben a szakaszban a főbb műveletek üzembe helyezés a PowerShell-szkriptek által elvégzett listáját láthatja. A PowerShell ISE-ben hibakereső majd végig a szkripteken kódban műveletek megtekintéséhez használhatja.

#### <a name="major-actions-of-provisioning"></a>Üzembe helyezésének főbb műveletek

Lépkedjen végig a kiépítési munkafolyamat fő elemei a következők:

- **Az új bérlőkulcs kiszámítása**: A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
- **Ellenőrizze, hogy a bérlői kulcs már**: A katalógus be van jelölve, a kulcs nem már regisztrált biztosításához.
- **Az alapértelmezett bérlői adatbázis bérlői inicializálása**: A bérlői adatbázis frissül, vegye fel az új bérlő adatait.  
- **A katalógus-bérlő regisztrálásához**: Az új bérlőkulcs és a meglévő tenants1-adatbázis közötti hozzáadódik a katalógusban. 
- **A bérlő nevét hozzá egy katalógus bővítmény táblához**: A helyszín neve bekerül a bérlők tábla a katalógusban.  A Hozzáadás bemutatja, hogyan a katalógus-adatbázis támogatására, további alkalmazásspecifikus adatait is kiterjeszthető.
- **Nyissa meg az új bérlő eseményeket felsoroló weblapunkon**: A *Bushwillow Blues* események lap megnyitása a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>A hibakereső lépéseket

Szeretné megtudni, hogyan a Wingtip alkalmazás valósít meg olyan megosztott adatbázist az új bérlőket, adjon hozzá egy töréspontot, és lépegessen végig a munkafolyamaton:

1. Az a *PowerShell ISE-ben*, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* és állítsa be a következő paraméterekkel:
   - **$TenantName** = **Bushwillow Blues**, egy új helyszín neve.
   - **$VenueType** = **blues**, az előre beállított helyszíntípusok egyike: blues, tánc, tánc, jazz, dzsúdó, motorsport, többcélú, opera, rockzene, futball (kisbetűk, szóközök nélkül).
   - **$DemoScenario** = **1**, hogy más bérlők felhasználóival megosztott adatbázist az új bérlő kiépítéséhez.

2. Adjon hozzá egy töréspontot ehhez vigye a kurzort bárhol a arról, hogy a sor 38, sor: *Új bérlő "*, és nyomja le az **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Futtassa a szkriptet lenyomásával **F5**.

4. Miután a parancsfájl végrehajtása a töréspont leáll, nyomja le az ENTER **F11** lépéssel beilleszti a kódba.

   ![hibakeresés](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. A szkript végrehajtását nyomkövetési használatával a **Debug** menüpontok, **F10** és **F11**, vagy be a meghívott függvényeken keresztül. lépésre.

Hibakeresés a PowerShell-parancsfájlokkal kapcsolatos további információkért lásd: [tippekkel szolgál az használatához és hibakereséséhez PowerShell-parancsfájlok](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Az új bérlő kiépítéséhez a *saját* adatbázis

#### <a name="major-actions-of-provisioning"></a>Üzembe helyezésének főbb műveletek

A munkafolyamat a parancsfájl nyomkövetése során végighaladhat fő elemei a következők:

- **Az új bérlőkulcs kiszámítása**: A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
- **Ellenőrizze, hogy a bérlői kulcs már**: A katalógus be van jelölve, a kulcs nem már regisztrált biztosításához.
- **Hozzon létre egy új bérlői adatbázist**: Az adatbázis másolásával létrehozása a *basetenantdb* adatbázis egy Resource Manager-sablon használatával.  Az új adatbázis neve a bérlő neve alapján.
- **Adatbázis-katalógushoz hozzáadott**: Az új bérlői adatbázis regisztrálása a katalógusban szereplő szegmensek szerint.
- **Az alapértelmezett bérlői adatbázis bérlői inicializálása**: A bérlői adatbázis frissül, vegye fel az új bérlő adatait.  
- **A katalógus-bérlő regisztrálásához**: Az új bérlőkulcs közötti leképezést, és a *sequoiasoccer* adatbázis bekerül a katalógusba.
- **Bérlő neve bekerül a katalógusba**: A helyszín neve bekerül a bérlők bővítmény tábla a katalógusban.
- **Nyissa meg az új bérlő eseményeket felsoroló weblapunkon**: A *Sequoia foci* események lap megnyitása a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>A hibakereső lépéseket

Most már a parancsfájl folyamat bemutatására egy bérlő létrehozása a saját adatbázis esetén:

1. Még mindig... \\Tanulási modulok\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* állítsa be a következő paraméterekkel:
   - **$TenantName** = **sequoia foci**, egy új helyszín neve.
   - **$VenueType** = **foci**, az előre beállított helyszíntípusok egyike: blues, tánc, tánc, jazz, dzsúdó, motorsport, többcélú, opera, rockzene, futball (kisbetű, szóközök nélkül).
   - **$DemoScenario** = **2**, az új bérlő kiépítéséhez a saját adatbázisába.

2. Adjon hozzá egy új töréspontot ehhez vigye a kurzort bárhol a arról, hogy a sor 57, sor:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "*, nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Futtassa a szkriptet lenyomásával **F5**.

4. Miután a parancsfájl végrehajtása leáll a töréspontot, nyomja le az ENTER **F11** lépéssel beilleszti a kódba.  Használat **F10** és **F11** ugorja át, és lépjen be a funkciók végrehajtását nyomon követéséhez.

## <a name="provision-a-batch-of-tenants"></a>Bérlők kötegelt kiépítése

Ebben a gyakorlatban 17 bérlők egy kötegét építi ki. A bérlők kötegelt kiépítése más Wingtip Tickets oktatóanyagok előtt, így további adatbázisok használata ajánlott.

1. Az a *PowerShell ISE-ben*, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* , és módosítsa a *$DemoScenario* 4 paramétert:
   - **$DemoScenario** = **4**, bérlők kötegelt kiépítése egy megosztott adatbázisba.

2. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Ellenőrizze a bérlők üzembe helyezett készlete 

Ebben a szakaszban van üzembe helyezve a megosztott adatbázis bérlők és a bérlők számára saját adatbázisok helyezi üzembe. Az Azure portal segítségével megvizsgálhatja a létrehozott adatbázisok. Az a [az Azure portal](https://portal.azure.com), nyissa meg a **tenants1-mt -\<felhasználói\>**  kiszolgálót. Ehhez keresse az SQL Server-kiszolgálók listájához.  A **SQL-adatbázisok** listában szerepelnie kell a megosztott **tenants1** adatbázis és az adatbázisok a bérlők számára, amelyek a saját adatbázisban:

   ![adatbázislista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Az Azure Portalon a bérlői adatbázisokat jeleníti meg, amíg nem engedélyezi a bérlők lásd *belül* a megosztott adatbázis. A teljes listát a bérlők látható a **Eseményközpont** Wingtip, és keresse meg az alkalmazáskatalógus weboldal.

#### <a name="using-wingtip-tickets-events-hub-page"></a>A Wingtip Tickets események eseményközpontba oldal használatával

Az Eseményközpont lapot megnyitna a böngészőben (http:events.wingtip-mt.\<felhasználói\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Katalógus-adatbázis használatával

A bérlők és a megfelelő adatbázist, az egyes teljes listáját a katalógusban érhető el. Egy SQL-nézet van, feltéve, hogy csatlakozik a bérlő neve az adatbázis neve. A nézet szépen kiterjesztése a katalógusban tárolt metaadatok értékét mutatja be.
- Az SQL-nézet az tenantcatalog adatbázisban érhető el.
- A bérlő nevét a bérlők tábla tárolódik.
- Az adatbázis nevét a Szilánkkezelési táblákban tárolódnak.

1. Az SQL Server Management Studio (SSMS), a bérlők kiszolgáló kapcsolódni **katalógus-MT\<felhasználói\>. database.windows.net**, bejelentkezéssel = **fejlesztői**, és a jelszó =  **P\@ssword1**

    ![SSMS kapcsolat párbeszédpanel](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Az SSMS Object Explorerben keresse meg a nézetekben a *tenantcatalog* adatbázis.

3. A jobb gombbal a nézet a *TenantsExtended* válassza **legfelső 1000 sor kiválasztása**. Vegye figyelembe a különböző bérlőkhöz tartozó bérlő neve és az adatbázis közötti leképezést.

    ![ExtendedTenants megtekintése az ssms-ben](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Ez a szakasz bemutatja az egyéb érdekes kiépítési minták.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>A rugalmas készletekben található előzetesen kiépített adatbázisokat

Az előzetesen kiépített mintája kihasználja a tény, hogy a rugalmas készletek használata esetén a számlázás a készlet ne legyen az adatbázisok. Így adatbázisokat felveheti egy rugalmas készlet előtt nem kapcsolódik további költség szükség van rájuk. Ezt előre visioning jelentősen csökkenti az idő az adatbázisba egy új bérlő kiépítéséhez. Tartsa a várható kiépítési aránynak megfelelő puffer igény szerint módosítani lehet az előzetesen kiépített adatbázisok száma.

#### <a name="auto-provisioning"></a>Automatikus üzembe helyezés

Az automatikus kiépítési mintában egy dedikált kiépítő szolgáltatás segítségével kiszolgálók, készletek és adatbázisok szükség szerint automatikusan üzembe helyezése. Ezt az automatizálást magában foglalja a rugalmas készletekben található adatbázisokat előzetes kiépítését. És adatbázisok szerelni és törlése, ha a hiányosságok pótlásában, ez a rugalmas készletekben hoz létre a kiépítő szolgáltatás igény szerint is ki kell tölteni.

Az ilyen típusú automatizált szolgáltatás lehet egyszerű vagy összetett. Például az automation sikerült kezelni, egyszerre több földrajzi, és vész-helyreállítási georeplikációt állíthat be. Az automatikus kiépítési mintában egy ügyfélalkalmazás vagy szkript elküld egy kiépítési kérést egy üzenetsorba a kiépítési szolgáltatás általi feldolgozásának. A parancsfájl kellene majd elindítja a lekérdezést befejezési észleléséhez. Előzetes kiépítését használata esetén reagál a kérésekre, amíg egy háttérszolgáltatást kezelne egy helyettesítő adatbázist üzembe helyezése.

## <a name="additional-resources"></a>További források

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
- [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Megosztott több-bérlős adatbázis és a saját adatbázis egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése
> * Lépkedjen végig a bérlők kiépítése és a katalógusban való regisztrálásukra vonatkozó részletek

Próbálja ki a [teljesítményfigyelési oktatóanyag](saas-multitenantdb-performance-monitoring.md).

