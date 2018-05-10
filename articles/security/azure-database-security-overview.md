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
ms.openlocfilehash: b5e9e412fddf727237b008e37238fa2c547694de
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="azure-database-security-overview"></a>Azure-adatbázis biztonsági áttekintése

Biztonsági elsődleges szempont kezeléséhez, adatbázisok, és az Azure SQL Database prioritást mindig lett. Az Azure SQL Database tűzfalszabályok és a kapcsolat titkosítási kapcsolat biztonsági szolgáltatásait támogatja. Támogatja a felhasználónévvel és jelszóval hitelesítési és az Azure Active Directory-hitelesítéssel, amely Azure Active Directory által kezelt identitások használ. Engedélyezési szerepköralapú hozzáférés-vezérlés használja.

Az Azure SQL Database anélkül, hogy a módosításokat a valós idejű titkosítási és visszafejtési adatbázist, a társított biztonsági másolatok és a tranzakciós naplófájlok nyugalmi elvégzésével támogatja a titkosítást.

A Microsoft további módszereket kínál a vállalati adatok titkosítása:

-   Egyes oszlopok vagy akár cellák az adatok titkosítása különböző titkosítási kulccsal rendelkező cellaszintű titkosítását alkalmazza.
-   Ha egy hardveres biztonsági modul vagy a titkosítási kulcs hierarchia központi felügyeleti van szüksége, fontolja meg az Azure Key Vault SQL Server egy Azure virtuális gép.
-   (Jelenleg az előzetes verzió) titkosított mindig titkosítás átlátható teszi alkalmazások, és lehetővé teszi az ügyfelek titkosításához ügyfélalkalmazások található bizalmas adatok megosztása a titkosítási kulcsokat az SQL Database nélkül.

