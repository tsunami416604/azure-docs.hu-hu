---
title: Gyakori kérdések – Azure dedikált HSM-be |} A Microsoft Docs
description: Gyakori Azure dedikált HSM-be más témakörökre vonatkozó kérdések
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: barclayn
ms.openlocfilehash: 2c77f5abac69a1bade523d2a4b6793c2a9df8cf2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447863"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

A Microsoft Azure dedikált HSM kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="the-basics"></a>Alapismeretek

### <a name="q-what-is-a-hardware-security-module-hsm"></a>K: Mit jelent a hardveres biztonsági modul (HSM)?

Hardveres biztonsági modul (HSM) segítségével védheti és felügyelheti a titkosítási kulcsok egy fizikai számítástechnikai eszköz. A HSM modulokban tárolt kulcsok a titkosítási műveletekhez használható. A megosztottkulcs-anyag biztonságosan módosítás ellen védett, illetéktelen hozzáférést jelző hardveres modulokban marad. A HSM csak lehetővé teszi, hogy hitelesítése és engedélyezése az alkalmazások. A kulcsok sohasem hagyják el a HSM védett körét.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>K: Mi az Azure dedikált HSM kínál?

Dedikált HSM Azure egy felhőalapú szolgáltatás, amely az Azure-adatközpontok, amely közvetlenül csatlakozik egy ügyfél virtuális hálózatán lévő üzemeltetett HSM-EK. A hardveres biztonsági modulokat a dedikált hálózati berendezések (Gemalto a SafeNet hálózati HSM 7 modell A790). Közvetlenül az ügyfelek általi magánhálózati IP-címtér telepítésük és a Microsoft nem érik el a HSM titkosítási működését. Csak az ügyfél rendelkezik teljes körű felügyeleti és titkosítási felett ezeket az eszközöket. Ügyfelei felelősek az eszköz felügyeletéhez, és hozzáférhetnek a teljes tevékenységeket tartalmazó naplók közvetlenül az eszközeikről. Dedikált HSM-EK segíthet az ügyfeleknek felel meg a megfelelőségi és szabályozási követelmények például a FIPS 140-2 Level 3, HIPAA, PCI-DSS és eIDAS és sok más.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>K: Milyen hardver szolgál a dedikált HSM?

