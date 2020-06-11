---
title: 'Gyors útmutató: felügyelt SQL-példány (portál) létrehozása'
description: Hozzon létre egy felügyelt példányt, a hálózati környezetet és az ügyfél virtuális gépet a rövid útmutató Azure Portal használatával való hozzáféréshez.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: e00736c734b3e6f6ba6b188061ec2ffb59a5b169
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659752"
---
# <a name="quickstart-create-a-managed-instance-of-sql-managed-instance"></a>Gyors útmutató: felügyelt SQL-példány felügyelt példányának létrehozása
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre felügyelt [Azure SQL](sql-managed-instance-paas-overview.md) -példányokat a Azure Portalban.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](resource-limits.md#supported-regions) és [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

Felügyelt példány létrehozásához kövesse az alábbi lépéseket: 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az **Azure SQL** kifejezést a keresőmezőbe.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A felügyelt Azure SQL-példányra vonatkozó további információkat az **SQL felügyelt példányának** csempén látható **Részletek megjelenítése** lehetőség választásával tekintheti meg.
1. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/instance-create-quickstart/create-managed-instance.png)

4. A szükséges és választható információk hozzáadásához használja az **Azure SQL felügyelt példány** létesítési űrlapjának létrehozásához használt lapokat. A következő szakaszok ismertetik ezeket a lapokat.

### <a name="basics-tab"></a>Alapbeállítások lap

