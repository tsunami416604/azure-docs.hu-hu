---
title: "Az Azure SQL Database biztonsági áttekintése | Microsoft Docs"
description: "További tudnivalók az Azure SQL Database és SQL Server biztonsági, beleértve a felhőben és helyszíni SQL Server közötti különbségeket."
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/12/2018
ms.author: giladm
ms.openlocfilehash: 1dc34f021fa6482c65cce0e922951ae329987c43
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="securing-your-sql-database"></a>Az SQL Database-adatbázis védelme

Ez a cikk bemutatja az Azure SQL Database-t használó alkalmazások adatrétegének védelméhez szükséges alapszintű információkat. Különösen ez a cikk megismerteti erőforrások adatok védelmét, a proaktív figyeléshez, és hozzáférés szabályozása. 

Az SQL minden fajtájában elérhető biztonsági funkciók teljes körű áttekintése: [Az SQL Server-adatbázismotor és az Azure SQL Database biztonsági központja](https://msdn.microsoft.com/library/bb510589). Emellett [A biztonság és az Azure SQL Database című műszaki tanulmányban](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) is talál további információt.

## <a name="protect-data"></a>Adatok védelme

### <a name="encryption"></a>Titkosítás
SQL-adatbázis, adja meg a mozgásérzékelő – az adatok titkosítása az adatok biztonságossá tételére [Transport Layer Security](https://support.microsoft.com/kb/3135244), az inaktív adatok [átlátható adattitkosítási](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), és az adatok használatban van a [ Mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx). 

> [!IMPORTANT]
>Az Azure SQL Database-hez való kapcsolódáshoz minden esetben titkosítás (SSL/TLS) szükséges, amikor az adatok átvitele folyamatban van az adatbázisból vagy az adatbázisba. Az alkalmazás a kapcsolati karakterláncot, meg kell adnia a kapcsolat titkosítására paraméterek és *nem* kell bíznia a kiszolgáló (ebben az esetben, ha a kapcsolati karakterláncot az Azure-portálon kívül), ellenkező esetben a kapcsolat nem ellenőrzi a kiszolgáló identitásának, és ki vannak téve a "man közel" támadásokkal szemben. Az ADO.NET-illesztő számára például a következők a kapcsolati karakterlánc paraméterei: **Encrypt=True** és **TrustServerCertificate=False**. 

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

* A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
* Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelésére van szüksége, fontolja meg az [Azure Key Vault és az SQL Server együttes használatát egy Azure virtuális gépen](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Felderítési adatok & besorolás
Felderítési adatok & besorolás (jelenleg előzetes verzió) Azure SQL Database beépített felderítésével, zárolásának, címkézés és az adatbázisok a bizalmas adatok védelmének speciális funkciókat biztosít. Felderítésének, illetve a lehető legnagyobb mértékben bizalmas adatok zárolásának (üzleti/pénzügyi, egészségügyi, személyazonosításra alkalmas adatok, stb.) is döntő szerepet játszanak a szervezeti adatokat védelmi stature. Az infrastruktúra lehetnek:

- Különféle biztonsági forgatókönyvek, például a figyelési (naplózás), és riasztást küld rendellenes bizalmas adatokhoz való hozzáférést.
- Való hozzáférés vezérlése, és nagyon érzékeny adatokat tartalmazó adatbázisok biztonsági korlátozására.
- Gondoskodik az adatok adatvédelmi szabványok és az előírásoknak való megfelelés igényeinek megfelelő.

További információkért lásd: [Ismerkedés az SQL DB adatok felderítési & besorolás](sql-database-data-discovery-and-classification.md). 

## <a name="control-access"></a>Vezérlési hozzáférés
Az SQL Database úgy biztosítja az adatai védelmét, hogy korlátozza az adatbázishoz való hozzáférést tűzfalszabályokkal, a felhasználókat az azonosságuk igazolására kényszerítő hitelesítési mechanizmusokkal, engedélyezéssel az adatokhoz szerepalapú tagságok és engedélyek útján, valamint sorszintű biztonsággal és dinamikus adatmaszkolással. Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.

> [!IMPORTANT]
> Az adatbázisok és logikai kiszolgálók az Azure-ban való kezelését a portál felhasználói fiókjának szerepkör-hozzárendelése szabályozza. Ez a cikk további információkért lásd: [Azure portál szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-what-is.md).
>

### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok
Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja [tűzfalszabályokkal](sql-database-firewall-configure.md), hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

### <a name="authentication"></a>Hitelesítés
Az SQL Database-hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:
* A felhasználónévvel és jelszóval végzett **SQL-hitelesítést**. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
* Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják. [Amikor csak lehet](https://msdn.microsoft.com/library/ms144284.aspx), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

### <a name="authorization"></a>Engedélyezés
Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis szerepkörtagságai és objektumszintű engedélyei határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

### <a name="row-level-security"></a>Sorszintű biztonság
A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet). További információkat a [sorszintű biztonsággal kapcsolatos](https://msdn.microsoft.com/library/dn765131) részben találhat.

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás 
SQL-adatbázis dinamikus adatmaszkolási maszkolás a jogosulatlan felhasználók által bizalmas adatok veszélyeztetettségének korlátozása. Dinamikus adatmaszkolási automatikusan felderíti az Azure SQL Database potenciálisan bizalmas adatokat, és ezeket a mezőket, gyakorolt minimális hatás mellett az alkalmazási rétegre a maszkolandó végrehajthatóként ajánlásokat. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információkért lásd: [Ismerkedés az SQL-adatbázis dinamikus adatmaszkolási](sql-database-dynamic-data-masking-get-started.md) korlátozzák a bizalmas adatok felfedésnek használható.

## <a name="proactive-monitoring"></a>Proaktív figyelés
Az SQL Database naplózási és fenyegetésészlelési képességek biztosításával védi az adatokat. 

### <a name="auditing"></a>Naplózás
Az SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit, és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található, ezáltal segíti az előírásoknak való megfelelőség fenntartását. A naplózás révén könnyebben átláthatja az adatbázisban folyamatban lévő tevékenységeket, illetve a lehetséges fenyegetések, esetleges visszaélések és biztonságmegsértések azonosítása céljából elemezheti és kivizsgálhatja a tevékenységelőzményeket. További információk: [Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing.md).  

### <a name="threat-detection"></a>Fenyegetések észlelése
A Fenyegetésészlelés kiegészíti a naplózás esetén az Azure SQL Database szolgáltatás által észlelt szokatlan és potenciálisan káros kísérletek eléréséhez, vagy az adatbázis a biztonsági rések elleni beépített biztonsági eszközintelligencia további réteget megadásával. Figyelmeztetést gyanús tevékenységek, a potenciális biztonsági réseket és a SQL injektálási támadások, valamint rendellenes adatbázis memóriahozzáférési mintáitól. Figyelmeztetések-ból is megtekinthetők [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és a gyanús tevékenység részleteinek megadása, és vizsgálja meg, és a fenyegetések mérséklésére művelet javasolja. A Fenyegetésészlelés $15/kiszolgáló/hó költségek. Szabad az első 60 nap. További információk: [Ismerkedés az SQL Database fenyegetések észlelése szolgáltatásával](sql-database-threat-detection.md).
 
## <a name="compliance"></a>Megfelelőség
Rendszeres ellenőrzéseket részt vesz a fenti szolgáltatásait és funkcióit, amelyekkel az alkalmazás különböző biztonsági követelmények, az Azure SQL Database is teljesítéséhez mellett, és megfelelőségi követelményeket számos elleni hitelesített. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://azure.microsoft.com/support/trust-center/services/) aktuális listájával együtt.


## <a name="security-management"></a>Biztonság kezelése

SQL-adatbázis segítségével az adatok biztonsági azáltal, hogy az adatbázis-vizsgálatok és központosított biztonsági Irányítópult segítségével felügyelheti [SQL biztonsági réseinek értékelése](sql-vulnerability-assessment.md).

**Biztonsági réseinek értékelése**: [SQL biztonsági réseinek értékelése](sql-vulnerability-assessment.md) (jelenleg előzetes verzió) egy könnyen konfigurálható felderítésére, nyomon követheti és szervizelheti azokat a lehetséges adatbázis segítséget az Azure SQL Database beépített eszköz biztonsági rések. A értékelése végrehajtja a biztonsági vizsgálatok az adatbázishoz, és hoz létre olyan jelentést, amely betekintést nyújt a biztonsági állapotát, beleértve, a biztonsági problémák megoldásához és az adatbázis biztonsági végrehajtandó lépések. A vizsgálati jelentés környezetre úgy, hogy egy elfogadható alapterv engedély konfigurációk, konfigurációk és adatbázis-beállításainak testre szabható. Ennek segítségével:

- A megfelelőségi követelményeknek adatbázis vizsgálat jelentések igénylő. 

- Adatok adatvédelmi szabványoknak megfelelő. 

- Egy dinamikus adatbázis környezetében, ahol módosításokat nehéz nyomon követéséhez figyelje.

További információkért lásd: [SQL biztonsági réseinek értékelése](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>További lépések

- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Adatbázis naplózási tárgyalását lásd: [SQL Database auditing](sql-database-auditing.md).
- A fenyegetésészlelés tárgyalását lásd: [SQL-adatbázis fenyegetésészlelés](sql-database-threat-detection.md).
