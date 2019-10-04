---
title: Az Azure Database biztonsági áttekintése | Microsoft Docs
description: Ez a cikk az Azure Database biztonsági funkcióinak áttekintését tartalmazza.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906061"
---
# <a name="azure-database-security-overview"></a>Az Azure Database biztonsági áttekintése

A biztonság az adatbázisok kezelésének egyik legfőbb szempontja, és mindig a Azure SQL Database prioritása. A Azure SQL Database a tűzfalakkal és a kapcsolatok titkosításával támogatja a kapcsolatok biztonságát. Támogatja a hitelesítést a felhasználónévvel és jelszóval, valamint Azure Active Directory (Azure AD) hitelesítéssel, amely Azure Active Directory által felügyelt identitásokat használ. Az engedélyezés szerepköralapú hozzáférés-vezérlést használ.

Azure SQL Database támogatja a titkosítást az adatbázisok, a társított biztonsági másolatok és a tranzakciónapló-fájlok valós idejű titkosításával és visszafejtésével anélkül, hogy módosítani kellene az alkalmazást.

A Microsoft további lehetőségeket biztosít a vállalati információk titkosítására:

-   A cella szintű titkosítás az egyes oszlopok vagy az adatcellák különböző titkosítási kulcsokkal történő titkosítására is használható.
-   Ha hardveres biztonsági modulra vagy a titkosítási kulcs hierarchiájának központi felügyeletére van szüksége, vegye fontolóra Azure Key Vault használatát egy Azure-beli virtuális gép (VM) SQL Serverával.
-   A Always Encrypted (jelenleg előzetes verzióban érhető el) lehetővé teszi az alkalmazások számára a titkosítás átláthatóságát. Azt is lehetővé teszi, hogy az ügyfelek titkosítsák a bizalmas adatokat az ügyfélalkalmazások között anélkül, hogy a titkosítási kulcsokat a SQL Databaseával kellene megosztani.

Azure SQL Database naplózás lehetővé teszi, hogy a vállalatok eseményeket rögzítsen az Azure Storage-ban lévő naplóba. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést.

