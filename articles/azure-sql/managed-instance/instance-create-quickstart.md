---
title: 'Rövid útmutató: Azure SQL felügyelt példány létrehozása (portál)'
description: Hozzon létre egy Azure SQL felügyelt példányt, egy hálózati környezetet és egy ügyfél virtuális gépet, amellyel hozzáférhet a rövid útmutató Azure Portal használatával.
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
ms.openlocfilehash: cc3a25992297dd8deb02deb2c561cad4b53e318b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113746"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Rövid útmutató: Azure SQL felügyelt példány létrehozása
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy [felügyelt Azure SQL-példányt](sql-managed-instance-paas-overview.md) a Azure Portal.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](resource-limits.md#supported-regions) és [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types).

## <a name="create-sql-managed-instance"></a>Felügyelt SQL-példány létrehozása

Az alábbi lépéseket követve hozhat létre egy Azure SQL-alapú összekeveredésű példányt: 

### <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
1. Azure Portal bal oldali menüjében válassza az **Azure SQL** lehetőséget. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az **Azure SQL** kifejezést a keresőmezőbe.
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A felügyelt Azure SQL-példányokkal kapcsolatos további információkat az **SQL felügyelt példányának** csempén található **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
1. Kattintson a **Létrehozás** gombra.

   ![SQL felügyelt példány létrehozása](./media/instance-create-quickstart/create-managed-instance.png)

4. A szükséges és választható információk hozzáadásához használja az **Azure SQL felügyelt példány** létesítési űrlapjának létrehozásához használt lapokat. A következő szakaszok ismertetik ezeket a lapokat.

### <a name="basics-tab"></a>Alapbeállítások lap

- Adja meg az **alapok** lapon szükséges kötelező információkat. Ez az SQL-felügyelt példány kiépítéséhez szükséges minimális információ.

   ![Az SQL felügyelt példányainak létrehozásához szükséges alapismeretek lap](./media/instance-create-quickstart/mi-create-tab-basics.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Felügyelt példány neve** | Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre kívánja hozni az SQL felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
   | **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármilyen érvényes Felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza a **felügyelt példány beállítása** lehetőséget a számítási és tárolási erőforrások méretének és a díjszabási szintek áttekintéséhez. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 

   ![Felügyelt példány űrlapja](./media/instance-create-quickstart/mi-create-tab-configure-performance.png)

- Ha a felügyelt SQL-példány létrehozása előtt szeretné áttekinteni a beállításokat, válassza a **felülvizsgálat + létrehozás**lehetőséget. Vagy konfigurálja a hálózatkezelési beállításokat a következő lehetőség kiválasztásával **: hálózatkezelés**.

### <a name="networking-tab"></a>Hálózatkezelés lap

- Adja meg a választható adatokat a **hálózatkezelés** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["Hálózatkezelés" lap a felügyelt SQL-példány létrehozásához](./media/instance-create-quickstart/mi-create-tab-networking.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](vnet-existing-add-subnet.md) , mielőtt kiválasztja célként az új SQL felügyelt példányhoz. További információ a hálózati környezet SQL felügyelt példányhoz való konfigurálásának követelményeiről: [virtuális hálózat konfigurálása SQL felügyelt példányhoz](connectivity-architecture-overview.md). |
   | **Kapcsolat típusa** | Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: az [Azure SQL felügyelt példányának kapcsolattípus](../database/connectivity-architecture.md#connection-policy).|
   | **Nyilvános végpont**  | Válassza az **Engedélyezés** lehetőséget. | Ahhoz, hogy egy SQL felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell ezt a beállítást. | 
   | **Hozzáférés engedélyezése** (ha a **nyilvános végpont** engedélyezve van) | Válasszon egyet a lehetőségek közül.   |A portál felhasználói felülete lehetővé teszi egy biztonsági csoport nyilvános végponttal való konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>**Azure-szolgáltatások**: ezt a lehetőséget akkor javasoljuk, ha Power bi vagy egy másik több-bérlős szolgáltatásból csatlakozik. </li> <li> **Internet**: tesztelési célokra használható, ha gyorsan el szeretné végezni egy FELÜGYELt SQL-példány gyors üzembe helyezését. Éles környezetekhez nem ajánlott. </li> <li> **Nincs hozzáférés**: Ez a beállítás egy **megtagadási** biztonsági szabályt hoz létre. Módosítsa ezt a szabályt úgy, hogy egy nyilvános végponton keresztül elérhető SQL felügyelt példányt hozzon nyilvánosságra. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért tekintse meg az [Azure SQL felügyelt példány biztonságos használata nyilvános végponttal](public-endpoint-overview.md)című témakört.|

- Az SQL felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget. Vagy konfigurálja a további egyéni beállításokat a **Tovább: további beállítások**lehetőség kiválasztásával.

### <a name="additional-settings"></a>További beállítások

- Adja meg a választható információkat a **További beállítások** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["További beállítások" lap a felügyelt SQL-példány létrehozásához](./media/instance-create-quickstart/mi-create-tab-additional-settings.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Rendezés** | Válassza ki az SQL felügyelt példányához használni kívánt rendezést. Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a használatával, `SELECT SERVERPROPERTY(N'Collation')` és használja ezt az értéket.| További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Időzóna** | Válassza ki azt az időzónát, amelyet az SQL felügyelt példánya figyelembe fog venni.|További információ: [időzóna](timezones-overview.md).|
   | **Használat feladatátvételi másodlagosként** | Válassza az **Igen** lehetőséget. | Ezzel a beállítással engedélyezheti az SQL felügyelt példány másodlagos feladatátvételi csoportként való használatát.|
   | **Elsődleges SQL felügyelt példánya** (ha a **másodlagos feladatátvételi** beállítás értéke **Igen**) | Válasszon egy meglévő elsődleges SQL felügyelt példányt, amelyet ugyanahhoz a DNS-zónához kíván csatlakoztatni a létrehozandó SQL felügyelt példánnyal. | Ez a lépés lehetővé teszi a feladatátvételi csoport létrehozás utáni konfigurációját. További információ: [oktatóanyag: SQL Database SQL felügyelt példány hozzáadása feladatátvételi csoporthoz](failover-group-add-instance-tutorial.md).|

## <a name="review--create"></a>Felülvizsgálat + létrehozás

1. A felügyelt SQL-példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** fület.

   ![Lap a felügyelt SQL-példányok áttekintéséhez és létrehozásához](./media/instance-create-quickstart/mi-create-tab-review-create.png)

1. Válassza a **Létrehozás** lehetőséget a felügyelt SQL-példány kiépítés indításához.

> [!IMPORTANT]
> A felügyelt SQL-példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő SQL felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő: [SQL felügyelt példányok kezelési műveletei](sql-managed-instance-paas-overview.md#management-operations).

## <a name="monitor-deployment-progress"></a>Központi telepítési folyamat figyelése

1. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

   ![SQL felügyelt példány központi telepítésének telepítési folyamata](./media/instance-create-quickstart/mi-create-deployment-in-progress.png)

1. Válassza a **telepítés folyamatban** van az értesítésben az SQL felügyelt példány ablakának megnyitásához és a telepítési folyamat további figyeléséhez. 

> [!TIP]
> Ha bezárta a webböngészőt, vagy áthelyezte a telepítési folyamat képernyőről, kövesse az alábbi lépéseket az üzembe helyezési folyamat helyének megkereséséhez:
> 1. A Azure Portalban nyissa meg az erőforráscsoportot (az **alapok** lapon), amelyre telepíteni kívánja az SQL felügyelt példányát.
> 2. Válassza a **központi telepítések**lehetőséget.
> 3. Válassza ki az SQL felügyelt példány központi telepítési műveletét folyamatban.

> [!IMPORTANT]
> Ahhoz, hogy a felügyelt SQL-példányok létrehozásának állapota lekérdezhető legyen, **olvasási engedéllyel** kell rendelkeznie az erőforráscsoporthoz. Ha nem rendelkezik ezzel az engedéllyel vagy visszavonással, miközben az SQL felügyelt példánya létrehozási folyamatban van, akkor az SQL felügyelt példány nem látható az erőforráscsoport-telepítések listájában.
>

## <a name="view-resources-created"></a>Létrehozott erőforrások megtekintése

Az SQL felügyelt példányának sikeres üzembe helyezése után a létrehozott erőforrások megtekintése:

1. Nyissa meg az SQL felügyelt példányának erőforrás-csoportját. 

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
> Ha nyilvános végpontot konfigurált az SQL felügyelt példányához, meg kell nyitnia a portokat, hogy engedélyezze a hálózati forgalom számára a nyilvános internetről felügyelt példányhoz való csatlakozást, további információért lásd: [nyilvános végpont konfigurálása SQL felügyelt](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) példányhoz.
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Kapcsolat adatainak beolvasása a felügyelt SQL-példányhoz

A felügyelt SQL-példányhoz való kapcsolódáshoz kövesse az alábbi lépéseket az állomásnév és a teljes tartománynév (FQDN) lekéréséhez:

1. Térjen vissza az erőforráscsoporthoz, és válassza ki az SQL felügyelt példányát.

   ![SQL felügyelt példány az erőforráscsoporthoz](./media/instance-create-quickstart/managed-instance.png)

2. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja az SQL felügyelt példány állomásnevét a következő rövid útmutatóban való használatra.

   ![Állomásnév](./media/instance-create-quickstart/host-name.png)

   A másolt érték egy teljes tartománynevet (FQDN) jelöl, amely az SQL felügyelt példányhoz való kapcsolódáshoz használható. A következőhöz hasonló példa: *your_host_name. a1b2c3d4e5f6. database. Windows. net*.

## <a name="next-steps"></a>További lépések

További információ a felügyelt SQL-példányokhoz való kapcsolódásról:
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Az Azure-beli virtuális gépekről származó SQL felügyelt példányokhoz való kapcsolódást bemutató rövid útmutató: [Azure-beli Virtuálisgép-kapcsolat konfigurálása](connect-vm-instance-configure.md).
- A pont – hely kapcsolat [konfigurálásával](point-to-site-p2s-configure.md)megtudhatja, hogyan CSATLAKOZHAT egy SQL felügyelt példányhoz egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.

Meglévő SQL Server-adatbázis visszaállítása a helyszínről egy SQL felügyelt példányra: 
- Az [áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) használja a Azure Database Migration Service az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
- Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](restore-sample-database-quickstart.md) .

Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
