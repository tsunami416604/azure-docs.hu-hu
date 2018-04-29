---
title: Gyakorlati tanácsok a biztonsági adatok és a titkosítás |} Microsoft Docs
description: Ez a cikk számos gyakorlati tanácsok az adatok biztonságát, és a beépített titkosítási használata Azure-képességek.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 574ca8a68bf6e532331a4b6f1106e472c8ab0449
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Az Azure Data biztonsági és a titkosítás gyakorlati tanácsok

A kulcsokat a felhőben az adatvédelem egyik elszámolása van a lehetséges állapotok az adatok akkor fordulhat elő, és milyen vezérlők érhetők el az adott állapotban. Céljából az Azure data biztonsági és a titkosítás gyakorlati tanácsok a javaslatok lesz körül állapotok a következő adatokat:

* Nyugalmi: Ez magában foglalja a tárolási objektum, a tárolók és a fizikai adathordozó statikusan létező típusok kell azt mágneses vagy optikai lemez összes információt.
* Az átvitel közbeni: Ha adatátvitel közötti összetevők, a helyek vagy a programok, többek között a hálózaton keresztül egy service bus (a felhőbe helyszíni és fordítva, beleértve a hibrid kapcsolatok, például az ExpressRoute), vagy egy bemeneti/kimeneti folyamat során, akkor-re, hogy a mozgásérzékelési.

Ez a cikk az Azure data biztonsági és a titkosítás az ajánlott eljárások gyűjteménye ismertetik. Az alábbi gyakorlati tanácsok az Azure data biztonsági, titkosítási és az ügyfelek, például a saját kezűleg feladatait tapasztalatunk származik.

Az egyes ajánlott eljárás azt ismertetjük:

* Mi az az ajánlott eljárás szerint
* Miért érdemes, hogy a legjobb engedélyezése
* Milyen lehet az eredmény, ha nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan megismerheti az ajánlott eljárás engedélyezése

A Azure Adatbiztonság és -titkosítás gyakorlati tanácsok cikk az együttműködési véleményét, és az Azure platform olyan képességeit és a szolgáltatáskészletek, alapján ez a cikk írásának időpontjában léteznek. Vélemények és technológiák változnak az idők, és ez a cikk a változások követése érdekében rendszeresen frissül.

Az Azure data biztonsági és titkosítási gyakorlati tanácsokat cikkben említett a következők:

* Többtényezős hitelesítés kikényszerítéséhez
* Használjon szerepköralapú hozzáférés-vezérlést (RBAC)
* Az Azure virtuális gépek titkosítása
* Hardveres biztonsági modellek használata
* Biztonságos munkaállomások kezelése
* SQL-titkosítás engedélyezéséhez
* Adatok védelmére átvitel
* Fájl szintű adatok titkosításának kényszerítése

## <a name="enforce-multi-factor-authentication"></a>Többtényezős hitelesítés kikényszerítéséhez

Az első lépés az adatok elérése a Microsoft Azure-ban vezérlő pedig úgy, hogy hitelesíteni a felhasználót. [Az Azure multi-factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md) csupán felhasználónévvel és jelszóval mint egy másik módszer használatával felhasználói identitás ellenőrzése módot. A hitelesítési módszer segítségével hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett.

A felhasználók számára az Azure MFA engedélyezésével ad hozzá egy második biztonsági réteggel felhasználói bejelentkezéseket és tranzakciókat. Ebben az esetben egy tranzakció lehetséges, hogy használja a dokumentum egy fájlkiszolgálón, vagy a SharePoint Online-ban található. Az Azure MFA is segít csökkenteni szeretné annak valószínűségét, egy sérült biztonságú hitelesítő adat rendelkezik-e a szervezet adataihoz való hozzáférés informatikai.

Példa: Ha az Azure többtényezős hitelesítés kényszerítéséhez a felhasználók számára, és konfigurálja úgy, hogy használják telefonhívást vagy SMS-üzenet ellenőrzést, ha a felhasználó hitelesítő adatainak biztonsága sérül, a támadó nem fogja tudni bármilyen olyan erőforrás elérésére, mivel azt nem kell a hozzáférést a felhasználó telefonjára. A szervezeteknek, amelyek nem adja hozzá a további védelmi réteg biztosítása identitás jobban ki vannak téve a hitelesítő adatok jelszóellopásos támadáshoz, ami azt eredményezheti, hogy az adatok biztonsági sérülése.

