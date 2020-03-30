---
title: Az adatbázis biztonságával kapcsolatos gyakorlati tanácsok – Microsoft Azure
description: Ez a cikk az Azure-adatbázisok biztonságával kapcsolatos gyakorlati tanácsok készletét tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125106"
---
# <a name="azure-database-security-best-practices"></a>Azure-beli adatbázis-biztonság – ajánlott eljárások
Ez a cikk az adatbázis-biztonsággal kapcsolatos gyakorlati tanácsokat ismerteti.

Az ajánlott eljárások a véleménykonszenzuson alapulnak, és a jelenlegi Azure platform-képességekkel és szolgáltatáskészletekkel működnek. A vélemények és a technológiák idővel változnak, és ezt a cikket rendszeresen frissítik, hogy tükrözze ezeket a változásokat.

## <a name="secure-databases"></a>Biztonságos adatbázisok
A biztonság az adatbázisok kezelésének egyik legfontosabb szempontja, és mindig is prioritást élvezett az [Azure SQL Database számára.](../../sql-database/index.yml) Az adatbázisok szorosan biztosíthatók a legtöbb szabályozási vagy biztonsági követelmény teljesítése érdekében, beleértve a HIPAA-t, az ISO 27001/27002-t és a PCI DSS 1-es szintet. A biztonsági megfelelőségi tanúsítványok aktuális listája a [Microsoft Adatvédelmi központ webhelyén](https://azure.microsoft.com/support/trust-center/services/)érhető el. Azt is választhatja, hogy az adatbázisokat adott Azure-adatközpontokban helyezi el a szabályozási követelmények alapján.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Az adatbázis-hozzáférés korlátozása tűzfalszabályok használatával
A Microsoft Azure SQL Database relációs adatbázis-szolgáltatást biztosít az Azure-hoz és más internetalapú alkalmazásokhoz. A hozzáférés biztonságának biztosítása érdekében az SQL Database a következő kkel szabályozza a hozzáférést:

- Tűzfalszabályok, amelyek ip-cím szerint korlátozzák a kapcsolatot.
- Hitelesítési mechanizmusok, amelyek megkövetelik a felhasználóktól személyazonosságuk igazolását.
- Engedélyezési mechanizmusok, amelyek a felhasználókat meghatározott műveletekre és adatokra korlátozzák.

A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

Az alábbi ábra azt mutatja, hogy hol állított be kiszolgálótűzfalat az SQL Database-ben:

![Tűzfalszabályok](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Az Azure SQL Database szolgáltatás csak a 1433-as TCP-porton keresztül érhető el. Sql-adatbázis számítógépről való eléréséhez győződjön meg arról, hogy az ügyfélszámítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as TCP-porton. Ha nincs szüksége ezekre a kapcsolatokra más alkalmazásokhoz, tiltsa le a bejövő kapcsolatokat az 1433-as TCP-porton.

A csatlakozási folyamat részeként az Azure virtuális gépekről érkező kapcsolatok átirányításra kerülnek egy IP-címre és portra, amely minden egyes feldolgozói szerepkörhöz egyedi. A portszám a 11000-től 11999-ig terjedő tartományban található. A TCP-portokról a [4.5-ös ADO.NET 1433 utáni portjai](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)című témakörben talál további információt.

További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Az IP-szabályok mellett a tűzfal kezeli a virtuális hálózati szabályokat. A virtuális hálózati szabályok a virtuális hálózati szolgáltatás végpontjain alapulnak. A virtuális hálózati szabályok bizonyos esetekben előnyösebbek lehetnek az IP-szabályoknál. További információ: [Virtuális hálózati szolgáltatás végpontok és szabályok az Azure SQL Database.](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)

## <a name="enable-database-authentication"></a>Adatbázis-hitelesítés engedélyezése
Az SQL Database kétféle hitelesítést támogat, az SQL Server-hitelesítést és az Azure AD-hitelesítést.

### <a name="sql-server-authentication"></a>*SQL Server hitelesítés*

Ez az alábbi előnyökkel jár:

- Lehetővé teszi, hogy az SQL Database vegyes operációs rendszerekkel rendelkező környezeteket támogasson, ahol a Windows-tartomány nem hitelesíti az összes felhasználót.
- Lehetővé teszi, hogy az SQL Database támogassa az SQL Server hitelesítést igénylő régebbi és partneráltal biztosított alkalmazásokat.
- Lehetővé teszi a felhasználók számára, hogy ismeretlen vagy nem megbízható tartományokból csatlakozzanak. Erre példa egy olyan alkalmazás, ahol a létrehozott ügyfelek a hozzárendelt SQL Server bejelentkezésekhez csatlakoznak, hogy megkapják a rendeléseik állapotát.
- Lehetővé teszi, hogy az SQL Database támogassa azokat a webalapú alkalmazásokat, amelyekben a felhasználók saját identitásokat hoznak létre.
- Lehetővé teszi a szoftverfejlesztők számára, hogy az ismert, előre beállított SQL Server bejelentkezéseken alapuló összetett engedélyhierarchia használatával terjesszék alkalmazásaikat.

> [!NOTE]
> Az SQL Server hitelesítése nem tudja használni a Kerberos biztonsági protokollt.

Ha SQL Server-hitelesítést használ, a következőket kell tennie:

- Kezelje az erős hitelesítő adatokat saját maga.
- Védje a hitelesítő adatokat a kapcsolati karakterláncban.
- (Potenciálisan) védi a hálózaton keresztül a webkiszolgálóról az adatbázisba továbbított hitelesítő adatokat. További információt a [Hogyan csatlakozhat az SQL Server kiszolgálóhoz SQL-hitelesítéssel ASP.NET 2.0-s](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD) hitelesítés*
Az Azure AD-hitelesítés az Azure SQL Database és az [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) az Azure AD identitások használatával való kapcsolódásának mechanizmusa. Az Azure AD-hitelesítéssel egyetlen központi helyen kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait. A központi azonosítófelügyelettel egyetlen helyen kezelheti az adatbázis-felhasználókat, így leegyszerűsítheti az engedélykezelést.

> [!NOTE]
> Azt javasoljuk, hogy az Azure AD-hitelesítés sql server hitelesítés használata felett.

Ez az alábbi előnyökkel jár:

- Az SQL Server-hitelesítés alternatívája.
- Segít megállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálók között.
- Ez lehetővé teszi a jelszó elforgatását egyetlen helyen.
- Az ügyfelek kezelhetik az adatbázis-engedélyeket külső (Azure AD) csoportok használatával.
- Megszüntetheti a jelszavak tárolását az integrált Windows-hitelesítés és az Azure Active Directory által támogatott egyéb hitelesítési formák engedélyezésével.
- A tartalmazott adatbázis-felhasználók segítségével hitelesíti az identitásokat az adatbázis szintjén.
- Támogatja az SQL-adatbázishoz csatlakozó alkalmazások tokenalapú hitelesítését.
- Támogatja az AD FS (tartományösszevonás) vagy a natív felhasználói/jelszó-hitelesítés egy helyi Azure Active Directory-példány tartomány szinkronizálása nélkül.
- Az Azure AD támogatja az SQL Server Management Studio olyan kapcsolatait, amelyek az Active Directory univerzális hitelesítést használják, amely többtényezős hitelesítést is tartalmaz. A többtényezős hitelesítés erős hitelesítést biztosít számos ellenőrzési lehetőséggel – telefonhívással, szöveges üzenettel, PIN-kóddal rendelkező intelligens kártyákkal vagy mobilalkalmazás-értesítéssel. További információ: [SSMS support for Azure AD Multi-Factor Authentication with SQL Database and SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

A konfigurációs lépések a következő eljárásokat tartalmazzák az Azure AD-hitelesítés konfigurálásához és használatához:

- Hozzon létre és feltöltse az Azure AD-t.
- Nem kötelező: Társítsa vagy módosítsa az Azure-előfizetéséhez jelenleg társított Active Directory-példányt.
- Hozzon létre egy Azure Active Directory-rendszergazdát az Azure SQL Database vagy az [Azure SQL Data Warehouse számára.](https://azure.microsoft.com/services/sql-data-warehouse/)
- Konfigurálja az ügyfélszámítógépeket.
- Hozzon létre egyben foglalt adatbázis-felhasználókat az adatbázisban az Azure AD-identitások hoz létre.
- Csatlakozzon az adatbázishoz az Azure AD-identitások használatával.

Részletes információkat az [Azure Active Directory-hitelesítés használata sql adatbázissal, felügyelt példányral vagy SQL Data Warehouse-ral történő hitelesítéshez](../../sql-database/sql-database-aad-authentication.md)talál.

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Az adatok védelme titkosítással és sorszintű biztonsággal
[Az Azure SQL Database transzparens adattitkosítás](../../sql-database/transparent-data-encryption-azure-sql.md) segít megvédeni a lemezen tárolt adatokat, és védelmet nyújt a hardverhez való jogosulatlan hozzáférés sel szemben. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Az átlátszó adattitkosítás titkosítja a teljes adatbázis tárolását egy szimmetrikus kulcs, az adatbázis titkosítási kulcsa használatával.

Még akkor is, ha a teljes tároló titkosítva van, fontos, hogy magát az adatbázist is titkosítsa. Ez az adatvédelemre vonatkozó mélyreható védelmi megközelítés végrehajtása. Ha Az Azure SQL Database használatával, és szeretné védeni a bizalmas adatokat (például a hitelkártya- vagy társadalombiztosítási számok), akkor titkosíthatja adatbázisok FIPS 140-2 érvényesített 256 bites AES titkosítást. Ez a titkosítás számos iparági szabvány (például HIPAA és PCI) követelményeinek felel meg.

A [pufferkészlet kiterjesztéséhez (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) kapcsolódó fájlok nem titkosítva vannak, ha az adatbázist transzparens adattitkosítással titkosítja. A BPE-vel kapcsolatos fájlokhoz fájlrendszerszintű titkosítási eszközöket kell használnia, például a [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) vagy az [EFS titkosítási rendszert (EFS).](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10))

Mivel egy jogosult felhasználó, például egy biztonsági rendszergazda vagy egy adatbázis-rendszergazda hozzáférhet az adatokhoz, még akkor is, ha az adatbázis átlátszó adattitkosítással van titkosítva, az alábbi javaslatokat is követnie kell:

- Engedélyezze az SQL Server hitelesítést az adatbázis szintjén.
- Az Azure AD-hitelesítés használata [RBAC-szerepkörök](/azure/role-based-access-control/overview)használatával.
- Győződjön meg arról, hogy a felhasználók és az alkalmazások külön fiókokat használnak a hitelesítéshez. Így korlátozhatja a felhasználók és alkalmazások számára biztosított engedélyeket, és csökkentheti a rosszindulatú tevékenységek kockázatát.
- Adatbázisszintű biztonság megvalósítása rögzített adatbázis-szerepkörök (például db_datareader vagy db_datawriter) használatával. Egyéni szerepköröket is létrehozhat az alkalmazáshoz, hogy explicit engedélyeket adjon a kijelölt adatbázis-objektumoknak.

Az adatok védelmének egyéb módjaiért vegye figyelembe a következőket:

- A [cellaszintű titkosítás](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
- [Mindig titkosított](/sql/relational-databases/security/encryption/always-encrypted-database-engine), amely lehetővé teszi az ügyfelek számára, hogy titkosítsák a bizalmas adatokat az ügyfélalkalmazásokon belül, és soha ne fedjék fel a titkosítási kulcsokat az adatbázis-kezelő motor (SQL Database vagy SQL Server) számára. Ennek eredményeképpen a Mindig titkosított a dedikált adatok (és megtekintheti) és az adatokat kezelők (de nem férhetnek hozzá) közötti elkülönítést biztosít.
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security), amely lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó jellemzői alapján szabályozzák az adatbázistábla soraihoz való hozzáférést. (A példa jellemzői a csoporttagság és a végrehajtási környezet.)

Az adatbázisszintű titkosítást nem használó szervezetek érzékenyebbek lehetnek az SQL-adatbázisokban található adatokat veszélyeztető támadásokra.

Az SQL Database transzparens adattitkosításáról az Átlátszó adattitkosítás az Azure SQL Database használatával című cikkben olvashat [bővebben.](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)

## <a name="enable-database-auditing"></a>Adatbázis-naplózás engedélyezése
Az SQL Server Adatbázismotor vagy az egyes adatbázisok egy példányának naplózása események nyomon követését és naplózását vonja maga után. Az SQL Server esetében olyan naplózásokat hozhat létre, amelyek kiszolgálószintű eseményekre és adatbázisszintű események specifikációira vonatkozó specifikációkat tartalmaznak. A naplózott események az eseménynaplókba vagy a naplófájlokba írhatók.

Az SQL Server naplózásának több szintje van, a telepítés kormányzati vagy szabványkövetelményeitől függően. Az SQL Server naplózása eszközöket és folyamatokat biztosít a különböző kiszolgáló- és adatbázis-objektumok naplózásának engedélyezéséhez, tárolásához és megtekintéséhez.

[Az Azure SQL Database naplózása](/azure/sql-database/sql-database-auditing) nyomon követi az adatbázis-eseményeket, és az Azure storage-fiókjában naplóba írja őket.

A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenységek megértésében, valamint olyan eltérések és anomáliák megtalálásában, amelyek üzleti problémákra vagy biztonsági szabálysértésre utalhatnak. A naplózás megkönnyíti a megfelelőségi szabványok betartását, de nem garantálja a megfelelőséget.

Az adatbázis-naplózásról és engedélyezéséről az [SQL-adatbázis-naplózás – Első lépések című témakörben olvashat bővebben.](/azure/sql-database/sql-database-auditing)

## <a name="enable-database-threat-detection"></a>Adatbázis-fenyegetések észlelésének engedélyezése
A veszélyforrások elleni védelem túlmutat az észlelésen. Az adatbázis veszélyforrások elleni védelme a következőket tartalmazza:

- A legérzékenyebb adatok felderítése és osztályozása, így megvédheti adatait.
- Biztonságos konfigurációk megvalósítása az adatbázisban, így megvédheti az adatbázist.
- Észleli és reagál a potenciális fenyegetések, mivel azok bekövetkeznek, így gyorsan reagálni és kiújítani.

**Ajánlott eljárás:** Az adatbázisokban lévő bizalmas adatok felderítése, besorolása és címkézése.   
**Részlet:** Az ADATOK felderítése és [besorolása](/azure/sql-database/sql-database-data-discovery-and-classification) az Azure SQL Database-ben az adatok osztályozásával az SQL-adatbázisban. Figyelheti a bizalmas adatokhoz való hozzáférést az Azure irányítópultján, vagy jelentéseket tölthet le.

**Ajánlott eljárás:** Kövesse nyomon az adatbázis biztonsági réseit, így proaktív módon javíthatja az adatbázis biztonságát.   
**Részlet:** Használja az Azure SQL Database [biztonsági résértékelési](/azure/sql-database/sql-vulnerability-assessment) szolgáltatását, amely az adatbázis esetleges biztonsági réseit vizsgálja. A szolgáltatás olyan szabályok tudásbázisát alkalmazza, amelyek megjelölik a biztonsági réseket, és eltéréseket mutatnak az ajánlott eljárásoktól, például a helytelen konfigurációktól, a túlzott engedélyektől és a nem védett bizalmas adatoktól.

A szabályok a Microsoft bevált módszerein alapulnak, és azokra a biztonsági problémákra összpontosítanak, amelyek a legnagyobb kockázatot jelentik az adatbázisra és annak értékes adataira. Ezek mind az adatbázis-szintű problémákat, mind a kiszolgálószintű biztonsági problémákat, például a kiszolgáló tűzfalbeállításait és a kiszolgálószintű engedélyeket is lefedik. Ezek a szabályok a szabályozó szervek számos követelményét is képviselik a megfelelőségi előírások nak való megfelelés érdekében.

**Ajánlott eljárás:** Fenyegetésészlelés engedélyezése.  
**Részletes:** Engedélyezze az Azure SQL Database [fenyegetésészlelésengedélyezését,](/azure/sql-database/sql-database-threat-detection) hogy biztonsági riasztásokat és javaslatokat kapjon a fenyegetések kivizsgálására és csökkentésére vonatkozóan. Riasztást kap a gyanús adatbázis-tevékenységekről, a potenciális biztonsági résekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról.

[A komplex veszélyforrások elleni védelem](/azure/sql-database/sql-advanced-threat-protection) egy egységes csomag a fejlett SQL biztonsági képességekhez. Ez magában foglalja a korábban említett szolgáltatásokat: Adatfelderítés és -besorolás, Sebezhetőség felmérése és fenyegetésészlelés. Egyetlen helyet biztosít a képességek engedélyezéséhez és kezeléséhez.

A képességek engedélyezése a következőkben segít:

- Feleljen meg az adatvédelmi szabványoknak és a jogszabályi megfelelőségi követelményeknek.
- Szabályozhatja az adatbázisokhoz való hozzáférést, és megkeményítheti azok biztonságát.
- Dinamikus adatbázis-környezet figyelése, ahol a változások nehezen követhetőek.
- A potenciális fenyegetések észlelése és az azokra való reagálás.

Emellett a fenyegetésészlelés integrálja a riasztásokat az Azure Security Centerrel az Összes Azure-erőforrás biztonsági állapotának központi megtekintéséhez.

## <a name="next-steps"></a>További lépések
Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.

Az alábbi források az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általánosabb információk biztosításához érhetők el:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb adatairól
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – ahol a Microsoft biztonsági rései, beleértve az Azure-ral kapcsolatos problémákat is, jelenthetők, vagy e-mailben asecure@microsoft.com
