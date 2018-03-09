---
title: "Általános SQL összekötő |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft általános SQL-összekötő konfigurálásával."
services: active-directory
documentationcenter: 
author: billmath
manager: bhu
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billmath
ms.openlocfilehash: 5ac8aff7e302a255efb86606416131ecf8088137
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="generic-sql-connector-technical-reference"></a>Általános SQL összekötő műszaki útmutatója
Ez a cikk ismerteti az általános SQL-összekötőt. A cikk vonatkozik a következő termékek:

* A Microsoft Identity Manager 2016 (MIM2016)
* A Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Kell használnia a 4.1.3671.0 gyorsjavítás vagy újabb [KB3092178](https://support.microsoft.com/kb/3092178).

MIM2016 és FIM2010R2, az összekötő rendelkezésre áll egy letölthető a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Ez az összekötő működés közben, olvassa el a [részletes általános SQL összekötő](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) cikk.

## <a name="overview-of-the-generic-sql-connector"></a>Az általános SQL összekötő áttekintése
Az általános SQL-összekötő lehetővé teszi a szinkronizálási szolgáltatás integrálása egy ODBC-kapcsolat által adatbázisrendszer.  

A következő szolgáltatásokat a magas szintű szempontjából, a jelenlegi kiadásban az összekötő támogatja:

| Szolgáltatás | Támogatás |
| --- | --- |
| Csatlakoztatott adatforrás |Az összekötő minden 64 bites ODBC-illesztőprogramok használata támogatott. A következő tesztelték: <li>Microsoft SQL Server és SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 &11;g</li><li>MySQL 5.x</li> |
| Forgatókönyvek |<li>Objektum életciklusának kezelésére</li><li>Jelszókezelés</li> |
| Műveletek |<li>Teljes importálás és a különbözeti importálás, exportálás</li><li>Az exportált: Hozzáadása, törlése, frissítés, és cserélje le</li><li>Jelszó beállítása, a jelszó módosítása</li> |
| Séma |<li>Az objektumok és attribútumok dinamikus felderítése</li> |

### <a name="prerequisites"></a>Előfeltételek
Az összekötő használatához győződjön meg arról, hogy a szinkronizálási kiszolgálón a következő:

* Microsoft keretrendszer 4.5.2-es vagy újabb verzió
* 64 bites ügyfél illesztőprogramok

### <a name="permissions-in-connected-data-source"></a>Engedélyek szükségesek a csatlakoztatott adatforrás
A támogatott műveletek elvégzését általános SQL Connector, meg kell rendelkeznie:

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>Portok és protokollok
Az ODBC-illesztőprogram működéséhez szükséges portok az adatbázis gyártójának dokumentációjában talál.

## <a name="create-a-new-connector"></a>Új összekötő létrehozása
Egy általános SQL-összekötő létrehozásához az **szinkronizálási szolgáltatás** válasszon **kezelőügynök** és **létrehozása**. Válassza ki a **általános SQL (Microsoft)** összekötő.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Kapcsolatok
Az összekötő egy ODBC Adatforrásnevet fájlt használja a hálózati kapcsolatot. Hozzon létre a DSN fájl használatával **ODBC adatforrások** csoportban a start menüben található **felügyeleti eszközök**. A felügyeleti eszköz, hozzon létre egy **fájl DSN** , megadható, hogy az összekötő.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Kapcsolat a képernyő az első új általános SQL összekötő létrehozásakor. Először meg kell adnia a következőket:

* DSN fájl elérési útja
* Hitelesítés
  * Felhasználónév
  * Jelszó

Az adatbázis támogatnia kell az alábbi hitelesítési módszerek egyikét:

* **Windows-hitelesítés**: A hitelesítő adatbázis történő ellenőrzésére, hogy a felhasználó Windows hitelesítő adatokat használja. A felhasználónév és jelszó megadva az adatbázis szolgáltatással való hitelesítésre szolgál. Ennek a fióknak engedéllyel kell rendelkeznie az adatbázishoz szükséges.
* **SQL-hitelesítés**: A hitelesítő adatbázis használatát, a felhasználónév és jelszó definiálása a kapcsolat képernyő az adatbázishoz való kapcsolódáshoz. Ha a DSN-fájlban tárolja a felhasználói név/jelszavak, a kapcsolat képernyőn megadott hitelesítő adatok prioritást élveznek a.
* **Az Azure SQL-adatbázis hitelesítési**: további információkért lásd: [Csatlakozás SQL Database által használó Azure Active Directory-hitelesítéssel](../../sql-database/sql-database-aad-authentication.md).

**Megkülönböztető név horgonyzási**: ezt a beállítást, ha a megkülönböztető név a horgonyattribútum is használatos. Egy egyszerű végrehajtását is használható, de a következő korlátozás is van:

* Összekötő csak egy objektumtípus támogatja. Ezért hivatkozás attribútuma csak az azonos típusú hivatkozhat.

**Exportálás típusa: Az objektum a név felülírandó**: exportálásakor, ha csak néhány attribútum módosult, a teljes objektum attribútumainak exportálja majd lecseréli a meglévő objektumot.

### <a name="schema-1-detect-object-types"></a>Séma 1 (Hibakeresés objektumtípusok)
Ezen a lapon kívánja konfigurálni, hogyan lesz az összekötő a különböző objektumtípusokra található az adatbázisban.

Minden objektum típus, egy partíciót mutatják be, és konfigurálva van a további **konfigurálása partíciók és hierarchiák**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Az objektum típusa észlelési módszer**: az összekötő támogatja az objektum típusa észlelési módszerek.

* **Rögzített értékével**: az Objektumtípusok listáját vesszővel tagolt listáját tartalmazzák. Például: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **Tábla/nézet vagy tárolt eljárás**: Adja meg a tábla/nézet vagy tárolt eljárás nevét, majd az oszlop nevét, amely objektumtípusok listáját jeleníti meg. Használatakor a tárolt eljárás, majd is biztosít paraméterek formátumú **[Name]: [irány]: [érték]**. Adja meg mindegyik paraméter (használja a Ctrl + Enter új sor eléréséhez) külön sorban.  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **SQL-lekérdezés**: Ez a beállítás lehetővé teszi egy SQL-lekérdezést csak egy oszlop objektumtípusokat, például visszaadó `SELECT [Column Name] FROM TABLENAME`. A visszaadott oszlopnak karakterlánc típusú (varchar) kell lennie.

### <a name="schema-2-detect-attribute-types"></a>Séma 2 (Hibakeresés attribútum esetében)
Ezen a lapon kívánja konfigurálni, hogy hogyan attribútum nevét és típusát is szeretné észlelhető. A konfigurációs beállítások listája minden objektumtípus észlelte az előző lapon.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Attribútum-típus észlelési metódusát**: az összekötő támogatja az attribútum típusa észlelési módszerek minden észlelt objektumtípus séma 1 képernyőn.

* **Tábla/nézet vagy tárolt eljárás**: Adja meg a tábla/nézet vagy tárolt eljárás, amellyel az attribútumok nevében található nevét. Használatakor a tárolt eljárás, majd is biztosít paraméterek formátumú **[Name]: [irány]: [érték]**. Adja meg mindegyik paraméter (használja a Ctrl + Enter új sor eléréséhez) külön sorban. Az attribútumok nevében azonosíthatók a többértékű attribútum, adja meg vesszővel tagolt listáját táblákat vagy nézeteket. Többértékű forgatókönyvek nem támogatottak. Ha a szülő és gyermek tábla ugyanazon oszlopnevek rendelkeznek.
* **SQL-lekérdezés**: Ez a beállítás lehetővé teszi egy SQL-lekérdezést csak egy oszlop attribútum nevekkel, például visszaadó `SELECT [Column Name] FROM TABLENAME`. A visszaadott oszlopnak karakterlánc típusú (varchar) kell lennie.

### <a name="schema-3-define-anchor-and-dn"></a>Séma 3 (Define horgony és DN)
Ezen a lapon konfigurálhatja a horgony és a megkülönböztető név attribútum az egyes észlelt objektum teszi lehetővé. A horgony egyedivé több attribútumok kiválasztása

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* Többértékű és logikai típusú attribútumok nem találhatók.
* Ugyanaz az attribútum nem használható megkülönböztető Nevet és a horgony, kivéve, ha **DN horgonyzási** van jelölve, a kapcsolat lapon.
* Ha **DN horgonyzási** van kiválasztva a kapcsolat lapon ezen a lapon csak a megkülönböztető név attribútum szükséges. Ez az attribútum a horgonyattribútum akkor is használható.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Séma 4 (Define attribútum típusát, hivatkozás és iránya)
Ezen a lapon az attribútum típusa egész szám, a bináris, vagy a logikai és a irány minden attribútum például konfigurálását teszi lehetővé. Összes attribútum oldalról **séma 2** vannak felsorolva, beleértve a többértékű attribútumok.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **DataType**: az attribútum típusa leképezhető ezeket a szinkronizálási motor által ismert típusok. Az alapértelmezett használatára ugyanarra a típusra, mert az SQL-séma észlelt, de dátum és idő és a hivatkozás nem könnyen felismerhető. Azok, meg kell adnia **DateTime** vagy **hivatkozás**.
* **Irány**: az attribútum irányba importálás, exportálás vagy ImportExport állíthat be. ImportExport alapértelmezett beállítás.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Megjegyzések:

* Ha az attribútum típusa nem észlelhető az összekötő által, a karakterlánc adattípusúnak használ.
* **Táblák egymásba** egy oszlopból adatbázistáblák tekinthető. Oracle egy beágyazott tábla sorainak nem meghatározott sorrendben tárolja. Azonban amikor a beágyazott tábla egy PL/SQL változóba, a sorok adta 1-gyel kezdődő egymást követő alsó indexek. Amely hozzáférést biztosít a tömbszerű egyedi sorok.
* **VARRYS** az összekötő nem támogatottak.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Séma 5 (Define partíció hivatkozás attribútumok)
Ezen a lapon konfigurálnia minden hivatkozási attribútum melyik partíció (Objektumtípus) attribútum hivatkozik.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Ha **DN horgonyzási**, és a hivatkozott azonosnak kell használnia az azonos típusú. Egy másik objektum típusa nem lehet hivatkozni.

>[!NOTE]
Már létezik a beállítás a 2017. márciusi frissítés kezdve "*" Ez a beállítás esetén a választott, akkor minden lehetséges Tagtípusok importált lesz.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 Előfordulhat, hogy 2017 frissítésétől a "\*" más néven **bármilyen olyan beállítás** támogatott importálás és Exportálás folyamata értékre változott. Ha szeretné használni ezt a beállítást a többértékű tábla/nézet olyan attribútum, amely tartalmazza az objektum típusának kell rendelkeznie.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> Ha a "*" van kiválasztva, akkor az objektum típusú oszlop nevét is meg kell adni.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

Az importálás után jelenik meg az alábbi képen hasonló:

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>Globális paraméterek
A globális paraméterek lap különbözeti importálás, a dátum és idő formátumban, és a jelszó metódusát konfigurálására szolgál.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



Az általános SQL-összekötő különbözeti importálás a következő módszereket támogatja:

* **Eseményindító**: lásd: [eseményindítók használatával különbözeti nézetek generálása](https://technet.microsoft.com/library/cc708665.aspx).
* **Vízjel**: egy általános módszer bármely adatbázis esetében használható. A vízjel lekérdezés nem ki van töltve a forgalmazótól alapján. A vízjel oszlop minden tábla/nézet használt jelen kell lennie. Ebben az oszlopban kell nyomon követnie beszúrása és a frissítésekről, a táblák és a függő (többértékű vagy gyermek) táblákat. A Synchronization Service és az adatbázis-kiszolgáló közötti kell szinkronizálni. Ha nem, előfordulhat, hogy lehet hagyni a különbözeti importálás bizonyos bejegyzések.  
  Korlátozás:
  * Vízjel stratégia támogatási törölt objektumok nem létezik.
* **Pillanatkép**: (csak a Microsoft SQL Server működik) [készített pillanatfelvételek segítségével történő különbözeti nézetek létrehozása](https://technet.microsoft.com/library/cc720640.aspx)
* **A változáskövetés**: (csak a Microsoft SQL Server működik) [kapcsolatos változások követése](https://msdn.microsoft.com/library/bb933875.aspx)  
  Korlátozások vonatkoznak:
  * Megkülönböztető név attribútum & horgonyzása a kijelölt objektum a tábla elsődleges kulcsaként részének kell lennie.
  * SQL-lekérdezés során az importálás és exportálás a változások követése nem támogatott.

**További paraméterek**: Adja meg az adatbázis időzóna azt jelzi, hogy ha az adatbázis-kiszolgáló található. Ez az érték használható a különböző formátumokba dátum és idő attribútumok támogatásához.

Az összekötő dátuma és időpontja mindig UTC formátumban tárolja. Tudja megfelelően konvertálni az a dátum és idő, az adatbázis-kiszolgáló és a használt formátumot az időzóna meg kell adni. A formátum .net formátumban kell megadni.

Exportálás során minden dátum-idő attribútumot meg kell adni az összekötő UTC idő formátumban.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Jelszó konfigurálása**: az összekötő jelszó-szinkronizálás képességeket biztosít, és támogatja és jelszó módosítása.

Az összekötő támogatja a jelszó-szinkronizálás két módszert kínál:

* **Tárolt eljárás**: ennél a módszernél két tárolt eljárás & Módosítás beállítása jelszót. Írja be az összes paraméterének hozzáadása, és módosítsa a jelszó-művelet **beállítva jelszó SP** és **Módosítsa jelszavát SP** alábbi példában rendre, egy paramétereket.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **Jelszó bővítmény**: ennél a módszernél jelszó bővítmény-DLL (meg kell adnia, hogy a kiterjesztés bővítmény-dll-fájl neve a [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) interface). Jelszó bővítményszerelvény bővítmény mappába kell helyezni, úgy, hogy az összekötő futásidőben a DLL betöltése.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Akkor is engedélyezze a Jelszófelügyeletet a a **konfigurálása bővítmény** lap.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Partíciók és hierarchiák konfigurálása
Az oldalon a partíciók és hierarchiák minden objektumtípusokat jelöl ki. Minden objektum saját partíció típus.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Felülbírálhatja a megadott érték beolvasása a **kapcsolat** vagy **globális paraméterek** lap.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Horgonyok konfigurálása
Ezen a lapon csak olvasható, mert a horgony már meg van adva. A kijelölt horgonyattribútum mindig kiegészül az objektumtípus, annak érdekében, hogy egyedi marad objektumtípusok között.

![a központi jellegűek](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Futtatási lépés paraméter konfigurálása
Ezeket a lépéseket az összekötő a futtatási profil konfigurálása. Ezek a konfigurációk el a valódi munkát az adatok importálása és exportálása.

### <a name="full-and-delta-import"></a>Teljes és különbözeti importálás
Általános SQL Connector támogatása teljes és különbözeti importálás ezekkel a módszerekkel:

* Tábla
* Nézet
* Tárolt eljárás
* SQL-lekérdezés

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tábla/nézet**  
Egy objektum többértékű attribútumok importálásához meg kell adnia a tábla vagy nézet nevét **nevét a többértékű tábla/nézetek** és a megfelelő illesztési feltételek a **feltétel csatlakozás** és a szülő tábla . Ha egynél több többértékű tábla az adatforrás, használhatja az Unió egyetlen nézetben.

>[!IMPORTANT]
Az általános SQL management agent csak egyazon többértékű táblában együttműködhet. Nem helyezett több tábla Név többértékű tábla vagy nézet nevét. Az általános SQL korlátozás.


Példa: Importálandó az alkalmazott objektum és a hozzá tartozó többértékű attribútumok. Nincsenek alkalmazott (fő tábla) és a részleg (többértékű) nevű táblát.
Tegye a következőket:

* Típus **alkalmazott** a **tábla/nézet/SP**.
* Típus részlege **nevét a többértékű tábla/nézetek**.
* Írja be az illesztési feltétel alkalmazott & részlege között **illesztési feltétel**, például `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Tárolt eljárások**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* Ha sok adatot, a tárolt eljárások tördelési megvalósításához ajánlott.
* A tárolt eljárás tördelési támogatásához meg kell adnia a Start Index és a záró Index. Lásd: [keresztül nagy mennyiségű adat hatékonyan lapozás](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndex és @EndIndex cserélése a végrehajtás során konfigurált megfelelő lapra méretértéket **konfigurálása lépés** lap. Például, ha az összekötő lekéri az első lap, és a lapméretnél értéke 500, ilyen esetben @StartIndex 1 lenne és @EndIndex 500. Ezek az értékek növelhető, ha az összekötő kéri le a következő lapjain, és módosítsa a @StartIndex & @EndIndex érték.
* A paraméteres tárolt eljárás végrehajtásához adja meg a paramétereket a `[Name]:[Direction]:[Value]` formátumban. Adja meg mindegyik paraméter (használata Ctrl + Enter új sor eléréséhez) külön sorban.
* Általános SQL-összekötő csatolt kiszolgálók az importálási művelet is támogatja a Microsoft SQL Server kiszolgálón. Csatolt kiszolgáló táblából származó információkat kell olvasni, ha majd tábla kell megadni a formátumban: `[ServerName].[Database].[Schema].[TableName]`
* Általános SQL-összekötő csak azokat az objektumokat, amelyek hasonló szerkezete (az alias nevét és az adatokat egyaránt típus) között információkat és a séma észlelési lépések futtatásával támogatja. Ha a kijelölt objektum séma- és futtatási lépés: a megadott adatok különböző, SQL-összekötő nem tudja támogatják ilyen típusú forgatókönyvek.

**SQL Query**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* Több eredményhalmazt lekérdezések nem támogatott.
* SQL-lekérdezést a tördelési támogatja, és adja meg a kezdő Index és a záró Index változóként tördelési támogatásához.

### <a name="delta-import"></a>Különbözeti importálás
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Különbözeti importálás konfigurálása azonban a teljes importálás képest további konfigurálást igényel.

* Ha az eseményindító vagy pillanatképét módszerrel különbözeti változásainak követése, majd adja meg Előzménytábla vagy a pillanatkép-adatbázisban az **Előzménytábla vagy pillanatképét adatbázisnév** mezőben.
* Adja meg például előzmények és szülő tábla, közötti illesztési feltétel kell `Employee.ID=History.EmployeeID`
* Nyomon követheti a tranzakció a szülőtábla az előzmények táblából, meg kell adnia az (Add/Update/Delete) műveletet információt tartalmazó oszlop nevét.
* Ha vízjel különbözeti változásainak követése, majd adja meg az oszlop nevét, amely a művelet a **vízjel megjelölés oszlopnév**.
* A **attribútum módosítása** a változástípushoz kötelező. Ez az oszlop, amely akkor fordul elő, az elsődleges vagy többértékű táblában a különbözeti nézetben módosítás típusra képezi le. Ez az oszlop tartalmazhat a Modify_Attribute változástípushoz attribútum szintű módosítása vagy egy hozzáadása, módosítása, vagy Delete objektumszintű változástípushoz típusának módosítása. Ha nem az alapértelmezett érték hozzáadása, módosítása, vagy törölje, majd ezeket az értékeket ezzel a beállítással adhatja meg.

### <a name="export"></a>Exportálás
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Általános SQL Connector támogatása exportálási négy támogatott módszerek használatával, mint:

* Tábla
* Nézet
* Tárolt eljárás
* SQL-lekérdezés

**Tábla/nézet**  
Ha a tábla/nézet beállítást választja, akkor az összekötő hoz létre a megfelelő lekérdezések az exportálás.

**Tárolt eljárások**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Ha a tárolt eljárás beállítást választja, az exportáláshoz három különböző tárolt eljárások Insert/Update/Delete műveletek végrehajtásához.

* **Adja hozzá SP nevét**: az SP fut, ha minden objektum elérhető lesz az összekötő a megfelelő tábla beszúrásához.
* **Frissítés SP neve**: az SP fut, ha minden objektum elérhető lesz az összekötő a frissítés a megfelelő tábla.
* **SP neve**: A Szolgáltató fut, ha minden objektum elérhető lesz az összekötő a megfelelő tábla törlésre.
* Az attribútum ki a következő tárolt eljárás paraméter értékeként használt séma. Például `@EmployeeName: INPUT: EmployeeName` (EmployeeName választotta az összekötő-sémát, és az összekötő a megfelelő értéket lecseréli ennek az exportálás során)
* A paraméteres tárolt eljárás futtatására, adja meg a paramétereket a `[Name]:[Direction]:[Value]` formátumban. Adja meg mindegyik paraméter (használata Ctrl + Enter új sor eléréséhez) külön sorban.

**SQL-lekérdezés**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Ha úgy dönt, hogy az SQL-lekérdezési lehetőség, akkor az exportáláshoz három különböző lekérdezéseket Insert/Update/Delete műveletek végrehajtásához.

* **Helyezze be a lekérdezés**: Ez a lekérdezés fut, ha minden objektum elérhető lesz az összekötő a megfelelő tábla beszúrásához.
* **Lekérdezés frissítése**: Ez a lekérdezés fut, ha minden objektum elérhető lesz az összekötő a frissítés a megfelelő tábla.
* **Törölje a lekérdezés**: Ez a lekérdezés fut, ha minden objektum elérhető lesz az összekötő a megfelelő tábla törlésre.
* A séma, például egy paraméterérték a lekérdezéshez, használja a kijelölt attribútum `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Hibaelhárítás
* Az összekötő hibaelhárítása naplózásának engedélyezése a további információkért lásd: a [hogyan ETW-nyomkövetés engedélyezése a csatlakozók](http://go.microsoft.com/fwlink/?LinkId=335731).
