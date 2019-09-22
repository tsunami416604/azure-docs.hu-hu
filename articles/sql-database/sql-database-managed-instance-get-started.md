---
title: 'Azure Portal: SQL Database felügyelt példány létrehozása | Microsoft Docs'
description: Hozzon létre egy SQL Database felügyelt példányt, hálózati környezetet és ügyfél virtuális gépet a hozzáféréshez.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 5f37ca8e22b07e39eda87e11f52358e1d1497c60
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178501"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Azure SQL Database felügyelt példány létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure SQL Database [felügyelt példányt](sql-database-managed-instance.md) a Azure Portalban.

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az Azure SQL nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe.
2. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. Azure SQL Database felügyelt példányra vonatkozó további információkat a **felügyelt példányok** csempén látható **Részletek megjelenítése** lehetőség választásával tekintheti meg.
3. Válassza a **Létrehozás**lehetőséget:

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Töltse ki a **létrehozás SQL Database felügyelt példány** létesítése űrlapot az alapszintű lapon megjelenő információk kitöltésével. A Hálózatkezelés és a speciális beállítások segítségével konfigurálhatja a további beállításokat.

### <a name="basic-tab"></a>Alapszintű lap

Töltse ki az alaplapon szükséges kötelező **adatokat az alábbi** táblázat használatával. A felügyelt példányok kiépítéséhez minimálisan szükséges információk.

   ![Felügyelt példány létrehozása – alapszintű lap](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   | **Felügyelt példány neve** | Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre kívánja hozni a felügyelt példányt.|További információ a régiókkal kapcsolatban: [Azure-régiók](https://azure.microsoft.com/regions/).|
| **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármilyen érvényes Felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza a **felügyelt példány konfigurálása** a számítási és tárolási erőforrások méretének és a díjszabási szintek áttekintésének beállítását.
- A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát.
- Ha elkészült, kattintson az **alkalmaz** gombra a kijelölés mentéséhez. 

  ![Felügyelt példány űrlapja](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

A felügyelt példány létrehozása előtt a **felülvizsgálat + létrehozás** lehetőségre kattintva áttekintheti a kiválasztott beállításokat, vagy egyéni hálózatkezelést is **konfigurálhat a következőre kattintva: Hálózatkezelés**.

### <a name="networking-tab"></a>Hálózatkezelés lap

Adja meg a választható adatokat a hálózatkezelés lapon az alábbi táblázat használatával. Ha ez az információ nincs megadva, az alapértelmezett beállítások lesznek alkalmazva.

   ![Felügyelt példány létrehozása hálózatkezelés lap](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza az **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, akkor azt módosítani kell, [hogy megfeleljen a hálózati követelményeknek](sql-database-managed-instance-configure-vnet-subnet.md) , mielőtt kiválasztja az új felügyelt példány célhelyének. A felügyelt példányok hálózati környezetének konfigurálásával kapcsolatos tudnivalókat lásd: [virtuális hálózat konfigurálása felügyelt példányhoz](sql-database-managed-instance-connectivity-architecture.md). |
   | **Kapcsolattípus** | Válasszon egy proxy és egy átirányítási kapcsolat típusa közül.|További információ a kapcsolatok típusairól: [Azure SQL Database a kapcsolatkérelem-házirend](sql-database-connectivity-architecture.md#connection-policy).|
   | **Nyilvános végponthoz**  | Válassza ezt a lehetőséget a nyilvános végpont engedélyezéséhez | Ahhoz, hogy a felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, **engedélyezni** kell a nyilvános végpont kiválasztását. | 
   | **Hozzáférés engedélyezése innen** : (abban az esetben, ha a nyilvános végpont engedélyezve van) | Válasszon egyet a lehetőségek közül: <ul> <li>**Azure-szolgáltatások**</li> <li>**Internet**</li> <li>**Nincs hozzáférés**</li></ul>   |A portál felülete lehetővé teszi a biztonsági csoport nyilvános végponttal történő konfigurálását. </br> </br> A forgatókönyv alapján válasszon a következő lehetőségek közül: </br> <ul> <li>Azure-szolgáltatások – ajánlott Power BI vagy más több-bérlős szolgáltatáshoz való csatlakozáskor. </li> <li> Internet – tesztelési célokra használható, ha gyorsan el szeretné végezni a felügyelt példányok üzembe helyezését. Éles környezetekben való használatra nem ajánlott. </li> <li> Nincs hozzáférés – ez a beállítás egy megtagadási biztonsági szabályt hoz létre. Módosítania kell ezt a szabályt, hogy a felügyelt példány elérhető legyen nyilvános végponton keresztül. </li> </ul> </br> A nyilvános végpontok biztonságával kapcsolatos további információkért lásd: [Azure SQL Database felügyelt példány biztonságos használata nyilvános végponttal](sql-database-managed-instance-public-endpoint-securely.md).|

A felügyelt példány létrehozása előtt a **felülvizsgálat + létrehozás** lehetőséggel áttekintheti a kiválasztott beállításokat, vagy egyéni beállításokat adhat meg a **további beállításokhoz a következő gombra kattintva: További beállítások**.

### <a name="additional-settings-tab"></a>További beállítások lap

A **További beállítások** lapon adja meg a választható adatokat az alábbi táblázat használatával. Ha ez az információ nincs megadva, az alapértelmezett beállítások lesznek alkalmazva.

   ![Felügyelt példány – további beállítások lap létrehozása](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Egybevetés** | A felügyelt példányhoz használni kívánt rendezés.|Ha SQL Serverból telepít át adatbázisokat, ellenőrizze a forrás rendezését a `SELECT SERVERPROPERTY(N'Collation')` használatával, és használja ezt az értéket. További információ a rendezésekről: [a kiszolgáló rendezésének beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|   
   | **Időzóna** | A felügyelt példány által megfigyelt időzóna.|További információ: [időzóna](sql-database-managed-instance-timezone.md).|
   | **Használat feladatátvételi másodlagosként** | Ha a felügyelt példányt a példányok feladatátvételi csoportjának másodlagos használatára szeretné használni, válassza az Igen lehetőséget. |
   | **Elsődleges felügyelt példány** (ha a feladatátvétel másodlagos beállítása az Igen értékre van állítva) | Úgy döntött, hogy az elsődleges felügyelt példány (már létezik), akivel a létrehozandó felügyelt példány ugyanahhoz a DNS-zónához csatlakozik. Ez egy előfeltétel lépés a feladatátvételi csoport konfigurációjának létrehozása utáni létrehozásához. További részletek [: oktatóanyag: SQL Database felügyelt példány hozzáadása egy feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md). |

### <a name="review--create-tab"></a>Felülvizsgálat + Létrehozás lap

1. A felügyelt példány létrehozása előtt válassza a **felülvizsgálat + létrehozás** fület a kiválasztott beállítások áttekintéséhez.

   ![Felügyelt példányok áttekintése és létrehozása lap](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

1. Válassza a **Létrehozás** lehetőséget a felügyelt példány kiépítés indításához.

> [!IMPORTANT]
> A felügyelt példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő: [felügyelt példányok kezelési műveletei](sql-database-managed-instance.md#managed-instance-management-operations).

### <a name="monitor-deployment-progress"></a>Központi telepítési folyamat figyelése

1. A központi telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

    ![Felügyelt példányok telepítési folyamata](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

1. Válassza a **telepítés folyamatban van** az értesítésben a felügyelt példány ablak megnyitásához a telepítési folyamat további figyeléséhez. 

> [!TIP]
> Ha bezárta a webböngészőt, vagy elindult a telepítési folyamat képernyőről, kövesse az alábbi lépéseket az üzembe helyezési folyamat képernyő megkereséséhez:
> 1. A Azure Portal nyissa meg az erőforráscsoportot (az **alapszintű** lapon megadott), amelyhez felügyelt példányt helyez üzembe.
> 2. Válassza a **központi telepítések**lehetőséget.
> 3. Válassza ki a felügyelt példány központi telepítési műveletét folyamatban.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Erőforrások áttekintése és a teljes kiszolgálónév beolvasása

Az üzembe helyezés sikerességét követően tekintse át a létrehozott erőforrásokat, és kérje le a teljes kiszolgálónevet a későbbi gyors útmutatókban való használatra.

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot. Tekintse meg a [felügyelt példány létrehozása](#create-a-managed-instance) rövid útmutatójában létrehozott erőforrásait.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

2. Válassza ki az útválasztási táblázatot az Ön számára létrehozott felhasználó által megadott route (UDR) tábla áttekintéséhez.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

3. Az útválasztási táblázatban tekintse át azokat a bejegyzéseket, amelyek a felügyelt példányok virtuális hálózatán belüli és onnan érkező forgalmat irányítják. Ha manuálisan hozza létre vagy konfigurálja az útválasztási táblázatot, akkor ezeket a bejegyzéseket az útválasztási táblában kell létrehoznia.

   ![Felügyelt példány alhálózatának helyire való bejegyzése](./media/sql-database-managed-instance-get-started/udr.png)

4. Térjen vissza az erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot a biztonsági szabályok áttekintéséhez.

   ![Hálózati biztonsági csoport](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Tekintse át a bejövő és kimenő biztonsági szabályokat. Ha a felügyelt példányhoz nyilvános végpontokat konfigurált, további információért tekintse meg a [nyilvános végpont konfigurálása](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) című cikket.

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Térjen vissza az erőforráscsoporthoz, és válassza ki a felügyelt példányt.

   ![Felügyelt példány](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Az **Áttekintés** lapon keresse meg a **gazdagép** tulajdonságot. Másolja a felügyelt példány teljes állomásnevét a következő rövid útmutatóban való használatra.

   ![Gazdagép neve](./media/sql-database-managed-instance-get-started/host-name.png)

   A név hasonlít a **your_machine_name. a1b2c3d4e5f6. database. Windows. net**névre.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokhoz való kapcsolódással kapcsolatos tudnivalók:
  - Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
  - A felügyelt példányok Azure-beli virtuális gépekről történő csatlakoztatását bemutató rövid útmutató: Azure-beli virtuálisgép- [kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - A pont – hely kapcsolat [konfigurálásával](sql-database-managed-instance-configure-p2s.md)megtudhatja, hogyan csatlakozhat egy felügyelt példányhoz egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.
- Meglévő SQL Server-adatbázis visszaállítása a helyszínről a felügyelt példányra: 
    - Az [áttelepítéshez használja a Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) az adatbázis biztonságimásolat-fájljából való visszaállításhoz. 
    - Az adatbázis-biztonságimásolat-fájlból történő visszaállításhoz használja a [T-SQL Restore parancsot](sql-database-managed-instance-get-started-restore.md) .
- A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