Az Azure SQL Database-adatbázisok szorosan biztonságosak a legtöbb szabályozási vagy biztonsági követelmény kielégítése érdekében, beleértve a HIPAA, az ISO 27001/27002 és az 1. szintű PCI DSS. A biztonsági megfelelőségi tanúsítványok aktuális listája a [Microsoft Azure megbízhatósági központ webhelyén](https://azure.microsoft.com/support/trust-center/services/)érhető el.

Ez a cikk bemutatja, hogyan biztosítható Microsoft Azure SQL-adatbázisok strukturált, táblázatos és összehasonlítható adatforrásokhoz való biztonságossá tételéhez. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is.

## <a name="protection-of-data"></a>Adatvédelem

A SQL Database titkosítás biztosításával segít az adatai védelmében:

- [Transport Layer Security (TLS) protokollon](https://support.microsoft.com/kb/3135244)keresztüli mozgásban lévő adatfeldolgozások.
- A nyugalmi állapotban lévő adatok [transzparens](https://go.microsoft.com/fwlink/?LinkId=526242)adattitkosítással.
- [Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx)használatával használatban lévő adatértékek.

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   [Azure Key Vault az Azure-beli virtuális gép SQL Serverával](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), ha hardveres biztonsági modulra vagy a titkosítási kulcs hierarchiájának központi felügyeletére van szüksége.

### <a name="encryption-in-motion"></a>Titkosítás a mozgásban

Az ügyfél-és kiszolgálói alkalmazások esetében gyakori probléma az, ha az adatok nyilvános és magánhálózati hálózatokon keresztüli továbbításra szorulnak. Ha a hálózaton áthaladó adatforgalom nincs titkosítva, lehetséges, hogy a jogosulatlan felhasználók rögzítheti és ellophatja azokat. Az adatbázis-szolgáltatásokkal kapcsolatos problémák esetén győződjön meg arról, hogy az adatbázis-ügyfél és a kiszolgáló között titkosítva vannak az adatforgalom. Győződjön meg arról is, hogy a rendszer titkosítja az adattitkosítást az adatbázis-kiszolgálók között, amelyek kommunikálnak egymással és a középső rétegbeli alkalmazásokkal.

A hálózat felügyeletének egyik problémája az alkalmazások közötti, nem megbízható hálózaton keresztül küldött adat védelme. A [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) segítségével hitelesítheti a kiszolgálókat és az ügyfeleket, majd a használatával titkosíthatja az üzeneteket a hitelesített felek között.

A hitelesítési folyamat során a TLS/SSL-ügyfél üzenetet küld egy TLS-/SSL-kiszolgálónak. A kiszolgáló válaszol azokra az adatokra, amelyeket a kiszolgálónak hitelesítenie kell. Az ügyfél és a kiszolgáló a munkamenet-kulcsok további cseréjét hajtja végre, a hitelesítési párbeszédablak pedig véget ér. A hitelesítés befejezésekor az SSL-védelemmel ellátott kommunikáció a kiszolgáló és az ügyfél között a hitelesítési folyamat során létesített szimmetrikus titkosítási kulcsok használatával kezdődhet.

A Azure SQL Database összes kapcsolatának titkosítást (TLS/SSL) kell használnia, amikor az adatok átvitele folyamatban van az adatbázisból és az-ból. SQL Database a TLS/SSL protokoll használatával hitelesíti a kiszolgálókat és az ügyfeleket, majd a használatával titkosítja az üzeneteket a hitelesített felek között. 

Az alkalmazás kapcsolati karakterláncában meg kell adnia paramétereket a kapcsolat titkosításához, és nem kell megbíznia a kiszolgálói tanúsítványban. (Ez akkor történik, ha a Azure Portalről másolja a kapcsolatok karakterláncát.) Ellenkező esetben a kapcsolódás nem ellenőrzi a kiszolgáló identitását, és a "személyről-a középső" támadásokra lesz kitéve. A ADO.net-illesztőprogram esetében például ezek a kapcsolatok karakterlánc-paraméterei `Encrypt=True` a `TrustServerCertificate=False`és a.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az adatbázis biztonságossá tételéhez több óvintézkedést is igénybe vehet. Megtervezheti például a biztonságos rendszer létrehozását, a bizalmas adategységek titkosítását, és tűzfalat hozhat létre az adatbázis-kiszolgálók köré. Azonban olyan helyzetekben, ahol a fizikai adathordozót (például meghajtókat vagy biztonsági mentési szalagokat) ellopják, a rosszindulatú felek egyszerűen visszaállíthatják vagy csatolhatják az adatbázist, és böngészhetik az adatok között.

Az egyik megoldás az, hogy Titkosítsa a bizalmas adatokat az adatbázisban, és megóvja az adatok tanúsítvánnyal való titkosításához használt kulcsokat. Ez a megoldás megakadályozza, hogy a kulcsok ne használják az adatok használatát, de az ilyen típusú védelmet meg kell tervezni.

A probléma megoldásához SQL Server és SQL Database támogatja az [transzparens](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017)adattitkosítást. Az transzparens adattitkosítás titkosítja SQL Server és SQL Database adatfájlokat, más néven titkosítási adatok.

Az transzparens adattitkosítás segít megvédeni a kártékony tevékenységek fenyegetését. Az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre, anélkül, hogy az alkalmazás módosítására lenne szükség.  

Az transzparens adattitkosítás titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. SQL Database az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden SQL Database-kiszolgáló esetében egyedi.

Ha egy adatbázis egy geo-DR kapcsolatban van, az egyes kiszolgálókon egy másik kulccsal védett. Ha két adatbázis csatlakozik ugyanahhoz a kiszolgálóhoz, akkor ugyanazt a beépített tanúsítványt használják. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. 

További információ: [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Használatban lévő titkosítás (ügyfél)

A legtöbb adatvesztés magában foglalja a kritikus adatok, például a hitelkártya-számok vagy a személyazonosításra alkalmas adatok ellopását. Az adatbázisok bizalmas információk troves lehetnek. Magukban foglalhatják az ügyfelek személyes adatait (például a nemzeti azonosító számokat), a bizalmas kompetitív információkat és a szellemi tulajdont. Az elveszett vagy ellopott adatszolgáltatások, különösen az ügyféladatok, a márka sérülését, a verseny hátrányait és a súlyos pénzbüntetéseket is okozhatják.

![A Always Encrypted zárolásként és kulcsként ábrázolt funkciója](./media/database-security-overview/azure-database-fig1.png)

[Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx) a Azure SQL Database-vagy SQL Server-adatbázisokban tárolt bizalmas adatok védelmére szolgáló szolgáltatás. Always Encrypted lehetővé teszi az ügyfelek számára a bizalmas adatok titkosítását az ügyfélalkalmazások belsejében, és soha ne fedje fel a titkosítási kulcsokat az adatbázismotor számára (SQL Database vagy SQL Server).

A Always Encrypted elkülöníti az adattulajdonost (és megtekintheti) és az adatkezelést végző személyeket (de nem rendelkezik hozzáféréssel). Segít biztosítani, hogy a helyszíni adatbázis-rendszergazdák, a felhőalapú adatbázis-kezelők vagy más magas jogosultságú, de jogosulatlan felhasználók ne férhessenek hozzá a titkosított információhoz.

Emellett a Always Encrypted az alkalmazások számára transzparensvé teszi a titkosítást. Egy Always Encrypted-kompatibilis illesztőprogram van telepítve az ügyfélszámítógépen, így az képes automatikusan titkosítani és visszafejteni a bizalmas adatokat az ügyfélalkalmazás számára. Az illesztőprogram titkosítja a bizalmas oszlopokban lévő adatokat, mielőtt átadná az adatokat az adatbázismotor számára. Az illesztőprogram automatikusan újraírja a lekérdezéseket, így az alkalmazás szemantikai megmaradnak. Hasonlóképpen, az illesztőprogram transzparens módon visszafejti a titkosított adatbázis-oszlopokban tárolt és a lekérdezési eredményekben található adatmennyiséget.

## <a name="access-control"></a>Hozzáférés-vezérlés

A biztonság biztosításához SQL Database szabályozza a hozzáférést a következő használatával:

- Tűzfalszabályok, amelyek IP-cím alapján korlátozzák a kapcsolatot.
- Hitelesítési mechanizmusok, amelyeknek a felhasználóknak igazolniuk kell identitásukat.
- Engedélyezési mechanizmusok, amelyek korlátozzák a felhasználókat az adott műveletekre és adatszolgáltatásokra.

### <a name="database-access"></a>Adatbázis-hozzáférés

Az adatvédelem az adataihoz való hozzáférés szabályozásával kezdődik. Az adatközpont, amely az adatait tárolja, fizikai hozzáférést kezel. A hálózati réteg biztonságának kezeléséhez beállíthat egy tűzfalat. A hozzáférést úgy is szabályozhatja, hogy konfigurálja a bejelentkezéseket a hitelesítéshez, és meghatározza a kiszolgáló-és adatbázis-szerepkörökre vonatkozó engedélyeket.

#### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) az Azure-hoz és más internetalapú alkalmazásokhoz is biztosít egy, a kapcsolatot biztosító adatbázis-szolgáltatást. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](/azure/sql-database/sql-database-firewall-configure).

A Azure SQL Database szolgáltatás csak a 1433-es TCP-porton keresztül érhető el. Ha egy SQL-adatbázist szeretne elérni a számítógépről, győződjön meg arról, hogy az ügyfélszámítógép tűzfala engedélyezi a kimenő TCP-kommunikációt a 1433-es TCP-porton. Ha más alkalmazásokhoz nem szükségesek bejövő kapcsolatok, tiltsa le őket a 1433-as TCP-porton.

#### <a name="authentication"></a>Authentication

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Database két hitelesítési típust támogat:

-   **SQL Server hitelesítés**: Egyetlen bejelentkezési fiók jön létre a logikai SQL-példány létrehozásakor, az úgynevezett SQL Database-előfizetői fiók. Ez a fiók [SQL Server hitelesítés](/azure/sql-database/sql-database-security-overview) (Felhasználónév és jelszó) használatával csatlakozik. Ez a fiók rendszergazdának számít a logikai kiszolgáló példányán és a példányhoz csatolt összes felhasználói adatbázisban. Az előfizetői fiók engedélyei nem korlátozhatók. Ilyen fiókból csak egy létezhet.
-   **Azure Active Directory hitelesítés**: Az [Azure ad-hitelesítés](/azure/sql-database/sql-database-aad-authentication) olyan mechanizmus, amellyel az Azure ad-ben identitások használatával kapcsolódhat Azure SQL Databasehoz és Azure SQL Data Warehousehoz. A segítségével központilag kezelheti az adatbázis-felhasználók identitásait.

![Azure AD-hitelesítés SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Az Azure AD-hitelesítés előnyei a következők:
  - Alternatívát biztosít a SQL Server hitelesítéshez.
  - Segít leállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálókon, és lehetővé teszi a jelszó elforgatását egyetlen helyen.
  - Az adatbázis-engedélyeket külső (Azure AD-) csoportok használatával is kezelheti.
  - Az integrált Windows-hitelesítés és az Azure AD által támogatott egyéb hitelesítési formák engedélyezésével megtörölheti a jelszavak tárolását.

#### <a name="authorization"></a>Authorization

Az [Engedélyezés](/azure/sql-database/sql-database-manage-logins) arra utal, hogy a felhasználók mit tehetnek egy Azure SQL Database-adatbázison belül. Ezt a felhasználói fiók adatbázis- [szerepköri tagsága](https://msdn.microsoft.com/library/ms189121) és az [objektum szintű engedélyek](https://msdn.microsoft.com/library/ms191291.aspx)vezérlik. Az engedélyezés az a folyamat, amely meghatározza, hogy mely biztonságos erőforrások férhetnek hozzá a rendszerbiztonsági tagokhoz, és hogy mely műveletek engedélyezettek az adott erőforrásokhoz.

### <a name="application-access"></a>Alkalmazás-hozzáférés

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

A Call Center egyik szolgáltatása a hívók számára a társadalombiztosítási szám vagy a hitelkártya számának több számjegye alapján is azonosítható. Ezek az adatelemek azonban nem lehetnek teljes mértékben kitéve a szolgáltatás képviselőjének.

Meghatározhatja azt a maszkolási szabályt, amely az összes, de az utolsó négy számjegyet egy adott társadalombiztosítási szám vagy hitelkártyaszám esetében az összes lekérdezés eredmény-készletében elvégezte.

![Az SQL Database és az üzleti alkalmazások között eljuttatott szám maszkolása](./media/database-security-overview/azure-database-fig3.png)

Egy másik példaként egy megfelelő adatmaszkot is meghatározhat a személyes azonosításra alkalmas adatok védelme érdekében. A fejlesztő a megfelelőségi előírások megsértése nélkül lekérdezheti a termelési környezeteket a hibaelhárítási célból.

[Az SQL Database dinamikus adatmaszkolása](/azure/sql-database/sql-database-dynamic-data-masking-get-started) úgy korlátozza a bizalmas adatok közzétételét, hogy maszkolja azokat a nem kiemelt jogosultságú felhasználók számára. A dinamikus adatmaszkolás a Azure SQL Database V12-es verziója esetén támogatott.

A [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) segít megakadályozni a bizalmas adatok jogosulatlan elérését azáltal, hogy kijelöli, hogy a bizalmas adatok mekkora részét mutatják ki az alkalmazási rétegre gyakorolt minimális hatás. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

> [!Note]
> A dinamikus adatmaszkolást az Azure adatbázis-rendszergazda, a kiszolgálói rendszergazda vagy a biztonsági igazgató szerepkör konfigurálhatja.

#### <a name="row-level-security"></a>Sorszintű biztonság

A több-bérlős adatbázisok esetében egy másik gyakori biztonsági követelmény a [sor szintű biztonság](https://msdn.microsoft.com/library/dn765131.aspx). Ezzel a funkcióval szabályozhatja az adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján. (Példa a csoporttagság és a végrehajtási környezet jellemzőire.)

![Sor szintű biztonság, amely lehetővé teszi a felhasználók számára, hogy egy ügyfél-alkalmazáson keresztül hozzáférjenek a táblázat soraihoz](./media/database-security-overview/azure-database-fig4.png)

A hozzáférés-korlátozási logika az adatbázis-szinten található, nem pedig egy másik alkalmazási szinten lévő adatoktól. Az adatbázisrendszer minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor az adathozzáférés bármely szintről megkísérelhető. Így megbízhatóbb és robusztus biztonsági rendszere lesz a biztonsági rendszerek felületének csökkentése.

A sor szintű biztonság bevezeti a predikátum-alapú hozzáférés-vezérlést. Egy rugalmas, központosított értékelést tartalmaz, amely figyelembe veheti a metaadatokat, vagy bármely más olyan feltételt, amelyet a rendszergazda adott esetben meghatároz. A predikátum feltételként használható annak megállapításához, hogy a felhasználó rendelkezik-e megfelelő hozzáféréssel az adatbázishoz a felhasználói attribútumok alapján. A címkéken alapuló hozzáférés-vezérlést predikátum-alapú hozzáférés-vezérléssel is megvalósíthatja.

## <a name="proactive-monitoring"></a>Proaktív figyelés

A SQL Database a naplózási és veszélyforrás - *észlelési* képességek biztosításával segíti az adatai védelmét.

### <a name="auditing"></a>Naplózás

[Azure SQL Database naplózás](/azure/sql-database/sql-database-auditing-get-started) növeli az adatbázison belül bekövetkező események és változások megismerésének képességét. Ilyenek például az információk frissítései és lekérdezései.

SQL Database naplózás nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenység megértésében, valamint az üzleti problémákat vagy a biztonsági szabálysértések gyanúját jelző eltérések és rendellenességek megismerésében. A naplózás lehetővé teszi a megfelelőségi szabványok betartását, de nem garantálja a megfelelőséget.

SQL Database naplózást a következőre használhatja:

- A kiválasztott események naplózási nyomvonalának **megőrzése** . Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis-tevékenységről. Az előre konfigurált jelentések és az irányítópultok segítségével gyorsan megkezdheti a tevékenységek és az események jelentéskészítését.
- Jelentések **elemzése** . Megtalálhatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

Két naplózási módszer létezik:

-   **Blob naplózása**: A naplókat a rendszer az Azure Blob Storage-ba írja. Ez egy újabb naplózási módszer. Magasabb szintű teljesítményt nyújt, támogatja a magasabb részletességű objektumosztály-naplózást, és költséghatékony.
-   **Tábla naplózása**: A naplók az Azure Table Storage-ba íródnak.

### <a name="threat-detection"></a>Fenyegetések észlelése

[A Azure SQL Database komplex veszélyforrások elleni védelme](/azure/sql-database/sql-advanced-threat-protection) észleli azokat a gyanús tevékenységeket, amelyek potenciális biztonsági fenyegetéseket jeleznek. A veszélyforrások észlelésével válaszolhat az adatbázis gyanús eseményeire, például az SQL-injektálásokra. Riasztásokat biztosít, és lehetővé teszi Azure SQL Database naplózás használatát a gyanús események megismeréséhez.

![Fenyegetések észlelése SQL Database és webalkalmazások számára külső támadóval és rosszindulatú bennfentes](./media/database-security-overview/azure-database-fig5.jpg)

A SQL Advanced Threat Protection (ATP) fejlett SQL biztonsági képességeket biztosít, beleértve az adatfelderítési & besorolást, a sebezhetőségek felmérését és a fenyegetések észlelését. 

- [Adatfelderítési & besorolása](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Sebezhetőségi felmérés](/azure/sql-database/sql-vulnerability-assessment)  
- [Fenyegetések észlelése](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL komplex veszélyforrások elleni védelem](/azure/postgresql/concepts-data-access-and-security-threat-protection) új biztonsági réteget biztosít, amely lehetővé teszi, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelje és reagáljon a lehetséges fenyegetésekre. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, valamint a lehetséges sebezhetőségekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A Azure Database for PostgreSQL komplex veszélyforrások elleni védelme a riasztásokat Azure Security Centerokkal integrálja. A riasztások típusa többek között:

- Hozzáférés szokatlan helyről
- Hozzáférés szokatlan Azure-adatközpontból 
- Hozzáférés az ismeretlen résztvevőtől 
- Hozzáférés egy potenciálisan ártalmas alkalmazáshoz 
- Találgatásos Azure Database for PostgreSQL hitelesítő adatok 

[Azure Database for MySQL](/azure/mysql/concepts-data-access-and-security-threat-protection) a komplex veszélyforrások elleni védelem a PostgreSQL speciális védelméhez hasonló védelmet nyújt.  

## <a name="centralized-security-management"></a>Központosított biztonsági felügyelet

[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. Segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

A [Security Center](../../security-center/security-center-alerts-data-services.md) a SQL Database az adatvédelmet segíti az összes kiszolgáló és adatbázis biztonságának biztosításával. A Security Center a következőket teheti:

- Szabályzatok meghatározása a SQL Database titkosításhoz és a naplózáshoz.
- SQL Database erőforrások biztonságának figyelése az összes előfizetésében.
- Gyorsan azonosíthatja és elháríthatja a biztonsági problémákat.
- Riasztások integrálása [Azure SQL Database veszélyforrások észlelésével](/azure/sql-database/sql-database-threat-detection).

A Security Center támogatja a szerepköralapú hozzáférést.

## <a name="sql-information-protection"></a>SQL Information Protection

Az [SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automatikusan felkeresi és osztályozza a potenciálisan bizalmas adatokat, címkéző mechanizmust biztosít a bizalmas adatok állandó címkézéséhez a besorolási attribútumokkal, és részletes irányítópultot jelenít meg a az adatbázis besorolási állapota.  

Emellett kiszámítja az SQL-lekérdezések eredmény-beállítását is, így a bizalmas adatokat kinyerő lekérdezések explicit módon naplózhatók, és az adatok védetté is menthetők. Az SQL Information Protectionről további részleteket a Azure SQL Database adatfelderítés és besorolás című témakörben talál.

Az [SQL Information Protection házirendeket](/azure/security-center/security-center-info-protection-policy) Azure Security Center-ben is konfigurálhatja.

## <a name="azure-marketplace"></a>Azure Marketplace

Az Azure Piactér olyan online alkalmazás- és szolgáltatáspiactér, amelyen az induló vállalkozások és a független szoftverszállítók kínálhatják megoldásaikat az Azure-szolgáltatásokat világszerte használó ügyfeleknek.
Az Azure piactér Microsoft Azure partneri ökoszisztémákat egyesít egy egységes platformra, így jobban kiszolgálhatja az ügyfeleket és a partnereket. A [Keresés futtatásával](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) megtekintheti az Azure piactéren elérhető adatbázis-biztonsági termékeket.

## <a name="next-steps"></a>További lépések

- [Az Azure SQL Database védelme](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center és Azure SQL Database szolgáltatás](/azure/security-center/security-center-sql-database)
- [SQL Database fenyegetések észlelése](/azure/sql-database/sql-database-threat-detection)
- [Az SQL Database teljesítményének javítása](/azure/sql-database/sql-database-performance-tutorial)
