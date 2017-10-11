---
title: "-Microsoft fenyegetés modellezési eszköz - titkosítás Azure |} Microsoft Docs"
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
ms.openlocfilehash: 96e74371fe51a8050a91c86215e3eefab07bbed8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-cryptography--mitigations"></a>Biztonsági keret: Titkosítás |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Csak a jóváhagyott szimmetrikus blokk Rejtjelek és a kulcshosszok](#cipher-length)</li><li>[Használja a jóváhagyott blokk titkosítási módok és szimmetrikus titkosítási inicializálási vektor ismert](#vector-ciphers)</li><li>[Jóváhagyott aszimmetrikus algoritmusok, kulcshossz és kitöltési használata](#padding)</li><li>[Jóváhagyott véletlenszerű szám generátorokat használata](#numgen)</li><li>[Ne használjon szimmetrikus adatfolyam rejtjel.](#stream-ciphers)</li><li>[Jóváhagyott MAC/HMAC/kulccsal kivonatoló algoritmusok használata](#mac-hash)</li><li>[Csak a jóváhagyott titkosítási kivonatot funkciók](#hash-functions)</li></ul> |
| **Adatbázis** | <ul><li>[Az adatbázis-adatok titkosítására erős titkosítási algoritmusok használata](#strong-db)</li><li>[SSIS-csomagok legyen titkosítva, és digitálisan aláírt](#ssis-signed)</li><li>[Kritikus adatbázis securables digitális aláírás hozzáadása](#securables-db)</li><li>[SQL server EKM használatával titkosítási kulcsok védelme](#ekm-keys)</li><li>[AlwaysEncrypted szolgáltatással, ha a titkosítási kulcsok nem fedhető adatbázismotor](#keys-engine)</li></ul> |
| **IoT-eszközök** | <ul><li>[Kriptográfiai kulcsokat tárolja biztonságos helyen az IoT-eszközök](#keys-iot)</li></ul> | 
| **Az IoT átjáró** | <ul><li>[Hozzon létre egy véletlenszerű szimmetrikus kulcsot hosszúnak az IoT Hub-hitelesítés](#random-hub)</li></ul> | 
| **Dynamics CRM mobil ügyfél** | <ul><li>[Győződjön meg arról, egy eszköz-kezelési házirenddel van, amely csak a PIN-kód használatát igényli, és lehetővé teszi a távoli törlése](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook ügyfélben** | <ul><li>[Győződjön meg arról egy eszköz-kezelési házirenddel, amely csak a PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker)](#bitlocker)</li></ul> | 
| **Identity Serverben** | <ul><li>[Győződjön meg arról, hogy aláíró kulcsok vannak tanúsítványváltást Identitáskiszolgálók használata esetén](#rolled-server)</li><li>[Győződjön meg arról, hogy erős titkosítással ügyfél-azonosító, ügyfélkulcs használja az Identity Serverben](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Csak a jóváhagyott szimmetrikus blokk Rejtjelek és a kulcshosszok

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Termékek csak blokk szimmetrikus titkosítási és társított kulcshosszokat, a titkosítási Advisor a szervezet explicit módon jóváhagyott kell használnia. A Microsoftnál engedélyezett szimmetrikus algoritmusok közé tartozik a következő blokk Rejtjelek:</p><ul><li>Új kód AES-128, AES-192 és AES-256-ot is elfogadható</li><li>A meglévő kód visszamenőleges kompatibilitás érdekében három-kulcs 3DES elfogadható</li><li>A termékek szimmetrikus blokk Rejtjelek használatával:<ul><li>Advanced Encryption Standard (AES) szükség az új kódot</li><li>Három-kulcs triple Data Encryption Standard (3DES) megengedett a meglévő kódot a visszamenőleges kompatibilitás érdekében</li><li>Minden más blokk Rejtjelek, többek között a RC2, DES, 2 kulcs 3DES, DESX és bonító, csak akkor használható, a régi adatok visszafejtése, és le kell cserélni, ha a titkosításhoz használt</li></ul></li><li>Szimmetrikus blokkot a titkosítási algoritmusok a minimális kulcshosszúság 128 bit szükség. A csak blokk ajánlott az új kódot AES titkosítási algoritmus (az AES-128, AES-192 és AES-256 elfogadhatók összes)</li><li>Három-kulcs 3DES jelenleg elfogadható, ha már használatban a meglévő kód; AES átmenet ajánlott. DES, DESX, RC2 és bonító nem tekinti biztonságos. Ezek az algoritmusok tartalomkulcsot az előző verziókkal való kompatibilitás érdekében a meglévő adatok csak akkor használható, és adatokat kell újból titkosítja a javasolt blokktitkosításon</li></ul><p>Vegye figyelembe, hogy minden szimmetrikus blokk Rejtjelek kell használni egy jóváhagyott titkosítási mód, amely egy megfelelő inicializálási vektor (IV) használatát igényli. Egy megfelelő IV általában egy véletlenszerű számot, és soha nem állandó érték</p><p>Örökölt vagy egyéb nem jóváhagyott titkosítási algoritmusokat és kulcshosszokat kisebb a felhasznált (nem új adatok írása) meglévő adatok olvasása a szervezet titkosítási Board áttekintése után engedélyezhető. Azonban meg kell a fájl a kivételt a követelménnyel szemben. Emellett a vállalati telepítésekben termékek gondolja át figyelmeztetés rendszergazdák adatokat olvasni a gyenge titkosítás használata esetén. Ilyen figyelmeztetések magyarázó és végrehajthatóként kell lennie. Bizonyos esetekben lehet megfelelő, hogy a csoportházirend szabályozza a gyenge titkosítás használatát</p><p>.NET-algoritmusok használata felügyelt titkosítási agilitást engedélyezett (preferencia szerinti sorrendben)</p><ul><li>AesCng (FIPS-kompatibilis)</li><li>AuthenticatedAesCng (FIPS-kompatibilis)</li><li>AESCryptoServiceProvider (FIPS-kompatibilis)</li><li>(Nem érvényes a FIPS-kompatibilis) AESManaged</li></ul><p>Vegye figyelembe, hogy ezek az algoritmusok egyike adható meg keresztül a `SymmetricAlgorithm.Create` vagy `CryptoConfig.CreateFromName` módszerek a machine.config fájl módosítása nélkül. Azt is, vegye figyelembe, hogy a .NET 3.5 előtt .NET verzióiban AES neve `RijndaelManaged`, és `AesCng` és `AuthenticatedAesCng` vannak > Codeplex webhelyen keresztül érhető el, és van szüksége az alapul szolgáló operációs rendszer a CNG</p>

## <a id="vector-ciphers"></a>Használja a jóváhagyott blokk titkosítási módok és szimmetrikus titkosítási inicializálási vektor ismert

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Minden szimmetrikus blokk Rejtjelek egy jóváhagyott szimmetrikus titkosítási mód együtt kell használni. A csak jóváhagyott módok a következők: CBC és CTS. Különösen az elektronikus kód könyv (ECB) működési mód jelentésparaméterének; ECB használatát a szervezet titkosítási Board felülvizsgálatot igényel. Minden használati OFB, a CFB, a Parancsra, a CCM, és a GCM vagy más titkosítási mód a szervezet titkosítási Board kell vizsgálnia. Újbóli felhasználása a azonos inicializálási vektor (IV) blokk Rejtjelek "Rejtjelek módok adatfolyam" Parancsra, például a titkosított adatok fedhető okozhat. Minden szimmetrikus blokk Rejtjelek is használható kell egy megfelelő inicializálási vektor (IV). Egy megfelelő IV egy kriptográfiai erős, véletlenszerű számot, és soha nem egy állandó értékkel. |

## <a id="padding"></a>Jóváhagyott aszimmetrikus algoritmusok, kulcshossz és kitöltési használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Tiltott titkosítási algoritmusok használatát jelentős kockázat bemutatja a termék biztonsági, és el kell kerülni. Termékek kell használnia csak titkosítási algoritmusokat és társított kulcshosszokat és kitöltési explicit módon a szervezet titkosítási Bizottság által jóváhagyott.</p><ul><li>**RSA -** titkosítás, a kulcscsere és az aláírási használható. RSA-titkosítás csak a OAEP típusú vagy RSA-KEM kitöltési módot kell használnia. A meglévő kódot PKCS #1 1.5-ös verzióját kitöltési mód csak a kompatibilitás használhat. Használjon null szövegtávolság explicit módon le van tiltva. Kulcsok > = 2048 bit szükséges új kódot. A meglévő kódot támogathatja a kulcsok < 2048 bit, csak az visszamenőleges kompatibilitás a szervezet titkosítási testület áttekintése után. Kulcsok < 1024 bit a régi adatok visszafejtése/ellenőrzése csak akkor használható, és kicserélt if használandó titkosítási és aláírási műveletekhez</li><li>**ECDSA -** csak aláírás használható. Az ECDSA > =, 256 bites kulcsok szükséges új kódot. ECDSA alapú aláírások a három NIST jóváhagyott görbék egyikét kell használnia (P-256, 384 vagy P521). Alaposan elemzett görbék csak a szervezete titkosítási Board áttekintése után is használható.</li><li>**ECDH -** csak a kulcscseréhez használt használható. A ECDH > 256 bites kulcsok = új kód szükséges. Kulcscsere ECDH-alapú a három NIST jóváhagyott görbék egyikét kell használnia (P-256, 384 vagy P521). Alaposan elemzett görbék csak a szervezete titkosítási Board áttekintése után is használható.</li><li>**DSA -** áttekintésre és jóváhagyásra a szervezet titkosítási tábláról után elfogadható lehet. Lépjen kapcsolatba a biztonsági tanácsadó ütemezni a szervezet titkosítási Board áttekintése. Ha jóváhagyja a DSA használatát, vegye figyelembe, hogy szüksége lesz a kulcsok használatát tiltó kisebb, mint 2048 bit hosszúságú. CNG támogatja a Windows 8-től 2048 bites és nagyobb kulcshosszokat.</li><li>**Diffie-Hellman -** munkamenet kulcskezelés csak használható. Kulcshossz > = 2048 bit szükséges új kódot. A meglévő kódot támogathatja a kulcshossza < 2048 bit, csak az visszamenőleges kompatibilitási a szervezet titkosítási testület áttekintése után. Kulcsok < 1024 bit nem használható.</li><ul>

## <a id="numgen"></a>Jóváhagyott véletlenszerű szám generátorokat használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Termékek jóváhagyott véletlenszerű szám generátorokat kell használnia. Pseudorandom funkciókat, például a C futásidejű függvény VÉL, a .NET-keretrendszer osztály System.Random vagy a rendszer funkciókat, például a GetTickCount kell, ezért soha nem használhatók ilyen kódban. A kettős elliptikus görbéjű véletlenszerű szám generátor (DUAL_EC_DRBG) algoritmus használata nem engedélyezett</p><ul><li>**CNG -** BCryptGenRandom (az ajánlott, ha a hívó bármely IRQL [Ez azt jelenti, hogy PASSIVE_LEVEL] 0-nál nagyobb lehet, hogy futtassa BCRYPT_USE_SYSTEM_PREFERRED_RNG jelző használata)</li><li>**CAPI -** cryptGenRandom</li><li>**A Win32/64-** (új megvalósítások használja BCryptGenRandom vagy CryptGenRandom) RtlGenRandom * rand_s * SystemPrng (a kernel mód)</li><li>**. A NET -** RNGCryptoServiceProvider vagy RNGCng</li><li>**A Windows Store Apps -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom vagy. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef véletlenszerű, size_t száma, uint8_t *bájt)</li><li>**Apple OS X (< 10.7)-** / dev/véletlenszerű lekéréséhez használja véletlenszerű számok</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom osztály. Vegye figyelembe, hogy az Android 4.3 (ún Bean), a fejlesztők kell hajtsa végre a javasolt megoldás Android rendelkező /dev/urandom vagy /dev/random entrópia PRNG explicit módon inicializálni az alkalmazások frissítése</li></ul>|

## <a id="stream-ciphers"></a>Ne használjon szimmetrikus adatfolyam rejtjel.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Szimmetrikus adatfolyam Rejtjelek, például az RC4, nem kell használni. Szimmetrikus adatfolyam Rejtjelek helyett termékek használjon a blokktitkosításon, kifejezetten AES legalább 128 bites kulcsot használnak. |

## <a id="mac-hash"></a>Jóváhagyott MAC/HMAC/kulccsal kivonatoló algoritmusok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Termékek csak a jóváhagyott üzenethitelesítő kódot (MAC) vagy a kivonat-alapú üzenethitelesítési kód (HMAC) algoritmusok kell használnia.</p><p>Üzenethitelesítő kódot (MAC) csatolva egy üzenetet, amely lehetővé teszi, hogy a küldő hitelességének, mind az üzenet egy titkos kulccsal sértetlenségének ellenőrzéséhez a címzett információ. Vagy a kivonat-alapú MAC használatát ([HMAC](http://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) vagy [titkosítási-blokkalapú MAC](http://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) van megengedett addig, amíg minden alapul szolgáló kivonatoló vagy szimmetrikus titkosítási algoritmusok is használatra jóváhagyott; jelenleg ez magában foglalja a HMAC-SHA2 funkciók (HMAC-SHA-256, HMAC-SHA384 és HMAC-SHA512) és a CMAC/OMAC1 és OMAC2 blokkolja a Mac számítógépek (alapulhat AES) titkosító-alapú.</p><p>Lehet, hogy a HMAC-SHA1 használata megengedett platform kompatibilitás, de be kell Kivételt képez ez az eljárás fájlba, és a szervezet titkosítási felülvizsgálati változni. Kisebb, mint 128 bit a üzenetkivonatokat csonkolása nem engedélyezett. Egy kulcs adatok nincs jóváhagyva, és a szervezet titkosítási Board kell alávetni kivonatoló metódusok használata előtt tekintse át a felhasználói használatával.</p>|

## <a id="hash-functions"></a>Csak a jóváhagyott titkosítási kivonatot funkciók

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Termékek az SHA-2 család kivonatoló algoritmus (SHA-256, SHA384 és az SHA512) kell használnia. Rövidebb kivonatát van szüksége, például a 128 bites kimeneti hosszánál ahhoz, hogy elférjen kialakítása során vegye figyelembe, a rövidebb MD5 kivonatoló adatszerkezet termékekért felelős csoportokkal előfordulhat, hogy csonkolja a SHA2 kivonatok (általában az SHA256 titkosítást) egyikét. Vegye figyelembe, hogy SHA384 SHA512 csonkolt verzióját. Csonkolása a kriptográfiai kivonatokat kisebb, mint 128 bites biztonsági okokból nem engedélyezett. Új kód nem használható a MD2, MD4, MD5, SHA-0, SHA-1 vagy RIPEMD kivonatoló algoritmus. Kivonatütköztetés számításilag megvalósítható az ezek az algoritmusok, ami gyakorlatilag feltörésüket.</p><p>A felügyelt titkosítási agilitást .NET kivonatoló algoritmusainak engedélyezett (preferencia szerinti sorrendben):</p><ul><li>SHA512Cng (FIPS-kompatibilis)</li><li>SHA384Cng (FIPS-kompatibilis)</li><li>SHA256Cng (FIPS-kompatibilis)</li><li>SHA512Managed (nem érvényes a FIPS-kompatibilis) (használni SHA512 HashAlgorithm.Create vagy CryptoConfig.CreateFromName hívásokban algoritmus neve)</li><li>SHA384Managed (nem érvényes a FIPS-kompatibilis) (használja az SHA384 a HashAlgorithm.Create vagy CryptoConfig.CreateFromName algoritmus neve)</li><li>SHA256Managed (nem érvényes a FIPS-kompatibilis) (használja az SHA-256 algoritmus neve HashAlgorithm.Create vagy CryptoConfig.CreateFromName hívásokban)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibilis)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibilis)</li></ul>| 

## <a id="strong-db"></a>Az adatbázis-adatok titkosítására erős titkosítási algoritmusok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Egy titkosítási algoritmus kiválasztása](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Lépések** | Titkosítási algoritmusok határozza meg, amelyek jogosulatlan felhasználók számára nem lehet könnyen visszavonni az adatátalakításokat. SQL Server lehetővé teszi a rendszergazdák és fejlesztők közül több algoritmusokat, beleértve az DES, háromszoros DES, TRIPLE_DES_3KEY, RC2, RC4, 128 bites RC4, DESX, 128 bites AES, 192 bites AES és 256 bites AES kiválasztása |

## <a id="ssis-signed"></a>SSIS-csomagok legyen titkosítva, és digitálisan aláírt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A forrás digitális aláírással megőrzött csomagok azonosítása](https://msdn.microsoft.com/library/ms141174.aspx), [fenyegetés és biztonsági megoldás (integrációs szolgáltatások)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Lépések** | Csomag forrása a személy vagy szervezet, amely a csomag létrehozása. Ismeretlen vagy nem megbízható forrásból származó csomag futtató kockázatos lehet. Az illetéktelen jogosulatlan SSIS-csomagok, digitális aláírások kell használni. Továbbá védelme érdekében a csomagok tárolására/átvitel során, SSIS-csomagok, hogy titkosítani kell |

## <a id="securables-db"></a>Kritikus adatbázis securables digitális aláírás hozzáadása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ALÁÍRÁS (Transact-SQL) hozzáadása](https://msdn.microsoft.com/library/ms181700) |
| **Lépések** | Esetekben, amikor biztonságos kritikus adatbázis integritásának ellenőrzését digitális aláírások kell használni. Például egy tárolt eljárás, függvény, assembly vagy eseményindító adatbázis securables digitális aláírás. Az alábbiakban látható egy példa, ha ez akkor lehet hasznos: ossza meg velünk tegyük fel például, egy független Szoftverszállító (független szoftvergyártók) megadta a szoftver az ügyfeleknek egyikére kézbesíteni a támogatási szolgálathoz. Mielőtt támogatásának biztosítása az ISV szeretné győződjön meg arról, hogy a szoftver biztonságos adatbázis nem sérült vagy módosították vagy véletlenül vagy egy rosszindulatú kísérlet után. A biztonságos digitálisan aláírt, az ISV ellenőrizheti a digitális aláírás és a sértetlenségének ellenőrzése.| 

## <a id="ekm-keys"></a>SQL server EKM használatával titkosítási kulcsok védelme

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [SQL Server a bővíthető kulcskezelési (EKM)](https://msdn.microsoft.com/library/bb895340), [a bővíthető kulcskezelési használata az Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Lépések** | SQL Server bővíthető kulcskezelési lehetővé teszi, hogy a rendszer ne tárolja őket egy be eszköz, például intelligens kártyával, USB-eszköz vagy EKM/HSM modul az adatbázisfájlok védő titkosítási kulcsokat. Ennek a szolgáltatásnak köszönhetően az adatok védelmét a adatbázis-rendszergazdák (kivéve a sysadmin (rendszergazda) csoport tagjai). Adatok titkosíthatók, hogy csak az adatbázis-felhasználó hozzáfér a külső EKM/HSM modul titkosítási kulcsok használatával. |

## <a id="keys-engine"></a>AlwaysEncrypted szolgáltatással, ha a titkosítási kulcsok nem fedhető adatbázismotor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Az SQL Azure, a helyi üzemeltetésű |
| **Attribútumok**              | SQL-verzió - 12-es verzió, MsSQL2016 |
| **Hivatkozások**              | [Mindig titkosítja (adatbázismotor)](https://msdn.microsoft.com/library/mt163865) |
| **Lépések** | Mindig titkosított nem úgy tervezték, hogy a bizalmas adatok védelmét, hitelkártyaszámok vagy a nemzeti azonosító szám (pl. Egyesült államokbeli társadalombiztosítási számot), például az Azure SQL Database vagy az SQL Server adatbázis tárolja. Titkosított mindig lehetővé teszi az ügyfelek ügyfélalkalmazások található bizalmas adatok titkosításához, és soha ne adja meg a titkosítási kulcsokat az adatbázismotor (SQL-adatbázis vagy SQL Server). Ennek eredményeképpen az adatok tulajdonosa (és megtekinthesse) elkülönítése Always Encrypted funkciója és azokra, akik az adatok kezelése (de nem hozzáféréssel kell rendelkeznie) |

## <a id="keys-iot"></a>Kriptográfiai kulcsokat tárolja biztonságos helyen az IoT-eszközök

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Az eszköz operációs rendszere - Windows IoT Core Eszközkapcsolatok - Azure IoT eszközoldali SDK-k |
| **Hivatkozások**              | [A Windows IoT Core TPM](https://developer.microsoft.com/windows/iot/docs/tpm), [Windows IoT alapvető TPM beállítása](https://developer.microsoft.com/windows/iot/win10/setuptpm), [Azure IoT eszköz SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Lépések** | Symmetric vagy a tanúsítvány titkos kulcsokat biztonságosan hardveres tárolási például TPM vagy intelligens kártyás processzorok védett. Windows 10 IoT Core támogatja a felhasználó TPM és több kompatibilis TPM használható: https://developer.microsoft.com/windows/iot/win10/tpm. Egy belső vezérlőprogram vagy különálló TPM használata ajánlott. A szoftver TPM csak fejlesztési és tesztelési célra használható. Ha érhető el TPM, és a kulcsok kiépített azt, a kódot, amely a token hoz létre a következő formában rögzített kódolási benne bizalmas információk nélkül. | 

### <a name="example"></a>Példa
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Amint is látható, az eszköz elsődleges kulcs nincs jelen a kódban. Ehelyett tárolódik adatszalagot a 0, a TPM-ben. TPM eszköz hoz létre egy rövid élettartamú SAS-jogkivonatot, majd az IoT-központhoz való kapcsolódáshoz használt. 

## <a id="random-hub"></a>Hozzon létre egy véletlenszerű szimmetrikus kulcsot hosszúnak az IoT Hub-hitelesítés

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Átjáró choice - Azure IoT Hub |
| **Hivatkozások**              | N/A  |
| **Lépések** | Az IoT-központ Identitásjegyzékhez eszköz tartalmaz, és közben az eszköz kiépítése, automatikusan létrehoz egy véletlenszerű szimmetrikus kulcsot. Javasoljuk, hogy ez az Azure IoT Hub Identitásjegyzékhez funkcióját használja a hitelesítéshez használt kulcs létrehozásához. Az IoT-központ is lehetővé teszi egy kulccsal, hogy az eszköz létrehozásakor meg kell adni. A kulcs az IoT-központ kívül jön létre, eszköz-üzembehelyezési során, ajánlott hozzon létre egy véletlenszerű szimmetrikus kulcs, vagy legalább 256 bit. |

## <a id="pin-remote"></a>Győződjön meg arról, egy eszköz-kezelési házirenddel van, amely csak a PIN-kód használatát igényli, és lehetővé teszi a távoli törlése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM mobil ügyfél | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, egy eszköz-kezelési házirenddel van, amely csak a PIN-kód használatát igényli, és lehetővé teszi a távoli törlése |

## <a id="bitlocker"></a>Győződjön meg arról egy eszköz-kezelési házirenddel, amely csak a PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker)

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM Outlook ügyfélben | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról egy eszköz-kezelési házirenddel, amely csak a PIN-kód/jelszó vagy automatikus zárolás igényel, és titkosítja az összes adatot (pl. Bitlocker) |

## <a id="rolled-server"></a>Győződjön meg arról, hogy aláíró kulcsok vannak tanúsítványváltást Identitáskiszolgálók használata esetén

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Serverben | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Identitáskezelési kiszolgáló - kulcsokat, aláírás és titkosítás](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Lépések** | Győződjön meg arról, hogy aláíró kulcsok vannak tanúsítványváltást Identitáskiszolgálók használatakor. A hivatkozás a references szakaszában ismerteti, hogyan ez kell megtervezni anélkül, hogy ez az alkalmazások támaszkodva Identitáskiszolgálók kimaradások esetén. |

## <a id="client-server"></a>Győződjön meg arról, hogy erős titkosítással ügyfél-azonosító, ügyfélkulcs használja az Identity Serverben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Serverben | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Győződjön meg arról, hogy erős titkosítással ügyfél-azonosító, titkos ügyfélkulcs az Identity Serverben használt. A következő irányelveket kell követni egy ügyfél-azonosító és a titkos kulcs létrehozása közben:</p><ul><li>Hozzon létre egy véletlenszerű GUID Azonosítót, az ügyfél-azonosító</li><li>Hozzon létre egy kriptográfiai véletlenszerű 256 bites kulcsot a titkos kulcsként</li></ul>|