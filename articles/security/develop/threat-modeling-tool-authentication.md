---
title: Hitelesítés – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: a Threat Modeling Toolban elérhető fenyegetések enyhítése
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
ms.openlocfilehash: d7fb9fadcfac0b57fb98dc54f40ff0d2f07c6827
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728394"
---
# <a name="security-frame-authentication--mitigations"></a>Biztonsági keret: Hitelesítés | Enyhítését 

| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás**    | <ul><li>[A webalkalmazások hitelesítéséhez használjon szabványos hitelesítési mechanizmust](#standard-authn-web-app)</li><li>[Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket](#handle-failed-authn)</li><li>[Lépésenkénti vagy adaptív hitelesítés engedélyezése](#step-up-adaptive-authn)</li><li>[Győződjön meg arról, hogy a felügyeleti felületek megfelelően vannak zárolva](#admin-interface-lockdown)</li><li>[Elfelejtett jelszó funkcióinak biztonságos implementálása](#forgot-pword-fxn)</li><li>[Győződjön meg arról, hogy a jelszó és a fiók házirendje implementálva van](#pword-account-policy)</li><li>[Vezérlőelemek implementálása a Felhasználónév enumerálásának megakadályozása érdekében](#controls-username-enum)</li></ul> |
| **Adatbázis** | <ul><li>[Ha lehetséges, a Windows-hitelesítés használatával csatlakozzon SQL Server](#win-authn-sql)</li><li>[Ha lehetséges, Azure Active Directory hitelesítést használ SQL Databasehoz való csatlakozáshoz](#aad-authn-sql)</li><li>[SQL-hitelesítési mód használata esetén győződjön meg arról, hogy a fiók és a jelszó házirendje kényszerítve van az SQL Serveren.](#authn-account-pword)</li><li>[SQL-hitelesítés használata a foglalt adatbázisokban](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Eszközönkénti hitelesítő adatok használata SaS-tokenek használatával](#authn-sas-tokens)</li></ul> |
| **Azure-beli megbízhatósági kapcsolat határa** | <ul><li>[Az Azure multi-Factor Authentication engedélyezése az Azure-rendszergazdák számára](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határa** | <ul><li>[Névtelen hozzáférés korlátozása Service Fabric fürthöz](#anon-access-cluster)</li><li>[Győződjön meg arról, hogy Service Fabric ügyfél és csomópont közötti tanúsítvány különbözik a csomópontok közötti tanúsítványtól](#fabric-cn-nn)</li><li>[Az ügyfelek hitelesítése a Service Fabric-fürtökön a HRE használatával](#aad-client-fabric)</li><li>[Győződjön meg arról, hogy a Service Fabric-tanúsítványok beszerzése jóváhagyott hitelesítésszolgáltatótól történik](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Az Identity Server által támogatott szabványos hitelesítési forgatókönyvek használata](#standard-authn-id)</li><li>[Az Identity Server-jogkivonat alapértelmezett gyorsítótárának felülbírálása skálázható alternatív megoldással](#override-token)</li></ul> |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Hitelesítés engedélyezése az MSMQ-várólistákhoz való csatlakozáskor a WCF-ben](#msmq-queues)</li><li>[WCF – ne állítsa be az üzenet clientCredentialType nincs értékre](#message-none)</li><li>[WCF – ne állítson be átviteli clientCredentialType nincs értékre](#transport-none)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a webes API-k biztonságossá tétele a szabványos hitelesítési módszerekkel történik](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[A Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata](#authn-aad)</li><li>[Az alapértelmezett ADAL-jogkivonat gyorsítótárának felülbírálása méretezhető alternatív megoldással](#adal-scalable)</li><li>[Győződjön meg arról, hogy a TokenReplayCache a ADAL-hitelesítési tokenek visszajátszásának megakadályozására szolgál](#tokenreplaycache-adal)</li><li>[ADAL-kódtárak használata a OAuth2-ügyfelektől a HRE (vagy a helyszíni AD-hez) való jogkivonat-kérelmek kezeléséhez](#adal-oauth2)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[A Field Gatewayhez csatlakozó eszközök hitelesítése](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy a felhőalapú átjáróhoz csatlakozó eszközök hitelesítése megtörtént](#authn-devices-cloud)</li><li>[Eszközönkénti hitelesítő adatok használata](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy csak a szükséges tárolók és Blobok névtelen olvasási hozzáférést kapnak](#req-containers-anon)</li><li>[Korlátozott hozzáférés biztosítása az Azure Storage-beli objektumokhoz SAS vagy SAP használatával](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>A webalkalmazások hitelesítéséhez használjon szabványos hitelesítési mechanizmust

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | <p>A hitelesítés az a folyamat, amelyben az entitás igazolja az identitását, jellemzően hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek megtekinthetők. Némelyikük alább látható:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlapok alapján</li><li>Összevonás – ADFS</li><li>Összevonás – Azure AD</li><li>Összevonási-identitás kiszolgálója</li></ul><p>Érdemes lehet szabványos hitelesítési mechanizmust használni a forrás folyamat azonosítására.</p>|

## <a id="handle-failed-authn"></a>Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | <p>A felhasználókat explicit módon hitelesítő alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket. A hitelesítési mechanizmusnak a következőket kell tennie:</p><ul><li>Jogosultsággal rendelkező erőforrásokhoz való hozzáférés megtagadása, ha a hitelesítés meghiúsul</li><li>Általános hibaüzenet megjelenítése a sikertelen hitelesítés után, és a hozzáférés megtagadva</li></ul><p>Tesztelés:</p><ul><li>A rendszerjogosultságú erőforrások védelme sikertelen bejelentkezések után</li><li>Általános hibaüzenet jelenik meg a sikertelen hitelesítés és a hozzáférés-megtagadási esemény (ek) esetén</li><li>A fiókok túl sok sikertelen kísérlet után le vannak tiltva</li><ul>|

## <a id="step-up-adaptive-authn"></a>Lépésenkénti vagy adaptív hitelesítés engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | <p>Ellenőrizze, hogy az alkalmazás rendelkezik-e további engedélyekkel (például a Step up vagy az adaptív hitelesítéssel a többtényezős hitelesítésen keresztül, például az egyszeri jelszavas hitelesítést SMS-ben, e-mailben stb.), vagy az ismételt hitelesítést kéri. bizalmas információk. Ez a szabály arra is vonatkozik, hogy a fiók vagy a művelet kritikus fontosságú módosításokat hajtson végre.</p><p>Ez azt is jelenti, hogy a hitelesítés hozzáigazítását olyan módon kell megvalósítani, hogy az alkalmazás helyesen kényszerítse ki a kontextus-érzékeny hitelesítést, hogy ne engedélyezze a jogosulatlan kezelést például a paraméterek módosításával</p>|

## <a id="admin-interface-lockdown"></a>Győződjön meg arról, hogy a felügyeleti felületek megfelelően vannak zárolva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | Az első megoldás az, hogy csak egy adott forrás IP-címről biztosít hozzáférést a felügyeleti felülethez. Ha ez a megoldás nem lenne lehetséges, mint a felügyeleti felületen való bejelentkezéshez szükséges lépésenkénti vagy adaptív hitelesítés kikényszerítés. |

## <a id="forgot-pword-fxn"></a>Elfelejtett jelszó funkcióinak biztonságos implementálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | <p>Először is meg kell győződnie arról, hogy az elfelejtett jelszó és az egyéb helyreállítási útvonalak egy hivatkozást küldenek, beleértve egy időkorlátos aktiválási jogkivonatot is, nem pedig magát a jelszót. A további hitelesítés a Soft-tokenek (például SMS-token, natív mobil alkalmazások stb.) alapján is megkövetelhető, még a hivatkozás elküldése előtt is. Másodszor, ne zárja be a felhasználói fiókot, amíg az új jelszó beszerzésének folyamata folyamatban van.</p><p>Ez szolgáltatásmegtagadást idézhet elő, amikor egy támadó úgy dönt, hogy szándékosan zárolja a felhasználókat egy automatizált támadással. Harmadszor, ha az új jelszó kérésének beállítása folyamatban van, a megjelenített üzenetnek általánosítva kell lennie a Felhasználónév enumerálásának megelőzése érdekében. Negyedszer, mindig letiltja a régi jelszavak használatát és erős jelszóházirend megvalósítását.</p> |

## <a id="pword-account-policy"></a>Győződjön meg arról, hogy a jelszó és a fiók házirendje implementálva van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| Részletek | <p>A szervezeti házirendnek és az ajánlott eljárásoknak megfelelően a jelszó-és a fiók házirendjét kell megvalósítani.</p><p>A találgatásos és a szótáron alapuló találgatások elleni védekezéshez: Erős jelszóházirend szükséges ahhoz, hogy a felhasználók összetett jelszót hozzanak létre (például 12 karakteres minimális hossz, alfanumerikus és speciális karakter).</p><p>A fiókzárolási házirendeket a következő módon lehet megvalósítani:</p><ul><li>**Puha zárolás:** Ez jó lehetőség lehet a felhasználók elleni védelemre a találgatásos támadásokkal szemben. Ha például a felhasználó háromszor helytelen jelszót ad meg, akkor az alkalmazás egy percig zárolhatja a fiókot, hogy lelassítsa a jelszó kikényszerítésének folyamatát, hogy a támadók a folytatáshoz kevésbé nyereségesek legyenek. Ha a fenti példában a nehezen kizáró ellenintézkedéseket kívánja megvalósítani, a fiókok végleges kikapcsolásával "DoS-t" érhet el. Azt is megteheti, hogy az alkalmazás létrehoz egy egyszeri jelszavas (egyszer használatos) jelszót, és sávon kívüli (e-mailben, SMS-ben stb.) küldi el a felhasználónak. Egy másik megközelítés lehet a CAPTCHA implementálása a sikertelen kísérletek küszöbértékének elérésekor.</li><li>**Rögzített zárolás:** Ezt a zárolási típust akkor kell alkalmazni, amikor az alkalmazást támadó felhasználót azonosít, és a fiókját tartósan kizárja, amíg a válasz csapatnak nem volt ideje a kriminalisztikai munkára. A folyamat után dönthet úgy, hogy a felhasználó visszaadja a fiókját, vagy további jogi műveleteket hajt végre rajtuk. Ez a fajta megközelítés megakadályozza, hogy a támadó továbbra is behatoljon az alkalmazásba és az infrastruktúrába.</li></ul><p>Az alapértelmezett és kiszámítható fiókok elleni támadások elleni védelem érdekében ellenőrizze, hogy az összes kulcs és jelszó áthelyezhető-e, és hogy a telepítés után a rendszer létrehozza vagy lecseréli-e azokat.</p><p>Ha az alkalmazásnak automatikusan kell létrehoznia a jelszavakat, ügyeljen arra, hogy a generált jelszavak véletlenszerűek legyenek, és magas entrópia legyen.</p>|

## <a id="controls-username-enum"></a>Vezérlőelemek implementálása a Felhasználónév enumerálásának megakadályozása érdekében

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Az összes hibaüzenetet általánosítani kell a Felhasználónév enumerálásának megelőzése érdekében. Időnként előfordulhat, hogy nem kerülheti el az adatok szivárgását olyan funkciókkal, mint a regisztrációs oldal. Itt kell használnia a rate-limiting metódusokat, például a CAPTCHA-t, hogy megakadályozza a támadók automatizált támadását. |

## <a id="win-authn-sql"></a>Ha lehetséges, a Windows-hitelesítés használatával csatlakozzon SQL Server

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | OnPrem |
| **Attribútumok**              | SQL-verzió – mind |
| **Hivatkozik**              | [SQL Server – hitelesítési mód kiválasztása](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Lépések** | A Windows-hitelesítés Kerberos biztonsági protokollt használ, a jelszó-házirend kényszerítését biztosítja az erős jelszavak összetettségének ellenőrzésével kapcsolatban, és támogatja a fiók zárolását, és támogatja a jelszó lejáratát.|

## <a id="aad-authn-sql"></a>Ha lehetséges, Azure Active Directory hitelesítést használ SQL Databasehoz való csatlakozáshoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió – V12 |
| **Hivatkozik**              | [Csatlakozás SQL Databasehoz Azure Active Directory hitelesítés használatával](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Lépések** | **Minimális verzió:** Azure SQL Database V12 szükséges ahhoz, hogy a Azure SQL Database HRE-hitelesítést használhassanak a Microsoft-címtárral |

## <a id="authn-account-pword"></a>SQL-hitelesítési mód használata esetén győződjön meg arról, hogy a fiók és a jelszó házirendje kényszerítve van az SQL Serveren.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [SQL Server jelszóházirend](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Lépések** | SQL Server hitelesítés használatakor a rendszer olyan SQL Server hozza létre a bejelentkezéseket, amelyek nem Windows felhasználói fiókokon alapulnak. A felhasználónevet és a jelszót is létrehozhatja a SQL Server használatával, és SQL Server tárolja. SQL Server használhatja a Windows-jelszóházirend mechanizmusait. A Windowsban a SQL Serverban használt jelszavakkal megegyező összetettségi és elévülési szabályzatokat alkalmazhat. |

## <a id="autn-contained-db"></a>SQL-hitelesítés használata a foglalt adatbázisokban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | OnPrem, SQL Azure |
| **Attribútumok**              | SQL-verzió – MSSQL2012, SQL-verzió – V12 |
| **Hivatkozik**              | [Ajánlott biztonsági eljárások a foglalt adatbázisokkal](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Lépések** | A kényszerített jelszóházirend hiánya növelheti a gyenge hitelesítő adatoknak a tárolt adatbázisban való létrehozása valószínűségét. Windows-hitelesítés kihasználása. |

## <a id="authn-sas-tokens"></a>Eszközönkénti hitelesítő adatok használata SaS-tokenek használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | <p>A Event Hubs biztonsági modell a közös hozzáférésű aláírási (SAS-) tokenek és az esemény-közzétevők kombinációján alapul. A közzétevő neve a jogkivonatot fogadó DeviceID. Ez segít a megfelelő eszközökhöz generált jogkivonatok hozzárendelésében.</p><p>Minden üzenet a szolgáltatás oldalán található kezdeményezővel van megjelölve, amely lehetővé teszi a hasznos adatokkal való adatgyűjtési kísérletek észlelését. Az eszközök hitelesítése esetén az eszközönkénti SaS-tokent egy egyedi közzétevőre kell állítani.</p>|

## <a id="multi-factor-azure-admin"></a>Az Azure multi-Factor Authentication engedélyezése az Azure-rendszergazdák számára

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-beli megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Mi az az Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Lépések** | <p>A többtényezős hitelesítés (MFA) olyan hitelesítési módszer, amely egynél több ellenőrzési módszert igényel, és kritikus második biztonsági réteget vesz fel a felhasználói bejelentkezésekhez és tranzakciókra. Úgy működik, hogy az alábbi ellenőrzési módszerek közül kettőt vagy többet igényel:</p><ul><li>Amit ismer (általában jelszó)</li><li>Valami, ami rendelkezik (olyan megbízható eszköz, amely nem könnyen duplikált, például telefon)</li><li>Egy dolog (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Névtelen hozzáférés korlátozása Service Fabric fürthöz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure  |
| **Hivatkozik**              | [Service Fabric-fürtök biztonsági forgatókönyveit](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Lépések** | <p>A fürtöket mindig védeni kell annak megakadályozása érdekében, hogy jogosulatlan felhasználók csatlakozzanak a fürthöz, különösen akkor, ha az éles környezetben futó munkaterhelésekkel rendelkezik.</p><p>Service Fabric-fürt létrehozásakor győződjön meg arról, hogy a biztonsági mód "Secure" értékre van állítva, és konfigurálja a szükséges X. 509 kiszolgálói tanúsítványt. Ha egy "nem biztonságos" fürtöt hoz létre, akkor a névtelen felhasználók csatlakozhatnak hozzá, ha a felügyeleti végpontokat a nyilvános internetre teszik közzé.</p>|

## <a id="fabric-cn-nn"></a>Győződjön meg arról, hogy Service Fabric ügyfél és csomópont közötti tanúsítvány különbözik a csomópontok közötti tanúsítványtól

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure, környezet – önálló |
| **Hivatkozik**              | Az [ügyfél és a csomópont közötti biztonság Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Kapcsolódás biztonságos fürthöz ügyféltanúsítvány használatával](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Lépések** | <p>Az ügyfél és a csomópont közötti tanúsítvány biztonsága a fürt létrehozásakor a Azure Portal, a Resource Manager-sablonokon vagy egy önálló JSON-sablonon keresztül történik egy rendszergazdai ügyféltanúsítvány és/vagy felhasználói ügyféltanúsítvány megadásával.</p><p>Az Ön által megadott rendszergazdai ügyfél és felhasználói ügyféltanúsítvány nem lehet azonos a csomópontok közötti biztonsághoz megadott elsődleges és másodlagos tanúsítványokkal.</p>|

## <a id="aad-client-fabric"></a>Az ügyfelek hitelesítése a Service Fabric-fürtökön a HRE használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozik**              | [Fürtök biztonsági forgatókönyvei – biztonsági javaslatok](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Lépések** | Az Azure-on futó fürtök Azure Active Directory (HRE) használatával is biztonságossá tehetik a felügyeleti végpontokat, az Ügyféltanúsítványok mellett. Az Azure-fürtök esetében ajánlott a HRE biztonság használatával hitelesíteni az ügyfeleket és a tanúsítványokat a csomópontok közötti biztonság érdekében.|

## <a id="fabric-cert-ca"></a>Győződjön meg arról, hogy a Service Fabric-tanúsítványok beszerzése jóváhagyott hitelesítésszolgáltatótól történik

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozik**              | [X. 509 tanúsítványok és Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Lépések** | <p>A Service Fabric X. 509 kiszolgálói tanúsítványokat használ a csomópontok és az ügyfelek hitelesítéséhez.</p><p>A tanúsítványok a Service fabricben való használata során megfontolandó szempontok:</p><ul><li>Az éles munkaterheléseket futtató fürtökben használt tanúsítványokat megfelelően konfigurált Windows Server Certificate Service használatával kell létrehozni, vagy egy jóváhagyott hitelesítésszolgáltatótól (CA) kell beszerezni. A HITELESÍTÉSSZOLGÁLTATÓ lehet jóváhagyott külső HITELESÍTÉSSZOLGÁLTATÓ vagy megfelelően felügyelt belső nyilvános kulcsokra épülő infrastruktúra (PKI)</li><li>Soha ne használjon olyan ideiglenes vagy tesztelési tanúsítványt az éles környezetben, amely olyan eszközökkel lett létrehozva, mint például a MakeCert. exe</li><li>Használhat önaláírt tanúsítványt is, de csak tesztelési fürtök esetében, és nem éles környezetben</li></ul>|

## <a id="standard-authn-id"></a>Az Identity Server által támogatott szabványos hitelesítési forgatókönyvek használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [IdentityServer3 – a nagy kép](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Lépések** | <p>Az alábbi jellemző interakciókat támogatja az Identity Server:</p><ul><li>Böngészőkkel kommunikáló webalkalmazások</li><li>A webalkalmazások webes API-kkal kommunikálnak (esetenként a felhasználók nevében)</li><li>A böngészőalapú alkalmazások kommunikálnak a webes API-kkal</li><li>A natív alkalmazások a webes API-kkal kommunikálnak</li><li>A kiszolgáló-alapú alkalmazások kommunikálnak a webes API-kkal</li><li>A webes API-k webes API-kkal kommunikálnak (esetenként a felhasználók nevében)</li></ul>|

## <a id="override-token"></a>Az Identity Server-jogkivonat alapértelmezett gyorsítótárának felülbírálása skálázható alternatív megoldással

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Identity Server-telepítés – gyorsítótárazás](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | <p>A IdentityServer egy egyszerű beépített memóriabeli gyorsítótárral rendelkezik. Habár ez jó megoldás a kis méretű natív alkalmazások esetében, a közepes szintű és a háttérbeli alkalmazások esetében nem méretezhető a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat számos felhasználó egyszerre éri el. Az azonos tárolóban lévő összes hozzáférési jogkivonat mentése elszigetelési problémákat okoz, és kihívásokat jelent, ha a skálán működik: sok felhasználó, akik a legtöbb tokent használják, mint az alkalmazás által a nevükben elérhető erőforrások, nagy mennyiségű és nagyon költséges keresési műveletet jelenthetnek.</li><li>Ezek az alkalmazások jellemzően elosztott topológiákban vannak telepítve, ahol több csomópontnak ugyanahhoz a gyorsítótárhoz kell hozzáférnie.</li><li>A gyorsítótárazott tokeneknek meg kell túlélni a folyamat újrahasznosítását és inaktiválását</li><li>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülbírálni az alapértelmezett Identity Server jogkivonat-gyorsítótárát egy skálázható alternatíva, például az Azure cache for Redis</li></ul>|

## <a id="binaries-signed"></a>Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva, hogy a bináris fájlok integritása ellenőrizhető legyen|

## <a id="msmq-queues"></a>Hitelesítés engedélyezése az MSMQ-várólistákhoz való csatlakozáskor a WCF-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | – |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Lépések** | A program nem tudja engedélyezni a hitelesítést az MSMQ-várólistákhoz való csatlakozáskor, a támadók névtelenül küldhetnek üzeneteket a várólistába feldolgozásra. Ha a hitelesítés nem használható olyan MSMQ-várólistához való csatlakozáshoz, amelyet egy másik programba való üzenet kézbesítésére használ, a támadó rosszindulatú üzenetet küldhet.|

### <a name="example"></a>Példa
Az alábbi WCF-konfigurációs fájl elemearrautasítjaaWCF-t,hogytiltsaleahitelesítést,amikorMSMQ-várólistáhozcsatlakozikazüzenetekkézbesítéséhez.`<netMsmqBinding/>`
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
Konfigurálja az MSMQ-t úgy, hogy minden bejövő vagy kimenő üzenet esetében a Windows-tartomány vagy a tanúsítvány hitelesítését mindig megkövetelje.

### <a name="example"></a>Példa
Az alábbi WCF konfigurációs fájl elemearrautasítjaaWCF-t,hogyengedélyezzeatanúsítvány-hitelesítéstegyMSMQ-várólistáhozvalócsatlakozáskor.`<netMsmqBinding/>` Az ügyfél X. 509 tanúsítvánnyal lett hitelesítve. Az ügyféltanúsítványt a-kiszolgáló tanúsítványtárolójában kell megjelenni.
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

## <a id="message-none"></a>WCF – ne állítsa be az üzenet clientCredentialType nincs értékre

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa – none |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítés](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Lépések** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet a szolgáltatáshoz. Egy olyan szolgáltatás, amely nem hitelesíti az ügyfeleit, hozzáférést biztosít az összes felhasználóhoz. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy az üzenetet Windows vagy tanúsítvány clientCredentialType állítja be. |

### <a name="example"></a>Példa
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF – ne állítson be átviteli clientCredentialType nincs értékre

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa – none |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítés](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Lépések** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet a szolgáltatáshoz. Egy szolgáltatás, amely nem hitelesíti az ügyfeleit, lehetővé teszi az összes felhasználó számára a funkciók elérését. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy a Windows vagy a tanúsítvány átviteli clientCredentialType állítja be. |

### <a name="example"></a>Példa
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Győződjön meg arról, hogy a webes API-k biztonságossá tétele a szabványos hitelesítési módszerekkel történik

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Hitelesítés és engedélyezés a ASP.net web API-ban](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [külső hitelesítési szolgáltatások ASP.net webes APIC#-val ()](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Lépések** | <p>A hitelesítés az a folyamat, amelyben az entitás igazolja az identitását, jellemzően hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek megtekinthetők. Némelyikük alább látható:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlapok alapján</li><li>Összevonás – ADFS</li><li>Összevonás – Azure AD</li><li>Összevonási-identitás kiszolgálója</li></ul><p>A hivatkozások szakaszban található hivatkozások alacsony szintű részleteket biztosítanak arról, hogy az egyes hitelesítési sémák hogyan valósíthatók meg a webes API-k biztonságossá tételéhez.</p>|

## <a id="authn-aad"></a>A Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Hitelesítési forgatókönyvek az Azure ad-hez](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory mintakód](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory fejlesztői útmutató](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Lépések** | <p>Azure Active Directory (Azure AD) leegyszerűsíti a fejlesztők hitelesítését szolgáltatásként, az iparági szabványnak megfelelő protokollok, például a OAuth 2,0 és az OpenID Connect támogatásával. Az alábbi öt elsődleges alkalmazási forgatókönyv az Azure AD által támogatott:</p><ul><li>Webböngészőből webes alkalmazásba: A felhasználónak be kell jelentkeznie egy Azure AD által védett webalkalmazásba</li><li>Egyoldalas alkalmazás (SPA): Egy felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít</li><li>Natív alkalmazás webes API-hoz: Egy telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure AD által védett webes API erőforrásainak lekéréséhez.</li><li>Webalkalmazás webes API-hoz: Egy webalkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat</li><li>Daemon vagy Server alkalmazás webes API-hoz: Az Azure AD által védett webes API-k erőforrásainak lekérése egy olyan démon-alkalmazás vagy kiszolgálóalkalmazás, amely nem rendelkezik webes felhasználói felülettel</li></ul><p>Az alacsony szintű megvalósítás részleteiért tekintse meg a hivatkozások szakaszban található hivatkozásokat</p>|

## <a id="adal-scalable"></a>Az alapértelmezett ADAL-jogkivonat gyorsítótárának felülbírálása méretezhető alternatív megoldással

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Modern hitelesítés Azure Active Directory](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/)webalkalmazásokhoz, [Redis használata ADAL jogkivonat](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/) -gyorsítótárként  |
| **Lépések** | <p>A ADAL (Active Directory-hitelesítési tár) által használt alapértelmezett gyorsítótár a memóriabeli gyorsítótár, amely egy statikus tárolóra támaszkodik, amely elérhető a teljes folyamatra. Habár ez a natív alkalmazások esetében is működik, a közepes szintű és háttérbeli alkalmazások esetében nem méretezhető a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat számos felhasználó egyszerre éri el. Az azonos tárolóban lévő összes hozzáférési jogkivonat mentése elszigetelési problémákat okoz, és kihívásokat jelent, ha a skálán működik: sok felhasználó, akik a legtöbb tokent használják, mint az alkalmazás által a nevükben elérhető erőforrások, nagy mennyiségű és nagyon költséges keresési műveletet jelenthetnek.</li><li>Ezek az alkalmazások jellemzően elosztott topológiákban vannak telepítve, ahol több csomópontnak ugyanahhoz a gyorsítótárhoz kell hozzáférnie.</li><li>A gyorsítótárazott tokeneknek meg kell túlélni a folyamat újrahasznosítását és inaktiválását</li></ul><p>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülbírálni az alapértelmezett ADAL-jogkivonat gyorsítótárát egy skálázható alternatív megoldással, például az Azure cache for Redis szolgáltatással.</p>|

## <a id="tokenreplaycache-adal"></a>Győződjön meg arról, hogy a TokenReplayCache a ADAL-hitelesítési tokenek visszajátszásának megakadályozására szolgál

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Modern hitelesítés Azure Active Directory webalkalmazásokhoz](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Lépések** | <p>A TokenReplayCache tulajdonság lehetővé teszi a fejlesztők számára a jogkivonat-Visszajátszási gyorsítótár meghatározását, amely egy olyan tároló, amely a jogkivonatok mentésére használható annak ellenőrzése céljából, hogy a jogkivonat nem használható többször.</p><p>Ez egy közös támadáson alapuló mérték, a "jogkivonat-Visszajátszási támadás": a bejelentkezéskor küldött tokent elfogó támadó megpróbálhatja elküldeni az alkalmazásba ("Replay") egy új munkamenet létrehozásához. Például a sikeres felhasználói hitelesítés után a OIDC-kód engedélyezési folyamatában a függő entitás "/signin-oidc" végpontjának a "id_token", a "code" és az "State" paraméterekkel kell rendelkezni.</p><p>A függő entitás érvényesíti ezt a kérést, és új munkamenetet hoz létre. Ha egy támadó rögzíti ezt a kérést, és visszajátssza, akkor sikeres munkamenetet hozhat létre, és meghamisíthatja a felhasználót. Az Egypéldányos kapcsolat megléte az OpenID Connectben korlátozható, de nem teljes mértékben kizárja azokat a körülményeket, amelyekben a támadás sikeresen elvégezhető. Az alkalmazások megvédése érdekében a fejlesztők a ITokenReplayCache megvalósítását és a TokenReplayCache-példányok hozzárendelését is lehetővé teszik.</p>|

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
Íme egy példa a ITokenReplayCache felület megvalósítására. (A projekt-specifikus gyorsítótárazási keretrendszer testreszabása és implementálása)
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
A megvalósított gyorsítótárat a következőképpen kell hivatkozni a OIDC-beállításokban a "TokenValidationParameters" tulajdonságon keresztül.
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

Vegye figyelembe, hogy a konfiguráció hatékonyságának teszteléséhez jelentkezzen be a helyi OIDC-védelemmel ellátott alkalmazásba, és `"/signin-oidc"` rögzítse a kérést a Hegedűs végpontjának. Ha a védelem nincs érvényben, a Hegedűs új munkamenet-cookie-t állít vissza. Ha a kérést a TokenReplayCache-védelem hozzáadása után is visszajátsszák, az alkalmazás kivételt jelez az alábbiak szerint:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>ADAL-kódtárak használata a OAuth2-ügyfelektől a HRE (vagy a helyszíni AD-hez) való jogkivonat-kérelmek kezeléséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Lépések** | <p>Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfélalkalmazások fejlesztői számára, hogy egyszerűen hitelesítsék a felhasználókat a felhőben vagy a helyszíni Active Directoryban (AD), majd hozzáférési jogkivonatokat kapjanak az API-hívások biztonságossá tételéhez.</p><p>A ADAL számos funkcióval rendelkezik, amelyek megkönnyítik a hitelesítést a fejlesztők számára, például az aszinkron támogatást, a hozzáférési jogkivonatokat tároló konfigurálható jogkivonat-gyorsítótárat, az automatikus jogkivonat-frissítést a hozzáférési token érvényességének lejárta és a frissítési jogkivonat elérhetővé tétele esetén, valamint További.</p><p>A ADAL a legtöbb összetettségtel segíti a fejlesztőket az alkalmazás üzleti logikájának, valamint az erőforrások egyszerű biztonsága nélkül. Külön könyvtárak érhetők el a .NET, a JavaScript (Client és Node. js), az iOS, az Android és a Java rendszerekhez.</p>|

## <a id="authn-devices-field"></a>A Field Gatewayhez csatlakozó eszközök hitelesítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy minden eszközt a Field Gateway hitelesít, mielőtt az adatokat befogadja tőlük, és még a Felhőbeli átjáróval való felsőbb szintű kommunikáció megkönnyítése előtt. Győződjön meg arról is, hogy az eszközök eszközönkénti hitelesítő adatokkal kapcsolódnak, hogy az egyes eszközök egyedi azonosításra alkalmasak legyenek.|

## <a id="authn-devices-cloud"></a>Győződjön meg arról, hogy a felhőalapú átjáróhoz csatlakozó eszközök hitelesítése megtörtént

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, C#, Node. js,  |
| **Attribútumok**              | N/A, átjáró választása – Azure IoT Hub |
| **Hivatkozik**              | N/A, [Azure IoT hub .net](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)-tel, [első lépések az IoT hub és a Node js](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted)használatával [](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) [](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Lépések** | <ul><li>**Általános** Az eszköz hitelesítése Transport Layer Security (TLS) vagy IPSec használatával. Az infrastruktúrának támogatnia kell az előmegosztott kulcs (PSK) használatát azokon az eszközökön, amelyek nem képesek a teljes aszimmetrikus titkosítás kezelésére. Használja ki az Azure AD-t, a OAuth-t.</li><li>**C#:** DeviceClient-példány létrehozásakor alapértelmezés szerint a Create metódus létrehoz egy DeviceClient-példányt, amely a AMQP protokollt használja a IoT Hub való kommunikációhoz. A HTTPS protokoll használatához használja a Create metódus felülbírálását, amely lehetővé teszi a protokoll megadását. Ha a HTTPS protokollt használja, a `Microsoft.AspNet.WebApi.Client` NuGet csomagot is hozzá kell adnia a projekthez, hogy tartalmazza a `System.Net.Http.Formatting` névteret.</li></ul>|

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
**Node. JS: Hitelesítés**
#### <a name="symmetric-key"></a>Szimmetrikus kulcs
* IoT hub létrehozása az Azure-ban
* Bejegyzés létrehozása az eszköz identitásának beállításjegyzékében
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
  #### <a name="sas-token"></a>SAS-jogkivonat
* Belsőleg generált a szimmetrikus kulcs használatakor, de explicit módon is létrehozható és használható.
* Protokoll definiálása:`var Http = require('azure-iot-device-http').Http;`
* Sas-token létrehozása:
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
* Kapcsolat sas-token használatával: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Tanúsítványok
* Önaláírt X509-tanúsítvány létrehozása bármilyen eszközzel, például OpenSSL használatával a tanúsítvány és a kulcs tárolására szolgáló. CERT és. Key fájlok létrehozásához.
* Olyan eszköz kiépítése, amely a tanúsítványokkal biztonságos kapcsolatokat fogad el.
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
* Eszköz csatlakoztatása tanúsítvány használatával
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

## <a id="authn-cred"></a>Eszközönkénti hitelesítő adatok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway  | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozik**              | [Azure IoT Hub biztonsági tokenek](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Lépések** | Használjon eszközönkénti hitelesítő adatokat SaS-jogkivonatok használatával az eszköz kulcs vagy ügyféltanúsítvány alapján, IoT Hub szintű közös hozzáférésű házirendek helyett. Ez megakadályozza, hogy egy eszköz vagy egy mező átjárójának hitelesítési jogkivonatait egy másik |

## <a id="req-containers-anon"></a>Győződjön meg arról, hogy csak a szükséges tárolók és Blobok névtelen olvasási hozzáférést kapnak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType – blob |
| **Hivatkozik**              | [ [A tárolók és Blobok névtelen olvasási hozzáférésének kezelése](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), közös hozzáférésű aláírások, 1. rész: Az SAS-modell megismerése](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Lépések** | <p>Alapértelmezés szerint a tárolót és a benne található blobokat csak a Storage-fiók tulajdonosa érheti el. Ahhoz, hogy a névtelen felhasználók olvasási engedélyt adjanak a tárolók és a Blobok számára, az egyik megadhatja a tároló engedélyeit a nyilvános hozzáférés engedélyezéséhez. A névtelen felhasználók a kérelem hitelesítése nélkül is elolvashatják a blobokat egy nyilvánosan elérhető tárolón belül.</p><p>A tárolók a következő beállításokat biztosítják a tárolók hozzáférésének kezeléséhez:</p><ul><li>Teljes nyilvános olvasási hozzáférés: A tárolók és a Blobok adatolvasása névtelen kéréssel végezhető el. Az ügyfelek névtelen kéréssel enumerálják a tárolóban lévő blobokat, de nem tudják enumerálni a tároló fiókjában lévő tárolókat.</li><li>Nyilvános olvasási hozzáférés csak Blobok esetén: A tárolóban található blob-információk névtelen kéréssel olvashatók, de a tárolók nem érhetők el. Az ügyfelek névtelen kéréssel nem tudják enumerálni a tárolóban lévő blobokat</li><li>Nincs nyilvános olvasási hozzáférés: A tároló-és blob-adatkészleteket csak a fiók tulajdonosa tudja beolvasni</li></ul><p>A névtelen hozzáférés a legjobb olyan helyzetekben, amikor bizonyos bloboknak mindig elérhetőnek kell lenniük a névtelen olvasási hozzáféréshez. A részletes szabályozáshoz létrehozhat egy közös hozzáférési aláírást, amely lehetővé teszi a korlátozott hozzáférés delegálását a különböző engedélyekkel és a megadott időintervallumon belül. Győződjön meg arról, hogy a tárolók és Blobok, amelyek esetleg bizalmas adatokat is tartalmazhatnak, nem kapnak véletlenül névtelen hozzáférést</p>|

## <a id="limited-access-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-beli objektumokhoz SAS vagy SAP használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozik**              | [Közös hozzáférési aláírások, 1. rész: Az SAS-modell](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [a közös hozzáférési aláírások ismertetése, 2. rész: A blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/)-val rendelkező sas létrehozása és használata, a [fiókban lévő objektumokhoz való hozzáférés delegálása közös hozzáférésű aláírások és tárolt hozzáférési szabályzatok használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Lépések** | <p>A közös hozzáférésű aláírások (SAS) használatával a Storage-fiókban lévő objektumok számára korlátozott hozzáférést biztosíthat a többi ügyfélnek, anélkül, hogy a fiók elérési kulcsát kellene kitennie. Az SAS egy URI, amely magában foglalja a lekérdezési paramétereit a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információval. A tároló erőforrásainak SAS-vel való eléréséhez az ügyfélnek csak az SAS-t kell átadnia a megfelelő konstruktorba vagy metódusba.</p><p>Ha olyan ügyfél számára kíván hozzáférést biztosítani a Storage-fiók erőforrásaihoz, amely nem megbízható a fiók kulcsával, akkor használhat SAS-t. A Storage-fiók kulcsa egy elsődleges és egy másodlagos kulcsot is tartalmaz, amelyek mindegyike rendszergazdai hozzáférést biztosít a fiókjához és a benne lévő összes erőforráshoz. A fiók kulcsainak bármelyike megnyithatja a fiókját a kártékony vagy gondatlan használat lehetőségével. A közös hozzáférésű aláírások biztonságos alternatívát biztosítanak, amely lehetővé teszi más ügyfelek számára, hogy a megadott engedélyek alapján a Storage-fiókban tárolt adatait olvassák, írják és töröljék, és a fiók kulcsa nélkül legyenek szükségesek.</p><p>Ha van olyan logikai készlete, amely minden alkalommal hasonló, a tárolt hozzáférési szabályzat (SAP) használatával jobb ötlet. Mivel a tárolt hozzáférési házirendből származtatott SAS-t használva azonnal visszavonhatja az SAS-t, az ajánlott eljárás, hogy mindig a tárolt hozzáférési szabályzatokat használja, ha lehetséges.</p>|
