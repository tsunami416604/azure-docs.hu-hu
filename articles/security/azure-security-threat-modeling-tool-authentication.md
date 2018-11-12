---
title: Hitelesítés – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
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
ms.openlocfilehash: b4b6e91f9b20166f63a4710f42726bf1d4090022
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251559"
---
# <a name="security-frame-authentication--mitigations"></a>Biztonsági keret: Hitelesítés |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás**    | <ul><li>[Fontolja meg a standard hitelesítési mechanizmust webes alkalmazásba való hitelesítéséhez](#standard-authn-web-app)</li><li>[Alkalmazások biztonságos módon kell kezelnie a sikertelen hitelesítési forgatókönyvek](#handle-failed-authn)</li><li>[Enable lépés felfelé vagy adaptív hitelesítés](#step-up-adaptive-authn)</li><li>[Győződjön meg arról, hogy a felügyeleti felületek megfelelően zárolva lefelé](#admin-interface-lockdown)</li><li>[Megvalósítása biztonságosan elfelejtett jelszó funkciói](#forgot-pword-fxn)</li><li>[Győződjön meg arról, hogy a jelszó- és házirend vannak megvalósítva](#pword-account-policy)</li><li>[Alkalmazzon-vezérlést, hogy a felhasználónév-enumerálás](#controls-username-enum)</li></ul> |
| **Adatbázis** | <ul><li>[Ha lehetséges, használja a Windows-hitelesítés az SQL Serverhez való csatlakozáshoz](#win-authn-sql)</li><li>[Ha lehetséges SQL-adatbázishoz való csatlakozás használja az Azure Active Directory-hitelesítés](#aad-authn-sql)</li><li>[SQL-hitelesítési mód használata esetén ellenőrizze, hogy fiókkal és jelszóval házirend érvényben vannak-e az SQL server](#authn-account-pword)</li><li>[Ne használja a tartalmazott adatbázisok SQL-hitelesítés](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Használja az eszköz hitelesítő adatok használatával SaS-jogkivonatok száma](#authn-sas-tokens)</li></ul> |
| **Az Azure megbízhatósági kapcsolat határán** | <ul><li>[Az Azure multi-factor Authentication szolgáltatás engedélyezése az Azure-rendszergazdák](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Service Fabric-fürtön való névtelen hozzáférés korlátozása](#anon-access-cluster)</li><li>[Győződjön meg arról, hogy a Service Fabric-csomópont ügyfél tanúsítvány eltérő csomópontok tanúsítvány](#fabric-cn-nn)</li><li>[Az AAD ügyfelek számára, hogy a service fabric-fürtök hitelesítéséhez](#aad-client-fabric)</li><li>[Győződjön meg arról, hogy a service fabric-tanúsítványok vannak lekérdezve az egy elismert hitelesítésszolgáltató (CA)](#fabric-cert-ca)</li></ul> |
| **Identitáskezelési kiszolgáló** | <ul><li>[Identitáskezelési kiszolgáló támogatja a standard hitelesítési forgatókönyvek használata](#standard-authn-id)</li><li>[Bírálja felül az alapértelmezett Identitáskiszolgálók-jogkivonatok gyorsítótárának egy méretezhető megoldás](#override-token)</li></ul> |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy az üzembe helyezett alkalmazás bináris fájljainak digitális aláírással](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Hitelesítés engedélyezése, amikor csatlakozik a WCF lévő MSMQ-várólista](#msmq-queues)</li><li>[WCF-ne beállítása üzenet clientCredentialType nincs](#message-none)</li><li>[WCF-ne beállítása átviteli clientCredentialType nincs](#transport-none)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy standard hitelesítési módszerek segítségével biztonságos webes API-k](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Használja az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek](#authn-aad)</li><li>[Bírálja felül az alapértelmezett ADAL-jogkivonatok gyorsítótárának egy méretezhető megoldás](#adal-scalable)</li><li>[Győződjön meg arról, hogy az TokenReplayCache használja az ADAL-hitelesítési tokenek az ismétlés megakadályozása](#tokenreplaycache-adal)</li><li>[ADAL könyvtárakat használata kezelheti a jogkivonat-kérelmeket OAuth2-ügyfelekről az aad-be (vagy a helyszíni AD)](#adal-oauth2)</li></ul> |
| **IoT helyszíni átjáró** | <ul><li>[Eszközök csatlakoztatása a helyszíni átjáró hitelesítése](#authn-devices-field)</li></ul> |
| **IoT átjáró** | <ul><li>[Győződjön meg arról, hogy a felhő-átjáróhoz való csatlakozáskor eszközök hitelesítés](#authn-devices-cloud)</li><li>[Eszközönkénti hitelesítés hitelesítő adatok használata](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy csak a szükséges tárolók és blobok névtelen olvasási hozzáférés megadva](#req-containers-anon)</li><li>[Korlátozott hozzáférést biztosít az Azure storage-, SAS- vagy az SAP-objektumokhoz](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Fontolja meg a standard hitelesítési mechanizmust webes alkalmazásba való hitelesítéséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Hitelesítés az a folyamat, ha egy entitás igazolja a identitását, általában hitelesítő adatokat, például egy felhasználónév és jelszó használatával. Nincsenek több hitelesítési protokollok érhető el lehet tekinteni. Ezek közül néhányat az alábbiakban olvashatók:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlap alapú</li><li>Összevonás - ADFS</li><li>Összevonási – Azure ad-ben</li><li>Összevonás - Identitáskiszolgálók</li></ul><p>Fontolja meg a standard hitelesítési mechanizmust forrás folyamat azonosítása</p>|

## <a id="handle-failed-authn"></a>Alkalmazások biztonságos módon kell kezelnie a sikertelen hitelesítési forgatókönyvek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Az alkalmazásokat, amelyek kifejezetten a felhasználók hitelesítése sikertelen hitelesítési forgatókönyvek biztonságos módon kell kezelnie. A hitelesítési mechanizmust kell:</p><ul><li>Privilegizált erőforrásokhoz való hozzáférés megtagadása, amikor a hitelesítés sikertelen</li><li>Egy általános hibaüzenetet jelenít meg a sikertelen hitelesítés után, és akkor fordul elő, a hozzáférés megtagadva</li></ul><p>Teszt:</p><ul><li>Sikertelen bejelentkezés után az emelt szintű erőforrások védelme</li><li>Általános hibaüzenet jelenik meg a sikertelen hitelesítés és hozzáférés-Megtagadás esemény</li><li>Fiókok le vannak tiltva, hogy túl sok sikertelen bejelentkezési kísérletek után</li><ul>|

## <a id="step-up-adaptive-authn"></a>Enable lépés felfelé vagy adaptív hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Ellenőrizze az alkalmazás további engedélyezési (például fokozzák vagy adaptív hitelesítést, például az egyszeri jelszó küldése SMS-, e-mail stb. vagy újbóli hitelesítést kér a multi-factor authentication) rendelkezik, a felhasználó próbálnak futtatni rajta előtt megkapják a hozzáférést bizalmas adatokat. Ez a szabály is vonatkozik, kritikus fontosságú változtatásokat egy fiók vagy a művelet</p><p>Ez azt is jelenti, hogy a betanítás hitelesítési oly módon, hogy az alkalmazás megfelelően előírja nem lehetővé teszi a jogosulatlan adatkezelési révén a példában a környezetfüggő engedélyezési végrehajtandó rendelkezik-e illetéktelen paraméter</p>|

## <a id="admin-interface-lockdown"></a>Győződjön meg arról, hogy a felügyeleti felületek megfelelően zárolva lefelé

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | Az első megoldás, ha csak egy bizonyos forrás IP-tartomány az a rendszergazdai felületen való hozzáférést biztosít. Ha ezt a megoldást nem lehet, mint minden esetben javasoljuk, hogy jelentkezik be a rendszergazdai felületen step-up vagy adaptív hitelesítési kényszerítése |

## <a id="forgot-pword-fxn"></a>Megvalósítása biztonságosan elfelejtett jelszó funkciói

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Először is, ellenőrizze, hogy elfelejtette a jelszavát, és más helyreállítási elérési utak beleértve magát a jelszó helyett egy időben korlátozott aktiválási token hivatkozást is küldhet. További hitelesítés helyreállítható-jogkivonatok (például SMS token, natív mobilalkalmazások stb.) alapján lehet szükséges, valamint a hivatkozás keresztül elküldése előtt. Második, meg kell nem a felhasználói fiók zárolása miközben folyamatban van egy új jelszót első folyamatát.</p><p>Ez vezethet egy szolgáltatásmegtagadási támadást, amikor egy támadó úgy dönt, hogy a felhasználók számára a automatizált támadás szándékosan zárolására. Harmadik, amikor új jelszó kérésére folyamatban van beállítva, az üzenet megjelenítheti, hogy érdemes általánosítani felhasználónév enumerálás elkerülése érdekében. Negyedik mindig tiltsa le a régi jelszót és a egy erős jelszót a házirend megvalósítása.</p> |

## <a id="pword-account-policy"></a>Győződjön meg arról, hogy a jelszó- és házirend vannak megvalósítva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Jelszó- és házirend megfelelnek-e a szervezeti házirendnek és az ajánlott eljárásokat kell végrehajtani.</p><p>A elleni találgatásos és a találgatás alapú szótár: erős jelszót kell valósítja meg, győződjön meg arról, hogy a felhasználók létre összetett jelszót (például minimális hosszának 12 karakter hosszú, alfanumerikus vagy speciális karaktereket).</p><p>Fiókzárolási házirendeket is kell végrehajtani a következő módon:</p><ul><li>**Helyreállítható zárolási felskálázás:** jó választás a felhasználók a találgatásos támadások elleni védelme is lehet. Például, amikor a felhasználó helytelen jelszót ad az alkalmazás három alkalommal tudta zárolni a fiók egy percet ahhoz, hogy le a kevésbé nyereséges folytatja a támadó így jelszó kényszerítése találgatásos folyamata lassú. Ha ebben a példában, akkor merevlemez zárolása kibővített ellenintézkedések megvalósításához "DOS" véglegesen a fiókok zárolásának szerint. Azt is megteheti, alkalmazás készítése az OTP (egy idő jelszó) és küldje el a sávon kívüli (e-mailben, sms stb.) a felhasználó számára. Egy másik módszer a CAPTCHA megvalósításához, a sikertelen bejelentkezési kísérletek számát küszöbérték elérése után lehet.</li><li>**Merevlemez zárolása felskálázás:** fiókzárolási az ilyen típusú kell alkalmazni, amikor egy felhasználó az alkalmazás intézményt észlelése és a számláló neki ügyfélügynökkel véglegesen fiókját zárolásának mindaddig, amíg egy válasz csapat időm azok a vizsgálati tegye. Eldöntheti, hogy a felhasználó ezen folyamat után vissza a fiókját, vagy további ellene jogi műveletek végrehajtása. Az ilyen megközelítés megakadályozza, hogy a támadó további túljutó az alkalmazás és -infrastruktúra.</li></ul><p>Az alapértelmezett és kiszámítható fiókok ellen indított támadások elleni védelemre, győződjön meg arról, hogy az összes kulcsait és jelszavait cserélhető, és létrehozott vagy a telepítés után cseréje.</p><p>Ha az alkalmazás automatikus létrehozása jelszavak, győződjön meg arról, hogy a létrehozott jelszavakat véletlenszerű és magas vysokou.</p>|

## <a id="controls-username-enum"></a>Alkalmazzon-vezérlést, hogy a felhasználónév-enumerálás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Az összes hibaüzenet felhasználónév enumerálás elkerülése érdekében érdemes általánosítani. Emellett néha nem kerülheti el kiszivárgását funkciókat, például a regisztrációs oldalon található információkat. Itt kell használnia a sebességhatárolt módszerek, például a CAPTCHA, hogy egy támadó egy automatizált támadás. |

## <a id="win-authn-sql"></a>Ha lehetséges, használja a Windows-hitelesítés az SQL Serverhez való csatlakozáshoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Rendszert |
| **Attribútumok**              | SQL-verzió - minden |
| **Hivatkozások**              | [Az SQL Server - hitelesítési mód kiválasztása](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Lépések** | Windows-hitelesítés Kerberos biztonsági protokollt használ, az erős jelszavak bonyolultságát érvényesítési kapcsolatban jelszó házirendek kikényszerítését biztosítja, támogatja a fiók zárolása és támogatja a jelszó lejárati idejét.|

## <a id="aad-authn-sql"></a>Ha lehetséges SQL-adatbázishoz való csatlakozás használja az Azure Active Directory-hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Csatlakozás az SQL Database az Azure Active Directory-hitelesítés használatával](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Lépések** | **Minimális verzió:** Azure SQL Database V12 szükséges, hogy az Azure SQL Database a Microsoft Directory ellen AAD-hitelesítés használatára |

## <a id="authn-account-pword"></a>SQL-hitelesítési mód használata esetén ellenőrizze, hogy fiókkal és jelszóval házirend érvényben vannak-e az SQL server

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az SQL Server jelszóházirend](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Lépések** | SQL Server-hitelesítés használatakor az SQL Server, a Windows felhasználói fiókok nem alapuló bejelentkezések jönnek létre. Az SQL-kiszolgáló által létrehozott és az SQL Serverben tárolt mind a felhasználónevet és jelszót. Az SQL Server Windows jelszó házirend mechanizmusok használhatja. Azonos összetettségét és használt Windows SQL Server-en belül használt jelszavak jelszóelévülési házirendek alkalmazhatja. |

## <a id="autn-contained-db"></a>Ne használja a tartalmazott adatbázisok SQL-hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Rendszert, az SQL Azure |
| **Attribútumok**              | SQL-12-es verzió - MSSQL2012, SQL-verzió- |
| **Hivatkozások**              | [Ajánlott biztonsági eljárások a tartalmazott adatbázisok](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Lépések** | Az érvényben levő jelszóházirend hiányában egy tartalmazott adatbázisban éppen létrehozott gyenge hitelesítő adatot valószínűségét növelhető. Használja ki a Windows-hitelesítés. |

## <a id="authn-sas-tokens"></a>Használja az eszköz hitelesítő adatok használatával SaS-jogkivonatok száma

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | <p>Az Event Hubs biztonsági modellt kombinációját közös hozzáférésű Jogosultságkód (SAS) jogkivonatokat és az esemény-közzétevők alapul. A közzétevő neve, amely megkapja a jogkivonatot a DeviceID jelöli. Ez segíthet a tokeneket a megfelelő eszközökkel létrehozott társítani.</p><p>Összes üzenet lehetővé teszi a kísérletek hamisítást hasznos a származási észlelési szolgáltatás oldalán kezdeményezőnek megfelelő címkékkel. Eszközök hitelesítéséhez, hozzon létre egy eszköz SaS-jogkivonat hatóköre egy egyedi közzétevő száma.</p>|

## <a id="multi-factor-azure-admin"></a>Az Azure multi-factor Authentication szolgáltatás engedélyezése az Azure-rendszergazdák

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Mi az az Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Lépések** | <p>Többtényezős hitelesítés (MFA), amely egynél több ellenőrzési módszert igényel, és a egy kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési módszer. Azzal, hogy bármely két vagy több, a következő ellenőrzési módszerek sorát működik:</p><ul><li>Valami tudja (általában jelszót)</li><li>Hiba (megbízható eszközzel rendelkezik, amely nem könnyen lettek duplikálva, például telefon)</li><li>Hiba (biometrikus adatok) áll</li><ul>|

## <a id="anon-access-cluster"></a>Service Fabric-fürtön való névtelen hozzáférés korlátozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure  |
| **Hivatkozások**              | [Service Fabric-fürtök biztonsági forgatókönyveit](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Lépések** | <p>Fürtök mindig kell biztosítani, hogy megakadályozza a jogosulatlan felhasználók csatlakozzanak a fürthöz, különösen akkor, ha fut rajta éles üzemi van.</p><p>Service fabric-fürt létrehozásakor győződjön meg arról, hogy a biztonsági mód értéke "biztonságos", és konfigurálja a szükséges X.509-tanúsítvány. Egy "nem biztonságos" fürt létrehozása lehetővé teszi bármely névtelen felhasználóként szeretne csatlakozni, vagy ha végpontoknak a nyilvános interneten teszi elérhetővé.</p>|

## <a id="fabric-cn-nn"></a>Győződjön meg arról, hogy a Service Fabric-csomópont ügyfél tanúsítvány eltérő csomópontok tanúsítvány

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Önálló – Azure, környezet – környezet |
| **Hivatkozások**              | [A Service Fabric-csomópont ügyfél tanúsítvány biztonsági](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [ügyféltanúsítvány használatával egy biztonságos fürthöz való csatlakozás](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Lépések** | <p>Ügyfél-csomópont tanúsítvány biztonság az Azure Portalon, Resource Manager-sablonokkal, vagy adjon meg egy rendszergazdai ügyfél tanúsítványa és/vagy felhasználói ügyféltanúsítványt egy különálló JSON-sablon vagy a fürt létrehozásakor van konfigurálva.</p><p>Rendszergazdai ügyfél és a felhasználói ügyféltanúsítványok megadott eltér a megadott csomópont és csomópont közötti biztonsághoz elsődleges és másodlagos tanúsítványának kell lennie.</p>|

## <a id="aad-client-fabric"></a>Az AAD ügyfelek számára, hogy a service fabric-fürtök hitelesítéséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [Fürtök – biztonsági helyzetek – a biztonsági javaslatok](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Lépések** | Az Azure-ban futó fürtök használatával az Azure Active Directory (AAD), kivéve az ügyféltanúsítványokat a felügyeleti-végpontokhoz való hozzáférés is gondoskodhat. Az Azure-fürtök esetén ajánlott AAD biztonsági használhat az ügyfelek és a csomópontok közötti biztonsági tanúsítványok hitelesítéséhez.|

## <a id="fabric-cert-ca"></a>Győződjön meg arról, hogy a service fabric-tanúsítványok vannak lekérdezve az egy elismert hitelesítésszolgáltató (CA)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [X.509-tanúsítványokat és a Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Lépések** | <p>A Service Fabric X.509 kiszolgálói tanúsítványokat használ a csomópontok és az ügyfelek hitelesítéséhez.</p><p>Fontos szempontokat kell figyelembe venni szolgáltatás hálók tanúsítványok használatával:</p><ul><li>Éles fürtökben használt tanúsítványok legyen egy megfelelően konfigurált szolgáltatással a Windows Server tanúsítvány létrehozása vagy a egy elismert hitelesítésszolgáltató (CA) kapott. A hitelesítésszolgáltató egy jóváhagyott külső hitelesítésszolgáltató vagy egy megfelelő felügyelt belső nyilvános kulcsok infrastruktúrája (PKI) lehet.</li><li>Soha ne használja a bármely ideiglenes vagy tanúsítványok tesztelése az éles környezetben, eszközök, például a MakeCert.exe használatával létrehozott</li><li>Használhat önaláírt tanúsítványt, de csak akkor tegye ezt tesztfürtök esetében, és nem éles környezetben</li></ul>|

## <a id="standard-authn-id"></a>Identitáskezelési kiszolgáló támogatja a standard hitelesítési forgatókönyvek használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 - átfogó kép](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Lépések** | <p>Az alábbiakban tipikus kapcsolati identitás-kiszolgáló által támogatott:</p><ul><li>Webes alkalmazások kommunikálni böngészők</li><li>Webes alkalmazások kommunikálnak a webes API-k (néha a saját, néha egy felhasználó nevében)</li><li>Webes API-k kommunikálni böngészőalapú alkalmazások</li><li>Natív alkalmazások kommunikálnak a webes API-k</li><li>Server-alapú alkalmazások kommunikálnak a webes API-k</li><li>Webes API-k kommunikálni a webes API-k (néha a saját, néha egy felhasználó nevében)</li></ul>|

## <a id="override-token"></a>Bírálja felül az alapértelmezett Identitáskiszolgálók-jogkivonatok gyorsítótárának egy méretezhető megoldás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Identitáskezelési kiszolgáló üzembe helyezése – gyorsítótár](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | <p>IdentityServer rendelkezik egy egyszerű beépített memórián belüli gyorsítótárat. Bár ez kis léptékű natív alkalmazások helyes, azt nem méretezhető mid szint és a háttérkiszolgáló alkalmazások a következő okok miatt:</p><ul><li>Ezek az alkalmazások által elért számos felhasználó egyszerre. Minden hozzáférési jogkivonatok mentése ugyanabban a tárolóban hoz létre az elkülönítési problémák és kihívásokat jelent, ha nagy mennyiségű: sok annyi jogkivonatok az alkalmazás hozzáfér a felhasználók nevében, mint a felhasználó is jelenti azt, hogy hatalmas számok és rendkívül drága lenne keresési műveletek</li><li>Ezek az alkalmazások általában telepítve vannak, elosztott topológia, ahol több csomópont hozzáféréssel kell rendelkeznie az azonos gyorsítótár</li><li>Gyorsítótárazott jogkivonatok folyamata újrahasznosítást és deactivations kell túlélni.</li><li>Az összes fenti okokból implementing web apps szolgáltatásban, miközben javasoljuk, hogy egy méretezhető megoldás, például az Azure Redis cache-jogkivonatok gyorsítótárának az alapértelmezett Identitáskiszolgálók felülbírálása</li></ul>|

## <a id="binaries-signed"></a>Győződjön meg arról, hogy az üzembe helyezett alkalmazás bináris fájljainak digitális aláírással

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy üzembe helyezett alkalmazás bináris fájljainak digitális aláírással, hogy a bináris fájlok sértetlenségének ellenőrzéséhez|

## <a id="msmq-queues"></a>Hitelesítés engedélyezése, amikor csatlakozik a WCF lévő MSMQ-várólista

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | – |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Lépések** | Program sikertelen hitelesítés engedélyezése, amikor csatlakozik az MSMQ-várólista, a támadó névtelenül küldhet üzeneteket az üzenetsorba a feldolgozáshoz. Ha szeretne csatlakozni egy MSMQ-várólista kézbesíti az üzenetet egy másik program használja nem használatos a hitelesítés, a támadó egy névtelen üzenet, amely rosszindulatú sikerült elküldeni.|

### <a name="example"></a>Példa
A `<netMsmqBinding/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF-hitelesítés letiltása, az üzenetek kézbesítését az MSMQ-várólista való csatlakozáskor.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurálja a szükséges Windows-tartomány vagy a Tanúsítványalapú hitelesítés mindig minden bejövő vagy kimenő üzenetek az MSMQ.

### <a name="example"></a>Példa
A `<netMsmqBinding/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF Tanúsítványalapú hitelesítés engedélyezése az MSMQ-várólista való csatlakozáskor. Az ügyfél hitelesítése X.509 tanúsítványok használatával. Az ügyfél-tanúsítványt a tanúsítványtárolóban, a kiszolgáló jelen kell lennie.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF-ne beállítása üzenet clientCredentialType nincs

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél-hitelesítő adat típusa – None |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Lépések** | Érhető el a hitelesítés azt jelenti, hogy mindenki érhetik el a szolgáltatást. Egy szolgáltatás, amely az ügyfelek nem hitelesíti a hozzáférést minden felhasználó számára lehetővé teszi. Állítsa be az alkalmazás ügyfél-hitelesítő adatok hitelesítése. Ezt megteheti úgy, hogy a Windows vagy a tanúsítvány az üzenet clientCredentialType. |

### <a name="example"></a>Példa
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF-ne beállítása átviteli clientCredentialType nincs

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél-hitelesítő adat típusa – None |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg](https://vulncat.hpefod.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Lépések** | Érhető el a hitelesítés azt jelenti, hogy mindenki érhetik el a szolgáltatást. Egy szolgáltatás, amely nem hitelesíti az ügyfeleket lehetővé teszi, hogy minden felhasználó számára a működését. Állítsa be az alkalmazás ügyfél-hitelesítő adatok hitelesítése. Ezt megteheti úgy, hogy a Windows vagy a tanúsítvány az átviteli clientCredentialType. |

### <a name="example"></a>Példa
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Győződjön meg arról, hogy standard hitelesítési módszerek segítségével biztonságos webes API-k

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Hitelesítés és engedélyezés az ASP.NET webes API-k](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [külső hitelesítési szolgáltatások az ASP.NET webes API-k (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Lépések** | <p>Hitelesítés az a folyamat, ha egy entitás igazolja a identitását, általában hitelesítő adatokat, például egy felhasználónév és jelszó használatával. Nincsenek több hitelesítési protokollok érhető el lehet tekinteni. Ezek közül néhányat az alábbiakban olvashatók:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlap alapú</li><li>Összevonás - ADFS</li><li>Összevonási – Azure ad-ben</li><li>Összevonás - Identitáskiszolgálók</li></ul><p>A hivatkozások szakaszban található hivatkozások hogyan a hitelesítési sémákat mindegyike kialakítható egy webes API biztonságossá tétele a adja meg a részleteket.</p>|

## <a id="authn-aad"></a>Használja az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Hitelesítési forgatókönyvek az Azure ad-ben](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory-Kódminták](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory fejlesztői útmutatója](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Lépések** | <p>Az Azure Active Directory (Azure AD-) hitelesítés fejlesztőknek leegyszerűsíti identitás biztosítása szolgáltatásként az szabványos protokollok, mint például az OAuth 2.0 és OpenID Connect-támogatással. Az alábbiakban az Azure AD által támogatott öt elsődleges alkalmazási:</p><ul><li>Webes alkalmazás a böngészőben: egy felhasználónak van szüksége egy Azure AD által védett webes alkalmazásba való bejelentkezés</li><li>Egyetlen lap alkalmazás (SPA): A felhasználó nem jelentkezzen be egy egyoldalas alkalmazás Azure AD által védett</li><li>Natív alkalmazás webes API-nak: egy natív alkalmazás egy telefonon, táblagépen vagy számítógépen futó kell hitelesíteni a felhasználót, hogy a webes API-k az Azure AD által védett erőforrások beolvasása</li><li>Webes alkalmazás webes API-nak: webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API</li><li>Démon vagy webes API-nak a kiszolgálói alkalmazás: A démon alkalmazások vagy kiszolgálói alkalmazás webes felhasználói felület nélkül kell-erőforrásokat az Azure AD által biztonságossá tett webes API</li></ul><p>Tekintse meg a hivatkozások a hivatkozások szakaszban az alacsony szintű megvalósítási részletei</p>|

## <a id="adal-scalable"></a>Bírálja felül az alapértelmezett ADAL-jogkivonatok gyorsítótárának egy méretezhető megoldás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A webes alkalmazásokhoz az Azure Active Directoryval modern hitelesítést](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [használatával a Redis ADAL-jogkivonatok gyorsítótárának használata](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Lépések** | <p>Az alapértelmezett gyorsítótár, amely adal-t (Active Directory Authentication Library) használ a memórián belüli gyorsítótár, amely egy tárolóból, rendelkezésre álló teljes folyamat támaszkodik. Ez a natív alkalmazások esetében működik, amíg azt nem méretezhető mid szint és a háttérkiszolgáló alkalmazások a következő okok miatt:</p><ul><li>Ezek az alkalmazások által elért számos felhasználó egyszerre. Minden hozzáférési jogkivonatok mentése ugyanabban a tárolóban hoz létre az elkülönítési problémák és kihívásokat jelent, ha nagy mennyiségű: sok annyi jogkivonatok az alkalmazás hozzáfér a felhasználók nevében, mint a felhasználó is jelenti azt, hogy hatalmas számok és rendkívül drága lenne keresési műveletek</li><li>Ezek az alkalmazások általában telepítve vannak, elosztott topológia, ahol több csomópont hozzáféréssel kell rendelkeznie az azonos gyorsítótár</li><li>Gyorsítótárazott jogkivonatok folyamata újrahasznosítást és deactivations kell túlélni.</li></ul><p>Az összes fenti okokból miközben implementing web apps szolgáltatásban, ajánlott felülbírálhatja az alapértelmezett ADAL-jogkivonatok gyorsítótárának egy méretezhető megoldás, például az Azure Redis cache.</p>|

## <a id="tokenreplaycache-adal"></a>Győződjön meg arról, hogy az TokenReplayCache használja az ADAL-hitelesítési tokenek az ismétlés megakadályozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Modern hitelesítés az Azure Active Directory webes alkalmazásokhoz](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Lépések** | <p>A TokenReplayCache tulajdonság lehetővé teszi a fejlesztők számára adja meg a hitelesítési gyorsítótár, annak ellenőrzése, hogy nem jogkivonat egynél többször is használható céljából jogkivonatok mentéséhez használható egy tárolóban.</p><p>Ez a mérték a gyakori támadások, a szolgáltatási nevű ismétlésének támadás ellen: küldje el az alkalmazást újra megpróbálhatja egy támadó elfogja a rendszer a következő bejelentkezési jogkivonat ("visszajátszani",) új munkamenetet. Például a OIDC kód-megadási folyamatában, a felhasználó sikeres hitelesítés után, irányuló kérelem "/ bejelentkezési-oidc" végpont a függő entitás jön létre a "id_token", "code" és "state" paraméterek.</p><p>A függő entitás ellenőrzi ezt a kérelmet, és létrehozza egy új munkamenetet. Ha egy támadó rögzíti ezt a kérelmet, és azt visszajátssza, hallgatója sikeres munkamenetet létrehozni, és a felhasználó agresszívebb. Az egyszeri az OpenID Connect jelenléte is korlátozza, de nincs teljes egészében megszüntetheti a körülmények, amelyben a támadás is lehet sikeresen gyakorlatokkal. Azok az alkalmazások védelme érdekében fejlesztők ITokenReplayCache megvalósítását adja meg, és egy példányt rendel TokenReplayCache.</p>|

### <a name="example"></a>Példa
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Példa
Íme egy minta megvalósítását a ITokenReplayCache felületen. (Adja testreszabása és a project-specifikus gyorsítótárazási keretrendszer megvalósítása)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
A megvalósított gyorsítótárában lehet hivatkozni a következő OIDC beállításai "TokenValidationParameters" tulajdonságon keresztül.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Vegye figyelembe, hogy ezt a konfigurációt, jelentkezzen be a helyi OIDC által védett alkalmazás hatékonyságát, és rögzítheti a kérést `"/signin-oidc"` végpont a fiddler esetében. Ha a védelem nem teljesülnek, a kérelem a fiddlerben visszajátszásával állítja be egy új munkamenetcookie-t. Ha a kérelem van játssza vissza, miután felvette a TokenReplayCache védelmet, az alkalmazás kivételt ad a következő: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>ADAL könyvtárakat használata kezelheti a jogkivonat-kérelmeket OAuth2-ügyfelekről az aad-be (vagy a helyszíni AD)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Lépések** | <p>Az Azure AD authentication Library (ADAL) lehetővé teszi az alkalmazásfejlesztők ügyfél, könnyedén hitelesítheti a felhasználókat a felhőalapú vagy helyszíni Active Directory (AD), és ezután szerezze be a hozzáférési jogkivonatok az API-hívások biztonságossá tételéhez.</p><p>Adal-t számos funkcióval rendelkezik, hogy ellenőrizze a hitelesítés egyszerűbb a fejlesztők, például az aszinkron támogatást, konfigurálható token gyorsítótár, amely tárolja a hozzáférési jogkivonatok és frissítési biztonsági jogkivonat, automatikus jogkivonat-frissítések során a hozzáférési jogkivonat lejár, és a egy frissítési jogkivonat érhető el, és További.</p><p>Kezeli a legtöbb összetettségét, adal-t üzleti logikát az alkalmazás fejlesztői hangsúlyt segítségével, és könnyedén anélkül, hogy a biztonsági szakértői az erőforrások védelme. Külön kódtárak .NET, JavaScript (ügyfél- és Node.js), iOS, Android- és Java esetében érhetők el.</p>|

## <a id="authn-devices-field"></a>Eszközök csatlakoztatása a helyszíni átjáró hitelesítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy egyes eszközök által hitelesített a helyszíni átjáró ezekből származó adatokat fogadja és előtt a Felhőátjáró felsőbb rétegbeli kommunikáció megkönnyítése. Emellett győződjön meg arról, hogy az eszközöknek csatlakozniuk egy eszközönként hitelesítőadat-úgy, hogy az egyes eszközökön egyedileg azonosítható.|

## <a id="authn-devices-cloud"></a>Győződjön meg arról, hogy a felhő-átjáróhoz való csatlakozáskor eszközök hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, C#, Node.JS,  |
| **Attribútumok**              | Nem releváns, átjáró kiválasztása – Azure IoT Hub |
| **Hivatkozások**              | Nem releváns, [Azure IoT hubba a .NET-tel](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [egyidejűleg az IoT hub első lépések és Node JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [SAS és tanúsítványok védelmét biztosító IoT](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git-tárház](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Lépések** | <ul><li>**Általános:** hitelesíteni az eszközt a Transport Layer Security (TLS) vagy az IPSec használatával. Infrastruktúra támogatnia kell a előre megosztott kulcs (PSK) használatával, amely nem tudja kezelni a teljes aszimmetrikus titkosítási ezeken az eszközökön. Mindemellett az Azure AD, az Oauth.</li><li>**C#:** alapértelmezés szerint egy DeviceClient példány létrehozásakor a létrehozás módszer létrehoz egy DeviceClient-példányt, amely az AMQP protokollt használja az IoT Hub való kommunikációra. A HTTPS protokoll használatához használja a felülbírálást, a létrehozás metódus túlterhelését, amely lehetővé teszi a protokoll megadását. Ha a HTTPS protokollt használja, hozzá kell a `Microsoft.AspNet.WebApi.Client` NuGet-csomagot a projekthez, hogy tartalmazza a `System.Net.Http.Formatting` névtér.</li></ul>|

### <a name="example"></a>Példa
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Példa
**Node.JS: hitelesítés**
#### <a name="symmetric-key"></a>Szimmetrikus kulcs
* Egy IoT hub létrehozása az Azure-ban
* Hozzon létre egy bejegyzést az eszközidentitás-jegyzékben lévő
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Szimulált eszköz létrehozása
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>SAS-azonosító
* Lekérdezi a szimmetrikus kulcs de használata esetén belsőleg generáltak hoz létre, és explicit módon is használhatják
* Adja meg a protokoll: `var Http = require('azure-iot-device-http').Http;`
* Hozzon létre egy sas-token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Csatlakozás a sas-jogkivonat használatával: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Tanúsítványok
* Hozzon létre egy saját aláírt X509 tanúsítványok bármilyen eszközre, például a OpenSSL használatával egy .cert és .key fájlokat rendre tárolja a tanúsítvány és a kulcs létrehozásához
* Tanúsítványok használatával biztonságos kapcsolatot fogadó-eszköz kiépítése.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Csatlakoztasson egy eszközt egy tanúsítvány használatával
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Eszközönkénti hitelesítés hitelesítő adatok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró  | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Átjáró kiválasztása – Azure IoT Hub |
| **Hivatkozások**              | [Az Azure IoT Hub biztonsági tokenek](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Lépések** | Eszköz hitelesítő adatok használatával SaS-tokeneket kiszolgálónként használatát eszközkulcs vagy ügyféltanúsítvány-alapú, IoT Hub-szintű helyett megosztott hozzáférési házirendek. Ez megakadályozza, hogy a hitelesítési tokenek egy eszköz vagy a mező átjáró ismételt egy másik |

## <a id="req-containers-anon"></a>Győződjön meg arról, hogy csak a szükséges tárolók és blobok névtelen olvasási hozzáférés megadva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [közös hozzáférési aláírások, 1. rész: a SAS-modell ismertetése](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Lépések** | <p>Alapértelmezés szerint egy tárolót, és minden benne lévő blobok érhetik el csak a tárfiók tulajdonosa. Adjon meg egy tárolóhoz és annak blobjaihoz olvassa el a névtelen felhasználók, az egyik állítja be a tároló nyilvános hozzáférésének engedélyezése engedélyekkel. Névtelen felhasználók olvashatják a nyilvánosan elérhető tárolóban található blobok a kérelem hitelesítése nélkül.</p><p>Tárolók biztosítják a tároló hozzáférésének kezeléséhez a következő beállításokat:</p><ul><li>Teljes nyilvános olvasási hozzáférés: névtelen kérelem használatával tároló és blobnév adatok olvashatók. Ügyfelek névtelen kérelem használatával a tárolóban található blobok enumerálása, de nem sikerült felsorolni a tárfiókban lévő tárolókat.</li><li>Nyilvános olvasási hozzáférés csak blobok: Ebben a tárolóban lévő Blobok adatai olvashatók névtelen kérelem használatával, de adatokat tároló nem érhető el. Ügyfelek névtelen kérelem használatával a tárolóban lévő blobok nem sikerült felsorolni.</li><li>Nincs nyilvános olvasási hozzáférés: tároló és blobnév adatok csak a fióktulajdonos által olvasható</li></ul><p>Névtelen hozzáférés olyan forgatókönyvek, ahol bizonyos blobok mindig elérhetőknek kell lenniük a névtelen olvasási hozzáférés esetén ajánlott. Vezérlő részletesebben egy közös hozzáférésű jogosultságkódot, amely lehetővé teszi a különböző engedélyekkel korlátozott delegált hozzáférés és a egy adott idő alatt hozhat létre. Győződjön meg arról, hogy a tárolókhoz és blobokhoz, amely potenciálisan esetleg bizalmas adatokat tartalmazó, nem kapnak névtelen hozzáférés véletlenül</p>|

## <a id="limited-access-sas"></a>Korlátozott hozzáférést biztosít az Azure storage-, SAS- vagy az SAP-objektumokhoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozások**              | [Közös hozzáférésű Jogosultságkódok, 1. rész: A SAS-modell ismertetése](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [közös hozzáférési aláírások, a 2. rész: hozzon létre, és használhatja az SAS-Blob storage-](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [hogyan delegálása közös használatával-fiókjában található objektumok elérése Közös hozzáférésű Jogosultságkód és a tárolt hozzáférési szabályzatok](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Lépések** | <p>Közös hozzáférésű jogosultságkód (SAS) használata hatékonyan a tárfiókban lévő objektumokra korlátozott hozzáférés biztosítása más ügyfelek számára elérhetővé hívóbetűje nélkül. A SAS URI, amely magában foglalja a lekérdezési paramétereket lévő az összes szükséges információt a tárolási erőforrásokhoz való hozzáférést hitelesített. A SAS-tároló-erőforrások eléréséhez, az ügyfélnek csak kell megadni a megfelelő konstruktor vagy metódus az SAS.</p><p>SAS is használhatja, ha lehetővé szeretné tenni a tárfiókban lévő erőforrásokhoz való hozzáférést egy ügyfélre, mely nem megbízható és a fiókkulcsot. A tárfiók kulcsait is, amelyek hozzáférést rendszergazdai fiókja és az összes erőforrás is az elsődleges és másodlagos kulcsot tartalmaznak. A fiók annak lehetőségét, hogy a rosszindulatú vagy hanyagságból használata adatokhoz hozzáférést biztosító fiók kulcsok egyikét nyílik meg. Közös hozzáférésű jogosultságkódok adjon meg egy biztonságos megoldás, amely lehetővé teszi más ügyfelek számára olvasási, írási és törlési megfelelően a jogosultságaitól és a fiókkulcshoz nélkül a tárfiókban lévő adatokat.</p><p>Ha rendelkezik egy olyan logikai készlete, amelyek hasonló minden alkalommal, amikor paraméterek, egy tárolt hozzáférési szabályzat (SAP) használata jobb képet. Mivel egy tárolt hozzáférési szabályzat származó SAS használatával lehetővé teszi, hogy a SAS azonnal visszavonása, az ajánlott mindig használja a tárolt hozzáférési szabályzatok lehetőség.</p>|