A Microsoft közzétegyék az általuk Gemalto a dedikált HSM Azure szolgáltatás biztosításához. Használja az adott eszköz a [SafeNet Luna hálózati HSM 7 modell A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Az eszköz nem csak a FIPS 140-2-3. szint érvényesítve belső vezérlőprogram biztosít, de is kínál a kis késleltetésű, nagy teljesítményű és nagy kapacitású 10 partíciók keresztül. 

### <a name="q-what-is-an-hsm-used-for"></a>K: Mire használják a HSM-eket?

A HSM-ek rendeltetése az olyan titkosítási funkciókhoz használt titkosítási kulcsok tárolása, mint az SSL, az adattitkosítás, a PKI (nyilvános kulcsokra épülő infrastruktúra), a DRM (digitális jogok kezelése), és dokumentumok aláírása.

### <a name="q-how-does-dedicated-hsm-work"></a>K: Hogyan működik a dedikált HSM?

Ügyfelek üzembe helyezhetik a PowerShell vagy parancssori felület használatával meghatározott régióiba HSM-EK. Az ügyfél adja meg, milyen virtuális hálózat, a HSM-EK fog kell csatlakozik, és egyszer építette ki a HSM-eket a kijelölt alhálózat magánhálózati IP-címtér az ügyfél a hozzárendelt IP-címek, elérhető lesz. Majd ügyfelek csatlakozhatnak a HSM-EK SSH-val a készülék felügyeleti és állítsa be a HSM-ügyfélkapcsolatokat, a felügyelet inicializálni a HSM-EK, partíciók létrehozása, határozza meg, és például partíciós tisztviselő, a titkosítási igazgató és a titkosítási felhasználói szerepköröket. Az ügyfél majd használni a HSM eszközök/SDK/ügyfélszoftver megadott Gemalto titkosítási műveleteket alkalmazásaikból.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>K: Milyen szoftverre a dedikált HSM szolgáltatást?

Gemalto egyszer kiépítette a Microsoft HSM-eszközre vonatkozó összes szoftver választékát kínálja. A szoftvert mindig elérhető legyen a [Gemalto támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/). Gemalto támogatja, és van egy ügyfél-azonosító, amely lehetővé teszi a hozzáférést és a megfelelő Szoftverletöltés regisztrálni a dedikált HSM szolgáltatást használó ügyfelek szükségesek. Támogatott szoftver verziója 7.2, amely a FIPS 140-2 – 3. szint érvényesítve belső vezérlőprogram verziója 7.0.3 kompatibilis. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>K: Kínál Azure dedikált HSM-be a jelszó- és PED-alapú hitelesítést?

Jelenleg Azure dedikált HSM-be csak révén a HSM-EK jelszóalapú hitelesítés.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>K: Azure dedikált HSM üzemelteti a HSM-EK a számomra?

A Microsoft csak a Gemalto SafeNet Luna hálózati hardveres biztonsági MODULT a dedikált HSM szolgáltatáson keresztül biztosít, és nem tárolható az ügyfél által biztosított eszközöket.

### <a name="q-does-azure-dedicated-hsm-support-pinetf-features-or-meet-pci-hsm-v1-or-v2-certification-requirements"></a>K: Nem Azure dedikált HSM-be PIN-kód/ETH-szolgáltatásokat támogatják, és megfelel a PCI HSM v1 vagy v2-minősítési követelmények?

A dedikált HSM Azure szolgáltatás SafeNet Luna hálózati HSM 7 (modell A790) eszközöket használ. Ezek az eszközök nem támogatják a fizetési HSM bizonyos funkciók (például PIN-kód vagy ETH) vagy a tanúsítványok. Ha szeretné, hogy Azure dedikált HSM szolgáltatást, hogy támogatja a HSM-EK fizetési a jövőben, adja meg az alábbi visszajelzés.

## <a name="interoperability"></a>Együttműködési lehetőség

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>K: Hogyan csatlakozhat az alkalmazásom egy dedikált HSM?

Gemalto HSM eszközök/SDK/ügyfélszoftver megadott használatával az alkalmazások a titkosítási műveleteket. A szoftvert mindig elérhető legyen a [Gemalto támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/). Gemalto támogatja, és van egy ügyfél-azonosító, amely lehetővé teszi a hozzáférést és a megfelelő Szoftverletöltés regisztrálni a dedikált HSM szolgáltatást használó ügyfelek szükségesek.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>K: Egy alkalmazás kapcsolódhat dedikált HSM egy vagy több régió, vagy a másik virtuális Hálózattal?

Igen, meg kell használnia [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) virtuális hálózatok közötti kapcsolat létrehozásához egy adott régión belül. A régiók közötti kapcsolatot, kell használnia [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>K: Is dedikált HSM is szinkronizálja a helyszíni HSM-EK?

Igen, szinkronizálhatja a helyszíni HSM-EK a dedikált HSM-mel. [Pont-pont típusú VPN vagy a pont – hely](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolatot létesíteni a helyszíni hálózattal létesített kapcsolathoz is használható.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>K: Titkosítsa a dedikált HSM-ben tárolt kulcsok használata más Azure-szolgáltatások által használt adatokat?

Nem. Az Azure dedikált HSM-EK csak érhetők el a virtuális hálózaton belül.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>K: Lehet importálni kulcsok egy meglévő helyszíni HSM-ből dedikált HSM-be?

Igen, ha rendelkezik a helyszíni HSM-EK SafeNet Gemalto. Több módszerrel. Tekintse meg a Gemalto HSM dokumentációját.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>K: Mely operációs rendszerek dedikált HSM kliens szoftver által támogatott?

* Windows, Linux, Solaris, AIX, a HP-UX, a FreeBSD
* Virtuális: VMware-, Hyper-v, Xen-KVM vagy

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>K: Hogyan konfigurálható a saját ügyfélalkalmazás több HSM moduljaiban tárolt több partícióval rendelkező magas rendelkezésre állású konfiguráció létrehozásához?

Ahhoz, hogy magas rendelkezésre állású, be kell állítania a HSM-ügyfél Alkalmazáskonfiguráció minden HSM-ből a partíciók használatára. Tekintse meg a Gemalto HSM ügyfél szoftver dokumentációját.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>K: Dedikált HSM milyen hitelesítési mechanizmusok támogatottak?

Azure dedikált HSM SafeNet hálózati HSM 7 berendezések (modell A790) használ, és támogatják a jelszóalapú hitelesítés.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>K: Ügyfél SDK-k API-k, szoftverek érhető el a dedikált HSM-mel használni?

PKCS #11, a Java (JCA/KVV), a Microsoft CAPI és a CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>K: Importálás/áttelepíthetek Luna 5/6 HSM moduljaiban tárolt kulcsokat Azure dedikált HSM?

Igen. Tekintse meg a Gemalto migrálási útmutatót. 

## <a name="using-your-hsm"></a>A HSM használata

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>K: Hogyan válasszak e használni az Azure Key Vaultot vagy az Azure dedikált HSM-be?

Az Azure, a dedikált HSM a megfelelő választás az olyan vállalatok számára az Azure a helyszíni alkalmazások által használt HSM-ba való migrálás. Dedikált HSM-EK minimális változtatása mellett alkalmazást telepítene át lehetőséget nyújtanak. Ha a titkosítási műveleteket a az alkalmazás kódja egy Azure virtuális Gépen vagy Web App, dedikált HSM használhatják. A modellek IaaS (szolgáltatott infrastruktúra), amely támogatja a HSM-EK, mivel a kulcstároló használhatják a dedikált HSM-be, például a végrehajtotta az SSL, ADCS (Active Directory tanúsítványszolgáltatás), az Application gateway vagy a traffic manager szoftvert általában shrink-wrapped vagy hasonló nyilvános kulcsokra épülő infrastruktúra eszközeit, dokumentum-aláíráshoz, kód aláírása, vagy a főkulcs egy hardveres biztonsági modulban, egy (bővíthető kulcskezelés) EKM-szolgáltató használatával a TDE (átlátható adatbázis-titkosítás) konfigurált SQL Server (IaaS) használt eszközök és alkalmazások. Az Azure Key Vault ideális választás "született – felhőalapú" alkalmazások vagy a rest-forgatókönyvek, ahol vásárlói adatokat dolgozza fel a PaaS (szolgáltatásként nyújtott platformon) vagy SaaS (szolgáltatott szoftver) forgatókönyvek, például az Office 365-ügyfél kulcsot, az Azure Information Protection-titkosításhoz , Az azure Disk Encryption az Azure Data Lake Store-titkosítás az ügyfél által felügyelt kulcsokat, Azure Storage-titkosítás az ügyfél által felügyelt kulcsot, és az Azure SQL-ügyfél által felügyelt kulcsot.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>K: Milyen használati forgatókönyvek ajánlott igény szerint az Azure dedikált HSM-be?

Az Azure, a dedikált HSM leginkább megfelelő áttelepítési forgatókönyvek. Ez azt jelenti, hogy a helyszíni alkalmazások az Azure-ban, amely már a HSM-EK áttelepítésekor. Ez az alkalmazás minimális módosításait az Azure-bA migrálásához, alacsony terhelésű lehetőséget kínál. Ha a titkosítási műveleteket a futó Azure virtuális Gépen vagy Web App az alkalmazás kódjában, dedikált HSM használható. Általánosságban véve shrink-wrapped szoftvert a modellek IaaS (szolgáltatott infrastruktúra), amely támogatja a HSM-EK, mivel használhatják a kulcstároló a dedikált HSM-be, például:

* Application gateway vagy a traffic manager végrehajtotta SSL-hez
* ADCS (Active Directory tanúsítványszolgáltatás)
* Nyilvános kulcsokra épülő infrastruktúra hasonló eszközök
* A dokumentum-aláíráshoz használt eszközök és alkalmazások
* Kód aláírása
* A főkulcs egy hardveres biztonsági modulban, egy (bővíthető kulcskezelés) EKM-szolgáltató használatával a TDE (átlátható adatbázis-titkosítás) konfigurált SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>K: Használható dedikált HSM az Office 365 Customer Key, Azure Information Protection, Azure Data Lake Store, Disk Encryption, Azure Storage Encryption, Azure SQL TDE szolgáltatással?

Nem. Dedikált HSM van kiépítve közvetlenül egy felhasználói privát IP-cím címtér így ezt nem érhető el nem más Azure-ban vagy a Microsoft-szolgáltatások.

## <a name="administration-access-and-control"></a>Felügyeleti hozzáférés és vezérlés

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>K: Az ügyfél beolvasása a HSM-EK a dedikált HSM-EK teljes kizárólagos felett?

Igen. Minden egyes HSM készülék teljes egy egyetlen ügyfél számára fenntartott, és senki más nem felügyeleti vezérlő egyszer kiépített és a rendszergazda jelszava megváltozott.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>K: Milyen szintű hozzáférésre van a Microsoft a HSM-be?

A Microsoft nem rendelkezik minden olyan felügyeleti és titkosítási szabályozhatja a hardveres biztonsági MODULT. A Microsoft rendelkezik alapszintű telemetriai adatokat, például a hőmérséklet és az összetevő állapotának lekéréséhez sorosport-kapcsolaton keresztül a szintű hozzáférés figyelése. Ez lehetővé teszi a Microsoft health-problémák proaktív értesítésben. Ha szükséges, az ügyfél letilthatja ezt a fiókot.

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>K: A Microsoft vagy a Microsoft tárelérési kulcsok saját dedikált HSM-ben is?

Nem. A Microsoft nem rendelkezik az ügyfél lefoglalt dedikált HSM tárolt kulcsok elérését.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>K: Frissíthetem a HSM-EK nekem kiosztott szoftverek és belső vezérlőprogramjának?

Ajánlott segítségre van szüksége, a Microsoft határozottan javasolja, nem a HSM-en szoftverek és belső vezérlőprogram frissítése. Az ügyfél azonban teljes körű felügyeletet például a szoftverek és belső vezérlőprogram frissítése, ha a funkciók szükségesek a különböző verziójú belső vezérlőprogrammal rendelkezik. Mielőtt módosítaná, milyen vonzatokkal jár meg kell értenie, mivel ez sikerült, például a hatás FIPS érvényesítve állapotát. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>K: Hogyan kezelheti a dedikált HSM?

Férnek hozzájuk segítségével kezelheti a dedikált HSM-EK SSH-val.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>K: Hogyan kezelhetem a partíciókat a dedikált HSM-en?

A Gemalto HSM ügyfélszoftvert a HSM-ekről és a partíciók kezelésére szolgál.

### <a name="q-how-do-i-monitor-my-hsm"></a>K: Hogyan figyelhetem a saját HSM?

Egy ügyfél HSM tevékenységeket tartalmazó naplók a syslog- és SNMP-n keresztül teljes hozzáféréssel rendelkezik. Syslog-kiszolgálónak vagy SNMP-kiszolgáló beállítása a naplók és események fogadása a HSM-eket kell egy ügyfél.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>K: Kaphatok minden HSM-műveletek teljes hozzáférési napló dedikált HSM-ből?

Igen. Naplók küldésével HSM a készülék a syslog-kiszolgálónak

## <a name="high-availability"></a>Magas rendelkezésre állás

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>K: A lehetséges ugyanabban a régióban, illetve több régióban a magas rendelkezésre állású konfigurálásához?

Igen. Magas rendelkezésre állású konfiguráció és beállítás a HSM-ügyfélszoftver Gemalto által biztosított hajtott végre. HSM-EK a helyszíni HSM-EK ugyanazon a VNETEN vagy más virtuális hálózatok ugyanabban a régióban vagy régiók között, vagy helyek közötti virtuális hálózat csatlakozik, vagy pont-pont típusú VPN azonos magas rendelkezésre állású konfiguráció lehet hozzáadni.

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Is hozzáadhatok HSM-EK a helyszíni hálózatról egy magas rendelkezésre állási csoporthoz a Azure dedikált HSM-mel?

Igen. Meg kell felelniük a magas rendelkezésre állással SafeNet Luna hálózati HSM-7.

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Is hozzáadhatok Luna 5/6 HSM-EK a helyszíni hálózat egy magas rendelkezésre állási csoporthoz a Azure dedikált HSM-mel?

Nem.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>K: Hány HSM-EK adhatok hozzá ugyanazt a magas rendelkezésre állású konfigurációt egy egyszeri alkalmazásból?

16.

## <a name="support"></a>Támogatás

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>K: Mit jelent a dedikált HSM szolgáltatás SLA?

Jelenleg nincs nincs biztosítva a dedikált HSM-szolgáltatás szolgáltatói szerződés. A Microsoft biztosítja a hálózati szintű hozzáférés az eszközön, és ezért alkalmazni a standard szintű Azure szolgáltatói szerződésekkel.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>K: A hardveres biztonsági modulok használata az Azure dedikált HSM által védett?

Az Azure-adatközpontok rendelkezik kiterjedt fizikai és procedurális biztonsági vezérlők. Emellett a dedikált HSM-EK egy korlátozott hozzáférésű további területén az adatközpontban futnak. Ezek a területek további fizikai hozzáférés-vezérlést és a fokozott biztonság videokamera felügyeleti rendelkezik.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>K: Mi történik, ha a biztonsági szabályzat megsértése vagy illetéktelen módosítást esemény hardver?

Dedikált HSM-szolgáltatást használ SafeNet hálózati HSM 7 készülékek. Ezek a készülékek támogatja a fizikai és logikai illetéktelen módosítások észleléséhez. Ha valaha az illetéktelen esemény a rendszer automatikusan zeroized a HSM-eket.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>K: Hogyan biztosítja, hogy a kulcsokat a saját dedikált HSM-EK nem vesznek el miatt hiba vagy egy rosszindulatú belső támadás?

Erősen ajánlott egy helyszíni HSM biztonsági mentési eszköz használata a vész-helyreállítási rendszeres rendszeres biztonsági mentése a HSM-EK végrehajtásához. Szüksége lesz egy helyi munkaállomáson, egy HSM biztonsági mentési eszköz csatlakozik a társ-társ vagy helyek közötti VPN-kapcsolat használatával.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>K: Hogyan dedikált HSM-támogatást kérni?

Ugyanúgy, mint más Azure-szolgáltatásokhoz kap támogatást. Az Azure ügyfélszolgálata Gemalto támogatási attól függően, így szükség szerint továbbítja.

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>K: Hogyan szerezhetem be a dedikált HSM-ügyfélszoftvert, a dokumentáció, a belső vezérlőprogram lemezképeiről a hozzáférést?

Ügyfél együttműködve kell közvetlenül hozzáférhet a HSM-ügyfélszoftvert, dokumentáció, az operációs rendszer és belső vezérlőprogram lemezképeiről Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>K: Ha a biztonsági rés található és a egy javítási Gemalto, frissítése és javításokat az operációs rendszer/belső vezérlőprogram felelős által kiadott?

A Microsoft nem tudnak csatlakozni az ügyfelek számára lefoglalt HSM-EK. Ügyfelek frissítenie kell, majd a HSM-EK javítására.

## <a name="cryptography-and-standards"></a>Titkosítás és előírások

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>K: Rendszer biztonságosan tárolja a titkosítási kulcsokat a legfontosabb adataimat a dedikált HSM-ben?

Igen, a dedikált HSM SafeNet hálózati HSM 7 berendezések, hogy használja a 3-es szintű FIPS 140-2 ellenőrzött HSM-EK építi ki. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>K: Milyen titkosítási kulcsokat és algoritmusok dedikált HSM által támogatott?

Dedikált HSM szolgáltatás kiosztja SafeNet hálózati HSM 7 készülékek. Számos titkosítási kulcstípusok és algoritmusokat, beleértve a támogatják: Teljes körű Suite B-támogatás

* Aszimmetrikus:
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptikus görbe alapú
  * Titkosítás (ECDSA, ECDH, Ed25519, ECIES) az elnevezett, felhasználó által definiált, és Brainpool görbék, KCDSA
* A szimmetrikus:
  * AES-GCM
  * Háromszoros DES
  * DES
  * ARIA, KEZDŐÉRTÉK
  * RC2
  * RC4
  * RC5 ALAPÚ TITKOSÍTÁS
  * CAST
  * Kivonatoló/üzenet kivonatoló/HMAC-val: AZ SHA-1, SHA-2, SM3
  * Kulcs származtatását: SP800-108 számláló mód
  * Kulcs Alkalmazásburkoló: SP800-38F
  * Véletlenszám-generálás: A FIPS 140-2 jóváhagyott DRBG (SP 800 – 90 Parancsra üzemmód), BSI DRG.4 betartása

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>K: Dedikált HSM FIPS 140-2 3. szint érvényesítve van?

Igen. Dedikált HSM-szolgáltatás látja el, hogy használja a 3-es szintű FIPS 140-2 ellenőrzött HSM-EK SafeNet hálózati HSM 7 készülékek.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>K: Mit kell tennie, hogy e működnek a dedikált HSM FIPS 140-2 3. szint érvényesítve mód?

A dedikált HSM-szolgáltatás látja el SafeNet Luna hálózati HSM 7 készülékek. Ezek a készülékek használata 3-es szintű FIPS 140-2 ellenőrzött HSM-EK. A telepített alapértelmezett konfigurációja, az operációs rendszer és a belső vezérlőprogram egyúttal FIPS használatával érvényesített. Nem kell semmit sem a FIPS 140-2-3. szint megfelelőségét.

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Hogyan biztosítja az ügyfél, hogy ha hardveres biztonsági MODULT az üzemből az összes megosztottkulcs-anyag törli?

Kérő megszüntetés, mielőtt egy ügyfél zeroized Crossover HSM ügyféleszközök megadott Gemalto kell rendelkeznie.

## <a name="performance-and-scale"></a>Teljesítmény és méret

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>K: Hány titkosítási műveletek száma másodpercenként a dedikált HSM-mel támogatottak?

Dedikált HSM rendelkezések SafeNet hálózati HSM 7 berendezések (modell A790). Itt található egy összefoglaló az egyes műveletek esetében a maximális teljesítményt: 

* RSA-2048: 10 000 tranzakció / másodperc
* ECC P256: 20 000 tranzakció / másodperc
* AES-GCM: 17,000 tranzakció / másodperc

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>K: Hány partíció hozható létre dedikált HSM-ben?

A használt HSM adott modellen alapuló, elérhető 10 partíciók vannak.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>K: A dedikált HSM támogatható, hogy hány kulcsot?

A kulcsok maximális számát. Ezek a számok esetében is kulcspárokat Ha aszimmetrikus kulcsokkal.

* RSA 2048 - 19,000
* ECC-P256 - 91,000
* AES-256 - 218,000

Kapacitás beállítása a kulcs létrehozása sablon és a partíciók száma az adott kulcsattribútum függően változhat.