Olyan szervezeteknek, amelyek a hitelesítési vezérlő helyszíni megtartja egy alternatív, hogy használja [Azure multi-factor Authentication kiszolgáló](../active-directory/authentication/howto-mfaserver-deploy.md), más néven az MFA a helyszínen. Ez a módszer használatával is tudja kényszeríteni a többtényezős hitelesítést a multi-factor Authentication kiszolgáló helyszíni megtartásával.

További információ az Azure MFA, olvassa el a cikk [Ismerkedés az Azure multi-factor Authentication a felhőben](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Használjon szerepköralapú hozzáférés-vezérlést (RBAC)

Alapuló hozzáférés korlátozása a [tudniuk kell, hogy](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági szint](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági alapelveket. Ez elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy adott hatókörben. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást.

Kihasználhatja [beépített RBAC-szerepkörök](../role-based-access-control/built-in-roles.md) felhasználók jogosultságok hozzárendelése az Azure-ban. Érdemes lehet *tárolási fiók közreműködői* a felhő üzemeltetői, amely szükséges a storage-fiókok kezelése és *klasszikus tárolási fiók közreműködői* szerepkör kezelése a klasszikus tárfiókokat. A felhő üzemeltetői, amelyet a virtuális gépek és a tárfiók kezelése, fontolja meg, hogy *virtuális gép közreműködő* szerepkör.

A szervezeteknek, amelyek kényszeríti ki a hozzáférés-vezérlés képességeinek például RBAC által előfordulhat, hogy kell jogosultságot ad mint azok a felhasználók számára szükséges további engedélyekkel. Ez azzal, hogy bizonyos adatokat, az elsőként nem rendelkeznek hozzáféréssel rendelkező felhasználók adatok biztonsági sérülés vezethet.

További tudnivalók az Azure RBAC által a cikk elolvasása [átruházásához hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

## <a name="encrypt-azure-virtual-machines"></a>Az Azure virtuális gépek titkosítása

A legtöbb szervezet számára [adatok titkosítását](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés adatvédelmi, a megfelelőség és az adatok közös joghatóság alá felé. Az Azure Disk Encryption lehetővé teszi, hogy a rendszergazdák számára a Windows és Linux rendszerű infrastruktúra-szolgáltatási virtuális gép (VM) lemezek titkosításához. Az Azure Disk Encryption használja. Ez az iparági szabványos BitLocker a Windows és a DM-crypt program segítségével a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít.

Kihasználhatja az Azure Disk Encryption és megvédeni az adatok a szervezeti biztonsági és megfelelőségi követelmények teljesítéséhez segítségével. A szervezetek is figyelembe kell venni, titkosítással segítségével mérsékelhetik a kockázatokat jogosulatlan kapcsolatos adatok elérése. Ajánlott továbbá meghajtók számukra bizalmas adatok írása előtt a titkosítást.

Ügyeljen arra, hogy a virtuális gép az adatkötetek és a rendszerindító kötet titkosítása az Azure storage-fiók az inaktív adatok védelme érdekében. A titkosítási kulcsok és titkos kulcsok védelme, ami [Azure Key Vault](../key-vault/key-vault-whatis.md).

A helyszíni Windows kiszolgálók esetén fontolja meg a következő titkosítási gyakorlati tanácsok:

* Használjon [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) az adatok titkosításához
* Az AD DS-ben helyreállítási információk tárolására.
* Ha bármilyen probléma, hogy a BitLocker-kulcsok biztonsága sérült, javasoljuk, hogy a meghajtó a BitLocker metaadatok összes példányt eltávolítja, a meghajtóról vagy formázása vagy visszafejteni, és a teljes meghajtót újra titkosítani.

A szervezeteknek, amelyek nem a következő adatokat a titkosítás kényszerítéséhez nagyobb eséllyel tartják be adatok épségével kapcsolatos problémák, például az adatok ellopása rosszindulatú vagy rosszindulatú felhasználók számára elérhetővé tehető, és törölje a jelet formátumú adatokat jogosulatlan hozzáférést szerez fiókok biztonsága sérült. Mellett a kockázatok vállalatok számára, hogy iparági szabályozások ahhoz, hogy igazolja, hogy azok szokott és a megfelelő adatok biztonság érdekében a megfelelő biztonsági vezérlőket használ.

További tudnivalók az Azure Disk Encryption által a cikk elolvasása [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Hardveres biztonsági modulok használata
Iparági titkosítási megoldások titkos kulcsok használatával titkosítsa az adatokat. Ezért fontos, hogy ezeket a kulcsokat biztonságosan kell tárolni. Kulcskezelés adatok védelmével, szerves részét válik, mivel az adatok titkosítására használt titkos kulcsok tárolására fog javítható.

Használja az Azure lemeztitkosítás [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével szabályozhatja és lemez titkosítási kulcsok és titkos kulcsainak kulcstároló-előfizetése kezeléséhez, győződjön meg arról, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek-e az Azure-ban aktívan során tárolás. Az Azure Key Vault naplózása kulcsok és a házirend-használati kell használnia.

Nincsenek sok rejlő kockázatokat nem rendelkezik megfelelő biztonsági vezérlőket, hogy az adatok titkosítására használt titkos kulcsok védelme. Ha támadók hozzáfér a titkos kulcsokhoz, akkor nem tudja visszafejteni az adatokat és potenciálisan bizalmas információk elérését.

További kapcsolatos általános javaslatok az Azure tanúsítványkezelés által a cikk elolvasása [Azure tanúsítványkezelés: javaslatok és dolog](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Az Azure Key Vault kapcsolatos további információkért olvassa el a [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Biztonságos munkaállomások kezelése
A támadások többsége a végfelhasználó cél, mert a végpont kihasználásának egyik elsődleges támadások válik. Ha egy támadó a végpont gyengíti, ő használhatják fel a szervezeti adatok eléréséhez a felhasználó hitelesítő adatait. A legtöbb végpont támadások a tényt, hogy a végfelhasználók a saját helyi munkaállomások rendszergazdák kihasználni.

A biztonságos felügyeleti munkaállomás használatával a kockázatok csökkentése érdekében. Azt javasoljuk, hogy használja a [Privileged Access munkaállomások (PAW)](https://technet.microsoft.com/library/mt634654.aspx) csökkenteni a támadási felületet, a munkaállomások. A biztonságos felügyeleti munkaállomások segítségével ezek közül néhány támadások biztosítsa az adatok biztonságosabb. Ügyeljen arra, hogy PAW segítségével megerősítése és a munkaállomás zárolását. Ez a magas biztonsági garanciák kényes fiókok, feladatok és az adatvédelem biztosításához fontos lépés.

Az endpoint protection hiánya lehet, hogy az adatok veszélynek, győződjön meg arról, hogy biztonsági szabályzatokat érvényesítsen az összes eszközön, amely adatokat, függetlenül az adatok helye (felhőalapú vagy helyszíni) segítségével.

Megismerheti a kiemelt bővebben a cikk elolvasása eléréséhez munkaállomás [emelt szintű hozzáférés biztonságossá tétele](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>SQL-titkosítás engedélyezéséhez
[Az Azure SQL Database átlátható adattitkosítás](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) segítségével valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, a kártékony tevékenység fenyegetés elleni és a tranzakciós naplófájlok aktívan nélkül hogy az alkalmazást módosítani kellene.  TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja.

Akkor is, ha a teljes tárterület titkosított, nagyon fontos is titkosítani az adatbázist, saját magát. Ez az a mélység megközelítés a data protection a védelmet megvalósítása. Ha használ [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) és szeretne védeni a bizalmas adatok, például a hitelkártya vagy társadalombiztosítási számot, titkosíthatja adatbázisok a FIPS 140-2 hitelesített 256 bites AES titkosítást számos követelményeknek az ipari szabványok (például a HIPAA, a PCI).

Fontos tudni, hogy fájlok kapcsolódó [kiterjesztés puffer](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) Ha egy adatbázis titkosítása a TDE nincs titkosítva. A fájlszintű titkosítás Rendszereszközök fájlt például a BitLocker kell használnia, vagy a [titkosított fájlrendszer](https://technet.microsoft.com/library/cc700811.aspx) (EFS) BPE a kapcsolódó fájlokat.

Hitelesített felhasználó óta, mint például a biztonsági rendszergazda vagy egy adatbázis-rendszergazda férhet hozzá az adatokhoz akkor is, ha a TDE, az adatbázis titkosított is kövesse az alábbi javaslatokat:

* Az adatbázis szintjén SQL-hitelesítés
* Az Azure AD-alapú hitelesítés használata az RBAC-szerepkörök
* Felhasználók és az alkalmazások által használandó külön fiókok hitelesítéséhez. Így korlátozhatja a felhasználók és az alkalmazások számára megadott engedélyeket és a kártékony tevékenység a kockázatok csökkentése
* Alkalmazzon adatbázis szintű biztonsági rögzített adatbázis-szerepkörök (például a db_datareader vagy db_datawriter), vagy létrehozhat egyéni szerepkörök az alkalmazáshoz kiválasztott adatbázis-objektumok explicit engedélyt

Lehet, hogy adatbázis a blokkszintű titkosítás nem használó szervezetek jobban ki vannak téve a támadásokkal szemben, amelyek esetlegesen veszélyeztető SQL-adatbázisban található adatokhoz.

További Erőforráscsoportoknál titkosításával kapcsolatos által a cikk elolvasása [átlátható adattitkosítást az Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Adatok védelmére átvitel

Az átvitel során az adatok védelme a data protection stratégia nagyon fontos részét kell lennie. Oda-vissza adatokat fog áthelyezése több helyről, mert az általános ajánlás az, hogy mindig SSL/TLS protokollokat használ az exchange-adatok különböző helyek között. Bizonyos esetekben érdemes lehet különítheti el a teljes kommunikációs csatornát a helyszíni és a felhő közötti virtuális magánhálózati (VPN) segítségével infrastruktúra.

Az adatok áthelyezése a helyszíni infrastruktúra és az Azure között megfelelő védelmi funkciók, például a HTTPS- vagy VPN-érdemes lehet.

Használja a szervezet számára több található munkaállomások helyszíni Azure hozzáférést igénylő [Azure telephelyek közötti VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md).

A szervezet számára, hogy a biztonságos hozzáférés a helyszíni található egy munkaállomás Azure kell használni [pont-pont VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Nagyobb, mint egy dedikált nagy sebességű WAN-kapcsolaton keresztül anélkül áthelyezhetők [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Ha ExpressRoute használatát választja, a alkalmazás szintű az adatok is titkosíthatók [SSL/TLS](https://support.microsoft.com/kb/257591) vagy egyéb protokollok hozzáadott védelemre.

Az Azure Storage az Azure-portálon való interakció, minden tranzakció történik meg HTTPS használatával. [Storage REST API felülete](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS is használható, amellyel kommunikálni tud [Azure Storage](https://azure.microsoft.com/services/storage/) és [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Adatok védelmére átvitel nem válaszoló szervezetek jobban ki vannak téve a rendszer [-átjárójának](https://technet.microsoft.com/library/gg195821.aspx), [lehallgatás](https://technet.microsoft.com/library/gg195641.aspx) és munkamenet-eltérítés. Ilyen támadások lehet az első lépés a bizalmas adatok elérésekor.

További Azure VPN lehetőségekről a cikk olvasásával [tervezése és kialakítása VPN-átjáró](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Fájl szintű adatok titkosításának kényszerítése

Amely növelheti az adatok biztonsági szint védelmi réteget titkosítja a fájlok, függetlenül a fájl helyét.

[Az Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) titkosítási, identitáskezelési és engedélyezési házirendeket használ a fájlok és e-mailek biztonságossá. Több eszközön működik az Azure RMS-telefonokon, táblagépeken és számítógépeken megvédi a szervezeten belül, mind a szervezeten kívülről. Ez a lehetőség azért lehetséges, mert az Azure RMS biztosítja, ami az adatok maradnak, még akkor is, ha elhagyják a szervezet területét.

Ha Azure RMS használatával a fájlok védelme, teljes körű támogatása szabványos kriptográfia használ [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). A data protection is használja az Azure RMS, ha akkor is, ha nem az ellenőrzése alatt álló tárolók másolódik lehet, hogy a fájl védelme mindig fennmarad benne informatikai, például egy felhőalapú tárolási szolgáltatásba. Az egyetlen különbség a keresztül e-mailben megosztott fájlok a fájl védett e-mailt, utasításokkal mellékletként nyitni a védett mellékletet.

Azure RMS bevezetését tervezésekor a következőket javasoljuk:

* Telepítse a [RMS-megosztó alkalmazás](https://technet.microsoft.com/library/dn339006.aspx). Ez az alkalmazás integrálja az Office-alkalmazások telepítésekor egy Office-bővítmény, hogy a felhasználók egyszerűen védhetik a fájlok közvetlenül.
* Az Azure RMS-t támogató alkalmazások és szolgáltatások konfigurálása
* Hozzon létre [egyéni sablonok](https://technet.microsoft.com/library/dn642472.aspx) , amely az üzleti igényeknek megfelelően. Például: felső titkos adatok, az összes felső titkos alkalmazni kívánt sablont kapcsolódó e-maileket.

A szervezetek, amelyek a gyenge [adatbesorolást](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és lehet, hogy a fájl védelem jobban ki vannak téve az adatok kiszivárgásának. Megfelelő fájl védelem nélkül a szervezet nem fogja tudni üzleti elemzések készítése beszerzése, figyelje a visszaélés, és hogy rosszindulatú férhessenek hozzá a fájlok.

További tudnivalók az Azure RMS által a cikk elolvasása [Ismerkedés az Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
