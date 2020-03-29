---
title: Gyakori kérdések - Azure dedikált HSM | Microsoft dokumentumok
description: Gyakori kérdések az Azure dedikált HSM-jének különböző témáival kapcsolatban
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: a0cb7957008308425d91abb3e0f828cc40301736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064929"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Válaszok a Microsoft Azure dedikált HSM-mel kapcsolatos gyakori kérdésekre.

## <a name="the-basics"></a>Az alapok

### <a name="q-what-is-a-hardware-security-module-hsm"></a>K: Mi az a hardveres biztonsági modul (HSM)?

A hardveres biztonsági modul (HSM) egy fizikai számítástechnikai eszköz, amely a titkosítási kulcsok védelmére és kezelésére szolgál. A HSM-ekben tárolt kulcsok kriptográfiai műveletekhez használhatók. A kulcsanyag biztonságosan marad a szabotázsálló, illetéktelen beavatkozást érdemlő hardvermodulokban. A HSM csak a hitelesített és engedélyezett alkalmazások számára engedélyezi a kulcsok használatát. A kulcsanyag soha nem hagyja el a HSM védelmi határt.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>K: Mi az Azure dedikált HSM-ajánlat?

Az Azure dedikált HSM egy felhőalapú szolgáltatás, amely az Azure-adatközpontokban üzemeltetett HSM-eket biztosítja, amelyek közvetlenül kapcsolódnak az ügyfél virtuális hálózatához. Ezek a HSM-ek dedikált hálózati készülékek (Gemalto SafeNet Network HSM 7 Model A790). Ezek közvetlenül az ügyfelek privát IP-címterébe vannak telepítve, és a Microsoft nem fér hozzá a HSM-ek titkosítási funkcióihoz. Csak az ügyfél rendelkezik teljes felügyeleti és kriptográfiai ellenőrzéssel ezek felett az eszközök felett. Az ügyfelek felelősek az eszköz kezeléséért, és teljes tevékenységnaplókat kaphatnak közvetlenül az eszközeikről. A dedikált HSM-ek segítenek az ügyfeleknek megfelelni a megfelelőségi/szabályozási követelményeknek, például a FIPS 140-2 Level 3, HIPAA, PCI-DSS és eIDAS és még sokan mások.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>K: Milyen hardvert használnak a dedikált HSM-hez?

