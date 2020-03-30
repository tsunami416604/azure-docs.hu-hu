---
title: Az Azure-adatbázis biztonságának áttekintése| Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure-adatbázis biztonsági funkcióiról.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906061"
---
# <a name="azure-database-security-overview"></a>Az Azure-adatbázis biztonságának áttekintése

A biztonság az adatbázisok kezelésének egyik legfontosabb szempontja, és mindig is prioritást élvezett az Azure SQL Database számára. Az Azure SQL Database tűzfalszabályokkal és kapcsolattitkosítással támogatja a kapcsolat biztonságát. Támogatja a hitelesítést felhasználónévvel és jelszóval, valamint az Azure Active Directory (Azure AD) hitelesítés, amely az Azure Active Directory által kezelt identitások használatával. Az engedélyezés szerepköralapú hozzáférés-vezérlést használ.

Az Azure SQL Database támogatja a titkosítást azáltal, hogy valós idejű titkosítást és visszafejtést az adatbázisok, a kapcsolódó biztonsági mentések és a tranzakciós naplófájlok inaktív anélkül, hogy az alkalmazás módosítása.

A Microsoft további lehetőségeket kínál a vállalati adatok titkosítására:

-   Cellszintű titkosítás érhető el bizonyos oszlopok vagy akár különböző titkosítási kulcsokkal rendelkező adatcellák titkosításához.
-   Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi felügyeletére van szüksége, fontolja meg az Azure Key Vault és az SQL Server használatát egy Azure virtuális gépen (VM).
-   Mindig titkosított (jelenleg előzetes verzióban) teszi a titkosítást átláthatóvá az alkalmazások számára. Azt is lehetővé teszi az ügyfelek számára, hogy titkosítsák a bizalmas adatokat az ügyfélalkalmazásokon belül anélkül, hogy megosztanák a titkosítási kulcsokat az SQL Database-lel.

Az Azure SQL Database Auditing lehetővé teszi a vállalatok számára, hogy eseményeket rögzítsenek az Azure Storage naplójába. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést.

