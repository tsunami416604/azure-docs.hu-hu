---
title: Connector Verziókiadásai |} A Microsoft Docs
description: Ez a témakör felsorolja az összekötők összes kiadása a Forefront Identity Manager (FIM) és a Microsoft Identity Manager (MIM)
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9bbf75f258f9853803ca4c00155eb186ceca54a3
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916406"
---
# <a name="connector-version-release-history"></a>Összekötő verziókiadásai
Az összekötők a Forefront Identity Manager (FIM) és a Microsoft Identity Manager (MIM) rendszeresen frissülnek.

> [!NOTE]
> Ez a témakör csak a FIM és a MIM van. Ezek az összekötők nem támogatottak az Azure AD Connect telepítés. Elérhető összekötők verzióra a megadott Build esetén az aad Connect előre telepített.


Ez a témakör az összekötők kiadott összes verzió listázása.

Kapcsolódó hivatkozások:

* [Töltse le a legújabb összekötők](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Általános LDAP-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) referenciadokumentációt
* [Általános SQL-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) referenciadokumentációt
* [Web Services-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) referenciadokumentációt
* [PowerShell-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) referenciadokumentációt
* [Lotus Domino-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) referenciadokumentációt


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Rögzített problémák:
* Megoldott ConnectorsLog System.Diagnostics.EventLogInternal.InternalWriteEvent(Message: A device attached to the system is not functioning)
* Ebben a kiadásban az összekötők kell frissíteni a kötés irányítja át a 3.3.0.0-4.1.3.0 4.1.4.0 miiserver.exe.config a
* Általános webes szolgáltatások:
    * Megoldott érvényes JSON-válasz nem lehet menteni a konfigurációs eszköz
* Általános SQL:
    * Exportálás mindig csak frissítés lekérdezés törlése működéséhez állít elő. Hozzáadott hozható létre lekérdezés törlése
    * Az SQL-lekérdezést, mely objektumokat a művelet a különbözeti importálás, ha "A különbözeti stratégia" Change Tracking rendszerriasztások mechanizmusában. Ez a megvalósítás ismert korlátozás a: Change Tracking móddal különbözeti importálás nem többértékű attribútumok változásainak követése
    * Hozzáadott lehetősége arra, hogy egy törlési lekérdezés esetekhez generáljon többértékű attribútum utolsó értékét törölni kell, és a sor nem tartalmaz értéket, amelyet törölni kell kivételével minden egyéb adatot.
    * Kezelése mikor System.ArgumentException végrehajtott SP által a kimeneti paraméterek 
    * Ha a műveletet az export mezőbe, amely a varbinary(max) típusú helytelen lekérdezés
    * Probléma parameterList változó inicializálása kétszer (az a funkciók ExportAttributes és GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Rögzített problémák:

* Lotus Notes:
  * Egyéni képesítést adók engedélyezése beállítás szűrése
  * Importálás az osztály ImportOperations rögzíteni a meghatározását, hogy milyen műveleteket a "Nézetek" módban, és amely a "Search" üzemmódban futtatható.
* Általános LDAP:
  * OpenLDAP Directory entryUUI helyett a forráshorgony DN-t használja. Új beállítás GLDAP összekötőt, amely lehetővé teszi, hogy módosítani a forráshorgony
* Általános SQL:
  * Rögzített exportálási mezőbe, amely a varbinary(max) típusú.
  * Bináris adatokat egy adatforrásból való hozzáadásakor CSEntry objektum, a DataTypeConversion függvény nulla bájtot sikertelen volt. Rögzített DataTypeConversion függvény CSEntryOperationBase osztály.




### <a name="enhancements"></a>Fejlesztések:

* Általános SQL:
  * Hajtsa végre a mód beállításával tárolt eljárást az elnevezett paraméterek vagy a neve nem kerül be a "Globális Parameters" oldalon általános SQL kezelőügynök konfigurációs ablak. Az oldal "Globális Parameters" hiba "Felhasználás elnevezett paraméterek egy tárolt eljárás végrehajtására" mód az execute tárolt eljárás felelős címkével jelölőnégyzetet neve paraméterek, vagy nem.
    * Lehetővé teszi az elnevezett paraméterek tárolt eljárás végrehajtása jelenleg csak olyan adatbázisok, IBM DB2-höz és MSSQL működik. Az Oracle- és MySQL adatbázisok Ez a módszer nem működik: 
      * Az SQL-szintaxis a MySQL elnevezett paramétereket nem támogatja a tárolt eljárásokban.
      * Az Oracle ODBC-illesztő nem támogatja a tárolt eljárások elnevezett paraméterek elnevezett paraméterek)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Rögzített problémák:

