---
title: Engedélyezés – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: megoldások a fenyegetések között szerepelnek a Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: eec628a5084dc663978e16e617192802d3ecfcfa
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307656"
---
# <a name="security-frame-authorization--mitigations"></a>Biztonsági keret: Engedélyezési |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy a megfelelő ACL-eket vannak konfigurálva a jogosulatlan hozzáférés korlátozása az eszközön tárolt adatok](#acl-restricted-access)</li><li>[Győződjön meg arról, hogy az érzékeny felhasználó-specifikus alkalmazás tartalmát a rendszer felhasználói profil könyvtárban tárolja](#sensitive-directory)</li><li>[Győződjön meg arról, hogy a telepített alkalmazások a legkevesebb jogosultsággal futnak](#deployed-privileges)</li></ul> |
| **Webalkalmazás** | <ul><li>[Egymást követő lépést ahhoz, ha üzleti logikai folyamatok feldolgozása vonatkozóan](#sequential-logic)</li><li>[Mechanizmus enumerálás megelőzése érdekében korlátozza arány megvalósítása](#rate-enumeration)</li><li>[Győződjön meg arról, hogy megfelelő engedély van beállítva, és a legalacsonyabb jogosultságok elvét az azt követő](#principle-least-privilege)</li><li>[Üzleti logika és a resource access felhasználását engedélyezési döntésekhez nem kell a bejövő kérelem paraméterei alapján](#logic-request-parameters)</li><li>[Győződjön meg arról, hogy a tartalom és erőforrások nem enumerálható vagy elérhetők közötti választásra tallózása](#enumerable-browsing)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy alacsonyabb szintű fiókok használják-e csatlakozni az adatbázis-kiszolgáló](#privileged-server)</li><li>[Sor szintű biztonsági RLS megakadályozhatja, hogy a bérlők egymás adataihoz hozzáférjenek megvalósítása](#rls-tenants)</li><li>[SysAdmin (rendszergazda) szerepkörrel kell rendelkeznie az érvényes szükséges felhasználók](#sysadmin-users)</li></ul> |
| **IoT átjáró** | <ul><li>[Alacsonyabb szintű jogkivonatok használatával Felhőbeli átjárón](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Egy csak küldési engedélyeket SAS-kulcsot használ a tokenek létrehozásához](#sendonly-sas)</li><li>[Ne használjon, amely közvetlen hozzáférést biztosíthat az Eseményközpont hozzáférési jogkivonatok](#access-tokens-hub)</li><li>[Csatlakozás az Eseményközponthoz a minimálisan szükséges jogosultsággal rendelkező SAS-kulcsok használata](#sas-minimum-permissions)</li></ul> |
| **Az Azure Document DB-ről** | <ul><li>[Csatlakozás az Azure Cosmos DB, amikor csak lehetséges erőforrás-jogkivonatok használatával](#resource-docdb)</li></ul> |
| **Az Azure megbízhatósági kapcsolat határán** | <ul><li>[Azure-előfizetéshez az RBAC használatával részletes hozzáférés-vezérlést engedélyezése](#grained-rbac)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Az RBAC használatával fürtműveletek ügyfél elérésének korlátozása](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Biztonsági modellezési mező biztonság használható, és ahol szükséges](#modeling-field)</li></ul> |
| **Dynamics CRM-portál** | <ul><li>[Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Table Storage entitástartományának a minden részletre kiterjedő engedély megadása](#permission-entities)</li><li>[Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure storage-fiókba az Azure Resource Manager engedélyezése](#rbac-azure-manager)</li></ul> |
| **Mobileszköz ügyfél** | <ul><li>[Implicit függetlenítés vagy észlelési telepítés végrehajtása](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[A WCF gyenge Osztályhivatkozása](#weak-class-wcf)</li><li>[WCF-megvalósítása engedélyezési vezérlő](#wcf-authz)</li></ul> |
| **Webes API** | <ul><li>[Megfelelő engedélyezési mechanizmus megvalósításához az ASP.NET Web API](#authz-aspnet)</li></ul> |
| **IoT-eszköz** | <ul><li>[Hajtsa végre az eszköz engedélyezési ellenőrzések, ha támogatja a különböző műveleteket, amelyek különböző jogosultsági szintek igényelnek](#device-permission)</li></ul> |
| **IoT helyszíni átjáró** | <ul><li>[Hajtsa végre a helyszíni átjáró engedélyezési ellenőrzések, ha támogatja a különböző műveleteket, amelyek különböző jogosultsági szintek igényelnek](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Győződjön meg arról, hogy a megfelelő ACL-eket vannak konfigurálva a jogosulatlan hozzáférés korlátozása az eszközön tárolt adatok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy a megfelelő ACL-eket vannak konfigurálva a jogosulatlan hozzáférés korlátozása az eszközön tárolt adatok|

## <a id="sensitive-directory"></a>Győződjön meg arról, hogy az érzékeny felhasználó-specifikus alkalmazás tartalmát a rendszer felhasználói profil könyvtárban tárolja

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az érzékeny felhasználó-specifikus alkalmazás tartalmát a rendszer felhasználói profil könyvtárban tárolja. Ez az, hogy a gép több felhasználó egymás adataihoz hozzáférjenek.|

## <a id="deployed-privileges"></a>Győződjön meg arról, hogy a telepített alkalmazások a legkevesebb jogosultsággal futnak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az üzembe helyezett alkalmazás fut-e a legalacsonyabb jogosultságok. |

## <a id="sequential-logic"></a>Egymást követő lépést ahhoz, ha üzleti logikai folyamatok feldolgozása vonatkozóan

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy ebben a szakaszban volt egy valódi felhasználónak futtatnia emberi valós időben feldolgozott összes lépés a csak üzleti logika folyamatok egymást követő lépést ahhoz, hogy az alkalmazás kényszeríteni szeretné, és nem dolgozza fel sorrendben, kihagyva lépések , lépést egy másik felhasználója által feldolgozott, vagy túl gyorsan elküldött tranzakciókat.|

## <a id="rate-enumeration"></a>Mechanizmus enumerálás megelőzése érdekében korlátozza arány megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy bizalmas azonosítók véletlenszerű. Névtelen oldalain CAPTCHA-vezérlés megvalósításához. Győződjön meg arról, hogy hiba- és a kivétel nem kódproblémájáról adott adatok|

## <a id="principle-least-privilege"></a>Győződjön meg arról, hogy megfelelő engedély van beállítva, és a legalacsonyabb jogosultságok elvét az azt követő

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az elvet azt jelenti, egy felhasználói fiókot csak ezeket a jogosultságokat szempontjából alapvető fontosságú, hogy a felhasználók dolgozhatnak. Például egy biztonsági mentési felhasználónak nem kell szoftvert telepíteni: ezért biztonsági mentési rendelkezik jogosultságokkal csak a biztonsági mentési és a biztonsági mentésével kapcsolatos alkalmazások futtatásához. Más jogosultságokat, mint például az új szoftverek telepítése le vannak tiltva. Az elve is vonatkozik egy személyi számítógép felhasználó, aki általában működik az egy normál felhasználói fiókot, és megnyílik egy rendszerjogosultságú, jelszóval védett fiók (azaz superuser) csak ha a helyzet egyáltalán megköveteli azt. </p><p>Ezt az elvet a webes alkalmazásokhoz is alkalmazható. Helyett kizárólag a szerepkör-alapú hitelesítési módszerek használatával a munkamenetek, attól függően inkább szeretnénk jogosultságok hozzárendelése a felhasználók számára olyan adatbázis-alapú hitelesítés rendszerrel. Ha a felhasználó bekerült helyesen, csak most ahelyett, hogy a felhasználó számára, ellenőrizze, milyen műveleteket végezzen he hajtsa végre a rendszer az emelt szintű jogosultságokkal rendelkező hozzárendeljük adott szerepkörrel azonosítása érdekében munkamenetek továbbra is használjuk. A metódus egy big Data típusú pro is, minden alkalommal, amikor egy felhasználó rendelni a módosításokat alkalmazza menet közben, mivel a hozzárendelése nem függ a munkamenet, amelyek egyébként első kellett kevesebb jogosultsággal rendelkezik.</p>|

## <a id="logic-request-parameters"></a>Üzleti logika és a resource access felhasználását engedélyezési döntésekhez nem kell a bejövő kérelem paraméterei alapján

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Minden alkalommal, amikor ellenőrzi a felhasználó-e korlátozni, tekintse át az egyes adatok, a hozzáférési korlátozásokat feldolgozott kiszolgálóoldali kell lennie. A felhasználói azonosító egy munkamenet-változó a bejelentkezési belül kell tárolni, és felhasználói adatokat lekérni az adatbázis használható |

### <a name="example"></a>Példa
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Most egy esetleges támadó is védelmet, és nem módosítsa az alkalmazás művelet, mivel az adatok beolvasása az azonosítója a kiszolgálóoldali kezeli.

## <a id="enumerable-browsing"></a>Győződjön meg arról, hogy a tartalom és erőforrások nem enumerálható vagy elérhetők közötti választásra tallózása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>A webalkalmazás-legfelső szintű bizalmas statikus és a konfigurációs fájlok nem kell tárolni. A tartalmat nem kell lennie a nyilvános, vagy a megfelelő hozzáférés-vezérlést alkalmazni kell, vagy maga a tartalom eltávolítását.</p><p>Ezenkívül közötti választásra böngészés általában együtt találgatásos technikák összegyűjteni a számba venni a könyvtárak és fájlok a kiszolgálón a lehető legtöbb URL-címek elérésére tett kísérlet. A támadók gyakran meglévő összes két előfordulhat, hogy ellenőrizze fájlokat. Például egy jelszó keresési psswd.txt, password.htm, password.dat és egyéb változások többek között fájlok lenne tartalmazzák.</p><p>A hiba elhárítása érdekében az találgatásos kísérlet észlelési képességeket kell tartalmaznia.</p>|

## <a id="privileged-server"></a>Győződjön meg arról, hogy alacsonyabb szintű fiókok használják-e csatlakozni az adatbázis-kiszolgáló

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az SQL Database engedélyekkel hierarchia](https://msdn.microsoft.com/library/ms191465), [SQL database biztonságos elemekhez](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | Alacsonyabb szintű fiókok az adatbázishoz való csatlakozáshoz használandó. Alkalmazás bejelentkezési az adatbázisban kell korlátozódnia, és a kiválasztott tárolt eljárások csak futtatni kell. Alkalmazás bejelentkezési nem rendelkezhetnek közvetlen tábla. |

## <a id="rls-tenants"></a>Sor szintű biztonsági RLS megakadályozhatja, hogy a bérlők egymás adataihoz hozzáférjenek megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Az SQL Azure, a rendszert |
| **Attribútumok**              | SQL - 12-es, az SQL-verzió - verzió MsSQL2016 |
| **Hivatkozások**              | [Az SQL Server sorszintű biztonság (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Lépések** | <p>A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet).</p><p>Sorszintű biztonság (RLS) egyszerűsíti a megtervezését és kódolását az alkalmazás. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Biztosítható például, hogy a munkavállalók csak a szervezeti egységükre vonatkozó adatsorokhoz férjenek hozzá, vagy egy ügyfél adathozzáférése korlátozható a vállalatával kapcsolatos adatokra.</p><p>A hozzáférés korlátozási logika található az adatbázisszinten, hanem helyezkedik el az adatokat egy másik alkalmazás szinten. Az adatbázis-rendszer a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az egyik csomagunkban kísérel meg, hogy adatelérési. Így a biztonsági rendszer megbízhatóbb és robusztus biztonsági rendszer felületének csökkentése révén.</p><p>|

Vegye figyelembe, hogy az RLS-a-beépített adatbázis-szolgáltatásként egyaránt vonatkozik csak az SQL Server 2016 indítása és az Azure SQL database. Ha a-a-beépített RLS funkce není implementována, biztosítani kell, hogy adatelérési-e a korlátozott nézetek használata és eljárások

## <a id="sysadmin-users"></a>SysAdmin (rendszergazda) szerepkörrel kell rendelkeznie az érvényes szükséges felhasználók

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az SQL Database engedélyekkel hierarchia](https://msdn.microsoft.com/library/ms191465), [SQL database biztonságos elemekhez](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | A sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai kell nagyon korlátozott, és soha nem tartalmazzák az alkalmazások által használt fiókok.  Tekintse át az a szerepkörhöz tartozó felhasználók listáját, és távolítsa el a felesleges fiókokat|

## <a id="cloud-least-privileged"></a>Alacsonyabb szintű jogkivonatok használatával Felhőbeli átjárón

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Átjáró kiválasztása – Azure IoT Hub |
| **Hivatkozások**              | [Az IOT Hub-hozzáférés-vezérlés](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Lépések** | Adja meg a felhő átjárón (IoT Hub) használt különféle összetevőket legkisebb jogosultság engedélyekkel. Tipikus példája – eszköz-kezelés/kiépítés összetevő által használt registryread írható, Event Processor (ASA) használ a szolgáltatás csatlakozása. Az egyes eszközök csatlakoznak az eszköz hitelesítő adatai használatával|

## <a id="sendonly-sas"></a>Egy csak küldési engedélyeket SAS-kulcsot használ a tokenek létrehozásához

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Egyéni eszköz jogkivonatokat hoz létre egy SAS-kulcs szolgál. Egy adott közzétevő az eszköz jogkivonatát elkészítése közben csak küldési engedéllyel SAS-kulcs használata|

## <a id="access-tokens-hub"></a>Ne használjon, amely közvetlen hozzáférést biztosíthat az Eseményközpont hozzáférési jogkivonatok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Egy jogkivonatot, amely közvetlen hozzáférést biztosít az event hubs az eszköznek nem kell megadni. A legalacsonyabb jogosultsági szintű jogkivonatot használ az eszköz csak a közzétevő hozzáférést biztosító segít azonosítani és tiltólistára, ha egy engedélyezetlen bizonyul, vagy feltört eszköz.|

## <a id="sas-minimum-permissions"></a>Csatlakozás az Eseményközponthoz a minimálisan szükséges jogosultsággal rendelkező SAS-kulcsok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Adja meg a különböző háttér-alkalmazások, amelyek az Event Hubs legalacsonyabb jogosultsági engedélyekkel. Háttér-alkalmazások mindegyike külön SAS-kulcsok létrehozása, és csak adja meg a szükséges engedélyekkel – küldés, fogadás vagy kezelés hozzájuk.|

## <a id="resource-docdb"></a>Csatlakozás a Cosmos DB-hez, amikor csak lehetséges erőforrás-jogkivonatok használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Egy erőforrás-jogkivonat társítva az Azure Cosmos DB-engedély erőforrás és kell-e a kapcsolat a felhasználó az adatbázis és az engedély között, hogy a felhasználó rendelkezik egy adott Azure Cosmos DB alkalmazás erőforrást (pl. gyűjtemény és dokumentum). Mindig egy erőforrás-jogkivonat használatával az Azure Cosmos DB eléréséhez, ha az ügyfél nem adható meg megbízhatóként a master és a csak olvasható kulcsok – mint például a mobil- vagy asztali ügyfél végfelhasználói alkalmazás kezelése. Használja a főkulcs vagy háttérmodul-alkalmazásokkal, amelyek ezeket a kulcsokat biztonságosan tárolhatja kulcsait csak olvasható.|

## <a id="grained-rbac"></a>Azure-előfizetéshez az RBAC használatával részletes hozzáférés-vezérlést engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Lépések** | Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges.|

## <a id="cluster-rbac"></a>Az RBAC használatával fürtműveletek ügyfél elérésének korlátozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [Szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Lépések** | <p>Az Azure Service Fabric támogatja a Service Fabric-fürt csatlakozó ügyfelek különböző hozzáférés-vezérlő kétféle: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így több biztonságos a fürt egyes fürtműveletek való hozzáférés korlátozásához.</p><p>A rendszergazdák (beleértve az olvasási/írási képességeket) felügyeleti képességek teljes hozzáféréssel rendelkezik. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához.</p><p>A két ügyfél szerepkörök (a rendszergazda és az ügyfél) a fürt létrehozása idején adjon meg egyedi tanúsítványt azáltal, hogy az egyes.</p>|

## <a id="modeling-field"></a>Biztonsági modellezési mező biztonság használható, és ahol szükséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Biztonsági modellezési mező biztonság használható, és ahol szükséges|

## <a id="portal-security"></a>Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM-portál | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok|

## <a id="permission-entities"></a>Az Azure Table Storage entitástartományának a minden részletre kiterjedő engedély megadása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | StorageType - tábla |
| **Hivatkozások**              | [Hogyan delegálása SAS használatával az Azure storage-fiókjában található objektumok elérése](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Lépések** | Bizonyos üzleti forgatókönyvek esetén az Azure Table Storage fiókdíjat caters másik fél bizalmas adatokat tárolhat. Például bizalmas adatokat kérelmekre vonatkozó különböző országokban megvalósítható. Ezekben az esetekben SAS-aláírás lehet létrehozni a partíció- és sorkulcsot kulcstartományokkal, megadásával a felhasználók érhetik el egy adott ország jellemző adatok.| 

## <a id="rbac-azure-manager"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure storage-fiókba az Azure Resource Manager engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Hogyan tegye biztonságossá tárfiókját, a szerepköralapú hozzáférés-vezérlés (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Lépések** | <p>Amikor létrehoz egy új tárfiókot, válassza ki az Azure Resource Manager és klasszikus telepítési modell. A klasszikus modellt az Azure-erőforrások létrehozását, csak lehetővé teszi, hogy a kiküszöböléséhez hozzáférést az előfizetés, és kapcsolja be, a storage-fiókot.</p><p>Az Azure Resource Manager-modell helyezi a tárfiók egy erőforrás-csoport és hozzáférés a felügyeleti sík, hogy adott storage-fiók az Azure Active Directoryval való. Például adott felhasználóknak biztosíthat hozzáférést a tárfiók kulcsaihoz, más felhasználók megtekintheti a storage-fiók adatait, de nem tudja elérni a tárfiók kulcsait.</p>|

## <a id="rooting-detection"></a>Implicit függetlenítés vagy észlelési telepítés végrehajtása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Alkalmazás ezért biztonságos helyen tárolja a esetben, ha a telefonos feltörték vagy jailbreakelni saját konfigurációval és felhasználói adatokkal. Telepítés/egyes használhatatlanná tévő azt jelenti, hogy jogosulatlan hozzáférést, mely normál felhasználók nem a saját telefonokon. Ezért az alkalmazás kellene rendelkeznie az implicit észlelési logikája az alkalmazás indításakor, észleli, hogy rootolt-e a telefonhoz.</p><p>Az észlelési logikát is egyszerűen hozzáférhet az melyik általában csak gyökér szintű felhasználó férhet hozzá, például fájlok:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/System/bin/su</li><li>/System/xbin/su</li><li>/Data/Local/xbin/su</li><li>/Data/local/bin/su</li><li>/System/SD/xbin/su</li><li>/System/bin/FailSafe/su</li><li>/Data/Local/su</li></ul><p>Ha az alkalmazás el tudja érni ezeket a fájlokat, azt jelöli, hogy az alkalmazás fut a gyökérszintű felhasználóként.</p>|

## <a id="weak-class-wcf"></a>A WCF gyenge Osztályhivatkozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Lépések** | <p>A rendszer gyenge osztály hivatkozást, amely előfordulhat, hogy egy támadó illetéktelenül kódot használja. A program egy felhasználó által meghatározott osztály, amely nem egyedileg azonosítható hivatkozik. Ez kis mértékben azonosított osztály .NET betöltésekor a CLR-beli típus betöltő keresi az osztály a következő helyeken a megadott sorrendben:</p><ol><li>Ha a szerelvény típusú ismert, a betöltő a konfigurációs fájl átirányítási helyeken keresi, GAC, a konfigurációs adatokat, és az alkalmazás alapkönyvtárának aktuális szerelvény</li><li>A szerelvény nem ismert, ha a betöltő keres az aktuális szerelvény, mscorlib, és a TypeResolve eseménykezelő által visszaadott helye</li><li>Hurkok, mint például a típusú továbbítás mechanizmus és a AppDomain.TypeResolve esemény lehet módosítani a CLR-beli keresési sorrendje</li></ol><p>Ha egy támadó kihasználja a CLR-beli keresési sorrend hozzon létre egy másik osztály ugyanazzal a névvel és a egy másodlagos helyet, hogy a CLR-beli tölti be először, a CLR-beli helyezés véletlenül hajtsa végre a támadó által megadott kódot</p>|

### <a name="example"></a>Példa
A `<behaviorExtensions/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF egyéni viselkedés osztály egy adott WCF-bővítmény hozzáadása.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Egyedi használatával (erős) teljesen minősített nevek egy típust azonosítja, és tovább növeli a biztonságot, a rendszer. Használja szerelvény teljesen minősített nevek, típusok regisztrálásakor a machine.config és az app.config fájlban.

### <a name="example"></a>Példa
A `<behaviorExtensions/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF egyéni viselkedés erősen hivatkozott osztály hozzáadása egy adott WCF-bővítményt.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>WCF-megvalósítása engedélyezési vezérlő

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_unauthorized_access) |
| **Lépések** | <p>Ez a szolgáltatás nem használja az engedélyezési vezérlők. Amikor egy ügyfél egy adott WCF-szolgáltatást hív meg, a WCF biztosít különböző engedélyezési programok, győződjön meg arról, hogy a hívó jogosult a szolgáltatás metódus végrehajtása a kiszolgálón. Engedélyezési vezérlők nincsenek engedélyezve a WCF-szolgáltatások, ha egy hitelesített felhasználó jogosultságok eszkalálását érheti el.</p>|

### <a name="example"></a>Példa
A következő konfigurációt arra utasítja a WCF való végrehajtásakor a szolgáltatás ellenőrzi az ügyfél engedélyezési szintje:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
A szolgáltatás engedélyezési séma használatával győződjön meg arról, hogy a szolgáltatás metódus a hívó jogosult-e ennek a végrehajtására. A WCF kétféle módon biztosítja, és lehetővé teszi, hogy egy egyéni engedélyezési séma meghatározása. A UseWindowsGroups mód Windows-szerepkörök és -felhasználók és a UseAspNetRoles módot használja az ASP.NET szerepkör szolgáltatóját, például az SQL Server, hitelesítéséhez.

### <a name="example"></a>Példa
A következő konfigurációt arra utasítja a WCF, győződjön meg arról, hogy az ügyfél a Rendszergazdák csoport tagja a Hozzáadás szolgáltatás végrehajtása előtt:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
A szolgáltatás majd van deklarálva, a következőket:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Megfelelő engedélyezési mechanizmus megvalósításához az ASP.NET Web API

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, MVC5 |
| **Attribútumok**              | Nem releváns, Identity Provider - ADFS, identitásszolgáltató – Azure ad-ben |
| **Hivatkozások**              | [Hitelesítés és engedélyezés az ASP.NET webes API-k](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Lépések** | <p>Ha az alkalmazás támaszkodik őket identitás-szolgáltatóként, vagy előfordulhat, hogy maga az alkalmazás AD FS-jogcímek megadott, vagy az alkalmazás felhasználóinak szerepköradatok származtatható Azure ad-ben. Az ilyen esetek bármelyikében egyéni engedélyezési végrehajtása ellenőrizni kell a felhasználói szerepköri információkat.</p><p>Ha az alkalmazás támaszkodik őket identitás-szolgáltatóként, vagy előfordulhat, hogy maga az alkalmazás AD FS-jogcímek megadott, vagy az alkalmazás felhasználóinak szerepköradatok származtatható Azure ad-ben. Az ilyen esetek bármelyikében egyéni engedélyezési végrehajtása ellenőrizni kell a felhasználói szerepköri információkat.</p>

### <a name="example"></a>Példa
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
A tartományvezérlők és a művelet metódusokkal, amelyeket meg kell védeni kell rendelkeznie az attribútum felett.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Hajtsa végre az eszköz engedélyezési ellenőrzések, ha támogatja a különböző műveleteket, amelyek különböző jogosultsági szintek igényelnek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az eszköz engedélyezhetik a hívót annak ellenőrzése, ha a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. Az funkciók, például a távoli zárolás az ajtó biztosít, továbbá például lehetővé teszi, hogy tegyük fel, hogy az eszköz nem egy intelligens ajtó zárolás figyelhető a felhőből.</p><p>A Smart Lock ajtó videókban rejlő információk funkciókat biztosít, csak ha valaki fizikailag közel az ajtó kártyával. Ebben az esetben a távoli parancs és vezérlés megvalósítása oly módon, hogy nem biztosít olyan funkciót, az átjáró nem jogosult kell küldeni egy parancsot a ajtajának kinyitása kinyitása kell elvégezni.</p>|

## <a id="field-permission"></a>Hajtsa végre a helyszíni átjáró engedélyezési ellenőrzések, ha támogatja a különböző műveleteket, amelyek különböző jogosultsági szintek igényelnek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A helyszíni átjáró engedélyezhetik a hívót annak ellenőrzése, ha a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. A például lehetnek egy rendszergazdai felhasználói felület és az API egy átjáró mező-v-vagy HTTPS, csatlakozó eszközök konfigurálásához használt különböző engedélyeket.|
