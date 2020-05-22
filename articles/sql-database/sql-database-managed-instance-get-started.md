---
title: 'Azure Portal: felügyelt példány létrehozása'
description: Hozzon létre egy SQL Database felügyelt példányt, hálózati környezetet és ügyfél virtuális gépet a hozzáféréshez.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: e8a0b27f055f39186371e23e46c8b41679e05dea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770108"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Azure SQL Database felügyelt példány létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure SQL Database [felügyelt példányt](sql-database-managed-instance.md) a Azure Portalban.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt:

1. Azure Portal bal oldali menüjében válassza az **Azure SQL** lehetőséget. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az **Azure SQL** kifejezést a keresőmezőbe.
2. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. Azure SQL Database felügyelt példányra vonatkozó további információkat a **felügyelt példányok** csempén látható **Részletek megjelenítése** lehetőség választásával tekintheti meg.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. A **létrehozás Azure SQL Database felügyelt példány** létesítése űrlapon található lapokat a szükséges és választható információk hozzáadásához használhatja. A következő szakaszok ismertetik ezeket a lapokat.

### <a name="basics"></a>Alapvető beállítások

- Adja meg az **alapok** lapon szükséges kötelező információkat. Ez a felügyelt példány kiépítéséhez szükséges minimális információ.

   !["Alapismeretek" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Felügyelt példány neve** | Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre kívánja hozni a felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
   | **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármilyen érvényes Felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza a **felügyelt példány beállítása** lehetőséget a számítási és tárolási erőforrások méretének és a díjszabási szintek áttekintéséhez. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 

   ![Felügyelt példány űrlapja](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Ha a felügyelt példány létrehozása előtt szeretné áttekinteni a beállításokat, válassza a **felülvizsgálat + létrehozás**lehetőséget. Vagy konfigurálja a hálózatkezelési beállításokat a következő lehetőség kiválasztásával **: hálózatkezelés**.

### <a name="networking"></a>Hálózat

- Adja meg a választható adatokat a **hálózatkezelés** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["Hálózatkezelés" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](sql-database-managed-instance-configure-vnet-subnet.md) , mielőtt kiválasztja az új felügyelt példány célhelyének. A felügyelt példányok hálózati környezetének konfigurálásával kapcsolatos tudnivalókat lásd: [virtuális hálózat konfigurálása felügyelt példányhoz](sql-database-managed-instance-connectivity-architecture.md). |
   | **Kapcsolat típusa** | Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: [Azure SQL Database a kapcsolatkérelem-házirend](sql-database-connectivity-architecture.md#connection-policy).|
   | **Nyilvános végpont**  | Válassza az **Engedélyezés** lehetőséget. | Ahhoz, hogy egy felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell ezt a beállítást. | 
   | **Hozzáférés engedélyezése** (ha a **nyilvános végpont** engedélyezve van) | Válasszon egyet a lehetőségek közül.   |A portál felhasználói felülete lehetővé teszi egy biztonsági csoport nyilvános végponttal való konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>**Azure-szolgáltatások**: ezt a lehetőséget akkor javasoljuk, ha Power bi vagy egy másik több-bérlős szolgáltatásból csatlakozik. </li> <li> **Internet**: tesztelési célokra használható, ha gyorsan el szeretné végezni a felügyelt példányok üzembe helyezését. Éles környezetekhez nem ajánlott. </li> <li> **Nincs hozzáférés**: Ez a beállítás egy **megtagadási** biztonsági szabályt hoz létre. Módosítsa ezt a szabályt úgy, hogy egy nyilvános végponton keresztül elérhetővé váljon a felügyelt példány. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért tekintse meg a [Azure SQL Database felügyelt példány biztonságos használata nyilvános végponttal](sql-database-managed-instance-public-endpoint-securely.md)című témakört.|

- A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. Vagy konfigurálja a további egyéni beállításokat a **Tovább: további beállítások**lehetőség kiválasztásával.

### <a name="additional-settings"></a>További beállítások

- Adja meg a választható információkat a **További beállítások** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat fogja alkalmazni.

   !["További beállítások" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Az alábbi táblázat az ezen a lapon szükséges információkra mutató hivatkozásként használható.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Rendezés** | Válassza ki a felügyelt példányhoz használni kívánt rendezést. Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a használatával, `SELECT SERVERPROPERTY(N'Collation')` és használja ezt az értéket.| További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Időzóna** | Válassza ki azt az időzónát, amelyet a felügyelt példány figyelembe fog venni.|További információ: [időzóna](sql-database-managed-instance-timezone.md).|
   | **Használat feladatátvételi másodlagosként** | Válassza az **Igen** lehetőséget. | Ezzel a beállítással engedélyezheti a felügyelt példány másodlagos feladatátvételi csoportként való használatát.|
   | **Elsődleges felügyelt példány** (ha a **feladatátvétel másodlagos** beállítása az **Igen**értékre van állítva) | Válasszon egy meglévő elsődleges felügyelt példányt, amely ugyanabban a DNS-zónában lesz csatlakoztatva, mint a létrehozandó felügyelt példány. | Ez a lépés lehetővé teszi a feladatátvételi csoport létrehozás utáni konfigurációját. További információ: [oktatóanyag: SQL Database felügyelt példány hozzáadása egy feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Felülvizsgálat + létrehozás

5. A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** fület a lehetőségek áttekintéséhez.

   ![Felügyelt példányok áttekintésére és létrehozására szolgáló lap](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Válassza a **Létrehozás** lehetőséget a felügyelt példány kiépítés indításához.

> [!IMPORTANT]
> A felügyelt példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő: [felügyelt példányok kezelési műveletei](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Központi telepítési folyamat figyelése

7. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

   ![Felügyelt példány központi telepítésének telepítési folyamata](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Válassza a **telepítés folyamatban** van az értesítésben a felügyelt példányok ablak megnyitásához és a telepítési folyamat további figyeléséhez. 

> [!TIP]
> Ha bezárta a webböngészőt, vagy áthelyezte az üzembe helyezési folyamat képernyőjéről, kövesse az alábbi lépéseket az üzembe helyezési folyamat ablakának megkereséséhez:
> 1. A Azure Portalban nyissa meg az erőforráscsoportot (az **alapok** lapon), amelyhez felügyelt példányt helyez üzembe.
> 2. Válassza a **központi telepítések**lehetőséget.
> 3. Válassza ki a felügyelt példány központi telepítési műveletét folyamatban.

> [!IMPORTANT]
> A felügyelt példányok létrehozási állapotának lekéréséhez **olvasási engedéllyel** kell rendelkeznie az erőforráscsoporthoz. Ha nem rendelkezik ezzel az engedéllyel, vagy visszavonja azt, amíg a felügyelt példány a létrehozási folyamatban van, akkor ez a felügyelt példány nem látható az erőforráscsoport-telepítések listájában.
>

## <a name="post-deployment-operations"></a>Üzembe helyezés utáni műveletek

A létrehozott erőforrások áttekintéséhez, a hálózati beállítások finomhangolásához és a gazdagép kapcsolati adatainak lekéréséhez kövesse az ebben a szakaszban ismertetett lépéseket.

### <a name="view-resources-created"></a>Létrehozott erőforrások megtekintése

A felügyelt példány sikeres üzembe helyezése után a létrehozott erőforrások megtekintése:

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot. Tekintse meg a [felügyelt példány létrehozása](#create-a-managed-instance) rövid útmutatójában létrehozott erőforrásait.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Hálózati beállítások megtekintése és finomhangolása

A hálózati beállítások finomhangolásához ellenőrizze a következőket:

1. Válassza ki az útválasztási táblázatot az Ön számára létrehozott felhasználó által megadott útvonal (UDR) áttekintéséhez.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

2. Az útválasztási táblázatban tekintse át azokat a bejegyzéseket, amelyek a felügyelt példány virtuális hálózatán belüli és onnan érkező forgalmat irányítják. Ha manuálisan hozza létre vagy konfigurálja az útválasztási táblázatot, győződjön meg róla, hogy létrehozza ezeket a bejegyzéseket a felügyelt példány útválasztási táblájában.

   ![Felügyelt példány alhálózatának helyire való bejegyzése](./media/sql-database-managed-instance-get-started/udr.png)

3. Térjen vissza az erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot.

   ![Hálózati biztonsági csoport](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Tekintse át a bejövő és kimenő biztonsági szabályokat. 

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Ha konfigurálta a nyilvános végpontot a felügyelt példányhoz, meg kell nyitnia a portokat, hogy a hálózati forgalom engedélyezze a nyilvános internetről a felügyelt példányhoz való csatlakozást, további információért lásd: [nyilvános végpont konfigurálása felügyelt](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) példányhoz.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Kapcsolat adatainak lekérése felügyelt példányra

A felügyelt példányhoz való kapcsolódáshoz kövesse az alábbi lépéseket az állomásnév és a teljes tartománynév (FQDN) lekéréséhez:

1. Térjen vissza az erőforráscsoporthoz, és válassza ki a felügyelt példányt.

   ![Felügyelt példány az erőforráscsoporthoz](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja a felügyelt példány állomásnevét a következő rövid útmutatóban való használathoz.

   ![Állomásnév](./media/sql-database-managed-instance-get-started/host-name.png)

   A másolt érték a felügyelt példányhoz való kapcsolódáshoz használható teljes tartománynevet (FQDN) jelöli. A következőhöz hasonló példa: *your_host_name. a1b2c3d4e5f6. database. Windows. net*.

## <a name="next-steps"></a>További lépések

A felügyelt példányokhoz való kapcsolódással kapcsolatos tudnivalók:
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- A felügyelt példányok Azure-beli virtuális gépekről történő csatlakoztatását bemutató rövid útmutató: Azure-beli virtuálisgép- [kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
- A pont – hely kapcsolat [konfigurálásával](sql-database-managed-instance-configure-p2s.md)megtudhatja, hogyan csatlakozhat egy felügyelt példányhoz egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.

Meglévő SQL Server-adatbázis visszaállítása a helyszínről a felügyelt példányra: 
- Az [áttelepítéshez](../dms/tutorial-sql-server-to-managed-instance.md) használja a Azure Database Migration Service az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
- Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](sql-database-managed-instance-get-started-restore.md) .

A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
