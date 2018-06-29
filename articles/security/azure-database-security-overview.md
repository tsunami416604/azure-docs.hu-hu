---
title: Azure-adatbázis biztonsági áttekintése |} Microsoft Docs
description: Ez a cikk áttekintést az Azure-adatbázis biztonsági funkciók.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 27f6ccadaa89042ebced759ac3040b5b79e1f8f8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051525"
---
# <a name="azure-database-security-overview"></a>Azure-adatbázis biztonsági áttekintése

Biztonsági adatbázisok kezelésével kapcsolatos elsődleges szempont, és az Azure SQL Database prioritást mindig lett. Az Azure SQL Database tűzfalszabályok és a kapcsolat titkosítási kapcsolat biztonsági szolgáltatásait támogatja. Támogatja a felhasználónévvel és jelszóval hitelesítési és az Azure Active Directory (Azure AD-) hitelesítést, amely Azure Active Directory által kezelt identitások használja. Engedélyezési szerepköralapú hozzáférés-vezérlés használja.

Az Azure SQL Database anélkül, hogy a módosításokat a valós idejű titkosítási és visszafejtési adatbázist, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi elvégzésével támogatja a titkosítást.

A Microsoft további módszereket kínál a vállalati adatok titkosítása:

-   Egyes oszlopok vagy akár cellák az adatok titkosítása különböző titkosítási kulccsal rendelkező cellaszintű titkosítását alkalmazza érhető el.
-   Ha egy hardveres biztonsági modul vagy a titkosítási kulcs hierarchia központi felügyeleti van szüksége, fontolja meg az Azure Key Vault használatával az SQL Server Azure virtuális gépre (VM).
-   Mindig (jelenleg az előzetes verzió) titkosított hajt végre titkosítás átlátható alkalmazások. Azt is lehetővé teszi az ügyfelek titkosításához ügyfélalkalmazások található bizalmas adatok megosztása a titkosítási kulcsokat az SQL Database nélkül.

Az Azure SQL Database Auditing lehetővé teszi a vállalatok számára a naplózási események jelentkezzen be Azure Storage rekord. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést.

