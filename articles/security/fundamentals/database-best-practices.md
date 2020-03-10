---
title: Ajánlott adatbázis-biztonsági eljárások – Microsoft Azure
description: Ez a cikk az Azure Database biztonságával kapcsolatos ajánlott eljárásokat ismerteti.
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
ms.openlocfilehash: 316c3ef3c5bd16b52291029924d04fc159375bc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943662"
---
# <a name="azure-database-security-best-practices"></a>Azure-beli adatbázis-biztonság – ajánlott eljárások
Ez a cikk az adatbázis-biztonsággal kapcsolatos ajánlott eljárásokat ismerteti.

Az ajánlott eljárások a vélemények konszenzusán alapulnak, és a jelenlegi Azure platform-képességekkel és-szolgáltatásokkal működnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

## <a name="secure-databases"></a>Biztonságos adatbázisok
A biztonság az adatbázisok kezelésének egyik legfőbb szempontja, és mindig a [Azure SQL Database](../../sql-database/index.yml)prioritása. Az adatbázisok szorosan biztonságossá teszik a legtöbb szabályozási vagy biztonsági követelményt, például a HIPAA, az ISO 27001/27002-et és az 1. PCI DSS szintet. A biztonsági megfelelőségi tanúsítványok aktuális listája a [Microsoft Trust Center webhelyen](https://azure.microsoft.com/support/trust-center/services/)érhető el. Azt is megteheti, hogy az adatbázisokat meghatározott Azure-adatközpontokban helyezi el szabályozási követelmények alapján.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Tűzfalszabályok használata az adatbázis-hozzáférés korlátozásához
A Microsoft Azure SQL Database az Azure-hoz és más internetalapú alkalmazásokhoz is biztosít egy, a kapcsolatot biztosító adatbázis-szolgáltatást. A hozzáférés biztonsága érdekében SQL Database szabályozza a hozzáférést a következőhöz:

- Tűzfalszabályok, amelyek IP-cím alapján korlátozzák a kapcsolatot.
- Hitelesítési mechanizmusok, amelyeknek a felhasználóknak igazolniuk kell identitásukat.
- Engedélyezési mechanizmusok, amelyek korlátozzák a felhasználókat az adott műveletekre és adatszolgáltatásokra.

A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

A következő ábra azt mutatja be, hol állítható be a kiszolgálói tűzfal a SQL Databaseban:

![Tűzfalszabályok](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

A Azure SQL Database szolgáltatás csak a 1433-es TCP-porton keresztül érhető el. Ha egy SQL-adatbázist szeretne elérni a számítógépről, győződjön meg arról, hogy az ügyfélszámítógép tűzfala engedélyezi a kimenő TCP-kommunikációt a 1433-es TCP-porton. A 1433-as TCP-port bejövő kapcsolatainak letiltása a tűzfalszabályok használatával, ha nincs szüksége ezekre a kapcsolatokra más alkalmazásokhoz.

A kapcsolódási folyamat részeként az Azure Virtual Machines szolgáltatással létesített kapcsolatokat a rendszer átirányítja egy olyan IP-címhez és porthoz, amely minden feldolgozói szerepkör esetében egyedi. A portszám a 11000-től 11999-ig terjedő tartományban található. A TCP-portokkal kapcsolatos további információkért lásd: [1433-nál nagyobb portok a ADO.NET 4,5-hez](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Az IP-szabályok mellett a tűzfal a virtuális hálózati szabályokat is kezeli. A virtuális hálózati szabályok a virtuális hálózati szolgáltatás végpontján alapulnak. Bizonyos esetekben a virtuális hálózati szabályok előnyben részesítettek az IP-szabályoknál. További információ: [Virtual Network szolgáltatás-végpontok és a Azure SQL Database vonatkozó szabályok](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Adatbázis-hitelesítés engedélyezése
SQL Database két hitelesítési típust támogat, SQL Server a hitelesítést és az Azure AD-hitelesítést.

### <a name="sql-server-authentication"></a>*SQL Server hitelesítés*

Ez az alábbi előnyökkel jár:

- Lehetővé teszi a SQL Database számára a vegyes operációs rendszerekkel rendelkező környezetek támogatását, ahol az összes felhasználót nem a Windows-tartomány hitelesíti.
- Lehetővé teszi a SQL Database számára a régebbi alkalmazások és a partner által biztosított alkalmazások támogatását, amelyek SQL Server hitelesítést igényelnek.
- Lehetővé teszi a felhasználóknak az ismeretlen vagy nem megbízható tartományokból való kapcsolódást. Példa egy alkalmazásra, amelyben a meghozott ügyfelek a rendelésük állapotának fogadásához hozzárendelt SQL Server bejelentkezésekkel csatlakoznak.
- Lehetővé teszi a SQL Database számára a webalapú alkalmazások támogatását, ahol a felhasználók saját identitásokat hoznak létre.
- Lehetővé teszi, hogy a szoftverfejlesztők az ismert, előre beállított SQL Server bejelentkezéseken alapuló összetett engedélyezési hierarchia használatával terjesszek alkalmazásaikat.

> [!NOTE]
> SQL Server hitelesítés nem tudja használni a Kerberos biztonsági protokollt.

Ha SQL Server hitelesítést használ, a következőket kell tennie:

- Saját kezűleg kezelheti az erős hitelesítő adatokat.
- A hitelesítő adatok megvédése a kapcsolatok karakterláncában.
- (Potenciálisan) a webkiszolgálóról az adatbázisba átadott hitelesítő adatok védetté tehetők. További információkért lásd [: Hogyan lehet csatlakozni a SQL Serverhoz az SQL-hitelesítés használatával a ASP.NET 2,0-ben](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD) hitelesítés*
Az Azure AD-hitelesítés olyan mechanizmus, amellyel az Azure AD-ben identitások használatával kapcsolódhat Azure SQL Databasehoz és [SQL Data Warehousehoz](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) . Az Azure AD-hitelesítés használatával egyetlen központi helyen kezelheti az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásait. A központi AZONOSÍTÓk kezelése egyetlen helyet biztosít az adatbázis-felhasználók felügyeletéhez, és egyszerűsíti az engedélyek kezelését.

> [!NOTE]
> Az Azure AD-hitelesítés használatát javasoljuk SQL Server hitelesítéshez.

Ez az alábbi előnyökkel jár:

- Alternatívát biztosít a SQL Server hitelesítéshez.
- Segít leállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálókon.
- Lehetővé teszi a jelszó-elforgatást egyetlen helyen.
- Az ügyfelek külső (Azure AD-) csoportok használatával kezelhetik az adatbázis-engedélyeket.
- Az integrált Windows-hitelesítés és a Azure Active Directory által támogatott hitelesítés más formáinak engedélyezésével megtörölheti a jelszavak tárolását.
- A tárolt adatbázis-felhasználók használatával hitelesíti az identitásokat az adatbázis szintjén.
- Támogatja a jogkivonat-alapú hitelesítést a SQL Databasehoz csatlakozó alkalmazások esetében.
- Támogatja a AD FS (tartományi összevonást) vagy a natív felhasználói/jelszó-hitelesítést egy helyi Azure Active Directory példányon, tartomány-szinkronizálás nélkül.
- Az Azure AD olyan SQL Server Management Studio kapcsolatokat támogat, amelyek Active Directory univerzális hitelesítést használnak, beleértve a Multi-Factor Authenticationt. Multi-Factor Authentication erős hitelesítést biztosít számos ellenőrzési lehetőséggel – telefonhívást, szöveges üzenetet, intelligens kártyákat PIN-kóddal vagy Mobile apps-értesítéssel. További információ: [SSMS-támogatás az Azure AD multi-Factor Authenticationhoz SQL Database és SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

A konfigurációs lépések az Azure AD-hitelesítés konfigurálásához és használatához a következő eljárásokkal rendelkeznek:

- Hozza létre és töltse fel az Azure AD-t.
- Nem kötelező: társítsa vagy módosítsa az Azure-előfizetéshez jelenleg társított Active Directory-példányt.
- Hozzon létre egy Azure Active Directory rendszergazdát Azure SQL Database vagy [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurálja az ügyfélszámítógépeket.
- Hozzon létre tárolt adatbázis-felhasználókat az adatbázisban az Azure AD-identitásokhoz rendelve.
- Kapcsolódjon az adatbázishoz az Azure AD-identitások használatával.

A [SQL Database, felügyelt példány vagy SQL Data Warehouse használatával történő hitelesítéshez a használat Azure Active Directory hitelesítéssel](../../sql-database/sql-database-aad-authentication.md)kapcsolatos részletes információkat talál.

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>A titkosítás és a sor szintű biztonság használatával gondoskodhat adatai védelméről
[Azure SQL Database transzparens adattitkosítás](../../sql-database/transparent-data-encryption-azure-sql.md) segíti a lemezek adatainak védelmét, és védelmet biztosít a hardverhez való jogosulatlan hozzáférés ellen. Az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre, anélkül, hogy az alkalmazás módosítására lenne szükség. Az transzparens adattitkosítás titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával.

Még ha a teljes tárterület is titkosítva van, fontos, hogy magát az adatbázist is titkosítsa. Ez az adatvédelem részletes adatvédelmi megközelítésének megvalósítása. Ha Azure SQL Databaset használ, és szeretné megóvni a bizalmas adatokat (például a hitelkártyát vagy a társadalombiztosítási számokat), az adatbázisokat az FIPS 140-2 hitelesített 256-bites AES-titkosítással titkosíthatja. Ez a titkosítás megfelel a számos iparági szabvány követelményeinek (például HIPAA és PCI).

A puffer- [készlet kiterjesztéséhez (bpe szolgáltatáskapcsoló)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) kapcsolódó fájlok nem titkosíthatók, ha transzparens adattitkosítás használatával titkosít egy adatbázist. A BPE SZOLGÁLTATÁSKAPCSOLÓ-hez kapcsolódó fájlokhoz olyan fájlrendszer szintű titkosítási eszközöket kell használnia, mint a [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) vagy a [titkosított fájlrendszer (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) .

Mivel egy jogosult felhasználó, például egy biztonsági rendszergazda vagy egy adatbázis-rendszergazda hozzáférhet az adatforgalomhoz, még akkor is, ha az adatbázis transzparens adattitkosítással van titkosítva, kövesse az alábbi ajánlásokat is:

- SQL Server hitelesítés engedélyezése az adatbázis szintjén.
- Használja az Azure AD-hitelesítést [RBAC-szerepkörök](/azure/role-based-access-control/overview)használatával.
- Győződjön meg arról, hogy a felhasználók és az alkalmazások külön fiókokat használnak a hitelesítéshez. Így korlátozhatja a felhasználók és alkalmazások számára biztosított engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát.
- Az adatbázis-szintű biztonság megvalósítása rögzített adatbázis-szerepkörök használatával (például db_datareader vagy db_datawriter). Vagy létrehozhat egyéni szerepköröket is az alkalmazáshoz, hogy explicit engedélyeket adjon a kiválasztott adatbázis-objektumoknak.

Az adatai védelmének egyéb módjaival kapcsolatban vegye figyelembe a következőket:

- A [cellaszintű titkosítás](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
- [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), amely lehetővé teszi az ügyfelek számára a bizalmas adatok titkosítását az ügyfélalkalmazások belsejében, és soha ne fedje fel a titkosítási kulcsokat az adatbázismotor számára (SQL Database vagy SQL Server). Ennek eredményeképpen a Always Encrypted elkülöníti az adattulajdonost (és megtekintheti) és az adatkezelést végzőket (de nem rendelkezik hozzáféréssel).
- [Sor szintű biztonság](/sql/relational-databases/security/row-level-security), amely lehetővé teszi az ügyfelek számára, hogy a lekérdezést végrehajtó felhasználó jellemzői alapján szabályozzák az adatbázistábla soraihoz való hozzáférést. (Példa a csoporttagság és a végrehajtási környezet jellemzőire.)

Előfordulhat, hogy az adatbázis-szintű titkosítást nem használó szervezetek hajlamosabbak az SQL-adatbázisokban található adatsérülést okozó támadásokra.

Az SQL Database transzparens adattitkosítással kapcsolatos további információkért tekintse meg a cikket a [Azure SQL Database transzparens adattitkosításával](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Adatbázis naplózásának engedélyezése
A SQL Server adatbázismotor vagy egy önálló adatbázis egy példányának naplózása az események nyomon követésével és naplózásával jár. SQL Server esetében olyan naplózásokat hozhat létre, amelyek a kiszolgálói szintű eseményekre és az adatbázis szintű eseményekre vonatkozó specifikációkat tartalmaznak. A naplózott események az eseménynaplóba vagy a fájlok naplózására is írhatók.

A telepítésre vonatkozó kormányzati vagy szabványok követelményeitől függően több naplózási szint is SQL Server. A SQL Server naplózása eszközöket és folyamatokat biztosít a különböző kiszolgáló-és adatbázis-objektumokon végzett naplózás engedélyezéséhez, tárolásához és megtekintéséhez.

[Azure SQL Database naplózás](/azure/sql-database/sql-database-auditing) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat.

A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenység megértésében, valamint olyan eltérések és anomáliák megtalálásában, amelyek üzleti vagy biztonsági szabálysértésekre mutathatnak. A naplózás megkönnyíti a megfelelőségi szabványok betartását, de nem garantálja a megfelelőséget.

További információ az adatbázis-naplózásról és annak engedélyezéséről: Ismerkedés [az SQL Database naplózási szolgáltatásával](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Az adatbázis-fenyegetések észlelésének engedélyezése
A veszélyforrások elleni védelem az észlelésen túl is meghalad. Az adatbázis-veszélyforrások elleni védelem a következőket tartalmazza:

- A legérzékenyebb adatok feltárása és besorolása az adatok védelme érdekében.
- Biztonságos konfigurációk megvalósítása az adatbázisban, hogy megvédje az adatbázist.
- A potenciális fenyegetések észlelése és reagálásuk, amint azok bekövetkeznek, így gyorsan reagálhat és javíthat.

**Ajánlott eljárás**: a bizalmas adatok felderítése, osztályozása és címkézése az adatbázisokban.   
**Részletek**: az SQL-adatbázisban lévő adatok osztályozása az [adatfelderítés és a besorolás](/azure/sql-database/sql-database-data-discovery-and-classification) Azure SQL Databaseban való engedélyezésével. A bizalmas adatokhoz való hozzáférést az Azure-irányítópulton vagy a jelentések letöltésén keresztül figyelheti.

**Ajánlott eljárás**: az adatbázis-sebezhetőségek nyomon követése, hogy proaktív módon fejlessze az adatbázis biztonságát.   
**Részletek**: használja a Azure SQL Database [sebezhetőségi felmérési](/azure/sql-database/sql-vulnerability-assessment) szolgáltatást, amely a lehetséges adatbázis-biztonsági réseket vizsgálja. A szolgáltatás olyan szabályok tudásbázisát alkalmazza, amelyek a biztonsági réseket megjelölik, és az ajánlott eljárásoktól, például a helytelen konfigurációtól, a túlzott engedélyektől és a nem védett bizalmas adatoktól származó eltéréseket jelenítenek meg.

A szabályok a Microsoft ajánlott eljárásain alapulnak, és azokra a biztonsági problémákra összpontosítanak, amelyek az adatbázisra és értékes adataira vonatkozó legnagyobb kockázatot jelentenek. Ezek az adatbázis-szintű problémákra és a kiszolgálói szintű biztonsági problémákra, például a kiszolgálói tűzfalbeállítások és a kiszolgálói szintű engedélyekre is kiterjednek. Ezek a szabályok a szabályozási szervek számos követelményét is tükrözik a megfelelőségi szabványoknak való megfelelés érdekében.

**Ajánlott eljárás**: a fenyegetések észlelésének engedélyezése.  
**Részletek**: Azure SQL Database [fenyegetés észlelésének](/azure/sql-database/sql-database-threat-detection) engedélyezése a fenyegetések kivizsgálására és enyhítésére vonatkozó biztonsági riasztások és javaslatok beszerzéséhez. Riasztást kap a gyanús adatbázis-tevékenységekről, a potenciális sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférésről és a lekérdezési mintákról.

A komplex [veszélyforrások elleni védelem](/azure/sql-database/sql-advanced-threat-protection) a fejlett SQL biztonsági funkciók egységes csomagja. Magában foglalja a korábban említett szolgáltatásokat: az adatfelderítést és besorolást, a sebezhetőségek felmérését és a fenyegetések észlelését. Egyetlen helyet biztosít a képességek engedélyezéséhez és kezeléséhez.

A képességek engedélyezése a következőket teszi lehetővé:

- Ismerje meg az adatvédelmi szabványokat és a szabályozási megfelelőségi követelményeket.
- Szabályozhatja az adatbázisokhoz való hozzáférést, és megerősítheti a biztonságot.
- Dinamikus adatbázis-környezet figyelése, ahol a változtatások nehezen követhetők.
- Észlelheti és reagálhat a lehetséges fenyegetésekre.

A veszélyforrások észlelése továbbá a riasztásokat a Azure Security Center az összes Azure-erőforrás biztonsági állapotának központi nézetével integrálja.

## <a name="next-steps"></a>További lépések
Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági eljárásaival és modelljeivel](best-practices-and-patterns.md) foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, vagy e-mailen keresztül secure@microsoft.com
