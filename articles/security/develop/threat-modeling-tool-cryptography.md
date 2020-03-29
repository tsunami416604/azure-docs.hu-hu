---
title: Kriptográfia - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
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
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728189"
---
# <a name="security-frame-cryptography--mitigations"></a>Biztonsági keret: Kriptográfia | Enyhítése 

| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Csak jóváhagyott szimmetrikus blokktitkosításokat és kulcshosszokat használjon](#cipher-length)</li><li>[Jóváhagyott blokktitkosítási módok és inicializálási vektorok használata szimmetrikus rejtjelekhez](#vector-ciphers)</li><li>[Jóváhagyott aszimmetrikus algoritmusok, kulcshosszok és párnázás használata](#padding)</li><li>[Jóváhagyott véletlenszám-generátorok használata](#numgen)</li><li>[Ne használjon szimmetrikus adatfolyam-rejtjeleket](#stream-ciphers)</li><li>[Jóváhagyott MAC/HMAC/keyed kivonatoló algoritmusok használata](#mac-hash)</li><li>[Csak jóváhagyott kriptográfiai kivonatoló függvények használata](#hash-functions)</li></ul> |
| **Adatbázis** | <ul><li>[Erős titkosítási algoritmusok használata az adatbázisban lévő adatok titkosításához](#strong-db)</li><li>[Az SSIS-csomagokat titkosítani kell, és digitálisan alá kell írni](#ssis-signed)</li><li>[Digitális aláírás hozzáadása a kritikus adatbázis biztonságos sápjaihoz](#securables-db)</li><li>[Titkosítási kulcsok védelme az SQL-kiszolgáló EKM szolgáltatásával](#ekm-keys)</li><li>[AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felfedni az adatbázis-kezelő motorban](#keys-engine)</li></ul> |
| **IoT-eszköz** | <ul><li>[Titkosítási kulcsok biztonságos tárolása IoT-eszközön](#keys-iot)</li></ul> | 
| **IoT felhőátjáró** | <ul><li>[Az IoT Hub hitelesítéséhez megfelelő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása](#random-hub)</li></ul> | 
| **Dynamics CRM mobilügyfél** | <ul><li>[Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amelyhez pin-kód szükséges, és amely lehetővé teszi a távoli törlést](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook ügyfél** | <ul><li>[Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amely pin-kódot/jelszót/automatikus zárolást igényel, és titkosítja az összes adatot (pl. BitLocker)](#bitlocker)</li></ul> | 
| **Identitás-kiszolgáló** | <ul><li>[Az aláíró kulcsok görgetése az Identitáskiszolgáló használatakor](#rolled-server)</li><li>[Győződjön meg arról, hogy a kriptográfiailag erős ügyfélazonosító, ügyféltitok az Identitáskiszolgálóban használatos](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Csak jóváhagyott szimmetrikus blokktitkosításokat és kulcshosszokat használjon

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A termékek csak azokat a szimmetrikus blokktitkosításokat és a kapcsolódó kulcshosszokat használhatják, amelyeket a szervezet kriptográfiai tanácsadója kifejezetten jóváhagyott. A Microsoft jóváhagyott szimmetrikus algoritmusai a következő blokktitkosításokat tartalmazzák:</p><ul><li>Az új Kód AES-128, AES-192 és AES-256 elfogadható</li><li>A meglévő kóddal való visszamenőleges kompatibilitás érdekében a háromgombos 3DES elfogadható</li><li>Szimmetrikus blokktitkosításokat használó termékek esetében:<ul><li>Az új kódhoz speciális titkosítási szabvány (AES) szükséges</li><li>A háromgombos hármas adattitkosítási szabvány (3DES) a meglévő kódban megengedett a visszamenőleges kompatibilitás érdekében</li><li>Minden más blokkrejtjel, beleértve az RC2, DES, 2 Key 3DES, DESX és Skipjack, csak a régi adatok visszafejtésére használható, és ki kell cserélni, ha titkosításra használják</li></ul></li><li>A szimmetrikus blokktitkosítási algoritmusok esetében legalább 128 bites kulcshossz szükséges. Az új kódhoz ajánlott egyetlen blokktitkosítási algoritmus az AES (AES-128, AES-192 és AES-256 egyáltalán elfogadható)</li><li>A háromgombos 3DES jelenleg elfogadható, ha már használatban van a meglévő kódban; javasolt az AES-re való áttérés. A DES, DESX, RC2 és Skipjack már nem tekinthető biztonságosnak. Ezek az algoritmusok csak a visszamenőleges kompatibilitás érdekében használhatók a meglévő adatok visszafejtésére, és az adatokat újra kell titkosítani egy ajánlott blokktitkosítással.</li></ul><p>Kérjük, vegye figyelembe, hogy minden szimmetrikus blokkrejtjelet jóváhagyott rejtjelmódban kell használni, amely megfelelő inicializációs vektor (IV) használatát igényli. A megfelelő IV, általában egy véletlen szám, és soha nem állandó érték</p><p>A szervezet Crypto Board-felülvizsgálata után engedélyezhető az örökölt vagy más módon jóvá nem hagyott titkosítási algoritmusok és a kisebb kulcshosszok használata a meglévő adatok olvasásához (szemben az új adatok írásával). Ehhez a követelményhez azonban kivételt kell benyújtania. Emellett a vállalati telepítéseknél a termékeknek figyelembe kell venniük a figyelmeztető rendszergazdákat, ha gyenge titkosítást használnak az adatok olvasásához. Az ilyen figyelmeztetéseknek magyarázónak és megvalósíthatónak kell lenniük. Bizonyos esetekben helyénvaló lehet, hogy a csoportházirend szabályozza a gyenge titkosítási</p><p>Engedélyezett .NET algoritmusok a felügyelt titkosítási agilitáshoz (preferencia sorrendben)</p><ul><li>AesCng (FIPS-kompatibilis)</li><li>AuthenticatedAesCng (FIPS-kompatibilis)</li><li>AESCryptoServiceProvider (FIPS-kompatibilis)</li><li>AESManaged (nem FIPS-kompatibilis)</li></ul><p>Kérjük, vegye figyelembe, hogy ezen algoritmusok `SymmetricAlgorithm.Create` `CryptoConfig.CreateFromName` egyike sem adható meg a vagy metódusokkal anélkül, hogy módosítaná a machine.config fájlt. Vegye figyelembe azt is, hogy a .NET 3.5 `RijndaelManaged`előtti `AesCng` .NET 3.5 verziókban az AES nevet kapta, és `AuthenticatedAesCng` >érhető el a CodePlex-en keresztül, és cng-t igényel az alapul szolgáló operációs rendszerben.</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Jóváhagyott blokktitkosítási módok és inicializálási vektorok használata szimmetrikus rejtjelekhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Minden szimmetrikus blokkrejtjelet jóváhagyott szimmetrikus rejtjelmódban kell használni. Az egyetlen jóváhagyott mód a CBC és a CTS. Különösen el kell kerülni az elektronikus kódkönyv (EKB) működési módját; az EKB használatához a szervezet Crypto Board felülvizsgálata szükséges. Az OFB, CFB, CTR, CCM és GCM minden használatát, illetve bármely más titkosítási módot a szervezet kriptográfiai tanácsának felül kell vizsgálnia. Ugyanazaz inicializálási vektor (IV) újrafelhasználása blokktitkosításokkal a "streamelési titkosítási módokban", például a CTR-ben, titkosított adatok felfedését okozhatja. Minden szimmetrikus blokkrejtjelet megfelelő inicializációs vektorral (IV) is kell használni. A megfelelő IV egy kriptográfiailag erős, véletlen szám, és soha nem állandó érték. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Jóváhagyott aszimmetrikus algoritmusok, kulcshosszok és párnázás használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A tiltott kriptográfiai algoritmusok használata jelentős kockázatot jelent a termékbiztonságra nézve, ezért el kell kerülni. A termékek csak azokat a kriptográfiai algoritmusokat és a hozzájuk tartozó kulcshosszokat és kitöltéseket használhatják, amelyeket a szervezet kriptográfiai táblája kifejezetten jóváhagyott.</p><ul><li>**RSA-** lehet használni a titkosítás, kulcscsere és aláírás. Az RSA-titkosítás csak az OAEP vagy az RSA-KEM kitöltési módot használhatja. A meglévő kód csak kompatibilitási módban használhatja a PKCS #1 1.5-ös jelű kitöltési módot. A null kitöltés használata kifejezetten tiltott. Az új kódhoz >= 2048 bites kulcsok szükségesek. A meglévő kód csak a szervezet titkosítási tanácsának felülvizsgálatát követően támogathatja a 2048 bites < kulcsokat a visszamenőleges kompatibilitás érdekében. A kulcsok < 1024 bites kulcsok csak a régi adatok visszafejtésére/ellenőrzésére használhatók, és akkor cserélhetők ki, ha titkosítási vagy aláírási műveletekhez használják őket.</li><li>**ECDSA-** csak aláírásra használható. Az új kódhoz >=256 bites kulcsokkal rendelkező ECDSA szükséges. Az ECDSA-alapú aláírásoknak a három NIST által jóváhagyott görbe (P-256, P-384 vagy P521) egyikét kell használniuk. Az alaposan elemzett görbék csak a szervezet kriptográfiai tanácsával való felülvizsgálat után használhatók.</li><li>**ECDH-** csak kulcscserére használható. Az új kódhoz >=256 bites kulcsokkal rendelkező ECDH szükséges. Az ECDH-alapú kulcscserének a három NIST által jóváhagyott görbe (P-256, P-384 vagy P521) egyikét kell használnia. Az alaposan elemzett görbék csak a szervezet kriptográfiai tanácsával való felülvizsgálat után használhatók.</li><li>**DSA-** elfogadható lehet a szervezet Crypto Board felülvizsgálata és jóváhagyása után. Lépjen kapcsolatba a biztonsági tanácsadóval a szervezet kriptográfiai tanácsának felülvizsgálatának ütemezéséhez. Ha a DSA használatát jóváhagyták, vegye figyelembe, hogy meg kell tiltania a 2048 bitnél rövidebb kulcsok használatát. A CNG támogatja a 2048 bites és a nagyobb billentyűhosszokat a Windows 8-tól.</li><li>**A Diffie-Hellman csak** munkamenetkulcs-kezeléshez használható. Az új kódhoz kulcshossz >= 2048 bit szükséges. A meglévő kód csak a 2048 bites kulcshosszokat < támogathatja a visszamenőleges kompatibilitás érdekében, miután a szervezet kriptográfiai táblája áttekintette. Az 1024 bites < billentyűk nem használhatók.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Jóváhagyott véletlenszám-generátorok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A termékeknek jóváhagyott véletlenszám-generátorokat kell használniuk. A pseudorandom függvényeket, például a C futásidejű függvényrandát, a .NET Framework class System.Random vagy a rendszerfüggvényeket, például a GetTickCount függvényeket ezért soha nem szabad ilyen kódban használni. A kettős elliptikus görbe véletlenszám-generátor (DUAL_EC_DRBG) algoritmus használata tilos</p><ul><li>**CNG-** BCryptGenRandom(használata a BCRYPT_USE_SYSTEM_PREFERRED_RNG jelző ajánlott, kivéve, ha a hívó futhat bármely IRQL nagyobb, mint 0 [azaz PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (új megvalósítások kellhasználni BCryptGenRandom vagy CryptGenRandom) * rand_s * SystemPrng (a kernel módban)</li><li>**. NET-** RNGCryptoServiceProvider vagy RNGCng</li><li>**Windows Áruházbeli alkalmazások-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom vagy . GenerateRandomNumber (Létrehozási véletlenszerű szám)</li><li>**Apple OS X (10.7+)/iOS(2.0+)-** int SecRandomCopyBytes (SecRandomRef \*random, size_t count, uint8_t byte )</li><li>**Apple OS X (<10.7)-** A /dev/random kapcsoló val a véletlen számok beolvasásához</li><li>**Java (beleértve a Google Android Java kód) -** java.security.SecureRandom osztály. Ne feledje, hogy az Android 4.3 (Jelly Bean) esetében a fejlesztőknek követniük kell az Android által ajánlott kerülő megoldásokat, és frissíteniük kell alkalmazásaikat, hogy kifejezetten inicializálni a PRNG-t entrópiával a / dev / urandom vagy / dev / random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Ne használjon szimmetrikus adatfolyam-rejtjeleket

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Szimmetrikus adatfolyam-rejtjeleket, például rc4-et nem szabad használni. A szimmetrikus adatfolyam-rejtjelek helyett a termékeknek blokktitkosítást kell használniuk, különösen a legalább 128 bites kulcshosszúságú AES-t. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Jóváhagyott MAC/HMAC/keyed kivonatoló algoritmusok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A termékek csak jóváhagyott üzenethitelesítési kódot (MAC) vagy kivonat-alapú üzenethitelesítési kód (HMAC) algoritmusokat használhatnak.</p><p>Az üzenethitelesítési kód (MAC) egy üzenethez csatolt információ, amely lehetővé teszi a címzett számára, hogy titkos kulccsal ellenőrizze a feladó hitelességét és az üzenet integritását. A kivonatoló mac[(HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) vagy [a blokk-titkosítás-alapú MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) használata megengedett, amennyiben az összes mögöttes kivonatoló vagy szimmetrikus titkosítási algoritmus támpontként szolgál; ez magában foglalja a HMAC-SHA2 funkciókat (HMAC-SHA256, HMAC-SHA384 és HMAC-SHA512), valamint a CMAC/OMAC1 és OMAC2 blokktitkosításon alapuló KB-okat (ezek az AES-en alapulnak).</p><p>A HMAC-SHA1 használata megengedett lehet a platform kompatibilitása érdekében, de kivételt kell benyújtania ez alól az eljárás alól, és át kell esnie a szervezet Crypto felülvizsgálatán. A HMC-k 128 bitnél kisebb értékre történő csonkolása nem megengedett. Az ügyfélmetódusok használata a kulcs kivonatolásához, és az adatok nem engedélyezettek, és használat előtt alá kell vetni a szervezet kriptográfiai tanácsának felülvizsgálatát.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Csak jóváhagyott kriptográfiai kivonatoló függvények használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A termékeknek az SHA-2 kivonatoló algoritmusok (SHA256, SHA384 és SHA512) családot kell használniuk. Ha rövidebb kivonatra van szükség, például egy 128 bites kimeneti hosszra, hogy elférjen egy olyan adatstruktúra, amelyet a rövidebb MD5 kivonatszem előtt terveznek, a termékcsapatok csonkolhatják az SHA2 kivonatok egyikét (általában az SHA256-ot). Ne feledje, hogy az SHA384 az SHA512 csonkolt verziója. A kriptográfiai kiképek biztonsági célú csonkolása 128 bitnél kisebb re nem engedélyezett. Az új kód nem használhatja az MD2, MD4, MD5, SHA-0, SHA-1 vagy RIPEMD kivonatoló algoritmusokat. A kivonatoló ütközések ezekaz algoritmusok esetében számításilag megvalósíthatók, ami hatékonyan megtöri őket.</p><p>Engedélyezett .NET kivonatoló algoritmusok a felügyelt titkosítási agilitáshoz (preferencia sorrendben):</p><ul><li>SHA512Cng (FIPS-kompatibilis)</li><li>SHA384Cng (FIPS-kompatibilis)</li><li>SHA256Cng (FIPS-kompatibilis)</li><li>SHA512Felügyelt (nem FIPS-kompatibilis) (az SHA512 használata algoritmusnévként a HashAlgorithm.Create vagy cryptoconfig.CreateFromName hívásokban)</li><li>SHA384Felügyelt (nem FIPS-kompatibilis) (az SHA384-et használja algoritmusnévként a HashAlgorithm.Create vagy cryptoconfig.CreateFromName hívásokban)</li><li>SHA256Felügyelt (nem FIPS-kompatibilis) (az SHA256-ot használja algoritmusnévként a HashAlgorithm.Create vagy cryptoconfig.CreateFromName hívásokban)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibilis)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Erős titkosítási algoritmusok használata az adatbázisban lévő adatok titkosításához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Titkosítási algoritmus kiválasztása](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Lépéseket** | A titkosítási algoritmusok olyan adatátalakításokat határoznak meg, amelyeket illetéktelen felhasználók nem tudnak könnyen visszafordítani. Az SQL Server lehetővé teszi a rendszergazdák és a fejlesztők számára, hogy számos algoritmus közül válasszanak, beleértve a DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128 bites RC4, DESX, 128 bites AES, 192 bites AES és 256 bites AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Az SSIS-csomagokat titkosítani kell, és digitálisan alá kell írni

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A digitális aláírással ellátott csomagok forrásának azonosítása](https://msdn.microsoft.com/library/ms141174.aspx), [a fenyegetés és a biztonsági rés ek enyhítése (integrációs szolgáltatások)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Lépéseket** | A csomag forrása az a személy vagy szervezet, amely létrehozta a csomagot. Ismeretlen vagy nem megbízható forrásból származó csomag futtatása kockázatos lehet. Az SSIS-csomagok jogosulatlan illetéktelen illetéktelen illetéktelen illetéktelen illetéktelen módosítása érdekében digitális aláírásokat kell használni. A csomagok tárolás/továbbítás során történő titkosságának biztosítása érdekében az SSIS-csomagokat |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Digitális aláírás hozzáadása a kritikus adatbázis biztonságos sápjaihoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [ALÁÍRÁS HOZZÁADÁSA (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Lépéseket** | Azokban az esetekben, amikor egy kritikus adatbázis integritását ellenőrizni kell, digitális aláírásokat kell használni. Az adatbázis biztonságos tápellátása, például a tárolt eljárás, a funkció, az összeállítás vagy az eseményindító digitálisan aláírható. Az alábbiakban egy példa, ha ez hasznos lehet: Tegyük fel, hogy egy FÜGGETLEN SZOFTVER (Independent Software Vendor) támogatást nyújtott egy szoftver szállított egyik ügyfeleik. A támogatás nyújtása előtt a független szoftverhozzászóló azt szeretné, ha a szoftverben biztonságos adatbázist nem véletlenül vagy rosszindulatú kísérlettel módosították volna. Ha a biztonságos értékpapír digitálisaláírással van elrendelve, a isv ellenőrizheti digitális aláírását, és ellenőrizheti annak integritását.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Titkosítási kulcsok védelme az SQL-kiszolgáló EKM szolgáltatásával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [SQL Server extensible key management (EKM)](https://msdn.microsoft.com/library/bb895340), [bővíthető kulcskezelés az Azure Key Vault (SQL Server) használatával](https://msdn.microsoft.com/library/dn198405) |
| **Lépéseket** | Az SQL Server Extensible Key Management lehetővé teszi az adatbázisfájlokat védő titkosítási kulcsok tárolását egy külső eszközben, például intelligens kártyán, USB-eszközön vagy EKM/HSM modulban. Ez lehetővé teszi az adatbázis-rendszergazdák (kivéve a rendszergazdai csoport tagjait) adatvédelmét is. Az adatok titkosíthatók olyan titkosítási kulcsokkal, amelyekhez csak az adatbázis-felhasználó férhet hozzá a külső EKM/HSM modulon. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felfedni az adatbázis-kezelő motorban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL verzió - V12, MsSQL2016 |
| **Referencia**              | [Mindig titkosított (adatbázis-motor)](https://msdn.microsoft.com/library/mt163865) |
| **Lépéseket** | A Mindig titkosítva olyan szolgáltatás, amely az Azure SQL Database vagy az SQL Server adatbázisokban tárolt bizalmas adatok, például hitelkártyaszámok vagy nemzeti azonosítószámok (pl. amerikai társadalombiztosítási számok) védelmét szolgálja. A Mindig titkosított lehetővé teszi az ügyfelek számára, hogy titkosítsák a bizalmas adatokat az ügyfélalkalmazásokon belül, és soha ne fedjék fel a titkosítási kulcsokat az adatbázis-motor (SQL Database vagy SQL Server) számára. Ennek eredményeképpen az Always Encrypted elkülöníti azokat, akik az adatokat birtokolják (és megtekinthetik) és azok között, akik kezelik az adatokat (de nem férhetnek hozzá) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Titkosítási kulcsok biztonságos tárolása IoT-eszközön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Eszköz operációs rendszer – Windows IoT Core, eszközkapcsolat – Azure IoT-eszköz SDK-k |
| **Referencia**              | [TPM windowsos IoT Core rendszeren](https://developer.microsoft.com/windows/iot/docs/tpm), [TPM beállítása Windows IoT Core rendszeren](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), Azure [IoT-eszköz SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Lépéseket** | Szimmetrikus vagy tanúsítvány titkos kulcsok biztonságosan egy hardveres védett tároló, mint a TPM vagy smart card chipek. A Windows 10 IoT Core támogatja a TPM felhasználóját, és számos https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpmkompatibilis TPM használható: . Javasoljuk, hogy firmware-t vagy különálló TPM-et használjon. A szoftveres TPM-et csak fejlesztési és tesztelési célokra szabad használni. Miután a TPM elérhetővé válik, és a kulcsok ki vannak építve benne, a jogkivonatot generáló kódot a bizalmas adatok szigorú kódolása nélkül kell írni. | 

### <a name="example"></a>Példa
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Mint látható, az eszköz elsődleges kulcsa nem szerepel a kódban. Ehelyett a TPM a 0 tárolóhelyen tárolódik. A TPM-eszköz egy rövid ideig tartalmazó SAS-jogkivonatot hoz létre, amely ezután az IoT Hubhoz való csatlakozáshoz használatos. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Az IoT Hub hitelesítéséhez megfelelő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választás - Azure IoT Hub |
| **Referencia**              | N/A  |
| **Lépéseket** | Az IoT Hub tartalmaz egy eszköz identitás-beállításjegyzéket, és az eszköz kiépítése közben automatikusan létrehoz egy véletlenszerű szimmetrikus kulcsot. Javasoljuk, hogy az Azure IoT Hub identity Registry ezen szolgáltatásának használatával hozza létre a hitelesítéshez használt kulcsot. Az IoT Hub azt is lehetővé teszi, hogy az eszköz létrehozása közben meg kell adni egy kulcsot. Ha egy kulcs az IOt Hubon kívül jön létre az eszköz kiépítése során, ajánlott létrehozni egy véletlenszerű szimmetrikus kulcsot vagy legalább 256 bitet. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amelyhez pin-kód szükséges, és amely lehetővé teszi a távoli törlést

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM mobilügyfél | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amelyhez pin-kód szükséges, és amely lehetővé teszi a távoli törlést |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amely pin-kódot/jelszót/automatikus zárolást igényel, és titkosítja az összes adatot (pl. BitLocker)

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Outlook ügyfél | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy olyan eszközkezelési házirend van érvényben, amely pin-kódot/jelszót/automatikus zárolást igényel, és titkosítja az összes adatot (pl. BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Az aláíró kulcsok görgetése az Identitáskiszolgáló használatakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Identitáskiszolgáló – kulcsok, aláírások és kriptográfia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Lépéseket** | Győződjön meg arról, hogy az aláíró kulcsok átgördülnek az Identity Server használatakor. A hivatkozások szakaszban található hivatkozás ismerteti, hogyan kell ezt megtervezni anélkül, hogy az Identitáskiszolgálóra támaszkodó alkalmazások kimaradásokat okoznának. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Győződjön meg arról, hogy a kriptográfiailag erős ügyfélazonosító, ügyféltitok az Identitáskiszolgálóban használatos

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identitás-kiszolgáló | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Győződjön meg arról, hogy a kriptográfiailag erős ügyfélazonosító, ügyféltitok az Identitáskiszolgálóban használatos. Az ügyfélazonosító és a titkos titok létrehozásakor a következő irányelveket kell használni:</p><ul><li>Véletlenszerű GUID létrehozása ügyfélazonosítóként</li><li>Hozzon létre egy kriptográfiailag véletlenszerű 256 bites kulcsot, mint a titkos</li></ul>|
