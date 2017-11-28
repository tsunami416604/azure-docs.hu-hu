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
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: 93a2f8aa8890f40a8ef9b88fe172efa24aac7811
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Új bérlők biztosítása és a katalógus egy SaaS-alkalmazáshoz szilánkos több-bérlős SQL-adatbázis használata

Ebben az oktatóanyagban elsajátíthatja a létrehozásához, és a bérlők katalogizálni a szilánkos több-bérlős adatbázismodell az használatakor mintájával kapcsolatos. 

Egy több-bérlős séma, amely tartalmazza a bérlő azonosítója bérlői adatokat tároló tábla elsődleges kulcsa, lehetővé teszi, hogy több bérlő egyetlen adatbázisban tárolja. Nagyszámú bérlők támogatása érdekében bérlői adatok több szegmensben osztják vagy adatbázisok terjesztése. Az adatokat bármely egy bérlő mindig teljes tartalmaz egy adatbázist.  A katalógus adatbázisokhoz bérlők leképezése tárolására szolgál.   

Választhatja azt is, bizonyos adatbázisok esetén csak egyetlen bérlői feltöltéséhez. Több bérlő rendelkező adatbázisok csökkenti a bérlők elszigetelésére bérlőnként alacsonyabb költségekkel alkalmazást.  Adatbázisok csak egyetlen bérlői vonatkozik, amelyek alacsony költségű keresztül elkülönítési alkalmazást.  Több bérlők és egyetlen bérlők adatbázisokkal is keverhetők a költségek vagy az egyes bérlők elszigetelését optimalizálása azonos SaaS-alkalmazáshoz. Bérlők a saját adatbázis üzembe helyezve, vagy később áthelyezhető a saját adatbázis adható meg.

   ![Bérlői katalógus szilánkos több-bérlős adatbázis-alkalmazás](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Bérlői katalógus minta

Ha bérlői adatok több adatbázis között van elosztva, fontos tudni, hogy minden bérlő adatok tárolására. Katalógus-adatbázis tárolja a bérlő és a hozzá kapcsolódó adatokat tároló adatbázis közötti leképezést.

Mindegyik bérlő azonosít egy kulcsot a katalógusban. A Wingtip jegyek Szolgáltatottszoftver-alkalmazásoknál, az a bérlői kulcs formátuma a kivonatát, a bérlő neve. Ez lehetővé teszi az alkalmazás a bérlő neve kinyerése egy weblap URL-CÍMÉT, és használja ezt a megfelelő adatbázishoz való kapcsolódáshoz. Más bérlői kulcs rendszerek is használható.

A katalógus lehetővé teszi a nevét vagy helyét egy bérlői-adatbázisnak a kérelem megszakítása nélkül kiépítése után módosítható.  Egy adatbázis több-bérlős modell ez alkalmazkodik a bérlő adatbázisok közötti áthelyezése.  A katalógus alkalmazáshoz való jelzi, hogy karbantartási és egyéb műveletek offline állapotban-e a bérlő is használható.

A katalógus bővíthető további bérlő vagy az adatbázis metaadatait, például a réteg a rendszer vagy egy adatbázist, adatbázis sémaverziója, bérlő neve és service-csomag vagy SLA-t és egyéb információkat Alkalmazáskezelés, ügyfél-támogatási vagy devops engedélyezése parancsra folyamatok.  

A katalógus is használható több-bérlős jelentéskészítési, séma felügyeletének engedélyezése, és az adatok kibontása elemzés céljából. 

### <a name="elastic-database-client-library"></a>Elastic Database-kezelési klienskódtár 
Az a Wingtip jegyek SaaS-alkalmazásokhoz, a katalógus meg van valósítva a *tenantcatalog* adatbázis, a Shard felügyeleti szolgáltatását használja, a [rugalmas adatbázis ügyfél könyvtár (EDCL)](sql-database-elastic-database-client-library.md). A könyvtár lehetővé teszi az alkalmazás létrehozására, kezelésére és használja az adatbázis biztonsági "shard térkép". A szilánkok térkép szilánkok (adatbázisok) és a kulcsok (bérlő azonosító) és a szilánkok leképezése listáját tartalmazza.  EDCL funkciók is használható alkalmazások vagy a PowerShell-parancsfájlok során bérlői létesítési bejegyzéseket a shard leképezés létrehozásához, és újabb verziók, a megfelelő adatbázishoz való kapcsolódáshoz. A könyvtár gyorsítótárazza a kapcsolódási adatokat, hogy a katalógus-adatbázis a forgalom csökkentése érdekében, és a kapcsolat sebessége. 

> [!IMPORTANT]
> A hozzárendelési adatok érhető el a katalógus-adatbázisban, de *nem szerkesztése!* A társítási adatokat kizárólag az Elastic Database-ügyfélkódtár API-jaival szerkessze. A társítási adatok közvetlen módosítása a katalógus sérülésének kockázatát hordozza magában, ezért nem támogatott.


## <a name="tenant-provisioning-pattern"></a>Bérlői üzembe helyezési minta

Egy új bérlőt a szilánkos több-bérlős adatbázismodell létesítésekor először kell meghatározni, ha a bérlő nem áll át az olyan megosztott adatbázist vagy a saját adatbázis megadott. Ha olyan megosztott adatbázist, azt kell meghatározni, ha nincs hely egy meglévő adatbázist, vagy egy új adatbázist van szükség. Ha szükség van egy új adatbázist, azt kell építhető ki a megfelelő helyet és a szolgáltatási rétegben, megfelelő séma- és referenciainformációkat adatokkal inicializálva, és regisztrálja a katalógusban. Végül a bérlő leképezési lehet hozzáadni a megfelelő shard hivatkozik.

Az adatbázis SQL-parancsfájlok végrehajtása, központi telepítése egy bacpac vagy sablon adatbázis másolása kiépítéséhez. A Wingtip jegyek SaaS-alkalmazásokhoz új bérlő adatbázisok létrehozása sablon adatbázis másolása.

A létesítési módszer adatbázis kell értelmezhető átfogó a séma-kezelési stratégiában, amely biztosítania kell, hogy új adatbázist, a legújabb séma törlődnek.  Ez az felfedezte összetevőkről a [séma felügyeleti oktatóanyag](saas-tenancy-schema-management.md).  

Ebben az oktatóanyagban a bérlő üzembe helyezési parancsfájlok például mindkét kiépítés egy bérlőt egy meglévő adatbázisba más bérlők osztva, és a kiépítés a bérlők a saját adatbázisába. Bérlői adatok majd inicializálva és a katalógus shard térkép regisztrálni. A mintaalkalmazás, több bérlő tartalmazó adatbázisok kap egy általános nevet, például *tenants1*, *tenants2*, amíg egy egybérlős tartalmazó adatbázisok szerepelnek a bérlő nevét stb. A mintában használt különleges elnevezési szabályai amelyeket nem a mintában a legkritikusabb feladata a katalógus használatával egyetlen hozzá kell rendelni az adatbázis nevét.  

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

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS több-bérlős adatbázis és a parancsfájlok

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit. 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Egy olyan megosztott adatbázist a többi bérlő tenant kiépítése

Ha szeretné megtudni, hogyan a Wingtip jegyek alkalmazás valósít meg új bérlő kiépítésének megosztott adatbázisban, töréspont és a munkafolyamaton keresztül lépés hozzáadása:

1. Az a _PowerShell ISE_, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ és állítsa be a következő paraméterekkel:
   * **$TenantName** = **Bushwillow kékek**, az új helyszínére nevét.
   * **$VenueType** = **kékek**, az előre definiált helyszínére típusok egyikét: *kékek*, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, foci () kisbetű, szóközök nélkül).
   * **$Scenario** = **1**található *kiépíteni egy olyan megosztott adatbázist a többi bérlő tenant*.

