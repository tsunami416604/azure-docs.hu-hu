---
title: Titkosítás – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
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
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728189"
---
# <a name="security-frame-cryptography--mitigations"></a>Biztonsági keret: Kriptográfia | Enyhítését 

| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Csak a jóváhagyott szimmetrikus blokk titkosítási algoritmusok és a kulcsok hosszának használata](#cipher-length)</li><li>[Jóváhagyott blokk titkosítási módok és inicializálási vektorok használata szimmetrikus titkosításhoz](#vector-ciphers)</li><li>[Jóváhagyott aszimmetrikus algoritmusok, kulcsok hosszának és kitöltésének használata](#padding)</li><li>[Jóváhagyott véletlenszerű számú generátorok használata](#numgen)</li><li>[Ne használjon szimmetrikus adatfolyam-titkosítást](#stream-ciphers)</li><li>[Jóváhagyott MAC/HMAC/kulcsos kivonatoló algoritmusok használata](#mac-hash)</li><li>[Csak jóváhagyott titkosítási kivonatoló függvények használata](#hash-functions)</li></ul> |
| **Adatbázis** | <ul><li>[Erős titkosítási algoritmusok használata az adatbázisban tárolt adattitkosításhoz](#strong-db)</li><li>[A SSIS-csomagokat titkosítani és digitálisan alá kell írni](#ssis-signed)</li><li>[Digitális aláírás hozzáadása a kritikus adatbázis-biztonságos elemek migrálására](#securables-db)</li><li>[Az SQL Server EKM használata a titkosítási kulcsok védeleméhez](#ekm-keys)</li><li>[AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felmutatni az adatbázismotor számára](#keys-engine)</li></ul> |
| **IoT-eszköz** | <ul><li>[Titkosítási kulcsok biztonságos tárolása a IoT-eszközön](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[A IoT Hub hitelesítéshez elegendő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile-ügyfél** | <ul><li>[Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-ügyfél** | <ul><li>[Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker).](#bitlocker)</li></ul> | 
| **Identity Server** | <ul><li>[Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor](#rolled-server)</li><li>[Győződjön meg arról, hogy a kriptográfiailag erős ügyfél-azonosító, az ügyfél titkos kulcsa az Identity Serverben van használatban](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Csak a jóváhagyott szimmetrikus blokk titkosítási algoritmusok és a kulcsok hosszának használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A termékeknek csak azokat a szimmetrikus blokkos titkosítási algoritmusokat és a hozzájuk tartozó kulcs hosszait kell használniuk, amelyeket kifejezetten jóváhagytak a szervezet kriptográfiai tanácsadója. A Microsoft által jóváhagyott szimmetrikus algoritmusok a következő blokk-rejtjeleket tartalmazzák:</p><ul><li>Az AES-128, AES-192 és AES-256 új kód esetén elfogadható</li><li>A meglévő kóddal való visszamenőleges kompatibilitás érdekében a három kulcsos 3DES elfogadható</li><li>Szimmetrikus blokk titkosítási algoritmust használó termékek esetén:<ul><li>Az új kód Advanced Encryption Standard (AES) szükséges</li><li>A három kulcsos Triple adattitkosítási szabvány (3DES) a meglévő kódban megengedett a visszamenőleges kompatibilitás érdekében.</li><li>Az összes többi blokk-titkosítás, például az RC2, a DES, a 2. kulcs, a DESX és a csíkoshasú csak a régi adattitkosítás visszafejtésére használható, és ha titkosításhoz használják, le kell cserélni.</li></ul></li><li>A szimmetrikus blokk titkosítási algoritmusok esetében az 128 bitek minimális hosszának megadása kötelező. A csak az új kódokhoz ajánlott blokk titkosítási algoritmus az AES (AES-128, AES-192 és AES-256) esetén elfogadható.</li><li>A három kulcsos 3DES jelenleg elfogadható, ha már használatban van meglévő kódban; az AES-re való áttérés ajánlott. A DES, a DESX, az RC2 és a csíkoshasú már nem tekinthető biztonságosnak. Ezek az algoritmusok csak a meglévő adatvisszafejtéshez használhatók a visszamenőleges kompatibilitás érdekében, és az adatmennyiséget ajánlott blokk-rejtjel használatával újra titkosítani kell.</li></ul><p>Vegye figyelembe, hogy az összes szimmetrikus blokkos titkosítást jóváhagyott titkosítási móddal kell használni, amely a megfelelő inicializálási vektor (IV) használatát igényli. A megfelelő IV, általában egy véletlenszerű szám, és soha nem állandó érték</p><p>Az örökölt vagy más módon nem jóváhagyott kriptográfiai algoritmusok és kisebb kulcsok használata a meglévő információk olvasásához (az új adatmennyiségek megírása helyett) a szervezet titkosítási Tanácsa általi felülvizsgálata után is engedélyezhető. Ebben az esetben azonban kivételt kell megadnia ehhez a követelményhöz. Emellett a vállalati telepítésekben a termékeknek figyelembe kell venniük a figyelmeztetési rendszergazdákat, ha gyenge titkosítást használnak az adatolvasáshoz. Az ilyen figyelmeztetéseknek magyarázó és végrehajthatónak kell lenniük. Bizonyos esetekben célszerű lehet a gyenge titkosítás használatát Csoportházirend vezérelni</p><p>Engedélyezett .NET-algoritmusok a felügyelt titkosítási rugalmasság érdekében (preferencia sorrendben)</p><ul><li>AesCng (FIPS-kompatibilis)</li><li>AuthenticatedAesCng (FIPS-kompatibilis)</li><li>AESCryptoServiceProvider (FIPS-kompatibilis)</li><li>AESManaged (non-FIPS-compliant)</li></ul><p>Vegye figyelembe, hogy ezek az algoritmusok egyike sem adható `SymmetricAlgorithm.Create` meg `CryptoConfig.CreateFromName` a vagy metóduson keresztül anélkül, hogy módosítani kellene a Machine. config fájlt. Azt is vegye figyelembe, hogy a .net 3,5-es verzió előtti AES a `RijndaelManaged`named `AesCng` , `AuthenticatedAesCng` és > érhető el a Codeplex-en keresztül, és az alapul szolgáló operációs rendszerhez a CNG szükséges.</p>

## <a id="vector-ciphers"></a>Jóváhagyott blokk titkosítási módok és inicializálási vektorok használata szimmetrikus titkosításhoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Az összes szimmetrikus blokkos titkosítást jóváhagyott szimmetrikus titkosítási móddal kell használni. Az egyetlen jóváhagyott mód a CBC és a CTS. Az elektronikus kódú (EKB) üzemmódot nem szabad elkerülni; az EKB használatához a szervezete kriptográfiai Tanácsának felülvizsgálata szükséges. Az OFB, a CFB, a CTR, a CCM és a GCM, illetve bármely más titkosítási mód használatát a szervezet kriptográfiai testületének kell áttekintenie. Ha ugyanazt az inicializálási vektort (IV) szeretné újból használni a "streaming titkosítási módokban", például a CTR-ben, akkor a titkosított adatmegjelenítést okozó blokkos titkosítási algoritmusokat. Az összes szimmetrikus blokkos titkosítást a megfelelő inicializálási vektor (IV) használatával is fel kell használni. A megfelelő IV titkosítási szempontból erős, véletlenszerű szám, és soha nem állandó érték. |

## <a id="padding"></a>Jóváhagyott aszimmetrikus algoritmusok, kulcsok hosszának és kitöltésének használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A tiltott titkosítási algoritmusok használata jelentős kockázatot jelent a termékbiztonság számára, és el kell kerülni. A termékeknek kizárólag azokat a titkosítási algoritmusokat és a hozzájuk tartozó kulcs hosszait és kitöltését kell használniuk, amelyeket a szervezet kriptográfiai Tanácsa explicit módon jóváhagyott.</p><ul><li>**RSA –** titkosításhoz, kulcscserehez és aláíráshoz is használható. Az RSA-titkosítás csak a OAEP vagy az RSA-KEM kitöltési módokat kell használnia. A meglévő kód a PKCS #1 v 1.5 kitöltési módot is használhatja a kompatibilitáshoz. A null kitöltés használata explicit módon tiltott. A kulcsok > = 2048 BITS szükségesek az új kódhoz. A meglévő kód a szervezet kriptográfiai Tanácsa általi felülvizsgálat után csak a visszamenőleges kompatibilitás érdekében támogatja a kulcsok < 2048 bitet. A kulcsok < 1024 bitek csak a régi adatok visszafejtéséhez vagy ellenőrzéséhez használhatók, és ha titkosítási vagy aláírási műveletekhez használják őket, le kell cserélni.</li><li>**ECDSA –** csak aláírás esetén használható. A ECDSA > = 256 bites kulcsokra van szükség az új kódokhoz. A ECDSA-alapú aláírásoknak a három NIST által jóváhagyott görbe egyikét kell használniuk (P-256, P-384 vagy P521). A alaposan elemzett görbék csak a szervezet titkosítási táblájának áttekintése után használhatók.</li><li>**ECDH –** csak kulcscsere esetén használható. A ECDH > = 256 bites kulcsokra van szükség az új kódokhoz. A ECDH-alapú kulcscserenek a három NIST által jóváhagyott görbe egyikét kell használnia (P-256, P-384 vagy P521). A alaposan elemzett görbék csak a szervezet titkosítási táblájának áttekintése után használhatók.</li><li>A **DSA –** a szervezete kriptográfiai táblájának felülvizsgálata és jóváhagyása után is elfogadható lehet. Forduljon a biztonsági tanácsadóhoz, és ütemezze a szervezet titkosítási Tanácsának felülvizsgálatát. Ha jóváhagyja a DSA használatát, vegye figyelembe, hogy a 2048-nál kisebb hosszúságú kulcsok használatát meg kell tiltania. A CNG a 2048 bites és nagyobb hosszúságú kulcsokat támogatja a Windows 8 rendszertől kezdve.</li><li>**Diffie-Hellman –** csak a munkamenet-kulcsok kezeléséhez használható. A kulcs hosszának > = 2048 bit szükséges az új kódhoz. A meglévő kód a szervezet kriptográfiai Tanácsa általi felülvizsgálat után csak a visszamenőleges kompatibilitás érdekében < 2048 biteket támogatja. A kulcsok < 1024 bitek nem használhatók.</li><ul>

## <a id="numgen"></a>Jóváhagyott véletlenszerű számú generátorok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A termékeknek jóváhagyott véletlenszerű számú generátort kell használniuk. A bájtján elvégeznek függvények, például a Rand C futásidejű függvény, a .NET-keretrendszer System. Random vagy a rendszerfüggvények, például a GetTickCount, soha nem használhatók ilyen kódban. A kettős elliptikus görbe Random Number Generator (DUAL_EC_DRBG) algoritmus használata tiltott</p><ul><li>**CNG –** BCryptGenRandom (csak akkor használja a BCRYPT_USE_SYSTEM_PREFERRED_RNG jelzőt, ha a hívó a 0 értéknél nagyobb mennyiségű IRQL fut: [azaz PASSIVE_LEVEL])</li><li>**CAPI –** cryptGenRandom</li><li>**Win32/64 –** RtlGenRandom (az új implementációknak BCryptGenRandom vagy CryptGenRandom) * rand_s * SystemPrng kell használniuk (kernel módban)</li><li>**. NET-** RNGCryptoServiceProvider vagy RNGCng</li><li>**Windows áruházbeli alkalmazások –** Windows. Security. kriptográfia. CryptographicBuffer. GenerateRandom vagy. GenerateRandomNumber</li><li>**Apple OS X (10.7 +)/iOS (2.0 +) –** int SecRandomCopyBytes (SecRandomRef véletlenszerű, size_t száma, uint8_t \*bájt)</li><li>**Apple OS X (< 10.7) –** Véletlenszerű számok beolvasása a/dev/Random használatával</li><li>**Java (például Google Android Java Code) –** Java. Security. SecureRandom osztály. Vegye figyelembe, hogy az Android 4,3 (zselés Bean) esetében a fejlesztőknek követniük kell az Android ajánlott megkerülő megoldását, és frissíteniük kell alkalmazásaikat, hogy explicit módon inicializálják az PRNG-t a/dev/urandom vagy a/dev/Random</li></ul>|

## <a id="stream-ciphers"></a>Ne használjon szimmetrikus adatfolyam-titkosítást

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A szimmetrikus adatfolyam-titkosítások (például az RC4) nem használhatók. A szimmetrikus adatfolyamok titkosítása helyett a termékeknek egy blokkos titkosítást, azaz legalább 128 bitet tartalmazó kulcs hosszúságú AES-t kell használniuk. |

## <a id="mac-hash"></a>Jóváhagyott MAC/HMAC/kulcsos kivonatoló algoritmusok használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A termékek csak a jóváhagyott üzenet-hitelesítési kód (MAC) vagy a hash-alapú üzenethitelesítő kód (HMAC) algoritmusokat használják.</p><p>Az üzenet-hitelesítési kód (MAC) egy üzenethez csatolt információ, amely lehetővé teszi a címzett számára, hogy ellenőrizze a küldő hitelességét és az üzenet integritását titkos kulccsal. A kivonatoló MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) vagy a [Block-Cipher-alapú Mac](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) használata engedélyezett, ha az összes alapul szolgáló kivonatoló vagy szimmetrikus titkosítási algoritmus is jóvá van hagyva; Ez jelenleg magában foglalja a HMAC-SHA2 függvényeket (HMAC-SHA256, HMAC-SHA384 és HMAC-SHA512), valamint a CMAC/OMAC1 és a OMAC2 blokk rejtjel-alapú Mac gépeket (ezek az AES-alapúak).</p><p>A HMAC-SHA1 használata engedélyezhető a platformok kompatibilitása szempontjából, de kivételt kell beírnia az eljárásba, és a szervezete titkosítási felülvizsgálatán át kell esnie. A HMACs kevesebb mint 128 bit-re való csonkítása nem engedélyezett. Ha az ügyfél módszereit használja a kulcsok és az Adatkivonatok kivonatolására, a használat előtt el kell végeznie a szervezet titkosítási Tanácsának felülvizsgálatát.</p>|

## <a id="hash-functions"></a>Csak jóváhagyott titkosítási kivonatoló függvények használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A termékeknek az SHA-2 kivonatoló algoritmust (SHA256, SHA384 és SHA512) kell használniuk. Ha rövidebb kivonatra van szükség, például egy 128 bites kimeneti hosszt ahhoz, hogy illeszkedjen egy rövidebb MD5-kivonattal kialakított adatszerkezethez, a termékcsoportok a SHA2-kivonatok egyikét (általában SHA256) is lerövidítik. Vegye figyelembe, hogy a SHA384 a SHA512 csonkolt verziója. A titkosítási kivonatok 128-nál kevesebb biten való csonkítása nem engedélyezett. Az új kód nem használhatja a MD2, a MD4, az MD5, az SHA-0, az SHA-1 vagy az RIPEMD kivonatoló algoritmust. Ezekhez az algoritmusokhoz a kivonatoló ütközések számítási feloldhatók, ami gyakorlatilag megszakítja azokat.</p><p>Engedélyezett .NET-kivonatoló algoritmusok a felügyelt titkosítási rugalmassághoz (preferencia sorrendben):</p><ul><li>SHA512Cng (FIPS-kompatibilis)</li><li>SHA384Cng (FIPS-kompatibilis)</li><li>SHA256Cng (FIPS-kompatibilis)</li><li>SHA512Managed (nem FIPS-kompatibilis) (használja a SHA512 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA384Managed (nem FIPS-kompatibilis) (használja a SHA384 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA256Managed (nem FIPS-kompatibilis) (használja a SHA256 as algoritmus nevét a HashAlgorithm. Create vagy CryptoConfig. CreateFromName) hívásokban.</li><li>SHA512CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibilis)</li></ul>| 

## <a id="strong-db"></a>Erős titkosítási algoritmusok használata az adatbázisban tárolt adattitkosításhoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Titkosítási algoritmus kiválasztása](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Lépések** | A titkosítási algoritmusok olyan adatátalakításokat határoznak meg, amelyeket jogosulatlan felhasználók nem könnyen visszafordítanak. SQL Server lehetővé teszi, hogy a rendszergazdák és a fejlesztők több algoritmus közül választhatnak, többek között a DES, a Triple DES, a TRIPLE_DES_3KEY, az RC2, az RC4, a 128 bites RC4, a DESX, a 128 bites AES, a 192 bites AES és a 256 bites AES |

## <a id="ssis-signed"></a>A SSIS-csomagokat titkosítani és digitálisan alá kell írni

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | A digitális aláírásokkal, a [fenyegetésekkel és a sebezhetőségek enyhítésével (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) [rendelkező csomagok forrásainak azonosítása](https://msdn.microsoft.com/library/ms141174.aspx) |
| **Lépések** | A csomag forrása a csomagot létrehozó személy vagy szervezet. A csomagok ismeretlen vagy nem megbízható forrásból való futtatása kockázatos lehet. A SSIS-csomagok jogosulatlan illetéktelen módosításának megakadályozása érdekében a digitális aláírásokat kell használni. Emellett a csomagok titkosságának biztosításához a tárolás/átvitel során a SSIS-csomagokat titkosítani kell |

## <a id="securables-db"></a>Digitális aláírás hozzáadása a kritikus adatbázis-biztonságos elemek migrálására

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Lépések** | Azokban az esetekben, amikor a biztonságos adatbázis integritását ellenőrizni kell, a digitális aláírásokat kell használni. Az adatbázis biztonságos elemek migrálására, például tárolt eljárás, függvény, szerelvény vagy trigger lehet digitálisan aláírva. Az alábbiakban egy példa látható, ha ez hasznos lehet: Tegyük fel, hogy egy ISV (független szoftvergyártó) támogatta az egyik ügyfele számára elérhető szoftvert. A támogatás megkezdése előtt az ISV-nak biztosítania kell, hogy a szoftverben biztonságos adatbázis nem módosult véletlenül vagy rosszindulatú kísérlet útján. Ha a biztonságos digitális aláírással rendelkezik, az ISV ellenőrizheti a digitális aláírását, és ellenőrizheti annak integritását.| 

## <a id="ekm-keys"></a>Az SQL Server EKM használata a titkosítási kulcsok védeleméhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [SQL Server bővíthető kulcskezelő (EKM)](https://msdn.microsoft.com/library/bb895340), [bővíthető kulcskezelő Azure Key Vault használatával (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Lépések** | SQL Server bővíthető kulcskezelő szolgáltatás lehetővé teszi, hogy az adatbázisfájlok tárolására szolgáló titkosítási kulcsok egy off-Box eszközön, például egy intelligens kártyán, USB-eszközön vagy EKM/HSM-modulban legyenek tárolva. Ez az adatvédelmet is lehetővé teszi az adatbázis-rendszergazdáktól (a sysadmin csoport tagjai kivételével). Az adattitkosítás olyan titkosítási kulcsok használatával végezhető el, amelyekhez csak az adatbázis-felhasználó fér hozzá a külső EKM/HSM-modulhoz. |

## <a id="keys-engine"></a>AlwaysEncrypted szolgáltatás használata, ha a titkosítási kulcsokat nem szabad felmutatni az adatbázismotor számára

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | SQL-verzió – V12, MsSQL2016 |
| **Hivatkozik**              | [Always Encrypted (adatbázismotor)](https://msdn.microsoft.com/library/mt163865) |
| **Lépések** | A Always Encrypted a bizalmas adatok, például a hitelkártya-számok vagy a nemzeti azonosító számok (például az Egyesült államokbeli társadalombiztosítási számok) védelmére szolgáló szolgáltatás, amely Azure SQL Database vagy SQL Server adatbázisokban van tárolva. Always Encrypted lehetővé teszi az ügyfelek számára a bizalmas adatok titkosítását az ügyfélalkalmazások belsejében, és soha ne fedje fel a titkosítási kulcsokat az adatbázismotor számára (SQL Database vagy SQL Server). Ennek eredményeképpen a Always Encrypted elkülöníti az adattulajdonost (és megtekintheti) és az adatkezelést végzőket (de nem rendelkezik hozzáféréssel) |

## <a id="keys-iot"></a>Titkosítási kulcsok biztonságos tárolása a IoT-eszközön

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Eszköz operációs rendszer – Windows IoT Core, Device connectivity – Azure IoT Device SDK-k |
| **Hivatkozik**              | [TPM a Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm)-ban, [TPM beállítása a Windows IoT Core-on](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Lépések** | A szimmetrikus vagy a tanúsítvány titkos kulcsa biztonságos a hardveres védett tárolóban, például a TPM-vagy intelligenskártya-chipek esetében. A Windows 10 IoT Core támogatja a TPM felhasználóját, és több kompatibilis TPM is használható: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. A belső vezérlőprogram vagy a különálló TPM használata javasolt. A szoftveres TPM csak fejlesztési és tesztelési célokra használható. Ha a TPM elérhetővé válik, és a kulcsok be vannak építve, a jogkivonatot létrehozó kódot írás nélkül kell megírni. | 

### <a name="example"></a>Példa
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Ahogy látható, az eszköz elsődleges kulcsa nem szerepel a kódban. Ehelyett a rendszer a 0. tárolóhelyen tárolja a TPM-ben. A TPM-eszköz egy rövid élettartamú SAS-tokent hoz létre, amelyet aztán a IoT Hubhoz való kapcsolódáshoz használ. 

## <a id="random-hub"></a>A IoT Hub hitelesítéshez elegendő hosszúságú véletlenszerű szimmetrikus kulcs létrehozása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozik**              | –  |
| **Lépések** | IoT Hub tartalmaz egy eszköz-identitási beállításjegyzéket, és egy eszköz kiépítés közben automatikusan létrehoz egy véletlenszerű szimmetrikus kulcsot. Javasoljuk, hogy az Azure IoT Hub Identity Registry ezen funkcióját használja a hitelesítéshez használt kulcs létrehozásához. A IoT Hub az eszköz létrehozásakor is lehetővé teszi az adott kulcs megadását. Ha a rendszer a IoT Hubon kívül hozza létre a kulcsot az eszköz kiépítés során, akkor azt javasoljuk, hogy hozzon létre egy véletlenszerű szimmetrikus kulcsot vagy legalább 256 bitet. |

## <a id="pin-remote"></a>Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Mobile-ügyfél | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy van olyan eszköz-felügyeleti szabályzat, amely használatához PIN-kód szükséges, és lehetővé teszi a távoli törlést |

## <a id="bitlocker"></a>Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker).

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Outlook-ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy van olyan eszköz-felügyeleti házirend, amely PIN-kód/jelszó/automatikus zárolást igényel, és titkosítja az összes adathalmazt (például BitLocker). |

## <a id="rolled-server"></a>Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Identity Server – kulcsok, aláírások és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Lépések** | Ellenőrizze, hogy az aláíró kulcsok át lesznek-e tekerve az Identity Server használatakor. A hivatkozások szakaszban található hivatkozás azt mutatja be, hogy ezt hogyan kell megtervezni anélkül, hogy a rendszer kiesést okozna az Identity Serveren alapuló alkalmazásokhoz. |

## <a id="client-server"></a>Győződjön meg arról, hogy a kriptográfiailag erős ügyfél-azonosító, az ügyfél titkos kulcsa az Identity Serverben van használatban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Győződjön meg arról, hogy a kriptográfiai szempontból erős ügyfél-azonosító, az ügyfél titkos kulcsa használatban van az Identity Serverben. A következő irányelveket kell használni az ügyfél-azonosító és a titkos kulcs létrehozásakor:</p><ul><li>Véletlenszerű GUID létrehozása ügyfél-AZONOSÍTÓként</li><li>Titkosítási szempontból véletlenszerű 256 bites kulcs létrehozása titokként</li></ul>|
