---
title: Azure database ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk egy Azure adatbázis biztonsági védelmének bevált gyakorlata biztosít.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: tomsh
ms.openlocfilehash: cceea9fa613d2a2428427bfe73eb50550db6c69a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281625"
---
# <a name="azure-database-security-best-practices"></a>Azure database ajánlott biztonsági eljárások
Biztonsági adatbázis elsődleges szempont, és azt mindig az, hogy prioritást [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Az adatbázisok szorosan leköthetőek súgó megfelel a legtöbb szabályozási és biztonsági követelmények, többek között a HIPAA, az ISO 27001/27002 és a PCI DSS Level 1. Biztonsági megfelelőségi tanúsítványok aktuális listáját érhető el: a [Microsoft Trust Center webhely](https://azure.microsoft.com/support/trust-center/services/). Is kiválaszthatja, hogy az adott Azure-adatközpontok a szabályozási követelményeknek megfelelően az adatbázisok helyezze.

Ebben a cikkben bemutatjuk, Azure database ajánlott biztonsági eljárások gyűjteménye. Ajánlott eljárások az Azure adatbázis biztonsági származnak az funkciót, és az ügyfelek a funkciókat, például saját magának.

A minden egyes ajánlott eljárás az hogy ismertetik:

-   Mi az az ajánlott eljárás szerint
-   Miért ajánlott eljárás, hogy engedélyezni szeretné
-   Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
-   Hogyan tudhat meg az ajánlott eljárás engedélyezése

Ez a cikk az Azure adatbázis biztonsági eljárások az Azure platform olyan képességeit és a egy konszenzus véleményével alapul, és a szolgáltatás állítja be, ez a cikk írásának időpontjában léteznek. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Adatbázis-hozzáférés korlátozása tűzfalszabályok használatával
A Microsoft Azure SQL Database relációsadatbázis-szolgáltatás Azure és egyéb internetalapú alkalmazások számára biztosít. Ahhoz, hogy a hozzáférés-biztonságot, az SQL Database szabályozza a hozzáférést az:

- Az tűzfalszabályt, amely korlátozza a kapcsolódási IP-cím alapján.
- Felhasználók kell igazolnia az identitását igénylő hitelesítési mechanizmusok.
- Engedélyezési mechanizmus, amely korlátozza a felhasználók számára bizonyos műveletek és adatok.

Tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgálóhoz való hozzáférés teljes, csak akkor adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

Az alábbi ábrán látható, hogy itt lehet megadni a kiszolgálói tűzfal SQL Database-ben:

![Tűzfalszabályok](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. SQL-adatbázis eléréséhez a számítógépről, győződjön meg arról, hogy az ügyfél tűzfal engedélyezi-e a kimenő TCP-kommunikációt az 1433-as TCP-porton. Tűzfalszabályok, az 1433-as TCP-porton bejövő kapcsolat blokkolására, ha már nincs szüksége ezeket a kapcsolatokat az egyéb alkalmazásokra vonatkozóan.

A kapcsolódási folyamat részeként a kapcsolatok az Azure-beli virtuális gépek rendszer átirányítja egy IP-cím és port, amelyet az egyes feldolgozói szerepkörök egyedi. A portszám a 11000-től 11999-ig terjedő tartományban található. TCP-portokkal kapcsolatos további információkért lásd: [az ADO.NET 4.5 szoftverrel 1433-Ason túli](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> A tűzfal IP-szabályokat, valamint kezeli a virtuális hálózati szabályok. A virtuális hálózati szabályok a virtuális hálózati Szolgáltatásvégpontok alapulnak. A virtuális hálózati szabályok bizonyos esetekben IP-szabályok használata előnyösebb lehet. További tudnivalókért lásd: [virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Adatbázis-hitelesítés engedélyezése
SQL Database két típusú hitelesítés, SQL Server-hitelesítés és Azure AD-hitelesítést támogatja.

### <a name="sql-server-authentication"></a>*SQL Server-hitelesítés*

Előnyei a következők:

- Lehetővé teszi SQL-adatbázis támogatja a környezetekben, ahol vegyes operációs rendszerek, ahol minden felhasználó nem hitelesített a Windows-tartomány szerint.
- Lehetővé teszi az SQL Database támogatja a régebbi alkalmazásokat és az SQL Server-hitelesítést igénylő partner által biztosított alkalmazások.
- Lehetővé teszi a felhasználók számára ismeretlen vagy nem megbízható tartományokból. Ilyen például az alkalmazás, ahol együttműködnek a meglévő ügyfelek hozzárendelt SQL Server bejelentkezési fogadni a rendelések állapotát.
- Lehetővé teszi, a felhasználók saját identitással létre SQL Database webes alkalmazások támogatásához.
- Lehetővé teszi a szoftverfejlesztők alkalmazásaik terjesztéséhez ismert, előre beállított SQL Server bejelentkezéseken alapuló összetett engedély hierarchia használatával.

> [!NOTE]
> SQL Server-hitelesítés nem használható a Kerberos biztonsági protokollt.

Ha az SQL Server-hitelesítést használ, a következőket kell tennie:

- Az erős hitelesítő adatok kezelése saját magának.
- A kapcsolati karakterláncban a hitelesítő adatok védelmét.
- A hitelesítő adatokat a hálózaton keresztül továbbított a webkiszolgáló az adatbázishoz (vélhetően) védelmét. További információkért lásd: [hogyan: Kapcsolódás az SQL Server használata az SQL-hitelesítés az ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Az Azure Active Directory (AD) hitelesítési*
Az Azure AD-hitelesítés egy Azure SQL Database-adatbázishoz kapcsolódáskor mechanizmust és [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identitásokat az Azure AD-ben. Az Azure AD-hitelesítés az identitások, az adatbázis-felhasználók és más Microsoft-szolgáltatások egyetlen központi helyen kezelheti. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűsíti az engedélyek kezelését.

> [!NOTE]
> Javasoljuk, hogy az Azure AD-hitelesítés az SQL Server-hitelesítés használatára.

Előnyei a következők:

- SQL Server-hitelesítés helyett biztosít.
- Ez segít, állítsa le a felhasználói identitások elterjedése adatbázis-kiszolgáló között.
- Ez lehetővé teszi, hogy a jelszóváltoztatás egyetlen helyen.
- Ügyfelek külső (az Azure AD-) csoportok az adatbázis-engedélyek kezelheti.
- Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével jelszavak tárolását, nyilvánvaló.
- Az adatbázis szintjén identitások hitelesítésére használ tartalmazott adatbázis felhasználóit.
- Hitelesítési jogkivonat-alapú alkalmazásokat, amelyek a csatlakozás az SQL Database támogatja.
- Active Directory összevonási szolgáltatások (tartományi összevonási) vagy a natív felhasználó/jelszó-hitelesítést támogatja a helyi Azure Active Directory példányhoz, tartományi szinkronizálás nélkül.
- Az Azure AD-példányokhoz SQL Server Management Studio által használt Active Directory univerzális hitelesítéssel, amely tartalmazza a multi-factor Authentication szolgáltatás. A multi-factor Authentication szolgáltatás-ellenőrzési lehetőségek széles erős hitelesítést nyújt – telefonhívás, szöveges üzenet, intelligens kártyák PIN-kód vagy mobilalkalmazásbeli értesítés. További információkért lásd: [SSMS-támogatás az Azure AD multi-factor Authentication az SQL Database és az SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

A konfigurálás lépéseinek végrehajtásához konfigurálhatja és használhatja az Azure AD-hitelesítést a következő eljárásokat tartalmazzák:

- Hozzon létre, és töltse fel az Azure ad-ben.
- Választható lehetőség: Hozzárendelése, vagy módosítsa a jelenleg az Azure-előfizetéséhez társított Active Directory-példányból.
- Hozzon létre egy Azure Active Directory-rendszergazda az Azure SQL Database vagy [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurálja az ügyfélszámítógépeken.
- Hozzon létre tartalmazottadatbázis-felhasználók az Azure AD-identitások leképezett adatbázis.
- Az Azure AD-identitások kapcsolódni az adatbázishoz.

A részletes információkat [használható Azure Active Directory-hitelesítést a hitelesítés az SQL Database felügyelt példányába vagy az SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Az adatok védelme titkosítás és a sorszintű biztonság használatával
[Az Azure SQL Database transzparens adattitkosításának](../sql-database/transparent-data-encryption-azure-sql.md) védi a lemezen lévő adatokat, és a védelmet, hardver való jogosulatlan hozzáféréssel szemben. Valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív azt végez anélkül, hogy a módosításokat. Transzparens adattitkosítás a tárolót a teljes adatbázisra az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja.

Akkor is, ha a teljes tárolási titkosított, fontos, maga az adatbázis is titkosíthatja. Ez az adatvédelmi jellegű defense megközelítés megvalósítását. Ha használ az Azure SQL Database és számára védelmet kíván biztosítani a bizalmas adatok (például hitelkártya vagy társadalombiztosítási szám), titkosíthatók adatbázisok a FIPS 140-2 hitelesített 256 bites AES-titkosítás. A titkosítás megfelel a követelményeknek (például a HIPAA és a PCI) számos iparági szabvány.

Kapcsolódó fájlok [(BPE) kiterjesztés puffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nem titkosítottak, amikor egy adatbázis transzparens adattitkosítás használatával titkosítja. Rendszer-fájlszintű titkosítás és hasonló eszközökkel kell használnia [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) vagy a [titkosított fájlrendszer (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) kapcsolatos BPE fájlok számára.

Egy jogosult felhasználó, például a biztonsági rendszergazda vagy egy adatbázis-rendszergazda férhet hozzá az adatokhoz, még akkor is, ha az adatbázis transzparens adattitkosítással titkosítva van, mert is hajtsa végre ezeket a javaslatokat:

- Az adatbázis szintjén az SQL Server-hitelesítés engedélyezéséhez.
- Az Azure AD-hitelesítés használatával [RBAC-szerepkörök](../role-based-access-control/overview.md).
- Győződjön meg arról, hogy felhasználók és alkalmazások használjon különálló fiókok hitelesítésére. Ezzel a módszerrel korlátozhatja a felhasználók és alkalmazások rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát.
- Adatbázis-biztonság (például db_datareader vagy db_datawriter) rögzített adatbázis-szerepkörök használatával valósítja meg. Vagy létrehozhat egyéni szerepköröket, hogy az alkalmazás a kiválasztott adatbázis-objektumok explicit engedélyeket.

Egyéb módjaival védi az adatait vegye figyelembe:

- A [cellaszintű titkosítás](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), amely lehetővé teszi az ügyfelek számára a bizalmas adatok ügyfélalkalmazásokon belüli titkosítását, és soha ne adja meg a titkosítási kulcsokat az adatbázismotorhoz (SQL Database vagy SQL Server). Ennek eredményeképpen azoknak, akik a saját adatok (és annak megtekintéséhez) elkülönülését Always Encrypted funkciója, és azok számára, akik kezelése az adatok (de nem rendelkezhetnek).
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security), amely lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz, a rendszer a lekérdezést végrehajtó felhasználó jellemzői alapján való hozzáférést. (A példában mutatókat csoport tagságát, és végrehajtási környezetben.)

Előfordulhat, hogy a szervezetek számára, amelyek nem használja az adatbázis-titkosítást fokozottabban ki a támadásokkal szemben, amelyek veszélyeztetik az SQL-adatbázisokban található adatokat.

További információ az SQL Database transzparens adattitkosítási című cikkben [az Azure SQL Database transzparens adattitkosítási](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Adatbázis naplózásának engedélyezése
Naplózás az SQL Server adatbázismotor és a egy önálló adatbázis egy példányát magában foglalja a nyomon követése és az események naplózását. Az SQL Server a kiszolgálói szintű események előírások és az adatbázis-szintű eseményeit specifikációk tartalmazó eseményeket is létrehozhat. A naplózott eseményeket az Eseménynapló bejegyzéseit, vagy fájlok naplózása írható.

Nincsenek több szintű naplózás az SQL Server, attól függően, kormányzati és a telepítési szabványok követelményeinek. Az SQL Server-naplózás biztosít eszközöket és folyamatokat engedélyezése, tárolásához és naplók megtekintése a különböző server és adatbázis-objektumok.

[Az Azure SQL Database naplózási](../sql-database/sql-database-auditing.md) nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban.

Naplózás segíthet a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint keresse meg az eltéréseket és rendellenességeket, amelyek mutasson előfordulhat, hogy az üzleti szempontból problematikus jelenségeket vagy biztonsági veszélyeket. Naplózás megkönnyíti a megfelelőségi előírásoknak való megfelelést, de nem garantálja a megfelelőség.

Adatbázis naplózási és engedélyezését a kapcsolatos további információkért lásd: [első lépései az SQL database naplózási szolgáltatásával](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Database fenyegetésészlelés engedélyezése
Veszélyforrások elleni védelem túllép észlelése. Adatbázis veszélyforrások elleni védelem tartalmazza:

- Felderítése és besorolása a legbizalmasabb adatokat, így az adatok védelméhez.
- Végrehajtási biztonságos konfigurációt az adatbázisban, ezért az adatbázis védheti.
- Észlelés és reagálás a potenciális fenyegetésekre azok felmerülésekor, így gyorsan reagálhat, és javítása.

**Ajánlott eljárás**: felderítésére, besorolására és címkézése a bizalmas adatokat az adatbázisban.   
**Részletes**: az SQL database-ben az adatok besorolását engedélyezésével [Adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md) Azure SQL Database-ben. Az Azure irányítópultján a bizalmas adatokhoz való hozzáférés figyelésére, vagy töltse le a jelentések.

**Ajánlott eljárás**: nyomon követheti az adatbázis biztonsági rések, így proaktív módon javíthatja az adatbázis biztonsági.   
**Részletes**: az Azure SQL Database használata [biztonságirés-értékelési](../sql-database/sql-vulnerability-assessment.md) szolgáltatás, amely az adatbázis biztonsági réseinek keres. A szolgáltatás, amelyet a biztonsági rések jelzőt és gyakorlattól eltérő jelenségeket, például konfigurációs hibák, a túlságosan széleskörű engedélyeket és a nem védett, bizalmas adatok megjelenítése Tudásbázis alkalmaz.

A szabályokat a Microsoft ajánlott eljárásai, így nyugodtan összpontosíthat a biztonsági problémákat, amelyek a legnagyobb kockázatot az adatbázis és az értékes adatok alapulnak. Fedhetik le az adatbázisszintű problémák és a kiszolgálói szintű biztonsági problémákat, például a kiszolgálói tűzfal beállításaiban, és a kiszolgálói szintű engedélyekkel. Ezek a szabályok jelentik azok megfelelőségi szabványainak való ellenőrző szervezetekkel, a követelmények többségének.

**Ajánlott eljárás**: fenyegetésészlelés engedélyezése.  
**Részletes**: engedélyezése az Azure SQL Database [Fenyegetésészlelés](../sql-database/sql-database-threat-detection.md) beolvasni a biztonsági riasztások és javaslatok vizsgálata és elhárítani a fenyegetéseket. Gyanús adatbázis-tevékenységekről, a potenciális biztonsági réseket, és a SQL-injektálásos támadásokról, valamint rendellenes adatbázis kapcsolatos értesítéseket kaphat hozzáférést és a lekérdezési minták.

[Komplex veszélyforrások elleni védelem](../sql-database/sql-advanced-threat-protection.md) egységes csomag egy tapasztalt SQL biztonsági funkciók. Tartalmazza a korábban említett szolgáltatások: az Adatfelderítés és a besorolás, a sebezhetőségi felmérés és a Fenyegetésészlelés. Egyetlen helyen engedélyezése és kezelése ezeket a képességeket biztosítja.

Engedélyezi ezeket a képességeket nyújt segítséget:

- Megfelel az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények teljesítését.
- Ki férhet hozzá az adatbázisokat, és azok biztonsági korlátozhatják.
- Egy dinamikus adatbázis környezetben, ahol visszakövetését, hogy-e módosítások figyelésére.
- Észleli, és reagáljon a lehetséges veszélyforrásokra.

Emellett a Fenyegetésészlelés integrálódik a riasztások az Azure Security Center egy helyen jeleníti meg az összes az Azure-erőforrások biztonsági állapotát.

## <a name="next-steps"></a>További lépések
Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.

Az alábbi forrásanyagokat biztosít az Azure biztonsági és a kapcsolódó Microsoft-szolgáltatások kapcsolatos általános információk érhetők el:
* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – az Azure Security legújabb naprakész információk
* [A Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – Ha a jelenteni lehet a Microsoft biztonsági réseket, beleértve a problémák az Azure-ral, vagy az e-mailen keresztül secure@microsoft.com