Az Azure SQL-adatbázisok szorosan védve legyenek a legtöbb szabályozó kiszolgálásához vagy biztonsági követelmények, többek között a HIPAA, ISO 27001/27002 és PCI DSS szintjét 1. A biztonsági megfelelőség tanúsítványok aktuális listáját érhető el: a [Microsoft Azure Trust Center webhely](http://azure.microsoft.com/support/trust-center/services/).

Ez a cikk végigvezeti a biztonságossá tétele a Microsoft Azure SQL-adatbázisok strukturált, táblázatos és relációs adatok alapjait. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is.

## <a name="protection-of-data"></a>Adatok védelme

SQL-adatbázis segítségével az adatok védelme, adja meg a titkosítást:

- Mozgásérzékelő – keresztül adatainak [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Az adatok aktívan keresztül [átlátható adattitkosítás](http://go.microsoft.com/fwlink/?LinkId=526242).
- Az adatok keresztül használja [mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx).

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   [Az SQL Server egy Azure virtuális gép az Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), ha egy hardveres biztonsági modul vagy a titkosítási kulcs hierarchia központi felügyeleti van szüksége.

### <a name="encryption-in-motion"></a>Mozgásérzékelő – a titkosítás

Összes ügyfél/kiszolgáló alkalmazás gyakori probléma esetén adatvédelmi szükség, mert az adatok áthelyezése nyilvános és titkos hálózatokon keresztül. Ha a hálózaton keresztül helyezze át adatok nem titkosítottak, esély van, hogy azt rögzíthetik és a jogosulatlan felhasználók ellopták. Ha az adatbázis-szolgáltatásokat foglalkozó, győződjön meg arról, hogy az adatok titkosítva van-e az adatbázis-ügyfél és kiszolgáló közötti. Győződjön meg arról is, hogy adattitkosítás adatbázis-kiszolgálók között kommunikálnak egymással, és a középső rétegbeli alkalmazásokkal.

Az egyik probléma egy hálózat adminisztrálásakor az alkalmazások között nem megbízható hálózaton keresztül küldött adatok. Használhat [a TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) hitelesítheti a kiszolgálókat és ügyfeleket, és majd a használatával titkosíthatja az üzeneteket a hitelesített felek között.

A hitelesítési folyamat során az a TLS/SSL-ügyfél üzenetet küld egy TLS/SSL-kiszolgálónak. A kiszolgáló válaszol, az adatokat, amelyek a kiszolgáló hitelesítését. Az ügyfél és kiszolgáló emellett végrehajtja a munkamenetkulcsok cseréjét, és a hitelesítési párbeszéd véget ér. Hitelesítés végbemegy, SSL-lel védett kommunikáció megkezdődhet az a kiszolgáló és az ügyfél között a hitelesítési folyamat során létesített szimmetrikus titkosítási kulcsok használatával.

Az összes kapcsolat az Azure SQL Database titkosításának megkövetelése (TLS/SSL) az összes idő közben "az átvitel", illetve onnan az adatbázis. SQL-adatbázis a TLS/SSL segítségével hitelesítheti a kiszolgálókat és ügyfeleket, és majd a használatával titkosíthatja az üzeneteket a hitelesített felek között. 

Az alkalmazás a kapcsolati karakterláncot meg kell adnia a kapcsolat titkosításához, és nem az, hogy bízzon meg a kiszolgálói tanúsítvány paraméterek. (Ez történik, ha a kapcsolati karakterláncot az Azure-portálon kívül másolja.) Ellenkező esetben a kapcsolat nem ellenőrzi a kiszolgáló identitását, és ki vannak téve a "man közel" támadások. Az ADO.NET illesztőprogram, a kapcsolódási karakterlánc paraméterek vannak `Encrypt=True` és `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Az adatbázis biztonságossá több óvintézkedéseket is igénybe vehet. Például egy biztonságos rendszer kialakítása, titkosítsa a bizalmas eszközök és az adatbázis-kiszolgálók körül tűzfal létrehozása. De forgatókönyv esetében, ahol a fizikai adathordozó (például meghajtókat vagy a biztonsági mentési szalagot) ellopják, a rosszindulatú fél csak visszaállíthatja vagy csatolni az adatbázist, és keresse meg az adatokat.

Egy megoldás, titkosíthatja a bizalmas adatokat az adatbázisban, és a tanúsítvánnyal az adatok titkosításához használt kulcs védelme. Ez a megoldás megakadályozza, hogy ezek a kulcsok az adatokat, de ilyen védelemre tervezett kell lennie.

Ez a probléma, SQL Server és SQL-adatbázisok támogatása megoldására [átlátható adattitkosítás](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Átlátható adattitkosítás titkosítja az SQL Server és SQL-adatbázis az adatfájlok, inaktív adatok titkosítása néven ismert.

Átlátható adattitkosítás abban a fenyegetését, hogy a rosszindulatú tevékenységhez. Valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi végrehajtása anélkül, hogy az alkalmazást módosítani kellene.  

Átlátható adattitkosítás a tárhely teljes adatbázis az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. Az SQL-adatbázis az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítványa nem egyedi a következő SQL-adatbázis kiszolgálónként.

Ha egy adatbázist egy földrajzi-vész-Helyreállítási kapcsolatban, védi egy másik kulcsot minden kiszolgálón. Ha a két adatbázis ugyanazon a kiszolgálón csatlakozik, a beépített tanúsítvány osztoznak. Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. 

További információkért lásd: [átlátható adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Titkosítás használata (ügyfél)

A legtöbb, adatszivárgáshoz hitelkártyaszámok vagy személyes azonosításra alkalmas adatokat például kritikus fontosságú adatok ellopása vonatkozhat. Adatbázisok kincsem troves a bizalmas adatok is lehetnek. Tartalmazhat olyan ügyfelek személyes adatokat (például nemzeti azonosító szám), a versenyképesség fenntartása érdekében bizalmas információkat és a szellemi tulajdont. Elveszett vagy ellopott adatok, különösen a felhasználói adatok, a márka kárt, versenyképes hátránya és súlyos bírságok – még akkor is, peres eljárások eredményezhet.

![A zárolási és kulcs bemutatott mindig titkosítja szolgáltatás](./media/azure-databse-security-overview/azure-database-fig1.png)

[Mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx) egy szolgáltatás, az Azure SQL Database vagy az SQL Server-adatbázisban tárolt bizalmas adatok védelmét. Titkosított mindig lehetővé teszi az ügyfelek ügyfélalkalmazások található bizalmas adatok titkosításához, és soha ne adja meg a titkosítási kulcsokat az adatbázismotor (SQL-adatbázis vagy SQL Server).

Mindig titkosított biztosítja az adatok tulajdonosa (és megtekinthesse) személyek és kezelheti az adatok (de nem hozzáféréssel kell rendelkeznie) személyek között kizárás. Segít biztosítani, hogy a helyi adatbázis-rendszergazdák, a felhő üzemeltetői az adatbázis vagy más kiemelt jogosultságú, de a jogosulatlan felhasználók nem férhetnek hozzá a titkosított adatokat.

Ezenkívül mindig titkosítja hajt végre titkosítás átlátható alkalmazások. Egy mindig titkosítja-kompatibilis illesztőprogram telepítve van-e az ügyfélszámítógépen, akkor is automatikusan titkosításához és visszafejtéséhez az ügyfélalkalmazás lévő bizalmas adatokat. Az illesztőprogram az adatok bizalmas oszlopban adatok az adatbázismotorhoz való csatlakozáshoz előtt titkosítja. Az illesztőprogram automatikusan újraírja lekérdezések, így az alkalmazás szemantikáját megmaradnak. Hasonlóképpen a az illesztőprogram transzparens módon visszafejti a titkosított oszlopokat, a lekérdezés eredményei között szereplő tárolt adatok.

## <a name="access-control"></a>Hozzáférés-vezérlés
A biztonság SQL-adatbázis használatával szabályozza a hozzáférést:

- Az tűzfalszabály, amely korlátozza a kapcsolódási IP-cím alapján.
- Hitelesítési mechanizmusok, hogy a felhasználóknak kell igazolnia az identitását.
- Hitelesítési mechanizmusok, hogy a felhasználók adott műveletekhez és az adatok.

### <a name="database-access"></a>Adatbázis-hozzáférés

Az adatvédelem kezdődik a adatokhoz való hozzáférés szabályozása. Az Adatközpont, amelyen az adatok fizikai hozzáférést kezeli. Beállíthatja, hogy a tűzfal, a hálózati rétegben biztonság kezeléséhez. Akkor is hozzáférést bejelentkezések a hitelesítés konfigurálása és engedélyek a kiszolgáló és az adatbázis-szerepkörök meghatározása.

#### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) biztosít az Azure és az egyéb internet-alapú alkalmazások relációs adatbázis-szolgáltatás. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Az Azure SQL Database szolgáltatásban csak az 1433-as TCP-porton keresztül érhető el. SQL-adatbázis eléréséhez a számítógépről, győződjön meg arról, hogy az ügyfél számítógép tűzfal engedélyezi-e a kimenő TCP kommunikációs 1433-as TCP-porton. Ha a bejövő kapcsolatok azonban nincs szükség más alkalmazások, tiltsa le azokat 1433-as TCP-porton.

#### <a name="authentication"></a>Hitelesítés

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Database két hitelesítési típust támogat:

-   **SQL Server-hitelesítés**: egyszeri bejelentkezési fiókot jön létre, amikor egy logikai SQL-példányhoz hoz létre, az SQL-adatbázis előfizető fiókja. Ez a fiók csatlakozik a [SQL Server-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (felhasználónév és jelszó). Ez a fiók rendszergazdának számít a logikai kiszolgáló példányán és a példányhoz csatolt összes felhasználói adatbázisban. Az előfizető fiók engedélyeit nem korlátozhatók. Ilyen fiókból csak egy létezhet.
-   **Az Azure Active Directory authentication**: [az Azure AD-alapú hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) egy mechanizmus a csatlakozás az Azure SQL Database és az Azure SQL Data Warehouse identitásokat az Azure ad-ben. Központilag az adatbázis-felhasználók az identitások kezeléséhez használható.

![Az SQL Database az Azure AD authentication](./media/azure-databse-security-overview/azure-database-fig2.png)

 Az Azure AD-alapú hitelesítés előnyei a következők:
  - SQL Server-hitelesítés helyett biztosít.
  - Ez segít a felhasználói identitások elterjedése adatbázis-kiszolgáló között, és lehetővé teszi, hogy a jelszó Elforgatás egyetlen helyen.
  - Adatbázis-engedélyek külső (az Azure AD) csoportok használatával kezelheti.
  - Integrált Windows-hitelesítés és egyéb, amely támogatja az Azure AD hitelesítési engedélyezésével azt megnövelésével kiküszöbölheti jelszavak tárolását.

#### <a name="authorization"></a>Engedélyezés
[Engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) hivatkozik a felhasználók mit tehetnek Azure SQL-adatbázis belül. A felhasználói fiók adatbázis által szabályozott [szerepkörtagságok](https://msdn.microsoft.com/library/ms189121) és [objektumszintű engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx). Engedélyezési meghatározása az adott erőforrás használata engedélyezett rendszerbiztonsági tag hozzáférhet biztonságos erőforrások, és milyen műveletek során a rendszer.

### <a name="application-access"></a>Alkalmazás-hozzáférés

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás
A képviselőjével egy ügyfélszolgálatával, előfordulhat, hogy hívóknak társadalombiztosítási szám vagy hitelkártyaszám több számjegyet azonosításához. Azonban ezeket az elemeket kell nem teljesen elérhetővé tehető a munkatársának.

Egy maszkolási szabályra, társadalombiztosítási szám vagy az eredményhalmaz bármely lekérdezés hitelkártyaszám utolsó négy számjegye kivételével az összes elfedi adhat meg.

![Egy SQL-adatbázis és az üzleti alkalmazások között küldött több maszkolás](./media/azure-databse-security-overview/azure-database-fig3.png)

Másik példaként a megfelelő adatok maszk definiálható személyes azonosításra alkalmas adatok védelme érdekében. Egy fejlesztő majd lekérdezheti a termelési környezetben hibaelhárítási célból megfelelőségi szabályzat megsértése nélkül.

[Az SQL Database dinamikus adatmaszkolása](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) úgy korlátozza a bizalmas adatok közzétételét, hogy maszkolja azokat a nem kiemelt jogosultságú felhasználók számára. Dinamikus adatmaszkolási Azure SQL Database 12-es verziója támogatott.

[Dinamikus adatmaszkolási](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) segít jogosulatlan hozzáférés elkerülése érdekében bizalmas adatokat azáltal, hogy meghatározza mekkora a bizalmas adatokat, hogy láthatóvá váljon a az alkalmazási rétegre gyakorolt minimális hatás mellett. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.


> [!Note]
> Dinamikus adatmaszkolási konfigurálhatja az Azure-adatbázis rendszergazdai, kiszolgálói rendszergazda vagy biztonsági tisztviselő szerepkörök.

#### <a name="row-level-security"></a>Sorszintű biztonság
Több-bérlős adatbázisok egy másik közös biztonsági követelménye [sorszintű biztonság](https://msdn.microsoft.com/library/dn765131.aspx). Ez a szolgáltatás segítségével a rendszer a lekérdezés végrehajtása felhasználó jellemzők alapján adatbázis tábla sorainak való hozzáférés szabályozása. (Például mutatókat csoport tagságát és végrehajtási környezetet.)

![Amely a felhasználó hozzáférési sorok egy tábla egy ügyfélalkalmazás keresztül sorszintű biztonság](./media/azure-databse-security-overview/azure-database-fig4.png)

A hozzáférés korlátozási logika található az adatbázis-rétegből, hanem helyezkedik el az adatokat egy másik alkalmazás réteg. Az adatbázis-rendszer a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az adott adatok próbál meg elérni a réteg alapján. Ez lehetővé teszi a biztonsági rendszer megbízhatóbb és robusztus a biztonsági rendszer felületének csökkentése révén.

A sorszintű biztonsági predikátum-alapú hozzáférés-vezérlés vezet be. Egy rugalmas, központi kiértékelése, amelyek is figyelembe venni metaadatok vagy a rendszergazda meghatározza, hogy a többi feltételt szükség szerint jellemzői. A predikátum használatos feltételként annak meghatározásához, hogy a felhasználó rendelkezik-e a megfelelő adatokhoz való hozzáférést a felhasználói attribútumok alapján. Címke-alapú hozzáférés vezérléséhez predikátum-alapú hozzáférés-vezérlés használatával.

## <a name="proactive-monitoring"></a>Proaktív figyelés
SQL-adatbázis segítségével az adatok védelme, adja meg a *naplózás* és *veszélyforrások detektálása* képességeit.

### <a name="auditing"></a>Naplózás
[Az Azure SQL Database auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) események és az adatbázis fellépő változások betekintést arra a képességére, növeli. Többek között a frissítések és az adatok lekérdezéseket.

Adatbázis-események, mind az írás őket naplózási jelentkezzen be az Azure storage-fiók SQL Database auditing követi nyomon. Naplózás segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek jelezhetik üzleti problémákra vagy a biztonság megsértésére betekintést. Naplózás lehetővé teszi, hogy és elősegíti a megfelelést a megfelelőségi követelményeket, de nem biztosítja a megfelelőség.

SQL-adatbázis, a naplózás is használhatja:

-   **Tartsa meg** kijelölt események egy naplóban. Adatbázis-műveleteket kell naplózni kategóriáinak adhat meg.
-   **A jelentés** adatbázis-tevékenység. Előre konfigurált jelentések és egy irányítópult segítségével gyorsan Ismerkedés a tevékenységet és az események naplózásához.
-   **Elemezze** jelentéseket. Gyanús események, a szokatlan tevékenység és a trendeket találja.

Két naplózási módszer áll rendelkezésre:

-   **Blobnaplózási funkció**: az Azure Blob storage írja a naplókat. Ez a újabb naplózási módszer. Ez nagyobb teljesítményt nyújt, nagyobb részletességgel objektumszintű naplózást, és költséghatékonyabb.
-   **Tábla naplózás**: Azure Table storage írja a naplókat.

### <a name="threat-detection"></a>Fenyegetések észlelése
[Az Azure SQL Database fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) gyanús tevékenységek észlelése esetén, amelyek esetleges biztonsági fenyegetéseket jelezhetnek jelzik. A fenyegetésészlelés segítségével válaszolni gyanús eseményt az adatbázisban, például az SQL-utasítások, azok bekövetkezésekor. Riasztások biztosít, és engedélyezi, hogy az Azure SQL Database segítségével megismerheti a gyanús események naplózása.

![A fenyegetésészlelés az SQL-adatbázis és a webes alkalmazás egy rosszindulatú belső és külső támadó](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Például SQL-injektálás az egyik webes alkalmazásokhoz, közös biztonsági problémákat. Adatalapú alkalmazások támadásokkal szolgál. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai.

Biztonsági tisztviselő vagy más kijelölt rendszergazdák kérheti le az azonnali értesítések adatbázis gyanús tevékenységekről azok bekövetkezésekor. Minden értesítés részletesen bemutatja a gyanús tevékenység, illetve további vizsgálata, és a fenyegetések mérséklésére javasolja.        

## <a name="centralized-security-management"></a>Központi kezelés

[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Biztosít integrált biztonsági monitorozást és az Azure-előfizetések. Észleli a veszélyeket, amely szabályzatkezelést segít, és számos biztonsági megoldással együttműködik.

[A Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) megakadályozhatja a adatokat az SQL-adatbázis azáltal, hogy a kiszolgálók és adatbázisok biztonsági betekintést nyújt segítséget. A Security Center a következőket teheti:

-   SQL adatbázis-titkosítás és naplózási házirendek meghatározása.
-   SQL adatbázis-erőforrások biztonságának figyelheti az előfizetések között.
-   Gyorsan problémák azonosítása és javítása biztonsági.
-   Riasztások integrálni [Azure SQL Database fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Biztonsági központ szerepkörön alapuló hozzáférés támogatja.

## <a name="azure-marketplace"></a>Azure Piactér

Az Azure Piactér olyan online alkalmazás- és szolgáltatáspiactér, amelyen az induló vállalkozások és a független szoftverszállítók kínálhatják megoldásaikat az Azure-szolgáltatásokat világszerte használó ügyfeleknek.
Az Azure piactér a Microsoft Azure partner ökoszisztéma egy egységes platform jobb szolgálnak ügyfelek és partnerek ezeket. Is [keresést futtat](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) adatbázis elérhető biztonsági termékek megtekintéséhez az Azure piactéren.

## <a name="next-steps"></a>További lépések

- [Az Azure SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Az Azure Security Center és az Azure SQL Database szolgáltatás](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [SQL-adatbázis fenyegetések észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [SQL-adatbázis teljesítményének növelése](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
