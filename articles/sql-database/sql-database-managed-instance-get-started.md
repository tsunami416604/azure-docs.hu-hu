---
title: 'Az Azure Portalon: SQL felügyelt példány létrehozása |} A Microsoft Docs'
description: Hozzon létre egy felügyelt SQL-példány, a hálózati környezet és a hozzáférési VM-ügyfél.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 481c82eb74bcf80c3d0546324009ec0bf6495cfb
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587055"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Egy Azure SQL Database felügyelt példány létrehozása

Ez a rövid útmutató végigvezeti azon, hogyan hozhat létre az Azure SQL Database [felügyelt példány](sql-database-managed-instance.md) az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt.

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.
2. Keresse meg **felügyelt példány** majd **Azure SQL felügyelt példánya**.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Töltse ki a **SQL felügyelt példánya** űrlapját a szükséges információkat, az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az Ön előfizetése | Olyan előfizetés, amely rendelkezik új erőforrások létrehozásához szükséges engedéllyel |
   |**Felügyelt példány neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd: [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd: [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Ne használja a "serveradmin", mivel ez egy fenntartott kiszolgálói szintű szerepkört.|
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Rendezés**|A rendezést, hogy a felügyelt példány használni kívánt|Ha az SQL Server adatbázisok áttelepítése, ellenőrizze a forrás rendezést használ `SELECT SERVERPROPERTY(N'Collation')` , és ezt az értéket használja. További információk a Rendezés: [kiszolgálószintű rendezések](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Hely**|A helyét, amelyben meg szeretné a felügyelt példány létrehozása|Régiókkal kapcsolatos információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).|
   |**Virtuális hálózat**|Ezek közül bármelyikre **új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózatot, és az alhálózatot.| Egy alhálózaton szürke esetén kell kell [módosítani kell a hálózati követelményeknek megfelelő](sql-database-managed-instance-configure-vnet-subnet.md) az új felügyelt példány cél kiválasztása előtt. A felügyelt példány a hálózati környezet konfigurálásához vonatkozó követelményekkel kapcsolatos információkért lásd: [a felügyelt példány virtuális hálózat konfigurálása](sql-database-managed-instance-connectivity-architecture.md). |
   |**Erőforráscsoport**|Egy új vagy létező erőforráscsoport|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|

   ![felügyelt példány űrlap](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Egy másodlagos példány feladatátvételi csoportot a következő felügyelt példányt használ, válassza ki a kivételt, és adja meg a DnsAzurePartner felügyelt példányát. Ez a funkció előzetes verzióban érhető el, és nem a kísérő képernyőképen látható.
6. Válassza ki **tarifacsomag** a számítási és tárolási erőforrások méretezéséhez, valamint a lehetséges tarifacsomagok áttekintéséhez. Az alapértelmezett érték az általános célú tarifacsomag, amely 32 GB memóriával és 16 virtuális maggal rendelkezik.
7. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát.
8. Amikor végzett, válassza ki a **alkalmaz** a mentéshez.  
9. Válassza ki **létrehozás** a felügyelt példány üzembe helyezéséhez.
10. Válassza ki a **értesítések** ikonra kattintva megtekintheti az üzembe helyezési állapotát.

    ![felügyelt példány üzembehelyezési folyamata](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Válassza ki **üzembe helyezés folyamatban** , amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot a felügyelt példány ablakának megnyitásához.

> [!IMPORTANT]
> Az első példánynál az alhálózatok üzembe helyezés ideje általában sokkal hosszabb, mint a további példányok. Ne szakítsa meg a központi telepítési műveletet, mert a vártnál tovább tart. A második felügyelt példány létrehozása az alhálózat csak néhány percet vesz igénybe.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Tekintse át az erőforrásokat, és a teljes kiszolgálónév lekérése

Az üzembe helyezés sikeres befejezése után tekintse át a létrehozott erőforrásokat, és kérje le a kiszolgáló teljes nevét, hogy a későbbiekben felhasználhassa gyorskonfigurálások alkalmával.

1. Az erőforráscsoport, a felügyelt példány megnyithatja és megtekintheti annak az Ön számára létrehozott erőforrást a [felügyelt példány létrehozása](#create-a-managed-instance) rövid.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

2. Jelölje be az útvonaltáblát tekintse át a felhasználó által megadott útvonal udr-t) az Ön számára létrehozott táblában.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

3. Az útválasztási táblázatban tekintse át a bejegyzések irányíthatja a forgalmat, és a felügyelt példány virtuális hálózaton belül. Ha létrehozásakor és manuális konfigurálása az útválasztási táblázatot, ne felejtse el ezeket a bejegyzéseket létrehozni az útvonaltáblában lévő kell lennie.

   ![MI alhálózat helyi belépési](./media/sql-database-managed-instance-get-started/udr.png)

4. Térjen vissza az erőforráscsoportot, és válassza ki a hálózati biztonsági csoport tekintse át a biztonsági szabályokat.

   ![Network-security-group](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Tekintse át a kimenő és bejövő biztonsági szabályokat.

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Térjen vissza az erőforráscsoportot, és válassza ki azt a felügyelt példányt.

   ![Felügyelt példány](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Az a **áttekintése** lapra, keresse meg a **gazdagép** tulajdonság és másolja a teljesen minősített gazdagép oldja meg a felügyelt példány a következő gyorsútmutatóval használható.

   ![Állomásnév](./media/sql-database-managed-instance-get-started/host-name.png)

   A név a következőhöz hasonló, **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>További lépések

- Csatlakozhat a felügyelt példány kapcsolatos további információkért lásd:
  - Az alkalmazások csatlakozási lehetőségek áttekintését lásd: [összekötheti saját alkalmazásait a felügyelt példány az](sql-database-managed-instance-connect-app.md).
  - A rövid útmutató, amely hogyan csatlakozhat a felügyelt példány az Azure virtuális gépből, lásd: [egy Azure-beli Virtuálisgép-kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - A rövid útmutató, amely hogyan csatlakozhat a felügyelt példány egy pont – hely kapcsolattal a helyszíni ügyfélszámítógépről, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Ha vissza kíván állítani egy meglévő SQL-adatbázist egy felügyelt példányra, használhatja az [Azure Database Migration Services (DMS) migrálásához](../dms/tutorial-sql-server-to-managed-instance.md) szolgáltatást, amellyel a visszaállítás egy adatbázis biztonságimásolat-fájljából történik, vagy a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md), amellyel egy adatbázis biztonságimásolat-fájljából végezhet visszaállítást.
- A speciális hibaelhárítási beépített intelligenciával felügyelt példány adatbázis-teljesítmény figyelését: [figyelése Azure SQL Database az Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md)
