---
title: "Összekötő Verziókiadások |} Microsoft Docs"
description: "Ez a témakör az összekötők összes kiadásaiban a Forefront Identity Manager (FIM) és a Microsoft Identity Manager (MIM)"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: billmath
ms.openlocfilehash: 5b43284a86a7e5d4cdbf50a29d73f970c9ad9d58
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="connector-version-release-history"></a>Összekötő verziókiadásai
Az összekötők a Forefront Identity Manager (FIM) és a Microsoft Identity Manager (MIM) gyakran frissül.

> [!NOTE]
> Ez a témakör csak a FIM és a MIM rendszer. Az összekötők nem támogatottak az Azure AD Connect telepítése. Kiadott összekötők frissítése a megadott Build esetén az AADConnect előtelepített.


Ez a témakör a csatlakozók kiadott összes verziójának felsorolása.

Kapcsolódó hivatkozások:

* [Töltse le a legfrissebb összekötők](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Általános LDAP-összekötő](active-directory-aadconnectsync-connector-genericldap.md) dokumentáció
* [Általános SQL-összekötő](active-directory-aadconnectsync-connector-genericsql.md) dokumentáció
* [Webalkalmazás-Services-összekötő](http://go.microsoft.com/fwlink/?LinkID=226245) dokumentáció
* [PowerShell-összekötő](active-directory-aadconnectsync-connector-powershell.md) dokumentáció
* [Lotus Domino-összekötő](active-directory-aadconnectsync-connector-domino.md) dokumentáció

## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Javított problémák:

* Lotus Notes:
  * Egyéni képesítést adók engedélyezése beállítás szűrése
  * Az osztály ImportOperations importálási rögzített a meghatározását, hogy milyen műveleteket futtathatja a "Nézetek" módban, és amely a "Search" módban.
* Általános LDAP:
  * OpenLDAP Directory DN entryUUI, hanem horgonyzási használja. Új lehetőség GLDAP összekötőt, amely lehetővé teszi a horgony módosítása
* Generic SQL:
  * Rögzített exportálási mezőbe, amely a varbinary(max) típusú.
  * Amikor hozzá a bináris adatok adatforrásból származó CSEntry objektum, a DataTypeConversion függvény nulla bájt sikertelen volt. Rögzített DataTypeConversion függvény CSEntryOperationBase osztály.




### <a name="enhancements"></a>Fejlesztései:

* Generic SQL:
  * Az ügyfélgépek konfigurálására a végrehajtási mód tárolt eljárást az elnevezett paramétereket, vagy neve nem szerepel-e az általános SQL felügyeleti ügynök a lap "Globális paraméterek" konfigurációs ablakban. A lap "Globális paraméterek" nincs "Use elnevezett paramétereket a tárolt eljárás végrehajtásához" hajtsa végre a tárolt eljárás mód felelős címkével jelölőnégyzetet neve paraméterek vagy nem.
    * Az elnevezett paraméterek tárolt eljárás végrehajtásához képességét jelenleg csak olyan adatbázisok IBM DB2 és MSSQL működik. Az Oracle és a MySQL-adatbázisok Ez a módszer nem működik: 
      * Az SQL-szintaxis MySQL az elnevezett paraméterek nem támogatja a tárolt eljárások.
      * Az Oracle ODBC-illesztőprogram nem támogatja az elnevezett paraméterek az elnevezett paramétereket a tárolt eljárások)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Javított problémák:

* Általános webszolgáltatások:
  * Megtörtént egy probléma javítása meggátolja, hogy a SOAP-projekt jöjjenek létre, amikor két vagy több végpontot történt.
* Generic SQL:
  * Importálási műveletben a GSQL lett nem időpont váltásakor megfelelően, a kapcsolódási térbe mentésekor. A GSQL a kapcsolódási térbe alapértelmezett dátum és idő formátuma "éééé-HH-NN HH:mm:ssZ" értékről "éééé-hh-nn hh:mm:ssZ" értékűre változott.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Javított problémák:

