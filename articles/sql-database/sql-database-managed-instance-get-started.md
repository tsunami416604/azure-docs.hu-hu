---
title: 'Azure Portal: Felügyelt SQL-példány létrehozása | Microsoft Docs'
description: Felügyelt SQL-példányt, hálózati környezetet és ügyféloldali virtuális gépet hozhat létre a hozzáféréshez.
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
ms.date: 11/28/2018
ms.openlocfilehash: d5be25abc634200e0c0afed6946b38fd163fb78e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890500"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rövid útmutató: Felügyelt Azure SQL Database-példány létrehozása

Ez a rövid útmutató lépésről lépésre ismerteti, hogyan hozhat létre egy Azure SQL Database-beli [felügyelt példányt](sql-database-managed-instance.md) az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések azt mutatják be, hogyan hozhat létre egy felügyelt példányt.

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.
2. Keresse meg a **Felügyelt példány** lehetőséget, és válassza a **Felügyelt Azure SQL-példány** elemet.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Töltse ki a **felügyelt példány** űrlapját a szükséges információkat, az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az Ön előfizetése | Olyan előfizetés, amely rendelkezik új erőforrások létrehozásához szükséges engedéllyel |
   |**Felügyelt példány neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a "serveradmin", mivel ez egy fenntartott kiszolgálói szintű szerepkört.|
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Hely**|Az a hely, ahol a felügyelt példányt létre szeretné hozni|A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**Virtuális hálózat**|Ezek közül bármelyikre **új virtuális hálózat létrehozása** vagy egy virtuális hálózatot, amely a korábban létrehozott, amely az űrlap korábban megadott erőforráscsoportban.| További információ virtuális hálózat konfigurálásáról felügyelt példány esetében egyéni beállításokkal: [Felügyelt SQL-példány virtuális hálózat környezeti sablonjának konfigurálása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) a Githubon. A hálózati környezet konfigurálása a felügyelt példány számára vonatkozó követelményekkel kapcsolatos információkért lásd: [virtuális hálózat konfigurálása az Azure SQL Database felügyelt példányába](sql-database-managed-instance-vnet-configuration.md). |
   |**Erőforráscsoport**|Egy új vagy létező erőforráscsoport|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|

   ![felügyelt példány űrlap](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. A felügyelt példány egy másodlagos példány feladatátvételi csoportot használ, válassza ki a kivételt, és adja meg a DnsAzurePartner következő felügyelt példányt. Ez a funkció előzetes verzióban érhető el, és nem a kísérő képernyőképen látható.
6. Válassza ki **tarifacsomag** a számítási és tárolási erőforrások méretezéséhez, valamint a lehetséges tarifacsomagok áttekintéséhez. Az alapértelmezett érték az általános célú tarifacsomag, amely 32 GB memóriával és 16 virtuális maggal rendelkezik.
7. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát.
8. Amikor végzett, válassza ki a **alkalmaz** a mentéshez.  
9. Válassza ki **létrehozás** a felügyelt példány üzembe helyezéséhez.
10. Válassza ki a **értesítések** ikonra kattintva megtekintheti az üzembe helyezési állapotát.

    ![felügyelt példány üzembehelyezési folyamata](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Válassza ki **üzembe helyezés folyamatban** , amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot a felügyelt példány ablakának megnyitásához.

> [!IMPORTANT]
> Az első példánynál az alhálózatok üzembe helyezés ideje általában sokkal hosszabb, mint a további példányok. Ne szakítsa meg a központi telepítési műveletet, mert a vártnál tovább tart. Eltarthat néhány percig, amíg az alhálózatban létrejön a második felügyelt példány.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Tekintse át az erőforrásokat, és a teljes kiszolgálónév lekérése

Az üzembe helyezés sikeres befejezése után tekintse át a létrehozott erőforrásokat, és kérje le a kiszolgáló teljes nevét, hogy a későbbiekben felhasználhassa gyorskonfigurálások alkalmával.

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot, és tekintse meg az erőforrásokat, amelyeket az imént hozott létre a [Felügyelt példány létrehozása](#create-a-managed-instance) gyorskonfigurálással.

2. Válassza ki azt a felügyelt példányt.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

3. Az a **áttekintése** lapra, keresse meg a **gazdagép** tulajdonság és másolja a teljesen minősített gazdagép oldja meg a felügyelt példány esetében.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/host-name.png)

   A név a következőhöz hasonló, **your_machine_name.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>További lépések

- További információkért a felügyelt példányokhoz való csatlakozásról lásd:
  - Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
  - Rövid útmutató arról, hogyan csatlakozhat felügyelt példányokhoz Azure-beli virtuális gépről: [Azure-beli virtuálisgép-kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - Rövid útmutató arról, hogyan csatlakozhat felügyelt példányokhoz helyi ügyfélszámítógépről pont-hely típusú kapcsolattal: [Pont-hely típusú kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Ha vissza kíván állítani egy meglévő SQL-adatbázist egy felügyelt példányra, használhatja az [Azure Database Migration Services (DMS) migrálásához](../dms/tutorial-sql-server-to-managed-instance.md) szolgáltatást, amellyel a visszaállítás egy adatbázis biztonságimásolat-fájljából történik, vagy a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md), amellyel egy adatbázis biztonságimásolat-fájljából végezhet visszaállítást.
- A speciális hibaelhárítási beépített intelligenciával felügyelt példány adatbázis-teljesítmény figyelését: [figyelése Azure SQL Database az Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md)
