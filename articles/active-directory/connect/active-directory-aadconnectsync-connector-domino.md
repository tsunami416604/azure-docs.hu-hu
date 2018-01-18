---
title: "Lotus Domino-összekötő |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft Lotus Domino-összekötő konfigurálása."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/119/2017
ms.author: barclayn
ms.openlocfilehash: 6c412be1c54e0378166791c61469c951bca3a583
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="lotus-domino-connector-technical-reference"></a>Lotus Domino-összekötő műszaki útmutatója
Ez a cikk ismerteti a Lotus Domino-összekötő. A cikk vonatkozik a következő termékek:

* A Microsoft Identity Manager 2016 (MIM2016)
* A Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Kell használnia a 4.1.3671.0 gyorsjavítás vagy újabb [KB3092178](https://support.microsoft.com/kb/3092178).

MIM2016 és FIM2010R2, az összekötő rendelkezésre áll egy letölthető a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Lotus Domino-összekötő áttekintése
Lotus Domino-összekötő lehetővé teszi a szinkronizálási szolgáltatás integrálása az IBM Lotus Domino-kiszolgáló.

A következő szolgáltatásokat a magas szintű szempontjából, a jelenlegi kiadásban az összekötő támogatja:

| Szolgáltatás | Támogatás |
| --- | --- |
| Csatlakoztatott adatforrás |Kiszolgáló: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Ügyfél:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Forgatókönyvek |<li>Objektum életciklusának kezelésére</li><li>Csoportok kezelése</li><li>Jelszókezelés</li> |
| Műveletek |<li>Teljes és különbözeti importálás</li><li>Exportálás</li><li>Állítsa be, és a jelszó módosításához a HTTP-jelszó</li> |
| Séma |<li>Személy (központi felhasználói, forduljon (tanúsítványa nem rendelkező személy))</li><li>Csoport</li><li>Erőforrás (erőforrás, hely, Online értekezletek)</li><li>Mail-adatbázisban</li><li>A támogatott objektumainak attribútumait dinamikus észlelése</li><li>Támogatja a szervezet és a szervezeti egység (OU) legfeljebb 250 egyéni képesítést adók engedélyezése</li> |

A Lotus Domino-összekötő a Lotus Notes ügyfél Lotus Domino-kiszolgálóval való kommunikációhoz használ. A függőség miatt a támogatott Lotus Notes ügyfél telepítenie kell a szinkronizálási kiszolgáló. Az ügyfél és a kiszolgáló közötti kommunikáció a Lotus Notes .NET együttműködési (Interop.domino.dll) felületen keresztül valósul meg. Ez az interfész elősegíti a Microsoft.NET platform és Lotus Notes ügyfél közötti kommunikációt, és támogatja a Lotus Domino dokumentumok és nézetek elérésére. Különbözeti importálás esetén is lehetséges, hogy a natív C++ felületének (attól függően, hogy a kijelölt különbözeti importálás módszer) használatos.

### <a name="prerequisites"></a>Előfeltételek
Az összekötő használatához győződjön meg arról, hogy a következő előfeltételek vonatkoznak a szinkronizálási kiszolgálón:

* Microsoft keretrendszer 4.5.2-es vagy újabb verzió
* A Lotus Notes ügyfél telepíteni kell a szinkronizálási kiszolgáló
* Lotus Domino-összekötő meglétét a Domino kiszolgáló az alapértelmezett Lotus Domino LDAP-séma adatbázis (schema.nsf) szükséges. Ha nincs jelen, telepítheti fut, vagy indítsa újra a Domino kiszolgálón az LDAP szolgáltatást.

### <a name="connected-data-source-permissions"></a>Csatlakoztatott adatforrás-engedélyek
A támogatott műveletek elvégzését a Lotus Domino-összekötő, a következő csoport tagja kell lennie:

* Teljes hozzáférés a rendszergazdák
* Rendszergazdák
* Adatbázis-rendszergazdák

A következő táblázat felsorolja az egyes művelethez szükséges engedélyek:

| Művelet | Hozzáférési jogok |
| --- | --- |
| Importálás |<li>Olvassa el a nyilvános dokumentumok</li><li> Teljes körű hozzáférési rendszergazda (amikor teljes hozzáférés a Rendszergazdák csoport tagja, automatikusan hozzáférhet a hatályos hozzáférés-vezérlési listában.)</li> |
| Exportálás és a jelszó beállítása |Hatályos hozzáférés: <li>Dokumentumok létrehozása</li><li>Dokumentumok törlése</li><li>Olvassa el a nyilvános dokumentumok</li><li>Nyilvános dokumentumok írása</li><li>Replikálás vagy másolat dokumentumok</li>Exportálási műveleteket is van szüksége a következő szerepkörök: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Közvetlen műveletek és AdminP
A műveletek vagy nyissa meg közvetlenül a Domino directory vagy a AdminP folyamat. Az alábbi táblázat a lista az összes támogatott objektumokat, műveletek és, ha van ilyen, a kapcsolódó végrehajtási módszer:

**Elsődleges címjegyzék**

| Objektum | Létrehozás | Frissítés | Törlés |
| --- | --- | --- | --- |
| Személy |AdminP |Közvetlen |AdminP |
| Csoport |AdminP |Közvetlen |AdminP |
| MailInDB |Közvetlen |Közvetlen |Közvetlen |
| Erőforrás |AdminP |Közvetlen |AdminP |

**Másodlagos címjegyzék**

| Objektum | Létrehozás | Frissítés | Törlés |
| --- | --- | --- | --- |
| Személy |– |Közvetlen |Közvetlen |
| Csoport |Közvetlen |Közvetlen |Közvetlen |
| MailInDB |Közvetlen |Közvetlen |Közvetlen |
| Erőforrás |– |– |– |

Amikor egy erőforrást hoz létre, a megjegyzéseket dokumentum jön létre. Hasonlóképpen erőforrás törlésekor törlődik a dokumentumba.

### <a name="ports-and-protocols"></a>Portok és protokollok
IBM Lotus Notes ügyfél és a kiszolgálók Domino kommunikációhoz megjegyzések távoli eljárás hívása (NRPC) ahol NRPC kell használni a TCP/IP használata. Az alapértelmezett portszám 1352, de a Domino rendszergazda módosíthatja.

### <a name="not-supported"></a>Nem támogatott
A jelenlegi kiadásban Lotus Domino-összekötő nem támogatja a következő műveleteket:

* Helyezze át a postaláda-kiszolgáló között.

## <a name="create-a-new-connector"></a>Új összekötő létrehozása
### <a name="client-software-installation-and-configuration"></a>Ügyfélszoftver telepítése és konfigurálása
Lotus Notes telepíteni kell a kiszolgáló **előtt** az összekötő telepítve van.

Amikor telepíti, semmiképpen egy **egyfelhasználós telepítés**. Az alapértelmezett **többfelhasználós telepítése** nem működik.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

A szolgáltatások lapon csak a szükséges Lotus Notes szolgáltatások telepítéséhez és **egyetlen ügyfél-bejelentkezési**. Egyszeri bejelentkezési szükség az összekötő tudjanak bejelentkezni a Domino-kiszolgálóra.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Megjegyzés:** Start Lotus Notes után, az összekötő használatát egy felhasználói fiók ugyanazon a kiszolgálón található szolgáltatásfiók. Ellenőrizze azt is zárhatja be az Lotus Notes ügyfelet a kiszolgálón. Az összekötő megkísérli az Domino-kiszolgálóhoz egyidejűleg nem működik.

### <a name="create-connector"></a>Összekötő létrehozása
Lotus Domino-összekötő, létrehozása a **Synchronization Service** válassza **kezelőügynök** és **létrehozása**. Válassza ki a **Lotus Domino (Microsoft)** összekötő.  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Ha a szinkronizálási szolgáltatás verziója biztosít az ügyfélgépek konfigurálására **architektúra**, győződjön meg arról, hogy az összekötő az alapértelmezett értékre van beállítva, futtatásához **folyamat**.

### <a name="connectivity"></a>Kapcsolatok
A kapcsolat lapon meg kell adnia a Lotus Domino-kiszolgáló nevét, adja meg a bejelentkezési hitelesítő adatait.  
![Kapcsolatok](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

A Domino kiszolgáló tulajdonság két formátum támogatja a kiszolgáló nevét:

* ServerName
* ServerName/DirectoryName

A **ServerName/könyvtárnév** formátuma ehhez az attribútumhoz előnyben részesített formátumban, mert ha az összekötő felveszi a kapcsolatot a Domino kiszolgáló gyorsabban szeretne választ kapni biztosít.

A megadott UserID fájl található, a konfigurációs adatbázis, a szinkronizálási szolgáltatás.

A **különbözeti importálás** ezek közül:

* **Nincs**. Az összekötő nem bármely különbözeti importálja.
* **Hozzá/frissíthet**. Az összekötő biztosítja különbözeti importálás hozzáadása, és a frissítési műveleteket. A Törlés a **teljes importálás** művelet szükség. Ez a művelet a .net együttműködési használ.
* **Adja hozzá/frissítés/törlés**. Az összekötő biztosítja különbözeti importálás hozzáadása, frissítése és törlési műveletek. Ez a művelet a natív C++-felületek használatával.

A **Sémabeállításokat** az alábbi lehetőségei vannak:

* **Alapértelmezett séma**. Az összekötő észleli a séma a Domino-kiszolgálóról. Ez a beállítás lesz az alapértelmezett beállítás.
* **DSML-séma**. Csak akkor használható, ha a Domino-kiszolgáló nem fedi fel a séma. Ezután hozzon létre egy DSML fájlt, a séma, és ehelyett importálja azt. A DSML további információkért lásd: [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Ha a Tovább gombra kattint, a felhasználónév és jelszó konfigurációs paraméterek ellenőrzése.

### <a name="global-parameters"></a>Globális paraméterek
A globális paraméterek oldalon az adott időzóna és az importálás konfigurálása, és exportálja a műveletet.  
![Globális paraméterek](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

A **Domino-kiszolgáló időzónája** paraméter határozza meg a Domino-kiszolgáló helyét.

Ez a beállítás támogatásához szükséges **különbözeti importálás** műveletek mert lehetővé teszi, hogy a szinkronizálási szolgáltatást határozza meg a módosításokat az utolsó két importálásokat között.

>[!Note]
2017. márciusi frissítés verziójától kezdve a globális paraméterek képernyő tartalmaz a beállítást, a felhasználó levelezési adatbázis törlése a felhasználó törlése közben.

![Törli a felhasználó postaládájához](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Módszer beállításainak importálása
A **végre teljes Import alapján** ezeket a lehetőségeket biztosítja:

* Keresés
* Nézet (ajánlott)

**Keresési** van a Domino, de az indexelő használata gyakori, hogy az indexek nem frissülnek a valós idejű és a kiszolgálóról visszaérkező adatokkal nem mindig megfelelő. A rendszer sok módosításokkal ezt a beállítást általában nem jól működik és biztosít bizonyos esetekben törli hamis. Azonban **keresési** gyorsabb, mint **nézet**.

**Nézet** lehetőség ajánlott, mivel az adatok megfelelő állapotban biztosít. Némileg lassabban futnak, mint a keresés.

#### <a name="creation-of-virtual-contact-objects"></a>Virtuális kapcsolattartási objektumok létrehozása
A **létrehozását lehetővé \_forduljon objektum** ezeket a lehetőségeket biztosítja:

* None
* Nem-hivatkozási érték
* Referencia- és nem hivatkozásos értékek

A Domino hivatkozás attribútumok sok különböző formátumokban való hivatkozáshoz más objektumok is tartalmazhat. Határoz meg különböző verziója, az összekötő valósít meg szeretnénk \_objektumok, más néven forduljon **virtuális névjegyek** (VC). Ezek az objektumok van, lehetővé teszi azok csatlakoztatását a meglévő MV-objektum létrehozása, vagy új objektumként várható. Így megőrizhetők attribútum hivatkozik.

Ez a beállítás engedélyezésével és a hivatkozási attribútum tartalma nem DN formátumban, ha egy \_forduljon objektum jön létre. Például egy csoport egy tagja attribútum SMTP-címeket tartalmazhat. Akkor is rövid_név és egyéb attribútumai hivatkozási attribútum szerepel. A jelen esetben válassza ki a **nem-hivatkozási érték**. Ez a konfiguráció a leggyakrabban használt beállítás Domino-megvalósítások esetében.

Lotus Domino van konfigurálva külön címtárakat ugyanazt az objektumot képviselő különböző megkülönböztető névvel rendelkező, esetén is létrehozására \_objektumok kérjen címjegyzék található hivatkozás értékeket. A jelen esetben válassza ki a **és a nem-hivatkozási** lehetőséget.

Ha több érték szerepel az attribútum **FullName** a Domino, majd is engedélyezni szeretné a virtuális partnerek létrehozása, hivatkozások feloldhatók legyenek. Ez az attribútum például rendelkezhet több érték házasság vagy felbontására után. Jelölje be a jelölőnégyzetet **engedélyezése... Több érték tartozik FullName** ehhez a forgatókönyvhöz.

A megfelelő attribútumok való csatlakozás a \_forduljon objektumok szeretné csatlakoztatni a MV-objektum.

Ezek az objektumok rendelkeznek VC =\_forduljon hozzá a megkülönböztető név.

#### <a name="import-settings-conflict-object"></a>Beállítások importálása, ütköző objektum
**Ütközés objektum kizárása**

Nagy Domino megvalósításában akkor lehetséges, hogy több objektum rendelkezik-e az azonos DN replikációjával kapcsolatos problémák miatt. Ebben az esetben az összekötő két objektumok különböző UniversalIDs, de ugyanazon megkülönböztető neve jelenik meg. Az ütközés miatt a kapcsolódási térbe létrehozása egy átmeneti objektum. Az összekötő figyelmen kívül hagyhatja ezt a replikációs áldozatai, Domino a kijelölt objektumok. Javasolt az adatok megőrzése a jelölőnégyzet be van jelölve.

#### <a name="export-settings"></a>Beállítások exportálása
Ha a beállítás **AdminP használja a hivatkozások frissítését** nincs bejelölve, akkor közvetlen hívásakor exportálása hivatkozás attribútumait, például a tagja, és nem használja a AdminP folyamat. Csak akkor használja ezt a beállítást, ha AdminP nem konfiguráltak hivatkozási egységének fenntartására szolgáló módszert.

#### <a name="routing-information"></a>Útvonal-információkat
Az Domino lehetséges, hogy rendelkezik-e a hivatkozási attribútum a megkülönböztető név az utótagnak pedig a beágyazott útvonal-információkat is. Például egy csoport a tag attribútumot tartalmazhat **CN =example/organization@ABC**. A utótag @ABC útválasztási adatokat. Az útvonal-információkat e-maileket küldhet a megfelelő Domino rendszerben, ami a rendszer egy másik vállalatnál lehet Domino használják. Az útválasztási információ mezőben adja meg az útválasztási utótagokat a szervezetben használt hatókörében az összekötő. A következő értékek egyike a hivatkozási attribútum található utótagként, az útvonal-információkat a hivatkozási távolítja el. Az útválasztási utótagot a egy hivatkozási érték nem rendelhető hozzá egy ezeket az értékeket adja meg, ha egy \_forduljon objektum jön létre. Ezek \_forduljon objektumok jönnek létre **RO = @<RoutingSuffix>**  beszúrt megkülönböztető neve. Ezek \_forduljon objektumokat a következő attribútumok is bekerülnek valós objektumhoz való csatlakozás szükség esetén engedélyezze: \_routingName, \_ügyintéző, \_displayName, és UniversalID.

#### <a name="additional-address-books"></a>További címtárakat
Ha nem rendelkezik **directory segítséget** telepítve, amely biztosítja, hogy a másodlagos címtárakat nevét, majd manuálisan adja meg ezeket a címtárakat.

#### <a name="multivalued-transformation"></a>Többértékű átalakítása
Lotus Domino sok attribútumokat többértékű adatelemeket. A megfelelő metaverse attribútumokat általában egyetlen értékelni. Az importálás és az exportálási művelet beállítás konfigurálásával engedélyezi az összekötő számára szükséges fordítása érintett attribútumát.

**Export**  
Az exportálási művelet beállítás két módot támogat:

* Elem hozzáfűzése
* Cserélje le a cikk

**Cserélje le a cikk** – ezt a beállítást, ha az összekötő mindig távolítsa el az aktuális értékeket az attribútum Domino, és lecseréli azokat a megadott értékek. A megadott érték lehet egyértékű vagy többértékű adatelemeket.

Példa: A Segéd egy személy objektum attribútumnak a következő értékeket:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Ha egy új Segéd nevű **David Alexander** hozzá van rendelve az személy objektumon, az eredmény van:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Elem hozzáfűzése** – ezt a beállítást, ha az összekötő megtartja a meglévő Domino és insert új értékeket az listája tetején attribútum értékét.

Példa: A Segéd egy személy objektum attribútumnak a következő értékeket:

* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Ha egy új Segéd nevű **David Alexander** hozzá van rendelve az személy objektumon, az eredmény van:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importálás**  
Az importálási művelet beállítás két módot támogat:

* Alapértelmezett
* Többértékű egyetlen értékre

**Alapértelmezett** – Ha az alapértelmezett beállítás, az összes attribútumok importált összes értéket választja.

**Egyetlen értékre többértékű** – Ha ezt a lehetőséget választja egy többértékű attribútum egy egyértékű attribútum alakítható. Ha egynél több értéket, az érték a felső (Ez az érték egyben általában a legújabb) használatos.

Példa: A Segéd egy személy objektum attribútumnak a következő értékeket:

* CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Az attribútum a legutóbbi frissítés **David Alexander**. Az importálási művelet beállítás Multivalued egyetlen értékre, mert összekötő csak importálja **David Alexander** a kapcsolódási térbe kerülnek.

A logika konvertálása egyértékű attribútumok a többértékű attribútumok nem felel meg a csoport tagja attribútum és a személy fullname attribútum.

Az importálás konfigurálása, és egy attribútum, többértékű attribútumok átalakítási szabályok exportálási kivételként a globális szabály is lehetséges. Adja meg ezt a lehetőséget, írja be a következőt [objecttype]. [attributename] az a **attribútum kivétellistát importálása** és **attribútum kivétellistát exportálása** szövegmezőket. Például ha Person.Assistant beírásakor, és a globális jelző be van állítva, minden értéket importálása, csak az első értéket az importált a Segéd.

#### <a name="certifiers"></a>Képesítést adók engedélyezése
Minden szervezet vagy szervezeti egység az összekötő alapján vannak listázva. Nem fogja tudni az elsődleges címjegyzék személy objektumok exportálása, az ahhoz tartozó jelszót a certifier szükség.

Ha minden képesítést adók engedélyezése ugyanazt a jelszót, a **jelszavát minden Certifers** is használható. Ezután írja be ide, és csak adja meg a certifier fájlt.

Ha csak importálja, majd nincs meg minden képesítést adók engedélyezése.

### <a name="configure-provisioning-hierarchy"></a>Kiépítési hierarchia konfigurálása
A Lotus Domino-összekötő konfigurálásakor a párbeszédpanelen lap kihagyása. A Lotus Domino-összekötő nem támogatja a kiépítési hierarchia.  
![Telepítési hierarchia](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Partíciók és hierarchiák konfigurálása
Partíciók és hierarchiák konfigurálása, jelölje be az elsődleges címjegyzék NAB=names.nsf nevezik. Mellett az elsődleges címjegyzék választhat másodlagos címtárakat, ha vannak ilyenek.  
![Partíciók](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Attribútumok kijelölése
Az attribútumok konfigurálásakor ki kell választania az összes attribútum, amely fűzve előtagként  **\_MMS\_**. Ezek az attribútumok szükség, amikor új objektumok Lotus Domino

![Attribútumok](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Objektum életciklusának kezelésére
Ez a szakasz áttekintést Domino a különböző objektumokat.

### <a name="person-objects"></a>Személy-objektumok
A személy objektum szervezet és a szervezeti egység felhasználók jelöli. Alapértelmezett attribútumok mellett a Domino rendszergazda is hozzáadhat egyéni attribútumok egy személy objektumhoz. Legalább egy személy objektum tartalmaznia kell az összes kötelező attribútumok. A kötelező attribútumok teljes listáját lásd: [Lotus Notes tulajdonságok](#lotus-notes-properties). Egy személy objektum regisztrálásához a következő előfeltételeknek kell teljesülniük:

* A címjegyzék (names.nsf) kell rendelkeznie nincs megadva, az elsődleges címjegyzék kell.
* Rendelkeznie kell a O/OU certifier azonosítója és a jelszó egy adott felhasználó regisztrálása a szervezet vagy szervezeti egység.
* Meg kell adni egy meghatározott Lotus Notes egy személy objektum tulajdonságait. Ezeket a tulajdonságokat a személy objektum történő üzembe helyezéséhez használt. További információkért lásd a témakör [Lotus Notes tulajdonságok](#lotus-notes-properties) újabb ebben a dokumentumban.
* A kezdeti HTTP személy jelszava egy attribútum és a készlet kiépítése során.
* A személy objektum a következő három támogatott típusok egyike lehet:
  1. A normál felhasználók, akik egy levelezési fájlt és egy felhasználói azonosító
  2. Központi felhasználói (a normál felhasználói központi adatbázisfájlokat tartalmazó)
  3. Névjegyek (nincs azonosítója fájl rendelkező felhasználó)

(Kivéve a névjegyek) személyek további sorolhatók Velünk és nemzetközi felhasználók értéke által meghatározott módon a \_MMS\_IDRegType tulajdonság. Ezen személyek Lotus Domino-kiszolgálókhoz, a megjegyzéseket ügyfél van a megjegyzések azonosítót, és egy személy dokumentumot. Megjegyzések mail használata, majd is rendelkeznek egy levelezési fájl. A felhasználónak aktiválásukhoz regisztrálva kell lennie. További információkért lásd:

* [Megjegyzések felhasználók](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [Felhasználó regisztrálása](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Felhasználók kezelése](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Felhasználók átnevezése](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Ezeket a műveleteket Lotus Domino végez, és ezután importálja a szinkronizálási szolgáltatást.

### <a name="resources-and-rooms"></a>Erőforrások és a
Egy erőforrást egy másik típus egy Lotus Domino-adatbázis. Erőforrások lehetnek a különböző típusú berendezések, például a kivetítők konferenciaterem. Lotus Domino-összekötő által támogatott erőforrások, az erőforrástípust attribútum által meghatározott altípusainak van:

| Az erőforrás típusát | Erőforrástípus attribútum |
| --- | --- |
| Hely |1 |
| Erőforrás (Other) |2 |
| Online értekezletek |3 |

Az erőforrás objektumtípus működjön, az alábbi feltételek teljesülése esetén:

* Erőforrás-foglalás adatbázis már léteznie kell a csatlakoztatott Domino-kiszolgálón
* Az erőforrás már definiálva van a helyhez

Az erőforrás-foglalás adatbázis három típusú dokumentumok tartalmazza:

* Hely profil
* Erőforrás
* Foglalás

Az erőforrás-foglalás adatbázis beállításával kapcsolatos további információkért lásd: [az erőforrás-fenntartások adatbázisának beállítása](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Hozzon létre, frissítsen, és törli az erőforrást**  
A létrehozási, frissítési és törlési műveleteket az erőforrás-foglalás adatbázisban Lotus Domino-összekötő által. Erőforrások Names.nsf (Ez azt jelenti, hogy az elsődleges címjegyzék)-dokumentumokként jönnek létre. Módosítása és törlése erőforrások kapcsolatos további információkért lásd: [szerkesztése és törlése az erőforrás-dokumentumok](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importálás és exportálás erőforrások**  
Az erőforrások importálása, és a szinkronizálási szolgáltatást, mint minden más objektum exportált. Válassza ki az objektumtípus-erőforrásként konfigurálása során. Sikeres exportálási művelet részletei erőforrástípus, konferencia adatbázis, és egy helynév kell rendelkeznie.

### <a name="mail-in-databases"></a>Mail-adatbázisok
Egy E-mail-adatbázisban egy adatbázis, amely arra tervezték, hogy az üzenetek fogadására. Lotus Domino postaládával, amely nincs társítva van egy adott Lotus Domino-felhasználói fiókot (Ez azt jelenti, hogy nincs saját fájl és a jelszót). Egy e-mail-adatbázisban egy egyedi felhasználói azonosítóját ("rövid neve"), és a saját e-mail cím társított rendelkezik.

Ha egy külön postaláda saját e-mail címmel, amely a különböző felhasználók között megosztható legyen szükség van (például group@contoso.com), egy e-mail-adatbázisban jön létre. A hozzáférés a postafiókot keresztül a hozzáférés-vezérlési lista (ACL), a felhasználók neveit, a megjegyzések, amelyek számára engedélyezett nyissa meg a postaládát tartalmazó szabályozható.

A kötelező attribútumok listáját lásd a témakör [kötelező attribútumok](#mandatory-attributes) című cikkben.

Ha egy adatbázis célja, hogy egy leveleket fogadni, E-mail-adatbázisban dokumentum Lotus Domino jön létre. Ez a dokumentum minden olyan kiszolgálóra, az adatbázis másolatát tárolja Domino Directory léteznie kell. Mail-adatbázisban dokumentum létrehozása részletes ismertetését lásd: [Mail-adatbázisban dokumentum létrehozása](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Mielőtt létrehozna egy E-mail-adatbázisban, az adatbázis már léteznie kell (kell hozott létre Lotus Admin) a Domino-kiszolgálón.

### <a name="group-management"></a>Csoportok kezelése
Részletes áttekintést nyújt a Lotus Domino kezelése lekérheti a következőket:

* [Csoportok használata](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Csoport létrehozása](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Létrehozás és módosítás csoportok](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Csoportok kezelése](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Egy csoport átnevezése](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Jelszókezelés
Lotus Domino regisztrált felhasználó a jelszavak két típusa van:

1. Felhasználói jelszó (User.id fájlban tárolt)
2. Internet / HTTP jelszó

A Lotus Domino-összekötő támogatja a csak HTTP jelszóval rendelkező műveleteket.

A jelszókezelés végrehajtásához engedélyeznie kell a jelszókezelés az összekötő a felügyeleti ügynök tervezőben. A jelszókezelés engedélyezéséhez jelölje be **jelszókezelés engedélyezése** a a **bővítmények konfigurálása** párbeszédpanel lap.  
![Bővítmények konfigurálása](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino connector támogatása a következő műveletek internetes jelszó:

* Beállította a jelszót: Beállította a jelszót a felhasználónak a Domino HTTP/Internet új jelszót állítja be. Alapértelmezés szerint a fiók is oldva. Az unlock jelzőt a WMI-felületén a szinkronizálási motor van közzétéve.
* Jelszó módosítása: Ebben a forgatókönyvben a felhasználó érdemes lehet módosítani a jelszavát vagy kéri, hogy a megadott idő után módosítsa a jelszavát. A művelet eltarthat helyezze el, a (a régi és az új jelszót) is kötelező. Miután módosította, Lotus Domino frissül az új jelszót.

További információkért lásd:

* [Az Internet zárolási szolgáltatás használata](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Internet-jelszavak kezelése](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Kapcsolódó információk
Ez a rész felsorolja, például a attribútum leírásokat és a Lotus Domino-összekötő attribútum követelményei.

### <a name="lotus-notes-properties"></a>Lotus Notes tulajdonságok
Amikor személy objektumok Lotus Domino-címtárat, az objektumok fel az adott értékek rendelkeznie kell egy adott készletét tulajdonságok. Ezek az értékek csak a szükséges műveletek létrehozása.

A következő táblázat ezeket a tulajdonságokat és a leírását.

| Tulajdonság | Leírás |
| --- | --- |
| \_MMS_AltFullName |A felhasználó alternatív teljes neve. |
| \_MMS_AltFullNameLanguage |A használni kívánt nyelvet, a felhasználó az alternatív teljes nevének megadását. |
| \_MMS_CertDaysToExpire |Az aktuális dátum előtt a tanúsítvány napok száma lejár. Ha nincs megadva, az alapértelmezett dátum két év az aktuális dátum utáni. |
| \_MMS_Certifier |Tulajdonság, amely a certifier szervezeti hierarchia nevét tartalmazza. Példa: OU = OrganizationUnit, O szervezeti, C = ország =. |
| \_MMS_IDPath |Ha a tulajdonság nincs megadva, nincs felhasználói azonosító fájl a szinkronizálási kiszolgálón helyileg jön létre. Ha a tulajdonság nevét, egy felhasználói azonosító fájl a madata mappában jön létre. A tulajdonság egy teljes elérési út is tartalmazhat. |
| \_MMS_IDRegType |Személyek névjegyek, a US felhasználók és a nemzetközi felhasználók is kell sorolni. Az alábbi táblázat a lehetséges értékek: <li>0 - ügyfél</li><li>1 - US felhasználó</li><li>2 - nemzetközi felhasználói</li> |
| \_MMS_IDStoreType |Kötelező tulajdonság az Amerikai Egyesült Államok és nemzetközi felhasználók. A tulajdonság, amely meghatározza, hogy a felhasználói azonosító tárolja a megjegyzések címjegyzék vagy a személy e-mail fájlt mellékletként egész értéket tartalmaz. Ha a felhasználói azonosító fájl címjegyzék mellékletet, opcionálisan létrehozás fájlba \_MMS_IDPath. <li>Üres - tároló azonosítója fájl azonosítója tárolóban, nincs azonosító fájlt (a névjegyek).</li><li> 1 – a megjegyzések címjegyzék mellékletet. A \_MMS_Password tulajdonságot be kell állítani felhasználói azonosító fájlokat mellékletek</li><li>2 - személy E-mail fájl Azonosítóját tárolja. A \_false értékre kell állítani MMS_UseAdminP ahhoz, hogy a levelezési fájl a személy regisztrálás során hozható létre. A \_MMS_Password tulajdonságot kell beállítani a felhasználói azonosító fájlok.</li> |
| \_MMS_MailQuotaSizeLimit |A száma, amelyek jogosultak az e-mail fájl adatbázis mérete (MB). |
| \_MMS_MailQuotaWarningThreshold |Mérete (MB), mielőtt a rendszer figyelmeztetést ad ki az e-mail fájl adatbázis engedélyezett száma. |
| \_MMS_MailTemplateName |Az e-mail sablon fájl, amely a felhasználó e-mail fájl létrehozásához használt. Ha egy sablon meg van adva, a levelezési fájl jön létre a megadott sablon használatával. Ha nincs sablon meg van adva, az alapértelmezett sablon fájllal a fájl létrehozásához. |
| \_MMS_OU |Nem kötelező tulajdonság, amely alatt a certifier OU neve. Ez a tulajdonság a névjegyek üresnek kell lennie. |
| \_MMS_Password |A felhasználók számára szükséges tulajdonság. A tulajdonság az objektum az azonosító fájlhoz tartozó jelszót tartalmazza. |
| \_MMS_UseAdminP |A tulajdonság igaz értékre, ha a levelezési fájl kell létrehozni a AdminP folyamat a Domino-kiszolgálón (aszinkron, hogy az exportálási folyamat) kell állítani. Ha tulajdonsága hamis értékre van beállítva, a levelezési fájl hozza létre a Domino felhasználói (szinkron az exportálási folyamat). |

Az azonosító fájllal, egy felhasználó a \_MMS_Password tulajdonság értéket kell tartalmaznia. E-mail hozzáférés a Lotus Notes ügyfélen keresztül a felhasználó levelezokiszolgalo és MailFile tulajdonságainak értéket kell tartalmaznia.

E-mail webböngészőn keresztül eléréséhez a következő tulajdonságok értékeket kell tartalmaznia:

* MailFile – a levelezési fájl tárolására Lotus Domino-kiszolgálón elérési útját kötelező tulajdonság.
* Levelezokiszolgalo - kötelező tulajdonság, amely tartalmazza a Lotus Domino-kiszolgáló nevét. Ez az érték esetén a Domino-kiszolgálón a Lotus levelezési fájl létrehozásakor használandó.
* HTTPPassword - tulajdonságot nem kötelező megadni, amely tartalmazza az objektum a webes hozzáférési jelszava.

A kiszolgálóhoz való hozzáféréshez Domino mail képesség nélkül, a HTTPPassword tulajdonság értéket kell tartalmaznia. A MailFile tulajdonság és a levelezokiszolgalo tulajdonság üres is lehet.

A \_MMS_ IDStoreType (Mail fájl azonosító tároló), 2 = NotesRegistrationclass MailSystem tulajdonságának értéke REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Kötelező attribútumok
A Lotus Domino-összekötő elsősorban támogatja az ilyen típusú objektumok (a dokumentum esetében):

* Csoport
* Mail-adatbázisban
* Személy
* Ügyfél (nincs certifier rendelkező személy)
* Erőforrás

Ez a rész felsorolja a attribútumok megadása kötelező, minden támogatott objektum exportálása Domino-kiszolgálóhoz.

| Objektumtípus | Kötelező attribútumok |
| --- | --- |
| Csoport |<li>ListName</li> |
| Fő-adatbázisban |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Személy |<li>Vezetéknév</li><li>MailFile</li><li>Rövid_név</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Ügyfél (nincs certifier rendelkező személy) |<li>\_MMS_IDRegType</li> |
| Erőforrás |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>Erőforráskapacitás</li><li>Hely</li><li>Megjelenítendő név</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Gyakori problémákat és kérdéseket
### <a name="schema-detection-does-not-work"></a>Séma észlelése nem működik.
Nem fogja tudni észlelni a séma, nem szükséges, hogy a schema.nsf fájl megtalálható-e a Domino-kiszolgálón. Ez a fájl csak akkor jelenik meg, ha az LDAP a kiszolgálóra van telepítve. Ha a séma nem észlelhető, ellenőrizze a következőket:

* A fájl schema.nsf megtalálható a gyökérmappában Domino kiszolgáló
* A felhasználó rendelkezik a szükséges engedélyekkel schema.nsf fájlban.
* Az LDAP-kiszolgáló újraindítását kényszeríti. Nyissa meg **Lotus Domino konzol** és **mondja el LDAP ReloadSchema** parancs a séma újbóli betöltéséhez.

### <a name="not-all-secondary-address-books-are-visible"></a>Nem minden másodlagos címtárakat láthatók.
A szolgáltatás támaszkodik a Domino-összekötő **Directory segítséget** kell lennie a másodlagos címtárakat találja. Ha a másodlagos címtárakat hiányzik, ellenőrizze, hogy ha [Directory segítséget](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) engedélyezve és konfigurálva a Domino-kiszolgálón.

### <a name="custom-attributes-in-domino"></a>Egyéni attribútumok Domino
Többféleképpen is a Domino a séma kiterjesztése, így az egyéni attribútumként fogyasztható az összekötő által jelenik meg.

**1. módszer: Lotus Domino-séma kiterjesztése**

1. Hozzon létre egy másolatot Domino Directory sablon {PUBNAMES. NTF} következő [ezeket a lépéseket](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (kell az alapértelmezett IBM Lotus Domino könyvtár sablont nem testre):
2. Nyissa meg a másolási a Domino directory sablon {CONTOSO. NTF} sablont, amely Domino-tervezőben hozta létre, és kövesse az alábbi lépéseket:
   * Kattintson a megosztott elemek, és bontsa ki a segédűrlapok
   * Kattintson duplán a ${ObjectName} InheritableSchema segédűrlap (ahol a {ObjectName} annak a neve, az alapértelmezett strukturális objektumosztály, például: személy).
   * Ez az attribútum hozzáadása {MyPersonAtrribute} séma és a megfelelő kívánt attribútum neve. Hozzon létre egy mező válassza ki a **létrehozása** menüre, és válassza ki azt **mező** menüből.
   * A hozzáadott mezőben az típusának, stílusának, méret, betűtípus, és egyéb kapcsolódó paraméterek mező tulajdonságai ablakban kiválasztásával tulajdonságainak beállítása.
   * Tartsa a attribútum értéke alapértelmezés szerint ugyanaz, mint az attribútum, amely a megadott név (például a MyPersonAttribute attribútum megadása esetén ne ugyanazzal a névvel az alapértelmezett érték).
   * A {ObjectName} $InheritableSchema segédűrlap mentse a frissített értékekkel.
3. Cserélje le a Domino Directory sablon {PUBNAMES. NTF} az új egyéni sablon {CONTOSO. NTF} következő [ezeket a lépéseket](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Zárja be a Domino rendszergazda, és nyissa meg az LDAP-szolgáltatás újraindítása, és töltse be újra az LDAP-séma Domino-konzolt:
   * Domino-konzolon, a parancs a beszúrása **Domino parancs** az LDAP - szolgáltatás újraindítása bejegyezve szöveg [indítsa újra a feladat LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * Töltse be újra az LDAP séma paranccsal kérje meg az LDAP - mondja el LDAP ReloadSchema
5. Nyitott Domino-rendszergazda, és válassza a felhasználók és csoportok lapon attribútumok domino megjelenik hozzáadása személy.
6. Nyissa meg a Schema.nsf **fájlok** lapra, és tekintse meg a hozzáadott attribútum dominoPerson LDAP objektumosztály van megjelennek.

**2. módszer: Hozzon létre egy auxClass egyéni attribútum, és rendelje hozzá a következő objektumosztály**

1. Hozzon létre egy másolatot Domino Directory sablon {PUBNAMES. NTF} következő [ezeket a lépéseket](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (soha nem a az alapértelmezett IBM Lotus Domino könyvtár sablon testreszabása):
2. Nyissa meg a másolási a Domino directory sablon {CONTOSO. NTF} létrehozott sablont, Domino-tervezőben.
3. A bal oldali panelen jelölje ki a megosztott kódot, majd a segédűrlapok.
4. Kattintson az új segédűrlap
5. Tegye a következőket adhatja meg az új segédűrlap tulajdonságai:
   * Nyissa meg az új segédűrlappal, válassza ki a Tervező - segédűrlap tulajdonságok
   * A Name tulajdonság mellett adja meg a kiegészítő objektumosztály – például TestSubform nevét.
   * Tartsa a beállítások tulajdonság "Felvétele a Beszúrás segédűrlap... párbeszédpanel" beállítás
   * Kapcsolja ki a beállítások tulajdonság "leképezési továbbítása HTML megjegyzésekben."
   * A többi tulajdonság változatlanul hagyja, és a segédűrlap tulajdonságainak bezárásához.
   * Mentse és zárja be az új segédűrlap.
6. Tegye a következőket a kiegészítő objektumosztály meghatározása mező felvétele:
   * Nyissa meg a létrehozott segédűrlap.
   * Válasszon létrehozása – mezőben.
   * Az alapismeretek lapon mező párbeszédpanel neve, mellett adja meg az tetszőleges nevet, például: {MyPersonTestAttribute}.
   * A hozzáadott mezőben az típusának, stílusának, méret, betűtípus és kapcsolódó tulajdonságok kiválasztásával tulajdonságainak beállítása.
   * Tartsa a attribútum értéke alapértelmezés szerint ugyanaz, mint az attribútum, amely a megadott név (például a MyPersonTestAttribute attribútum megadása esetén ne ugyanazzal a névvel az alapértelmezett érték).
   * A segédűrlap mentse a frissített értékekkel, és tegye a következőket:
     * A bal oldali panelen jelölje ki a megosztott kódot, majd a segédűrlapok
     * Válassza ki az új segédűrlap, és válassza a Tervező - Tervező tulajdonságai.
     * Kattintson a bal oldali harmadik fülre, és válassza ki **Ez a Tiltás tervezési változás propagálása**.
7. Nyissa meg a {ObjectName} $ExtensibleSchema segédűrlap, ({ObjectName} esetén az alapértelmezett strukturális object osztályt, például – személy nevét).
8. Erőforrás beszúrása, és válassza ki a segédűrlap (létrehozott, például – TestSubform), és mentse a ${ObjectName} ExtensibleSchema segédűrlap.
9. Cserélje le a Domino Directory sablon {PUBNAMES. NTF} az új egyéni sablon {CONTOSO. NTF} következő [ezeket a lépéseket](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Zárja be a Domino rendszergazda, és nyissa meg az LDAP-szolgáltatás újraindítása, és töltse be újra az LDAP-séma Domino-konzolt:
    * Domino-konzolon, a parancs a beszúrása **Domino parancs** az LDAP - szolgáltatás újraindítása bejegyezve szöveg [indítsa újra a feladat LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Töltse be újra az LDAP séma paranccsal kérje meg az LDAP **mondja el LDAP ReloadSchema**.
11. Nyissa meg a Domino rendszergazda, és válassza ki a felhasználók és csoportok lapon, láthatja a hozzáadott attribútum domino Hozzáadás személy is megjelenik (a többi a lap).
12. Nyissa meg a Schema.nsf **fájlok** lapra, és tekintse meg a hozzáadott attribútum TestSubform LDAP kiegészítő objektumosztály alatt is megjelenik.

**3. módszer: Az egyéni attribútum hozzáadása a ExtensibleObject osztály**

1. A gyökérkönyvtár helyezve {Schema.nsf} fájl megnyitása
2. Válassza ki az LDAP objektum osztályokat a bal oldali menüjében **összes Sémadokumentumokban** kattintson **objektum hozzáadása az osztály** gombra:
3. Adja meg az LDAP-név {zzzExtensibleSchema} formájában (ahol a zzz azt az alapértelmezett strukturális object osztályt, például személy nevét). Például a séma kiterjesztéséhez személy objektumosztály nevezze LDAP {PersonExtensibleSchema}.
4. Adja meg a fölérendelt objektum osztály neve legyen a séma kiterjesztése. Például a személy objektumosztály-séma kiterjesztése, adja meg a fölérendelt objektum osztálynév {dominoPerson}:
5. Adjon meg egy érvényes OID az object osztályt megfelelő.
6. Válassza ki az adott követelmény kötelező vagy választható attribútumtípust mezők Extended/egyéni attribútumok:
7. A ExtensibleObjectClass adásakor attribútumot, kattintson a **mentés és Bezárás**.
8. Egy ExtensibleObjectClass megfelelő alapértelmezett objektumosztály kiterjesztett attribútummal rendelkező jön létre.

## <a name="troubleshooting"></a>Hibaelhárítás
* Az összekötő hibaelhárítása naplózásának engedélyezése a további információkért lásd: a [hogyan ETW-nyomkövetés engedélyezése a csatlakozók](http://go.microsoft.com/fwlink/?LinkId=335731).
