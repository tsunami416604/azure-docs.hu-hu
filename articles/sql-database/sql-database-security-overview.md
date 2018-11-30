---
title: Az Azure SQL Database biztonsági áttekintése | Microsoft Docs
description: Ismerje meg az Azure SQL Database és az SQL Server biztonságát, beleértve a felhőalapú és helyszíni SQL Server közötti különbségeket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 5bb3dc0245371248b005d642debb5b60026b9f4c
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635475"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database biztonsági lehetőségeinek áttekintése

Ez a cikk bemutatja az Azure SQL Database-t használó alkalmazások adatrétegének védelméhez szükséges alapszintű információkat. Ez a cikk elsősorban, az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások használatának beolvasása.

Az SQL minden fajtájában elérhető biztonsági funkciók teljes körű áttekintése: [Az SQL Server-adatbázismotor és az Azure SQL Database biztonsági központja](https://msdn.microsoft.com/library/bb510589). Emellett [A biztonság és az Azure SQL Database című műszaki tanulmányban](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) is talál további információt.

## <a name="protect-data"></a>Adatok védelme

### <a name="encryption"></a>Titkosítás

Az SQL Database a mozgásban lévő adatokat a [Transport Layer Security](https://support.microsoft.com/kb/3135244) protokoll, az inaktív adatokat [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), a használatban lévő adatokat pedig az [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) protokoll használatával titkosítja az adatok védelme érdekében.

> [!IMPORTANT]
> Az Azure SQL Database biztonsága ne sérüljön titkosítás (SSL/TLS) minden esetben minden kapcsolatot, amely biztosítja, hogy az összes adat titkosítva van "átvitel" az adatbázis és az ügyfél között. Ez történik, függetlenül a beállítás a **titkosítása** vagy **TrustServerCertificate** a kapcsolati karakterláncban.
>
> Az alkalmazás kapcsolati karakterláncában, győződjön meg arról, hogy a megadott titkosított kapcsolatot és *nem* megbízható a tanúsítvány (Ez az ADO.NET-illesztőprogram **Encrypt = True** és  **TrustServerCertificate = False**). Ez segít meggátolni egy férfiról, aki a középső támadás esetén az alkalmazás által az alkalmazás ellenőrizze a kiszolgáló és a végrehajtó titkosítás. A kapcsolati karakterlánc lekérését az Azure Portalról, ha a helyes beállításokat fog rendelkezni.
>
> A TLS és a kapcsolat kapcsolatos információkért lásd: [TLS kapcsolatos szempontok](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

- A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-  Ha egy hardveres biztonsági modulra vagy a Bring Your Own Key (BYOK) technológia transzparens adattitkosítási van szüksége, fontolja meg [Azure SQL transzparens adattitkosítás: Bring Your Own Key-támogatás](transparent-data-encryption-byok-azure-sql.md).

### <a name="data-discovery--classification"></a>Adatfelderítés és besorolás

Adatfelderítés és besorolás (jelenleg előzetes verzióban érhető el) biztosít a felderítése, besorolása, címkézése és védelme a az adatbázisokban a bizalmas adatokat az Azure SQL Database-be épített fejlettebb képességek. Felderítése és besorolása a lehető legnagyobb mértékben bizalmas adatok (üzleti/pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is kulcsfontosságú szerepet játszanak az Information protection szervezeti helyzetében. Az infrastruktúra szolgálhasson:

- Különféle biztonsági forgatókönyvek, például a (naplózás) figyelési és riasztási a bizalmas adatokhoz való rendellenes hozzáférést.
- Való hozzáférés szabályozásának és korlátozására, biztonságát a szigorúan bizalmas adatokat tartalmazó adatbázisok.
- Lehetővé teszi, hogy megfeleljen az adatok adatvédelmi szabványok és az előírt megfelelőségi követelmények teljesítését.

További információkért lásd: [Ismerkedés az SQL DB Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md).

## <a name="control-access"></a>Vezérlési hozzáférés

Az SQL Database úgy biztosítja az adatai védelmét, hogy korlátozza az adatbázishoz való hozzáférést tűzfalszabályokkal, a felhasználókat az azonosságuk igazolására kényszerítő hitelesítési mechanizmusokkal, engedélyezéssel az adatokhoz szerepalapú tagságok és engedélyek útján, valamint sorszintű biztonsággal és dinamikus adatmaszkolással. Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.

> [!IMPORTANT]
> Az adatbázisok és logikai kiszolgálók az Azure-ban való kezelését a portál felhasználói fiókjának szerepkör-hozzárendelése szabályozza. Ez a cikk további információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/overview.md). Hozzáférés-tűzfalszabályokkal does *nem* a alkalmazni **Azure SQL Database felügyelt példányába**. További információt a következő [, a felügyelt példányhoz kapcsolódva](sql-database-managed-instance-connect-app.md) bővebben a hálózati konfiguráció szükséges.

### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok

Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja [tűzfalszabályokkal](sql-database-firewall-configure.md), hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

### <a name="authentication"></a>Hitelesítés

Az SQL Database-hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:

- **SQL-hitelesítés**

  Ezt a hitelesítési módszert használ, egy felhasználónevet és jelszót. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”).

- **Az Azure Active Directory-hitelesítés**

  Ez a hitelesítési módszer az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok támogatott. [Amikor csak lehet](https://msdn.microsoft.com/library/ms144284.aspx), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

### <a name="authorization"></a>Engedélyezés

Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis szerepkörtagságai és objektumszintű engedélyei határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

### <a name="row-level-security"></a>Sorszintű biztonság

Sorszintű biztonság lehetővé teszi, hogy az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz (például csoporttagság vagy végrehajtási környezet) lekérdezést végrehajtó felhasználó jellemzői alapján való hozzáférést. További információkat a [sorszintű biztonsággal kapcsolatos](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) részben találhat.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás

Az SQL Database dinamikus adatmaszkolása korlátozza a bizalmas adatok adatmaszkolás segít Önnek nem kiemelt jogosultságú felhasználók által. Dinamikus adatmaszkolás automatikusan észleli a potenciálisan bizalmas adatokat az Azure SQL Database és az alkalmazásrétegre gyakorolt minimális hatás mellett, ezen mezők maszkolására úgy végrehajtható javaslatokat tesz. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információkért lásd: [Ismerkedés az SQL Database dinamikus adatmaszkolása](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Proaktív figyelés

Az SQL Database naplózási és fenyegetésészlelési képességek biztosításával védi az adatokat.

### <a name="auditing"></a>Naplózás

Az SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit, és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található, ezáltal segíti az előírásoknak való megfelelőség fenntartását. A naplózás révén könnyebben átláthatja az adatbázisban folyamatban lévő tevékenységeket, illetve a lehetséges fenyegetések, esetleges visszaélések és biztonságmegsértések azonosítása céljából elemezheti és kivizsgálhatja a tevékenységelőzményeket. További információk: [Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing.md).  

### <a name="threat-detection"></a>Fenyegetések észlelése

A Fenyegetésészlelés egészíti ki a naplózást egy további, amely észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket az Azure SQL Database szolgáltatásba épített biztonsági információs réteget biztosít. Gyanús tevékenységek, a lehetséges biztonsági résekről, figyelmeztetést, és az SQL-injektálási támadások, valamint a rendellenes adatbázis-hozzáférési mintákról. Fenyegetésészlelési riasztások tekinthetők [az Azure Security Center](https://azure.microsoft.com/services/security-center/) , és adja meg a gyanús tevékenység részleteit, és tegyen javaslatot a műveletre vizsgálata, valamint a fenyegetés. A fenyegetésészlelés $15/kiszolgálói/hó. Fontos ingyenes az első 60 nap. További információk: [Ismerkedés az SQL Database fenyegetések észlelése szolgáltatásával](sql-database-threat-detection.md).

## <a name="compliance"></a>Megfelelőség

Mellett a fent említett szolgáltatások és funkciók, amelyek segíthetnek az alkalmazás is megfelelnek a különböző biztonsági követelmények, Azure SQL Database rendszeres vizsgálatokon részt vesz, és a egy több megfelelőségi szabvány tanúsított. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://www.microsoft.com/trustcenter/compliance/complianceofferings) aktuális listájával együtt.

## <a name="security-management"></a>Biztonság kezelése

SQL Database segít az adatok biztonságának kezelése azáltal, hogy adatbázis a vizsgálatokból és a egy központi irányítópulton a [SQL-sebezhetőségi felmérés](sql-vulnerability-assessment.md).

**[SQL-sebezhetőségi felmérés](sql-vulnerability-assessment.md)**  egy egyszerű, amelyek segítségével felderítése, nyomon követheti és szervizelheti az adatbázis biztonsági réseinek Azure SQL Database-be épített eszköz konfigurálása. Az értékelés hajt végre egy biztonsági rések keresése az adatbázisban, és létrehoz egy jelentést, amely a biztonsági állapot, beleértve a gyakorlatban is használható, a biztonsági problémák megoldása és az adatbázis-biztonság betekintést nyújt. Az értékelési jelentés egy elfogadható referenciakonfigurációjához tartozó engedély konfigurációk, a konfigurációk és az adatbázis-beállítások megadásával testre szabható a környezet. Ez segíthet, hogy:

- Adatbázis a vizsgálati jelentések igénylő megfelelőségi követelmények teljesítése érdekében.
- Adatok adatvédelmi követelményeknek.
- A változások követését amelyeknél dinamikus adatbázis környezet figyelésére.

További információkért lásd: [SQL-sebezhetőségi felmérés](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>További lépések

- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Az adatbázis naplózási szolgáltatásával, lásd: [SQL Database naplózási funkciójának](sql-database-auditing.md).
- A fenyegetésészlelés, lásd: [SQL Database fenyegetésészlelési](sql-database-threat-detection.md).