* Általános webszolgáltatások:
  * A Wsconfig eszköz nem megfelelő átalakítani a Json-tömb, a "kérelemmintát" a többi metódust. A Json-tömb, a többi kéréshez ennek oka a szerializálás problémákat.
  * Web Service Connector konfigurációs eszköz nem támogatja a hely szimbólumok használata a JSON-attribútum neve 
    * A behelyettesítések is manuálisan hozzáadni az WSConfigTool.exe.config fájlra, pl.```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes:
  * Ha a beállítás **lehetővé teszik egyéni képesítést adók engedélyezése a szervezet vagy szervezeti egység** le van tiltva, akkor az összekötő nem tud exportálásakor (frissítés), miután az Exportálás flow attribútumainak Domino exportálják, de exportálás időpontjában egy KeyNotFoundException szinkronizálási visszakerül. 
    * Ez akkor fordul elő, mert az átnevezési sikertelen lesz, amikor megpróbálja módosítani a megkülönböztető név (felhasználónév attribútum) módosításával az alábbi attribútumok egyikét:  
      - Vezetéknév
      - Utónév
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - szervezeti egység
      - altcommonname

  * Ha **lehetővé teszik egyéni képesítést adók engedélyezése a szervezet vagy szervezeti egység** engedélyezve van, de szükséges képesítést adók engedélyezése továbbra is üres, akkor KeyNotFoundException következik be.

### <a name="enhancements"></a>Fejlesztései:

* Generic SQL:
  * **Forgatókönyv: újratervezett megvalósítva:** "*" funkció
  * **Megoldás leírása:** megközelítés megváltozott [többértékű hivatkozási attribútum kezelési](active-directory-aadconnectsync-connector-genericsql.md).


### <a name="fixed-issues"></a>Javított problémák:

* Általános webszolgáltatások:
  * Kiszolgálókonfiguráció nem importálható, ha az megtalálható a WebService összekötő
  * Több webkiszolgáló szolgáltatással nem működik a WebService összekötő

* Generic SQL:
  * Egyetlen értéket hivatkozott attribútum nem objektumtípusok találhatók
  * Különbözeti importálás objektumon változások követése stratégia törlések érték többértékű táblából eltávolításakor
  * Az AS a DB2 GSQL Connector OverflowException / 400

Lotus:
  * Keresés a szervezeti egységek GlobalParameters lap megnyitása előtt enable\disable hozzáadott lehetőséget

## <a name="114430"></a>1.1.443.0

Kiadás dátuma: 2017. március

### <a name="enhancements"></a>Fejlesztések

* Generic SQL:</br>
  **A forgatókönyvben a jelenség:** az SQL-összekötővel, ha azt csak teszi lehetővé egy hivatkozás egy objektumtípus és tagokat kereszthivatkozás szükséges egy jól ismert korlátozás. </br>
  **Megoldás leírása:** a feldolgozási lépést hivatkozásainak voltak "*" lehetőséget választja, minden kombinációi objektumtípusok visszatér vissza a szinkronizálási motor.

>[!Important]
- Ezzel létrehoz sok helyőrzők
- Ellenőrizze, hogy a kiosztási egyedi közötti objektumtípusok szükség.


* Általános LDAP:</br>
 **Forgatókönyv:** Ha csak néhány tárolók adott partíció van kijelölve, majd a keresés továbbra is megtörténik a teljes partíció. Specifikus szűri a Synchronization szolgáltatás által, de nem MA, ami problémát okozhat teljesítménycsökkenést. </br>

 **Megoldás leírása:** megváltozott GLDAP összekötő kód révén lehetséges nyissa meg az összes tároló keresztül, és azok helyett a teljes partíció kereséssel objektumok kereséséhez.


* Lotus Domino:

  **Forgatókönyv:** Domino mail törlés támogatása egy személy eltávolítása az exportálás során. </br>
  **Megoldás:** konfigurálható mail törlés támogatása egy személy eltávolítása az exportálás során.

### <a name="fixed-issues"></a>Javított problémák:
* Általános webszolgáltatások:
 * A szolgáltatás URL-CÍMÉT az alapértelmezett módosításakor a SAP wsconfig WebService konfigurációs eszköz projekt, akkor történik, a következő hiba: nem található az elérési út egy része

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Általános LDAP:
 * GLDAP Connector nem látja az AD LDS összes attribútum
 * Varázsló oldaltörések nem UPN attribútum a LDAP-címtár séma észlelésekor
 * Különbözeti importálása sikertelen teljes importálás során, ha nincs bejelölve a "objectclass" attribútum nem található felderítési hibákkal
 * Egy "Partíciók és hierarchiák konfigurálása" konfigurációs lapján nem jeleníti meg minden objektumokat típust az általános új kiszolgálók partíció értéke  
LDAP MA. Ezek kimutatták RootDSE partíció csak objektumokat.


* Generic SQL:
 * Javítsa ki általános SQL vízjel különbözeti importálás többértékű attribútum nem importált hiba
 * Többértékű attribútum deleted\added értékek exportálásakor nincsenek deleted\added adatforrás.  


* Lotus Notes:
 * Egy adott mező "Teljes neve" jelenik meg a metaverzumba megfelelően azonban a megjegyzéseket az exportáláskor attribútum értéke Null vagy üres.
 * Hárítsa el az ismétlődő Certifier hiba
 * Kiválasztásakor az adatok nélkül a más objektumokkal a Lotus Domino-összekötő majd azt hibaüzenet jelenik meg a felderítés teljes-importálás végrehajtása közben.
 * Különbözeti importálás esetén éppen futó a Lotus Domino-összekötő rendszert futtató végén a Microsoft.IdentityManagement.MA.LotusDomino.Service.exe szolgáltatás néha értéket ad vissza egy alkalmazás hibát.
 * A csoporttagságot általános szolgáltatás megfelelően működik, és megmarad, kivéve az Exportálás próbálja meg eltávolítani a felhasználó a tagság futtatásakor frissítéssel sikeres jeleníti meg, de a felhasználó nem lesz beolvasása eltávolítva Lotus Notes tagjának kell lennie.
 * "Append elem alján" hozzá lett adva, a konfiguráció grafikus felhasználói Felülettel a Lotus MA új cikkek alján hozzáfűzendő többértékű attribútumok az exportálás során kiválaszthatja exportálás mód lehetőséget.
 * Összekötő adja hozzá a szükséges logikát, törölje a fájlt az E-mail mappa és azonosító tároló.
 * Törölje a tagság közötti NAB tag nem működik.
 * Értékek sikeresen törlődjenek többértékű attribútum

## <a name="111170"></a>1.1.117.0
Kiadás dátuma: 2016. március

**Új összekötő**  
A kezdeti kiadása a [általános SQL-összekötő](active-directory-aadconnectsync-connector-genericsql.md).

**Új szolgáltatások:**

* Általános LDAP-összekötőhöz:
  * Különbözeti importálás Isode való támogatása.
* Web Services-összekötővel:
  * Frissítve a csEntryChangeResult és setImportErrorCode tevékenység objektum szintű hibák a szinkronizálási motor vissza a visszaadandó engedélyezéséhez.
  * Az új objektum szint hiba funkciójával SAP6 és SAP6User sablonok frissítése.
* Lotus Domino-összekötő:
  * Exportálás van szüksége egy certifier címjegyzék száma. Használhatja ugyanazt a jelszót, az összes képesítést adók engedélyezése a felügyelet megkönnyítése.

**Javított problémák:**

* Általános LDAP-összekötőhöz:
  * Az IBM Tivoli DS, néhány hivatkozási attribútum a rendszer nem észlelt megfelelően.
  * Nyissa meg az LDAP a különbözeti importálás során, a rendszer csonkolt elején és végén karakterláncok szóközöket.
  * Novell és NetIQ exportálás, objektum áthelyezése, szervezeti egységek/tárolók között, és egyszerre átnevezni, az objektum sikertelen.
* Web Services-összekötővel:
  * Ha a webszolgáltatás ugyanezt a kötést a több végpontok közé, majd az összekötő nem megfelelően találta ezeket a végpontokat.
* Lotus Domino-összekötő:
  * A fullName attribútum egy e-mail-adatbázisban való exportálás sikertelen volt.
  * Amely is felvétele, illetve tag eltávolítása egy csoportból exportálás csak a hozzáadott tagok exportált.
  * Ha egy megjegyzések dokumentum érvénytelen (az attribútum isValid értéke hamis), akkor az összekötő nem tud.

## <a name="older-releases"></a>Régebbi kiadásai
2016. március, mielőtt az összekötők kiadott támogatási témakörök szerint.

**Általános LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, 2015. szeptember
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, 2015. március
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, 2015. január
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, 2014. szeptember
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, 2014 March

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, 2014. szeptember

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, 2014. szeptember

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, 2015. szeptember
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, 2015. március
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, 2014. augusztus
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, 2014. február  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, 2013. október
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, 2013. augusztus

## <a name="troubleshooting"></a>Hibaelhárítás 

> [!NOTE]
> Amikor frissíti a Microsoft Identity Manager vagy az AADConnect ECMA2 összekötők használatával. 

Az összekötő-definíció felel meg a frissítés után frissítenie kell vagy a következő hiba jelentését figyelmeztetés azonosítója 6947 alkalmazás Eseménynapló elindítása kap: "szerelvény verziója AAD-összekötő konfigurációjában ("X.X.XXX. "X") korábbi, mint a tényleges verzió ("X.X.XXX. "X") a "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".

A definíció frissítése:
* Az összekötő-példány tulajdonságainak megnyitása
* Kattintson a kapcsolat / lapon kapcsolódni
  * Az összekötő fiókjaként adja meg a jelszót
* Ezután kattintson az egyes tulajdonság lapokon
  * Ha az összekötő a partíciók lapon van a frissítés gombra, és kattintson a lapnak a frissítés gombra
* Az összes tulajdonság lap elérése után kattintson az OK gombra a módosítások mentéséhez.


## <a name="next-steps"></a>További lépések
További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
