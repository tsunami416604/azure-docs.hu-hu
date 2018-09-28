---
title: Ismerkedés az Azure Data Catalog szolgáltatással
description: Átfogó oktatóanyag az Azure Data Catalog forgatókönyveiről és képességeiről.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4386f87b6986e6ab1742a8d3c14a100fea112182
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406311"
---
# <a name="get-started-with-azure-data-catalog"></a>Ismerkedés az Azure Data Catalog szolgáltatással
Az Azure Data Catalog teljes körűen felügyelt felhőszolgáltatás, amely vállalati adategységek regisztrációs és felderítőrendszereként szolgál. A szolgáltatás részletes bemutatásáért olvassa el a [Mi az az Azure Data Catalog?](data-catalog-what-is-data-catalog.md) című cikket.

Ez az oktatóanyag az Azure Data Catalog használatának megkezdésébe vezeti be a felhasználót. Az oktatóanyagban az alábbi eljárásokat fogja végrehajtani:

| Eljárás | Leírás |
|:--- |:--- |
| [A Data Catalog kiépítése](#provision-data-catalog) |Az eljárás keretében el fogja végezni az Azure Data Catalog kiépítését vagy beállítását. Erre a lépésre csak akkor van szükség, ha a katalógust korábban még nem hozták létre. Szervezetenként (Microsoft Azure Active Directory-tartományonként) csupán egyetlen adatkatalógussal rendelkezhet, még akkor is, ha Azure-fiókjához több előfizetés is tartozik. |
| [Adategységek regisztrálása](#register-data-assets) |Ebben az eljárásban az AdventureWorks2014 példaadatbázisból fog adategységeket regisztrálni az adatkatalógusban. A regisztráció az a folyamat, amelynek során a rendszer kinyeri az adatforrásból a fő szerkezeti metaadatokat (például nevek, típusok és helyek), majd bemásolja ezeket a katalógusba. Az adatforrás és az adategységek az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket. |
| [Adategységek felderítése](#discover-data-assets) |Ebben az eljárásban az Azure Data Catalog portál segítségével fogja felderíteni az előző lépésben regisztrált adategységeket. Azt követően, hogy az adatforrást regisztrálja az Azure Data Catalogban, a szolgáltatás indexeli metaadatait, hogy a felhasználók egyszerűen megtalálják az általuk keresett adatokat. |
| [Adategységek ellátása dekorációkkal](#annotate-data-assets) |Ebben az eljárásban dekorációkkal (olyan információkkal, mint például a leírások, címkék, dokumentációk vagy szakértők) fogja ellátni az adategységeket. Ezt az információt kiegészíti az adatforrásból kinyer metaadatokat, és az adatforrást az emberi szem számára érhetőbbé teszi. |
| [Csatlakozás adategységekhez](#connect-to-data-assets) |Ebben az eljárásban adategységeket fog megnyitni integrált ügyféleszközökkel (például az Excellel és az SQL Server Data Tools eszközzel), valamint egy nem integrált eszközzel (SQL Server Management Studio). |
| [Adategységek felügyelete](#manage-data-assets) |Ebben az eljárásban fogja elvégezni az adategységek biztonságának beállítását. A Data Catalog nem ad hozzáférést a felhasználóknak magukhoz az adatokhoz. Az adatok elérését az adatforrás tulajdonosa szabályozza. <br/><br/> A Data Catalog szolgáltatás arra szolgál, hogy Ön adatforrásokat derítsen fel, és megtekintse a katalógusban regisztrált forrásokhoz tartozó **metaadatokat**. Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai számára szeretné láthatóvá tenni. Ebben az esetben a Data Catalog használatával átveheti a katalógusban regisztrált adategységek tulajdonjogát, és beállíthatja a birtokában lévő adategységek láthatóságát. |
| [Adategységek eltávolítása](#remove-data-assets) |Ebben az eljárásban megismerheti, hogyan távolíthat el adategységeket az adatkatalógusból. |

## <a name="tutorial-prerequisites"></a>Az oktatóanyag előfeltételei
### <a name="azure-subscription"></a>Azure-előfizetés
Az Azure Data Catalog szolgáltatás beállítását kizárólag Azure-előfizetések tulajdonosai vagy társtulajdonosai végezhetik el.

Az Azure-előfizetés segít rendszerezni a felhőszolgáltatás-erőforrások, például az Azure Data Catalog elérését. Ezenfelül az előfizetés révén azt is megszabhatja, hogy hogyan szeretne jelentést készíteni az erőforrások használatáról, hogy hogyan számlázzák ki azt Önnek, illetve, hogy hogyan szeretne fizetni érte. Az egyes előfizetésekhez eltérő számlázási és fizetési beállítások tartozhatnak, így osztályonként, projektenként, területi képviseletenként stb. különböző előfizetési és fizetési megoldásokat használhat. A felhőszolgáltatásokat mindig egy előfizetéshez kell rendelni, ezért ahhoz, hogy beállítsa az Azure Data Catalog szolgáltatást, rendelkeznie kell előfizetéssel. További információkat a [fiókok, előfizetések és rendszergazdai szerepkörök kezeléséről](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) szóló cikkben talál.

Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. A részletekért lásd: [Ingyenes próbafiók](https://azure.microsoft.com/pricing/free-trial/).

### <a name="azure-active-directory"></a>Azure Active Directory
Az Azure Data Catalog beállításához be kell jelentkeznie egy Azure Active Directory (Azure AD) felhasználói fiókkal. Önnek egy Azure-előfizetés tulajdonosának vagy társtulajdonosának kell lennie.  

Az Azure AD egyszerű módot kínál vállalkozásának az identitás és a hozzáférés kezelésére, mind a felhőben, mind a helyszínen. Ön egyetlen munkahelyi vagy iskolai fiók segítségével bejelentkezhet bármely felhőben futó vagy helyszíni webalkalmazásba. Az Azure Data Catalog az Azure AD segítségével hitelesíti a bejelentkező felhasználókat. További információk: [Mi az az Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)

### <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-szabályzat konfigurálása
Előfordulhat, hogy ugyan be tud lépni az Azure Data Catalog portálra, de amikor megpróbál bejelentkezni az adatforrás-regisztrációs eszközbe, hibaüzenet jelenik meg, amely meggátolja a belépést. Ez a hiba a céges hálózatról, illetve a céges hálózaton kívülről történő csatlakozás esetében egyaránt előfordulhat.

A regisztrációs eszköz *űrlapos hitelesítés* segítségével veti össze a felhasználói bejelentkezéseket az Azure Active Directory adatbázisával. Ahhoz, hogy a bejelentkezés sikeres legyen, az Azure Active Directory rendszergazdájának engedélyeznie kell az űrlapos hitelesítést a *globális hitelesítési szabályzatban*.

Ahogy az az alábbi képen is látható, a globális hitelesítési szabályzat segítségével különböző hitelesítést engedélyezhet az intranetes és az extranetes kapcsolatokhoz. Ha nincs engedélyezve az űrlapos hitelesítés azon a hálózaton, amelyről csatlakozik, bejelentkezési hiba léphet fel.

 ![Az Azure Active Directory globális hitelesítési szabályzata](./media/data-catalog-prerequisites/global-auth-policy.png)

További információkért lásd a [Hitelesítési házirendek konfigurálása](https://technet.microsoft.com/library/dn486781.aspx) című cikket.

## <a name="provision-data-catalog"></a>Adatkatalógus létrehozása
Szervezetenként (Azure Active Directory-tartományonként) mindössze egy adatkatalógust hozhat létre. Ez azt jelenti, hogy ha az Önnel megegyező Azure Active Directory-tartományhoz tartozó Azure-előfizetés tulajdonosa vagy társtulajdonosa már létrehozott egy katalógust, Ön nem fog tudni katalógust létrehozni, még akkor sem, ha több Azure-előfizetéssel rendelkezik. Ha szeretné megnézni, hogy az Ön Azure Active Directory-tartományában hozott-e már létre adatkatalógust valamelyik felhasználó, lépjen az [Azure Data Catalog honlapjára](http://azuredatacatalog.com), és ellenőrizze, hogy lát-e katalógust. Ha valaki már létrehozta a katalógust, ugorja át az itt látható eljárást, és folytassa a következő résszel.    

1. Nyissa meg a [Data Catalog szolgáltatás weboldalát](https://azure.microsoft.com/services/data-catalog), és kattintson **Az első lépések** lehetőségre.
   
    ![Azure Data Catalog – marketinges kezdőlap](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely Azure-előfizetés tulajdonosa vagy társtulajdonosa. A bejelentkezést követően megjelenik az alábbi oldal.
   
    ![Azure Data Catalog – adatkatalógus létrehozása](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Adja meg az adatkatalógus **nevét**, a használni kívánt **előfizetést**, valamint a katalógus kívánt **helyét**.
4. Bontsa ki az **Árképzés** csomópontot, és válassza ki az Azure Data Catalog **kiadását** (Ingyenes vagy Standard).
    ![Azure Data Catalog – kiadás kiválasztása](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Bontsa ki a **Katalógus felhasználói** csomópontot, majd kattintson a **Hozzáadás** gombra, és adja hozzá a kívánt felhasználókat az adatkatalógushoz. Önt a rendszer automatikusan hozzáadja ehhez a csoporthoz.
    ![Azure Data Catalog – felhasználók](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Bontsa ki a **Katalógus-rendszergazdák** csomópontot, majd kattintson a **Hozzáadás** gombra, így további rendszergazdákat rendelhet hozzá az adatkatalógushoz. Önt a rendszer automatikusan hozzáadja ehhez a csoporthoz.
    ![Azure Data Catalog – rendszergazdák](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. A szervezet adatkatalógusának létrehozásához kattintson a **Katalógus létrehozása** lehetőségre. Az adatkatalógus létrehozása után a rendszer megjeleníti annak kezdőlapját.
    ![Azure Data Catalog – létrehozva](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Adatkatalógus megkeresése az Azure Portalon
1. A böngésző egy másik lapján, illetve egy másik böngésző ablakában nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be ugyanazzal a fiókkal, amelyet az előző lépésnél az adatkatalógus létrehozásához használt.
2. Válassza a **Tallózás** lehetőséget, majd kattintson a **Data Catalog** elemre.
   
    ![Azure Data Catalog--Tallózás az Azure rendszerben](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Látni fogja a korábban létrehozott adatkatalógust.
   
    ![Azure Data Catalog – katalóguslista megtekintése](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Kattintson a korábban létrehozott katalógusra. Megjelenik a portál **Data Catalog** panele.
   
   ![Azure Data Catalog – panel a portálon ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Itt megtekintheti az adatkatalógus tulajdonságait, illetve frissítheti is őket. Kattintson például a **Tarifacsomag** elemre, és módosítsa a kiadást.
   
    ![Azure Data Catalog – tarifacsomag](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Az Adventure Works példaadatbázisa
Ebben a példában az AdventureWorks2014 példaadatbázisban található adategységeket (táblákat) fog regisztrálni az SQL Server adatbázismotor számára. Bármilyen támogatott adatforrást használhat, ha inkább olyan adatokkal szeretne dolgozni, amelyeket ismer, vagy amelyek munkaköre szempontjából relevánsak. A támogatott adatforrások listájáért lásd: [Supported data sources](data-catalog-dsr.md) (Támogatott adatforrások).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Az Adventure Works 2014 OLTP adatbázis telepítése
Az Adventure Works adatbázisa olyan szabványos online tranzakciófeldolgozási forgatókönyveket támogat, melyeket egy fiktív kerékpárgyártó (az Adventure Works Cycles) használhat például a termékek, az értékesítések és a vásárlások követésére. Ebben az oktatóanyagban termékekre vonatkozó információkat fog regisztrálni az Azure Data Catalogba.

Az Adventure Works példaadatbázisának telepítése:

1. Töltse le az [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) fájlt a CodePlex oldalról.
2. Állítsa vissza az adatbázist a gépére. Ehhez kövesse a [Restore a Database Backup by using SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx) (Adatbázis biztonsági másolatának visszaállítása az SQL Server Management Studio segítségével) című cikk utasításait, vagy az alábbi lépéseket:
   1. Nyissa meg az SQL Server Management Studiót, és kapcsolódjon az SQL Server adatbázismotorjához.
   2. Kattintson jobb gombbal a **Databases** (Adatbázisok) lehetőségre, majd a **Restore Database** (Adatbázis visszaállítása) elemre.
   3. A **Restore Database** (Adatbázis visszaállítása) területen kattintson a **Source** (Forrás) beállításhoz tartozó **Device** (Eszköz) lehetőségre, majd a **Browse** (Tallózás) gombra.
   4. A **Select backup devices** (Biztonsági mentési eszközök kiválasztása) lehetőségnél kattintson az **Add** (Hozzáadás) lehetőségre.
   5. Keresse meg a mappát, amelyben az **AdventureWorks2014.bak** fájlt tárolja, jelölje ki a fájlt, kattintson az **OK** gombra, majd zárja be a **Locate Backup File** (Biztonsági mentési fájl megkeresése) párbeszédpanelt.
   6. Kattintson az **OK** gombra a **Select backup devices** (Biztonsági mentési eszközök kiválasztása) párbeszédpanel bezárásához.    
   7. Kattintson az **OK** gombra a **Restore Database** (Adatbázis visszaállítása) párbeszédpanel bezárásához.

Mostantól regisztrálhatja az Adventure Works példaadatbázisból származó adategységeket az Azure Data Catalog segítségével.

## <a name="register-data-assets"></a>Adategységek regisztrálása
Ebben a gyakorlatban a regisztrációs eszközt használva regisztrálja az Adventure Works adatbázisból származó adategységeket a katalógusban. A regisztráció az a folyamat, amelynek során az adatforrásból és a benne található adategységekből kinyert fő szerkezeti metaadatok (például nevek, típusok és helyek) hozzáadódnak a katalógushoz. Az adatforrás és az adategységek az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket.

### <a name="register-a-data-source"></a>Adatforrás regisztrálása
1. Nyissa meg az [Azure Data Catalog kezdőlapját](http://azuredatacatalog.com), és kattintson az **Adatok közzététele** elemre.
   
   ![Azure Data Catalog – Adatok közzététele gomb](media/data-catalog-get-started/data-catalog-publish-data.png)
2. A regisztrációs eszköz letöltéséhez, telepítéséhez, majd a számítógépen való futtatásához kattintson az **Alkalmazás indítása** gombra.
   
   ![Azure Data Catalog – Indítás gomb](media/data-catalog-get-started/data-catalog-launch-application.png)
3. A **Kezdőlapon** kattintson a **Bejelentkezés** elemre, és adja meg a hitelesítő adatait.     
   
    ![Azure Data Catalog – Kezdőlap](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. A **Microsoft Azure Data Catalog** lapon kattintson az **SQL Server** lehetőségre, majd a **Next** (Tovább) gombra.
   
    ![Azure Data Catalog – adatforrások](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Adja meg az **AdventureWorks2014** adatbázishoz tartozó SQL Server-kapcsolati tulajdonságokat (lásd az alábbi példát), majd kattintson a **CONNECT** (CSATLAKOZÁS) lehetőségre.
   
   ![Azure Data Catalog – SQL Server-kapcsolati tulajdonságok](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Regisztrálja az adategység metaadatait. Ebben a példában a **Production/Product** objektumait regisztrálja az AdventureWorks Production névtérből:
   
   1. A **Kiszolgálóhierarchia** fában bontsa ki az **AdventureWorks2014** lehetőséget, majd kattintson **Production** elemre.
   2. A Ctrl+kattintás módszerrel jelölje ki egyszerre a következőket: **Product**, **ProductCategory**, **ProductDescription** és **ProductPhoto**.
   3. Kattintson a **kiválasztott elemeket áthelyező nyílra** (**>**). Ez a művelet az összes kiválasztott objektumot áthelyezi az **Objects to be registered** (Regisztrálandó objektumok) listára.
      
      ![Azure Data Catalog oktatóanyag – objektumok megkeresése és kiválasztása](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Ha szeretne pillanatképes előnézetet is hozzáadni az adatokról, jelölje be az **Előnézet belefoglalása** jelölőnégyzetet. A pillanatkép legfeljebb 20 bejegyzést tartalmazhat az egyes táblákból. A rendszer a katalógusba másolja az előnézetet.
   5. Ha szeretné, hogy az adatprofil objektumstatisztikáit (például: minimális, maximális és átlagos értékek az oszlopban, sorok száma) bemutató pillanatképet is tartalmazza a katalógus, válassza az **Adatprofil belefoglalása** lehetőséget.
   6. A **Címkék felvétele** mezőbe írja be a következőket: **adventure works, cycles**. Ez a művelet hozzáadja az adategységekhez a keresési címkéket. A címkék nagy segítséget nyújtanak abban, hogy a felhasználók megtaláljanak egy bizonyos regisztrált adatforrást.
   7. Adja meg az adatokhoz kapcsolódó **szakértő** nevét (kihagyható).
      
      ![Azure Data Catalog oktatóanyag – regisztrálandó objektumok](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Kattintson a **REGISTER** (REGISZTRÁLÁS) gombra. Az Azure Data Catalog regisztrálja a kiválasztott objektumokat. Ebben a gyakorlatban az Adventure Works kiválasztott objektumai lesznek regisztrálva. A regisztrációs eszköz kinyeri a metaadatokat az adategységből, majd az Azure Data Catalogba másolja az adatokat. Az adatok helye nem változik, ahogy az sem, hogy mely rendszergazdák felügyelik az adatokat, és milyen szabályzatok érvényesek rájuk.
      
      ![Azure Data Catalog – regisztrált objektumok](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. A regisztrált adatforrás-objektumok megtekintéséhez kattintson a **View Portal** (Portál megtekintése) lehetőségre. Az Azure Data Catalog-portálon ellenőrizze, hogy a rácsnézetben látja-e mind a négy táblát, illetve az adatbázist.
      
      ![Objektumok az Azure Data Catalog-portálon ](media/data-catalog-get-started/data-catalog-view-portal.png)

Ebben a gyakorlatban az Adventure Works példaadatbázisából regisztrált néhány objektumot, amelyeket mostantól a szervezet bármelyik felhasználója könnyen megtalálhat. A következő gyakorlatból elsajátíthatja, hogyan találhatja meg a regisztrált adategységeket.

## <a name="discover-data-assets"></a>Adategységek felderítése
Az Azure Data Catalog felderítési funkciója elsődlegesen két mechanizmust használ: keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Különböző jellemzőket (például szakértők, adatforrástípusok, objektumtípusok és címkék) adhat meg, és megtekintheti az azokkal egyező adategységeket, illetve az egyezést mutató adategységekre szűkítheti a találatokat.

A keresés és szűrés együttes használatával egyszerűen navigálhat az Azure Data Catalog szolgáltatásban regisztrált adatforrások között, így könnyen megtalálja a keresett adategységeket.

Ebben a gyakorlatban az Azure Data Catalog-portál segítségével fogja felderíteni az előző gyakorlat során regisztrált adategységeket. A keresési szintaxissal kapcsolatban lásd: [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (A Data Catalog keresési szintaxisának leírása).

Az alábbiakban különböző példákat hozunk a katalógusban szereplő adategységek felderítésére.  

### <a name="discover-data-assets-with-basic-search"></a>Adategységek felderítése az alapszintű kereséssel
Az alapszintű kereséssel egy vagy több keresőkifejezést megadva végezhet keresést a katalógusban. Megjelenik az összes olyan adategység, amelynek tulajdonságai egyeznek egy vagy több megadott kifejezéssel.

1. Kattintson az Azure Data Catalog portál **Kezdőlap** lehetőségére. Ha már bezárta a böngészőt, nyissa meg az [Azure Data Catalog kezdőlapját](https://www.azuredatacatalog.com).
2. A keresőmezőbe írja be a `cycles` kifejezést, majd nyomja le az **ENTER** billentyűt.
   
    ![Azure Data Catalog – alapszintű szöveges keresés](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Ellenőrizze, hogy a találatok között látja-e mind a négy táblát, valamint az adatbázist (AdventureWorks2014). Ahogy az az alábbi képen látható, az eszköztárban található gombokra kattintva válthat a **rácsnézet** és a **listanézet** között. Figyelje meg, hogy a kereséshez használt kulcsszót a rendszer kijelöli a találatok között. Ez azért van, mert a **Kiemelés** funkció **BE** értékre van állítva. Megadhatja az **eredmények száma oldalanként** beállítás értékét is.
   
    ![Azure Data Catalog – alapszintű szöveges keresés, találatok](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    A **Keresések** panel bal oldalon, a **Tulajdonságok** panel jobb oldalon található. A **Keresések** panelen módosíthatja a keresési feltételeket, valamint szűrheti a találatokat. A **Tulajdonságok** panelen láthatja a rácsban vagy a listában kiválasztott objektum tulajdonságait.
4. Kattintson a találatok között látható **Product** elemre. Kattintson az **Előnézet**, **Oszlopok**, **Adatprofil** és **Dokumentáció** lapfülekre, vagy kattintson a nyílra az alsó panel kibontásához.  
   
    ![Azure Data Catalog – alsó panel](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    A **Preview** (Előnézet) lapon a **Product** táblában szereplő adatok előnézete látható.  
5. Az **Oszlopok** lapon az adategységben szereplő oszlopok részletes adatait tekintheti meg (például **név** és **adattípus**).
6. Az adategységben szereplő adatok profiljának (például sorok száma, adatok mérete, adott oszlop legalacsonyabb értéke) megtekintéséhez kattintson az **Adatprofil** lapfülre.
7. A bal oldalt található **szűrők** segítségével szűrheti a találatokat. Ha például az **Object Type** (Objektumtípus) szűrőnél a **Table** (Tábla) lehetőséget választja, csak a négy tábla jelenik meg, maga az adatbázis nem.
   
    ![Azure Data Catalog – találatok szűrése](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Adategységek felderítése tulajdonságértékekben való kereséssel
A tulajdonságértékekben való keresés segítségével megkeresheti azokat az adategységeket, amelyeknél a keresőkifejezés és a megadott tulajdonság között egyezés található.

1. A **Szűrők** menü **Objektumtípus** szűrőjének **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.  
2. A keresőmezőbe írja be a `tags:cycles` kifejezést, majd nyomja le az **ENTER** billentyűt. Az adatkatalógusban való kereséshez használható összes tulajdonság megtekintéséhez lásd: [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (A Data Catalog keresési szintaxisának leírása).
3. Ellenőrizze, hogy a találatok között látja-e mind a négy táblát, valamint az adatbázist (AdventureWorks2014).  
   
    ![Data Catalog – tulajdonságértékekben való keresés, találatok](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>A keresés mentése
1. Az **Aktuális keresés** rész **Keresések** paneljén adjon nevet a keresésnek, majd kattintson a **Mentés** gombra.
   
    ![Azure Data Catalog – keresés mentése](media/data-catalog-get-started/data-catalog-save-search.png)
2. Ellenőrizze, hogy a mentett keresés megjelent-e a **Mentett keresések** menüben.
   
    ![Azure Data Catalog – mentett keresések](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Válasszon a mentett kereséseken elvégezhető műveletek közül (**Átnevezés**, **Törlés**, **Mentés alapértelmezettként**).
   
    ![Azure Data Catalog – mentett kereséseken elvégezhető műveletek](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Logikai operátorok
A keresést logikai operátorok segítségével bővítheti vagy szűkítheti.

1. A keresőmezőbe írja be a `tags:cycles AND objectType:table` kifejezést, majd nyomja le az **ENTER** billentyűt.
2. Ellenőrizze, hogy a találatok között csak a táblák jelennek-e meg (maga az adatbázis nem).  
   
    ![Azure Data Catalog – keresés logikai operátorokkal](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Csoportosítás zárójelekkel
Zárójelek segítségével csoportosítva különböző logikai egységekre bonthatja a lekérdezés részeit, ami különösen a logikai operátorokkal együtt alkalmazva lehet hasznos.

1. A keresőmezőbe írja be a `name:product AND (tags:cycles AND objectType:table)` kifejezést, majd nyomja le az **ENTER** billentyűt.
2. Ellenőrizze, hogy a találatok között csak a **Product** tábla jelent-e meg.
   
    ![Azure Data Catalog – keresések csoportosítása](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Összehasonlító operátorok
Az összehasonlító operátorok segítségével a szám és adat adattípusú tulajdonságok esetében a nem egyenlő összehasonlítások is használhatók.

1. A keresőmezőbe írja be a `lastRegisteredTime:>"06/09/2016"` kifejezést.
2. Az **Objektumtípus** szűrő **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.
3. Nyomja le az **ENTER** billentyűt.
4. Ellenőrizze, hogy a találatok között csak a **Product**, **ProductCategory**, **ProductDescription** és a **ProductPhoto** tábla, illetve a korábban regisztrált AdventureWorks2014 adatbázis jelent-e meg.
   
    ![Azure Data Catalog – összehasonlító találatok](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Az [adategységek felderítésével](data-catalog-how-to-discover.md) kapcsolatos cikkben a felderítés módjáról olvashat részletesen, a keresési szintaxissal kapcsolatban pedig a [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (A Data Calatog keresési szintaxisának leírása) című cikkben talál további információt.

## <a name="annotate-data-assets"></a>Adategységek ellátása dekorációkkal
Ebben a gyakorlatban az Azure Data Catalog portál használatával dekorációkat fog fűzni a katalógusban korábban regisztrált adategységekhez (azaz különféle információkat, például leírásokat, címkéket vagy szakértőket fog fűzni hozzájuk). A dekorációk kiegészítik és megerősítik az adatforrásból a regisztrálás során kinyert metaadatokat, és sokkal könnyebben megtalálhatóvá és értelmezhetővé teszik az adategységeket.

Ebben a gyakorlatban egyetlen adategységet (ProductPhoto) fogunk dekorációkkal ellátni. Hozzáadunk egy rövid nevet és egy leírást a ProductPhoto adategységhez.  

1. Nyissa meg az [Azure Data Catalog kezdőlapját](https://www.azuredatacatalog.com), és a `tags:cycles` kifejezés megadásával keresse meg a korábban regisztrált adategységeket.  
2. Kattintson a találatok között a **ProductPhoto** elemre.  
3. A **Rövid név** mezőbe írja be: **Product images**, a **Leírás** mezőbe pedig a következőt: **Product photos for marketing materials**.
   
    ![Azure Data Catalog – ProductPhoto leírása](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    A **Description** (Leírás) mező segít másoknak megismerni és értelmezni, hogy miért és hogyan használják a kiválasztott adategységet. Lehetősége van további címkék hozzáadására és oszlopok megtekintésére is. Most megpróbálhatja az adategységeket a katalógushoz hozzáadott leíró metaadatok használatával megkeresni és szűrni.

Az oldalon a következő műveleteket is elvégezheti:

* Szakértőket adhat az adategységekhez. Kattintson a **Szakértők** részen található **Hozzáadás** gombra.
* Címkéket adhat hozzá az adatkészlet szintjén. Kattintson a **Címkék** részen található **Hozzáadás** gombra. A címke lehet felhasználói vagy szószedetcímke. A Data Catalog Standard kiadása tartalmaz egy üzleti szószedetet is, amelynek segítségével a katalógus-rendszergazdák megalkothatják a központi üzleti elnevezési rendszert. A katalógus felhasználói ezután a szószedet kifejezéseivel jelölhetik meg az adategységeket. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása)
* Címkéket adhat hozzá az oszlopok szintjén. Kattintson annak az oszlopnak a **Címkék** részénél található **Hozzáadás** gombra, amelyhez dekorációt kíván fűzni.
* Leírást adhat hozzá az oszlopok szintjén. Töltse ki az oszlop **Leírás** mezőjét. Megtekintheti az adatforrásból kinyert leírási metaadatokat.
* Adjon hozzá **Hozzáférés kérése** típusú információkat, amelyekből a felhasználók megtudhatják, hogyan kérhetnek hozzáférést az adategységhez.
  
    ![Azure Data Catalog – címkék és leírások hozzáadása](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Válassza a **Dokumentáció** lapfület, és adja meg az adategységhez tartozó dokumentációt. Az Azure Data Catalog dokumentációs funkciójával az adatkatalógus egyben tartalomtárként is használható, amely bemutatja az adategységek közötti összefüggéseket is.
  
    ![Azure Data Catalog – Dokumentáció lap](media/data-catalog-get-started/data-catalog-documentation.png)

Ugyanazt a dekorációt egyszerre több adategységhez is hozzáadhatja. Kiválaszthatja például a korábban regisztrált összes adategységet, és megadhat hozzájuk egy szakértőt.

![Azure Data Catalog – dekoráció készítése több adategységhez](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Az Azure Data Catalog közösségi kiszervezést alkalmazó megközelítést használ a dekorációknál. A Data Catalog bármelyik felhasználója felvehet (felhasználói vagy szószedet-) címkéket, leírásokat és más típusú metaadatokat, így bárki, aki ismeri az adategységet, rögzítheti és elérhetővé teheti meglátásait.

Az adategységek dekorációkkal való ellátásával kapcsolatban [az adategységek dekorálását bemutató cikkben](data-catalog-how-to-annotate.md) olvashat részletesen.

## <a name="connect-to-data-assets"></a>Csatlakozás adategységekhez
Ebben a gyakorlatban a kapcsolatadatok segítségével az adategységeket egy integrált ügyféleszközben (Excel) és egy nem integrált eszközben (SQL Server Management Studio) is meg fogja nyitni.

> [!NOTE]
> Nem szabad elfelejteni, hogy az Azure Data Catalog nem ad hozzáférést magához az adatforráshoz, csupán megkönnyíti a felderítését és a megértését. Amikor Ön csatlakozik egy adatforráshoz, a választott ügyfélalkalmazás a windowsos hitelesítési adatokat fogja használni, illetve ha szükséges, felszólítja Önt a hitelesítési adatok megadására. Ha korábban még nem kapott hozzáférést az adatforráshoz, akkor a csatlakozáshoz hozzáférést kell kapnia.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Csatlakozás adategységhez az Excelből
1. A találatok közül válassza ki a **Product** elemet. Kattintson a **Megnyitás a következőben:** lehetőségre, majd válassza az **Excel** elemet.
   
    ![Azure Data Catalog – csatlakozás adategységhez](media/data-catalog-get-started/data-catalog-connect1.png)
2. Kattintson a **Megnyitás** gombra a letöltési előugró ablakban. Ez böngészőnként eltérően jelenhet meg.
   
    ![Azure Data Catalog – exceles csatlakozási fájl letöltése](media/data-catalog-get-started/data-catalog-download-open.png)
3. A **Microsoft Excel Security Notice** (Microsoft Excel biztonsági figyelmeztetés) ablakban kattintson az **Enable** (Engedélyezés) gombra.
   
    ![Azure Data Catalog – Excel biztonsági előugró ablak](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Az **Adatok beolvasása** párbeszédpanelen tartsa meg az alapértelmezéseket, majd kattintson az **OK** gombra.
   
    ![Azure Data Catalog – Excel, adatok beolvasása](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Tekintse meg az adatforrást az Excelben.
   
    ![Azure Data Catalog – terméktábla az Excelben](media/data-catalog-get-started/data-catalog-connect2.png)

Ebben a gyakorlatban az Azure Data Catalog segítségével felderített adategységekhez fog csatlakozni. Az Azure Data Catalog-portál segítségével közvetlenül, a **Open in** (Megnyitás a következőben) menüben elérhető integrált ügyfélalkalmazásokkal végezheti el a csatlakozást. Ezenfelül tetszőleges alkalmazáshoz csatlakozhat az adategység metaadataiban szereplő csatlakozási helyadatok segítségével. Például az SQL Server Management Studio használatával csatlakozhat az AdventureWorks2014 adatbázishoz, és elérheti az oktatóanyag korábbi részében regisztrált adategységekben szereplő adatokat.

1. Nyissa meg az **SQL Server Management Studiót**.
2. A **Kapcsolódás kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét az Azure Data Catalog portál **Tulajdonságok** paneljéről.
3. Adja meg az adategység eléréséhez szükséges megfelelő hitelesítési és bejelentkezési adatokat. Ha nincs hozzáférése, használja a **Hozzáférés kérése** mezőben szereplő adatokat, és kérjen hozzáférést.
   
    ![Azure Data Catalog – hozzáférés kérése](media/data-catalog-get-started/data-catalog-request-access.png)

Kattintson a **Kapcsolati sztringek megtekintése** elemre, tekintse meg, majd másolja a vágólapra az ADF.NET, ODBC és OLEDB kapcsolati sztringekat, amelyekre az alkalmazásban szükség lesz.

## <a name="manage-data-assets"></a>Adategységek felügyelete
Ebben a lépésben bemutatjuk, hogyan állítható be az adategységek biztonsága. A Data Catalog nem ad hozzáférést a felhasználóknak magukhoz az adatokhoz. Az adatok elérését az adatforrás tulajdonosa szabályozza.

A Data Catalog szolgáltatás arra szolgál, hogy adatforrásokat derítsen fel, és megtekintse a katalógusban regisztrált forrásokhoz tartozó metaadatokat. Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai számára szeretné láthatóvá tenni. Ebben az esetben a Data Catalog használatával átveheti a katalógusban regisztrált adategységek tulajdonjogát, és beállíthatja a birtokában lévő adategységek láthatóságát.

> [!NOTE]
> Az ebben a gyakorlatban leírt felügyeleti funkciók kizárólag az Azure Data Catalog Standard kiadásában érhetők el, az ingyenes kiadásban nem.
> Az Azure Data Catalog lehetőséget nyújt az adategységek saját tulajdonba vételére, társtulajdonosok hozzáadására, valamint az adategységek láthatóságának beállítására.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Adategységek birtokbavétele és láthatóságának korlátozása
1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com). A **Search** (Keresés) mezőbe írja be a `tags:cycles` kifejezést, majd nyomja le az **ENTER** billentyűt.
2. Kattintson a kívánt elemre a találatok listájában, majd kattintson az eszköztár **Saját tulajdonba vétel** elemére.
3. A **Tulajdonságok** panel **Felügyelet** részénél kattintson a **Saját tulajdonba vétel** elemre.
   
    ![Azure Data Catalog – saját tulajdonba vétel](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. A láthatóság korlátozásához a **Láthatóság** résznél válassza a **Tulajdonosok és ezek a felhasználók** lehetőséget, majd kattintson a **Hozzáadás** gombra. A szövegmezőbe írja be a kívánt e-mail-címet, majd nyomja le az **ENTER** billentyűt.
   
    ![Azure Data Catalog – hozzáférés korlátozása](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Adategységek eltávolítása
Ebben a gyakorlatban az Azure Data Catalog-portál használatával el fogja távolítani az előnézeti adatokat a regisztrált adategységekből, valamint adategységeket fog törölni a katalógusból.

Az Azure Data Catalogban az adategységek egyesével és csoportosan is törölhetők.

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com).
2. A **Search** (Keresés) mezőbe írja be a `tags:cycles` kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Ahogy az az alábbi képen is látható, válasszon egy elemet a találatok listájában, és kattintson az eszköztár **Delete** (Törlés) elemére.
   
    ![Azure Data Catalog – rácselem törlése](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Ahogy az az alábbi képen is látható, ha a listanézetet használja, a jelölőnégyzet az elemtől balra látható.
   
    ![Azure Data Catalog – listaelem törlése](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Ahogy az az alábbi képen is látható, egyszerre több adategységet is kiválaszthat:
   
    ![Azure Data Catalog – több adategység törlése](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> A katalógus alapértelmezett viselkedése szerint bármely felhasználó regisztrálhat bármilyen adatforrást, és bármely felhasználó törölhet bármilyen regisztrált adatforrást. Az Azure Data Catalog Standard kiadásának felügyeleti lehetőségei további lehetőségeket nyújtanak, amelyekkel az adategységek saját tulajdonba vehetők, és korlátozható az adategységeket felderíteni és törölni jogosult felhasználók köre.
> 
> 

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban bemutattuk az Azure Data Catalog alapvető funkcióit, például a regisztrálást, a dekorálást, a felderítést és a vállalati adategységek felügyeletét. Most, hogy teljesítette az oktatóprogramot, ideje megkezdeni a használatot. Regisztrálja az adatforrásokat, amelyeket a csapatával használ, és hívja fel a munkatársai figyelmét is a katalógus használatára.

## <a name="references"></a>Referencia
* [Adategységek regisztrálása](data-catalog-how-to-register.md)
* [Adategységek felderítése](data-catalog-how-to-discover.md)
* [Adategységek dekorálása](data-catalog-how-to-annotate.md)
* [Adategységek dokumentálása](data-catalog-how-to-documentation.md)
* [Adategységekhez való kapcsolódás](data-catalog-how-to-connect.md)
* [Adategységek felügyelete](data-catalog-how-to-manage.md)

