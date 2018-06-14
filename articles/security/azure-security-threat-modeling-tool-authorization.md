---
title: -Microsoft fenyegetés modellezési eszköz - engedélyezési Azure |} Microsoft Docs
description: a fenyegetések modellezése eszköz felfedett fenyegetések megoldást
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: b9ad3ceeb77a4adc2c47b262aa40a48c14423198
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019517"
---
# <a name="security-frame-authorization--mitigations"></a>Biztonsági keret: Engedélyezési |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy a megfelelő hozzáférés-vezérlési listák vannak konfigurálva az eszközhöz való illetéktelen hozzáférés korlátozása](#acl-restricted-access)</li><li>[Győződjön meg arról, hogy a bizalmas felhasználó-specifikus alkalmazás tartalma található-e felhasználóiprofil könyvtárban](#sensitive-directory)</li><li>[Győződjön meg arról, hogy a legkevesebb jogosultsággal futtatja a telepített alkalmazások](#deployed-privileges)</li></ul> |
| **Webalkalmazás** | <ul><li>[Egymást követő lépés rendelés kényszerítése üzleti logika adatfolyamok feldolgozásakor](#sequential-logic)</li><li>[Korlátozza az eljárást, amely megakadályozza a számbavételi arány megvalósítása](#rate-enumeration)</li><li>[Győződjön meg arról, hogy megfelelő engedély van beállítva, és a legalacsonyabb jogosultságok elvét az azt követő](#principle-least-privilege)</li><li>[Üzleti logika és az erőforrás hozzáférés felhasználását engedélyezési döntésekhez nem kell a bejövő kérelemben szereplő paraméterek alapján](#logic-request-parameters)</li><li>[Győződjön meg arról, hogy a tartalom és erőforrások nem enumerálható vagy közötti választásra böngészés keresztül érhető el](#enumerable-browsing)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy legkevésbé jogosultsági szintű fiók adatbázis-kiszolgálóhoz való csatlakozáshoz használják](#privileged-server)</li><li>[Sor szintű biztonsági RLS megakadályozhatja, hogy a bérlők a másik fél adatokhoz hozzáférő megvalósítása](#rls-tenants)</li><li>[SysAdmin (rendszergazda) szerepkör csak kell érvényes szükséges felhasználók](#sysadmin-users)</li></ul> |
| **Az IoT átjáró** | <ul><li>[Felhő átjárón legkevésbé jogosultságú tokenek használatára](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[A csak küldési engedélyeket SAS-kulcsot használ az eszköz jogkivonatokat előállító](#sendonly-sas)</li><li>[Ne használja a jogkivonatot, amely közvetlen hozzáférést biztosít az Event hubs](#access-tokens-hub)</li><li>[Csatlakozás Eseményközpont, amely rendelkezik a szükséges minimális engedélyeket az SAS-kulcsok használata](#sas-minimum-permissions)</li></ul> |
| **Az Azure Document DB rendszerbe** | <ul><li>[Erőforrás-jogkivonatok segítségével csatlakozzon Azure Cosmos Adatbázishoz. Ha lehetséges](#resource-docdb)</li></ul> |
| **Az Azure megbízhatósági kapcsolat határán** | <ul><li>[Engedélyezi a részletes hozzáféréskezelést az RBAC használata Azure-előfizetéshez](#grained-rbac)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Ügyfél elérésének korlátozása a fürt működését az RBAC használata](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Biztonsági modellezési mező biztonság használható, és ha szükséges](#modeling-field)</li></ul> |
| **Dynamics CRM-portál** | <ul><li>[Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Table Storage-ban entitástartományának részletes engedély megadása](#permission-entities)</li><li>[Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Resource Manager használatával Azure storage-fiók engedélyezése](#rbac-azure-manager)</li></ul> |
| **Mobileszköz ügyfél** | <ul><li>[Implicit függetlenítés vagy észlelési telepítés végrehajtása](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[A WCF gyenge Osztályhivatkozást](#weak-class-wcf)</li><li>[WCF-megvalósítása engedélyezési vezérlő](#wcf-authz)</li></ul> |
| **Webes API** | <ul><li>[Valósítja meg a megfelelő engedélyezési mechanizmus ASP.NET Web API-ban.](#authz-aspnet)</li></ul> |
| **IoT-eszközök** | <ul><li>[Engedélyezési ellenőrzéseket hajtanak végre az eszközt, ha az támogatja-e különböző jogosultsági szintek különböző műveleteket](#device-permission)</li></ul> |
| **Az IoT-mező átjáró** | <ul><li>[Hitelesítési ellenőrzések elvégzéséhez az a mező átjáró, ha az támogatja-e különböző jogosultsági szintek különböző műveleteket](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Győződjön meg arról, hogy a megfelelő hozzáférés-vezérlési listák vannak konfigurálva az eszközhöz való illetéktelen hozzáférés korlátozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy a megfelelő hozzáférés-vezérlési listák vannak konfigurálva az eszközhöz való illetéktelen hozzáférés korlátozása|

## <a id="sensitive-directory"></a>Győződjön meg arról, hogy a bizalmas felhasználó-specifikus alkalmazás tartalma található-e felhasználóiprofil könyvtárban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy bizalmas felhasználó-specifikus alkalmazás tartalmát a felhasználóiprofil könyvtárban tárolja. Ez az, hogy a gép több felhasználók egymás adatok hozzáférését.|

## <a id="deployed-privileges"></a>Győződjön meg arról, hogy a legkevesebb jogosultsággal futtatja a telepített alkalmazások

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy fut-e a telepített alkalmazás legkevesebb jogosultsággal. |

## <a id="sequential-logic"></a>Egymást követő lépés rendelés kényszerítése üzleti logika adatfolyamok feldolgozásakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az ebben a szakaszban az alkalmazás csak folyamat üzleti logika viszonylatában egymást követő lépés sorrendben kikényszerítéséhez emberi valós időben feldolgozott összes lépések valódi felhasználója futott, és nem dolgozza fel nem megfelelő sorrendben, kihagyva a lépései , egy másik felhasználó lépés, feldolgozott vagy túl gyorsan benyújtott tranzakciók.|

## <a id="rate-enumeration"></a>Korlátozza az eljárást, amely megakadályozza a számbavételi arány megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy bizalmas azonosítók véletlenszerű. CAPTCHA vezérléséhez a névtelen lapokon. Győződjön meg arról, hogy hiba és a kivétel nem kódproblémájáról adatokat|

## <a id="principle-least-privilege"></a>Győződjön meg arról, hogy megfelelő engedély van beállítva, és a legalacsonyabb jogosultságok elvét az azt követő

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az elve azt jelenti, egy felhasználói fiókot csak a lényeges, hogy a felhasználók dolgozhatnak jogosultságokkal. Például a biztonsági mentési felhasználó nem kell telepíteni:, ezért a biztonsági mentési felhasználó rendelkezik-e a biztonsági mentési és biztonsági mentésével kapcsolatos alkalmazások futtatásához csak jogosultsággal. Más jogosultságokat, mint például az új szoftverek telepítése le vannak tiltva. Az elve is vonatkozik egy általában normál felhasználói fiókok használhatók, és megnyílik egy rendszerjogosultságú, jelszóval védett fiókot (Ez azt jelenti, hogy a rendszeradminisztrátor) személyi számítógép felhasználó csak ha a helyzet feltétlenül megköveteli azt. </p><p>Ez az elv a webes alkalmazásokhoz is alkalmazhatók. Ahelyett, hogy kizárólag attól függően, szerepkör-alapú hitelesítési módszerek használatával munkamenetek, ahelyett, hogy szeretnénk jogosultságok hozzárendelése felhasználókhoz olyan adatbázis-alapú hitelesítés rendszerrel. A Microsoft továbbra is használja a munkamenetek megállapítani, hogy a felhasználó be voltak jelentkezve helyesen, csak most nem rendeli egy adott szerepkör azt rendelje őt ellenőrzése milyen műveleteket, hogy végrehajtását a rendszer az emelt szintű jogosultságokkal rendelkező felhasználó. Ez a módszer nagy pro is, amikor a felhasználó rendelkezik-e hozzárendelni a rendszer alkalmazza a beállításokat menet közben a hozzárendelése nem függ a munkamenetet, amelyek egyébként első kellett óta kevesebb jogosultsággal.</p>|

## <a id="logic-request-parameters"></a>Üzleti logika és az erőforrás hozzáférés felhasználását engedélyezési döntésekhez nem kell a bejövő kérelemben szereplő paraméterek alapján

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ha vannak ellenőrzése, hogy a felhasználó korlátozódik tekintse át az egyes adatok, a hozzáférési korlátozásokat feldolgozott kiszolgálóoldali kell lennie. A felhasználói azonosítóját a bejelentkezési munkamenet változó belül kell tárolni, és használandó felhasználói adatok beolvasása adatbázisból |

### <a name="example"></a>Példa
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Most egy esetleges támadó is védelmet, és nem módosítható a alkalmazás műveletet, mert az azonosítóját, az adatok beolvasása a kiszolgálóoldali kezelését.

## <a id="enumerable-browsing"></a>Győződjön meg arról, hogy a tartalom és erőforrások nem enumerálható vagy közötti választásra böngészés keresztül érhető el

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Bizalmas statikus és konfigurációs fájljait nem kell tartani a webalkalmazás-gyökérben. A tartalmat nem kell lennie, vagy a megfelelő hozzáférés-vezérlést alkalmazni kell és a tartalom eltávolítását.</p><p>Emellett közötti választásra böngészés általában együtt találgatásos technikák adatok gyűjtéséhez általi számba venni a könyvtárak és fájlok a kiszolgálón a lehető legtöbb URL-címek elérésére tett kísérlet. A támadók gyakran meglévő összes változatát előfordulhat, hogy keressen fájlokat. Például egy jelszó keresési psswd.txt, password.htm, password.dat és egyéb változatok beleértve fájlok volna foglalnia.</p><p>Ennek orvoslása érdekében a találgatásos kísérletek észlelése képességeket is tartalmaznia kell.</p>|

## <a id="privileged-server"></a>Győződjön meg arról, hogy legkevésbé jogosultsági szintű fiók adatbázis-kiszolgálóhoz való csatlakozáshoz használják

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [SQL Database engedélyekkel hierarchia](https://msdn.microsoft.com/library/ms191465), [SQL adatbázis securables](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | Legkevesebb jogosultságú fiókok használatával kapcsolódni az adatbázishoz. Alkalmazás bejelentkezési kell korlátozódnia az adatbázisban, és csak a kijelölt tárolt eljárások kell végrehajtani. Az alkalmazás bejelentkezési nincs közvetlen tábla hozzáféréssel kell rendelkeznie. |

## <a id="rls-tenants"></a>Sor szintű biztonsági RLS megakadályozhatja, hogy a bérlők a másik fél adatokhoz hozzáférő megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL - 12-es verzió, az SQL-verzió - verzió MsSQL2016 |
| **Hivatkozások**              | [SQL Server a sorszintű biztonságot (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Lépések** | <p>A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet).</p><p>A sorszintű biztonságot (RLS) egyszerűbbé teszi a terv és az alkalmazás biztonsági kódolása. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Biztosítható például, hogy a munkavállalók csak a szervezeti egységükre vonatkozó adatsorokhoz férjenek hozzá, vagy egy ügyfél adathozzáférése korlátozható a vállalatával kapcsolatos adatokra.</p><p>A hozzáférés korlátozási logika található az adatbázis-rétegből, hanem helyezkedik el az adatokat egy másik alkalmazás réteg. Az adatbázis-rendszer a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az adott adatok próbál meg elérni a réteg alapján. Ez lehetővé teszi a biztonsági rendszer megbízhatóbb és robusztus a biztonsági rendszer felületének csökkentése révén.</p><p>|

Vegye figyelembe, hogy az a-kész adatbázis szolgáltatás RLS tulajdonság csak SQL Server 2016 elindítása és az Azure SQL adatbázis vonatkozik. Ha a out-of-az-box RLS funkció nincs megvalósítva, biztosítani kell, hogy adat-hozzáférési-e a korlátozott jelenlegi nézetek és eljárások

## <a id="sysadmin-users"></a>SysAdmin (rendszergazda) szerepkör csak kell érvényes szükséges felhasználók

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [SQL Database engedélyekkel hierarchia](https://msdn.microsoft.com/library/ms191465), [SQL adatbázis securables](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | A sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagjai legyen erősen korlátozott, és soha nem tartalmazza az alkalmazások által használt fiókok.  Tekintse át a szerepet betöltő felhasználók listáját, és távolítsa el a felesleges fiókokat|

## <a id="cloud-least-privileged"></a>Felhő átjárón legkevésbé jogosultságú tokenek használatára

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Átjáró choice - Azure IoT Hub |
| **Hivatkozások**              | [Az IOT-központ hozzáférés-vezérlés](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Lépések** | Adja meg a felhő átjárón (IoT-központ) használt különféle összetevőket legkisebb jogosultság engedélyekkel. Tipikus példája – eszközt felügyeletre/kiépített összetevő használja registryread írása, esemény processzor (ASA) használja a szolgáltatás csatlakozzon. Egyes eszközök csatlakoznak a hitelesítő adatai segítségével|

## <a id="sendonly-sas"></a>A csak küldési engedélyeket SAS-kulcsot használ az eszköz jogkivonatokat előállító

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítés és a biztonsági modell – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | SAS-kulcs létrehozásához az egyes eszközök tokenjeit használatos. Az eszköz jogkivonatát generálása a megadott közzétevő csak küldési engedéllyel SAS-kulcs használata|

## <a id="access-tokens-hub"></a>Ne használja a jogkivonatot, amely közvetlen hozzáférést biztosít az Event hubs

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítés és a biztonsági modell – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Egy jogkivonatot, amely közvetlen hozzáférést biztosít az event hubs nem biztosítani kell az eszközt. Egy minimális jogosultságokkal rendelkező jogkivonatot használja az eszköz csak azokra a közzétevőkre hozzáférést biztosító segít azonosítani és tiltólistára akkor, ha egy engedélyezetlen található, vagy sérült eszköz.|

## <a id="sas-minimum-permissions"></a>Csatlakozás Eseményközpont, amely rendelkezik a szükséges minimális engedélyeket az SAS-kulcsok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítés és a biztonsági modell – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Adja meg a különböző háttér-alkalmazások, amelyek az Event hubs legalacsonyabb jogosultsági engedélyekkel. Minden háttér-alkalmazáshoz külön SAS-kulcsok létrehozása, és csak adja meg a szükséges engedélyek - küldési, Receive vagy rájuk kezelése.|

## <a id="resource-docdb"></a>Erőforrás-jogkivonatok segítségével csatlakozzon Cosmos Adatbázishoz. Ha lehetséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB rendszerbe | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Egy erőforrás-jogkivonat egy Azure Cosmos DB engedély erőforrás társított, és a kapcsolat rögzíti a felhasználó az adatbázis és az engedélyek között, hogy a felhasználó rendelkezik egy adott Azure Cosmos DB alkalmazás erőforrás (pl. gyűjtemény és dokumentum). Mindig egy erőforrás-jogkivonat segítségével az Azure Cosmos DB elérésére, ha az ügyfél nem adható meg megbízhatóként a fő vagy csak olvasható kulcsot – például a hordozható vagy asztali ügyfél végfelhasználói alkalmazás kezelése. A fő oszlopkulcs vagy csak olvasható kulcsokat biztonságosan tárolhatja ezeket a kulcsokat háttér-alkalmazással.|

## <a id="grained-rbac"></a>Engedélyezi a részletes hozzáféréskezelést az RBAC használata Azure-előfizetéshez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Lépések** | Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges.|

## <a id="cluster-rbac"></a>Ügyfél elérésének korlátozása a fürt működését az RBAC használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [A Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlés](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Lépések** | <p>Azure Service Fabric két különböző hozzáférést vezérlő típusokat támogatja az ügyfelek csatlakoznak a Service Fabric-fürt: rendszergazdai és felhasználói. Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így a fürt biztonságosabb bizonyos fürtműveletekben való hozzáférés korlátozásához.</p><p>Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.</p><p>A két ügyfél szerepkörök (a rendszergazda és az ügyfél) a fürt létrehozása idején adja meg az egyes különálló tanúsítványok megadásával.</p>|

## <a id="modeling-field"></a>Biztonsági modellezési mező biztonság használható, és ha szükséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Biztonsági modellezési mező biztonság használható, és ha szükséges|

## <a id="portal-security"></a>Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM-portál | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Hajtsa végre a biztonsági modellezési figyelembe vételével, amely a biztonsági modell a portál eltér a többi CRM portál fiókok|

## <a id="permission-entities"></a>Az Azure Table Storage-ban entitástartományának részletes engedély megadása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | StorageType - tábla |
| **Hivatkozások**              | [Hogyan adhat hozzáférést a SAS használatával az Azure-tárfiókban lévő objektumok](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Lépések** | Az egyes üzleti forgatókönyvek Azure Table Storage előfordulhat, hogy kell, hogy a különböző felek caters bizalmas adatok tárolásához. Például bizalmas adatokat alkalmas más országokban. Ebben az esetben SAS-aláírások lehet létrehozni a partíció- és sorfejlécek kulcstartományokkal, megadásával úgy, hogy egy felhasználó hozzáférhessen az adott ország vonatkozó adatok.| 

## <a id="rbac-azure-manager"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Resource Manager használatával Azure storage-fiók engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A storage-fiókkal, és a szerepköralapú hozzáférés-vezérlést (RBAC) biztonságossá tétele](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Lépések** | <p>Amikor létrehoz egy új tárfiókot, Azure Resource Manager és klasszikus telepítési modell választja. Az Azure-erőforrások létrehozása a Klasszikus modell csak mindent hozzáférés az előfizetéshez, és a tárfiók viszont lehetővé teszi.</p><p>Az Azure Resource Manager modellt helyezett a tárfiók egy erőforrás-csoport és a vezérlés hozzáférés és az Azure Active Directoryval bizonyos tárolási fiók azon felügyeleti. Például biztosíthat bizonyos felhasználók a tárfiókok kulcsait, elérését, amíg más felhasználók megtekinthetik a tárfiók adatait, de nem tud hozzáférni a tárfiók kulcsait.</p>|

## <a id="rooting-detection"></a>Implicit függetlenítés vagy észlelési telepítés végrehajtása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobileszköz ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Alkalmazás megvédik az esetben, ha a telefonszám feltörték vagy függetlenített saját konfigurációs és a felhasználó adatait. Telepítés/jailbreakelve megtörje azt jelenti, hogy jogosulatlan hozzáférés, mely a normál felhasználók a saját telefonokon nem tegye. Ezért az alkalmazás kell rendelkeznie az implicit észlelési logika alkalmazás következő indításakor, észleli, hogy a telefon rootolt-e.</p><p>Az észlelési logika is egyszerűen hozzáférhet az melyik általában csak gyökér szintű felhasználó férhet hozzá, például fájlok:</p><ul><li>/system/app/Superuser.apk</li><li>/ sbin/su</li><li>/System/bin/su</li><li>/system/xbin/su</li><li>/Data/Local/xbin/su</li><li>/Data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/System/bin/FailSafe/su</li><li>/Data/Local/su</li></ul><p>Alkalmazások hozzáférhetnek az ezeket a fájlokat, ha azt jelöli, hogy fut-e az alkalmazás legfelső szintű felhasználóként.</p>|

## <a id="weak-class-wcf"></a>A WCF gyenge Osztályhivatkozást

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | <p>A rendszer a gyenge osztályait ismertető dokumentációban, amely előfordulhat, hogy egy támadó jogosulatlan kódot használja. A program egy felhasználói osztály, amely egyedileg azonosítja nem hivatkozik. Ez az osztály gyengén azonosított .NET betöltésekor a CLR-típus betöltési keresi az osztály a következő helyeken a megadott sorrendben:</p><ol><li>Ha a típus a szerelvény ismert, a betöltő a konfigurációs fájl átirányítási helyeken keresi, GAC-ban, az aktuális szerelvény konfigurációs adatokat, és az alkalmazás alapkönyvtárának használatával</li><li>Ha a szerelvény ismeretlen, a betöltő keres az aktuális szerelvény, mscorlib használatát, és a TypeResolve eseménykezelő által visszaadott helye</li><li>Például a továbbítási típus mechanizmus és a AppDomain.TypeResolve esemény hurkok módosíthatók a CLR-beli keresési sorrendje</li></ol><p>Ha egy támadó kihasználja a CLR-keresés sorrendjét hozzon létre egy másik osztály ugyanazzal a névvel és véletlenül helyezés egy másodlagos helyet, hogy a közös nyelvi Futtatókörnyezet tölti be először, a közös nyelvi Futtatókörnyezet fogja végrehajtani a támadó által megadott kódot</p>|

### <a name="example"></a>Példa
A `<behaviorExtensions/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF egy egyéni viselkedés osztály hozzáadása egy adott WCF-kiterjesztést.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Nevekkel teljesen minősített (erős) egyedileg azonosítja a típusát, és tovább növeli a rendszer biztonsági. Használja a szerelvény teljesen minősített nevek, típusok a machine.config és az app.config fájlban regisztrálásakor.

### <a name="example"></a>Példa
A `<behaviorExtensions/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF egyéni viselkedés erősen hivatkozott osztály hozzáadása egy adott WCF-kiterjesztést.
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
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | <p>Ez a szolgáltatás nem használ olyan engedélyezési vezérlőt. Amikor egy ügyfél meghívja az adott WCF-szolgáltatások, WCF biztosít a különböző hitelesítési sémák, ellenőrizze, hogy a hívó jogosult a metódus végrehajtása a kiszolgálón. Ha engedélyezési vezérlők WCF-szolgáltatások nincsenek engedélyezve, a hitelesített felhasználók jogosultság-eszkalációs érhető el.</p>|

### <a name="example"></a>Példa
A következő konfigurációs arra utasítja a WCF ellenőrizte a jogosultsági szintet, az ügyfél a szolgáltatás végrehajtásakor:
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
Egy szolgáltatás-hitelesítési séma használatával győződjön meg arról, hogy a hívó a a módszerrel jogosult-e erre. WCF két üzemmódot biztosít, és lehetővé teszi, hogy egy egyéni engedélyezési sémát definíciója. A UseWindowsGroups módot használ a Windows-alapú szerepkörök és felhasználók és a UseAspNetRoles mód az ASP.NET szerepkör-szolgáltató, például az SQL Server segítségével hitelesíteni.

### <a name="example"></a>Példa
A következő konfigurációs arra utasítja a WCF győződjön meg arról, hogy az ügyfél a Rendszergazdák csoport tagja a Hozzáadás szolgáltatás végrehajtása előtt:
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
A szolgáltatás majd van deklarálva, a következő:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Valósítja meg a megfelelő engedélyezési mechanizmus ASP.NET Web API-ban.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, MVC5 |
| **Attribútumok**              | Nincs; Identity Provider - ADFS, identitásszolgáltató - az Azure AD |
| **Hivatkozások**              | [Hitelesítési és engedélyezési ASP.NET webes API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Lépések** | <p>Szerepköri információkat az alkalmazás felhasználóinak származtatható az Azure AD, vagy az AD FS jogcímeket, ha az alkalmazás támaszkodik őket identitás-szolgáltatóként, vagy előfordulhat, hogy az alkalmazás maga biztosítja azt. Egyik sem szerepel ezekben az esetekben az egyéni engedélyezési megvalósítási érdemes ellenőrizni a felhasználói szerepköri információkat.</p><p>Szerepköri információkat az alkalmazás felhasználóinak származtatható az Azure AD, vagy az AD FS jogcímeket, ha az alkalmazás támaszkodik őket identitás-szolgáltatóként, vagy előfordulhat, hogy az alkalmazás maga biztosítja azt. Egyik sem szerepel ezekben az esetekben az egyéni engedélyezési megvalósítási érdemes ellenőrizni a felhasználói szerepköri információkat.</p>

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
A tartományvezérlők és műveletmetódusokhoz, amely védelmet biztosítani kell látható el a fenti attribútum.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Engedélyezési ellenőrzéseket hajtanak végre az eszközt, ha az támogatja-e különböző jogosultsági szintek különböző műveleteket

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Az eszköz engedélyezhetik a hívót annak ellenőrizze, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges jogosultságokkal. A megadható például, hogy tegyük fel például, az eszköz nem figyelhető a felhőből intelligens ajtó zárolást, valamint a Funkciók, például a távoli zárolás az ajtó biztosít.</p><p>Az intelligens ajtó zárolás feloldásának funkciókat biztosít, csak ha valaki fizikailag közel az ajtó kártya. Ebben az esetben a távoli parancskiadási és vezérlési végrehajtásának úgy, hogy nem biztosít olyan funkciót, az ajtó feloldásához, mivel az átjáró nem jogosult a kinyitása parancs küldése a kell elvégezni.</p>|

## <a id="field-permission"></a>Hitelesítési ellenőrzések elvégzéséhez az a mező átjáró, ha az támogatja-e különböző jogosultsági szintek különböző műveleteket

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A mező átjáró engedélyezhetik a hívót annak ellenőrizze, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges jogosultságokkal. A például nem lehet egy rendszergazda felhasználói felület/API-val egy átjáró mező-v/s-hez az eszközök különböző engedélyeit.|