Az Azure SQL Database Auditing lehetővé teszi a vállalatok számára, hogy egy naplózási Azure Storage bejelentkezési eseményeket rögzíteni. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést.

 SQL Azure-adatbázisok szorosan védve legyenek a legtöbb szabályozó kiszolgálásához vagy biztonsági követelmények, például a HIPAA, ISO 27001/27002 és PCI DSS szintjét 1, többek között. A biztonsági megfelelőség tanúsítványok aktuális listáját érhető el: a [Microsoft Azure Trust Center webhely](http://azure.microsoft.com/support/trust-center/services/).

Ez a cikk végigvezeti a strukturált, a Tabular és a relációs adatok biztonságossá tétele a Microsoft Azure SQL-adatbázisok alapjait. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is.

Azure-adatbázis biztonsági áttekintő cikkben elsősorban az alábbi területeken:

-   Adatok védelme
-   Hozzáférés-vezérlés
-   Proaktív figyelés
-   Központi kezelés
-   Azure Piactér

## <a name="protect-data"></a>Adatok védelme

SQL-adatbázis titkosítja az adatokat, adja meg a titkosítási adatok mozgásérzékelési használatával [Transport Layer Security](https://support.microsoft.com/kb/3135244), az adatokat a rest-használatával [átlátható adattitkosítási](http://go.microsoft.com/fwlink/?LinkId=526242), és az adatok használata használatával [ Mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx).

Ez a szakasz a döntésről bővebben:

-   Mozgásérzékelő – a titkosítás
-   Titkosítás inaktív állapotban
-   Titkosítás használata (ügyfél)

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelésére van szüksége, fontolja meg az [Azure Key Vault és az SQL Server együttes használatát egy Azure virtuális gépen](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Mozgásérzékelő – a titkosítás

Összes ügyfél/kiszolgáló alkalmazás gyakori probléma esetén adatvédelmi szükség, mert az adatok áthelyezése nyilvános és titkos hálózatokon keresztül. Ha nem titkosított a hálózaton keresztül helyezze át adatokat, nincs az esélye, hogy azt rögzíthetik és a jogosulatlan felhasználók ellopták. Adatbázis-szolgáltatásokat a meghatározásakor kell győződjön meg arról, hogy az adatok titkosítását, valamint az adatbázis-ügyfél és kiszolgáló közötti adatbázis-kiszolgálók kommunikálnak egymással, és a középső rétegbeli alkalmazásokkal.

Az egyik probléma egy hálózat adminisztrálásakor az alkalmazások között nem megbízható hálózaton keresztül küldött adatok. Használhat [a TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) hitelesítheti a kiszolgálókat és ügyfeleket, és majd a használatával titkosíthatja az üzeneteket a hitelesített felek között.

A hitelesítési folyamatban egy TLS/SSL-ügyfél üzenetet küld egy TLS/SSL-kiszolgálónak, és a kiszolgáló válaszol, hogy a kiszolgáló hitelesítését ezekre az adatokra. Az ügyfél és kiszolgáló emellett végrehajtja a munkamenetkulcsok cseréjét, és a hitelesítési párbeszéd véget ér. Hitelesítés végbemegy, a kiszolgáló és az ügyfelet a hitelesítési folyamat során létesített szimmetrikus titkosítási kulcsok között megkezdődhet az SSL-lel védett kommunikáció.

Az Azure SQL Database-hez való kapcsolódáshoz minden esetben titkosítás (SSL/TLS) szükséges, amikor az adatok átvitele folyamatban van az adatbázisból vagy az adatbázisba. SQL Azure a TLS/SSL segítségével hitelesítheti a kiszolgálókat és ügyfeleket, és majd a használatával titkosíthatja az üzeneteket a hitelesített felek között. Az alkalmazás a kapcsolati karakterláncot, meg kell adnia a kapcsolat titkosításához, és nem megbízható a tanúsítvány (ebben az esetben, ha a kapcsolati karakterláncot az Azure portálon kívül), ellenkező esetben a kapcsolat paraméterek nem ellenőrzi, hogy a a kiszolgáló identitását, és ki vannak téve a támadások "man közel" lesz. Az ADO.NET-illesztőprogramra vonatkozóan, például a kapcsolódási karakterlánc paraméterek titkosítása = True és TrustServerCertificate = False.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Az adatbázisban, például a biztonságos rendszerek tervezése, bizalmas eszközök titkosítása és az adatbázis-kiszolgálók körül tűzfal felépítése érdekében több óvintézkedéseket is igénybe vehet. Azonban forgatókönyv esetében, ahol a fizikai adathordozó (például meghajtókat vagy a biztonsági mentési szalagot) ellopják, a rosszindulatú fél csak visszaállíthatja vagy csatolni az adatbázist, és keresse meg az adatokat.

Egy megoldást, hogy titkosítsa a bizalmas adatokat az adatbázisban, és a tanúsítvánnyal az adatok titkosításához használt kulcs védelmét. Ez megakadályozza, hogy ezek a kulcsok adatok használatával, de ilyen védelemre tervezett kell lennie.

A probléma megoldásához, SQL Server és az Azure SQL támogatja [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). TDE titkosítja az SQL Server és az Azure SQL Database az adatfájlok, inaktív adatok titkosítása néven ismert.

Az Azure SQL Database átlátható adattitkosítás segít valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, a kártékony tevékenység fenyegetés elleni védelem, és a tranzakciós naplófájlok nyugalmi nem igényel változtatásokat a az alkalmazás.  

TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja. Az SQL-adatbázis az adatbázis-titkosítási kulcs védi egy beépített kiszolgálói tanúsítványt. A beépített kiszolgálói tanúsítványa nem egyedi a következő SQL-adatbázis kiszolgálónként.

Ha egy adatbázis részt GeoDR-kapcsolatban, védi egy másik kulcsot minden kiszolgálón. Ha a két adatbázis ugyanazon a kiszolgálón csatlakozik, a beépített tanúsítvány osztoznak. Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. TDE általános ismertetését lásd: [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Titkosítás használata (ügyfél)

A legtöbb, adatszivárgáshoz hitelkártyaszámok vagy személyes azonosításra alkalmas adatokat például kritikus fontosságú adatok ellopása vonatkozhat. Adatbázisok kincsem troves a bizalmas adatok is lehetnek. Tartalmazhat olyan ügyfelek személyes adatokat, a versenyképesség fenntartása érdekében bizalmas információkat és a szellemi tulajdont. Elveszett vagy ellopott adatok, különösen a felhasználói adatok, a márka kárt, versenyképes hátránya és súlyos bírságok eredményezhet – peres eljárások is.

![Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

[Mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx) egy szolgáltatás, bizalmas adatok védelmének, hitelkártyaszámok vagy a nemzeti azonosító szám (például Egyesült államokbeli társadalombiztosítási számot), például az Azure SQL Database vagy az SQL Server adatbázis tárolja. Titkosított mindig lehetővé teszi az ügyfelek ügyfélalkalmazások található bizalmas adatok titkosításához, és soha ne adja meg a titkosítási kulcsokat az adatbázismotor (SQL-adatbázis vagy SQL Server).

Mindig titkosított biztosítja az adatok tulajdonosa (és megtekinthesse) elkülönítése és azokra, akik az adatok kezelése (de nem hozzáféréssel kell rendelkeznie). Biztosításával, hogy a helyi adatbázis-rendszergazdák, a felhő adatbázis operátorok, vagy más kiemelt jogosultságú, illetéktelen felhasználókkal szemben, de nem fér hozzá a titkosított adatokat.

Ezenkívül mindig titkosítja hajt végre titkosítás átlátható alkalmazások. Egy mindig titkosítja-engedélyezve van telepítve illesztőprogram az ügyfélszámítógépen, hogy azt is automatikusan titkosításához és visszafejtéséhez az ügyfélalkalmazás lévő bizalmas adatokat. Az illesztőprogram az adatok bizalmas oszlopban adatok az adatbázismotorhoz való csatlakozáshoz előtt titkosítja, és automatikusan újraírja lekérdezések, így az alkalmazás szemantikáját megmaradnak. Hasonlóképpen a az illesztőprogram transzparens módon visszafejti a titkosított oszlopokat, a lekérdezés eredményei között szereplő tárolt adatok.

## <a name="access-control"></a>Hozzáférés-vezérlés
A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést.

### <a name="database-access"></a>Adatbázis-hozzáférés

Az adatvédelem kezdődik a adatokhoz való hozzáférés szabályozása. Adatközpont meghibásodását az adatokat fizikai hozzáférés kezeli, amíg beállíthatja, hogy a tűzfal, a hálózati rétegben biztonság kezeléséhez. Akkor is hozzáférést bejelentkezések a hitelesítés konfigurálása és engedélyek a kiszolgáló és az adatbázis-szerepkörök meghatározása.

Ez a szakasz a döntésről bővebben:

-   Tűzfal és tűzfalszabályok
-   Hitelesítés
-   Engedélyezés

#### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) szolgáltatás 1433-as TCP-porton keresztül csak érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha más alkalmazásoknak nincs szüksége rá, akkor blokkolja az 1433-as TCP-port bejövő kapcsolatait.

#### <a name="authentication"></a>Hitelesítés

Az SQL Database-hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:

-   **SQL-hitelesítés:** egyszeri bejelentkezési fiókot jön létre, amikor egy logikai SQL-példányhoz hoz létre, az SQL-adatbázis előfizető fiókja. Ez a fiók csatlakozik használatával [SQL Server-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (felhasználónév és jelszó). Ez a fiók rendszergazdának számít a logikai kiszolgáló példányán és a példányhoz csatolt összes felhasználói adatbázisban. Az előfizetői fiók engedélyei nem korlátozhatók. Ilyen fiókból csak egy létezhet.
-   **Az Azure Active Directory-hitelesítés:** [az Azure Active Directory hitelesítési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) identitásokkal az Azure Active Directoryban (Microsoft Azure SQL Database és az SQL Data Warehouse kapcsolódni egy mechanizmus Az Azure AD). Ez lehetővé teszi, hogy az adatbázis-felhasználók identitásainak kezelése a központilag.

![Hitelesítés](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure Active Directory-hitelesítés előnyei a következők:
  - SQL Server-hitelesítés helyett biztosít.
  - Is segít a felhasználói identitások elterjedése adatbázis-kiszolgáló között, és lehetővé teszi, hogy a jelszó Elforgatás egyetlen helyen.
  - Adatbázis-engedélyek külső (az Azure Active Directory) csoportok használatával kezelheti.
  - Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével azt megnövelésével kiküszöbölheti jelszavak tárolását.

#### <a name="authorization"></a>Engedélyezés
[Engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) mi hivatkozik egy felhasználó egy Azure SQL Database belül végrehajtható, és ez által a felhasználói fiók adatbázis [szerepkörtagságok](https://msdn.microsoft.com/library/ms189121) és [objektumszintű engedélyeket](https://msdn.microsoft.com/library/ms191291.aspx). Engedélyezési meghatározása az adott erőforrás használata engedélyezett rendszerbiztonsági tag hozzáférhet biztonságos erőforrások, és milyen műveletek során a rendszer.

### <a name="application-access"></a>Alkalmazás-hozzáférés

Ez a szakasz a döntésről bővebben:

-   Dinamikus adatmaszkolás
-   Sorszintű biztonság

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás
A képviselőjével egy ügyfélszolgálatával, előfordulhat, hogy azonosíthatja a hívók társadalombiztosítási szám vagy hitelkártyaszám több számjegyet, de ezeket az elemeket kell nem teljesen elérhetővé tehető a munkatársának.

Egy maszkolási szabályra meghatározása, hogy minden maszkok, de a társadalombiztosítási szám vagy az eredményben hitelkártyaszám utolsó négy számjegye bármely lekérdezés beállítva.

![Dinamikus adatmaszkolás](./media/azure-databse-security-overview/azure-database-fig3.png)

Másik példaként a megfelelő adatok maszk definiálható személyes azonosításra alkalmas adatokat (PII) adatok védelmét, hogy a fejlesztő lekérdezheti a termelési környezetben hibaelhárítási célból megfelelőségi szabályzat megsértése nélkül.

[SQL adatbázis dinamikus Adatmaszkolási](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok veszélyeztetettségének által maszkolás a jogosulatlan felhasználók számára. Dinamikus adatmaszkolási Azure SQL Database 12-es verziója támogatott.

[Dinamikus adatmaszkolási](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) segít jogosulatlan hozzáférés elkerülése érdekében bizalmas adatokat azáltal, hogy meghatározza mekkora a bizalmas adatokat, hogy láthatóvá váljon a az alkalmazási rétegre gyakorolt minimális hatás mellett. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.


> [!Note]
> Dinamikus adatmaszkolási konfigurálhatja az Azure-adatbázis rendszergazdai, kiszolgálói rendszergazda vagy biztonsági tisztviselő szerepkörök.

#### <a name="row-level-security"></a>Sorszintű biztonság
Több-bérlős adatbázisok egy másik közös biztonsági követelménye [sorszintű biztonság](https://msdn.microsoft.com/library/dn765131.aspx). Ez a funkció lehetővé teszi a felhasználó (pl. csoport tagsági vagy a végrehajtási környezet) lekérdezése jellemzők alapján adatbázis tábla sorainak való hozzáférés szabályozása.

![A biztonsági szint sor](./media/azure-databse-security-overview/azure-database-fig4.png)

A hozzáférés korlátozási logika található az adatbázis-rétegből, hanem helyezkedik el az adatokat egy másik alkalmazás réteg. Az adatbázis-rendszer a hozzáférési korlátozásokat alkalmazza, minden alkalommal, amikor az adott adatok próbál meg elérni a réteg alapján. Ez lehetővé teszi a biztonsági rendszer megbízhatóbb és robusztus a biztonsági rendszer felületének csökkentése révén.

Sorszintű biztonság predikátum alapú hozzáférés-vezérlés vezet be. Egy rugalmas, a központi, a predikátum alapú kiértékelése, amelyek is figyelembe venni metaadatok vagy a rendszergazda meghatározza, hogy a többi feltételt szükség szerint jellemzői. A predikátum használatos feltételként annak meghatározásához, hogy a felhasználó rendelkezik-e a megfelelő adatokhoz való hozzáférést a felhasználói attribútumok alapján. Címke-alapú hozzáférés-vezérlés predikátum-alapú hozzáférés-vezérlés használatával valósítható meg.

## <a name="proactive-monitoring"></a>Proaktív figyelés
SQL-adatbázis titkosítja az adatokat, adja meg a **naplózás** és **veszélyforrások detektálása** képességeit.

### <a name="auditing"></a>Naplózás
SQL Database Auditing növeli az események és az adatbázis, beleértve a frissítéseket és az adatok lekérdezéseket fellépő változások betekintést arra a képességére.

[Az Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be az Azure Storage-fiók. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában. Naplózás lehetővé teszi, hogy és elősegíti a megfelelést a megfelelőségi követelményeket, de nem biztosítja a megfelelőség.

SQL Database Auditing teszi lehetővé:

-   **Tartsa meg** kijelölt események egy naplóban. Adatbázis-műveleteket kell naplózni kategóriáinak adhat meg.
-   **A jelentés** adatbázis-tevékenység. Előre konfigurált jelentések és egy irányítópult segítségével gyorsan Ismerkedés a tevékenységet és az események naplózásához.
-   **Elemezze** jelentéseket. Gyanús események, a szokatlan tevékenység és a trendeket találja.

A naplózás két módszer áll rendelkezésre:

-   **Blobnaplózási funkció** -Azure Blob Storage írja a naplókat. Ez az újabb naplózási módszer, amely nagyobb teljesítményt nyújt, támogatja a nagyobb részletességgel objektumszintű naplózást, és költséghatékonyabb megoldás.
-   **Tábla naplózás** -Azure Table Storage írja a naplókat.

### <a name="threat-detection"></a>Fenyegetések észlelése
[Az Azure SQL Database fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) gyanús tevékenységek észlelése esetén, amelyek esetleges biztonsági fenyegetéseket jelezhetnek jelzik. A fenyegetésészlelés lehetővé teszi az adatbázisban, például az SQL-utasítások, gyanús események válaszolni azok bekövetkezésekor. Riasztások biztosít, és engedélyezi, hogy az Azure SQL Database Auditing felfedezése, mely a gyanús események.

![A fenyegetésészlelés](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Például SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák elleni támadásra adatvezérelt alkalmazások, az interneten. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai.

Biztonsági tisztviselő vagy más kijelölt rendszergazdák kérheti le az azonnali értesítések adatbázis gyanús tevékenységekről azok bekövetkezésekor. Minden értesítés részletesen bemutatja a gyanús tevékenység, illetve további vizsgálata, és a fenyegetések mérséklésére javasolja.        

## <a name="centralized-security-management"></a>Központi kezelés

[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

[A Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) megakadályozhatja a adatokat az SQL-adatbázis azáltal, hogy a kiszolgálók és adatbázisok biztonsági betekintést nyújt segítséget. A Security Center a következőket teheti:

-   SQL adatbázis-titkosítás és naplózási házirendek meghatározása.
-   SQL adatbázis-erőforrások biztonságának figyelheti az előfizetések között.
-   Gyorsan problémák azonosítása és javítása biztonsági.
-   Riasztások integrálni [Azure SQL Database fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Biztonsági központ szerepkörön alapuló hozzáférés támogatja.

## <a name="azure-marketplace"></a>Azure Piactér

Az Azure Piactér olyan online alkalmazás- és szolgáltatáspiactér, amelyen az induló vállalkozások és a független szoftverszállítók kínálhatják megoldásaikat az Azure-szolgáltatásokat világszerte használó ügyfeleknek.
Az Azure Piactér egyetlen platformban ötvözi a Microsoft Azure partneri rendszereit ügyfeleink és partnereink magasabb színvonalú kiszolgálása érdekében. Kattintson a [Itt](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) áttekintő adatbázis biztonsági termékek Azure piactéren elérhető.

## <a name="next-steps"></a>További lépések

- További információ [az Azure SQL Database biztonságos](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- További információ [az Azure Security Center és az Azure SQL Database szolgáltatás](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- A fenyegetésészlelés kapcsolatos további információkért lásd: [SQL adatbázis Fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- További tudnivalókért lásd: [javítása SQL-adatbázis teljesítményének](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