Az Azure SQL-adatbázisok szorosan biztosíthatók a legtöbb szabályozási vagy biztonsági követelmény nek való megfelelés érdekében, beleértve a HIPAA-t, az ISO 27001/27002-t és a PCI DSS 1-es szintet. A biztonsági megfelelőségi tanúsítványok aktuális listája a [Microsoft Azure Adatvédelmi központ webhelyén](https://azure.microsoft.com/support/trust-center/services/)érhető el.

Ez a cikk bemutatja a Microsoft Azure SQL-adatbázisok strukturált, táblázatos és relációs adatokhoz való védelmének alapjait. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is.

## <a name="protection-of-data"></a>Az adatok védelme

Az SQL Database titkosítással védi az adatokat:

- A [Transport Layer Security (TLS) (TLS) (TLS)](https://support.microsoft.com/kb/3135244)kapcsolatban álló adatokhoz.
- Az [adatok intikátkulás átlátszó adattitkosítás](https://go.microsoft.com/fwlink/?LinkId=526242).
- A [mindig titkosított](https://msdn.microsoft.com/library/mt163865.aspx)módon használt adatokhoz.

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   [Az Azure Key Vault az SQL Server egy Azure virtuális gép,](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)ha szüksége van egy hardveres biztonsági modul vagy a titkosítási kulcs hierarchia központi felügyeletét.

### <a name="encryption-in-motion"></a>Titkosítás mozgásban

Az összes ügyfél-kiszolgáló alkalmazás esetében gyakori probléma, hogy adatvédelemre van szükség, mivel az adatok a nyilvános és a magánhálózatokon keresztül mozognak. Ha a hálózaton keresztül mozgó adatok nincsenek titkosítva, fennáll annak az esélye, hogy illetéktelen felhasználók rögzíthetik és ellophatják őket. Adatbázis-szolgáltatások kezelése során győződjön meg arról, hogy az adatok titkosítva vannak az adatbázis-ügyfél és a kiszolgáló között. Győződjön meg arról is, hogy az adatok titkosítva vannak az egymással és a középső rétegbeli alkalmazásokkal kommunikáló adatbázis-kiszolgálók között.

A hálózat felügyelete során az egyik probléma a nem megbízható hálózaton keresztül az alkalmazások között küldött adatok védelme. A [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) segítségével hitelesítheti a kiszolgálókat és az ügyfeleket, majd titkosíthatja a hitelesített felek közötti üzeneteket.

A hitelesítési folyamat során a TLS/SSL-ügyfél üzenetet küld egy TLS/SSL-kiszolgálónak. A kiszolgáló azokkal az információkkal válaszol, amelyeket a kiszolgálónak saját maga hitelesítésére kell tudnia. Az ügyfél és a kiszolgáló emellett végrehajtja a munkamenetkulcsok cseréjét, és ezzel véget ér a hitelesítési párbeszéd. A hitelesítés befejezéseután a kiszolgáló és az ügyfél között ssl-védelem alatt álló kommunikáció kezdődhet a hitelesítési folyamat során létrehozott szimmetrikus titkosítási kulcsokon keresztül.

Az Azure SQL Database-hez való összes kapcsolat titkosítást (TLS/SSL) igényel mindenkor, amíg az adatok "átvitel alatt" vannak az adatbázisba és az adatbázisból. Az SQL Database TLS/SSL használatával hitelesíti a kiszolgálókat és az ügyfeleket, majd a hitelesített felek közötti üzenetek titkosítására használja. 

Az alkalmazás kapcsolati karakterláncában meg kell adnia paramétereket a kapcsolat titkosításához, és nem kell megbíznia a kiszolgálótanúsítványban. (Ez akkor történik meg, ha a kapcsolati karakterláncot kimásolja az Azure Portalról.) Ellenkező esetben a kapcsolat nem ellenőrzi a kiszolgáló identitását, és ki lesz téve a "közbeékelődéses" támadásoknak. A ADO.NET illesztőprogram esetében például ezek a `Encrypt=True` `TrustServerCertificate=False`kapcsolati karakterlánc-paraméterek a és .

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az adatbázis védelme érdekében számos óvintézkedést tehet. Például tervezzen egy biztonságos rendszert, titkosítsa a bizalmas eszközöket, és hozzon létre tűzfalat az adatbázis-kiszolgálók köré. De egy olyan esetben, amikor a fizikai adathordozók (például meghajtók vagy biztonsági másolatkazetták) ellopják, egy rosszindulatú fél csak visszaállítani vagy csatolni az adatbázist, és böngészhet az adatokközött.

Az egyik megoldás az adatbázis ban lévő bizalmas adatok titkosítása és az adatok tanúsítványsal történő titkosításához használt kulcsok védelme. Ez a megoldás megakadályozza, hogy a kulcsok nélkül bárki használja az adatokat, de ezt a fajta védelmet meg kell tervezni.

A probléma megoldásához az SQL Server és az SQL Database támogatja az [átlátszó adattitkosítást.](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017) Az átlátszó adattitkosítás titkosítja az SQL Server és az SQL Database adatfájljait, más néven titkosítási adatokat.

Az átlátható adattitkosítás segít a rosszindulatú tevékenységek fenyegetése elleni védelemben. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást.  

Az átlátszó adattitkosítás titkosítja a teljes adatbázis tárolását egy szimmetrikus kulcs, az adatbázis titkosítási kulcsa használatával. Az SQL Database rendszerben az adatbázis-titkosítási kulcsot beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden SQL Database-kiszolgáló esetében egyedi.

Ha egy adatbázis geo-DR kapcsolatban van, azt minden kiszolgálón más-más kulcs védi. Ha két adatbázis ugyanahhoz a kiszolgálóhoz csatlakozik, ugyanaz a beépített tanúsítványuk. A Microsoft legalább 90 naponta automatikusan elforgatja ezeket a tanúsítványokat. 

További információ: [Transparent data encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Használatban lévő titkosítás (ügyfél)

A legtöbb adatvédelmi incidens kritikus adatok, például hitelkártyaszámok vagy személyazonosításra alkalmas adatok ellopásával jár. Az adatbázisok érzékeny információk kincsestárai lehetnek. Tartalmazhatják az ügyfelek személyes adatait (például a nemzeti azonosító számokat), a bizalmas versenyadatokat és a szellemi tulajdont. Az elveszett vagy ellopott adatok, különösen az ügyféladatok, márkakárt, versenyhátrányt és súlyos bírságokat eredményezhetnek - még a pereket is.

![A Mindig titkosított funkció zárolásként és kulcsként illusztrálva](./media/database-security-overview/azure-database-fig1.png)

[A Mindig titkosítva](https://msdn.microsoft.com/library/mt163865.aspx) egy olyan szolgáltatás, amely az Azure SQL Database vagy az SQL Server-adatbázisokban tárolt bizalmas adatok védelmére szolgál. A Mindig titkosított lehetővé teszi az ügyfelek számára, hogy titkosítsák a bizalmas adatokat az ügyfélalkalmazásokon belül, és soha ne fedjék fel az adatbázismotor (SQL Database vagy SQL Server) titkosítási kulcsait.

A Mindig titkosított érték elkülöníti azokat a személyeket, akik az adatokat birtokolják (és megtekinthetik) és azokat, akik kezelik az adatokat (de nem férhetnek hozzá). Ez segít biztosítani, hogy a helyszíni adatbázis-rendszergazdák, felhőalapú adatbázis-operátorok, vagy más magas jogosultsági szintű, de jogosulatlan felhasználók nem férhetnek hozzá a titkosított adatokhoz.

Ezenkívül a Mindig titkosított titkosítás tetszetőssé teszi az alkalmazások számára. A mindig titkosított illesztőprogram telepítve van az ügyfélszámítógépen, így automatikusan titkosíthatja és visszatudja fejteni a bizalmas adatokat az ügyfélalkalmazásban. Az illesztőprogram titkosítja az adatokat a bizalmas oszlopokban, mielőtt az adatokat az adatbázis-motorba adná. Az illesztőprogram automatikusan újraírja a lekérdezéseket, hogy az alkalmazás szemantikája megmaradjon. Hasonlóképpen az illesztőprogram transzparens módon visszafejti a titkosított adatbázisoszlopokban tárolt adatokat, amelyek a lekérdezési eredményekben találhatók.

## <a name="access-control"></a>Hozzáférés-vezérlés

A biztonság érdekében az SQL Database a következő kontőket használja:

- Tűzfalszabályok, amelyek ip-cím szerint korlátozzák a kapcsolatot.
- Hitelesítési mechanizmusok, amelyek megkövetelik a felhasználóktól személyazonosságuk igazolását.
- Engedélyezési mechanizmusok, amelyek a felhasználókat meghatározott műveletekre és adatokra korlátozzák.

### <a name="database-access"></a>Adatbázis-hozzáférés

Az adatvédelem az adatokhoz való hozzáférés szabályozásával kezdődik. Az adatokat tartalmazó adatközpont kezeli a fizikai hozzáférést. A tűzfal at a hálózati réteg biztonságának kezelésére konfigurálhatja. A hozzáférést úgy is szabályozhatja, hogy hitelesítésre konfigurálja a bejelentkezéseket, és meghatározza a kiszolgálói és adatbázis-szerepkörök engedélyeit.

#### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) relációs adatbázis-szolgáltatást biztosít az Azure-hoz és más internetalapú alkalmazásokhoz. Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján. További információt [az Azure SQL Database tűzfalszabályainak áttekintése című témakörben talál.](/azure/sql-database/sql-database-firewall-configure)

Az Azure SQL Database szolgáltatás csak a 1433-as TCP-porton keresztül érhető el. Sql-adatbázis számítógépről való eléréséhez győződjön meg arról, hogy az ügyfélszámítógép tűzfala engedélyezi a kimenő TCP-kommunikációt az 1433-as TCP-porton. Ha más alkalmazásokhoz nincs szükség bejövő kapcsolatokra, tiltsa le őket az 1433-as TCP-porton.

#### <a name="authentication"></a>Hitelesítés

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Database két hitelesítési típust támogat:

-   **SQL Server-hitelesítés**: Egy logikai SQL-példány létrehozásakor egyetlen bejelentkezési fiók jön létre, az SQL Database Subscriber Account néven. Ez a fiók [SQL Server-hitelesítéssel](/azure/sql-database/sql-database-security-overview) (felhasználónévvel és jelszóval) csatlakozik. Ez a fiók rendszergazdának számít a logikai kiszolgáló példányán és a példányhoz csatolt összes felhasználói adatbázisban. Az előfizetői fiók engedélyei nem korlátozhatók. Ilyen fiókból csak egy létezhet.
-   **Azure Active Directory-hitelesítés:** [Az Azure AD-hitelesítés](/azure/sql-database/sql-database-aad-authentication) az Azure SQL Database és az Azure SQL Data Warehouse identitások használatával az Azure AD-hez való csatlakozás mechanizmusa. Segítségével központilag kezelheti az adatbázis-felhasználók identitását.

![Azure AD-hitelesítés SQL-adatbázissal](./media/database-security-overview/azure-database-fig2.png)

 Az Azure AD-hitelesítés előnyei a következők:
  - Az SQL Server-hitelesítés alternatívája.
  - Segít megállítani a felhasználói identitások elterjedését az adatbázis-kiszolgálók között, és lehetővé teszi a jelszó elforgatását egyetlen helyen.
  - Az adatbázis-engedélyeket külső (Azure AD) csoportok használatával kezelheti.
  - Megszüntetheti a jelszavak tárolását az integrált Windows-hitelesítés és az Azure AD által támogatott egyéb hitelesítési formák engedélyezésével.

#### <a name="authorization"></a>Engedélyezés

[Az engedélyezés](/azure/sql-database/sql-database-manage-logins) azt a felhasználót, amit tehet egy Azure SQL-adatbázisban. Ezt a felhasználói fiók [adatbázis-szerepkör-tagsága](https://msdn.microsoft.com/library/ms189121) és [objektumszintű engedélyei szabályozzák.](https://msdn.microsoft.com/library/ms191291.aspx) Az engedélyezés annak meghatározása, hogy egy megbízó mely biztonságos erőforrásokhoz férhet hozzá, és mely műveletek engedélyezettek ezekhez az erőforrásokhoz.

### <a name="application-access"></a>Alkalmazás-hozzáférés

#### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

A telefonos ügyfélszolgálat iképviselőjének a hívókat a társadalombiztosítási számuk vagy a hitelkártyaszámuk több számjegyével azonosíthatja. Ezeket az adatelemeket azonban nem szabad teljes mértékben kitenni a szolgáltatás képviselőjének.

Megadhat egy maszkolási szabályt, amely a társadalombiztosítási szám vagy hitelkártyaszám utolsó négy számjegyét kivételével elfedi bármely lekérdezés eredményhalmazában.

![Sql-adatbázis és üzleti alkalmazások között küldött szám maszkolása](./media/database-security-overview/azure-database-fig3.png)

Egy másik példa, hogy a személyazonosításra alkalmas adatok védelme érdekében megfelelő adatmaszk definiálható. A fejlesztő ezután lekérdezheti az éles környezeteket hibaelhárítási célokra a megfelelőségi előírások megsértése nélkül.

[Az SQL Database dinamikus adatmaszkolása](/azure/sql-database/sql-database-dynamic-data-masking-get-started) korlátozza a bizalmas adatok nak való kitettséget azáltal, hogy nem emelt szintű jogosultsággal rendelkező felhasználók számára maszkolja azt. A dinamikus adatmaszkolás az Azure SQL Database V12-es verziója esetén támogatott.

[A dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) segít megakadályozni a bizalmas adatokhoz való jogosulatlan hozzáférést azáltal, hogy lehetővé teszi annak beállítását, hogy a bizalmas adatok közül mennyi kerüljön nyilvánosságra, minimális hatással az alkalmazásrétegre. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.

> [!Note]
> A dinamikus adatmaszkolást az Azure Database rendszergazdája, a kiszolgáló rendszergazdája vagy a biztonsági rendszergazdai szerepkörök konfigurálhatják.

#### <a name="row-level-security"></a>Sorszintű biztonság

A több-bérlős adatbázisok másik gyakori biztonsági követelménye a [sorszintű biztonság.](https://msdn.microsoft.com/library/dn765131.aspx) Ezzel a szolgáltatással szabályozhatja az adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján. (A példa jellemzői a csoporttagság és a végrehajtási környezet.)

![Sorszintű biztonság, amely lehetővé teszi a felhasználók számára, hogy ügyfélalkalmazáson keresztül hozzáférjenek a tábla soraihoz](./media/database-security-overview/azure-database-fig4.png)

A hozzáférés-korlátozási logika az adatbázisrétegben található, nem pedig egy másik alkalmazásréteg adataitól. Az adatbázis-rendszer minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor bármely rétegről adatelérést kísérelnek meg. Ez megbízhatóbbá és robusztusabbá teszi a biztonsági rendszert azáltal, hogy csökkenti a biztonsági rendszer felületét.

A sorszintű biztonság predikátumalapú hozzáférés-vezérlést vezet be. Rugalmas, központosított kiértékelést tartalmaz, amely figyelembe veheti a metaadatokat vagy bármely más feltételt, amelyet a rendszergazda megfelelőnek ítél meg. Az állítmány kritériumként szolgál annak meghatározásához, hogy a felhasználó rendelkezik-e a felhasználói attribútumokon alapuló megfelelő hozzáféréssel az adatokhoz. A címkealapú hozzáférés-vezérlés predikátus alapú hozzáférés-vezérléssel valósítható meg.

## <a name="proactive-monitoring"></a>Proaktív figyelés

Az SQL Database *naplózási* és *fenyegetésészlelési* lehetőségek biztosításával segíti az adatok biztonságát.

### <a name="auditing"></a>Naplózás

[Az Azure SQL Database naplózása](/azure/sql-database/sql-database-auditing-get-started) növeli a képességét, hogy betekintést nyerjen az adatbázison belüli eseményekbe és változásokba. Ilyenek például az adatokfrissítései és lekérdezései.

Az SQL Database naplózása nyomon követi az adatbázis-eseményeket, és az Azure storage-fiókjában naplóba írja őket. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenységek megértésében, valamint az olyan eltérésekés anomáliák megismerésében, amelyek üzleti problémákra vagy a biztonság feltételezett megsértésére utalhatnak. A naplózás lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, de nem garantálja a megfelelőséget.

Az SQL Database naplózása a következőkre használható:

- **A** kiválasztott események naplózási nyomvonalának megtartása. Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis tevékenységéről. Az előre konfigurált jelentések és irányítópultok segítségével gyorsan megkezdheti a tevékenységekkel és az eseményekkel kapcsolatos jelentéskészítést.
- **Jelentések elemzése.** Azonosíthatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

Két naplózási módszer létezik:

-   **Blob naplózása:** A naplók az Azure Blob storage-ba kerülnek. Ez egy újabb naplózási módszer. Nagyobb teljesítményt nyújt, támogatja a nagyobb részletességű objektumszintű naplózást, és költséghatékonyabb.
-   **Tábla naplózása:** A naplók az Azure Table storage-ba vannak írva.

### <a name="threat-detection"></a>Fenyegetések észlelése

[Az Azure SQL Database komplex veszélyforrások elleni védelem](/azure/sql-database/sql-advanced-threat-protection) észleli a gyanús tevékenységeket, amelyek potenciális biztonsági fenyegetéseket jeleznek. A fenyegetésészlelés segítségével válaszolhat az adatbázisban lévő gyanús eseményekre, például az SQL-injekciókra, amint azok bekövetkeznek. Riasztásokat biztosít, és lehetővé teszi az Azure SQL Database naplózásának használatát a gyanús események feltárásához.

![Fenyegetésészlelés az SQL Database és egy webalkalmazás számára, külső támadóval és rosszindulatú bennfentessel](./media/database-security-overview/azure-database-fig5.jpg)

Az SQL komplex veszélyforrások elleni védelem (ATP) fejlett SQL biztonsági képességeket biztosít, beleértve az adatfelderítést & besorolást, a biztonsági résfelmérést és a fenyegetésészlelést. 

- [Adatfelderítés & besorolása](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Sebezhetőségi felmérés](/azure/sql-database/sql-vulnerability-assessment)  
- [Fenyegetések észlelése](/azure/sql-database/sql-database-threat-detection)

[A PostgreSQL komplex veszélyforrások elleni védelemhez](/azure/postgresql/concepts-data-access-and-security-threat-protection) készült Azure Database egy új biztonsági réteget biztosít, amely lehetővé teszi a potenciális fenyegetések észlelését és az azokra való reagálást a rendellenes tevékenységekre vonatkozó biztonsági riasztások biztosításával. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről és a potenciális biztonsági résekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A PostgreSQL-hez készült Azure Database for Advanced Threat Protection integrálja a riasztásokat az Azure Security Centerrel. A riasztások típusaai a következők:

- Hozzáférés szokatlan helyről
- Hozzáférés a szokatlan Azure-adatközpontból 
- Hozzáférés ismeretlen megbízótól 
- Hozzáférés potenciálisan káros alkalmazásból 
- Brute force Azure-adatbázis a PostgreSQL hitelesítő adataihoz 

[Az Azure Database for MySQL Komplex veszélyforrások elleni védelem](/azure/mysql/concepts-data-access-and-security-threat-protection) a PostgreSQL Advanced Protection-hez hasonló védelmet nyújt.  

## <a name="centralized-security-management"></a>Központosított biztonságkezelés

[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben. Segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

[A Security Center](../../security-center/security-center-alerts-data-services.md) segít az SQL Database adatainak védelmében azáltal, hogy betekintést nyújt az összes kiszolgáló és adatbázis biztonságába. A Biztonsági központ segítségével a következőkre tehet:

- Az SQL Database titkosítására és naplózására vonatkozó házirendek definiálása.
- Az SQL Database-erőforrások biztonságának figyelése az összes előfizetésben.
- A biztonsági problémák gyors azonosítása és kiújítása.
- Az Azure [SQL Database fenyegetésészlelési](/azure/sql-database/sql-database-threat-detection)riasztásainak integrálása.

A Security Center támogatja a szerepköralapú hozzáférést.

## <a name="sql-information-protection"></a>SQL információvédelem

[Az SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automatikusan felderíti és osztályozni a potenciálisan bizalmas adatokat, címkézési mechanizmust biztosít a bizalmas adatok besorolási attribútumokkal való állandó címkézéséhez, és részletes irányítópultot biztosít az adatbázis besorolási állapotáról.  

Emellett kiszámítja az SQL-lekérdezések eredménykészlet-érzékenységét, így a bizalmas adatokat kibontó lekérdezések explicit módon naplózhatók, és az adatok védhetők. Az SQL Information Protection-ről további információt az Azure SQL Database Data Discovery and Classification című témakörben talál.

[Az SQL Information Protection szabályzatok](/azure/security-center/security-center-info-protection-policy) az Azure Security Centerben konfigurálhatók.

## <a name="azure-marketplace"></a>Azure Piactér

Az Azure Marketplace egy online alkalmazások és szolgáltatások piactere, amely lehetővé teszi az induló vállalkozások és a független szoftverszállítók (ISV-k) számára, hogy megoldásaikat az Azure-ügyfelek számára világszerte kínálják.
Az Azure Piactér egyesíti a Microsoft Azure partnerökoszisztémáit egy egységes platformon, hogy jobban kiszolgálja ügyfeleit és partnereit. Az Azure Marketplace-en elérhető adatbázis-biztonsági termékek megtekintéséhez [futtathat keresést.](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)

## <a name="next-steps"></a>További lépések

- [Az Azure SQL-adatbázis védelme](/azure/sql-database/sql-database-security-tutorial)
- [Az Azure Security Center és az Azure SQL Database szolgáltatás](/azure/security-center/security-center-sql-database)
- [SQL Database fenyegetésészlelése](/azure/sql-database/sql-database-threat-detection)
- [Az SQL-adatbázis teljesítményének javítása](/azure/sql-database/sql-database-performance-tutorial)
