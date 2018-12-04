---
title: Titkosítás – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
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
ms.openlocfilehash: 79803a749b6d08c94bcbf5f3ca66aac8b7294fa3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844651"
---
# <a name="security-frame-cryptography--mitigations"></a>Biztonsági keret: Titkosítás |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Csak a jóváhagyott szimmetrikus blokktitkosító és a kulcshosszok](#cipher-length)</li><li>[Használható jóváhagyott titkosítási mód letiltása és a szimmetrikus titkosítási inicializálási vektor ismert](#vector-ciphers)</li><li>[Jóváhagyott aszimmetrikus algoritmusok, kulcshosszok és kitöltési használata](#padding)</li><li>[Jóváhagyott véletlenszerű szám generátorok használata](#numgen)</li><li>[Ne használja a szimmetrikus stream Rejtjelek](#stream-ciphers)</li><li>[Jóváhagyott HMAC-val, MAC és beállítható kivonatoló algoritmusok használata](#mac-hash)</li><li>[Csak a jóváhagyott titkosítási kivonatot funkciók használata](#hash-functions)</li></ul> |
| **Adatbázis** | <ul><li>[Erős titkosítási algoritmusok használatával titkosítja az adatokat az adatbázisban](#strong-db)</li><li>[SSIS-csomagok titkosítást és digitálisan aláírt](#ssis-signed)</li><li>[Adja hozzá a digitális aláírás kritikus adatbázis biztonságos elemekhez](#securables-db)</li><li>[SQL server EKM védelme érdekében titkosítási kulcsok használata](#ekm-keys)</li><li>[AlwaysEncrypted szolgáltatást használhatja, ha a titkosítási kulcsok nem szabad felfedni adatbázismotor](#keys-engine)</li></ul> |
| **IoT-eszköz** | <ul><li>[Titkosítási kulcsok biztonságos Store IoT-eszköz](#keys-iot)</li></ul> | 
| **IoT átjáró** | <ul><li>[Hozzon létre egy véletlenszerű szimmetrikus kulcs megfelelő hosszúságú az IoT Hub-hitelesítés](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile ügyfél** | <ul><li>[Győződjön meg arról, egy másik felügyeleti szabályzat van érvényben, amely egy PIN-kód használatát igényli, és lehetővé teszi a távoli törlése](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-ügyfelének** | <ul><li>[Győződjön meg, hogy egy másik felügyeleti szabályzat, amely egy PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker)](#bitlocker)</li></ul> | 
| **Identitáskezelési kiszolgáló** | <ul><li>[Győződjön meg arról, hogy aláíró kulcsok vannak leváltani Identitáskiszolgálók használatakor](#rolled-server)</li><li>[Gondoskodjon arról, hogy erős titkosítási szempontból ügyfél-azonosító, titkos Ügyfélkód használt az Identity Serverben](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Csak a jóváhagyott szimmetrikus blokktitkosító és a kulcshosszok

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Termékek csak szimmetrikus blokktitkosító és társított kulcshosszokat, amely kifejezetten a kriptográfiai Advisor a szervezet által jóváhagyott kell használnia. A Microsoft engedélyezett szimmetrikus algoritmusok belefoglalása a következő blokktitkosító:</p><ul><li>Új kódot AES-128, az AES-192 és az AES-256-ot is elfogadható</li><li>A meglévő kódot a visszamenőleges kompatibilitás érdekében három-kulcsot a 3DES fogadható el</li><li>A termékek szimmetrikus blokktitkosító használatával:<ul><li>Advanced Encryption Standard (AES) az új kód megadása kötelező</li><li>Három kulcsot triple Data Encryption Standard (3DES) érték átadása is megengedett a meglévő kódot a visszamenőleges kompatibilitás érdekében</li><li>Az összes többi blokktitkosító, RC2, DES, 2 kulcs 3DES, DESX és bonító, beleértve a régi adatok csak akkor használható, és le kell cserélni, ha a titkosításhoz használt</li></ul></li><li>Blokk szimmetrikus titkosítási algoritmusok 128 bites minimális kulcshosszúság szükség. Új kódja: AES esetében ajánlott csak blokk titkosítási algoritmus (AES-128, az AES-192 és az AES-256 megfelelőek az összes)</li><li>Három-kulcsot a 3DES jelenleg elfogadható, ha már használatban a meglévő kód; AES való használata javasolt. DES, DESX, RC2 és bonító rendszer már nem tekinthető biztonságosnak. Ezek az algoritmusok számára az előző verziókkal való kompatibilitás érdekében meglévő adatok csak akkor használható, és adatokat kell használatával újra titkosíthatja a javasolt blokktitkosító</li></ul><p>Vegye figyelembe, hogy a szimmetrikus blokktitkosító egy jóváhagyott titkosítási mód, amely egy megfelelő inicializáló vektort (IV) használatát igényli együtt kell használni. Egy megfelelő IV általában egy véletlenszerű számot, majd soha többé nem állandó érték</p><p>Örökölt vagy más módon nem jóváhagyott titkosítási algoritmusokat és kulcshosszokat kisebb használatát (szemben az új adatok) meglévő adat beolvasására vonatkozóan engedélyezhető a szervezet kriptográfiai tábla áttekintése után. Azonban meg kell fájlt egy kivételt a követelménnyel szemben. Emellett vállalati üzemelő példánya esetében a termékek megfontolandó figyelmeztetés rendszergazdák gyenge titkosítás segítségével adatokat olvasni. Az ilyen figyelmeztetést magyarázó és gyakorlatban hasznosítható kell lennie. Bizonyos esetekben célszerű lehet szeretné, hogy a csoportházirend szabályozza a gyenge titkosítás használata</p><p>Engedélyezett felügyelt titkosítási agilitás .NET algoritmusokat (sorrendben)</p><ul><li>AesCng (FIPS-kompatibilis)</li><li>AuthenticatedAesCng (FIPS-kompatibilis)</li><li>AESCryptoServiceProvider (FIPS-kompatibilis)</li><li>AESManaged (nem érvényes a FIPS-kompatibilis)</li></ul><p>Vegye figyelembe, hogy ezek az algoritmusok egyike segítségével megadható az `SymmetricAlgorithm.Create` vagy `CryptoConfig.CreateFromName` módszerek a machine.config fájl módosítása nélkül. Vegye figyelembe azt is, hogy a .NET, .NET 3.5-ös előtti verzióiban AES neve `RijndaelManaged`, és `AesCng` és `AuthenticatedAesCng` vannak > Codeplex webhelyen keresztül érhető el, és az alapjául szolgáló operációs rendszer a CNG szükséges</p>

## <a id="vector-ciphers"></a>Használható jóváhagyott titkosítási mód letiltása és a szimmetrikus titkosítási inicializálási vektor ismert

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Az egy jóváhagyott szimmetrikus titkosítási mód szimmetrikus blokktitkosító kell használni. A csak a jóváhagyott módok a következők: CBC és CTS. Különösen el kell kerülni a elektronikus kód könyv (ECB) módú művelet; ECB használatát a szervezet kriptográfiai tábla át kell. Minden használati OFB, a CFB, a Parancsra, a CCM, és a GCM vagy bármely más titkosítási mód a szervezet kriptográfiai táblára kell vizsgálnia. Újbóli felhasználása a azonos inicializáló vektort (IV) a blokktitkosítási "Rejtjelek módok streamelési" Parancsra, például az okozhatja, hogy a titkosított adatok megjeleníthetők. Szimmetrikus blokktitkosító egy megfelelő inicializáló vektort (IV) együtt is kell használni. Egy megfelelő IV egy titkosítási szempontból erős, véletlenszerű számot, és soha nem állandó értéket. |

## <a id="padding"></a>Jóváhagyott aszimmetrikus algoritmusok, kulcshosszok és kitöltési használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Kitiltott titkosítási algoritmusok használatát bemutatja a termék biztonsági kockázatot, és el kell kerülni. Termékek csak titkosítási algoritmusokat kell használnia, és társított kulcshosszokat és kitöltési által a szervezet kriptográfiai táblához explicit módon jóváhagyott.</p><ul><li>**Az RSA -** titkosítás, a kulcscsere és aláírás használhatók. RSA-titkosítás csak a OAEP vagy RSA-KEM kitöltés módokat kell használnia. Meglévő kódot felhasználhatja a PKCS #1 1.5-ös verzióját kitöltési mód csak a kompatibilitás. NULL értékű kitöltés használatát explicit módon le van tiltva. Kulcsok > = 2048 bit szükséges új kódot. Meglévő kódot támogathatják kulcsok < 2048 bites csak a visszamenőleges kompatibilitási a szervezet kriptográfiai panel szerint áttekintése után. Kulcsok a régi adatok visszafejtése/ellenőrzése csak akkor használható, < 1024 bit, és kicserélt if használható titkosítási vagy aláírási műveletekhez</li><li>**ECDSA -** csak aláírás használhatók. Az ECDSA > = 256 bites kulcsok új kód megadása kötelező. Aláírások ECDSA-alapú a három NIST jóváhagyott görbék egyikét kell használnia (p-256, p-384 vagy P521). Alaposan elemzett görbék csak a szervezet kriptográfiai tábla áttekintése után is használható.</li><li>**ECDH -** kulcscsere csak használhatók. A ECDH > = 256 bites kulcsok új kód megadása kötelező. A kulcscsere ECDH-alapú a három NIST jóváhagyott görbék egyikét kell használnia (p-256, p-384 vagy P521). Alaposan elemzett görbék csak a szervezet kriptográfiai tábla áttekintése után is használható.</li><li>**DSA -** áttekintésre és jóváhagyásra a szervezet kriptográfiai tábláról után elfogadható lehet. Lépjen kapcsolatba a biztonsági tanácsadó ütemezni a szervezet kriptográfiai tábla áttekintése. Ha jóváhagyja a DSA használatát, vegye figyelembe, hogy ki kell kulcsok használatát tiltó kisebb, mint 2048 bit hosszúságú. CNG támogatja a 2048-bites és a nagyobb kulcshosszúságok Windows 8-tól.</li><li>**Diffie-Hellman -** legfontosabb felügyeleti munkamenet használhatók. Kulcs hossza > = 2048 bit szükséges új kódot. Meglévő kódot támogathatja az kulcshosszok < 2048 bites csak a visszamenőleges kompatibilitási a szervezet kriptográfiai panel szerint áttekintése után. Kulcsok < 1024 bit nem használhatók.</li><ul>

## <a id="numgen"></a>Jóváhagyott véletlenszerű szám generátorok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Termékek jóváhagyott véletlenszerű szám generátorok kell használnia. Például a C futásidejű függvény rand, a .NET-keretrendszer osztály System.Random vagy rendszerfunkciók például GetTickCount pseudorandom funkciók kell, ezért soha nem használható ilyen kód. A kettős elliptikus görbe alapú véletlenszerű szám generátor (DUAL_EC_DRBG) algoritmus je zakázaná.</p><ul><li>**CNG -** BCryptGenRandom (a BCRYPT_USE_SYSTEM_PREFERRED_RNG jelző ajánlott, kivéve, ha a hívó bármely IRQ [, PASSIVE_LEVEL] 0-nál nagyobb lehet, hogy futtassa a használata)</li><li>**CAPI -** cryptGenRandom</li><li>**A Win32/64-** RtlGenRandom (új kell megvalósításokban BCryptGenRandom vagy CryptGenRandom) * rand_s * SystemPrng (a rendszermag módú)</li><li>**. NET -** RNGCryptoServiceProvider vagy RNGCng</li><li>**Windows Store Apps -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom vagy. GenerateRandomNumber</li><li>**Az Apple-OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef véletlenszerű, size_t száma, uint8_t \*bájt)</li><li>**Az Apple-OS X (< 10.7) –** használatával/dev/véletlenszerű véletlenszerű szám lekérése</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom osztály. Vegye figyelembe, hogy Android 4.3-as (ún Bean), a fejlesztők kell kövesse az ajánlott megoldás Android explicit módon inicializálása a PRNG /dev/urandom vagy /dev/random vysokou az alkalmazásaikat frissítése</li></ul>|

## <a id="stream-ciphers"></a>Ne használja a szimmetrikus stream Rejtjelek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | A szimmetrikus stream Rejtjelek, például az RC4-et, nem kell használni. A szimmetrikus stream Rejtjelek, helyett termékek blokktitkosító, különösen a kulcs hossza legalább 128 bites AES kell használnia. |

## <a id="mac-hash"></a>Jóváhagyott HMAC-val, MAC és beállítható kivonatoló algoritmusok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Termékek csak a jóváhagyott üzenethitelesítő kódot (MAC) vagy kivonatalapú üzenethitelesítési kód (HMAC) algoritmusok kell használnia.</p><p>Egy üzenethitelesítő kódot (MAC) olyan adat, egy üzenet, amely lehetővé teszi, hogy mind a küldő szerint a gazdaszámítógép és a titkos kulcs használatával üzenet sértetlenségének ellenőrzéséhez a címzett csatlakoztatva. Mindkét kivonat-alapú MAC használatát ([HMAC-val](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) vagy [titkosító-blokkalapú MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) megengedett, mennyi ideig, az összes alapul szolgáló kivonatoló vagy szimmetrikus titkosítási algoritmusok is jóváhagyott használatra; jelenleg ez magában foglalja a HMAC-SHA2-függvények (HMAC-SHA256 algoritmust, HMAC-SHA384 és HMAC-SHA512) és a CMAC/OMAC1 és OMAC2 letiltása a Mac számítógépek (ezek alapulnak AES) titkosító-alapú.</p><p>HMAC-SHA1 használata megengedett tartozó platform kompatibilitását, de akkor kell Kivételt képez ez az eljárás a fájl és a szervezet titkosítási felülvizsgálati mennek keresztül. Kisebb, mint 128 bit üzenetkivonatokat csonkolása nem engedélyezett. Egy kulcsot és az adatok nem engedélyezett, és kell alávetni, a szervezet kriptográfiai tábla kivonatoló metódusok használata előtt tekintse át ügyfél használatával.</p>|

## <a id="hash-functions"></a>Csak a jóváhagyott titkosítási kivonatot funkciók használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Termékek kell használniuk az SHA-2 család kivonatoló algoritmusok (SHA256, SHA384, és az SHA512). Rövidebb kivonatot szükség esetén például annak érdekében, hogy rövidebb MD5-kivonat szem előtt, a tervezett adatstruktúra fér 128 bites kimeneti hossza termékcsoportjai előfordulhat, hogy csonkolja a SHA2-kivonatok (általában az SHA256 titkosítást) egyikét. Vegye figyelembe, hogy SHA384 a SHA512 rövidített verzióját. Biztonsági okokból kisebb, mint 128 bit, kriptográfiai kivonatokat csonkolása nem engedélyezett. Új kódot kell használja a MD2, MD4, MD5, SHA-0, SHA-1 vagy RIPEMD kivonatoló algoritmus. Kivonatütköztetés olyan számításilag megvalósítható az ezeket az algoritmusokat, amely hatékonyan működésképtelenné válik őket.</p><p>Engedélyezett .NET kivonatoló algoritmusainak kiválasztásakor felügyelt titkosítási rugalmasságot (sorrendben):</p><ul><li>SHA512Cng (FIPS-kompatibilis)</li><li>SHA384Cng (FIPS-kompatibilis)</li><li>SHA256Cng (FIPS-kompatibilis)</li><li>SHA512Managed (nem érvényes a FIPS-kompatibilis) (használja a SHA512 algoritmust neveként ve volání do HashAlgorithm.Create vagy CryptoConfig.CreateFromName)</li><li>SHA384Managed (nem érvényes a FIPS-kompatibilis) (használja a SHA384 ve volání do HashAlgorithm.Create vagy CryptoConfig.CreateFromName algoritmus neve szerint)</li><li>SHA256Managed (nem érvényes a FIPS-kompatibilis) (használja a SHA256 algoritmust neveként ve volání do HashAlgorithm.Create vagy CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibilis)</li></ul>| 

## <a id="strong-db"></a>Erős titkosítási algoritmusok használatával titkosítja az adatokat az adatbázisban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Egy titkosítási algoritmus kiválasztása](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Lépések** | Titkosítási algoritmus, amely nem lehet egyszerűen visszavonni a jogosulatlan felhasználók adatátalakítások határozza meg. Az SQL Server lehetővé teszi a rendszergazdák és fejlesztők több algoritmusokat, beleértve a DES, háromszoros DES, TRIPLE_DES_3KEY, RC2, RC4, 128 bites RC4, DESX, 128 bites AES, 192 bites AES és 256 bites AES megvalósítási |

## <a id="ssis-signed"></a>SSIS-csomagok titkosítást és digitálisan aláírt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Azonosítsa a forráshelyet, a digitális aláírások csomagok](https://msdn.microsoft.com/library/ms141174.aspx), [fenyegetések és biztonsági rések kockázatait (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Lépések** | Csomag forrása, a személy vagy szervezet, amely a csomagot hozta létre. Ismeretlen vagy nem megbízható forrásból származó csomag futó kockázatos lehet. Az illetéktelen nem engedélyezett az SSIS-csomagok, digitális aláírások kell használni. Védelme érdekében a csomagok tárolására/átvitel során, az SSIS-csomagok van arra is, csak titkosítás |

## <a id="securables-db"></a>Adja hozzá a digitális aláírás kritikus adatbázis biztonságos elemekhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ALÁÍRÁS (Transact-SQL) hozzáadása](https://msdn.microsoft.com/library/ms181700) |
| **Lépések** | Azokban az esetekben, ahol egy biztonságos kritikus adatbázis integritása rendelkezik-e vissza kell igazolnia digitális aláírások kell használni. Adatbázis biztonságos elemekhez, például a tárolt eljárás, függvény, szerelvény vagy az eseményindító digitális aláírással. Az alábbi példában az, amikor ez akkor lehet hasznos: ossza meg velünk tegyük fel, az ISV (független szoftverszállító) nyújtott támogatás kézbesíti az ügyfeleknek egy szoftverfrissítési. Működtessenek, mielőtt a független Szoftvergyártók szeretné győződjön meg arról, hogy a szoftver biztonságos adatbázis nem módosították vagy véletlenül, vagy egy rosszindulatú kísérlet során. A biztonságos digitális aláírással, ha az ISV ellenőrizheti a digitális aláírás és ellenőrizni az integritását.| 

## <a id="ekm-keys"></a>SQL server EKM védelme érdekében titkosítási kulcsok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az SQL Server bővíthető kulcskezelés (EKM)](https://msdn.microsoft.com/library/bb895340), [bővíthető kulcskezelés az Azure Key Vault (SQL Server) használatával](https://msdn.microsoft.com/library/dn198405) |
| **Lépések** | Az SQL Server bővíthető kulcskezelés lehetővé teszi, hogy a titkosítási kulcsokat az adatbázisfájlok tárolhatók a egy-box eszköz, például intelligens kártyával, USB-eszközt vagy EKM /-HSM modul védő. Ez az adatbázis-rendszergazdák (kivéve a sysadmin (rendszergazda) csoport tagjai) adatvédelmet is lehetővé teszi. Adatok, hogy az adatbázis-felhasználó hozzáfér a külső EKM /-HSM-modult a titkosítási kulcsok használatával titkosíthatók. |

## <a id="keys-engine"></a>AlwaysEncrypted szolgáltatást használhatja, ha a titkosítási kulcsok nem szabad felfedni adatbázismotor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL-verzió - 12-es MsSQL2016 |
| **Hivatkozások**              | [Always Encrypted (adatbázismotor)](https://msdn.microsoft.com/library/mt163865) |
| **Lépések** | Always Encrypted funkciója tárolt bizalmas adatok, például hitelkártya-számokat vagy különféle nemzeti azonosítószámokat (például Egyesült államokbeli társadalombiztosítási szám), az Azure SQL Database vagy SQL Server-adatbázisok védelmét szolgálja. Always Encrypted lehetővé teszi az ügyfelek számára a bizalmas adatok ügyfélalkalmazásokon belüli titkosítását, és soha ne adja meg a titkosítási kulcsokat az adatbázismotorhoz (SQL Database vagy SQL Server). Ennek eredményeképpen azoknak, akik a saját adatok (és annak megtekintéséhez) elkülönülését Always Encrypted funkciója, és azok számára, akik kezelése az adatok (de nem rendelkezhetnek) |

## <a id="keys-iot"></a>Titkosítási kulcsok biztonságos Store IoT-eszköz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Eszköz operációs rendszere – a Windows IoT Core, eszközkapcsolat – Azure IoT eszközoldali SDK-k |
| **Hivatkozások**              | [A Windows IoT Core TPM](https://developer.microsoft.com/windows/iot/docs/tpm), [állítsa be a TPM-eszköz Windows IoT Core-on](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Azure IoT eszköz SDK-t TPM-eszköz](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Lépések** | Biztonságosan Symmetric vagy a tanúsítvány titkos kulcsokat hardveres védett tárolók például TPM-eszköz vagy az intelligens kártya lapkák. Windows 10 IoT Core támogatja a felhasználó egy TPM-eszköz, és vannak, amelyek segítségével több kompatibilis TPM: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Belső vezérlőprogramban vagy különálló TPM használata ajánlott. Szoftver TPM kell csak fejlesztési és tesztelési célokra használható. Miután egy TPM-eszköz érhető el, valamint a kulcsok akkor vannak kiépítve, a kódot, amely a token állít elő kell írni nélkül sorszámozás benne bizalmas adatokat. | 

### <a name="example"></a>Példa
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Ahogy látható, az eszköz elsődleges kulcs nem szerepel a kódban. Ehelyett tárolódik, 0-s bővítőhelyen a TPM-ben. TPM-eszköz generál egy rövid életű, majd az IoT hubhoz való csatlakozáshoz használt SAS-token. 

## <a id="random-hub"></a>Hozzon létre egy véletlenszerű szimmetrikus kulcs megfelelő hosszúságú az IoT Hub-hitelesítés

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Átjáró kiválasztása – Azure IoT Hub |
| **Hivatkozások**              | –  |
| **Lépések** | Az IoT Hub Eszközidentitás-jegyzék eszközt tartalmaz, és egy eszköz kiépítésekor automatikusan létrehoz egy véletlenszerű szimmetrikus kulcsot. Javasoljuk, hogy ez az Azure IoT Hub-Identitásjegyzék funkcióját használja a hitelesítéshez használt kulcs létrehozásához. Az IoT Hub lehetővé teszi az eszköz létrehozásakor meg kell adni egy kulcs is. Ha egy kulcs előállítási kívül az IoT Hub eszközkiépítési során, ajánlott hozzon létre egy véletlenszerű szimmetrikus kulcsot, vagy legalább 256 bit. |

## <a id="pin-remote"></a>Győződjön meg arról, egy másik felügyeleti szabályzat van érvényben, amely egy PIN-kód használatát igényli, és lehetővé teszi a távoli törlése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Mobile ügyfél | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, egy másik felügyeleti szabályzat van érvényben, amely egy PIN-kód használatát igényli, és lehetővé teszi a távoli törlése |

## <a id="bitlocker"></a>Győződjön meg, hogy egy másik felügyeleti szabályzat, amely egy PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker)

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Outlook-ügyfelének | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg, hogy egy másik felügyeleti szabályzat, amely egy PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker) |

## <a id="rolled-server"></a>Győződjön meg arról, hogy aláíró kulcsok vannak leváltani Identitáskiszolgálók használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Identitáskezelési kiszolgáló - kulcsokat, aláírás és titkosítás ](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Lépések** | Győződjön meg arról, hogy aláíró kulcsok vannak leváltani Identitáskiszolgálók használatakor. A hivatkozás a hivatkozások szakaszban azt ismerteti, hogyan ezt meg kell tervezni anélkül, hogy ez az identitás-kiszolgálót a függő alkalmazások üzemkimaradások. |

## <a id="client-server"></a>Gondoskodjon arról, hogy erős titkosítási szempontból ügyfél-azonosító, titkos Ügyfélkód használt az Identity Serverben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitáskezelési kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Gondoskodjon arról, hogy erős titkosítási szempontból ügyfél-azonosító, titkos Ügyfélkód identitás-kiszolgálót használja. Egy ügyfél-azonosító és titkos létrehozása közben a következő irányelveket kell használni:</p><ul><li>Hozzon létre egy véletlenszerű GUID Azonosítót, az ügyfél-azonosító</li><li>Hozzon létre egy titkosítási szempontból véletlenszerű 256 bites kulcsot, a titkos kulcs</li></ul>|