* Általános webes szolgáltatások:
  * Kijavítva egy probléma megakadályozza, hogy egy SOAP-projektet jöjjenek létre, amikor két vagy több végpontot volt.
* Általános SQL:
  * Az importálási művelet az GSQL volt nem konvertálása idő megfelelően összekötőterében mentésekor. Az alapértelmezett dátum és idő formátumban az összekötőtérben, a GSQL módosult a "éééé-hh-nn hh:mm:ssZ" a "éééé-HH-NN HH:mm:ssZ".

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Rögzített problémák:

* Általános webes szolgáltatások:
  * A Wsconfig eszköz nem megfelelő átalakítani a Json-tömb, a REST szolgáltatás esetén "mintakérelemből" való. Ez problémákat okozott a szerializálása a Json-tömb, a többi kéréshez.
  * Webes szolgáltatás-összekötő konfigurálása eszközben nem támogatja a hely szimbólumok használatának JSON attribútumnevek 
    * Helyettesítés mintát is hozzáadhatók manuálisan a WSConfigTool.exe.config fájlt, például: ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```
> [!NOTE]
> JSONSpaceNamePattern kulcsot kötelező megadni, ahogy exportálása a következő hibaüzenetet kap: üzenet: üres a név nem lehet. 

* Lotus Notes:
  * Ha a beállítás **lehetővé teszik egyéni képesítést adók engedélyezése a szervezet vagy szervezeti egység** le van tiltva, akkor az összekötő nem tud exportálásakor (frissítés) után az exportálási folyamat Domino de exportfájljához exportált attribútumok egy Szinkronizálási KeyNotFoundException küld vissza. 
    * Ennek oka az, átnevezése sikertelen való DN (felhasználónév attribútum) módosítsa az alábbi attribútumok módosításával:  
      - Vezetéknév
      - Keresztnév
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - szervezeti egység
      - altcommonname

  * Amikor **lehetővé teszik egyéni képesítést adók engedélyezése a szervezet vagy szervezeti egység** beállítás engedélyezve van, de képesítést a szükséges adók engedélyezése továbbra is üresek, akkor KeyNotFoundException történik.

### <a name="enhancements"></a>Fejlesztések:

* Általános SQL:
  * **Forgatókönyv: újratervezve implementálva:** "*" funkció
  * **Megoldás leírása:** megközelítés megváltozott [többértékű hivatkozási attribútumok kezelése](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).


### <a name="fixed-issues"></a>Rögzített problémák:

* Általános webes szolgáltatások:
  * Kiszolgálókonfiguráció nem importálható, ha az megtalálható a webszolgáltatás-összekötő
  * Több webes szolgáltatással nem működik a webszolgáltatás-összekötő

* Általános SQL:
  * Nincs objektumtípusok egyetlen érték hivatkozott attribútumot fel vannak sorolva
  * Különbözeti importálás a Change Tracking stratégia törlések objektumot a rendszer eltávolítja az értéket a többértékű táblából
  * Az AS DB2-összekötőben GSQL OverflowException / 400

Lotus:
  * Keresés a szervezeti egységek GlobalParameters lap megnyitása előtt enable\disable hozzáadott lehetőséget

## <a name="114430"></a>1.1.443.0

Kiadás dátuma: 2017. március

### <a name="enhancements"></a>Fejlesztések

* Általános SQL:</br>
  **A forgatókönyv tünetek:** egy jól ismert korlátozás nevezhetjük csak egy hivatkozást egy objektumtípus engedélyezése és kereszthivatkozás tagjaival szükséges az SQL-összekötővel. </br>
  **Megoldás leírása:** hivatkozásokat a feldolgozási lépést a voltak "*" lehetőséget választja, minden kombinációja objektumtípusok ad vissza vissza a szinkronizálási motor.

>[!Important]
- Ezzel létrehoz számú helyőrzőket
- Fontos, hogy ellenőrizze, hogy a kiosztási egyedi platformfüggetlen objektumtípusokat.


* Általános LDAP:</br>
 **Forgatókönyv:** amikor csak néhány tárolók adott partíción van kijelölve, majd a keresés továbbra is végezheti el teljes partíció. Szinkronizálási szolgáltatás, de nem a MA, ami problémát okozhat teljesítménycsökkenést specifikus lesz szűrve. </br>

 **Megoldás leírása:** megváltozott GLDAP összekötő kódot, hogy az összes tároló keresztül lehetséges go és objektumok keresése azok helyett a teljes partícióban.


* Lotus Domino:

  **Forgatókönyv:** Domino mail törlés támogatása egy személy eltávolítása az exportálás során. </br>
  **Megoldás:** konfigurálható mail törlés támogatása egy személy eltávolítása az exportálás során.

### <a name="fixed-issues"></a>Rögzített problémák:
* Általános webes szolgáltatások:
 * A szolgáltatás URL-CÍMÉT az alapértelmezett módosításakor SAP wsconfig projektek webszolgáltatás konfigurációs eszközt, majd a következő hiba történik: az elérési út egy része nem található

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Általános LDAP:
 * GLDAP összekötő nem látja az összes attribútumot az AD LDS
 * Varázsló oldaltörések nem egyszerű felhasználónév attribútum az LDAP directory-sémát a észlelésekor
 * Különbözeti importálás miatt sikertelenül működő felderítési hibák teljes importálás során, ha nincs bejelölve a "objectclass" attribútum nem található
 * Egy "Partíciók és hierarchiák konfigurálása" konfigurációs lapján nem jelenik meg minden olyan objektumok típusának megegyezik a partícióra új kiszolgálók esetében az általános  
LDAP MA. Mutattak RootDSE partíció csak objektumokat.


* Általános SQL:
 * Javításra általános SQL vízjel különbözeti importálás nem importált többértékű attribútum
 * Többértékű attribútumok deleted\added értékek exportálásakor azok nem deleted\added az adatforrásban.  


* Lotus Notes:
 * Egy adott mező "Teljes neve" jelenik meg a metaverzumba megfelelően azonban a jegyzetek exportálásakor az attribútum értéke Null vagy üres.
 * Javítás a duplikált Certifier hiba
 * Amikor az objektum adatok nélkül van jelölve, a más objektumokkal Lotus Domino-összekötő majd azt hibaüzenetet kap a felderítési teljes-importálási végrehajtása közben.
 * Különbözeti importálás folyamatban futó a Lotus Domino-összekötő futtató, a végén a Microsoft.IdentityManagement.MA.LotusDomino.Service.exe szolgáltatás időnként értéket ad vissza hibát.
 * A csoporttagságot általános jól működik, és fenntartását, kivéve az Exportálás az egy felhasználó eltávolításakor a tagságát futtatásakor mutatja a frissítés sikeres, de a felhasználó a tagság Lotus Notes ténylegesen nem törlődnek.
 * Export mód kiválasztásához, "Hozzáfűzés elem alján" hozzá lett adva, a grafikus felhasználói Felülettel, Lotus MA többértékű attribútumok esetén az exportálás során új cikkek alján hozzáfűzni kívánt konfigurációs lehetőséget.
 * Összekötő hozzáadja a szükséges logikát, törölje a fájlt a levelek mappából, és a tároló azonosítója.
 * Törölje a tagság NAB tag keresztezése nem működik.
 * Többértékű atribut sikeresen törölni kell értékek

## <a name="111170"></a>1.1.117.0
Kiadás dátuma: 2016. március

**Új összekötő**  
Az első kiadása a [általános SQL-összekötő](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).

**Új funkciók:**

* Általános LDAP-összekötő:
  * Különbözeti importálás az Isode támogatása.
* Webszolgáltatások összekötőjének:
  * Frissítve a csEntryChangeResult és, hogy az objektum szintű hibák a szinkronizálási motor vissza a visszaadandó setImportErrorCode tevékenység.
  * Frissítve az új objektum szintű hiba funkciók használatához a SAP6 és SAP6User sablonokat.
* Lotus Domino-összekötő:
  * Exportálási szüksége lesz egy certifier címjegyzék száma. Mostantól használhatja ugyanazt a jelszót az összes képesítést adók engedélyezése a felügyelet megkönnyítése érdekében.

**Rögzített problémák:**

* Általános LDAP-összekötő:
  * Az IBM Tivoli DS néhány referenciaattribútumokon nem észlelt megfelelően.
  * Nyissa meg LDAP során egy különbözeti importálást nem állhat kizárólag szóközökből elején és végén karakterláncok csonkolva lett.
  * Novell és NetIQ az exportálást, objektum áthelyezése, szervezeti egységek és tárolók között, és a egy időben átnevezése nem sikerült az objektum.
* Webszolgáltatások összekötőjének:
  * Ha a webszolgáltatás több végpontok esetében ugyanezt a kötést, majd az összekötő nem megfelelően találta ezeket a végpontok.
* Lotus Domino-összekötő:
  * A fullName attribútum egy e-mail-adatbázisban való exportálása nem működött.
  * Az exportálási is hozzáadja, és a tag eltávolítása a csoportból csak a hozzáadott tagok exportált.
  * Ha a megjegyzések dokumentum érvénytelen (az attribútum isValid hamis), akkor az összekötő nem tud.

## <a name="older-releases"></a>Korábbi kiadások
2016. március előtti az összekötők, támogatási témakörök jelentek meg.

**Általános LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, 2015 szeptember
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, 2015. március
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, 2015 január
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, 2014. szeptember
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, 2014. március

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, 2014. szeptember

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, 2014. szeptember

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, 2015 szeptember
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, 2015. március
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, 2014. augusztus
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, 2014. február  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, 2013. október
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, 2013. augusztus

## <a name="troubleshooting"></a>Hibaelhárítás 

> [!NOTE]
> Amikor frissíti a Microsoft Identity Manager vagy az aad Connect ECMA2 csatlakozók bármelyike használ. 

Az összekötő definíciójának megfelelően frissítése esetén frissítenie kell vagy jelentéséhez azonosító 6947 figyelmeztetés alkalmazás Eseménynapló elindítása a következő hibaüzenetet kap: "a verze Sestavení az AAD-összekötő konfigurációja ("X.X.XXX. "X") már korábban, mint a tényleges verzió ("X.X.XXX. "X") a "C:\Program Files\Microsoft Azure ad-ben Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".

A definíció frissítése:
* Az összekötő-példány tulajdonságainak megnyitása
* Kattintson a kapcsolat / lapon csatlakozni
  * -Összekötő fiókjaként adja meg a jelszót
* Ezután kattintson az egyes a tulajdonságlapokról
  * A partíciók lapon az összekötő típusú-e a frissítés gomb, kattintson a frissítés gombra a lap
* Után minden tulajdonságlapokról elért, kattintson az OK gombra a módosítások mentéséhez.


## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](active-directory-aadconnectsync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
