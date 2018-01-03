---
title: "SaaS több-bérlős Azure rendelkezése |} Microsoft Docs"
description: "Megtudhatja, hogyan szeretnék telepíteni, és a katalógus egy Azure SQL Database több-bérlős SaaS-alkalmazás az új bérlők"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: MightyPen
ms.reviewer: billgib;andrela;genemi
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billgib
ms.openlocfilehash: 42bbb6131aa71520410b22af4d74e99a63fe81cf
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Új bérlők biztosítása és a katalógus egy SaaS-alkalmazáshoz szilánkos több-bérlős Azure SQL-adatbázis használata

Ez a cikk ismerteti a kiépítés és az új bérlők katalogizálni a *több-bérlős szilánkos adatbázis* modell vagy -mintát.

Ez a cikk két fő részből áll:

- [Általános vitafórum](#goto_2_conceptual) a telepítés és az új bérlők katalogizálni.

- [Az oktatóanyag](#goto_1_tutorial) , amely kiemeli a PowerShell parancsfájl kódot, amely a kiépítés és katalogizálni éri el.
    - Az oktatóprogram a Wingtip jegyek SaaS-alkalmazáshoz, a több-bérlős szilánkos adatbázis mintát igazítani.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Adatbázis-minta

Ebben a szakaszban, valamint néhány további, hajtsa végre, ismertetik a több-bérlős szilánkos adatbázis mintát elveit.

A több-bérlős szilánkos modellben a táblasémákat belüli egyes adatbázisok bérlői adatokat tároló tábla elsődleges kulcsa a bérlői kulcsot tartalmaznak. A bérlői kulcs lehetővé teszi, hogy minden egyes adatbázis tárolásához, 0, 1 vagy több bérlő. Horizontálisan skálázott adatbázisok használata megkönnyíti a bérlők nagyon nagy számú támogatásához az alkalmazás rendszerhez. A több bérlő számára az adatok egy adatbázisban tárolódik. A bérlők nagy számú a sok szilánkos adatbázis különböző pontjain. A katalógus adatbázis tárolja az egyes bérlők adatbázis leképezése.

#### <a name="isolation-versus-lower-cost"></a>Alacsonyabb költségek és elkülönítés

A bérlő, amely rendelkezik az adatbázis összes maga élvez elkülönítési előnyeit. A bérlő lehet az adatbázis visszaállítása korábbi alatt korlátozza a többi bérlő gyakorolt hatás nélkül. Adatbázis teljesítményének optimalizálása a egy bérlő anélkül, hogy a többi bérlő behatolhat szabályozható. A probléma az elkülönítési költségek több mint egy adatbázis megosztása más bérlők költséggel.

Amikor egy új bérlőt ki van építve, azt megosztása adatbázis más bérlők, vagy a saját új adatbázis felvehetők. Később meggondolja magát, és az adatbázis áthelyezése a másik helyzet.

Az azonos SaaS-alkalmazás költségek vagy az egyes bérlők elszigetelését optimalizálása vegyes több bérlők és egyetlen bérlők adatbázisokkal.

   ![Bérlői katalógus szilánkos több-bérlős adatbázis-alkalmazás](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Bérlői katalógus minta

Ha, hogy minden egyes tartalmaznak-e legalább egy bérlő két vagy több adatbázis, az alkalmazás számára az adatbázis tárolja a bérlő aktuális érdeklő úgy kell rendelkeznie. A katalógus adatbázis tárolja ezt a hozzárendelést.

#### <a name="tenant-key"></a>Bérlőkulcs

Az egyes bérlők számára a Wingtip alkalmazás származtathatók egyedi kulcs, amely a bérlői kulcs. Az alkalmazás a bérlő neve kiolvassa a weblap URL-CÍMÉT. Az alkalmazás csak a nevét, a kulcs beszerzése. Az alkalmazás elérése a katalógus kulcsot használja. A katalógus kereszthivatkozások kívánt adatbázis adatait, amely a bérlői tárolja. Az alkalmazás kapcsolódni az adatbázis-információ használ. Más bérlői kulcs rendszerek is használható.

A katalógus lehetővé teszi a nevét vagy helyét egy bérlői-adatbázisnak a kérelem megszakítása nélkül kiépítése után módosítható. Egy adatbázis több-bérlős modell a katalógus alkalmazkodik a bérlő adatbázisok közötti áthelyezése.

#### <a name="tenant-metadata-beyond-location"></a>Bérlői metaadatok túl helye

A katalógus is jelezheti, hogy karbantartási és egyéb műveletek offline állapotban-e a bérlő. És a katalógus bővíthető további bérlő vagy az adatbázis metaadatait, például a következő elemek tárolására:
- A szolgáltatási rétegben vagy egy adatbázis kiadásában.
- Az adatbázis-séma verziója.
- A bérlő nevét és a szolgáltatásiszint-szerződés (szolgáltatásiszint-megállapodás).
- Információkat, hogy felügyeleti alkalmazás, ügyfél-támogatási vagy devops folyamatok.  

A katalógus is használható több-bérlős jelentéskészítési, séma felügyeletének engedélyezése, és az adatok kibontása elemzés céljából. 

### <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár 

A Wingtip, a katalógus meg van valósítva a *tenantcatalog* adatbázis. A *tenantcatalog* jön létre a Shard felügyeleti szolgáltatását használja, a [rugalmas adatbázis ügyfél könyvtár (EDCL)](sql-database-elastic-database-client-library.md). A könyvtár lehetővé teszi az alkalmazás létrehozására, kezelésére és használja a *shard térkép* -adatbázisban tárolt. A shard térképre kereszthivatkozásokat a shard, azaz a szilánkos adatbázis bérlői kulcsot.

A bérlő kiépítésének, során EDCL funkciók segítségével alkalmazások vagy a PowerShell-parancsfájlok létrehozni a bejegyzéseket a shard térkép. Később a EDCL funkciók segítségével kell a megfelelő adatbázishoz. A EDCL gyorsítótárazza a katalógus-adatbázis a forgalom csökkentése érdekében, és a folyamat, amely felgyorsítása kapcsolódási adatait.

> [!IMPORTANT]
> Tegye *nem* szerkessze az adatokat a katalógus adatbázisban keresztül közvetlen hozzáférést! Közvetlen frissítések nem támogatottak a magas kockázatú adatsérülés miatt. Ehelyett a hozzárendelési adatok szerkesztéséhez csak EDCL API-k használatával.

## <a name="tenant-provisioning-pattern"></a>Bérlői üzembe helyezési minta

#### <a name="checklist"></a>Feladatlista

Ha meg szeretné egy új bérlőt kiépíteni egy meglévő megosztott adatbázist, a megosztott adatbázis kérje meg a következő kérdéseket:
- Van-e elegendő lemezterület az új bérlő?
- Rendelkezik a szükséges hivatkozási adatok rendelkező táblák az új bérlő számára, vagy az adatok felveheti?
- Rendelkezik a megfelelő változatát, az alap séma az új bérlő?
- Van az új tenanthoz megközelíti a megfelelő földrajzi helyet?
- Jelenleg ez a jobb szolgáltatási rétegben az új bérlő?

Ha azt szeretné, hogy a saját adatbázis különíthetők el az új tenanthoz hozhat létre a bérlői előírások teljesítéséhez.

A kiépítés befejezése után, regisztrálnia kell a bérlő a katalógusban. Végül a bérlő leképezési lehet hozzáadni a megfelelő shard hivatkozik.

#### <a name="template-database"></a>Sablon adatbázis

Az adatbázis SQL-parancsfájlok végrehajtása, központi telepítése egy bacpac vagy sablon adatbázis másolása kiépítéséhez. A Wingtip alkalmazások új bérlő adatbázisok létrehozása sablon adatbázis másolása.

Bármely alkalmazás, például a Wingtip fog verzióinformációk. Időnként a Wingtip a módosítások adatbázisba beállítást. Változások a következő elemek a következők lehetnek:
- Új vagy módosított séma.
- Új vagy módosított referenciaadatok.
- Rendszeres adatbázis-karbantartási feladatok alkalmazás optimális teljesítmény érdekében.

A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. A módosítások lehet a jövőben bérlői adatbázisok van szükségük a kiépítési folyamat szóló. Ez a kérdés felfedezte van a további a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Parancsprogramok

Ebben az oktatóanyagban a bérlő üzembe helyezési parancsfájlok támogatja az alábbi esetekben:
- A bérlő átadása egy meglévő megosztott a többi bérlő adatbázisba.
- A bérlő kiépítése a saját adatbázisába.

Bérlői adatok majd inicializálva és a katalógus shard térkép regisztrálni. A mintaalkalmazás, több bérlő tartalmazó adatbázisok kap egy általános nevet, például a *tenants1* vagy *tenants2*. Egy egybérlős tartalmazó adatbázisok szerepelnek a bérlő nevét. A mintában használt különleges elnevezési szabályai amelyeket nem a mintában a legkritikusabb feladata a katalógus használatával egyetlen hozzá kell rendelni az adatbázis nevét.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Az oktatóanyag megkezdése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A bérlő kiépíteni egy több-bérlős adatbázis
> * A bérlő kiépíteni egy bérlői adatbázis
> * Egy kötegben, a bérlő több-bérlős és a bérlői egyetlen kiépíteni
> * Egy adatbázis és a bérlői katalógusba leképezési regisztrálása

#### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás](saas-multitenantdb-get-started-deploy.md)

- Töltse le a Wingtip parancsfájlok és a forráskód:
    - A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárház.
    - Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip parancsfájlok lépéseit. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>A bérlő kiépíteni egy adatbázist *megosztott* a bérlőktől

Ebben a szakaszban a fő kialakítási végzett műveleteket a PowerShell-parancsfájlok listájának megtekintéséhez. A PowerShell ISE-hibakereső majd teljesítéséhez a parancsfájlok a kódban műveletek megtekintéséhez használja.

#### <a name="major-actions-of-provisioning"></a>Kiépítés főbb műveletek

Lépkedjen végig a kiépítési munkafolyamat fő elemei a következők:

- **Az új bérlőkulcs kiszámításához**: kivonatoló függvényt a bérlő neve a bérlői kulcs létrehozásához használt.
- **Ellenőrizze, hogy a bérlői kulcs már létezik-e**: A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
- **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**: A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
- **Bérlői regisztrálja a katalógus**: A bérlőkulcsról és a meglévő tenants1 adatbázis közötti leképezéseket hozzáadódik a katalógusban. 
- **Adja hozzá a bérlő nevét katalógus bővítmény táblához**: helyszínére neve bekerül a bérlők tábla a katalógusban.  A hozzáadást bemutatja, hogyan a katalógus-adatbázis is terjeszthető támogatásához további alkalmazásspecifikus adatait.
- **Nyissa meg az új tenanthoz események lapján**: A *Bushwillow kékek* események lap megnyitása a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>A hibakereső lépései

Ha szeretné megtudni, hogyan a Wingtip alkalmazás valósít meg új bérlő kiépítésének megosztott adatbázisban, töréspont és a munkafolyamaton keresztül lépés hozzáadása:

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*bemutató-ProvisionTenants.ps1* és állítsa be a következő paraméterekkel:
   - **$TenantName** = **Bushwillow kékek**, egy új helyszínére nevét.
   - **$VenueType** = **kékek**, az előre definiált helyszínére típusok egyikét: kékek, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetűk, szóközök nélkül).
   - **$DemoScenario** = **1**, egy olyan megosztott adatbázist a többi bérlő tenant kiépítéséhez.

2. Adja hozzá a töréspont tegyen a kurzort, bárhol, amely szerint a sor 38, sor: *New-bérlői "*, majd nyomja le az **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Futtassa a parancsfájlt billentyűkombináció lenyomásával **F5**.

4. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.

   ![hibakeresés](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Nyomon követni a parancsfájl végrehajtása használatával a **Debug** menü elemeit, **F10** és **F11**, a lépés vagy hívott függvényekké keresztül.

PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>A bérlő által kiépíteni a *saját* adatbázis

#### <a name="major-actions-of-provisioning"></a>Kiépítés főbb műveletek

Közben a parancsfájl-nyomkövetés végighaladhat munkafolyamat fő elemei a következők:

- **Az új bérlőkulcs kiszámításához**: kivonatoló függvényt a bérlő neve a bérlői kulcs létrehozásához használt.
- **Ellenőrizze, hogy a bérlői kulcs már létezik-e**: A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
- **Hozzon létre egy új bérlő adatbázist**: az adatbázis másolása hozza létre a *basetenantdb* adatbázis-Resource Manager-sablon használatával.  Az új adatbázis nevét a bérlő neve alapul.
- **Adatbázis hozzáadása katalógus**: az új bérlő adatbázis egy shard a katalógusban regisztrált.
- **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**: A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
- **Bérlői regisztrálja a katalógus**: az új bérlőkulcs közötti leképezést és a *sequoiasoccer* adatbázis hozzáadódik a katalógusban.
- **Bérlői meg van adva, a katalógus**: helyszínére neve bekerül a bérlők bővítmény tábla a katalógusban.
- **Nyissa meg az új tenanthoz események lapján**: A *Sequoia foci* események lap megnyitása a böngészőben.

   ![események](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>A hibakereső lépései

Amikor a bérlő létrehozása a saját adatbázisában most végezze el a parancsfájl folyamat:

1. Még mindig... \\Tanulási modulok\\ProvisionTenants\\*bemutató-ProvisionTenants.ps1* állítsa be a következő paraméterekkel:
   - **$TenantName** = **sequoia foci**, egy új helyszínére nevét.
   - **$VenueType** = **foci**, az előre definiált helyszínére típusok egyikét: kékek, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetű, szóközök nélkül).
   - **$DemoScenario** = **2**, a bérlő kiépítésének a saját adatbázisába.

2. Adja hozzá az új töréspont tegyen a kurzort, bárhol, amely szerint a sor 57, sor:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Futtassa a parancsfájlt billentyűkombináció lenyomásával **F5**.

4. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.  Használjon **F10** és **F11** ugorja át, és lépjen be a végrehajtási nyomkövetéséhez funkciók.

## <a name="provision-a-batch-of-tenants"></a>Egy kötegben, a bérlő kiépítése

Ebben a gyakorlatban egy kötegelt 17 bérlő látja el. A kötegelt bérlő kiépítése más Wingtip jegyek oktatóanyagok elindítása, így további adatbázisok történő együttműködésre előtt ajánlott.

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*bemutató-ProvisionTenants.ps1* , és módosítsa a *$DemoScenario* 4 paramétert:
   - **$DemoScenario** = **4**, hogy a bérlő köteg kiépíteni olyan megosztott adatbázist.

2. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Ellenőrizze a bérlők telepített készlete 

Ezen a ponton rendelkezik olyan megosztott adatbázist helyezett bérlők és a bérlők a saját adatbázisok helyezhető üzembe. Az Azure portál segítségével vizsgálja meg a létrehozott adatbázisokat. Az a [Azure-portálon](https://portal.azure.com), nyissa meg a **tenants1-mt -\<felhasználói\>**  server keresse meg azt az SQL Server-kiszolgálók listájához.  A **SQL-adatbázisok** lista tartalmaznia kell a megosztott **tenants1** adatbázis és a bérlők számára saját adatbázisban lévő adatbázisok:

   ![adatbázislista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Az Azure-portálon a bérlő adatbázisokat jeleníti meg, amíg nem teszi lehetővé a bérlők tájékozódhat *belül* a megosztott adatbázis. A bérlő teljes listája látható a **események Hub** weblap Wingtip, és keresse meg azt a katalógusban.

#### <a name="using-wingtip-tickets-events-hub-page"></a>A Wingtip jegyek események hub oldal használatával

A böngészőben nyissa meg az események központ lapján (http:events.wingtip-mt.\<felhasználói\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Katalógus-adatbázis használata

A bérlők és az egyes megfelelő adatbázis teljes listáját a katalógus érhető el. Az SQL-nézet van, feltéve, hogy csatlakozik a bérlő neve az adatbázis neve. A nézet szépen mutatja be az érték, amely a metaadatokat, amelyek a katalógus tárolja.
- Az SQL-nézet az tenantcatalog adatbázisban érhető el.
- A bérlő neve a bérlők tábla tárolódik.
- Az adatbázis nevét a rendszer a Shard felügyeleti táblákban tárolja.

1. Az SQL Server Management Studio (SSMS), csatlakozás a bérlők kiszolgálóra **katalógus-mt.\<felhasználói\>. database.windows.net**, bejelentkezéskor = **fejlesztői**, és a jelszó =**P@ssword1**

    ![SSMS kapcsolódási párbeszédpanel](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Az SSMS Object Explorer keresse meg a nézeteket a *tenantcatalog* adatbázis.

3. Kattintson jobb gombbal a nézet a *TenantsExtended* válassza **legfelső 1000 sor kiválasztása**. Vegye figyelembe a különböző bérlőkhöz tartozó bérlői nevét és az adatbázis közötti leképezést.

    ![Az SSMS ExtendedTenants megtekintése](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Ez a szakasz ismerteti, amelyek más létesítési érdekes szabályszerűségeket.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Előre a rugalmas készletek adatbázisok kiépítése

Az előre üzembe helyezési minta arra, hogy rugalmas készletek használatakor számlázási a készlet nem az adatbázisok biztonsági réseket. Így adatbázisokat felveheti egy rugalmas készlethez előtt szükség van kapcsolódik további költség nélkül. Az előre visioning jelentősen csökkenti a bérlő adatbázisba történő telepítéséhez szükséges idő. Előzetes kiosztása adatbázisok száma puffer megfelelő-e a kiépítési várható sebesség tartása szükség szerint módosítható.

#### <a name="auto-provisioning"></a>Automatikus kiépítés

Az automatikus átadásának mintának kiszolgálók készletek és szükség szerint automatikusan adatbázisok kiépítése egy dedikált létesítési szolgáltatást használja. Ezt az automatizálást magában foglalja az adatbázisok rugalmas készletek előzetes kiépítését. És adatbázisok szerelni és törlése, ha létrejön a rugalmas készletek hézagok tetszés szerint a létesítési szolgáltatás is ki kell tölteni.

Ilyen típusú automatizált szolgáltatás egyszerű vagy összetett lehet. Például az automatizálás tudta kezelni a különböző földrajzi létesíteni, és vész-helyreállítási georeplikáció beállítása sikerült. Az Automatikus kiépítés mintájú egy ügyfélalkalmazás vagy parancsfájl volna igényelnie létesítési annak a várólistára létesítési szolgáltatás által feldolgozandó. A parancsfájl volna majd kérdezze le befejezési észleléséhez. Kiépítés előtti használata esetén kérelmek volna a kezelt gyorsan, egy háttér-szolgáltatás ugyanúgy felügyelheti a csere adatbázis kiosztása közben.

## <a name="additional-resources"></a>További források

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
- [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Egy új bérlőt kiépíteni olyan több-bérlős megosztott adatbázist és a saját adatbázis
> * További bérlők kötegelt kiépítése
> * Bérlők kiépítés, és regisztrálja őket a katalógusba részleteit teljesítéséhez

Próbálja meg a [teljesítmény figyelési oktatóanyag](saas-multitenantdb-performance-monitoring.md).

