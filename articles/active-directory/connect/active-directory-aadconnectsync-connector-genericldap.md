---
title: "Általános LDAP-összekötő |} Microsoft Docs"
description: "Ez a cikk ismerteti a Microsoft általános LDAP-összekötő konfigurálása."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 984beeb0-4d91-4908-ad81-c19797c4891b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: adb174526bc377f484be5fb0a71b28e8daaaa6fd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="generic-ldap-connector-technical-reference"></a>Általános LDAP-összekötő műszaki útmutatója
Ez a cikk ismerteti az általános LDAP-összekötő. A cikk vonatkozik a következő termékek:

* A Microsoft Identity Manager 2016 (MIM2016)
* A Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Kell használnia a 4.1.3671.0 gyorsjavítás vagy újabb [KB3092178](https://support.microsoft.com/kb/3092178).

MIM2016 és FIM2010R2, az összekötő rendelkezésre áll egy letölthető a [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

Kontextusban való megnevezésekor IETF RFC-dokumentumokat, ez a dokumentum a formátumot használja (RFC [RFC szám] / [section RFC dokumentumban]), például (RFC 4512/4.3).
A http://tools.ietf.org/html/rfc4500 (kell 4500 cserélje le a megfelelő RFC-azonosító) talál további információkat.

## <a name="overview-of-the-generic-ldap-connector"></a>Az általános LDAP-összekötő áttekintése
Az általános LDAP-összekötő lehetővé teszi a szinkronizálási szolgáltatás integrálása egy LDAP v3-kiszolgálóval.

Az IETF RFC-dokumentumokat bizonyos műveleteket és a séma elemei, például a különbözeti importálás végrehajtásához szükséges nincs megadva. Ezeket a műveleteket csak explicit módon megadott LDAP-címtárak esetén támogatottak.

A következő szolgáltatásokat a magas szintű szempontjából, a jelenlegi kiadásban az összekötő támogatja:

| Szolgáltatás | Támogatás |
| --- | --- |
| Csatlakoztatott adatforrás |Az összekötő minden LDAP v3-as kiszolgáló (RFC 4510 szabványnak megfelelő) használata támogatott. A következő tesztelték: <li>Microsoft Active Directory Lightweight Directory-szolgáltatások (AD LDS)</li><li>Microsoft Active Directory globális katalógus (GC-AD)</li><li>A címtárkiszolgáló 389</li><li>Apache címtárkiszolgálóra</li><li>IBM Tivoli DS</li><li>Isode könyvtár</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Nyissa meg DJ</li><li>Nyissa meg DS</li><li>Nyissa meg az LDAP (openldap.org)</li><li>Oracle (korábban Sun) Directory Server Enterprise Edition</li><li>RadiantOne virtuális könyvtár Server (VDS)</li><li>Egy Sun Directory Server</li>**Nem támogatott a figyelmet a jelentősebb könyvtárak:** <li>Microsoft Active Directory tartományi szolgáltatások (AD DS) [a beépített Active Directory-összekötőt használja helyette]</li><li>Oracle Internet könyvtár (OID)</li> |
| Forgatókönyvek |<li>Objektum életciklusának kezelésére</li><li>Csoportok kezelése</li><li>Jelszókezelés</li> |
| Műveletek |A következő műveletek támogatottak minden LDAP-könyvtárak: <li>Teljes importálás</li><li>Exportálás</li>A következő műveletek csak a meghatározott könyvtárakban támogatottak:<li>Különbözeti importálás</li><li>Jelszó beállítása, a jelszó módosítása</li> |
| Séma |<li>Az LDAP-séma (RFC3673 és RFC4512/4.2) séma észlelésekor</li><li>Támogatja a szerkezeti osztályok, aux osztályok és extensibleObject objektumosztály (RFC4512/4.3)</li> |

### <a name="delta-import-and-password-management-support"></a>Különbözeti importálás és a jelszó támogatása
Különbözeti importálás és a jelszófelügyeletet támogatott könyvtárak:

* Microsoft Active Directory Lightweight Directory-szolgáltatások (AD LDS)
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a jelszó beállítása
* Microsoft Active Directory globális katalógus (GC-AD)
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a jelszó beállítása
* A címtárkiszolgáló 389
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Apache címtárkiszolgálóra
  * Nem támogatott a különbözeti importálás a könyvtár nem tartozik egy állandó Változásnapló
  * Támogatja a jelszó beállítása
* IBM Tivoli DS
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Isode könyvtár
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Novell eDirectory és NetIQ eDirectory
  * Különbözeti importálás hozzáadása, frissítése és átnevezési műveleteket
  * Nem támogatja a Delete művelet különbözeti importálás
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Nyissa meg DJ
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Nyissa meg DS
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Nyissa meg az LDAP (openldap.org)
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a jelszó beállítása
  * Nem támogatja a jelszó módosítása
* Oracle (korábban Sun) Directory Server Enterprise Edition
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* RadiantOne virtuális könyvtár Server (VDS)
  * 7.1.1 verzióját kell használnia vagy újabb
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása
* Egy Sun Directory Server
  * Különbözeti importálás összes műveletet támogatja
  * Támogatja a állítsa be a jelszót és a jelszó módosítása

### <a name="prerequisites"></a>Előfeltételek
Az összekötő használatához győződjön meg arról, hogy a szinkronizálási kiszolgálón a következő:

* Microsoft keretrendszer 4.5.2-es vagy újabb verzió

### <a name="detecting-the-ldap-server"></a>Az LDAP-kiszolgáló észlelése
Az összekötő ismeri fel és azonosítja az LDAP-kiszolgáló különböző módszerekkel alapul. Az összekötőt használja, a legfelső szintű DSE, a gyártó neve/verziója, és azt ellenőrzi, hogy a séma egyedi objektumok és attribútumok közismert, hogy az egyes LDAP-kiszolgálókon található. Ezeket az adatokat, ha található, az összekötő a konfigurációs beállítások előre feltöltésére használt eszköz.

### <a name="connected-data-source-permissions"></a>Csatlakoztatott adatforrás-engedélyek
Importálás végrehajtása, és exportálja a műveletek olyan objektumokon, a csatlakoztatott könyvtárban, az összekötő fiókjaként megfelelő engedélyekkel kell rendelkeznie. Az összekötő írási engedéllyel kell exportálni, és olvasási engedéllyel képes importálni kell. Engedély konfigurációs a felhasználói élmény mellett a célkönyvtárat, maga a belül történik.

### <a name="ports-and-protocols"></a>Portok és protokollok
Az összekötő a konfigurációban, amely alapértelmezés szerint 389 LDAP, és a LDAPS 636 portszámot használja.

LDAPS SSL 3.0 és TLS kell használnia. Az SSL 2.0 nem támogatott, és nem lehet aktiválni.

### <a name="required-controls-and-features"></a>Szükséges vezérlők és a szolgáltatások
A következő LDAP vezérlők/szolgáltatások az LDAP-kiszolgáló, az összekötő helyes működéséhez rendelkezésre kell állnia:  
`1.3.6.1.4.1.4203.1.5.3`Igaz/hamis szűrők

A True/False szűrő gyakran jelentése nem támogatja az LDAP-könyvtárak, előfordulhat, hogy az a **globális lap** alatt **kötelező funkciók nem található**. Létrehozásához használt **vagy** szűrők az LDAP-lekérdezések, például amikor több Objektumtípusok importálása. Ha egynél több objektumtípus importálásához az LDAP-kiszolgáló támogatja ezt a szolgáltatást.

Ha egy könyvtárat a horgony esetén az egyedi azonosító a következő is elérhetőnek kell lennie (további információkért lásd: a [horgonyok konfigurálása](#configure-anchors) szakaszt):  
`1.3.6.1.4.1.4203.1.5.1`Az összes műveleti attribútumok

Ha a könyvtár több objektum mi is elfér egy hívás a könyvtárba, majd javasoljuk, hogy lapozás használata. Lapozófájl működjön, szükséges a következő lehetőségek közül:

**1. lehetőség:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**2. lehetőség:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Ha az összekötő-konfiguráció engedélyezve van a mindkét lehetőség használata, pagedResultsControl szolgál.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl csak a törölt objektumok láthatók a USNChanged különbözeti importálás módszer segítségével.

Az összekötő megkísérli észlelni a beállításokat a kiszolgálón található. A beállítások nem észlelhető, ha a figyelmeztetés az összekötő tulajdonságainak a globális lapon jelen. Nem minden LDAP-kiszolgálókba található összes vezérlők/szolgáltatások támogatják, és akkor is, ha ezt a figyelmeztetést meg adva, a connector működik-e hibák nélkül.

### <a name="delta-import"></a>Különbözeti importálás
Különbözeti importálás csak akkor használható, ha egy támogatási könyvtárat már telepítve van. Az alábbi módszerek jelenleg használatban van:

* LDAP Accesslog. Lásd: [http://www.openldap.org/doc/admin24/overlays.html#Access naplózás](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
* LDAP változásnaplója. Lásd: [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
* Timestamp típusú. Az összekötő Novell/NetIQ eDirectory utolsó időpontját használja megszerezni a létrehozott és a frissített objektumokat. Novell/NetIQ eDirectory nem biztosít egy azzal egyenértékű azt jelenti, hogy a törölt objektumok beolvasása. Ezt a lehetőséget is használható, ha nincs más különbözeti importálás módszer aktív, az LDAP-kiszolgálón. Ez a beállítás nincs hogy objektum törölt importálása.
* USNChanged. Lásd: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Nem támogatott
A következő LDAP-funkciók nem támogatottak:

* LDAP-átirányítások (RFC 4511/4.1.10) kiszolgálók között

## <a name="create-a-new-connector"></a>Új összekötő létrehozása
Egy általános LDAP-összekötő létrehozásához az **szinkronizálási szolgáltatás** válasszon **kezelőügynök** és **létrehozása**. Válassza ki a **általános LDAP (Microsoft)** összekötő.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Kapcsolatok
A kapcsolat lapon meg kell adnia a Host, Port és kötés. Attól függően, amelyek kötés kijelölt, további információt a következő szakaszokban előfordulhat, hogy adni.

![Kapcsolatok](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

* A kapcsolódási időkorlát beállítást csak akkor használja az első csatlakozni a kiszolgálóhoz, ha a séma észlelésére.
* Ha a kötés névtelen, majd sem felhasználónév / jelszó és a tanúsítvány nem használható.
* Más kötésekben, írja be adatokat vagy a felhasználónév / jelszó, vagy válasszon egy tanúsítványt.
* Ha használ Kerberos hitelesítéshez, majd adni a tartomány/tartomány felhasználó.

A **aliasok attribútum** szövegmező RFC4522 szintaxissal a sémában meghatározott attribútumok szolgál. Ezek az attribútumok séma észlelési nem észlelhető, és az összekötő kell azok alapján. Például a következő kell beírni a attribútum aliasok, ha azt szeretné, hogy helyesen azonosítani a userCertificate attribútum bináris attribútumként:

`userCertificate;binary`

A következő egy példa a hogyan Ez a konfiguráció nézhet:

![Kapcsolatok](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Válassza ki a **működési attribútumait tartalmazza a sémában** jelölőnégyzetet is az a kiszolgáló által létrehozott attribútumait tartalmazza. Ezek közé tartozik például amikor az objektum lett létrehozva, és a legutóbbi frissítésének ideje attribútumok.

Válassza ki **bővíthető attribútumait tartalmazza a sémában** Ha bővíthető objektumok (RFC4512/4.3) használja, és ez a beállítás lehetővé teszi, hogy a használni kívánt összes objektum minden attribútum. Ezzel a beállítással lehetővé teszi a séma túl nagy, kivéve, ha a csatlakoztatott címtárhoz van ezzel a szolgáltatással javasolt az adatok megőrzése a beállítást.

### <a name="global-parameters"></a>Globális paraméterek
Globális paraméterek lap különbözeti Változásnapló és további LDAP funkciók DN konfigurálhatja. A lap az LDAP-kiszolgáló által előre megadott jelenti.

![Kapcsolatok](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

A felső részében a kiszolgálójának nevével, például a nevét, a kiszolgáló által biztosított információit tartalmazza. Az összekötő is ellenőrzi, hogy a kötelező szabályozza a legfelső szintű DSE szerepel. Ezek az intézkedések nem szerepelnek a listán, ha figyelmeztetés jelenik meg. Egy LDAP-könyvtárak nem szerepelhet a legfelső szintű DSE az összes szolgáltatás, és előfordulhat, hogy a Connector működik hibák nélkül akkor is, ha jelen egy figyelmeztetés.

A **vezérlők támogatott** replikációból bizonyos műveletek működését:

* A kiválasztott fa törlése, a hierarchia törlődik egy LDAP-hívással. A fa törlése nincs bejelölve az összekötő rekurzív törlési funkciója, szükség esetén.
* A kiválasztott lapozható eredmények az összekötő nem lapozható importálása a futtatási lépéseket a megadott méret.
* A VLVControl SortControl pedig adatokat olvasni az LDAP-címtár pagedResultsControl helyett.
* Ha mind a három beállítást (pagedResultsControl, VLVControl és SortControl) nincsenek bejelölve, az összekötő importálja az összes objektum meghiúsulhat, ha nagy könyvtár egy műveletben.
* ShowDeletedControl csak akkor használja, ha a különbözeti importálás módszer USNChanged.

A módosítási napló DN például használják a különbözeti Változásnapló névhasználati környezet **cn = változásnaplója**. Különbözeti importálás elvégzéséhez meg kell adni ezt az értéket.

A következő egy alapértelmezett Változásnapló DNs listáját:

| Címtár | Különbözeti Változásnapló |
| --- | --- |
| Microsoft AD LDS és AD globális Katalógus |Automatikusan észleli. USNChanged. |
| Apache címtárkiszolgálóra |Nem érhető el. |
| Directory 389 |Változásnapló. Alapértelmezett érték használata: **cn = változásnaplója** |
| IBM Tivoli DS |Változásnapló. Alapértelmezett érték használata: **cn = változásnaplója** |
| Isode könyvtár |Változásnapló. Alapértelmezett érték használata: **cn = változásnaplója** |
| Novell/NetIQ eDirectory |Nem érhető el. Timestamp típusú. Az összekötő által használt utolsó frissítés dátum/idő beolvasandó hozzáadja, és rögzíti. |
| Nyissa meg a DJ/DS-ben |Változásnapló.  Alapértelmezett érték használata: **cn = változásnaplója** |
| Nyissa meg az LDAP |Hozzáférési napló. Alapértelmezett érték használata: **cn = accesslog** |
| Oracle DSEE |Változásnapló. Alapértelmezett érték használata: **cn = változásnaplója** |
| A virtuális Lemezszolgáltatás RadiantOne |Virtuális könyvtár. A VDS csatlakozik directory függ. |
| Egy Sun Directory Server |Változásnapló. Alapértelmezett érték használata: **cn = változásnaplója** |

A password attribútum az összekötő segítségével állítsa be a jelszavát a jelszó módosítása a nevét, és jelszót kell beállítani, műveletek.
Ez az érték az alapértelmezett értéke **userPassword** , de egy adott LDAP-rendszer szükség esetén módosítható.

A további partíciókat listában is lehet további névterek automatikusan nem észlelt. Ez a beállítás például használható, ha több kiszolgáló logikai fürt, amely az összes importálni kell egy időben. Active Directory egy erdő több tartományban is vannak, de minden olyan tartománynál megosztani egy séma, mint az azonos is lehet szimulált ebben a mezőben írja be a további névteret. Minden névtér különböző kiszolgálókról származó importálhatja, és további van-e konfigurálva a konfigurálása partíciók és hierarchiák lapon. Ctrl + Enter ezen új sor.

### <a name="configure-provisioning-hierarchy"></a>Kiépítési hierarchia konfigurálása
Ezen a lapon a megkülönböztető név összetevő, például OU hozzárendelése az objektumtípus, amely kell megadva lennie, például organizationalUnit szolgál.

![Telepítési hierarchia](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Kiépítési hierarchia konfigurálása, beállíthatja az összekötő egy struktúra, szükség esetén automatikusan létrehozásához. Például, ha egy névtér dc = contoso, dc = com és egy új objektum cn Joe, ou = = budapesti, c = US, dc = contoso, dc = com ki van építve, akkor az összekötő objektum típusa ország az USA és egy organizationalUnit Budapest számára hozhat létre, ha azok még nem érhetők el a címtárban.

### <a name="configure-partitions-and-hierarchies"></a>Partíciók és hierarchiák konfigurálása
A partíciók és hierarchiák lapon válassza ki az összes névtér objektumok importálni és exportálni szeretné.

![Partíciók](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Minden névtér esetében akkor is, amely akkor bírálja felül a kapcsolat képernyőn megadott értékek kapcsolat beállításainak konfigurálása. Ha ezeket az értékeket alapértelmezett értékét üresen marad, a a kapcsolat képernyőről adatok.

Úgy is is válassza ki, melyik tárolók és szervezeti egységek, az összekötő kell importálhat és exportálhat.

Keresés végrehajtása során ez a partíció az összes tároló keresztül történik. Azokban az esetekben, ahol nagyszámú tárolók e működés teljesítménycsökkenést.

>[!NOTE]
Az általános LDAP 2017. márciusi frissítés indítása összekötő keresések lehet korlátozni hatókörrel, hogy csak a kijelölt tárolók. Ezt megteheti "Keresés csak a kijelölt tárolók" jelölőnégyzet bejelölésével, az alábbi ábrán látható módon.

![Keresés csak a kijelölt tároló](./media/active-directory-aadconnectsync-connector-genericldap/partitions-only-selected-containers.png)

### <a name="configure-anchors"></a>Horgonyok konfigurálása
Ezen a lapon mindig rendelkezik előre beállított értékkel, és nem módosítható. Ha a kiszolgáló szállító azonosítása, majd a horgony előfordulhat, hogy lehet megadni, nem módosítható attribútumot, például egy objektum a GUID. Ha nem észlelhető, vagy ismert, hogy nem rendelkezik az attribútum nem módosítható, majd az összekötő használ kapcsolatainak megkülönböztető név (megkülönböztető neve).

![a központi jellegűek](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)


A következő egy LDAP-kiszolgálókba és a horgony használja listáját:

| Címtár | Horgonyattribútum |
| --- | --- |
| Microsoft AD LDS és AD globális Katalógus |objectGUID |
| A címtárkiszolgáló 389 |megkülönböztető név |
| Apache könyvtár |megkülönböztető név |
| IBM Tivoli DS |megkülönböztető név |
| Isode könyvtár |megkülönböztető név |
| Novell/NetIQ eDirectory |GUID |
| Nyissa meg a DJ/DS-ben |megkülönböztető név |
| Nyissa meg az LDAP |megkülönböztető név |
| Oracle ODSEE |megkülönböztető név |
| A virtuális Lemezszolgáltatás RadiantOne |megkülönböztető név |
| Egy Sun Directory Server |megkülönböztető név |

## <a name="other-notes"></a>Egyéb megjegyzések
Ez a szakasz szempontok az összekötő jellemző vagy más okból fontos tudni, hogy információkat nyújt.

### <a name="delta-import"></a>Különbözeti importálás
Az LDAP-kiszolgálón nyissa meg a különbözeti vízjel UTC dátum/idő. Emiatt a FIM szinkronizálási szolgáltatás és a nyitott LDAP között kell szinkronizálni. Ha nem, előfordulhat, hogy lehet hagyni a különbözeti Változásnapló néhány elemet.

Novell eDirectory, az a különbözeti importálás nem érzékeli bármely objektum törlése. Ezért fontos rendszeresen az összes törölt objektumok kereséséhez teljes importálást futtatásához.

A dátum és idő alapján a különbözeti módosítási napló-címtárak esetén ajánlott rendszeres időközönként teljes importálást futtatásához. Ez a folyamat lehetővé teszi, hogy a szinkronizálási motor kereséséhez és az LDAP-kiszolgáló és a jelenleg a a kapcsolódási térbe között eltérő.

## <a name="troubleshooting"></a>Hibaelhárítás
* Az összekötő hibaelhárítása naplózásának engedélyezése a további információkért lásd: a [hogyan ETW-nyomkövetés engedélyezése a csatlakozók](http://go.microsoft.com/fwlink/?LinkId=335731).