A Microsoft a Gemalto vállalattal együttműködve biztosítja az Azure dedikált HSM-szolgáltatását. Az adott eszköz a [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Ez az eszköz nem csak fips 140-2 Level 3 érvényesített firmware-t biztosít, hanem alacsony késleltetést, nagy teljesítményt és nagy kapacitású 10 partíciót is kínál. 

### <a name="q-what-is-an-hsm-used-for"></a>K: Mire használják a HSM-et?

A HSM-ek olyan titkosítási funkciókhoz használt kriptográfiai kulcsok tárolására szolgálnak, mint az SSL (biztonságos szoftvercsatorna-réteg), az adatok titkosítása, a PKI (nyilvános kulcsú infrastruktúra), a DRM (digitális jogkezelés) és a dokumentumok aláírása.

### <a name="q-how-does-dedicated-hsm-work"></a>K: Hogyan működik a dedikált HSM?

Az ügyfelek a PowerShell vagy a parancssori felület használatával kiépíthetnek HSM-eket adott régiókban. Az ügyfél határozza meg, hogy a HSM-ek milyen virtuális hálózathoz lesznek csatlakoztatva, és miután kiépítették, a HSM-ek elérhetők lesznek a kijelölt alhálózaton az ügyfél privát IP-címterében hozzárendelt IP-címeken. Ezután az ügyfelek csatlakozhatnak a HSM-ekhez az SSH használatával a berendezés kezeléséhez és felügyeletéhez, hsm-ügyfélkapcsolatokat állíthatnak be, inicializálhatják a HSM-eket, partíciókat hozhatnak létre, definiálhatnak és rendelhetnek szerepköröket, például partíciófelelőst, kriptográfiai tisztviselőt és kriptográfiai felhasználót. Ezután az ügyfél a Gemalto által biztosított HSM ügyféleszközöket/SDK/szoftvert fogja használni az alkalmazásaikból származó kriptográfiai műveletek végrehajtásához.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>K: Milyen szoftver van ellátva a dedikált HSM szolgáltatással?

A Gemalto a Microsoft által kiépített összes szoftvert a HSM-eszközhöz szállítja. A szoftver elérhető a [Gemalto ügyfélszolgálati portálján.](https://supportportal.gemalto.com/csm/) A dedikált HSM szolgáltatást használó ügyfeleknek regisztrálniuk kell a Gemalto-támogatásra, és olyan ügyfélazonosítóval kell rendelkezniük, amely lehetővé teszi a megfelelő szoftverek elérését és letöltését. A támogatott ügyfélszoftver a 7.2-es verzió, amely kompatibilis a FIPS 140-2 Level 3 validált firmware 7.0.3-as verziójával. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>K: Az Azure dedikált HSM jelszó- és PED-alapú hitelesítést kínál?

Ebben az időben az Azure dedikált HSM csak a HSM jelszóalapú hitelesítést biztosít.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>K: Az Azure dedikált HSM-ben lesz a HSM-em?

A Microsoft csak a Gemalto SafeNet Luna Network HSM-et kínálja a dedikált HSM szolgáltatáson keresztül, és nem tud az ügyfél által biztosított eszközöket üzemeltetni.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>K: Támogatja az Azure dedikált HSM-szolgáltatása a PIN-kód (PIN/EFT) funkciókat?

Az Azure dedikált HSM szolgáltatás SafeNet Luna Network HSM 7 (A790-es modell) eszközöket használ. Ezek az eszközök nem támogatják a fizetési HSM-specifikus funkciókat (például PIN-kódot vagy EFT-t) vagy tanúsítványokat. Ha azt szeretné, hogy az Azure dedikált HSM-szolgáltatás a jövőben támogassa a fizetési HSM-eket, adja át a visszajelzést a Microsoft-ügyfélképviselőjének.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>K: Mely Azure-régiókban érhető el a dedikált HSM?

2019. március végén a dedikált HSM az alább felsorolt 14 régióban érhető el. További régiókat tervezünk, amelyeket a Microsoft-ügyfélképviseleten keresztül tárgyalhatunk.

* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA déli középső régiója
* Délkelet-Ázsia
* Kelet-Ázsia
* Közép-India
* Dél-India
* Kelet-Japán
* Nyugat-Japán
* Észak-Európa
* Nyugat-Európa
* Az Egyesült Királyság déli régiója
* Az Egyesült Királyság nyugati régiója
* Közép-Kanada
* Kelet-Kanada
* Kelet-Ausztrália
* Délkelet-Ausztrália

## <a name="interoperability"></a>Együttműködési lehetőség

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>K: Hogyan kapcsolódik az alkalmazásom egy dedikált HSM-hez?

A Gemalto hsm-ügyféleszközöket/SDK/szoftvert használ az alkalmazások ból származó kriptográfiai műveletek végrehajtásához. A szoftver elérhető a [Gemalto ügyfélszolgálati portálján.](https://supportportal.gemalto.com/csm/) A dedikált HSM szolgáltatást használó ügyfeleknek regisztrálniuk kell a Gemalto-támogatásra, és olyan ügyfélazonosítóval kell rendelkezniük, amely lehetővé teszi a megfelelő szoftverek elérését és letöltését.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>K: Csatlakozhat egy alkalmazás dedikált HSM-hez egy másik virtuális hálózatról a régiókban vagy régiók között?

Igen, a virtuális hálózatok közötti kapcsolat létrehozásához egy régión belüli [virtuális hálózat társviszony-létesítése](../virtual-network/virtual-network-peering-overview.md) szükséges. A régiók közötti kapcsolathoz [VPN-átjárót kell használnia.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>K: Szinkronizálhatom a dedikált HSM-et a helyszíni HSM-ekkel?

Igen, szinkronizálhatja a helyszíni HSM-eket a dedikált HSM-mel. [A pont-pont VPN vagy a pont-hely](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolat segítségével kapcsolatot létesíthet a helyszíni hálózattal.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>K: Titkosíthatom a más Azure-szolgáltatások által használt adatokat a dedikált HSM-ben tárolt kulcsok használatával?

Nem. Az Azure dedikált HSM-ek csak a virtuális hálózaton belül érhetők el.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>K: Importálhatok kulcsokat egy meglévő helyszíni HSM-ről dedikált HSM-be?

Igen, ha a helyszíni Gemalto SafeNet HSMs. Több módszer is létezik. Tekintse meg a Gemalto HSM dokumentációját.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>K: Milyen operációs rendszereket támogat a dedikált HSM ügyfélszoftver?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuális: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>K: Hogyan konfigurálhatom az ügyfélalkalmazást úgy, hogy több HSM-ből származó több partícióval rendelkező, magas rendelkezésre állású konfigurációt hozzon létre?

A magas rendelkezésre állás érdekében be kell állítania a HSM-ügyfélalkalmazás-konfigurációt az egyes HSM-ek partícióinak használatához. Tekintse meg a Gemalto HSM kliens szoftver dokumentációját.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>K: Milyen hitelesítési mechanizmusokat támogat a dedikált HSM?

Az Azure dedikált HSM SafeNet Network HSM 7 készülékeket (A790-es modell) használ, és támogatják a jelszóalapú hitelesítést.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>K: Milyen SDK-k, API-k, ügyfélszoftverek érhetők el a dedikált HSM-mel?

PKCS#11, Java (JCA/JCE), Microsoft CAPI és CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>K: Importálhatok/áttelepíthetek kulcsokat a Luna 5/6 HSM-ekből az Azure dedikált HSM-ekbe?

Igen. Tekintse meg a Gemalto áttelepítési útmutatót. 

## <a name="using-your-hsm"></a>A HSM használata

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>K: Hogyan dönthetem el, hogy az Azure Key Vault vagy az Azure dedikált HSM?

Az Azure dedikált HSM a megfelelő választás a hsm-eket használó helyszíni Azure-alkalmazásokba áttelepített vállalatok számára. A dedikált HSM-ek egy alkalmazás minimális módosításokkal történő áttelepítésének lehetőségét ismertetik. Ha kriptográfiai műveleteket hajtanak végre az alkalmazás kódját fut egy Azure virtuális gép vagy webalkalmazás, használhatják dedikált HSM. Általánosságban elmondható, hogy az IaaS (infrastruktúra mint szolgáltatás) modellekben futó zsugorfóliába csomagolt a HSM-eket kulcstárolóként támogató hsm-ek használhatnak hitelesítési HSM-et, például alkalmazásátjárót vagy forgalomkezelőt kulcs nélküli SSL, ADCS (Active Directory tanúsítványszolgáltatások) vagy hasonló PKI-eszközökhöz, dokumentumaláíráshoz, kódaláíráshoz vagy TDE-vel (transzparens adatbázis-titkosítás) konfigurált SQL Server (IaaS) eszközökhöz, amelyek főkulcsos kulcsa egy EKM (bővíthető kulcskezelő) szolgáltató használatával. Az Azure Key Vault alkalmas "felhőben született" alkalmazásokhoz vagy inaktív titkosításhoz olyan forgatókönyvek esetén, ahol az ügyféladatokat a PaaS (platform szolgáltatásként) vagy a SaaS (szoftver, mint szolgáltatás) forgatókönyvek, például az Office 365 ügyfélkulcs, az Azure Information Protection dolgozza fel , Az Azure Disk Encryption, az Azure Data Lake Store titkosítása ügyfél által kezelt kulccsal, Az Azure Storage titkosítása az ügyfél által kezelt kulccsal, és az Azure SQL az ügyfél által kezelt kulccsal.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>K: Milyen használati forgatókönyvek felelnek meg leginkább az Azure dedikált HSM-nek?

Az Azure dedikált HSM a legalkalmasabb a migrálási forgatókönyvek. Ez azt jelenti, hogy ha a helyszíni alkalmazások áttelepítése az Azure-ba, amelyek már hsms.This means that if you are migrating on-premises applications to Azure that are already using HSMs. Ez egy kis súrlódású lehetőséget biztosít az Azure-ba való áttelepítéshez az alkalmazás minimális módosításával. Ha kriptográfiai műveleteket hajtanak végre az alkalmazás kódja fut az Azure VM vagy webapp, dedikált HSM használható. Általánosságban elmondható, hogy az IaaS (infrastruktúra szolgáltatásként) modellekben futó, zsugorfóliába csomagolt szoftverek, amelyek kulcstárolóként támogatják a HSM-eket, használhatják a HSM-et, például:

* Alkalmazásátjáró vagy forgalomkezelő kulcs nélküli SSL-hez
* ADCS (Active Directory tanúsítványszolgáltatások)
* Hasonló PKI-eszközök
* Dokumentumaláíráshoz használt eszközök/alkalmazások
* Kód aláírása
* TDE -vel (transzparens adatbázis-titkosítás) konfigurált SQL Server (IaaS) főkulccsal hsm-ben, EKM (bővíthető kulcskezelés) szolgáltató használatával

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>K: Használható dedikált HSM az Office 365 ügyfélkulccsal, az Azure Information Protection, az Azure Data Lake Store, a Lemeztitkosítás, az Azure Storage titkosításával, az Azure SQL TDE-vel?

Nem. A dedikált HSM közvetlenül az ügyfél privát IP-címterébe van kiépítve, így az nem érhető el más Azure- vagy Microsoft-szolgáltatásokáltal.

## <a name="administration-access-and-control"></a>Adminisztráció, hozzáférés és ellenőrzés

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>K: Az ügyfél teljes körű kizárólagos irányítást kap a HSM-ek felett dedikált HSM-ekkel?

Igen. Minden HSM-készülék teljes mértékben egyetlen ügyfélnek van szentelve, és senki más nem rendelkezik felügyeleti felügyeletkel a kiépítés és a rendszergazdai jelszó módosítása után.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>K: Milyen szintű hozzáféréssel rendelkezik a Microsoft a HSM-hez?

A Microsoft nem rendelkezik felügyeleti vagy kriptográfiai ellenőrzéssel a HSM felett. A Microsoft rendelkezik a figyelési szintű hozzáféréssoros portkapcsolaton keresztül az alapvető telemetriai adatok, például a hőmérséklet és az összetevők állapotának lekéréséhez. Ez lehetővé teszi a Microsoft számára, hogy proaktív értesítést küldsen az egészségügyi problémákról. Szükség esetén az ügyfél letilthatja ezt a fiókot.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>K: Mi az a "tenantadmin" fiók, amelyet a Microsoft használ, és hozzászoktam ahhoz, hogy a rendszergazda felhasználó "rendszergazda" legyen a SafeNet HSM-eken?

A HSM-eszköz a szokásos alapértelmezett jelszóval rendelkező alapértelmezett rendszergazdai felhasználóval együtt szállít. A Microsoft nem kívánta, hogy az alapértelmezett jelszavak használata, míg bármely eszköz a készletben várja, hogy kikell építeni az ügyfelek. Ez nem felelne meg a szigorú biztonsági követelményeknek. Ezért erős jelszót állítunk be, amely et a kiépítés időpontjában elveti. A kiépítési időpontban hozzunk létre egy új felhasználót a "tenantadmin" nevű rendszergazdai szerepkörben. Ez a felhasználó rendelkezik az alapértelmezett jelszóval, és az ügyfelek ezt módosítják az első műveletként, amikor először bejelentkezik az újonnan kiépített eszközbe. Ez a folyamat biztosítja a magas fokú biztonságot, és fenntartja ígéretünket, hogy ügyfeleink számára egyetlen adminisztratív ellenőrzést biztosít. Meg kell jegyezni, hogy a "tenantadmin" felhasználó segítségével alaphelyzetbe állíthatja a rendszergazdai felhasználói jelszót, ha az ügyfél szeretné használni a fiókot. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>K: A Microsoft vagy bárki a Microsoft hozzáférési kulcsain a dedikált HSM-ben?

Nem. A Microsoft nem rendelkezik hozzáféréssel az ügyfél által lefoglalt dedikált HSM-ben tárolt kulcsokhoz.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>K: Frissíthetem a szoftvert/firmware-t a nekem kiosztott HSM-eken?

A legjobb támogatás érdekében a Microsoft nyomatékosan javasolja, hogy ne frissítse nőbbszoftvert/firmware-t a HSM-en. Az ügyfél azonban teljes körű felügyeleti felügyelettel rendelkezik, beleértve a szoftver/belső vezérlőprogram frissítését is, ha a különböző belső vezérlőprogram-verziókhoz speciális funkciókra van szükség. A módosítások végrehajtása előtt a következményeket úgy kell értelmezni, hogy ez például hatással lehet a FIPS által ellenőrzött állapotra. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>K: Hogyan kezelhetem a dedikált HSM-et?

A dedikált HSM-eket az SSH használatával érheti el.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>K: Hogyan kezelhetem a partíciókat a dedikált HSM?Q:

A Gemalto HSM kliens szoftver a HSM-ek és a partíciók kezelésére szolgál.

### <a name="q-how-do-i-monitor-my-hsm"></a>K: Hogyan figyelhetem a HSM-et?

Az ügyfél teljes hozzáféréssel rendelkezik a HSM tevékenységnaplókhoz a syslog és az SNMP segítségével. Az ügyfélnek be kell állítania egy syslog-kiszolgálót vagy SNMP-kiszolgálót, hogy megkapja a naplókat vagy eseményeket a HSM-ektől.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>K: Teljes hozzáférési naplót kaphatok az összes HSM-műveletről a dedikált HSM-től?

Igen. Naplókat küldhet a HSM készülékről egy syslog szerverre

## <a name="high-availability"></a>Magas rendelkezésre állás

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>K: Lehet-e konfigurálni a magas rendelkezésre állás ugyanabban a régióban vagy több régióban?

Igen. A gemalto által biztosított HSM-ügyfélszoftver magas rendelkezésre állású konfigurációt és telepítést hajt végre. Az azonos virtuális hálózatból vagy más virtuális hálózatokból származó hsm-ek ugyanabból a régióból vagy régiók között, vagy a helyszíni vagy pont-pont VPN használatával a virtuális hálózathoz csatlakoztatott hálózati hsm-ek ugyanazokat a magas rendelkezésre állású konfigurációt adhatják hozzá. Meg kell jegyezni, hogy ez csak a kulcsanyagokat szinkronizálja, és nem bizonyos konfigurációs elemeket, például szerepköröket.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>K: Hozzáadhatok HSM-eket a helyszíni hálózatomról egy magas rendelkezésre állású csoporthoz az Azure dedikált HSM-mel?

Igen. Meg kell felelniük a SafeNet Luna Network HSM 7 magas rendelkezésre állási követelményeinek.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>K: Hozzáadhatok Luna 5/6 HSM-eket a helyszíni hálózatokból egy magas rendelkezésre állású csoporthoz az Azure dedikált HSM-mel?

Nem.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>K: Hány HSM-et adhatok hozzá egyetlen alkalmazásból ugyanabból a magas rendelkezésre állású konfigurációhoz?

Egy HA csoport 16 tagja elmaradt, teljes gázzal végzett tesztelés kiváló eredménnyel.

## <a name="support"></a>Támogatás

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>K: Mi a dedikált HSM szolgáltatás SLA-ja?

A dedikált HSM szolgáltatásra nem külön üzemidő-garancia vonatkozik. A Microsoft biztosítja a hálózati szintű hozzáférést az eszközhöz, és így a szabványos Azure hálózati SLA-k érvényesek.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>K: Hogyan használják a HSM-ek az Azure dedikált HSM-ben?

Az Azure-adatközpontok kiterjedt fizikai és eljárási biztonsági vezérlőkkel rendelkeznek. Amellett, hogy a dedikált HSM-ek az adatközpont egy további korlátozott hozzáférési területén találhatók. Ezeken a területeken további fizikai hozzáférés-vezérlés és videokamera-felügyelet a nagyobb biztonság érdekében.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>K: Mi történik, ha biztonsági rés vagy hardveres illetéktelen beavatkozási esemény történik?

A dedikált HSM szolgáltatás SafeNet Network HSM 7 készülékeket használ. Ezek a készülékek támogatják a fizikai és logikai szabotázsészlelést. Ha bármikor történik szabotázsesemény, a HSM-ek automatikusan nulláizálódnak.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>K: Hogyan biztosítható, hogy a dedikált HSM-ekben lévő kulcsok ne vesszenek el hiba vagy rosszindulatú belső támadás miatt?

Erősen ajánlott egy helyszíni HSM biztonsági mentési eszköz használata a HSM-ek rendszeres rendszeres biztonsági mentéséhez a vész-helyreállításhoz. Társközi vagy helyek közötti VPN-kapcsolatot kell használnia egy HSM biztonsági mentési eszközhöz csatlakoztatott helyszíni munkaállomáshoz.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>K: Hogyan kaphatok támogatást a dedikált HSM-hez?

A támogatást a Microsoft és a Gemalto is biztosítja.  Ha problémája van a hardver- vagy hálózati hozzáféréssel, kérjen támogatási kérelmet a Microsoftnál, és ha a HSM konfigurációjával, szoftverével és alkalmazásfejlesztésével kapcsolatos problémája van, a Gemalto támogatási kérelmet nyújt be. Ha meghatározatlan problémája van, nyújtson be támogatási kérelmet a Microsoftnak, majd a Gemalto igény szerint részt vehet. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>K: Hogyan szerezhetem be az ügyfélszoftvert, dokumentációt és hozzáférést a SafeNet Luna 7 HSM integrációs útmutatásához?

A szolgáltatásra való regisztrációt követően egy Gemalto ügyfélazonosítót biztosítunk, amely lehetővé teszi a gemaltoi ügyfélszolgálati portálon való regisztrációt. Ez lehetővé teszi a hozzáférést az összes szoftverhez és dokumentációhoz, valamint lehetővé teszi a támogatási kérelmeket közvetlenül a Gemalto-val.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>K: Ha biztonsági rést talál, és a Gemalto kiszabadít egy javítást, aki az operációs rendszer/firmware frissítéséért/javításáért felelős?

A Microsoft nem tud csatlakozni az ügyfelek számára kiosztott HSM-ekhez. Az ügyfeleknek frissíteniük kell és javítaniuk kell a HSM-eket.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>K: Mi a teendő, ha újra kell indítanom a HSM-et?

A HSM egy parancssori újraindítás opcióval rendelkezik, azonban időnként újraindítjuk az újraindítási problémákat, és ezért ajánlott a legbiztonságosabb újraindításhoz, hogy támogatási kérelmet nyújtson be a Microsofthoz, hogy az eszközt fizikailag újraindítsa. 

## <a name="cryptography-and-standards"></a>Kriptográfia és szabványok

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>K: Biztonságos a legfontosabb adatok titkosítási kulcsainak tárolása a dedikált HSM-ben?

Igen, dedikált HSM rendelkezések SafeNet Network HSM 7 készülékek használó FIPS 140-2 Level 3 érvényesített HSMs. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>K: Milyen titkosítási kulcsokat és algoritmusokat támogat a dedikált HSM?

Dedikált HSM szolgáltatás rendelkezések SafeNet Network HSM 7 készülékek. A kriptográfiai kulcstípusok és algoritmusok széles skáláját támogatják, többek között: Full Suite B támogatás

* Aszimmetrikus:
  * RSA
  * Dsa
  * Diffie-Hellman között
  * Elliptikus görbe
  * Kriptográfia (ECDSA, ECDH, Ed25519, ECIES) elnevezett, felhasználó által definiált és Brainpool görbékkel, KCDSA
* Szimmetrikus:
  * AES-GCM
  * Hármas DES
  * DES
  * ARIA, MAG
  * RC2
  * RC4
  * RC5
  * CAST
  * Kivonat/Üzenet Kivonat/HMAC: SHA-1, SHA-2, SM3
  * Kulcslevezetés: SP800-108 számláló mód
  * Kulcscsomagolás: SP800-38F
  * Véletlenszám-generálás: FIPS 140-2 jóváhagyott DRBG (SP 800-90 CTR mód), amely megfelel a BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>K: A dedikált HSM FIPS 140-2 Level 3 érvényesítve van?

Igen. A dedikált HSM szolgáltatás a SafeNet Network HSM 7 készülékeket támogatja, amelyek FIPS 140-2 Level 3 validált HSM-eket használnak.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>K: Mit kell tennem annak érdekében, hogy a dedikált HSM-et FIPS 140-2 Level 3 validált módban működtessem?

A dedikált HSM szolgáltatás safenet luna network HSM 7 készülékeket tartalmaz. Ezek a készülékek FIPS 140-2 Level 3 validált HSM-eket használnak. Az alapértelmezett telepített konfiguráció, operációs rendszer és belső vezérlőprogram is FIPS-ellenőrzés. A FIPS 140-2 Level 3 megfelelősége érdekében nem kell semmilyen műveletet végrehajtania.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>K: Hogyan biztosítja az ügyfél, hogy ha egy HSM-et megszüntet, az összes kulcsfontosságú anyag törlődik?

A megszüntetés kérése előtt az ügyfélnek nulláznia kell a HSM-et a Gemalto által biztosított HSM-ügyféleszközök használatával.

## <a name="performance-and-scale"></a>Teljesítmény és méret

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>K: Hány kriptográfiai művelet támogatott másodpercenként dedikált HSM-mel?

Dedikált HSM rendelkezések SafeNet Network HSM 7 készülékek (Modell A790). Az alábbiakban összefoglaljuk egyes műveletek maximális teljesítményét: 

* RSA-2048: 10 000 tranzakció másodpercenként
* ECC P256: 20 000 tranzakció másodpercenként
* AES-GCM: 17 000 tranzakció másodpercenként

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>K: Hány partíció hozható létre dedikált HSM?Q: How many partitions can be created in Dedicated HSM?

A SafeNet Luna HSM 7 a használt A790 modell 10 partíció licencét tartalmazza a szolgáltatás költségében. Az eszköz legfeljebb 100 partícióval rendelkezik, és ha ehhez a korlátig hozzáadja a partíciókat, az további licencelési költségekkel járna, és új licencfájl telepítését igényelné az eszközön.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>K: Hány kulcs támogatható dedikált HSM?

A billentyűk maximális száma a rendelkezésre álló memória függvénye. A SafeNet Luna 7 modell A790 használatban van 32MB memória. A következő számok aszimmetrikus kulcsok használata esetén a kulcspárokra is vonatkoznak.

* RSA-2048 - 19 000
* ECC-P256 - 91 000

A kapacitás a kulcslétrehozási sablonban beállított kulcsattribútumoktól és a partíciók számától függ.