- Adja meg az **alapok** lapon szükséges kötelező információkat. Ez a felügyelt példány kiépítéséhez szükséges minimális információ.

   !["Alapismeretek" lap felügyelt példány létrehozásához](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Felügyelt példány neve** | Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre kívánja hozni a felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
   | **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármilyen érvényes Felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza a **felügyelt példány beállítása** lehetőséget a számítási és tárolási erőforrások méretének és a díjszabási szintek áttekintéséhez. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 

   ![Felügyelt példány űrlapja](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Ha a felügyelt SQL-példány létrehozása előtt szeretné áttekinteni a beállításokat, válassza a **felülvizsgálat + létrehozás**lehetőséget. Vagy konfigurálja a hálózatkezelési beállításokat a következő lehetőség kiválasztásával **: hálózatkezelés**.

### <a name="networking-tab"></a>Hálózatkezelés lap

- Adja meg a választható adatokat a **hálózatkezelés** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["Hálózatkezelés" lap felügyelt példány létrehozásához](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](vnet-existing-add-subnet.md) , mielőtt kiválasztja az új felügyelt példány célhelyének. További információ az SQL felügyelt példányának hálózati környezetének konfigurálásával kapcsolatos követelményekről: [virtuális hálózat konfigurálása SQL felügyelt példányhoz](connectivity-architecture-overview.md). |
   | **Kapcsolat típusa** | Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: az [Azure SQL felügyelt példányának kapcsolattípus](../database/connectivity-architecture.md#connection-policy).|
   | **Nyilvános végpont**  | Válassza az **Engedélyezés** lehetőséget. | Ahhoz, hogy egy felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell ezt a beállítást. | 
   | **Hozzáférés engedélyezése** (ha a **nyilvános végpont** engedélyezve van) | Válasszon egyet a lehetőségek közül.   |A portál felhasználói felülete lehetővé teszi egy biztonsági csoport nyilvános végponttal való konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>**Azure-szolgáltatások**: ezt a lehetőséget akkor javasoljuk, ha Power bi vagy egy másik több-bérlős szolgáltatásból csatlakozik. </li> <li> **Internet**: tesztelési célokra használható, ha gyorsan el szeretné végezni a felügyelt példányok üzembe helyezését. Éles környezetekhez nem ajánlott. </li> <li> **Nincs hozzáférés**: Ez a beállítás egy **megtagadási** biztonsági szabályt hoz létre. Módosítsa ezt a szabályt úgy, hogy egy nyilvános végponton keresztül elérhetővé váljon a felügyelt példány. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért tekintse meg az [Azure SQL felügyelt példány biztonságos használata nyilvános végponttal](public-endpoint-overview.md)című témakört.|

- A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. Vagy konfigurálja a további egyéni beállításokat a **Tovább: további beállítások**lehetőség kiválasztásával.

### <a name="additional-settings"></a>További beállítások

- Adja meg a választható információkat a **További beállítások** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["További beállítások" lap felügyelt példány létrehozásához](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Rendezés** | Válassza ki a felügyelt példányhoz használni kívánt rendezést. Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a használatával, `SELECT SERVERPROPERTY(N'Collation')` és használja ezt az értéket.| További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Időzóna** | Válassza ki azt az időzónát, amelyet a felügyelt példány figyelembe fog venni.|További információ: [időzóna](timezones-overview.md).|
   | **Használat feladatátvételi másodlagosként** | Válassza az **Igen** lehetőséget. | Ezzel a beállítással engedélyezheti a felügyelt példány másodlagos feladatátvételi csoportként való használatát.|
   | **Elsődleges SQL felügyelt példánya** (ha a **másodlagos feladatátvételi** beállítás értéke **Igen**) | Válasszon egy meglévő elsődleges felügyelt példányt, amely ugyanabban a DNS-zónában lesz csatlakoztatva, mint a létrehozandó felügyelt példány. | Ez a lépés lehetővé teszi a feladatátvételi csoport létrehozás utáni konfigurációját. További információ: [oktatóanyag: felügyelt példány hozzáadása feladatátvételi csoporthoz](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Felülvizsgálat + létrehozás

1. A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** fület a lehetőségek áttekintéséhez.

   ![Felügyelt példányok áttekintésére és létrehozására szolgáló lap](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Válassza a **Létrehozás** lehetőséget a felügyelt példány kiépítés indításához.

> [!IMPORTANT]
> A felügyelt példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő: [SQL felügyelt példányok kezelési műveletei](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Központi telepítési folyamat figyelése

1. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

   ![SQL felügyelt példány központi telepítésének telepítési folyamata](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Válassza a **telepítés folyamatban** van az értesítésben az SQL felügyelt példány ablakának megnyitásához és a telepítési folyamat további figyeléséhez. 

> [!TIP]
> Ha bezárta a webböngészőt, vagy áthelyezte a telepítési folyamat képernyőről, kövesse az alábbi lépéseket az üzembe helyezési folyamat helyének megkereséséhez:
> 1. A Azure Portal nyissa meg az erőforráscsoportot (az **alapok** lapon), amelyre telepíteni kívánja az SQL felügyelt példányát.
> 2. Válassza a **központi telepítések**lehetőséget.
> 3. Válassza ki az SQL felügyelt példány központi telepítési műveletét folyamatban.

> [!IMPORTANT]
> A felügyelt példányok létrehozási állapotának lekéréséhez **olvasási engedéllyel** kell rendelkeznie az erőforráscsoporthoz. Ha nem rendelkezik ezzel az engedéllyel vagy visszavonással, miközben a felügyelt példány létrehozása folyamatban van, ez azt eredményezheti, hogy az SQL felügyelt példánya nem látható az erőforráscsoport-telepítések listájában.
>

## <a name="view-resources-created"></a>Létrehozott erőforrások megtekintése

Felügyelt példány sikeres üzembe helyezése után a létrehozott erőforrások megtekintése:

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot. 

   ![SQL felügyelt példány erőforrásai](./media/instance-create-quickstart/resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Hálózati beállítások megtekintése és finomhangolása

A hálózati beállítások finomhangolásához ellenőrizze a következőket:

1. Válassza ki az útválasztási táblázatot az Ön számára létrehozott felhasználó által megadott útvonal (UDR) áttekintéséhez.

   ![Útválasztási táblázat](./media/instance-create-quickstart/route-table.png)

2. Az útválasztási táblázatban tekintse át azokat a bejegyzéseket, amelyek az SQL felügyelt példányának virtuális hálózatán belüli és onnan érkező forgalmat irányítják. Ha manuálisan hozza létre vagy konfigurálja az útválasztási táblázatot, győződjön meg róla, hogy létrehozza ezeket a bejegyzéseket az SQL felügyelt példány útválasztási táblázatában.

   ![SQL felügyelt példány alhálózatának bejegyzése helyire](./media/instance-create-quickstart/udr.png)

3. Térjen vissza az erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot.

   ![Hálózati biztonsági csoport](./media/instance-create-quickstart/network-security-group.png)

4. Tekintse át a bejövő és kimenő biztonsági szabályokat. 

   ![Biztonsági szabályok](./media/instance-create-quickstart/security-rules.png)

> [!IMPORTANT]
> Ha nyilvános végpontot konfigurált az SQL felügyelt példányához, meg kell nyitnia a portokat, hogy engedélyezzék a hálózati forgalom számára a nyilvános internetről felügyelt példányhoz való kapcsolódást. További információért lásd: [nyilvános végpont konfigurálása SQL felügyelt példányhoz](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) .
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Kapcsolat adatainak beolvasása a felügyelt SQL-példányhoz

A felügyelt SQL-példányhoz való kapcsolódáshoz kövesse az alábbi lépéseket az állomásnév és a teljes tartománynév (FQDN) lekéréséhez:

1. Térjen vissza az erőforráscsoporthoz, és válassza ki a felügyelt példányt.

   ![Felügyelt példány az erőforráscsoporthoz](./media/instance-create-quickstart/managed-instance.png)

2. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja a felügyelt példány állomásnevét a következő rövid útmutatóban való használathoz.

   ![Állomásnév](./media/instance-create-quickstart/host-name.png)

   A másolt érték egy teljes tartománynevet (FQDN) jelöl, amely az SQL felügyelt példányhoz való kapcsolódáshoz használható. A következőhöz hasonló példa: *your_host_name. a1b2c3d4e5f6. database. Windows. net*.

## <a name="next-steps"></a>Következő lépések

További információ a felügyelt SQL-példányokhoz való kapcsolódásról:
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Az Azure-beli virtuális gépekről az SQL felügyelt példányhoz való kapcsolódást bemutató rövid útmutató: Azure-beli [Virtuálisgép-kapcsolat konfigurálása](connect-vm-instance-configure.md).
- A pont – hely kapcsolat [konfigurálásával](point-to-site-p2s-configure.md)megtudhatja, hogyan CSATLAKOZHAT az SQL felügyelt példányához egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.

Meglévő SQL Server-adatbázis visszaállítása a helyszínről az SQL felügyelt példányára: 
- Az [áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) használja a Azure Database Migration Service az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
- Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](restore-sample-database-quickstart.md) .

Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
