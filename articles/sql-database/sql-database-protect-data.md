---
title: "Az Azure SQL Database adatvédelmi áttekintése | Microsoft Docs"
description: "Az Azure SQL Database-adabázisok adatvédelmének bemutatása."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Adatvédelem az SQL Database-adatbázisban

Az SQL Database titkosítás segítségével védi az adatokat.  

## <a name="overview"></a>Áttekintés

Az SQL Database a mozgásban lévő adatokat a [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244) protokol, az inaktív adatokat [transzparens adattitkosítás](http://go.microsoft.com/fwlink/?LinkId=526242), a használatban lévő adatokat pedig az [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) protokoll használatával titkosítja az adatok védelme érdekében. Az SQL Database adatvédelmi szolgáltatásainak használatával kapcsolatban lásd az [adatvédelemmel és biztonsággal](sql-database-protect-data.md) foglalkozó témakört.

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

* A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
* Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelésére van szüksége, fontolja meg az [Azure Key Vault és az SQL Server együttes használatát egy Azure virtuális gépen](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


Az SQL Database naplózási és fenyegetésészlelési képességek biztosításával védi az adatokat. 

### <a name="auditing"></a>Naplózás
Az SQL Database naplózási szolgáltatása nyomon követi az adatbázisok eseményeit, és felvezeti ezeket egy naplófájlba, amely a felhasználó Azure Storage-fiókjában található, ezáltal segíti az előírásoknak való megfelelőség fenntartását. A naplózás révén könnyebben átláthatja az adatbázisban folyamatban lévő tevékenységeket, illetve a lehetséges fenyegetések, esetleges visszaélések és biztonságmegsértések azonosítása céljából elemezheti és kivizsgálhatja a tevékenységi előzményeket. További információk: [Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Fenyegetések észlelése
A fenyegetések észlelése a biztonsági intelligencia egy további, az Azure SQL Database szolgáltatásba beépített rétegével egészíti ki a naplózást. A szolgáltatás folyamatosan dolgozik az adatbázist érintő rendellenes tevékenységek észlelésén, elemzésén és a profilkészítésen. A szolgáltatás értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A riasztásokra az informatív és hasznos utasításokat követve reagálhat. További információk: [Ismerkedés az SQL Database fenyegetések észlelése szolgáltatásával](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Kapcsolatbiztonság
A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályokat a kiszolgáló és az adatbázis is felhasználja, hogy elutasítsa a csatlakozási kísérleteket a nem kifejezetten engedélyezett IP-címekről. Ha engedélyezni kívánja egy új adatbázishoz való csatlakozást egy alkalmazás vagy egy ügyfélgép nyilvános IP-címe számára, akkor először létre kell hoznia egy kiszolgálószintű tűzfalszabályt a klasszikus Azure portál, a REST API vagy a PowerShell használatával. Az ajánlott eljárás a kiszolgáló tűzfalán átengedett IP-címtartományokat lehető legnagyobb mértékű korlátozása. További információ: [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/ee621782).

Az Azure SQL Database-hez való kapcsolódáshoz minden esetben titkosítás (SSL/TLS) szükséges, amikor az adatok átvitele folyamatban van az adatbázisból vagy az adatbázisba. Az alkalmazás kapcsolati karakterláncában megadott paraméterekkel állítsa be a kapcsolat titkosítását, és hogy a kiszolgálótanúsítvány *nem* megbízható (ezt elvégzi a rendszer, ha a kapcsolati karakterláncot a klasszikus Azure portálról másolja), különben a kapcsolat nem tudja ellenőrizni a kiszolgáló identitását, és védtelen lesz a közbeékelődéses támadásokkal szemben. Az ADO.NET-illesztő számára például a következők a kapcsolati karakterlánc paraméterei: **Encrypt=True** és **TrustServerCertificate=False**. További információ: [Kapcsolatok titkosítása és tanúsítványhitelesítés az Azure SQL Database-ben](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Authentication
A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. Az SQL Database két hitelesítési típust támogat:

* A felhasználónévvel és jelszóval végzett **SQL-hitelesítést**
* Az **Azure Active Directory-alapú hitelesítést**, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják

Az adatbázis logikai kiszolgálójának létrehozásakor megadta a „kiszolgálói rendszergazda” bejelentkezés felhasználónevét és jelszavát. Ezen hitelesítő adatokkal hitelesítheti magát a kiszolgáló minden adatbázisában az adatbázis tulajdonosaként („dbo”). Ha Azure Active Directory-alapú hitelesítést kíván használni, létre kell hoznia egy másik kiszolgálói rendszergazdát „Azure AD admin” névvel, amely engedélyekkel rendelkezik az Azure AD-felhasználók és -csoportok felügyeletéhez. Ez a rendszergazda a normál kiszolgálói rendszergazdák által elvégezhető összes műveletet is végrehajthatja. A [Csatlakozás az SQL Database-hez Azure Active Directory-alapú hitelesítéssel](sql-database-aad-authentication.md) című cikk bemutatja, hogyan hozhat létre Azure AD-rendszergazdát az Azure Active Directory-alapú hitelesítés engedélyezéséhez.

Az ajánlott eljárás az, ha az alkalmazás másik fiókot használ a hitelesítéshez. Ezzel korlátozhatja az alkalmazáshoz rendelt engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálásos támadásokkal szemben. Az ajánlott módszer a [tartalmazott adatbázis-felhasználó](https://msdn.microsoft.com/library/ff929188) létrehozása, amellyel az alkalmazás közvetlenül kapcsolódhat az adatbázishoz. SQL-hitelesítést használó tartalmazott adatbázis-felhasználó létrehozásához futtassa az alábbi T-SQL-parancsot, miközben a kiszolgálói rendszergazdai bejelentkezéssel csatlakozik a felhasználói adatbázishoz:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Ha Azure Active Directory-hitelesítést használó rendszergazdát hozott létre, egy tartalmazott adatbázis-felhasználó létrehozásához futtassa az alábbi T-SQL-parancsot, miközben Azure AD-rendszergazdaként csatlakozik a felhasználói adatbázishoz:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Az adatbázishoz való csatlakozáshoz mindkét esetben az alkalmazás kapcsolati karakterláncának kell meghatároznia a felhasználói hitelesítő adatokat a kiszolgálói rendszergazdai bejelentkezés helyett.

Az SQL Database-adatbázissal való hitelesítésről az [Adatbázisok és bejelentkezések Azure SQL Database-ben történő kezelésével foglalkozó](sql-database-manage-logins.md) cikkben talál további információt.

## <a name="authorization"></a>Engedélyezés
Az engedélyezés az Azure SQL Database-adatbázisokban végrehajtható műveleteket jelenti, amelyeket a felhasználói fiók szerepkörtagságai és engedélyei határoznak meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. Az Azure SQL Database-ben ez egyszerűen kezelhető a T-SQL-szerepkörökkel:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

Többféleképpen korlátozhatja a felhasználók engedélyeit az Azure SQL Database-ben:

* A db_datareadertől és a db_datawritertől eltérő [adatbázis-szerepkörökkel](https://msdn.microsoft.com/library/ms189121) sokoldalúbb alkalmazásfelhasználói fiókokat, illetve kevésbé sokoldalú felügyeleti fiókokat hozhat létre.
* A részletes [engedélyek](https://msdn.microsoft.com/library/ms191291) használatával szabályozhatja, hogy mely műveleteket végezheti el az adatbázis egyes oszlopain, tábláin, nézetein, eljárásain vagy egyéb objektumain.
* A [megszemélyesítés](https://msdn.microsoft.com/library/vstudio/bb669087) és a [modulaláírás](https://msdn.microsoft.com/library/bb669102) az engedélyek biztonságos átmeneti növelésére használható.
* A [sorszintű biztonság](https://msdn.microsoft.com/library/dn765131) használatával korlátozhatja, hogy a felhasználó mely sorokhoz férhessen hozzá.
* Az [adatmaszkolás](sql-database-dynamic-data-masking-get-started.md) használatával korlátozhatja a bizalmas adatok megjelenítését.
* A [tárolt eljárások](https://msdn.microsoft.com/library/ms190782) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

Az adatbázisok és logikai kiszolgálók klasszikus Azure portálon történő, illetve az Azure Resource Manager API-val végzett kezelését a portál felhasználói fiókjának szerepkör-hozzárendelése szabályozza. A témával kapcsolatos további információk az [Szerepköralapú hozzáférés-vezérlés az Azure Portalon](../active-directory/role-based-access-control-configure.md) című cikkben találhatók.

## <a name="encryption"></a>Titkosítás
Az Azure SQL Database úgy segítheti az adatok védelmét, hogy a [transzparens adattitkosítással](http://go.microsoft.com/fwlink/?LinkId=526242) akkor titkosítja az adatokat, amikor azok nincsenek használatban, vagy amikor adatbázisfájlokban és biztonsági mentésekben vannak tárolva. Az adatbázis titkosításához csatlakozzon adatbázis-tulajdonosként, és hajtsa végre a következő utasítást:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

A bizalmas adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

* A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
* Ha hardveres biztonsági modulra vagy a titkosítási kulcshierarchia központi kezelésére van szüksége, fontolja meg az [Azure Key Vault és az SQL Server együttes használatát egy Azure virtuális gépen](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* Az [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) (előzetes verzió) használata transzparenssé teszi a titkosítást az alkalmazások számára, továbbá lehetővé teszi az ügyfelek számára a bizalmas adatok ügyfélalkalmazásokon belüli titkosítását anélkül, hogy a titkosítási kulcsokat meg kellene osztani az SQL Database-zel.

## <a name="auditing"></a>Naplózás
Az adatbázisok eseményeinek naplózásával és nyomon követésével fenntarthatja az előírásoknak való megfelelőséget, és azonosíthatja a gyanús tevékenységeket. Az SQL Database naplózási szolgáltatásával az Azure Storage-fiók egyik naplófájljában rögzítheti az adatbázis eseményeit. Az SQL Database naplózási szolgáltatása a Microsoft Power BI-ba is integrálható, hogy elősegítse a részletes jelentéskészítést és elemzést. További információk: [Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing-get-started.md).

Az SQL Database fenyegetések észlelése a naplózáson túl egy további biztonsági réteget nyújt. A szolgáltatás használatával a rendellenes tevékenységek esetén kiadott biztonsági riasztások révén a fenyegetésekre azok felmerülésekor azonnal reagálhat. További információk: [Ismerkedés az SQL Database fenyegetések észlelése szolgáltatásával](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Megfelelőség
A fent említett szolgáltatások és funkciók mellett, amelyek alkalmazásával megfelelhet a különféle biztonsági megfelelőségi követelményeknek, az Azure SQL Database rendszeres vizsgálatokon esik át, és több megfelelőségi szabvány szempontjából is hitelesítve van. További információkat az [Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/) talál, az [SQL Database megfelelőségi tanúsítványainak](https://azure.microsoft.com/support/trust-center/services/) aktuális listájával együtt.

## <a name="next-steps"></a>Következő lépések

- Az SQL Database összes biztonsági szolgáltatásairól [az SQL biztonsági szolgáltatásainak áttekintése](sql-database-security-overview.md) biztosít további információkat.
- Az SQL Database említett hozzáférés-vezérlési funkcióinak használatával kapcsolatban lásd a [hozzáférés-vezérléssel](sql-database-control-access.md) foglalkozó témakört.
- Információk a proaktív figyelésről: [az SQL Database naplózási szolgáltatását](sql-database-auditing-get-started.md) és [az SQL Database fenyegetésészlelési szolgáltatását](sql-database-threat-detection-get-started.md) bemutató cikkek.




<!--HONumber=Dec16_HO4-->


