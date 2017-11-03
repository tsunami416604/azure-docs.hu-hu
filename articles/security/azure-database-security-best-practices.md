---
title: "Azure-adatbázishoz ajánlott biztonsági eljárások |} Microsoft Docs"
description: "Ez a cikk ajánlott eljárások az Azure-adatbázis biztonsági biztosít."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: tomsh
ms.openlocfilehash: 5bd6fe0dd369b3bbc7ca0d697c964badda557cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-best-practices"></a>Azure-adatbázishoz ajánlott biztonsági eljárások

Biztonsági elsődleges szempont kezeléséhez, adatbázisok, és az Azure SQL Database prioritást mindig lett. Az adatbázisok szorosan védve legyenek megfelel a legtöbb szabályozó segítségével vagy a biztonsági követelményeket, például a HIPAA, ISO 27001/27002 és PCI DSS szintjét 1, többek között. A biztonsági megfelelőség tanúsítványok aktuális listáját érhető el: a [Microsoft Trust Center webhely](http://azure.microsoft.com/support/trust-center/services/). Is használhat az adatbázisok elhelyezni az adott Azure adatközpontjaiban szabályozási követelmények alapján.

Ez a cikk ismertetik Azure-adatbázishoz ajánlott biztonsági eljárások gyűjteménye. Az alábbi gyakorlati tanácsok az Azure-adatbázis biztonsági származtatja tapasztalatunk, és az ügyfelek feladatokat, például saját maga.

A minden ajánlott eljárás az hogy ismertetik:

-   Mi az az ajánlott eljárás szerint
-   Miért érdemes, hogy a legjobb engedélyezése
-   Milyen lehet az eredmény, ha nem engedélyezi az ajánlott eljárás szerint
-   Hogyan megismerheti az ajánlott eljárás engedélyezése

Azure adatbázis ajánlott biztonsági eljárások a cikkben egy együttműködési véleményt és az Azure platform képességei alapján, és a szolgáltatás állítja be, ez a cikk írásának időpontjában léteznek. Vélemények és technológiák változnak az idők, és ez a cikk a változások követése érdekében rendszeresen frissül.

Azure-adatbázishoz ajánlott biztonsági eljárások cikkben említett a következők:

-   A tűzfalszabályok segítségével adatbázis elérésének korlátozása
-   Adatbázis-hitelesítés engedélyezése
-   Adatok védelme a titkosítás
-   Adatok védelmére átvitel
-   Adatbázis naplózásának engedélyezése
-   Adatbázis fenyegetésészlelés engedélyezéséhez


## <a name="use-firewall-rules-to-restrict-database-access"></a>A tűzfalszabályok segítségével adatbázis elérésének korlátozása

A Microsoft Azure SQL Database egy relációs adatbázis-szolgáltatást nyújt az Azure és egyéb internetalapú alkalmazások számára. Hozzáférés a biztonság SQL-adatbázis a tűzfalszabályok korlátozza az IP-cím, a hitelesítési mechanizmusok megkövetelheti a felhasználóktól kell igazolnia az identitását és a konkrét műveletek és az adatok felhasználók hitelesítési mechanizmusok kapcsolat szabályozza a hozzáférést. Tűzfalak tagadni a hozzáférést minden az adatbázis-kiszolgáló csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.

![Tűzfalszabályok](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Az Azure SQL Database szolgáltatás kizárólag a 1433-as TCP-porton keresztül érhető el. Ha a helyi számítógépről szeretné elérni az SQL Database-t, akkor ellenőrizze, hogy az ügyfélszámítógépen lévő tűzfal engedélyezi-e a kimenő kommunikációt az 1433-as TCP-porton keresztül. Ha nincs szükség más alkalmazások, bejövő kapcsolat tiltása a 1433-as TCP-port tűzfalszabályok használatával.

A kapcsolódási folyamat részeként az Azure virtuális gépektől érkező kapcsolatok az egyes feldolgozói szerepkörök egyedi IP-címeire és portjaira lesznek átirányítva. A portszám a 11000-től 11999-ig terjedő tartományban található. További információ a TCP-portok: [kívüli ADO.NET 4.5 és SQL Database2 1433-as portokon](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Adatbázis-hitelesítés engedélyezése
SQL adatbázis két típusú hitelesítés, SQL-hitelesítést és az Azure Active Directory-hitelesítés (az Azure AD-alapú hitelesítés) támogatja.

**SQL-hitelesítés** a következő esetekben ajánlott:

-   Lehetővé teszi SQL Azure támogatására környezetek vegyes operációs rendszerekkel, ahol minden felhasználó nem hitelesíti a Windows-tartománynak.
-   Lehetővé teszi, hogy az SQL Azure régebbi SQL Server-hitelesítést igénylő harmadik felek által nyújtott alkalmazásokat és támogatásához.
-   Lehetővé teszi, hogy a felhasználók kapcsolódhatnak ismeretlen vagy nem megbízható tartományokból. Például egy adott csatlakozás meghatározott felhasználók alkalmazás hozzárendelt SQL Server bejelentkezési fogadására a rendelések állapotát.
-   Lehetővé teszi SQL Azure Web-alapú alkalmazások támogatásához, ahol a felhasználók létre saját identitással.
-   Lehetővé teszi, hogy eltérő szoftverfejlesztők is ismert, előre megadott SQL Server bejelentkezési alapján összetett engedély hierarchiába használatával az alkalmazások terjesztése.

> [!Note]
> Azonban az SQL Server-hitelesítés Kerberos biztonsági protokoll nem használható.

Ha **SQL-hitelesítés** kell:

-   Az erős hitelesítő adatok kezelése magát.
-   A kapcsolati karakterláncban a hitelesítő adatok védelme.
-   A hitelesítő adatokat, továbbítja a hálózaton keresztül a webkiszolgáló az adatbázis (vélhetően) védelmét. További információ: [hogyan: Kapcsolódás az SQL Server használatával SQL-hitelesítés az ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

**Az Azure Active Directory authentication** a Microsoft Azure SQL-adatbázishoz szeretne csatlakozni egy mechanizmus és [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) identitások az Azure Active Directory (Azure AD) segítségével. Az Azure AD-alapú hitelesítés adatbázis-felhasználók identitását, és más Microsoft-szolgáltatásokban egyetlen központi helyen központilag kezelheti. Központi azonosítófelügyeleti biztosít egy helyen adatbázis-felhasználók kezelése és egyszerűbbé teszi a jogosultság kezelése. Előnyei a következők:

-   SQL Server-hitelesítés helyett biztosít.
-   Segítséget nyújt a felhasználói identitások elterjedése leállítása adatbázis-kiszolgáló között.
-   Lehetővé teszi, hogy a jelszó Elforgatás egyetlen helyen.
-   Az ügyfelek adatbázis-engedélyek külső (AAD) csoportok használatával kezelheti.
-   Integrált Windows-hitelesítés és egyéb Azure Active Directory által támogatott hitelesítési engedélyezésével azt megnövelésével kiküszöbölheti jelszavak tárolását.
-   Az Azure AD authentication tartalmazott adatbázis-felhasználók használatával személyazonosság igazolása az adatbázis szintjén.
-   Az Azure AD hitelesítési jogkivonat-alapú alkalmazások SQL-adatbázishoz szeretne csatlakozni támogatja.
-   Az Azure AD authentication az ADFS (tartomány-összevonás) vagy a natív felhasználói/jelszó-hitelesítés támogat egy helyi Azure Active Directory tartományi szinkronizálás nélkül.
-   Az Azure AD-példányokhoz SQL Server Management Studio eszközt, amely az Active Directory univerzális hitelesítés használatára, amely magában foglalja a többtényezős hitelesítés (MFA). Többtényezős hitelesítés tartalmazza, könnyen ellenőrzési lehetőségek széles erős hitelesítés – telefonhívás, szöveges üzenetet, intelligens kártyák PIN-kód és az értesítést a mobilalkalmazásban. További információkért lásd: [SSMS támogatása az Azure AD MFA az SQL-adatbázis és az SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

A konfigurálás lépéseinek végrehajtásához konfigurálhatja és használhatja az Azure Active Directory-hitelesítés a következő eljárásokat tartalmazzák.

-   Létrehozása és feltöltése az Azure AD.
-   Választható lehetőség: Hozzárendelése, vagy módosítsa az active directory jelenleg az Azure-előfizetéshez társított.
-   Hozzon létre egy Azure Active Directory-rendszergazda az Azure SQL server vagy [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Állítsa be az ügyfélszámítógépen.
-   Tartalmazott adatbázis-felhasználók létrehozása az Azure AD-identitások leképezve az adatbázisban.
-   Az adatbázis csatlakozás az Azure AD-identitások segítségével.

Részletek információt [Itt](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Adatok védelme a titkosítás

[Az Azure SQL Database átlátható adattitkosítás (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) védelmét a rosszindulatú tevékenységek fenyegetés elleni valós idejű titkosítási és visszafejtési az adatbázis, a társított biztonsági másolatok, és a tranzakciós naplófájlok aktívan anélkül, hogy az alkalmazást módosítani kellene. TDE teljes adatbázis tárterülete az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával titkosítja.

Akkor is, ha a teljes tárterület titkosított, nagyon fontos is titkosítani az adatbázist, saját magát. Ez az a mélység megközelítés a data protection a védelmet megvalósítása. Ha használja az Azure SQL Database, és szeretne védeni a bizalmas adatok, például a hitelkártya vagy társadalombiztosítási számot, a FIPS 140-2 érvényesítve 256 bites AES titkosítást, amely megfelel az sok ipari szabványok (például a HIPAA, a PCI) adatbázisok titkosíthatók.

Fontos tudni, hogy fájlok kapcsolódó [(BPE) kiterjesztés puffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) amikor adatbázis titkosítása a TDE nincs titkosítva. A fájlszintű titkosítás Rendszereszközök fájl például kell használnia [BitLocker](https://technet.microsoft.com/library/cc732774) vagy a [titkosított fájlrendszer (EFS)](https://technet.microsoft.com/library/cc700811.aspx) BPE a kapcsolódó fájlokat.

Hitelesített felhasználó óta, mint például a biztonsági rendszergazda vagy egy adatbázis-rendszergazda férhet hozzá az adatokhoz akkor is, ha a TDE, az adatbázis titkosított is kövesse az alábbi javaslatokat:

-   Engedélyezze az SQL-hitelesítést az adatbázis szintjén.
-   Használja az Azure AD használatával [RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Felhasználók és az alkalmazások által használandó külön fiókok hitelesítéséhez. Így korlátozhatja a felhasználók és az alkalmazások számára megadott engedélyeket és a kártékony tevékenység a kockázatok csökkentése.
-   Alkalmazzon adatbázis szintű biztonsági rögzített adatbázis-szerepkörök (például a db_datareader vagy db_datawriter), vagy az alkalmazáshoz kiválasztott adatbázis-objektumok explicit engedélyt egyéni szerepköröket hozhatnak létre.

Az adatok titkosításának egyéb módjaira vonatkozóan fontolja meg az alábbiakat:

-   A [cellaszintű titkosítás](https://msdn.microsoft.com/library/ms179331.aspx) használatával az egyes oszlopokat, vagy akár a cellákat is külön titkosítási kulccsal titkosíthatja.
-   Használ, mindig titkosítja a titkosítási: [mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx) lehetővé teszi az ügyfelek ügyfélalkalmazások található bizalmas adatok titkosításához, és soha ne adja meg a titkosítási kulcsokat az adatbázismotor (SQL-adatbázis vagy SQL Server). Ennek eredményeképpen az adatok tulajdonosa (és megtekinthesse) elkülönítése Always Encrypted funkciója és azokra, akik az adatok kezelése (de nem hozzáféréssel kell rendelkeznie).
-   Használata a sorszintű biztonság: sorszintű biztonság lehetővé teszi az ügyfelek a jellemzők, a felhasználó (pl. csoport tagsági vagy a végrehajtási környezet) lekérdezése alapján adatbázis tábla sorainak való hozzáférés vezérlése érdekében. További információkat a [sorszintű biztonsággal kapcsolatos](https://msdn.microsoft.com/library/dn765131) részben találhat.

## <a name="protect-data-in-transit"></a>Adatok védelmére átvitel
Az átvitel során az adatok védelme a data protection stratégia nagyon fontos részét kell lennie. Oda-vissza adatokat fog áthelyezése több helyről, mert az általános ajánlás az, hogy mindig SSL/TLS protokollokat használ az exchange-adatok különböző helyek között. Bizonyos esetekben érdemes lehet különítheti el a teljes kommunikációs csatornát a helyszíni és a felhő közötti virtuális magánhálózati (VPN) segítségével infrastruktúra.

Az adatok áthelyezése a helyszíni infrastruktúra és az Azure között megfelelő védelmi funkciók, például a HTTPS- vagy VPN-érdemes lehet.

Használja a szervezet számára több található munkaállomások helyszíni Azure hozzáférést igénylő [Azure telephelyek közötti VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Biztonságos gondoskodni kívánó szervezetek számára a hozzáférést a található egyéni munkaállomások helyszíni vagy helyszíni Azure-ba, inkább használja az [pont-pont VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Nagyobb, mint egy dedikált nagy sebességű WAN-kapcsolaton keresztül anélkül áthelyezhetők [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Ha ExpressRoute használatát választja, a alkalmazás szintű az adatok is titkosíthatók [SSL/TLS](https://support.microsoft.com/kb/257591) vagy egyéb protokollok hozzáadott védelemre.

Az Azure Storage az Azure-portálon való interakció, minden tranzakció történik meg HTTPS használatával. [Storage REST API felülete](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS is használható, amellyel kommunikálni tud [Azure Storage](https://azure.microsoft.com/services/storage/) és [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Adatok védelmére átvitel nem válaszoló szervezetek jobban ki vannak téve a rendszer [-átjárójának](https://technet.microsoft.com/library/gg195821.aspx), [lehallgatás](https://technet.microsoft.com/library/gg195641.aspx) és munkamenet-eltérítés. Ilyen támadások lehet az első lépés a bizalmas adatok elérésekor.

További információt Azure VPN lehetőség szerint a cikk elolvasása [tervezése és kialakítása VPN-átjáró](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Adatbázis naplózásának engedélyezése
Egy példányt az SQL Server adatbázismotor vagy egyedi adatbázis naplózásának magában foglalja a nyomon követése és az adatbázismotor események naplózása. SQL Server-naplózás lehetővé teszi a kiszolgáló felülvizsgálatát, amely a kiszolgálói szintű események naplózási specifikációk server és adatbázis szolgáltatásiszint-események naplózási specifikációk adatbázis létrehozása. A naplózott eseményeket az Eseménynapló bejegyzéseit, vagy azoknak a fájlok naplózása írhatók.

Nincsenek attól függően, hogy kormányzati vagy a telepítési szabványok követelményeinek SQL Server-naplózás több szintet. Az SQL Server Audit biztosít az eszközök és folyamatok kell rendelkeznie a engedélyezése, tárolására és naplózás különböző server és adatbázis-objektumok megtekintése.

[Az Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be az Azure Storage-fiók.

A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

Naplózás lehetővé teszi, hogy és elősegíti a megfelelést a megfelelőségi követelményeket, de nem biztosítja a megfelelőség.

Adatbázis naplózási és ahhoz, hogyan kapcsolatos további tudnivalókért olvassa el a cikk [engedélyezése az Azure Security Centerben SQL-kiszolgálón a naplózás és a fenyegetések észlelésére](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Adatbázis fenyegetésészlelés engedélyezéséhez
A Fenyegetésészlelés SQL lehetővé teszi észlelése és azok bekövetkezésekor, adja meg a biztonsági riasztások a rendellenes tevékenységek reagáljon a lehetséges veszélyforrásokra. Adatbázis gyanús tevékenységeket, a potenciális biztonsági réseket, és a SQL injektálási támadások, valamint rendellenes adatbázis hozzáférési minták riasztást fog kapni. SQL Fenyegetésészlelés riasztásokat gyanús tevékenység részleteinek megadása, és vizsgálja meg, és a fenyegetések mérséklésére művelet javasolja.

Például SQL-injektálás az egyik a közös webes alkalmazás biztonsági problémák elleni támadásra adatvezérelt alkalmazások, az interneten. A támadók előnyeit alkalmazás biztonsági rések rosszindulatú SQL-utasítások alkalmazás mezőkbe szúrjon megsértése vagy módosítása az adatbázis adatai.

További információt az adatbázis az Azure portál lásd: a fenyegetések észlelése beállításával [SQL adatbázis Fenyegetésészlelés](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Ezenkívül integrálható az SQL Fenyegetésészlelés a riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/). Lehetőséget kínálunk a szolgáltatás 60 napos díjmentes kipróbálására.

Adatbázis fenyegetések észlelése és az engedélyezéshez kapcsolatos további tudnivalókért olvassa el a cikk [engedélyezése az Azure Security Centerben SQL-kiszolgálón a naplózás és a fenyegetések észlelésére](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Összegzés
Azure-adatbázis egy robusztus adatbázis platform, amely sok szervezeti és szabályozási megfelelőségi követelményeknek funkciókat teljes tartománnyal. A adatokhoz való fizikai hozzáférés vezérlése, és számos lehetőséget használ az adatok biztonsági a fájl-, oszlop-vagy sor szintjén átlátható adattitkosítási, cellaszintű titkosítását alkalmazza vagy sorszintű biztonság is adatok védelme. Mindig titkosított is lehetővé teszi a titkosított adatok műveleteket leegyszerűsítésével alkalmazás frissítések. Viszont naplófájlokat SQL adatbázis-tevékenység hozzáférést biztosít a szükséges információkat, így tudja, hogyan és mikor adatokhoz.

## <a name="next-steps"></a>Következő lépések
- Tűzfalszabályok kapcsolatos további információkért lásd: [tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Felhasználókkal és bejelentkezésekkel kapcsolatos információk: [Bejelentkezések kezelése](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Az oktatóanyagok esetén lásd: [az Azure SQL Database biztonságos](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
