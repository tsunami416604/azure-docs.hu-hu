---
title: "Kiosztását, és a katalógus egy SaaS-alkalmazáshoz több-bérlős Azure SQL adatbázis SQL-adatbázis használata az új bérlők |} Microsoft Docs"
description: "Megtudhatja, hogyan szeretnék telepíteni, és a katalógus egy Azure SQL Database több-bérlős SaaS-alkalmazás az új bérlők"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: f6707b85cc80178da663d7e2b95eeb5c9550789c
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-multi-tenant-sql-database"></a>Új bérlők biztosítása és a katalógus egy SaaS-alkalmazáshoz több-bérlős SQL-adatbázis használata

Ebben az oktatóanyagban elsajátíthatja a létrehozásához, és a bérlők katalogizálni a szilánkos több-bérlős adatbázismodell az használatakor mintájával kapcsolatos. 

Egy több-bérlős séma lehetővé teszi több bérlő egyetlen adatbázisban tárolja. Nagyszámú bérlők támogatása érdekében bérlői adatok több szegmensben osztják vagy adatbázisok terjesztése. Az adatokat bármely egy bérlő mindig teljes tartalmaz egy adatbázist.  A katalógus adatbázisokhoz bérlők leképezése tárolására szolgál.   

Választhatja azt is, bizonyos adatbázisok esetén csak egyetlen bérlői feltöltéséhez. Több bérlő rendelkező adatbázisok csökkenti a bérlők elszigetelésére bérlőnként alacsonyabb költségekkel alkalmazást.  Csak egyetlen bérlői rendelkező adatbázisok elkülönítési keresztül költség alkalmazást.  A több bérlő és egyetlen bérlők adatbázisok is keverhetők költségek vagy az egyes bérlők elszigetelését optimalizálása egyetlen SaaS-alkalmazásokban. Bérlők a saját adatbázis üzembe helyezve, vagy később áthelyezhető a saját adatbázis adható meg.

   ![Bérlői katalógus szilánkos több-bérlős adatbázis-alkalmazás](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Bérlői katalógus minta

Ha bérlői adatok több adatbázis között van elosztva, fontos tudni, hogy minden bérlő adatok tárolására. Katalógus-adatbázis tárolja a bérlő és a hozzá kapcsolódó adatokat tároló adatbázis közötti leképezést.

Mindegyik bérlő azonosít egy kulcsot a katalógusban. A Wingtip jegyek Szolgáltatottszoftver-alkalmazásoknál, az a bérlői kulcs formátuma a kivonatát, a bérlő neve. Ez lehetővé teszi az alkalmazás a bérlő neve kinyerése egy weblap URL-CÍMÉT, és használja ezt a megfelelő adatbázishoz való kapcsolódáshoz. Más bérlői kulcs rendszerek is használható.

A katalógus lehetővé teszi a nevét vagy helyét egy bérlői-adatbázisnak a kérelem megszakítása nélkül kiépítése után módosítható.  Egy adatbázis több-bérlős modell ez alkalmazkodik a bérlő adatbázisok közötti áthelyezése.  A katalógus alkalmazáshoz való jelzi, hogy karbantartási és egyéb műveletek offline állapotban-e a bérlő is használható.

A katalógus bővíthető további bérlő vagy az adatbázis metaadatait, például a réteg a rendszer vagy egy adatbázist, adatbázis sémaverziója, bérlő neve és service-csomag vagy SLA-t és egyéb információkat Alkalmazáskezelés, ügyfél-támogatási vagy devops engedélyezése parancsra folyamatok.  

A katalógus is használható több-bérlős jelentéskészítési, séma felügyeletének engedélyezése, és az adatok kibontása elemzés céljából. 

### <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár 
Az a Wingtip jegyek SaaS-alkalmazásokhoz, a katalógus meg van valósítva a *tenantcatalog* adatbázis, a Shard felügyeleti szolgáltatását használja, a [rugalmas adatbázis ügyfél könyvtár (EDCL)](sql-database-elastic-database-client-library.md). A könyvtár lehetővé teszi az alkalmazás létrehozására, kezelésére és használja az adatbázis biztonsági "shard térkép". A szilánkok térkép szilánkok (adatbázisok) és a kulcsok (bérlőkkel) és a szilánkok leképezése listáját tartalmazza.  EDCL funkciók is használható alkalmazások vagy a PowerShell-parancsfájlok során bérlői létesítési bejegyzéseket a shard leképezés létrehozásához, és újabb verziók, a megfelelő adatbázishoz való kapcsolódáshoz. A könyvtár gyorsítótárazza a kapcsolódási adatokat, hogy a katalógus-adatbázis a forgalom csökkentése érdekében, és a kapcsolat sebessége. 

> [!IMPORTANT]
> A társítási adatok hozzáférhetők a katalógus-adatbázisban, de *ne módosítsa őket*! A társítási adatokat kizárólag az Elastic Database-ügyfélkódtár API-jaival szerkessze. A társítási adatok közvetlen módosítása a katalógus sérülésének kockázatát hordozza magában, ezért nem támogatott.


## <a name="tenant-provisioning-pattern"></a>Bérlői üzembe helyezési minta

Egy új bérlőt a szilánkos több-bérlős adatbázismodell létesítésekor először kell meghatározni, ha a bérlő nem áll át az olyan megosztott adatbázist vagy a saját adatbázis megadott. Ha olyan megosztott adatbázist, azt kell meghatározni, ha nincs hely egy meglévő adatbázist, vagy egy új adatbázist van szükség. Ha szükség van egy új adatbázist, azt kell építhető ki a megfelelő helyet és a szolgáltatási rétegben, megfelelő séma- és referenciainformációkat adatokkal inicializálva, és regisztrálja a katalógusban. Végül a bérlő leképezési lehet hozzáadni a megfelelő shard hivatkozik.

Adatbázis-kiépítés legyen elérhető SQL-parancsfájlok végrehajtása, központi telepítése egy bacpac vagy sablon adatbázis másolása. A Wingtip jegyek SaaS-alkalmazásokhoz új bérlő adatbázisok létrehozása sablon adatbázis másolása.

A létesítési módszer adatbázis kell értelmezhető átfogó a séma-kezelési stratégiában, amely biztosítania kell, hogy új adatbázist, a legújabb séma törlődnek.  Ez az felfedezte összetevőkről a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md).  