1. Adja hozzá a töréspont tegyen a kurzort, bárhol, amely szerint a sor 38, sor: *New-bérlő "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. A parancsfájl nyomja le az futtatásához **F5**.

1. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.

   ![hibakeresés](media/saas-multitenantdb-provision-and-catalog/debug.png)

Nyomon követni a parancsfájl végrehajtása használatával a **Debug** menü elemeit, **F10** és **F11**, a lépés vagy hívott függvényekké keresztül. PowerShell-parancsfájlok hibakereső kapcsolatban további információkért lásd: [kezelése és a PowerShell-parancsfájlok hibakeresési tippeket](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Lépkedjen végig a kiépítési munkafolyamat fő elemei a következők:

* **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
* **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**. A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
* **Bérlői regisztrálja a katalógus** a bérlőkulcsról és a meglévő tenants1 adatbázis közötti leképezéseket hozzáadódik a katalógusban. 
* **Adja hozzá a bérlő nevét katalógus bővítmény táblához**. A helyszínére neve bekerül a bérlők tábla a katalógusban.  Ez azt jelenti, hogy hogyan a katalógus-adatbázis is terjeszthető támogatásához további alkalmazásspecifikus adatait.
* **Nyissa meg az új tenanthoz események lapján**. A *Bushwillow kékek* események lap megnyitása a böngészőben:

   ![események](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>A bérlők a saját adatbázis kiépítése

Most már a bemutató a folyamat, amikor a bérlő létrehozása a saját adatbázisában:

1. Még mindig... \\Tanulási modulok\\ProvisionAndCatalog\\_bemutató-ProvisionAndCatalog.ps1_ állítsa be a következő paraméterekkel:
   * **$TenantName** = **sequoia foci**, az új helyszínére nevét.
   * **$VenueType** = **foci**, az előre definiált helyszínére típusok egyikét: kékek, classicalmusic, tánc, jazz, judo, motorracing, többcélú, opera, rockmusic, *foci* () kisbetű, szóközök nélkül).
   * **$Scenario** = **2**található *kiépíteni egy olyan megosztott adatbázist a többi bérlő tenant*.

1. Adja hozzá az új töréspont tegyen a kurzort, bárhol, amely szerint a sor 57, sor:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase "*, és nyomja le az ENTER **F9**.

   ![töréspont](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. A szkript futtatásához nyomja le az **F5** billentyűt.

1. Miután a parancsfájl végrehajtása a töréspont megáll, nyomja le az **F11** lépéssel be a kódját.  Használjon **F10** és **F11** ugorja át, és lépjen be a végrehajtási nyomkövetéséhez funkciók.

Közben a parancsfájl-nyomkövetés végighaladhat munkafolyamat fő elemei a következők:

* **Kiszámítja az új bérlőkulcsot**. A bérlőkulcsot egy kivonatoló algoritmus hozza létre a bérlő nevéből.
* **Ellenőrzi, hogy a bérlőkulcs létezik-e már**. A katalógus ellenőrzésével megállapítja, hogy a kulcs már nincs regisztrálva.
* **Hozzon létre egy új bérlő adatbázist**. Az adatbázis másolása hozza létre a *basetenantdb* adatbázis-Resource Manager-sablon használatával.  Az új adatbázis nevét a bérlő neve alapul.
* **Adatbázis hozzáadása katalógus**. Az új bérlő adatbázis egy shard a katalógus van regisztrálva.
* **Az alapértelmezett bérlői adatbázisban bérlői inicializálása**. A bérlő frissítése folyamatban van az új bérlő tudnivalókkal.  
* **Bérlői regisztrálja a katalógus** az új bérlőkulcs közötti leképezést és a *sequoiasoccer* adatbázis hozzáadódik a katalógusban.
* **Bérlői meg van adva, a katalógus**. A helyszínére neve bekerül a bérlők bővítmény tábla a katalógusban.
* **Nyissa meg az új tenanthoz események lapján**. A *Sequoia foci* események lap megnyitása a böngészőben:

   ![események](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Egy kötegben, a bérlő kiépítése

Ebben a gyakorlatban egy kötegelt 17 bérlő látja el. A kötegelt bérlő kiépítése más Wingtip jegyek oktatóanyagok elindítása, így további adatbázisok történő együttműködésre előtt ajánlott.

1. Az a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\ProvisionAndCatalog\\*bemutató-ProvisionAndCatalog.ps1* , és módosítsa a *$Scenario* 3 paramétert:
   * **$Scenario** = **3**található *bérlő köteg kiépíteni olyan megosztott adatbázist*.
1. Nyomja le az **F5** billentyűt, és futtassa a szkriptet.


### <a name="verify-the-deployed-set-of-tenants"></a>Ellenőrizze a bérlők telepített készlete 
Ezen a ponton rendelkezik olyan megosztott adatbázist helyezett bérlők és a bérlők a saját adatbázisok helyezhető üzembe. Az Azure-portálon létrehozott adatbázisokat vizsgálata használhatók:  

* Az a [Azure-portálon](https://portal.azure.com), nyissa meg a **tenants1-mt -\<felhasználói\>**  server keresse meg azt az SQL Server-kiszolgálók listájához.  A **SQL-adatbázisok** lista tartalmaznia kell a megosztott **tenants1** adatbázis és a bérlők számára saját adatbázisban lévő adatbázisok:

   ![adatbázislista](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Az Azure-portálon a bérlő adatbázisokat jeleníti meg, amíg nem teszi lehetővé a bérlők tájékozódhat *belül* a megosztott adatbázis. A bérlők teljes listáját a Wingtip jegyek események központ lapján, és keresse meg azt a katalógusban látható:   

1. A böngészőben nyissa meg az események központ lapján (http:events.wingtip-mt.\<felhasználói\>. trafficmanager.net)  

   A bérlők és a megfelelő adatbázis teljes listáját a katalógus érhető el. Az SQL-nézet az tenantcatalog adatbázis, amelyhez csatlakozik, a bérlő nevét a bérlők táblában adatbázis neve a Shard felügyeleti táblákban tárolt valósul meg. Ez a nézet szépen mutatja be, amely a metaadatokat a katalógusban tárolt érték.

2. A *SQL Server Management Studio (SSMS)*, csatlakozzon a bérlők kiszolgálóra **tenants1-mt.\<felhasználói\>. database.windows.net**, a bejelentkezési: **fejlesztői** , Jelszó:**P@ssword1**

    ![SSMS kapcsolódási párbeszédpanel](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Az a *Object Explorer*, keresse meg a nézeteket a *tenantcatalog* adatbázis.
2. Kattintson jobb gombbal a nézet a *TenantsExtended* válassza **legfelső 1000 sor kiválasztása**. Vegye figyelembe a különböző bérlőkhöz tartozó bérlői nevét és az adatbázis közötti leképezést.

    ![Az SSMS ExtendedTenants megtekintése](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Egyéb kiépítési minták

Más létesítési érdekes szabályszerűségeket a következők:

**Adatbázisok rugalmas készletek előzetes kiépítése.** Az előre üzembe helyezési minta arra, hogy rugalmas készletek használatakor számlázási a készlet nem az adatbázisok biztonsági réseket. Így adatbázisokat felveheti egy rugalmas készlethez előtt szükség van kapcsolódik további költség nélkül. Előzetes kiépítése adatbázis készletben, és lefoglalása őket, amikor szükséges, a bérlő adatbázisba történő telepítéséhez szükséges idő jelentősen csökkenthető. Előzetes kiosztása adatbázisok száma puffer megfelelő-e a kiépítési várható sebesség tartása szükség szerint módosítható.

**Automatikus kiépítés.** Az automatikus átadásának mintának egy dedikált létesítési szolgáltatásával kiszolgálók, a készletek és a adatbázisok automatikusan szükség szerint oszthatják ki – beleértve a előre létesítési adatbázisok rugalmas készletek. És adatbázisok való üzembe helyezése és törlése, ha ez a rugalmas készletek létrehoz hézagok tetszés szerint a létesítési szolgáltatás is ki kell tölteni. Ilyen szolgáltatás lehet egyszerű vagy összetett – például létesíteni, több földrajzi kezelése és vész-helyreállítási georeplikáció beállítása sikerült. Automatikus kiépítés mintájú egy ügyfélalkalmazás vagy parancsfájl akkor igényelnie létesítési annak a várólistára létesítési szolgáltatás által feldolgozandó, és szeretné majd befejezési megállapításához kérdezze le. Kiépítés előtti használata esetén kérelmek volna a kezelt gyorsan, amíg egy másik szolgáltatás ugyanúgy felügyelheti a háttérben helyettesítő adatbázis üzembe.



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Egy új bérlőt kiépíteni olyan több-bérlős megosztott adatbázist és a saját adatbázis
> * További bérlők kötegelt kiépítése
> * Bérlők kiépítés, és regisztrálja őket a katalógusba részleteit teljesítéséhez

Próbálja meg a [teljesítmény figyelési oktatóanyag](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>További források

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)
* [Szkriptek hibakeresése a Windows PowerShell ISE-ben](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
