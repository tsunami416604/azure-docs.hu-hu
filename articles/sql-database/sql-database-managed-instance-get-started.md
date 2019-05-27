---
title: 'Az Azure Portalon: Az SQL Database felügyelt példány létrehozása |} A Microsoft Docs'
description: Hozzon létre egy felügyelt SQL Database-példány, a hálózati környezet és a hozzáférési VM-ügyfél.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: f54cea75e6f7866f1be41d3b0f82393af2235a0a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949873"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Egy Azure SQL Database felügyelt példány létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure SQL Database [felügyelt példány](sql-database-managed-instance.md) az Azure Portalon.

> [!IMPORTANT]
> További korlátozások: [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott típusú előfizetésessel](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt.

1. Válassza ki **erőforrás létrehozása** az Azure portal bal felső sarkában.
2. Keresse meg **felügyelt példány**, majd válassza ki **Azure SQL felügyelt példánya**.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Töltse ki a **SQL felügyelt példánya** űrlapján az alábbi táblázatban szereplő információk segítségével a kért adatokat.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetés. | Egy előfizetés, amely új erőforrásokat hozhat létre ahhoz. |
   |**Felügyelt példány neve**|Bármely érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a "serveradmin", mert ez egy fenntartott kiszolgálói szintű szerepkört.|
   |**Jelszó**|Bármely érvényes jelszó.|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Időzóna**|Az időzóna által, a felügyelt példány.|További információkért lásd: [időzónák](sql-database-managed-instance-timezone.md).|
   |**Rendezés**|A rendezést, amelyet a felügyelt példány használni szeretne.|Ha az SQL Server adatbázisok telepít át, ellenőrizze a forrás-rendezés használatával `SELECT SERVERPROPERTY(N'Collation')` , és ezt az értéket használja. További információk a Rendezés: [beállítása és módosítása a kiszolgálói rendezés](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Hely**|A hely, amelyben szeretné létrehozni a következő felügyelt példányt.|Régiókkal kapcsolatos információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).|
   |**Virtuális hálózat**|Ezek közül bármelyikre **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózatot, és az alhálózatot.| Ha a hálózat vagy alhálózat nem használható, kell lennie [módosítani kell a hálózati követelményeknek megfelelő](sql-database-managed-instance-configure-vnet-subnet.md) az új felügyelt példány cél kiválasztása előtt. A hálózati környezet a felügyelt példány konfigurálása követelményeivel kapcsolatos információkért lásd: [konfigurálnia kell egy virtuális hálózatot a felügyelt példány](sql-database-managed-instance-connectivity-architecture.md). |
   |**Nyilvános végpont engedélyezése**   |Ezt a beállítást választva nyilvános végpont engedélyezése   |A nyilvános adatokat végponton keresztül érhető el a felügyelt példány **engedélyezése a nyilvános végponthoz** ellenőrizni kell.| 
   |**Engedélyezze a hozzáférést**   |Válasszon a lehetőségek közül: <ul> <li>**Azure-szolgáltatások**</li> <li>**Internet**</li> <li>**Nincs hozzáférés**</li></ul>   |Portál lehetővé teszi a nyilvános végponthoz konfigurálása biztonsági csoport. </br> </br> A forgatókönyv alapján, válassza ki az alábbi lehetőségek közül: </br> <ul> <li>Azure-szolgáltatások – ajánlott, ha a Kapcsolódás a Power bi-ban vagy egyéb több-bérlős szolgáltatás. </li> <li> Internet - tesztelési célokra, ha meg szeretné gyorsan helyezzen üzembe egy felügyelt példány használatát. Éles környezetben nem ajánlott. </li> <li> Nincs hozzáférés – Ez a beállítás egy megtagadási szabályt hoz létre. Ez a szabály módosítása annak érdekében, hogy a felügyelt példány elérhetővé nyilvános végponton keresztül kell. </li> </ul> </br> A nyilvános végponthoz biztonsági további információkért lásd: [Azure SQL Database felügyelt példány nyilvános végponttal rendelkező biztonságosan](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Kapcsolat típusa**|Proxy és a egy átirányítási kapcsolat típusa közül választhat.|Kapcsolattípusok kapcsolatos további információkért lásd: [Azure SQL Database-kapcsolat házirend](sql-database-connectivity-architecture.md#connection-policy).|
   |**Erőforráscsoport**|Egy új vagy meglévő erőforráscsoportot.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|

   ![felügyelt példány űrlap](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Egy másodlagos példány feladatátvételi csoportot a következő felügyelt példányt használ, válassza ki a kivételt, és adja meg a DnsAzurePartner felügyelt példányát. Ez a funkció előzetes verzióban érhető el, és nem jelenik meg az alábbi képernyőfelvételen.
6. Válassza ki **tarifacsomag** méretű számítási és tárolási erőforrások és a lehetséges tarifacsomagok áttekintéséhez. Az alapértelmezett érték az általános célú tarifacsomag, amely 32 GB memóriával és 16 virtuális maggal rendelkezik.
7. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát.
8. Ha elkészült, válassza ki a **alkalmaz** a mentéshez. 
9. Válassza ki **létrehozás** a felügyelt példány üzembe helyezéséhez.
10. Válassza ki a **értesítések** ikonra az üzembe helyezés állapotának megtekintéséhez.

    ![felügyelt példány üzembe helyezés folyamatban](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Válassza ki **üzembe helyezés folyamatban** , amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot a felügyelt példány ablakának megnyitásához. 

> [!IMPORTANT]
> Az első példánynál az alhálózatok üzembe helyezés ideje általában sokkal hosszabb, mint a további példányok. Ne szakítsa meg a központi telepítési műveletet, mert a vártnál tovább tart. A második felügyelt példány létrehozása az alhálózat csak néhány percet vesz igénybe.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Tekintse át az erőforrásokat, és a teljes kiszolgálónév lekérése

Miután az üzembe helyezés sikeres, tekintse át a létrehozott erőforrásokat, és használni a későbbi rövid útmutatók a teljes kiszolgálónév lekérése.

1. Nyissa meg az erőforráscsoport, a felügyelt példány. Annak az Ön számára létrehozott erőforrások megtekintése a [felügyelt példány létrehozása](#create-a-managed-instance) rövid.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

2. Jelölje be az útvonaltáblát tekintse át az Ön számára létrehozott felhasználó által megadott útvonal (UDR) táblában.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

3. Az útválasztási táblázatban tekintse át a bejegyzések irányíthatja a forgalmat, és a felügyelt példány virtuális hálózaton belül. Ha hoz létre, vagy manuális konfigurálása az útválasztási táblázatot, ne felejtse el ezeket a bejegyzéseket létrehozni az útvonaltáblában lévő kell lennie.

   ![A felügyelt példány alhálózatára helyi bejegyzés](./media/sql-database-managed-instance-get-started/udr.png)

4. Térjen vissza az erőforráscsoportot, és válassza ki a hálózati biztonsági csoport tekintse át a biztonsági szabályokat.

   ![Hálózati biztonsági csoport](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Tekintse át a kimenő és bejövő biztonsági szabályokat. Ha konfigurálta a nyilvános végpontokat a felügyelt példány, tekintse meg a cikket [konfigurálja a nyilvános végponthoz](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) további információt.

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Térjen vissza az erőforráscsoportot, és válassza ki azt a felügyelt példányt.

   ![Felügyelt példány](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Az a **áttekintése** lapra, keresse meg a **gazdagép** tulajdonság. Másolja a következő gyorsútmutatóval a teljesen minősített állomásnév cím a felügyelt példány használatra.

   ![Állomásnév](./media/sql-database-managed-instance-get-started/host-name.png)

   A név a következőhöz hasonló, **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan csatlakozhat a felügyelt példány kapcsolatos:
  - Az alkalmazások csatlakozási lehetőségek áttekintését lásd: [összekötheti saját alkalmazásait a felügyelt példány az](sql-database-managed-instance-connect-app.md).
  - Rövid útmutató bemutatja, hogyan szeretne csatlakozni a felügyelt példány az Azure virtuális gépből, lásd: [egy Azure-beli Virtuálisgép-kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - Rövid útmutató bemutatja, hogyan szeretne csatlakozni a felügyelt példány egy a helyszíni ügyfélszámítógépről egy pont – hely kapcsolat használatával, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Meglévő SQL Server-adatbázis visszaállítása helyi a felügyelt példány: 
    - Használja a [Azure Database Migration Service (DMS) az áttelepítéshez](../dms/tutorial-sql-server-to-managed-instance.md) vissza egy adatbázis biztonságimásolat-fájlt. 
    - Használja a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md) vissza egy adatbázis biztonságimásolat-fájlt.
- A speciális hibaelhárítási beépített intelligenciával felügyelt példány adatbázis-teljesítmény figyelését: [figyelése Azure SQL Database az Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
