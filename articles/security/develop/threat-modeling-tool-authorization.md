---
title: Engedélyezés - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: a fenyegetésmodellezési eszközben elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728386"
---
# <a name="security-frame-authorization--mitigations"></a>Biztonsági keret: Engedélyezés | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Gép megbízhatósági határa** | <ul><li>[Annak biztosítása, hogy a megfelelő Hozzáférés-hozzáférés-hozzáférés-hozzáférés-hozzáférés korlátozza az eszközön lévő adatokhoz való jogosulatlan hozzáférést](#acl-restricted-access)</li><li>[Annak ellenőrzése, hogy a bizalmas felhasználóspecifikus alkalmazástartalom a felhasználói profil könyvtárában van-e tárolva](#sensitive-directory)</li><li>[Győződjön meg arról, hogy az üzembe helyezett alkalmazások a legkevesebb jogosultsággal futnak](#deployed-privileges)</li></ul> |
| **Webalkalmazás** | <ul><li>[Egymást követő lépéssorrend kényszerítése üzleti logikai folyamatok feldolgozásakor](#sequential-logic)</li><li>[Sebességkorlátozó mechanizmus megvalósítása a számbavétel megakadályozására](#rate-enumeration)</li><li>[Annak biztosítása, hogy a megfelelő engedélyezés érvényben legyen, és a legkisebb jogosultságok elvét betartsák](#principle-least-privilege)</li><li>[Az üzleti logika és az erőforrás-hozzáférés engedélyezési döntései nem alapulhatnak bejövő kérelemparamétereken.](#logic-request-parameters)</li><li>[Győződjön meg arról, hogy a tartalom és az erőforrások nem szerepelnek, illetve nem érhetők el erőteljes böngészés útján](#enumerable-browsing)</li></ul> |
| **Adatbázis** | <ul><li>[Annak ellenőrzése, hogy a legkevésbé kiemelt jogosultságú fiókok at használják-e az adatbázis-kiszolgálóhoz való csatlakozáshoz](#privileged-server)</li><li>[Sorszintű biztonsági RLS megvalósítása, hogy a bérlők ne férhessenek hozzá egymás adataihoz](#rls-tenants)</li><li>[A sysadmin szerepkörnek csak érvényes szükséges felhasználókkal kell rendelkeznie](#sysadmin-users)</li></ul> |
| **IoT felhőátjáró** | <ul><li>[Csatlakozás a Cloud Gateway-hez a legkevésbé kiemelt jogkivonatokkal](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Csak küldési engedélyek SAS-kulcs használata eszközjogkivonatok létrehozásához](#sendonly-sas)</li><li>[Ne használjon olyan hozzáférési jogkivonatokat, amelyek közvetlen hozzáférést biztosítanak az Event Hubhoz](#access-tokens-hub)</li><li>[Csatlakozás az Event Hubhoz a szükséges minimális engedélyekkel rendelkező SAS-kulcsokkal](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Erőforrás-tokenek használata az Azure Cosmos DB-hez való csatlakozáshoz, amikor csak lehetséges](#resource-docdb)</li></ul> |
| **Azure megbízhatósági határa** | <ul><li>[Részletes hozzáférés-kezelés engedélyezése az Azure-előfizetéshez az RBAC használatával](#grained-rbac)</li></ul> |
| **Szolgáltatásháló-kapcsolat megbízhatósági határa** | <ul><li>[Az ügyfél fürtműveletekhez való hozzáférésének korlátozása az RBAC használatával](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Biztonsági modellezés végrehajtása és szükség esetén a mezőszintű biztonság használata](#modeling-field)</li></ul> |
| **Dynamics CRM-portál** | <ul><li>[A portálfiókok biztonsági modellezésének végrehajtása annak szem előtt tartásával, hogy a portál biztonsági modellje eltér a CRM többi részétől](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Részletes engedély megadása számos entitáshoz az Azure Table Storage-ban](#permission-entities)</li><li>[Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure-tárfiókba az Azure Resource Manager használatával](#rbac-azure-manager)</li></ul> |
| **Mobil ügyfél** | <ul><li>[Implicit jailbreak vagy gyökeresedési észlelésének megvalósítása](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Gyenge osztályhivatkozás a WCF-ben](#weak-class-wcf)</li><li>[WCF-implement hitelesítésvezérlés](#wcf-authz)</li></ul> |
| **Webes API** | <ul><li>[Megfelelő engedélyezési mechanizmus megvalósítása ASP.NET webes API-ban](#authz-aspnet)</li></ul> |
| **IoT-eszköz** | <ul><li>[Engedélyezési ellenőrzések végrehajtása az eszközön, ha támogatja a különböző engedélyszinteket igénylő különböző műveleteket](#device-permission)</li></ul> |
| **IoT field átjáró** | <ul><li>[Engedélyezési ellenőrzések végrehajtása a Field Gateway-ben, ha különböző engedélyszinteket igénylő különböző műveleteket támogat](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Annak biztosítása, hogy a megfelelő Hozzáférés-hozzáférés-hozzáférés-hozzáférés-hozzáférés korlátozza az eszközön lévő adatokhoz való jogosulatlan hozzáférést

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Annak biztosítása, hogy a megfelelő Hozzáférés-hozzáférés-hozzáférés-hozzáférés-hozzáférés korlátozza az eszközön lévő adatokhoz való jogosulatlan hozzáférést|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Annak ellenőrzése, hogy a bizalmas felhasználóspecifikus alkalmazástartalom a felhasználói profil könyvtárában van-e tárolva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy a bizalmas felhasználóspecifikus alkalmazástartalom a felhasználói profil könyvtárában van tárolva. Ezzel megakadályozhatja, hogy a számítógép több felhasználója hozzáférjen egymás adataihoz.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Győződjön meg arról, hogy az üzembe helyezett alkalmazások a legkevesebb jogosultsággal futnak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy az üzembe helyezett alkalmazás fut a legkevesebb jogosultsággal. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Egymást követő lépéssorrend kényszerítése üzleti logikai folyamatok feldolgozásakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Annak ellenőrzése érdekében, hogy ezt a fázist egy valódi felhasználó futtatta-e, az alkalmazást úgy szeretné érvényesíteni, hogy az üzleti logika folyamatait csak egymást követő lépéssorrendben dolgozza fel, és az összes lépést reális emberi idő szerint dolgozza fel, és ne dolgozza fel a sorrenden kívül, kihagyott lépések , egy másik felhasználó lépéseit, vagy túl gyorsan elküldött tranzakciókat.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Sebességkorlátozó mechanizmus megvalósítása a számbavétel megakadályozására

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy a bizalmas azonosítók véletlenszerűek. Végre CAPTCHA ellenőrzés névtelen oldalakon. Annak biztosítása, hogy a hiba és a kivétel ne fedje fel a konkrét adatokat|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Annak biztosítása, hogy a megfelelő engedélyezés érvényben legyen, és a legkisebb jogosultságok elvét betartsák

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az elv azt jelenti, hogy a felhasználói fiók csak azokat a jogosultságokat, amelyek elengedhetetlenek, hogy a felhasználók munkáját. Például egy biztonsági mentést használónak nem kell szoftvert telepítenie: ezért a biztonsági másolat felhasználójának csak a biztonsági mentéshez és a biztonsági mentéshez kapcsolódó alkalmazások futtatásához van jogosultsága. Minden más jogosultság, például új szoftver telepítése le van tiltva. Az elv vonatkozik a személyi számítógép-felhasználó, aki általában nem működik egy normál felhasználói fiók, és nyit egy kiváltságos, jelszóval védett fiók (azaz a rendszergazda) csak akkor, ha a helyzet feltétlenül megköveteli. </p><p>Ez az elv a webes alkalmazásokra is alkalmazható. Ahelyett, hogy kizárólag a munkameneteket használó szerepköralapú hitelesítési módszerektől függenénk, inkább egy adatbázis-alapú hitelesítési rendszer segítségével szeretnénk jogosultságokat rendelni a felhasználókhoz. Továbbra is használjuk a munkameneteket annak megállapítására, hogy a felhasználó megfelelően van-e bejelentkezve, csak most, ahelyett, hogy egy adott szerepkörrel hozzárendelnénk a felhasználót, jogosultságokkal rendeljük hozzá, hogy ellenőrizze, mely műveleteket kell végrehajtania a rendszeren. Szintén egy nagy profi ez a módszer, ha egy felhasználó kell hozzárendelni kevesebb jogosultságot a változások at alkalmazni fogják menet közben, mivel a hozzárendelés nem függ a munkamenet, amely egyébként kellett lejárni az első.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Az üzleti logika és az erőforrás-hozzáférés engedélyezési döntései nem alapulhatnak bejövő kérelemparamétereken.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Amikor azt ellenőrzi, hogy a felhasználó csak bizonyos adatok áttekintésére van-e korlátozva, a hozzáférési korlátozásokat kiszolgálóoldali módon kell feldolgozni. A felhasználói azonosítót bejelentkezéskor egy munkamenet-változóban kell tárolni, és a felhasználói adatok adatbázisból való lekéréséhez kell használni. |

### <a name="example"></a>Példa
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Most egy lehetséges támadó nem szabotázs és módosíthatja az alkalmazás működését, mivel az azonosító az adatok beolvasása kezeli szerver oldali.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Győződjön meg arról, hogy a tartalom és az erőforrások nem szerepelnek, illetve nem érhetők el erőteljes böngészés útján

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A bizalmas statikus és konfigurációs fájlokat nem szabad a web-root-ban tárolni. A nem nyilvános tartalom esetében vagy megfelelő hozzáférés-ellenőrzést kell alkalmazni, vagy magát a tartalmat kell eltávolítani.</p><p>Is, erőteljes böngészés általában együtt Brute Force technikák at információkat gyűjteni azáltal, hogy megpróbálja elérni a lehető legtöbb URL-t, hogy felsorolják könyvtárak és fájlok a szerveren. A támadók ellenőrizhetik a gyakran létező fájlok összes változatát. A jelszófájl-keresés például olyan fájlokat foglal magában, mint a psswd.txt, a password.htm, a password.dat és más változatok.</p><p>Ennek enyhítése érdekében a találgatásos kísérletek észlelésére vonatkozó képességeket is bele kell foglalni.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Annak ellenőrzése, hogy a legkevésbé kiemelt jogosultságú fiókok at használják-e az adatbázis-kiszolgálóhoz való csatlakozáshoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [SQL Database-engedélyek hierarchiája](https://msdn.microsoft.com/library/ms191465), [SQL adatbázis biztonságos](https://msdn.microsoft.com/library/ms190401) |
| **Lépéseket** | A legkevésbé kiemelt jogosultságú fiókokat kell használni az adatbázishoz való csatlakozáshoz. Az alkalmazásbejelentkezést korlátozni kell az adatbázisban, és csak a kiválasztott tárolt eljárásokat kell végrehajtania. Az alkalmazás bejelentkezésének nem kell közvetlen hozzáféréssel rendelkeznie a táblához. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Sorszintű biztonsági RLS megvalósítása, hogy a bérlők ne férhessenek hozzá egymás adataihoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL verzió - V12, SQL verzió - MsSQL2016 |
| **Referencia**              | [SQL Server sorszintű biztonság (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Lépéseket** | <p>A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet).</p><p>A sorszintű biztonság (RLS) leegyszerűsíti az alkalmazás biztonságának tervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Biztosítható például, hogy a munkavállalók csak a szervezeti egységükre vonatkozó adatsorokhoz férjenek hozzá, vagy egy ügyfél adathozzáférése korlátozható a vállalatával kapcsolatos adatokra.</p><p>A hozzáférés-korlátozási logika az adatbázisrétegben található, nem pedig egy másik alkalmazásréteg adataitól. Az adatbázis-rendszer minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor bármely rétegről adatelérést kísérelnek meg. Ez megbízhatóbbá és robusztusabbá teszi a biztonsági rendszert azáltal, hogy csökkenti a biztonsági rendszer felületét.</p><p>|

Kérjük, vegye figyelembe, hogy az RLS mint beépített adatbázis-szolgáltatás csak a 2016-tól kezdődő SQL Server és az Azure SQL-adatbázis esetén alkalmazható. Ha a beépített RLS-szolgáltatás nincs megvalósítva, biztosítani kell, hogy az adatokhoz való hozzáférés korlátozott legyen a nézetek és eljárások használatával

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>A sysadmin szerepkörnek csak érvényes szükséges felhasználókkal kell rendelkeznie

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [SQL Database-engedélyek hierarchiája](https://msdn.microsoft.com/library/ms191465), [SQL adatbázis biztonságos](https://msdn.microsoft.com/library/ms190401) |
| **Lépéseket** | A SysAdmin fix kiszolgálói szerepkör tagjainak nagyon korlátozottnak kell lenniük, és soha nem tartalmazhatnak alkalmazások által használt fiókokat.  Tekintse át a szerepkörben lévő felhasználók listáját, és távolítsa el a szükségtelen fiókokat|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Csatlakozás a Cloud Gateway-hez a legkevésbé kiemelt jogkivonatokkal

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választás - Azure IoT Hub |
| **Referencia**              | [Iot hub hozzáférés-vezérlés](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Lépéseket** | A Felhőátjáróhoz (IoT Hub) kapcsolódó különböző összetevők höz a legkevesebb jogosultsági engedélyek biztosítása. Tipikus példa: Az eszközkezelés/kiépítési összetevő rendszerleíró írást,-writet használ, az eseményfeldolgozó (ASA) a Service Connect szolgáltatást használja. Az egyes eszközök eszközhitelesítő adatokkal csatlakoznak|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Csak küldési engedélyek SAS-kulcs használata eszközjogkivonatok létrehozásához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Eseményközpontok hitelesítése és biztonsági modellje – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépéseket** | A SAS-kulcs az egyes eszközjogkivonatok létrehozásához használható. Csak küldési engedélyek SAS-kulcs használata egy adott közzétevő eszközjogkivonatának létrehozása közben|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Ne használjon olyan hozzáférési jogkivonatokat, amelyek közvetlen hozzáférést biztosítanak az Event Hubhoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Eseményközpontok hitelesítése és biztonsági modellje – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépéseket** | Az eseményközponthoz közvetlen hozzáférést biztosító jogkivonatot nem szabad megadni az eszköznek. Egy legkevésbé kiemelt jogkivonat használata az eszköz, amely hozzáférést csak a közzétevő segítene azonosítani és feketelistára, ha kiderült, hogy egy engedélyezetlen vagy feltört eszköz.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Csatlakozás az Event Hubhoz a szükséges minimális engedélyekkel rendelkező SAS-kulcsokkal

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Eseményközpontok hitelesítése és biztonsági modellje – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépéseket** | Adja meg a legkevesebb jogosultsági engedélyeket a különböző háttéralkalmazások, amelyek csatlakoznak az Event Hub. Hozzon létre külön SAS-kulcsokat minden háttéralkalmazáshoz, és csak a szükséges engedélyeket adja meg – Küldés, fogadás vagy kezelés.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Erőforrás-jogkivonatok használatával, hogy csatlakozzon a Cosmos DB-hez, amikor csak lehetséges

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Egy erőforrás-jogkivonat egy Azure Cosmos DB-engedély-erőforráshoz van társítva, és rögzíti az adatbázis felhasználója és a felhasználó által egy adott Azure Cosmos DB-alkalmazás-erőforráshoz (például gyűjtemény, dokumentum) vonatkozó engedély közötti kapcsolatot. Mindig használjon erőforrás-jogkivonatot az Azure Cosmos DB eléréséhez, ha az ügyfél nem megbízható a fő- vagy írásvédett kulcsok kezelésével – például egy végfelhasználói alkalmazás, például egy mobil vagy asztali ügyfél. Használja a főkulcs vagy a csak olvasható kulcsokat a háttéralkalmazásokból, amelyek biztonságosan tárolhatják ezeket a kulcsokat.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-rbac"></a><a id="grained-rbac"></a>Részletes hozzáférés-kezelés engedélyezése az Azure-előfizetéshez az RBAC használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure megbízhatósági határa | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Lépéseket** | Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges.|

## <a name="restrict-clients-access-to-cluster-operations-using-rbac"></a><a id="cluster-rbac"></a>Az ügyfél fürtműveletekhez való hozzáférésének korlátozása az RBAC használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure |
| **Referencia**              | [Szerepköralapú hozzáférés-vezérlés Service Fabric-ügyfelek számára](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Lépéseket** | <p>Az Azure Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz kapcsolódó ügyfelek számára: a rendszergazdát és a felhasználót. A hozzáférés-vezérlés lehetővé teszi a fürtrendszergazdának, hogy korlátozza a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára, így a fürt biztonságosabbá téve azt.</p><p>A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az olvasási/írási képességeket is). A felhasználók alapértelmezés szerint csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez), és képesek az alkalmazások és szolgáltatások feloldására.</p><p>A fürt létrehozásakor a két ügyfélszerepkört (rendszergazda és ügyfél) külön tanúsítványok at adhat meg mindegyikhez.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Biztonsági modellezés végrehajtása és szükség esetén a mezőszintű biztonság használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Biztonsági modellezés végrehajtása és szükség esetén a mezőszintű biztonság használata|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>A portálfiókok biztonsági modellezésének végrehajtása annak szem előtt tartásával, hogy a portál biztonsági modellje eltér a CRM többi részétől

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM-portál | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A portálfiókok biztonsági modellezésének végrehajtása annak szem előtt tartásával, hogy a portál biztonsági modellje eltér a CRM többi részétől|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Részletes engedély megadása számos entitáshoz az Azure Table Storage-ban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType - tábla |
| **Referencia**              | [Az Azure-tárfiók objektumaihoz való hozzáférés delegálása a SAS használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Lépéseket** | Bizonyos üzleti forgatókönyvek esetén előfordulhat, hogy az Azure Table Storage-ra van szükség a különböző felek számára kielégedő bizalmas adatok tárolásához. Például a különböző országokra/régiókra vonatkozó érzékeny adatok. Ilyen esetekben a SAS-aláírások a partíció- és sorkulcs-tartományok megadásával is létrehozhatók, így a felhasználó hozzáférhet egy adott országra/régióra vonatkozó adatokhoz.| 

## <a name="enable-role-based-access-control-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése az Azure-tárfiókba az Azure Resource Manager használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A tárfiók védelme szerepköralapú hozzáférés-vezérléssel (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Lépéseket** | <p>Amikor új tárfiókot hoz létre, kiválasztegy központi telepítési modellt a Klasszikus vagy az Azure Resource Manager. Az Azure-beli erőforrások létrehozásának klasszikus modellje csak az előfizetéshez és a tárfiókhoz való összes vagy semmit hozzáférést teszi lehetővé.</p><p>Az Azure Resource Manager modell, a tárfiókot egy erőforráscsoportba helyezi, és az Azure Active Directory használatával szabályozza az adott tárfiók felügyeleti síkjához való hozzáférést. Például adott felhasználók nak adhat hozzáférést a tárfiók kulcsaihoz, míg más felhasználók megtekinthetik a tárfiók adatait, de nem férhetnek hozzá a tárfiók kulcsaihoz.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implicit jailbreak vagy gyökeresedési észlelésének megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Alkalmazás kell védenie a saját konfiguráció és a felhasználói adatok abban az esetben, ha a telefon gyökerezik, vagy jail tört. A gyökeresedés / jail törés jogosulatlan hozzáférést jelent, amelyet a normál felhasználók nem tesznek meg a saját telefonjukon. Ezért az alkalmazásnak implicit észlelési logikával kell rendelkeznie az alkalmazás indításakor, hogy észlelje, hogy a telefon gyökerezik-e.</p><p>Az észlelési logika egyszerűen hozzáférhet olyan fájlokhoz, amelyekhez általában csak a root felhasználó férhet hozzá, például:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/rendszer/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Ha az alkalmazás hozzáférhet ezekhez a fájlokhoz, azt jelzi, hogy az alkalmazás root felhasználóként fut.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Gyenge osztályhivatkozás a WCF-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Lépéseket** | <p>A rendszer gyenge osztályhivatkozást használ, amely lehetővé teheti a támadó számára, hogy jogosulatlan kódot hajtson végre. A program olyan felhasználó által definiált osztályra hivatkozik, amely nem egyedileg azonosítva van. Amikor a .NET betölti ezt a gyengén azonosított osztályt, a CLR típusú betöltő a következő helyeken keresi meg az osztályt a megadott sorrendben:</p><ol><li>Ha a típus összeállítása ismert, a betöltő megkeresi a konfigurációs fájl átirányítási helyeit, a GAC-t, az aktuális szerelvényt a konfigurációs adatok at és az alkalmazás alapkönyvtárát.</li><li>Ha a kódösszeállítás ismeretlen, a betöltő az aktuális szerelvényben, az mscorlibban és a TypeResolve eseménykezelő által visszaadott helyre keres.</li><li>Ez a CLR keresési sorrend olyan hookokkal módosítható, mint a Típustovábbítás mechanizmus és az AppDomain.TypeResolve esemény</li></ol><p>Ha a támadó kihasználja a CLR keresési sorrendjét egy másik osztály létrehozásával ugyanazzal a névvel, és egy másik helyre helyezi, amelyet a CLR először tölt be, a CLR véletlenül végrehajtja a támadó által megadott kódot</p>|

### <a name="example"></a>Példa
Az `<behaviorExtensions/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-et, hogy adjon hozzá egy egyéni viselkedési osztályt egy adott WCF-bővítményhez.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
A teljesen minősített (erős) nevek használata egyedileg azonosítja a típust, és tovább növeli a rendszer biztonságát. A gép.config és az app.config fájlban történő típusok regisztrálásakor használjon teljesen minősített kódösszeállítás-neveket.

### <a name="example"></a>Példa
Az `<behaviorExtensions/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-et, hogy erősen hivatkozott egyéni viselkedési osztályt adjon hozzá egy adott WCF-bővítményhez.
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

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF-implement hitelesítésvezérlés

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Lépéseket** | <p>Ez a szolgáltatás nem használ engedélyezési vezérlőt. Amikor egy ügyfél meghív egy adott WCF-szolgáltatást, a WCF különböző engedélyezési sémákat biztosít, amelyek ellenőrzik, hogy a hívó nak van-e engedélye a szolgáltatás metódusának végrehajtására a kiszolgálón. Ha a WCF-szolgáltatások engedélyezési vezérlői nincsenek engedélyezve, a hitelesített felhasználó jogosultságeszkalációt érhet el.</p>|

### <a name="example"></a>Példa
A következő konfiguráció arra utasítja a WCF-et, hogy a szolgáltatás végrehajtásakor ne ellenőrizze az ügyfél engedélyezési szintjét:
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
Használjon szolgáltatásengedélyezési sémát annak ellenőrzésére, hogy a szolgáltatási módszer hívója jogosult-e erre. A WCF két módot biztosít, és lehetővé teszi az egyéni engedélyezési séma meghatározását. A UseWindowsGroups mód Windows-szerepköröket és felhasználókat használ, a UseAspNetRoles mód pedig ASP.NET szerepkör-szolgáltatót, például az SQL Servert használja a hitelesítéshez.

### <a name="example"></a>Példa
A következő konfiguráció arra utasítja a WCF-et, hogy a hozzáadási szolgáltatás végrehajtása előtt győződjön meg arról, hogy az ügyfél a Rendszergazdák csoport része:
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
A szolgáltatás ezután a következőként lesz deklarálva:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Megfelelő engedélyezési mechanizmus megvalósítása ASP.NET webes API-ban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, MVC5 |
| **Attribútumok**              | N/A, identitásszolgáltató – ADFS, identitásszolgáltató – Azure AD |
| **Referencia**              | [Hitelesítés és engedélyezés ASP.NET webes API-ban](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Lépéseket** | <p>Az alkalmazás felhasználóinak szerepkör-információszármaztatható az Azure AD vagy Az ADFS-jogcímek, ha az alkalmazás támaszkodik rájuk, mint identitásszolgáltató, vagy maga az alkalmazás is megadható. Ezekben az esetekben az egyéni engedélyezési megvalósításnak ellenőriznie kell a felhasználói szerepkör adatait.</p><p>Az alkalmazás felhasználóinak szerepkör-információszármaztatható az Azure AD vagy Az ADFS-jogcímek, ha az alkalmazás támaszkodik rájuk, mint identitásszolgáltató, vagy maga az alkalmazás is megadható. Ezekben az esetekben az egyéni engedélyezési megvalósításnak ellenőriznie kell a felhasználói szerepkör adatait.</p>

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
Minden vezérlőt és cselekvési módszert, amelyet védeni kell, a fenti attribútummal kell díszítetten díszíteni.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Engedélyezési ellenőrzések végrehajtása az eszközön, ha támogatja a különböző engedélyszinteket igénylő különböző műveleteket

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az eszköznek engedélyeznie kell a hívó fél számára annak ellenőrzését, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. Például mondjuk, hogy a készülék egy intelligens ajtózár, amely a felhőből figyelhető, valamint olyan funkciókat biztosít, mint az ajtó távolról történő lezárása.</p><p>Az intelligens ajtózár csak akkor biztosít feloldási funkciót, ha valaki fizikailag az ajtó közelébe jön egy kártyával. Ebben az esetben a távoli parancs és vezérlés megvalósítását úgy kell elvégezni, hogy az ne biztosítson semmilyen funkciót az ajtó feloldásához, mivel a felhőátjáró nem jogosult az ajtó feloldására vonatkozó parancs küldésére.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Engedélyezési ellenőrzések végrehajtása a Field Gateway-ben, ha különböző engedélyszinteket igénylő különböző műveleteket támogat

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A Field Gateway-nek engedélyeznie kell a hívó számára annak ellenőrzését, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. Például különböző engedélyeket kell adni egy rendszergazdai felhasználói felülethez/API-hoz, amely a hozzá kapcsolódó helyszíni átjáró v/s eszközök konfigurálásához szükséges.|
