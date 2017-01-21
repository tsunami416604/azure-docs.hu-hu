---
title: "Az Azure SQL Database biztonsági áttekintése | Microsoft Docs"
description: "Ez a dokumentum bemutatja az Azure SQL Database és az SQL Server biztonságát, beleértve a felhőalapú és a helyszíni SQL Server közötti különbségeket a hitelesítés, az engedélyezés, a kapcsolat biztonsága, a titkosítás és a megfelelőség szempontjából."
services: sql-database
documentationcenter: 
author: tmullaney
manager: jhubbard
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 06/09/2016
ms.author: thmullan;jackr
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 9ebf3cd290151449c05c89307500fa5679de3b09


---
# <a name="securing-your-sql-database"></a>Az SQL Database-adatbázis védelme

Ez a cikk bemutatja az Azure SQL Database-t használó alkalmazások adatrétegének védelméhez szükséges alapszintű információkat. A cikk ismerteti az adatok védelméhez, a hozzáférés szabályozásához és a proaktív figyeléshez szükséges erőforrások az első lépéseit is. Az alábbi ábra az SQL Database által biztosított biztonság rétegeit ábrázolja.

![SQL-biztonság és -megfelelőség](./media/sql-database-security-overview/diagram.png)

Az SQL minden fajtájában elérhető biztonsági funkciók teljes körű áttekintése: [Az SQL Server-adatbázismotor és az Azure SQL Database biztonsági központja](https://msdn.microsoft.com/library/bb510589). Emellett [A biztonság és az Azure SQL Database című műszaki tanulmányban](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF) is talál további információt.

## <a name="protect-data"></a>Adatok védelme
Az SQL Database a mozgásban lévő adatokat a [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244) protokol, az inaktív adatokat [transzparens adattitkosítás](http://go.microsoft.com/fwlink/?LinkId=526242), a használatban lévő adatokat pedig az [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) protokoll használatával titkosítja az adatok védelme érdekében. Az SQL Database adatvédelmi szolgáltatásainak használatával kapcsolatban lásd az [adatvédelemmel és biztonsággal](sql-database-protect-data.md) foglalkozó témakört.

> [!IMPORTANT]
>Az Azure SQL Database-hez való kapcsolódáshoz minden esetben titkosítás (SSL/TLS) szükséges, amikor az adatok átvitele folyamatban van az adatbázisból vagy az adatbázisba. Az alkalmazás kapcsolati karakterláncában megadott paraméterekkel állítsa be a kapcsolat titkosítását, és hogy a kiszolgálótanúsítvány *nem* megbízható (ezt elvégzi a rendszer, ha a kapcsolati karakterláncot a klasszikus Azure portálról másolja), különben a kapcsolat nem tudja ellenőrizni a kiszolgáló identitását, és védtelen lesz a közbeékelődéses támadásokkal szemben. Az ADO.NET-illesztő számára például a következők a kapcsolati karakterlánc paraméterei: **Encrypt=True** és **TrustServerCertificate=False**. 

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

* A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
* Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelésére van szüksége, fontolja meg az [Azure Key Vault és az SQL Server együttes használatát egy Azure virtuális gépen](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

## <a name="control-access"></a>Vezérlési hozzáférés
Az SQL Database úgy biztosítja az adatai védelmét, hogy korlátozza az adatbázishoz való hozzáférést tűzfalszabályokkal, a felhasználókat az azonosságuk igazolására kényszerítő hitelesítési mechanizmusokkal, engedélyezéssel az adatokhoz szerepalapú tagságok és engedélyek útján, valamint sorszintű biztonsággal és dinamikus adatmaszkolással. Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.

> [!IMPORTANT]
> Az adatbázisok és logikai kiszolgálók az Azure-ban való kezelését a portál felhasználói fiókjának szerepkör-hozzárendelése szabályozza. A témával kapcsolatos további információk az [Szerepköralapú hozzáférés-vezérlés az Azure Portalon](../active-directory/role-based-access-control-configure.md) című cikkben találhatók.
>

### <a name="firewall-and-firewall-rules"></a>Tűzfal és tűzfalszabályok
Az adatok védelme érdekében a tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja [tűzfalszabályokkal](sql-database-firewall-configure.md), hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

### <a name="authentication"></a>Authentication
Az SQL Database-hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:

* A felhasználónévvel és jelszóval végzett **SQL-hitelesítést**. Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). 
* Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják. [Amikor csak lehet](https://msdn.microsoft.com/library/ms144284.aspx), használja az Active Directory-hitelesítést (beépített biztonság). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

### <a name="authorization"></a>Engedélyezés
Az engedélyezés az Azure SQL Database-adatbázisokban a felhasználók által végrehajtható műveletek körét jelenti, amelyeket a felhasználóifiók-adatbázis szerepkörtagságai és objektumszintű engedélyei határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

### <a name="row-level-security"></a>Sorszintű biztonság
A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet). További információkat a [sorszintű biztonsággal kapcsolatos](https://msdn.microsoft.com/library/dn765131) részben találhat.

### <a name="data-masking"></a>Adatmaszkolás 
Az SQL Database dinamikus adatmaszkolása úgy korlátozza a bizalmas adatok közzétételét, hogy maszkolja azokat a nem kiemelt jogosultságú felhasználók számára. A dinamikus adatmaszkolás automatikusan észleli a potenciálisan bizalmas adatokat az Azure SQL Database-ben, és végrehajtható javaslatokat tesz ezen mezők maszkolására úgy, hogy az a lehető legkisebb hatással legyen az alkalmazásrétegre. Rejtjelezi a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. További információ: [Az SQL Database dinamikus adatmaszkolásának első lépései](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Proaktív figyelés
Az SQL Database naplózási és fenyegetésészlelési képességek biztosításával védi az adatokat. 

### <a name="auditing"></a>Naplózás
Az SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit, és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található, ezáltal segíti az előírásoknak való megfelelőség fenntartását. A naplózás révén könnyebben átláthatja az adatbázisban folyamatban lévő tevékenységeket, illetve a lehetséges fenyegetések, esetleges visszaélések és biztonságmegsértések azonosítása céljából elemezheti és kivizsgálhatja a tevékenységelőzményeket. További információk: [Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Fenyegetések észlelése
A fenyegetések észlelése a biztonsági intelligencia egy további, az Azure SQL Database szolgáltatásba beépített rétegével egészíti ki a naplózást. A szolgáltatás folyamatosan dolgozik az adatbázist érintő rendellenes tevékenységek észlelésén, elemzésén és a profilkészítésen. A szolgáltatás értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A riasztásokra az informatív és hasznos utasításokat követve reagálhat. További információk: [Ismerkedés az SQL Database fenyegetések észlelése szolgáltatásával](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Megfelelőség
A fent említett szolgáltatások és funkciók mellett, amelyek alkalmazásával megfelelhet a különféle biztonsági megfelelőségi követelményeknek, az Azure SQL Database rendszeres vizsgálatokon esik át, és több megfelelőségi szabvány szempontjából is hitelesítve van. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://azure.microsoft.com/support/trust-center/services/) aktuális listájával együtt.

## <a name="next-steps"></a>Következő lépések

- Az SQL Database adatvédelmi szolgáltatásainak használatával kapcsolatban lásd az [adatvédelemmel és biztonsággal](sql-database-protect-data.md) foglalkozó témakört.
- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Információk a proaktív figyelésről: [az SQL Database naplózási szolgáltatását](sql-database-auditing-get-started.md) és [az SQL Database fenyegetésészlelési szolgáltatását](sql-database-threat-detection-get-started.md) bemutató cikkek.


<!--HONumber=Dec16_HO4-->


