---
title: "-Microsoft fenyegetés modellezési eszköz - hitelesítési Azure |} Microsoft Docs"
description: "a fenyegetések modellezése eszköz felfedett fenyegetések megoldást"
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
ms.openlocfilehash: 1ac614156755b9b29db7c968c708a5cff706f7a8
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-authentication--mitigations"></a>Biztonsági keret: Hitelesítési |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás**    | <ul><li>[Érdemes lehet egy szabványon alapuló hitelesítési mechanizmus webes alkalmazás felé történő hitelesítésre](#standard-authn-web-app)</li><li>[Alkalmazások biztonságos helyen kell kezelni sikertelen hitelesítési forgatókönyvek](#handle-failed-authn)</li><li>[Enable lépés mentése vagy adaptív hitelesítés](#step-up-adaptive-authn)</li><li>[Győződjön meg arról, hogy a felügyeleti felületek megfelelően zárolva miatt](#admin-interface-lockdown)</li><li>[Alkalmazzon biztonságosan elfelejtett jelszó funkciók](#forgot-pword-fxn)</li><li>[Győződjön meg arról, hogy valósíthatók meg jelszó- és házirend](#pword-account-policy)</li><li>[Felhasználónév számbavételi megelőzése érdekében a vezérlőelemeket kell megvalósítani](#controls-username-enum)</li></ul> |
| **Adatbázis** | <ul><li>[Ha lehetséges, Windows-hitelesítés használatára SQL-kiszolgálójához való kapcsolódás](#win-authn-sql)</li><li>[Ha lehetséges Azure Active Directory-hitelesítés használja az SQL-adatbázishoz való csatlakozás](#aad-authn-sql)</li><li>[SQL-hitelesítési mód használata esetén győződjön meg arról, hogy fiókkal és jelszóval házirend SQL-kiszolgálón érvényesítik](#authn-account-pword)</li><li>[SQL-hitelesítés nem tartalmazott adatbázisok használata](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Eszköz hitelesítő adatok használatával SaS-tokenje hozott](#authn-sas-tokens)</li></ul> |
| **Az Azure megbízhatósági kapcsolat határán** | <ul><li>[Az Azure-rendszergazdák számára az Azure többtényezős hitelesítés engedélyezése](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határán** | <ul><li>[Névtelen hozzáférés korlátozása a Service Fabric-fürt](#anon-access-cluster)</li><li>[Győződjön meg arról, hogy a Service Fabric-csomópont ügyfél tanúsítvány nem azonos a csomópontok tanúsítvány](#fabric-cn-nn)</li><li>[Az AAD segítségével hitelesíti az ügyfeleket a service fabric-fürtök](#aad-client-fabric)</li><li>[Győződjön meg arról, hogy a service fabric tanúsítványokat kapnak egy jóváhagyott tanúsítvány hitelesítésszolgáltatói (CA)](#fabric-cert-ca)</li></ul> |
| **Identity Serverben** | <ul><li>[Identitáskezelési kiszolgáló által támogatott szabványon alapuló hitelesítési forgatókönyvek használata](#standard-authn-id)</li><li>[Az alapértelmezett Identitáskiszolgálók jogkivonat gyorsítótára méretezhető alternatív felülbírálása](#override-token)</li></ul> |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy a telepített alkalmazás bináris fájljainak digitális aláírással](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Hitelesítés engedélyezése WCF lévő MSMQ-várólista történő csatlakozás során](#msmq-queues)</li><li>[WCF-ne beállítása üzenet clientCredentialType nincs](#message-none)</li><li>[WCF-ne beállítása Transport clientCredentialType nincs](#transport-none)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy szabványon alapuló hitelesítési módszerek segítségével biztonságos webes API-k](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Használja az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvei](#authn-aad)</li><li>[Az alapértelmezett ADAL jogkivonat gyorsítótára méretezhető alternatív felülbírálása](#adal-scalable)</li><li>[Győződjön meg arról, hogy TokenReplayCache annak megelőzésére szolgál az ADAL-hitelesítés jogkivonatok ismétlés](#tokenreplaycache-adal)</li><li>[Segítségével ADAL könyvtárakat kezelheti a jogkivonat-kérelmeket az OAuth2-ügyfelektől az aad-be (vagy a helyszíni AD)](#adal-oauth2)</li></ul> |
| **Az IoT-mező átjáró** | <ul><li>[A mező átjáró csatlakozó eszközök hitelesítéséhez](#authn-devices-field)</li></ul> |
| **Az IoT átjáró** | <ul><li>[Győződjön meg arról, hogy az átjáró a kapcsolódó eszközök hitelesítése](#authn-devices-cloud)</li><li>[Eszköz hitelesítő adatok használata](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy csak a szükséges tárolók és blobok névtelen olvasási hozzáférést kap](#req-containers-anon)</li><li>[Az Azure storage-ban SAS vagy SAP objektumok korlátozott hozzáférést](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Érdemes lehet egy szabványon alapuló hitelesítési mechanizmus webes alkalmazás felé történő hitelesítésre

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Hitelesítés az a folyamat, ha egy entitás igazolja magát, általában a hitelesítő adatokat, például egy felhasználónevet és jelszót. Nincsenek több hitelesítési protokollok érhető el lehet tekinteni. Ezek közül az alábbiak:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlap alapú</li><li>Összevonási - AD FS</li><li>Összevonási – az Azure AD</li><li>Összevonási - Identitáskiszolgálók</li></ul><p>Érdemes lehet egy szabványon alapuló hitelesítési mechanizmus forrás folyamat azonosítása</p>|

## <a id="handle-failed-authn"></a>Alkalmazások biztonságos helyen kell kezelni sikertelen hitelesítési forgatókönyvek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Alkalmazások, amelyek kifejezetten a felhasználók hitelesítésére biztonságosan sikertelen hitelesítési forgatókönyvek kell kezelni. A hitelesítési módszert kell:</p><ul><li>Védett erőforrásokhoz való hozzáférés visszautasítja a hitelesítés sikertelen</li><li>Egy általános hibaüzenetet jeleníti meg a sikertelen hitelesítés után, és hozzáférés-Megtagadás</li></ul><p>Teszt:</p><ul><li>Sikertelen bejelentkezés után a védett erőforrások védelme</li><li>Általános hibaüzenet jelenik meg a sikertelen hitelesítési és hozzáférés-megtagadási esemény</li><li>Túl sok sikertelen kísérlet után letiltott fiókok</li><ul>|

## <a id="step-up-adaptive-authn"></a>Enable lépés mentése vagy adaptív hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Ellenőrizze az alkalmazás rendelkezik-e további engedélyezési (például egy fokozzák vagy adaptív hitelesítési, többtényezős hitelesítést, például küldés OTP SMS, e-mail stb. vagy újbóli hitelesítést kér keresztül), a felhasználó próbálnak futtatni rajta való hozzáférést megkapják előtt bizalmas adatokat. Ez a szabály is vonatkozik egy fiók vagy a művelet kritikus módosítása</p><p>Ez azt is jelenti, hogy rendelkezik-e a hitelesítés kiigazítása oly módon, hogy az alkalmazás megfelelően kikényszeríti nem lehetővé teszi az illetéktelen módosítását. révén példában környezetfüggő engedélyezési végrehajtandó paraméter illetéktelen módosítását.</p>|

## <a id="admin-interface-lockdown"></a>Győződjön meg arról, hogy a felügyeleti felületek megfelelően zárolva miatt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | Az első megoldás, hogy csak egy bizonyos forrás IP-tartomány a hozzáférési jogot a rendszergazdai felületen. Ha ez nem volna lehetséges, mint mindig ajánlott többlépéses vagy adaptív hitelesítési a rendszergazdai felületen történő naplózás kényszerítéséhez |

## <a id="forgot-pword-fxn"></a>Alkalmazzon biztonságosan elfelejtett jelszó funkciók

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Először is annak ellenőrzésére, hogy elfelejtette a jelszavát, és más helyreállítási elérési utak küldjön egy hivatkozást, beleértve magát a jelszó helyett egy időben korlátozott aktiválási jogkivonatot. További hitelesítési soft-jogkivonatok (pl. SMS token, natív mobilalkalmazások stb.) alapján lehet szükség, valamint, mielőtt a hivatkozás továbbítása. Második, meg kell nem a felhasználói fiók zárolása miközben folyamatban van egy új jelszó folyamatának.</p><p>Ez előfordulhat, hogy egy szolgáltatásmegtagadási támadás, amikor a támadó úgy dönt, hogy a felhasználók automatikus támadást szándékosan zárolására. Harmadik amikor az új jelszó kérelem folyamatban van beállítva, az üzenet jelenítjük meg kell általánosítva leállítja, nehogy felhasználónév számbavételi. Negyedik mindig tiltsa le a régi jelszót és egy erős jelszót házirend bevezetése.</p> |

## <a id="pword-account-policy"></a>Győződjön meg arról, hogy valósíthatók meg jelszó- és házirend

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Részletek | <p>Szervezeti házirend és gyakorlatok betartása jelszó- és házirend kell végrehajtani.</p><p>Találgatásos és alapú szótár találgatás elleni védelemre: erős jelszó kell megvalósítani, annak érdekében, hogy a felhasználók létrehozhatnak erős jelszót hozzon létre (pl. 12 karakterek minimális hosszát, alfanumerikus vagy speciális karaktereket).</p><p>Fiókzárolási házirendek végre a következő módon:</p><ul><li>**Letölthető zárolási kibővített:** Ez lehet, hogy a felhasználók találgatásos támadásokkal szembeni védelme jó választás. Például amikor a felhasználó helytelen jelszót ad háromszor az alkalmazás tudta zárolni a fiók egy percet ahhoz, hogy a művelet lelassíthat kényszerítése a jelszavát, így kevésbé hatékony folytatja a támadó találgatásos folyamatán. Ha merevlemez zárolása kibővített ellenintézkedések – ebben a példában meg elérése érdekében végrehajtásához "DOS" véglegesen a fiókok zárolásának által. Másik lehetőségként alkalmazás egy egyszeri Jelszavas (egy alkalommal jelszót) készítése és elküldi a sávon kívüli (mailben, sms stb.) a felhasználó számára. Egy másik módszer a CAPTCHA végrehajtásához egy sikertelen bejelentkezési kísérletek számát küszöbérték elérése után lehet.</li><li>**Merevlemez zárolása kibővített:** fiókzárolási az ilyen típusú kell alkalmazni, amikor a felhasználó az alkalmazás támadása észlelése és számláló őt segítségével véglegesen a fiók zárolásának mindaddig, amíg egy csoportnak átvették a törvényszéki tegye. Miután ezt a folyamatot a felhasználó dönthet úgy is biztonsági másolatot a fiók, vagy műveletek további jogi vele szemben. Megközelítés az ilyen típusú megakadályozza, hogy a támadó további túljutó az alkalmazás- és infrastruktúra.</li></ul><p>Az alapértelmezett és kiszámítható támadások elleni védelemre, győződjön meg arról, hogy minden kulcsok és jelszavak cserélhető, és jön létre, illetve lecserélik a telepítéshez szükséges idő után.</p><p>Kell-e az alkalmazás automatikusan előállítja a jelszavak, győződjön meg arról, hogy a létrehozott jelszavakat véletlenszerű és magas entrópia.</p>|

## <a id="controls-username-enum"></a>Felhasználónév számbavételi megelőzése érdekében a vezérlőelemeket kell megvalósítani

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Az összes hibaüzenet kell általánosítva leállítja, nehogy felhasználónév enumerálása. Emellett egyes esetekben nem kerülheti el megakadályozására funkciók, például a regisztrációs oldalon található információt. Itt kell módszerekkel Sebességhatároló CAPTCHA például egy támadó automatizált támadások megelőzése érdekében. |

## <a id="win-authn-sql"></a>Ha lehetséges, Windows-hitelesítés használatára SQL-kiszolgálójához való kapcsolódás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | A helyi üzemeltetésű |
| **Attribútumok**              | SQL-verzió - minden |
| **Hivatkozások**              | [SQL Server - hitelesítési módjának kiválasztása](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Lépések** | Windows-hitelesítés Kerberos biztonsági protokollt használ, jelszó betartatja tekintetében összetettségi érvényesítési erős jelszavak, támogatja a fiók zárolása és támogatja a jelszó lejárati idejét.|

## <a id="aad-authn-sql"></a>Ha lehetséges Azure Active Directory-hitelesítés használja az SQL-adatbázishoz való csatlakozás

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Csatlakozás az SQL Database az Azure Active Directory-hitelesítés használatával](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Lépések** | **Minimális verzió:** Azure SQL Database V12 Azure SQL-adatbázis a Microsoft Directoryban AAD-hitelesítés használatát lehetővé teszi, |

## <a id="authn-account-pword"></a>SQL-hitelesítési mód használata esetén győződjön meg arról, hogy fiókkal és jelszóval házirend SQL-kiszolgálón érvényesítik

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [SQL Server jelszóházirend](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Lépések** | SQL Server-hitelesítést használ, amikor az SQL Server, a Windows felhasználói fiókok nem épülő bejelentkezések jönnek létre. SQL Server használatával létrehozott és az SQL Serverben tárolt mind a felhasználónév és a jelszót. SQL Server Windows jelszó házirend mechanizmusok használhatja. Az azonos összetettségét, és az SQL-kiszolgálón belül használt jelszavakat a Windowsban használt lejárati házirendek láthat el. |

## <a id="autn-contained-db"></a>SQL-hitelesítés nem tartalmazott adatbázisok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | OnPrem, SQL Azure |
| **Attribútumok**              | SQL - MSSQL2012, SQL verzió - verzió 12-es verzió |
| **Hivatkozások**              | [Ajánlott biztonsági eljárások tartalmazott adatbázisok](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Lépések** | Az érvényben levő jelszóházirendnek hiányában növelheti a gyenge hitelesítő adat jött létre egy tartalmazott adatbázisban ennek valószínűségét. Éljen az olyan Windows-hitelesítést. |

## <a id="authn-sas-tokens"></a>Eszköz hitelesítő adatok használatával SaS-tokenje hozott

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Event Hubs hitelesítés és a biztonsági modell – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | <p>Az Event Hubs biztonsági modell közös hozzáférésű Jogosultságkód (SAS) tokenek és esemény-közzétevők alapul. A közzétevő neve, amely megkapja a jogkivonatot a DeviceID jelöli. Ez segíthet a jogkivonatok jön létre a megfelelő eszközökkel társíthat.</p><p>Az összes üzenet a kezdeményező szolgáltatás oldalán kísérletek-címek hamisítását az adattartalom származási észlelési így címkével rendelkeznek. Eszközök hitelesítésekor létrehozni egy egyedi közzétevő hatóköre SaS-jogkivonat eszköz száma.</p>|

## <a id="multi-factor-azure-admin"></a>Az Azure-rendszergazdák számára az Azure többtényezős hitelesítés engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Mi az az Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Lépések** | <p>Többtényezős hitelesítés (MFA), amely egynél több ellenőrzési módszert igényel, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési mód. Működését tekintve a igénylő bármely két vagy több, az alábbi hitelesítési módszerek:</p><ul><li>Valami tudja (általában jelszót)</li><li>Valami (megbízható eszközzel rendelkezik, amely nem egyszerű ismétlődik, például a telefon)</li><li>Valami áll (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Névtelen hozzáférés korlátozása a Service Fabric-fürt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure  |
| **Hivatkozások**              | [A Service Fabric-fürt biztonsági forgatókönyvek](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Lépések** | <p>Fürtök mindig titkosítani kell, hogy jogosulatlan felhasználók csatlakozzon a fürthöz, különösen akkor, ha rendelkezik termelési számítási feladatokhoz fut rajta.</p><p>A service fabric-fürt létrehozásakor győződjön meg arról, hogy a biztonsági mód értéke "biztonságos üzenetet", és konfigurálja a szükséges X.509 tanúsítvány. Egy "biztonságos" fürt létrehozása lehetővé teszi a névtelen felhasználók kapcsolódni hozzá vagy ha a nyilvános internethez felügyeleti végpontok teszi elérhetővé.</p>|

## <a id="fabric-cn-nn"></a>Győződjön meg arról, hogy a Service Fabric-csomópont ügyfél tanúsítvány nem azonos a csomópontok tanúsítvány

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Önálló – Azure, környezet - környezet |
| **Hivatkozások**              | [A Service Fabric-csomópont ügyfél tanúsítvány biztonsági](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [csatlakozás ügyfél-tanúsítvány használatával biztonságos fürthöz](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Lépések** | <p>Ügyfél-csomópont tanúsítvány a biztonsági beállítások konfigurálása az Azure portálon, a Resource Manager-sablonok vagy egy önálló JSON-sablon egy rendszergazdai ügyféltanúsítvány és/vagy felhasználói ügyféltanúsítványt megadásával keresztül vagy a fürt létrehozása során.</p><p>A felügyeleti ügyfél és a felhasználói ügyféltanúsítványok megadott különbözik az elsődleges és másodlagos tanúsítványokat állít be csomópontok biztonsági kell lennie.</p>|

## <a id="aad-client-fabric"></a>Az AAD segítségével hitelesíti az ügyfeleket a service fabric-fürtök

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [Fürtök biztonsági forgatókönyveinek - biztonsági javaslatok](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Lépések** | Azure-on futó fürtök is biztonságossá teheti az Azure Active Directory (AAD), használó ügyfél-tanúsítványok mellett felügyeleti végpontok elérését. Az Azure-fürtök esetén ajánlott AAD biztonsági használhat az ügyfelek és -csomópontok biztonsági tanúsítványok hitelesítéséhez.|

## <a id="fabric-cert-ca"></a>Győződjön meg arról, hogy a service fabric tanúsítványokat kapnak egy jóváhagyott tanúsítvány hitelesítésszolgáltatói (CA)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [X.509-tanúsítványokat és a Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Lépések** | <p>Service Fabric X.509 kiszolgálói tanúsítványokat használ a csomópontok és az ügyfelek hitelesítéséhez.</p><p>Fontos szempontokat kell figyelembe venni a szolgáltatás hálók tanúsítványok használata közben:</p><ul><li>A termelési számítási feladatokhoz rendszert futtató fürtöket használt tanúsítványok legyen a megfelelően konfigurált Windows kiszolgálói tanúsítvány szolgáltatások segítségével létrehozott vagy egy jóváhagyott tanúsítvány hitelesítésszolgáltatói (CA) kapott. A hitelesítésszolgáltató egy jóváhagyott külső hitelesítésszolgáltató vagy egy felügyelt megfelelően belső nyilvánoskulcs-infrastruktúrát (PKI) lehet.</li><li>Soha ne használja az összes ideiglenes vagy tanúsítványok tesztelése éles üzemben eszközöket, például a MakeCert.exe használatával létrehozott</li><li>Használhat önaláírt tanúsítványt, de csak akkor tegye ezt tesztfürtökön és nem éles környezetben</li></ul>|

## <a id="standard-authn-id"></a>Identitáskezelési kiszolgáló által támogatott szabványon alapuló hitelesítési forgatókönyvek használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IdentityServer3 - átfogó képet](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Lépések** | <p>Az alábbiakban a tipikus kapcsolati identitás-kiszolgáló által támogatott:</p><ul><li>Webalkalmazások kommunikálni böngészők</li><li>Webalkalmazások kommunikálni a webes API-k (néha a saját, néha egy felhasználó nevében)</li><li>Webböngésző-alapú alkalmazások kommunikálnak a webes API-k</li><li>Webes API-k kommunikálni natív alkalmazások</li><li>Kiszolgáló-alapú alkalmazások kommunikálni a webes API-k</li><li>Webes API-k kommunikálni a webes API-k (néha a saját, néha egy felhasználó nevében)</li></ul>|

## <a id="override-token"></a>Az alapértelmezett Identitáskiszolgálók jogkivonat gyorsítótára méretezhető alternatív felülbírálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Identitáskezelési kiszolgáló telepítési - gyorsítótárazás](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | <p>IdentityServer rendelkezik egy egyszerű beépített memórián belüli gyorsítótárral. Ez nem jó kis léptékű natív alkalmazásokhoz, akkor nem méretezhető a középső réteg és a háttérkiszolgáló alkalmazások a következők lehetnek az okai:</p><ul><li>Ezek az alkalmazások által elért sok felhasználó egyszerre. Minden hozzáférési jogkivonatok mentése ugyanabban a tárolóban elkülönítési problémák hoz létre, és megadja a kihívásokkal léptékű működő: számos annyi jogkivonatokat, mint az erőforrások, az alkalmazás hozzáfér a nevükben, a felhasználó azt hatalmas számok és nagyon költséges keresési művelet</li><li>Ezek az alkalmazások általában telepített Elosztott topológia, ahol több csomópont hozzáféréssel kell rendelkeznie a azonos gyorsítótár</li><li>Gyorsítótárazott jogkivonatok folyamat újrahasznosítja azt és deactivations tovább kell működnie</li><li>A fenti okokból minden közben implementing web apps, javasoljuk, hogy felülírják az alapértelmezett Identitáskiszolgálók jogkivonatok gyorsítótárát egy méretezhető alternatív, például az Azure Redis Cache-gyorsítótár</li></ul>|

## <a id="binaries-signed"></a>Győződjön meg arról, hogy a telepített alkalmazás bináris fájljainak digitális aláírással

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL Phase**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy üzembe helyezett alkalmazás bináris fájljainak digitális aláírással, hogy a bináris fájlok integritásának ellenőrizhetők.|

## <a id="msmq-queues"></a>Hitelesítés engedélyezése WCF lévő MSMQ-várólista történő csatlakozás során

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | – |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Lépések** | Hitelesítés engedélyezése, amikor csatlakozik az MSMQ-várólista nem működik, a támadó névtelenül is elküldhetik a feldolgozás a várólista-üzenetek. Hitelesítés nem használata csatlakozni az MSMQ-várólista kézbesíteni egy üzenetet egy másik program használja, a támadó rosszindulatú névtelen üzenetben sikerült elküldeni.|

### <a name="example"></a>Példa
A `<netMsmqBinding/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF hitelesítés letiltása, az MSMQ-várólista üzenetek kézbesítése történő csatlakozás során.
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
Mindig a Windows-tartomány vagy Tanúsítványalapú hitelesítés megkövetelése minden bejövő vagy kimenő üzenet az MSMQ beállítása.

### <a name="example"></a>Példa
A `<netMsmqBinding/>` elem a WCF konfigurációs fájl az alábbi arra utasítja a WCF-tanúsítványokon alapuló hitelesítés engedélyezése az MSMQ-várólista történő csatlakozás során. Az ügyfél hitelesítése X.509-tanúsítványokat használ. Az ügyféltanúsítvány szerepelnie kell a kiszolgáló tanúsítványtárolójában.
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
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél-hitelesítő adat típusa: nincs |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Hitelesítés hiányában azt jelenti, hogy mindenki érhessék el az ezt a szolgáltatást. Egy szolgáltatás, amely nem hitelesíti az ügyfeleket lehetővé teszi a hozzáférést minden felhasználó számára. Az alkalmazás ügyfél-hitelesítő adatok hitelesítése beállítása. Ezt megteheti úgy, hogy az üzenet clientCredentialType Windows vagy a tanúsítvány. |

### <a name="example"></a>Példa
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF-ne beállítása Transport clientCredentialType nincs

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél-hitelesítő adat típusa: nincs |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Hitelesítés hiányában azt jelenti, hogy mindenki érhessék el az ezt a szolgáltatást. Egy szolgáltatás, amely nem hitelesíti az ügyfeleket lehetővé teszi, hogy minden felhasználó a funkciók eléréséhez. Az alkalmazás ügyfél-hitelesítő adatok hitelesítése beállítása. Ezt megteheti úgy, hogy az átviteli clientCredentialType Windows vagy a tanúsítvány. |

### <a name="example"></a>Példa
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Győződjön meg arról, hogy szabványon alapuló hitelesítési módszerek segítségével biztonságos webes API-k

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Hitelesítési és engedélyezési ASP.NET webes API](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [külső hitelesítési szolgáltatások a ASP.NET Web API (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Lépések** | <p>Hitelesítés az a folyamat, ha egy entitás igazolja magát, általában a hitelesítő adatokat, például egy felhasználónevet és jelszót. Nincsenek több hitelesítési protokollok érhető el lehet tekinteni. Ezek közül az alábbiak:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlap alapú</li><li>Összevonási - AD FS</li><li>Összevonási – az Azure AD</li><li>Összevonási - Identitáskiszolgálók</li></ul><p>Hivatkozásokra a references szakaszában alacsony szintű részletekkel szolgálnak hogyan minden, a hitelesítési sémák végrehajtható egy webes API biztonságossá tételéhez.</p>|

## <a id="authn-aad"></a>Használja az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvei

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Hitelesítési forgatókönyvek az Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory-Kódminták](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory fejlesztői útmutatója](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Lépések** | <p>Azure Active Directory (Azure AD) egyszerűbbé teszi a fejlesztők hitelesítési azáltal identitás szolgáltatásként, például az OAuth 2.0 és az OpenID Connect szabványos protokollt támogat. Az alábbiakban az Azure AD által támogatott öt elsődleges alkalmazás forgatókönyveket:</p><ul><li>Böngészőben webalkalmazásokhoz: A felhasználó nem egy webalkalmazást, amelyet az Azure AD bejelentkezés</li><li>Egyetlen lap alkalmazás (SPA): A felhasználó be kell jelentkeznie, amelyet az Azure AD alkalmazás egylapos</li><li>Webes API a natív alkalmazás: A natív alkalmazást egy telefon, a tábla vagy a számítógépen futó kell hitelesíteni a felhasználót, hogy egy webes API-t az Azure AD által védett erőforrások lekérése</li><li>Webes API-webalkalmazás: webalkalmazás kell egy webes API-t az Azure AD által védett erőforrások lekérése</li><li>Démon vagy webes API kiszolgálói alkalmazás: egy démon alkalmazást vagy egy kiszolgálói alkalmazás webes felhasználói felület nélkül kell egy webes API-t az Azure AD által védett erőforrások lekérése</li></ul><p>Tekintse meg a hivatkozásokra a references szakaszában, az alacsony szintű megvalósítás részletei</p>|

## <a id="adal-scalable"></a>Az alapértelmezett ADAL jogkivonat gyorsítótára méretezhető alternatív felülbírálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Modern hitelesítést és az Azure Active Directory-webalkalmazások számára történő](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [ADAL jogkivonatok gyorsítótárát, Redis használatával](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Lépések** | <p>Az alapértelmezett gyorsítótárhoz adal-t (Active Directory Authentication Library) használ a memóriabeli gyorsítótár, amely statikus üzlet, elérhető folyamat kiterjedő támaszkodik. Ez a natív alkalmazások esetén használható, amíg azt nem méretezhető a középső réteg és a háttérkiszolgáló alkalmazások a következők lehetnek az okai:</p><ul><li>Ezek az alkalmazások által elért sok felhasználó egyszerre. Minden hozzáférési jogkivonatok mentése ugyanabban a tárolóban elkülönítési problémák hoz létre, és megadja a kihívásokkal léptékű működő: számos annyi jogkivonatokat, mint az erőforrások, az alkalmazás hozzáfér a nevükben, a felhasználó azt hatalmas számok és nagyon költséges keresési művelet</li><li>Ezek az alkalmazások általában telepített Elosztott topológia, ahol több csomópont hozzáféréssel kell rendelkeznie a azonos gyorsítótár</li><li>Gyorsítótárazott jogkivonatok folyamat újrahasznosítja azt és deactivations tovább kell működnie</li></ul><p>A fenti okokból összes közben implementing web apps, ajánlott felülbírálhatja az alapértelmezett ADAL jogkivonatok gyorsítótárát egy méretezhető alternatív, például az Azure Redis Cache-gyorsítótár.</p>|

## <a id="tokenreplaycache-adal"></a>Győződjön meg arról, hogy TokenReplayCache annak megelőzésére szolgál az ADAL-hitelesítés jogkivonatok ismétlés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Modern hitelesítést és az Azure Active Directory-webalkalmazások számára történő](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Lépések** | <p>A TokenReplayCache tulajdonság lehetővé teszi a fejlesztők számára adja meg a hitelesítési karakterláncok ismétlésének gyorsítótár, a tárolóban, nem használható annak ellenőrzése, hogy nincs lexikális elem egynél többször is használható céljából jogkivonatok mentése.</p><p>Ez a mérték a közös támadás, a szolgáltatási hívott hitelesítési karakterláncok ismétlésének támadásokkal szemben: támadó elfogja a rendszer a következő bejelentkezési token előfordulhat, hogy próbálja újból elküldeni az alkalmazás ("" játssza le újra) egy új munkamenet létrehozásának. Például a OIDC kód-támogatás folyamata, a felhasználó sikeres hitelesítés után, a kérelem "/ signin-oidc" végpont esetén a függő entitás történik, a "id_token", "code" és "állapot" paraméterek.</p><p>A függő entitás ellenőrzi ezt a kérelmet, és egy új munkamenetet létesít. Az ellenfél rögzíti ezt a kérelmet, és replays azt, ha többé sikeres munkamenetet hozhat létre, és a felhasználó hamisításának. Az OpenID Connect nonce jelenléte korlátozza, de nem teljes mértékben kiküszöbölhetők a körülmények között, amelyben a támadás is kell sikeresen léptetni. Az alkalmazások védelme érdekében fejlesztők ITokenReplayCache megvalósítást, és egy példány hozzárendelése TokenReplayCache.</p>|

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
Íme egy példa a ITokenReplayCache illesztőfelület megvalósítása. (Adja testreszabása és a projektre vonatkozó gyorsítótárazási keretrendszer megvalósítása)
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
A megvalósított gyorsítótár tartalmaz mutató hivatkozás fog megjelenni "TokenValidationParameters" tulajdonságon keresztül OIDC lehetőségek az alábbiak szerint.
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

Ne feledje, hogy ez a konfiguráció a helyi OIDC által védett alkalmazásba bejelentkezési hatékonyságát, és a kérelem rögzítését `"/signin-oidc"` a fiddler végpont. Ha a védelem nem van beállítva, a kérelem a fiddler visszajátszását állítja be egy új munkamenetcookie-t. Ha a kérelmet a rendszer játssza vissza a TokenReplayCache védelem hozzáadása után, az alkalmazás fogja kivételt jelez az alábbiak szerint:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Segítségével ADAL könyvtárakat kezelheti a jogkivonat-kérelmeket az OAuth2-ügyfelektől az aad-be (vagy a helyszíni AD)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Lépések** | <p>Az Azure AD authentication Library (ADAL) lehetővé teszi az alkalmazásfejlesztők ügyfél könnyen a felhőbe a felhasználók hitelesítéséhez vagy a helyszíni Active Directory (AD), és majd a védelmét biztosító API-hívások hozzáférési tokenek beszerzése érdekében.</p><p>ADAL számos lehetőséggel rendelkezik, hogy ellenőrizze a hitelesítési megkönnyíti a fejlesztők számára, például aszinkron támogatását, a konfigurálható jogkivonatok gyorsítótárát, amely tárolja a hozzáférési jogkivonatok és a frissítési jogkivonatokat, automatikus token frissítés, ha az előző lejár, és egy frissítési jogkivonat érhető el, és További.</p><p>Kezelnek összetettségét többségét, ADAL fejlesztői szűkítheti az üzleti logika tudnak biztosítani az alkalmazásokban, és anélkül, hogy a biztonsági szakértő könnyen erőforrások biztonságossá tételére. Külön szalagtárak .NET, JavaScript (ügyfél és a Node.js), iOS, Android és Java esetében érhetők el.</p>|

## <a id="authn-devices-field"></a>A mező átjáró csatlakozó eszközök hitelesítéséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy minden eszköz hitelesítése a mező átjáró elfogadása azokat az adatokat és az átjáró a felsőbb réteggel való kommunikáció biztosítása előtt. Emellett győződjön meg arról, hogy eszközök csatlakoznak-e a egy eszközönként hitelesítőadat-, hogy az egyes eszközökről egyedileg azonosítható.|

## <a id="authn-devices-cloud"></a>Győződjön meg arról, hogy az átjáró a kapcsolódó eszközök hitelesítése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, C#, Node.JS,  |
| **Attribútumok**              | Nincs; átjáró choice - Azure IoT Hub |
| **Hivatkozások**              | Nincs; [.NET Azure IoT hubot](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [egyidejűleg az IoT-központ bevezetés és csomópont JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [SAS és tanúsítványok védelmét biztosító IoT](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Git-tárház](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Lépések** | <ul><li>**Általános:** hitelesíteni az eszközt használja a Transport Layer Security (TLS) vagy az IPSec protokollt. Infrastruktúra támogatnia kell a előmegosztott kulccsal (PSK), amely nem tudja kezelni a teljes aszimmetrikus titkosítási eszközökön. Használja ki az Azure AD Oauth.</li><li>**C#:** alapértelmezés szerint egy DeviceClient példány létrehozásakor a Create metódussal létrehoz egy AMQP protokollt használó kommunikációra az IoT-központ DeviceClient példányt. A HTTPS protokollt használja a Create metódus, amely lehetővé teszi a protokoll adja meg a felülbírálás. Ha a HTTPS protokollt használ, akkor is fel kell a `Microsoft.AspNet.WebApi.Client` közé tartozik a projektet a NuGet-csomagot a `System.Net.Http.Formatting` névtér.</li></ul>|

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
* Létrehoz egy IoT-központot az azure-on
* Az eszközidentitási bejegyzés létrehozása
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* A szimulált eszköz létrehozásához
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>SAS-azonosító
* Lekérdezi belsőleg generáltak, de szimmetrikus kulcs használata esetén is létrehozhatják és explicit módon is használhatják
* Egy protokoll megadása:`var Http = require('azure-iot-device-http').Http;`
* Hozzon létre egy sas-jogkivonat:
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
* Létrehozni egy önaláírt aláírt X509 tanúsítvány eszközzel bármely OpenSSL például a tanúsítvány és a kulcs tárolására, illetve egy .cert és .key fájlok létrehozása
* Egy eszköz, amely támogatja a tanúsítványok segítségével biztonságos kapcsolatot kiépítéséhez.
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
* Egy olyan tanúsítványt használ eszköz csatlakoztatása
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

## <a id="authn-cred"></a>Eszköz hitelesítő adatok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway  | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Átjáró choice - Azure IoT Hub |
| **Hivatkozások**              | [Az Azure IoT-központ biztonsági jogkivonatokat](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Lépések** | Egy eszköz hitelesítő adatok használatával SaS-tokenje használata eszközkulcs vagy ügyféltanúsítvány alapján, helyett az IoT Hub-szintű megosztott elérési házirendeket. Ez megakadályozza, hogy a hitelesítési tokenek az egy eszközre vagy mező átjáró használatának egy másik |

## <a id="req-containers-anon"></a>Győződjön meg arról, hogy csak a szükséges tárolók és blobok névtelen olvasási hozzáférést kap

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Hivatkozások**              | [Tárolók és blobok névtelen olvasási hozzáférés kezelése](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [megosztott hozzáférési aláírásokkal, 1. rész: az SAS-modell ismertetése](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Lépések** | <p>Alapértelmezés szerint a tároló és szereplő bármely BLOB érhetik csak a tárfiók tulajdonosa által. Lehetőséget nyújt a névtelen felhasználóknak olvasási engedéllyel a tároló és a benne található blobokat, egy beállíthatja a tároló engedélyeit, nyilvános hozzáférés engedélyezéséhez. Névtelen felhasználók olvashatják a nyilvánosan elérhető tárolóban található blobok a kérelem hitelesítése nélkül.</p><p>Tárolók adja meg a tároló hozzáférés a következő beállításokat:</p><ul><li>Teljes nyilvános olvasási hozzáférés: névtelen kérelem keresztül olvasható tároló és a blob adatait. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok enumerálása, de nem tudja felsorolni a tárolók a tárfiókon belül.</li><li>Nyilvános olvasási hozzáférés a blobok csak: Blobadatok található olvasható névtelen kérelem keresztül, de adatai nem érhető el. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok nem tudja felsorolni.</li><li>Nincs nyilvános olvasási hozzáférés: tároló és a blob adatait csak a fiók tulajdonosának tudja olvasni.</li></ul><p>Névtelen hozzáférés esetén ajánlott forgatókönyvek, ahol egyes blobok mindig elérhetőknek kell lenniük a névtelen olvasási hozzáférés. Pontosabban megbízhatatlanná vezérlő egyik hozhat létre egy közös hozzáférésű jogosultságkódot, amely lehetővé teszi a különböző jogosultságokkal korlátozott delegált hozzáférés és a megadott időtartam alatt. Győződjön meg arról, hogy tárolók és blobok, amelyek vélhetően tartalmazhatnak bizalmas adatokat, nem hozzáférő névtelen véletlenül</p>|

## <a id="limited-access-sas"></a>Az Azure storage-ban SAS vagy SAP objektumok korlátozott hozzáférést

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozások**              | [A közös hozzáférésű Jogosultságkód, 1. rész: Ismertetése a SAS-modell](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [megosztott hozzáférési aláírásokkal, 2. rész: létrehozása és SAS-kód használatát a Blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [hogyan adhat hozzáférést a felhasználó fiókját megosztott objektumok Hozzáférési aláírásokkal és tárolt hozzáférési házirendek](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Lépések** | <p>A közös hozzáférésű jogosultságkód (SAS) használata egy hatékony módját kínálja a tárfiókban lévő objektumokhoz korlátozott hozzáférés biztosítása más ügyfelek teszi közzé a hívóbetűre nélkül. A SAS URI, amely magában foglalja a lekérdezés-paraméterek az összes szükséges információt a tárolási erőforrásokhoz való hozzáférés hitelesítése. Az SA-kat a tárolási erőforrások eléréséhez az ügyfélnek csak kell átadni a SAS a megfelelő konstruktort vagy metódust.</p><p>SAS-kód is használhatja, ha lehetővé szeretné tenni a tárfiókban lévő erőforrásokhoz való hozzáférést, amelyek nem megbízható fiók kulccsal rendelkező ügyfél számára. A tárfiók kulcsait tartalmazzák mind az elsődleges és másodlagos kulcs, amelyek mindegyikét hozzáférést rendszergazdai fiókját és az összes benne lévő erőforrás. A fiókját a lehetőségét, amely rosszindulatú vagy gondatlan használatát teszi ki a fiókot kulcsok nyílik meg. Közös hozzáférésű jogosultságkód adjon meg egy biztonságos megoldás, amely lehetővé teszi, hogy más ügyfelek számára olvasási, írási és törlési megfelelően a jogosultságaitól és a fiókkulcsot nélkül a tárfiókban lévő adatokat.</p><p>Ha egy olyan logikai készlete, amelyek hasonló minden alkalommal, amikor paraméterek, a tárolt hozzáférési házirend (SAP) használata segítenek meghatározni. A tárolt házirend származó SAS használatával lehetővé teszi az adott SAS azonnal visszavonni, mert az ajánlott mindig használjon tárolt hozzáférési házirendek lehetőség.</p>|