Ebben az oktatóanyagban a bérlő üzembe helyezési parancsfájlok tartalmazzák a bérlő kiépítést egy meglévő több-bérlős adatbázisba, és a bérlői új adatbázist hoz létre. Bérlői adatokat majd inicializálva, és regisztrálja a katalógus shard leképezés. A mintaalkalmazás egy egybérlős tartalmazó adatbázisok a bérlő neve alapuló nevet kapnak. Több bérlő tartalmazó adatbázisok kap egy általános _tenantsN_ name, amíg az adatbázisok csak a single-bérlőhöz kap a bérlő nevét. A mintában használt különleges elnevezési szabályai amelyeket nem a mintában a legkritikusabb feladata a katalógus használatával egyetlen hozzá kell rendelni az adatbázis nevét.  

## <a name="provision-and-catalog-tutorial"></a>Kiépítés és a katalógus oktatóanyag

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A bérlő kiépíteni egy több-bérlős adatbázis
> * A bérlő kiépíteni egy bérlői adatbázis
> * Egy kötegben, a bérlő több-bérlős és a bérlői egyetlen kiépíteni
> * Egy adatbázis és a bérlői katalógusba leképezési regisztrálása

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-management-scripts"></a>A Wingtip jegyek felügyeleti parancsfájlok beolvasása

A felügyeleti parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárház. <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-tenant-walkthrough-1"></a>Kiépítés bérlői forgatókönyv #1

Szeretné megtudni, hogyan a Wingtip jegyek alkalmazás valósít meg új bérlő kiépítésének egy több-bérlős adatbázissal, adja hozzá a töréspont és a lépés a munkafolyamaton keresztül egy bérlőt egy megosztott adatbázis más bérlők kiépítése során:

