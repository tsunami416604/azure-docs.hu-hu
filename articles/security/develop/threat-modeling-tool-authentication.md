---
title: Hitelesítés - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
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
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244634"
---
# <a name="security-frame-authentication--mitigations"></a>Biztonsági keret: Hitelesítés | Enyhítése 

| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás**    | <ul><li>[Fontolja meg egy szabványos hitelesítési mechanizmus használatát a webalkalmazáshitelesítéshez](#standard-authn-web-app)</li><li>[Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket](#handle-failed-authn)</li><li>[Fellépés vagy adaptív hitelesítés engedélyezése](#step-up-adaptive-authn)</li><li>[Az adminisztratív felületek megfelelő lezárása](#admin-interface-lockdown)</li><li>[Az elfelejtett jelszófunkciók biztonságos megvalósítása](#forgot-pword-fxn)</li><li>[A jelszó- és fiókházirend megvalósításának biztosítása](#pword-account-policy)</li><li>[Vezérlők megvalósítása a felhasználónevek számbavételének megakadályozására](#controls-username-enum)</li></ul> |
| **Adatbázis** | <ul><li>[Ha lehetséges, a Windows-hitelesítés használata az SQL Server kiszolgálóhoz való csatlakozáshoz](#win-authn-sql)</li><li>[Ha lehetséges, használja az Azure Active Directory-hitelesítést az SQL-adatbázishoz való csatlakozáshoz](#aad-authn-sql)</li><li>[SQL hitelesítési mód használata esetén győződjön meg arról, hogy a fiók- és jelszóházirend ek vannak érvényben az SQL-kiszolgálón](#authn-account-pword)</li><li>[Ne használjon SQL-hitelesítést a tartalmazott adatbázisokban](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Eszközönkénti hitelesítési hitelesítő adatok használata SaS-jogkivonatokkal](#authn-sas-tokens)</li></ul> |
| **Azure megbízhatósági határa** | <ul><li>[Az Azure többtényezős hitelesítésének engedélyezése az Azure-rendszergazdák számára](#multi-factor-azure-admin)</li></ul> |
| **Szolgáltatásháló-kapcsolat megbízhatósági határa** | <ul><li>[Névtelen hozzáférés korlátozása a Service Fabric-fürthöz](#anon-access-cluster)</li><li>[Annak ellenőrzése, hogy a Service Fabric ügyfél-csomópont tanúsítványa eltér-e a csomópont-csomópont tanúsítványtól](#fabric-cn-nn)</li><li>[Az AAD használata a hálófürtök ügyfelek hitelesítéséhez](#aad-client-fabric)</li><li>[Annak ellenőrzése, hogy a szolgáltatásháló-tanúsítványok jóváhagyott hitelesítésszolgáltatótól (CA) származnak](#fabric-cert-ca)</li></ul> |
| **Identitás-kiszolgáló** | <ul><li>[Az Identitáskiszolgáló által támogatott szabványos hitelesítési forgatókönyvek használata](#standard-authn-id)</li><li>[Az alapértelmezett Identitáskiszolgáló tokengyorsítótár felülbírálása skálázható alternatívával](#override-token)</li></ul> |
| **Gép megbízhatósági határa** | <ul><li>[Annak biztosítása, hogy az üzembe helyezett alkalmazás bináris fájljai digitálisaláírással legyenek elrendelve](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Hitelesítés engedélyezése MSMQ-várólistákhoz való csatlakozáskor a WCF-ben](#msmq-queues)</li><li>[WCF-Ne állítsa a Message clientCredentialType típust nincs re](#message-none)</li><li>[WCF-Ne állítsa a Transport clientCredentialType típust nincs re](#transport-none)</li></ul> |
| **Webes API** | <ul><li>[A webes API-k védelmére szabványos hitelesítési technikák alkalmazásának biztosítása](#authn-secure-api)</li></ul> |
| **Azure Hirdetés** | <ul><li>[Az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata](#authn-aad)</li><li>[Az alapértelmezett ADAL token-gyorsítótár felülbírálása skálázható alternatívával](#adal-scalable)</li><li>[Annak ellenőrzése, hogy a TokenReplayCache az ADAL hitelesítési tokenek visszajátszásának megakadályozására szolgál-e](#tokenreplaycache-adal)</li><li>[ADAL-kódtárak használatával kezelheti az OAuth2 ügyfelektől az AAD-ig (vagy a helyszíni AD-hez) érkező jogkivonat-kérelmeket](#adal-oauth2)</li></ul> |
| **IoT field átjáró** | <ul><li>[A Field Gateway-hez csatlakozó eszközök hitelesítése](#authn-devices-field)</li></ul> |
| **IoT felhőátjáró** | <ul><li>[A felhőátjáróhoz csatlakozó eszközök hitelesítésének ellenőrzése](#authn-devices-cloud)</li><li>[Eszközönkénti hitelesítési hitelesítő adatok használata](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy csak a szükséges tárolók és blobok kapnak névtelen olvasási hozzáférést](#req-containers-anon)</li><li>[Korlátozott hozzáférés az Azure storage-ban lévő objektumokhoz SAS vagy SAP használatával](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Fontolja meg egy szabványos hitelesítési mechanizmus használatát a webalkalmazáshitelesítéshez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | <p>A hitelesítés az a folyamat, amelynek során az entitás bizonyítja identitását, általában hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek figyelembe vehetők. Néhány ezek közül az alábbiakban felsorolt:</p><ul><li>Ügyféltanúsítványok</li><li>Windows alapú</li><li>Űrlapok alapján</li><li>Föderáció - ADFS</li><li>Összevonás – Azure Hirdetés</li><li>Összevonás – Identitáskiszolgáló</li></ul><p>Fontolja meg egy szabványos hitelesítési mechanizmus használatát a forrásfolyamat azonosítására</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | <p>A felhasználókat explicit módon hitelesítő alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket. A hitelesítési mechanizmusnak:</p><ul><li>A kiemelt erőforrásokhoz való hozzáférés megtagadása a hitelesítés sikertelenssé téve</li><li>Általános hibaüzenet megjelenítése a sikertelen hitelesítés és a hozzáférés megtagadása után</li></ul><p>A következő vizsgálat:</p><ul><li>Kiemelt erőforrások védelme sikertelen bejelentkezések után</li><li>Általános hibaüzenet jelenik meg a sikertelen hitelesítéskor, és a hozzáférés megtagadása esemény(ek)en</li><li>A fiókok le vannak tiltva a sikertelen kísérletek túl sok afeladatából</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Fellépés vagy adaptív hitelesítés engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | <p>Ellenőrizze, hogy az alkalmazás további engedéllyel rendelkezik (például fokozza vagy adaptív hitelesítést, többtényezős hitelesítéssel, például OTP küldésével SMS-ben, e-mailben stb., vagy ismételt hitelesítésre való rákérdezés), hogy a felhasználót megtámadják, mielőtt hozzáférést kapna a érzékeny információkat. Ez a szabály egy fiók vagy művelet kritikus módosítására is vonatkozik.</p><p>Ez azt is jelenti, hogy a hitelesítés kiigazítását úgy kell végrehajtani, hogy az alkalmazás helyesen érvényesítse a környezetfüggő engedélyezést, hogy ne engedélyezze a jogosulatlan manipulációt például paraméterilletéktelenítéssel.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Az adminisztratív felületek megfelelő lezárása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | Az első megoldás az, hogy csak egy bizonyos forrás IP-tartományból biztosít hozzáférést a felügyeleti felülethez. Ha ez a megoldás nem lenne lehetséges, mint mindig ajánlott egy lépés-up vagy adaptív hitelesítés kényszerítése a felügyeleti felületre való bejelentkezéshez |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Az elfelejtett jelszófunkciók biztonságos megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | <p>Az első dolog az, hogy ellenőrizze, hogy az elfelejtett jelszó és más helyreállítási útvonalak egy linket küldenek, beleértve az időkorlátos aktiválási tokent, nem pedig magát a jelszót. A hivatkozás átküldése előtt további hitelesítésre is szükség lehet a soft-tokenek (pl. SMS token, natív mobilalkalmazások stb.) alapján. Másodszor, ne zárja ki a felhasználói fiókot, amíg az új jelszó megszerzésének folyamata folyamatban van.</p><p>Ez szolgáltatásmegtagadási támadáshoz vezethet, amikor a támadó úgy dönt, hogy szándékosan zárolja a felhasználókat egy automatikus támadással. Harmadszor, amikor az új jelszókérés folyamatban van, a megjelenített üzenetet általánossá kell tenni a felhasználónevek számbavételének megakadályozása érdekében. Negyedszer, mindig lehetővé teszi a régi jelszavak használatát, és erős jelszóházirendet valósít meg.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>A jelszó- és fiókházirend megvalósításának biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Részletek | <p>A szervezeti szabályzatnak megfelelő jelszó- és fiókházirendet kell alkalmazni, és az ajánlott eljárásokat végre kell hajtani.</p><p>A találgatásos és szótáralapú találgatás oka elleni védelem érdekében: Erős jelszóházirendet kell bevezetni annak érdekében, hogy a felhasználók összetett jelszót hozzanak létre (pl. 12 karakter minimális hossza, alfanumerikus és speciális karakterek).</p><p>A fiókzárolási házirendek a következő módon hajthatók végre:</p><ul><li>**Puha lezárás:** Ez lehet egy jó választás részére megvéd -a használók ellen oktalan kényszerít támadás. Például, ha a felhasználó megad egy rossz jelszót háromszor az alkalmazás zárolhatja a fiókot egy percre annak érdekében, hogy lassítja a folyamatot a találgatás kényszerítve a jelszót így kevésbé nyereséges a támadó számára, hogy folytassa. Ha úgy volt, hogy végre kemény lock-out ellenintézkedések ebben a példában akkor elérné a "DoS" végleges zárolásával számlák. Másik lehetőségként az alkalmazás létrehozhat egy OTP-t (Egyszeri jelszó), és elküldheti a sávon kívül (e-mailen, sms-en stb.) a felhasználónak. Egy másik megközelítés a CAPTCHA végrehajtása lehet a sikertelen kísérletek küszöbértékének elérése után.</li><li>**Kemény lezárás:** Az ilyen típusú zárolást kell alkalmazni, ha azt észleli, hogy egy felhasználó megtámadja az alkalmazást, és a fiók végleges zárolásával ellensúlyozza őket, amíg a válaszcsapatnak nem volt ideje a kriminalisztikai műveletekelvégzésére. A folyamat után dönthet úgy, hogy visszaadja a felhasználónak a fiókját, vagy további jogi lépéseket tehet ellenük. Ez a megközelítés megakadályozza, hogy a támadó tovább behatoljon az alkalmazásba és az infrastruktúrába.</li></ul><p>Az alapértelmezett és kiszámítható fiókok elleni támadások elleni védelem érdekében ellenőrizze, hogy az összes kulcs és jelszó cserélhető-e, és a telepítési idő után jön-e létre vagy cserélődjön-e ki.</p><p>Ha az alkalmazásnak automatikusan létre kell hoznia a jelszavakat, győződjön meg arról, hogy a létrehozott jelszavak véletlenszerűek és magas entrópiával rendelkeznek.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Vezérlők megvalósítása a felhasználónevek számbavételének megakadályozására

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A felhasználónevek számbavételének megakadályozása érdekében minden hibaüzenetet általánossá kell tenni. Néha nem kerülheti el, hogy az információk olyan funkciókban szivárogjon ki, mint például a regisztrációs oldal. Itt sebességkorlátozó módszereket kell használnia, például a CAPTCHA-t a támadó automatikus támadásának megakadályozásához. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Ha lehetséges, a Windows-hitelesítés használata az SQL Server kiszolgálóhoz való csatlakozáshoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | OnPrem között |
| **Attribútumok**              | SQL verzió - minden |
| **Referencia**              | [SQL Server – Hitelesítési mód kiválasztása](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Lépéseket** | A Windows hitelesítés a Kerberos biztonsági protokollt használja, jelszóházirend-érvényesítést biztosít az erős jelszavak összetettségi ellenőrzése tekintetében, támogatja a fiókzárolást, és támogatja a jelszó lejáratát.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Ha lehetséges, használja az Azure Active Directory-hitelesítést az SQL-adatbázishoz való csatlakozáshoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure |
| **Attribútumok**              | SQL verzió - V12 |
| **Referencia**              | [Csatlakozás SQL-adatbázishoz az Azure Active Directory-hitelesítés használatával](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Lépéseket** | **Minimális verzió:** Az Azure SQL Database V12 szükséges ahhoz, hogy az Azure SQL Database a Microsoft Directory ellen használhassa az AAD-hitelesítést |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>SQL hitelesítési mód használata esetén győződjön meg arról, hogy a fiók- és jelszóházirend ek vannak érvényben az SQL-kiszolgálón

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [SQL Server jelszóházirend](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Lépéseket** | Az SQL Server-hitelesítés használatakor a bejelentkezések olyan SQL Server rendszerben jönnek létre, amelyek nem Windows felhasználói fiókokon alapulnak. Mind a felhasználónév, mind a jelszó az SQL Server használatával jön létre, és az SQL Server rendszerben tárolódik. Az SQL Server használhatja a Windows jelszóházirend-mechanizmusait. A Windows rendszerben használt összetettségi és elévülési házirendeket alkalmazhatja az SQL Server en belül használt jelszavakra. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Ne használjon SQL-hitelesítést a tartalmazott adatbázisokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | OnPrem, SQL Azure |
| **Attribútumok**              | SQL verzió - MSSQL2012, SQL verzió - V12 |
| **Referencia**              | [Biztonsági gyakorlati tanácsok a tartalmazott adatbázisokkal](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Lépéseket** | A kényszerített jelszóházirend hiánya növelheti annak valószínűségét, hogy egy tartalmazott adatbázisban gyenge hitelesítő adatok jönnek létre. Használja ki a Windows-hitelesítést. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Eszközönkénti hitelesítési hitelesítő adatok használata SaS-jogkivonatokkal

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Eseményközpontok hitelesítése és biztonsági modellje – áttekintés](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépéseket** | <p>Az Event Hubs biztonsági modellje a megosztott hozzáférésű aláírás (SAS) jogkivonatok és az eseményközzétevők kombinációján alapul. A közzétevő neve a jogkivonatot fogadó DeviceID-t jelöli. Ez segítene a létrehozott jogkivonatok társításában a megfelelő eszközökkel.</p><p>Minden üzenet van címkézve a kezdeményező a szolgáltatási oldalon, amely lehetővé teszi kimutatására in-hasznos eredetű hamisítási kísérletek. Eszközök hitelesítéseke kor hozzon létre egy eszközönkénti SaS-jogkivonat hatókörét egy egyedi közzétevő.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Az Azure többtényezős hitelesítésének engedélyezése az Azure-rendszergazdák számára

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Mi az az Azure Multi-Factor Authentication?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Lépéseket** | <p>A többtényezős hitelesítés (MFA) egy olyan hitelesítési módszer, amely egynél több ellenőrzési módszert igényel, és kritikus második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókhoz. Úgy működik, hogy a következő ellenőrzési módszerek közül kettőre vagy többre van szükség:</p><ul><li>Valami, amit tud (általában egy jelszó)</li><li>Valami, ami van (egy megbízható eszköz, amely nem könnyen duplikált, mint egy telefon)</li><li>Valami, amit (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Névtelen hozzáférés korlátozása a Service Fabric-fürthöz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure  |
| **Referencia**              | [A Service Fabric fürtjének biztonsági forgatókönyvei](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Lépéseket** | <p>A fürtöket mindig biztosítani kell, hogy illetéktelen felhasználók ne csatlakozhassanak a fürthöz, különösen akkor, ha éles számítási feladatok futnak rajta.</p><p>A szolgáltatásháló-fürt létrehozásakor győződjön meg arról, hogy a biztonsági mód "biztonságos" állapotra van állítva, és konfigurálja a szükséges X.509 kiszolgálói tanúsítványt. Egy "nem biztonságos" fürt létrehozása lehetővé teszi, hogy bármely névtelen felhasználó csatlakozzon hozzá, ha felügyeleti végpontokat tesz elérhetővé a nyilvános interneten.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Annak ellenőrzése, hogy a Service Fabric ügyfél-csomópont tanúsítványa eltér-e a csomópont-csomópont tanúsítványtól

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure, Környezet - Önálló |
| **Referencia**              | [Service Fabric ügyfél-csomópont tanúsítvány biztonsága](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Csatlakozás biztonságos fürthöz ügyféltanúsítvánnyal](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Lépéseket** | <p>Az ügyfél-csomópont tanúsítvány biztonsága konfigurálva van, miközben a fürt az Azure Portalon, az Erőforrás-kezelő sablonokon vagy egy önálló JSON-sablonon keresztül, rendszergazdai ügyféltanúsítvány és/vagy felhasználói ügyféltanúsítvány megadásával jön létre.</p><p>A megadott rendszergazdai ügyfél- és felhasználói ügyféltanúsítványoknak különbözniük kell a csomópont-csomópont biztonsághoz megadott elsődleges és másodlagos tanúsítványoktól.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Az AAD használata a hálófürtök ügyfelek hitelesítéséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure |
| **Referencia**              | [Fürtbiztonsági forgatókönyvek – Biztonsági javaslatok](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Lépéseket** | Az Azure-on futó fürtök is biztonságos hozzáférést biztosíthatnak a felügyeleti végpontokhoz az Azure Active Directory (AAD) használatával, az ügyféltanúsítványokon kívül. Az Azure-fürtök esetében ajánlott AAD-biztonság használatával hitelesíteni az ügyfeleket és a tanúsítványokat a csomópontok között történő biztonság érdekében.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Annak ellenőrzése, hogy a szolgáltatásháló-tanúsítványok jóváhagyott hitelesítésszolgáltatótól (CA) származnak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Szolgáltatásháló-kapcsolat megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet - Azure |
| **Referencia**              | [X.509 tanúsítványok és Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Lépéseket** | <p>A Service Fabric X.509 kiszolgálói tanúsítványokat használ a csomópontok és az ügyfelek hitelesítéséhez.</p><p>Néhány fontos dolog, amelyet figyelembe kell venni a tanúsítványok szolgáltatáshálókban való használata során:</p><ul><li>Az éles számítási feladatokat futtató fürtökben használt tanúsítványokat egy megfelelően konfigurált Windows Server tanúsítványszolgáltatás használatával kell létrehozni, vagy egy jóváhagyott hitelesítésszolgáltatótól kell beszerezni. A hitelesítésszolgáltató lehet jóváhagyott külső hitelesítésszolgáltató vagy megfelelően felügyelt belső nyilvános kulcsinfrastruktúra (PKI)</li><li>Soha ne használjon olyan ideiglenes vagy teszttanúsítványokat a gyártásban, amely olyan eszközökkel jön létre, mint a MakeCert.exe</li><li>Önaláírt tanúsítványt is használhat, de ezt csak tesztfürtöknél teheti meg, éles környezetben nem.</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Az Identitáskiszolgáló által támogatott szabványos hitelesítési forgatókönyvek használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [IdentityServer3 - A nagy kép](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Lépéseket** | <p>Az alábbiakban az Identity Server által támogatott tipikus interakciókat találjuk:</p><ul><li>A böngészők webalkalmazásokkal kommunikálnak</li><li>A webalkalmazások webes API-kkal kommunikálnak (néha önmagukban, néha a felhasználó nevében)</li><li>A böngészőalapú alkalmazások webes API-kkal kommunikálnak</li><li>A natív alkalmazások webes API-kkal kommunikálnak</li><li>A kiszolgálóalapú alkalmazások webes API-kkal kommunikálnak</li><li>A webes API-k webes API-kkal kommunikálnak (néha önmagukban, néha egy felhasználó nevében)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Az alapértelmezett Identitáskiszolgáló tokengyorsítótár felülbírálása skálázható alternatívával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Identitáskiszolgáló telepítése – gyorsítótárazás](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépéseket** | <p>Az IdentityServer egyszerű beépített memóriás gyorsítótárral rendelkezik. Bár ez jó a kis méretű natív alkalmazások, nem méretezhető a középrétegű és a háttéralkalmazások a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat egyszerre sok felhasználó éri el. Az összes hozzáférési jogkivonat ugyanabban az üzletben történő mentése elkülönítési problémákat okoz, és kihívást jelent a nagy méretekben végzett működés során: sok felhasználó, amelyek mindegyike annyi jogkivonattal rendelkezik, mint az alkalmazás által a nevükben elért erőforrások, hatalmas számokat és nagyon költséges keresési műveleteket jelenthetnek</li><li>Ezek az alkalmazások általában elosztott topológiákon vannak telepítve, ahol több csomópontnak kell hozzáférnie ugyanahhoz a gyorsítótárhoz</li><li>A gyorsítótárazott tokeneknek túl kell élniük a folyamat-újrahasznosításokat és inaktiválásokat</li><li>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülírni az alapértelmezett Identity Server tokengyorsítótárát egy méretezhető alternatívával, például az Azure Cache for Redis-szel.</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Annak biztosítása, hogy az üzembe helyezett alkalmazás bináris fájljai digitálisaláírással legyenek elrendelve

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy az üzembe helyezett alkalmazás bináris fájljai digitálisaláírással vannak eltéve, hogy a bináris fájlok sértetlensége ellenőrizhető legyen|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Hitelesítés engedélyezése MSMQ-várólistákhoz való csatlakozáskor a WCF-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Lépéseket** | A program nem engedélyezi a hitelesítést az MSMQ-várólistákhoz való csatlakozáskor, a támadó névtelenül küldhet üzeneteket a várólistába feldolgozásra. Ha a hitelesítés nem használható egy másik programnak küldött üzenet kézbesítéséhez használt MSMQ-várólistához való csatlakozáshoz, a támadó rosszindulatú névtelen üzenetet küldhet.|

### <a name="example"></a>Példa
Az `<netMsmqBinding/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-et, hogy tiltsa le a hitelesítést, amikor msmq-várólistához csatlakozik az üzenet kézbesítéséhez.
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
Konfigurálja úgy az MSMQ-t, hogy a bejövő vagy kimenő üzenetekhez bármikor megkövetelje a Windows-tartomány vagy tanúsítvány hitelesítését.

### <a name="example"></a>Példa
Az `<netMsmqBinding/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-et, hogy az MSMQ-várólistához való csatlakozáskor engedélyezze a tanúsítványhitelesítést. Az ügyfél hitelesítése X.509-es tanúsítványokkal történik. Az ügyféltanúsítványnak meg kell jelennie a kiszolgáló tanúsítványtárolójában.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Ne állítsa a Message clientCredentialType típust nincs re

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa - Nincs |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Sztify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Lépéseket** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet ehhez a szolgáltatáshoz. Az ügyfeleket nem hitelesítő szolgáltatás minden felhasználó számára lehetővé teszi a hozzáférést. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ez úgy teheti meg, hogy az clientCredentialType üzenetet Windows vagy Tanúsítvány szolgáltatásra állítja. |

### <a name="example"></a>Példa
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Ne állítsa a Transport clientCredentialType típust nincs re

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, .NET keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa - Nincs |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Sztify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Lépéseket** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet ehhez a szolgáltatáshoz. Az olyan szolgáltatás, amely nem hitelesíti ügyfeleit, lehetővé teszi, hogy minden felhasználó hozzáférjen a funkcióihoz. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ez úgy teheti meg, hogy az átviteli clientCredentialType típust Windows vagy tanúsítvány ra állítja be. |

### <a name="example"></a>Példa
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>A webes API-k védelmére szabványos hitelesítési technikák alkalmazásának biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Hitelesítés és engedélyezés ASP.NET webes API-ban](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [külső hitelesítési szolgáltatások ASP.NET webes API-val (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Lépéseket** | <p>A hitelesítés az a folyamat, amelynek során az entitás bizonyítja identitását, általában hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek figyelembe vehetők. Néhány ezek közül az alábbiakban felsorolt:</p><ul><li>Ügyféltanúsítványok</li><li>Windows alapú</li><li>Űrlapok alapján</li><li>Föderáció - ADFS</li><li>Összevonás – Azure Hirdetés</li><li>Összevonás – Identitáskiszolgáló</li></ul><p>A hivatkozások szakaszban található hivatkozások alacsony szintű részleteket tartalmaznak arról, hogy az egyes hitelesítési sémák hogyan valósíthatók meg a webes API-k biztonságossá tétele érdekében.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Az Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Hitelesítési forgatókönyvek az Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Az Azure Active Directory-kódminták](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Az Azure Active Directory fejlesztői útmutatója](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Lépéseket** | <p>Az Azure Active Directory (Azure AD) leegyszerűsíti a fejlesztők hitelesítését azáltal, hogy az identitást szolgáltatásként biztosítja, és támogatja az olyan iparági szabványnak megfelelő protokollokat, mint az OAuth 2.0 és az OpenID Connect. Az alábbiakban az Azure AD által támogatott öt elsődleges alkalmazásforgatókönyv et talál:</p><ul><li>Webböngésző webalkalmazás: A felhasználónak be kell jelentkeznie egy olyan webalkalmazásba, amelyet az Azure AD biztosít</li><li>Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít</li><li>Natív alkalmazás webAPI: Egy natív alkalmazás, amely fut a telefon, táblagép vagy pc kell hitelesíteni a felhasználót, hogy az erőforrásokat egy webes API-t, amely az Azure AD által védett</li><li>Webalkalmazás webAPI-ra: Egy webalkalmazásnak erőforrásokat kell beszereznie az Azure AD által védett webes API-ból</li><li>Démon- vagy kiszolgálóalkalmazás webAPI-ra: Egy démonalkalmazásnak vagy egy webes felhasználói felület nélküli kiszolgálóalkalmazásnak erőforrásokat kell beszereznie az Azure AD által védett webes API-ból</li></ul><p>Kérjük, olvassa el a hivatkozások részben található hivatkozásokat az alacsony szintű megvalósítás részleteiről.</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Az alapértelmezett ADAL token-gyorsítótár felülbírálása skálázható alternatívával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Modern hitelesítés az Azure Active Directory webalkalmazásokhoz szolgáltatásával,](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) [ADAL tokengyorsítótárként való használata](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Lépéseket** | <p>Az ADAL (Active Directory authentication Library) által használt alapértelmezett gyorsítótár egy memórián belüli gyorsítótár, amely egy statikus tárolóra támaszkodik, amely az egész folyamatra kiterjed. Bár ez működik a natív alkalmazások, nem skálázható a középső rétegű és a háttéralkalmazások a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat egyszerre sok felhasználó éri el. Az összes hozzáférési jogkivonat ugyanabban az üzletben történő mentése elkülönítési problémákat okoz, és kihívást jelent a nagy méretekben végzett működés során: sok felhasználó, amelyek mindegyike annyi jogkivonattal rendelkezik, mint az alkalmazás által a nevükben elért erőforrások, hatalmas számokat és nagyon költséges keresési műveleteket jelenthetnek</li><li>Ezek az alkalmazások általában elosztott topológiákon vannak telepítve, ahol több csomópontnak kell hozzáférnie ugyanahhoz a gyorsítótárhoz</li><li>A gyorsítótárazott tokeneknek túl kell élniük a folyamat-újrahasznosításokat és inaktiválásokat</li></ul><p>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülbírálni az alapértelmezett ADAL-jogkivonat-gyorsítótárat egy méretezhető alternatívával, például az Azure Cache for Redis-szel.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Annak ellenőrzése, hogy a TokenReplayCache az ADAL hitelesítési tokenek visszajátszásának megakadályozására szolgál-e

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Modern hitelesítés az Azure Active Directory webalkalmazásokhoz szolgáltatásával](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Lépéseket** | <p>A TokenReplayCache tulajdonság lehetővé teszi a fejlesztők számára, hogy meghatározza a token visszajátszási gyorsítótár, egy áruház, amely használható a tokenek mentése céljából annak ellenőrzése, hogy nincs token használható egynél többször.</p><p>Ez egy intézkedés ellen egy közös támadás, a találóan nevezett token visszajátszás támadás: a támadó elfogó a token küldött bejelentkezési megpróbálhatja elküldeni az alkalmazásnak újra ("replay" it) létrehozásához egy új munkamenetet. Pl., AZ OIDC-kód-támogatási folyamat során a sikeres felhasználói hitelesítés után a függő entitás "/signin-oidc" végpontjára vonatkozó kérés "id_token", "kód" és "állapot" paraméterekkel történik.</p><p>A függő entitás érvényesíti ezt a kérést, és létrehoz egy új munkamenetet. Ha egy ellenfél rögzíti ezt a kérést, és visszajátssza azt, akkor létrehozhat egy sikeres munkamenetet, és meghamisíthatja a felhasználót. A nonce jelenléte az OpenID Connect-ben korlátozhatja, de nem szüntetheti meg teljesen azokat a körülményeket, amelyek között a támadás sikeresen életbe léptethető. Az alkalmazások védelme érdekében a fejlesztők biztosíthatják az ITokenReplayCache implementációját, és hozzárendelhetnek egy példányt a TokenReplayCache-hez.</p>|

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
Itt van egy minta megvalósítása az ITokenReplayCache felület. (Kérjük, szabja testre és valósítsa meg a projektspecifikus gyorsítótárazási keretrendszert)
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
A megvalósított gyorsítótárra a "TokenValidationParameters" tulajdonságon keresztül kell hivatkozni az OIDC-beállításokban az alábbiak szerint.
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

Kérjük, vegye figyelembe, hogy a konfiguráció hatékonyságának teszteléséhez jelentkezzen be a helyi `"/signin-oidc"` OIDC-védelemmel ellátott alkalmazásba, és rögzítse a végpontra vonatkozó kérést a hegedűsben. Ha a védelem nincs érvényben, a kérés fiddlerben való visszajátszása új munkamenet-cookie-t állít be. A tokenreplaycache-védelem hozzáadása után a kérelem visszajátszása után az alkalmazás kivételt fog kiadni az alábbiak szerint:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>ADAL-kódtárak használatával kezelheti az OAuth2 ügyfelektől az AAD-ig (vagy a helyszíni AD-hez) érkező jogkivonat-kérelmeket

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Lépéseket** | <p>Az Azure AD hitelesítési könyvtár (ADAL) lehetővé teszi az ügyfélalkalmazás-fejlesztők számára, hogy egyszerűen hitelesítsék a felhasználókat a felhőbe vagy a helyszíni Active Directoryba (AD), majd szerezzenbe hozzáférési jogkivonatokat az API-hívások védelméhez.</p><p>Az ADAL számos olyan szolgáltatással rendelkezik, amelyek megkönnyítik a hitelesítést a fejlesztők számára, például az aszinkron támogatást, a hozzáférési jogkivonatokat és a frissítési jogkivonatokat tároló konfigurálható jogkivonat-gyorsítótárat, az automatikus jogkivonat-frissítést, amikor egy hozzáférési jogkivonat lejár, és elérhető egy frissítési jogkivonat, és Több.</p><p>A legtöbb összetettség kezelésével az ADAL segíthet abban, hogy a fejlesztők az alkalmazásukban az üzleti logikára összpontosítsanak, és könnyen biztosíthassák az erőforrásokat anélkül, hogy biztonsági szakértőként dolgoznának. A .NET, JavaScript (ügyfél és Node.js), python, iOS, Android és Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>A Field Gateway-hez csatlakozó eszközök hitelesítése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy minden eszközt a Field Gateway hitelesít, mielőtt adatokat fogadna el tőlük, és mielőtt megkönnyítené a felhőátjáróval folytatott upstream kommunikációt. Győződjön meg arról is, hogy az eszközök eszközönkénti hitelesítő adatokkal csatlakoznak, hogy az egyes eszközök egyedileg azonosíthatók legyenek.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>A felhőátjáróhoz csatlakozó eszközök hitelesítésének ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, C#, Node.JS,  |
| **Attribútumok**              | N/A, Átjáró választás - Azure IoT Hub |
| **Referencia**              | N/A, [Azure IoT hub .NET,](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/) [Első lépések az IoT hub és a Node JS,](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted) [Az IoT biztonságossá tétele SAS-szal és tanúsítványokkal,](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) [Git-tárház](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Lépéseket** | <ul><li>**Általános:** Hitelesítse az eszközt a Transport Layer Security (TLS) vagy az IPSec használatával. Az infrastruktúrának támogatnia kell az előmegosztott kulcs (PSK) használatát azokon az eszközökön, amelyek nem tudják kezelni a teljes aszimmetrikus titkosítást. Használja ki az Azure AD,Oauth.</li><li>**C#:** DeviceClient-példány létrehozásakor alapértelmezés szerint a Create metódus létrehoz egy DeviceClient-példányt, amely az AMQP protokollt használja az IoT Hubbal való kommunikációhoz. A HTTPS protokoll használatához használja a Create metódus felülírását, amely lehetővé teszi a protokoll megadását. Ha a HTTPS protokollt használja, `Microsoft.AspNet.WebApi.Client` a NuGet csomagot is `System.Net.Http.Formatting` hozzá kell adnia a projekthez a névtér felvételéhez.</li></ul>|

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
**Node.JS: Hitelesítés**
#### <a name="symmetric-key"></a>Szimmetrikus kulcs
* IoT-központ létrehozása az azure-ban
* Bejegyzés létrehozása az eszközidentitás-beállításjegyzékben
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
  #### <a name="sas-token"></a>SAS-token
* Kap belsőleg generált használata esetén szimmetrikus kulcs, de tudjuk generálni, és használja azt explicit módon is
* Protokoll definiálása:`var Http = require('azure-iot-device-http').Http;`
* Hozzon létre egy sas token:
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
* Csatlakozás sas-token használatával: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Tanúsítványok
* Saját aláírású X509 tanúsítvány létrehozása bármely eszközzel, például az OpenSSL-el .cert és .key fájlok létrehozásához a tanúsítvány és a kulcs tárolásához
* Olyan eszköz kiépítése, amely tanúsítványok használatával fogadja a biztonságos kapcsolatot.
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

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Eszközönkénti hitelesítési hitelesítő adatok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró  | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választás - Azure IoT Hub |
| **Referencia**              | [Azure IoT Hub biztonsági jogkivonatok](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Lépéseket** | Eszközalapú hitelesítési hitelesítő adatok használata Az IDo Hub-szintű megosztott hozzáférési házirendek helyett eszközalapú hitelesítési hitelesítő adatok használatával eszközkulcs vagy ügyféltanúsítvány alapján. Ez megakadályozza, hogy egy eszköz vagy helyszíni átjáró hitelesítési tokenjeit egy másik |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Győződjön meg arról, hogy csak a szükséges tárolók és blobok kapnak névtelen olvasási hozzáférést

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType - Blob |
| **Referencia**              | [A tárolókhoz és blobokhoz való névtelen olvasási hozzáférés kezelése](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [Megosztott hozzáférésű aláírások, 1. rész: A SAS-modell ismertetése](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Lépéseket** | <p>Alapértelmezés szerint egy tároló és a benne lévő blobok csak a tárfiók tulajdonosa érhető el. Ahhoz, hogy a névtelen felhasználók olvasási engedélyeket adjanak egy tárolóhoz és annak blobjaihoz, beállíthatja a tároló engedélyeit a nyilvános hozzáférés engedélyezéséhez. A névtelen felhasználók a kérelem hitelesítése nélkül olvashatják a blobokat egy nyilvánosan elérhető tárolóban.</p><p>A tárolók a következő lehetőségeket biztosítják a tárolók elérésének kezeléséhez:</p><ul><li>Teljes nyilvános olvasási hozzáférés: A tároló- és blobadatok névtelen kérelemmel olvashatók. Az ügyfelek névtelen kéréssel felsorolhatják a tárolón belüli blobokat, de a tárfiókon belüli tárolókat nem.</li><li>Nyilvános olvasási hozzáférés csak blobok: Blob adatok ebben a tárolóban névtelen kérelem melusz kéréssel, de a tároló adatok nem érhetők el. Az ügyfelek névtelen kérelem melinációval nem sorolhatják fel a tárolón belüli blobokat</li><li>Nincs nyilvános olvasási hozzáférés: A tároló- és blobadatokat csak a fiók tulajdonosa olvashatja</li></ul><p>Névtelen hozzáférés a legjobb olyan forgatókönyvek, ahol bizonyos blobok mindig elérhető névtelen olvasási hozzáférés. A finomszemcsés vezérléshez létrehozhat unk egy megosztott hozzáférésű aláírást, amely lehetővé teszi a korlátozott hozzáférés delegálását különböző engedélyekkel és egy megadott időintervallumban. Győződjön meg arról, hogy a bizalmas adatokat potenciálisan tartalmazó tárolók és blobok véletlenül nem kapnak névtelen hozzáférést</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Korlátozott hozzáférés az Azure storage-ban lévő objektumokhoz SAS vagy SAP használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A |
| **Referencia**              | [Megosztott hozzáférésű aláírások, 1. rész: A SAS-modell ismertetése,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [megosztott hozzáférésű aláírások, 2. rész: SAS létrehozása és használata Blob tárterülettel,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/) [Hozzáférés delegálása a fiók objektumaihoz megosztott hozzáférésű aláírások és tárolt hozzáférési házirendek használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Lépéseket** | <p>A megosztott hozzáférésű aláírás (SAS) használatával hatékony módja annak, hogy korlátozott hozzáférést biztosítson a tárfiókobjektumaihoz más ügyfeleknek anélkül, hogy fiókhozzáférési kulcsot kellene elérhetővé tenni. A SAS egy URI, amely magában foglalja a lekérdezési paraméterek a tárolási erőforrás hitelesített eléréséhez szükséges összes információt. A SAS-szal való tárolási erőforrások eléréséhez az ügyfélnek csak a Megfelelő konstruktornak vagy metódusnak kell átadnia a SAS-t.</p><p>SAS-t akkor használhat, ha hozzáférést szeretne biztosítani a tárfiókban lévő erőforrásokhoz egy olyan ügyfélnek, amely nem megbízható a fiókkulccsal. A tárfiók kulcsai egy elsődleges és másodlagos kulcsot is tartalmaznak, amelyek rendszergazdai hozzáférést biztosítanak a fiókjához és a benne lévő összes erőforráshoz. A fiókkulcsok felfedése megnyitja fiókját a rosszindulatú vagy gondatlan használat lehetőségével. A megosztott hozzáférésű aláírások biztonságos alternatívát biztosítanak, amely lehetővé teszi más ügyfelek számára, hogy a megadott engedélyeknek megfelelően és a fiókkulcs szükségessége nélkül olvassanak, írjanak és töröljenek adatokat a tárfiókban.</p><p>Ha minden alkalommal hasonló paraméterek et használ, a tárolt hozzáférési szabályzat (SAP) használata jobb ötlet. Mivel a Tárolt hozzáférési házirendből származó SAS használatával azonnal visszavonhatja ezt a SAS-t, ajánlott mindig a tárolt hozzáférési házirendeket használni, ha lehetséges.</p>|