1. Az a _PowerShell ISE_, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ és állítsa be a következő paraméterekkel:
   * **$TenantName** = **Bushwillow kékek**, egy új helyszínére nevét.
   * **$VenueType** = **kékek**, az előre definiált helyszínére típusok egyikét: kékek, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetű, szóközök nélkül).
   * **$DemoScenario** = **1**található *kiépíteni egy olyan megosztott adatbázist a többi bérlő tenant*.

1. Adja hozzá a töréspont tegyen a kurzort, bárhol, amely szerint a sor 38, sor: *New-bérlő "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. A parancsfájl nyomja le az futtatásához **F5**.

1. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.

   ![Hibakeresési](media/saas-multitenantdb-provision-and-catalog/debug.png)

Nyomon követni a parancsfájl végrehajtása használatával a **Debug** menüpontok - **F10** és **F11** lépéssel keresztül vagy a hívott függvényekké. PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Közben a parancsfájl-nyomkövetés végighaladhat munkafolyamat fő elemei a következők:

* **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
* **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**. A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
* **Bérlői regisztrálja a katalógus** a bérlőkulcsról és a meglévő tenants1 adatbázis közötti leképezéseket hozzáadódik a katalógusban. 
* **Bérlői meg van adva, a katalógus**. A helyszínére neve bekerül a bérlők tábla a katalógusban.  Ez azt jelenti, hogy hogyan a katalógus-adatbázis is terjeszthető támogatásához további alkalmazásspecifikus adatait.
* **Nyissa meg az új tenanthoz események lapján**. A *Bushwillow kékek* események lap megnyitása a böngészőben:

   ![események](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-tenant-walkthrough-2"></a>Kiépítés bérlői forgatókönyv #2

Most már a bemutató a folyamat, amikor a bérlő létrehozása a saját adatbázisában:

1. Még mindig... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ állítsa be a következő paraméterekkel:
   * **$TenantName** = **sequoia foci**, egy új helyszínére nevét.
   * **$VenueType** = **foci**, az előre definiált helyszínére típusok egyikét: kékek, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci (kisbetű, szóközök nélkül).
   * **$DemoScenario** = **2**található *kiépíteni egy olyan megosztott adatbázist a többi bérlő tenant*.

1. Adja hozzá az új töréspont tegyen a kurzort, bárhol, amely szerint a sor 57, sor:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. A szkript futtatásához nyomja le az **F5** billentyűt.

1. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.  Használjon **F10** és **F11** ugorja át, és lépjen be a végrehajtási nyomkövetéséhez funkciók.

Közben a parancsfájl-nyomkövetés végighaladhat munkafolyamat fő elemei a következők:

* **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
* **Hozzon létre egy új bérlő adatbázist**. Az adatbázis másolása hozza létre a *basetenantdb* adatbázis-Resource Manager-sablon használatával.  Az adatbázis nevét a bérlő neve alapul.
* **Adatbázis hozzáadása katalógus**. A bérlői adatbázis egy shard a katalógus van regisztrálva.
* **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**. A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
* **Bérlői regisztrálja a katalógus** az új bérlőkulcs közötti leképezést és a *sequoiasoccer* adatbázis hozzáadódik a katalógusban.
* **Bérlői meg van adva, a katalógus**. A helyszínére neve bekerül a bérlők tábla a katalógusban.
* **Nyissa meg az új tenanthoz események lapján**. A *Sequoia foci* események lap megnyitása a böngészőben:

   ![események](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Egy kötegben, a bérlő kiépítése

Ebben a gyakorlatban gyors kiépítése 17 bérlők egy tranzakcióköteghez. A kötegelt bérlő kiépítése többi Wingtip jegyek oktatóanyag elindítása, így több mint pár adatbázisok történő együttműködésre előtt ajánlott.

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\*bemutató-ProvisionAndCatalog.ps1* , és módosítsa a *$DemoScenario* 3 paramétert:
   * **$DemoScenario** = **3**található *bérlő köteg kiépíteni olyan megosztott adatbázist*.
1. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.


### <a name="verify-the-deployed-set-of-tenants"></a>Ellenőrizze a bérlők telepített készlete 
Ezen a ponton rendelkezik olyan megosztott adatbázist helyezett bérlők és a bérlők a saját adatbázisok helyezhető üzembe. Az Azure-portálon létrehozott adatbázisokat vizsgálata használhatók:  

* Az a [Azure-portálon](https://portal.azure.com), nyissa meg a **tenants1-mt -\<felhasználói\>**  server keresse meg azt az SQL Server-kiszolgálók listájához.  A **SQL-adatbázisok** lista tartalmaznia kell a megosztott **tenants1** adatbázis és a bérlők számára saját adatbázisban lévő adatbázisok:

   ![adatbázislista](media/saas-multitenantdb-provision-and-catalog/databases.png)

Az Azure-portálon a bérlő adatbázisokat jeleníti meg, amíg azt Ön lássuk belül a bérlők a megosztott adatbázis. A bérlők teljes listáját a Wingtip jegyek események központ lapján látható.   

* A böngészőben nyissa meg az események központ lapján (http:events.wingtip-mt.\<felhasználói\>. trafficmanager.net)  

A bérlők és a megfelelő adatbázis teljes listáját a katalógus érhető el. Az SQL-nézet az tenantcatalog adatbázis, amelyhez csatlakozik, a bérlő nevét a bérlők táblában adatbázis neve a Shard felügyeleti táblákban tárolt valósul meg. Ez a nézet szépen mutatja be, amely a metaadatokat a katalógusban tárolt érték.

* A *SQL Server Management Studio (SSMS)* a bérlők kiszolgálóra csatlakozás **tenants1-mt.\<felhasználói\>. database.windows.net**, a bejelentkezési: **fejlesztői** , Jelszó:**P@ssword1**

    ![SSMS kapcsolódási párbeszédpanel](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

* Az a *Object Explorer*, keresse meg a nézeteket a *tenantcatalog* adatbázis.
* Kattintson a jobb gombbal *ExtendedTenants* válassza **legfelső 1000 sor kiválasztása** meg és jegyezze fel a különböző bérlőkhöz tartozó bérlői nevét és az adatbázis közötti leképezést.

    ![Az SSMS ExtendedTenants megtekintése](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Az ebben az oktatóanyagban nem ismertetett egyéb kiépítési minták közé tartoznak a következők:

**Adatbázisok rugalmas készletek előzetes kiépítése.** Az előre üzembe helyezési minta kihasználja az, hogy nem adja hozzá az adatbázisok rugalmas készlethez kapcsolódik további költség. Számlázási a rugalmas készlet, nem az adatbázisokat, és inaktív adatbázisok nem erőforrást. Előzetes kiépítése adatbázis készletben, és lefoglalása őket, amikor szükséges, a saját adatbázisába előkészítésére bérlők szükséges idő jelentősen csökkenthető. Az előzetesen kiépített adatbázisok száma szükség szerint igazítható, hogy rendelkezésre álljon egy, a várható kiépítési aránynak megfelelő puffer.

**Automatikus kiépítés.** Az automatikus átadásának mintának egy dedikált létesítési szolgáltatásával kiszolgálók, a készletek és a adatbázisok automatikusan szükség szerint oszthatják ki – többek között a előre létesítési adatbázisok rugalmas készletek igény. Ha az adatbázisok leszerelésre és törlésre kerülnek, a rugalmas készletekben keletkező réseket a kiépítő szolgáltatás igény szerint betöltheti. Ilyen szolgáltatás lehet egyszerű vagy összetett – például létesíteni, több földrajzi kezelése és vész-helyreállítási georeplikáció beállítása sikerült. Automatikus kiépítés mintájú egy ügyfélalkalmazás vagy parancsfájl akkor igényelnie létesítési annak a várólistára létesítési szolgáltatás által feldolgozandó, és szeretné majd befejezési megállapításához kérdezze le. Kiépítés előtti használata esetén kérelmek volna kell kezelni a gyorsan, a szolgáltatás a háttérben futó helyettesítő adatbázis kiépítés kezelése.



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egyetlen új bérlő kiépítése
> * További bérlők kötegelt kiépítése
> * Lépjen be a bérlők kiépítés, és regisztrálja őket a katalógusba részleteit

Próbálja meg a [teljesítmény figyelési oktatóanyag](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>További források

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
