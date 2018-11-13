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
ms.date: 11/01/2018
ms.openlocfilehash: 3eadc2d233fd1716716c323f4c7087ee8363c67c
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912322"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rövid útmutató: Felügyelt Azure SQL Database-példány létrehozása

Ez a rövid útmutató lépésről lépésre ismerteti, hogyan hozhat létre egy Azure SQL Database-beli [felügyelt példányt](sql-database-managed-instance.md) az Azure Portalon. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések azt mutatják be, hogyan hozhat létre egy felügyelt példányt.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg a **Felügyelt példány** lehetőséget, és válassza a **Felügyelt Azure SQL-példány** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Adja meg a kért adatokat a felügyelt példány űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az Ön előfizetése | Olyan előfizetés, amely rendelkezik új erőforrások létrehozásához szükséges engedéllyel |
   |**Felügyelt példány neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a „serveradmin” szerepkört, mert ez egy lefoglalt, kiszolgáló szintű szerepkör.| 
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Erőforráscsoport**|Egy új vagy létező erőforráscsoport|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Hely**|Az a hely, ahol a felügyelt példányt létre szeretné hozni|A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**Virtuális hálózat**|Válassza ki vagy az **Új virtuális hálózat létrehozása** lehetőséget, vagy egy olyan virtuális hálózatot, amelyet az űrlapon előzőleg megadott erőforráscsoportban hozott létre| További információ virtuális hálózat konfigurálásáról felügyelt példány esetében egyéni beállításokkal: [Felügyelt SQL-példány virtuális hálózat környezeti sablonjának konfigurálása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) a Githubon. A hálózati környezet konfigurálási követelményeiről felügyelt példány esetében itt tájékozódhat: [Virtuális hálózat konfigurálása felügyelt Azure SQL Database-példányhoz](sql-database-managed-instance-vnet-configuration.md) |

   ![felügyelt példány űrlap](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Kattintson a **Tarifacsomag** elemre a számítási és tárolási erőforrások méretezéséhez, valamint a lehetséges tarifacsomagok áttekintéséhez. Az alapértelmezett érték az általános célú tarifacsomag, amely 32 GB memóriával és 16 virtuális maggal rendelkezik.
6. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. 
7. Ha végzett, a beállítások mentéséhez kattintson az **Alkalmaz** gombra.  
8. Kattintson a **Létrehozás** elemre a felügyelt példány üzembe helyezéséhez.
9. Kattintson az **Értesítések** ikonra az üzembe helyezés állapotának megtekintéséhez.

    ![felügyelt példány üzembehelyezési folyamata](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Kattintson az **Üzembe helyezés folyamatban** értesítésre a felügyelt példány ablakának megnyitásához, amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot. 

> [!IMPORTANT]
> Az alhálózat első példánya esetében az üzembe helyezés általában jóval több időt vesz igénybe, mint a további példányok esetében. Ne szakítsa meg az üzembehelyezési műveletet, ha az a vártnál tovább tart. Eltarthat néhány percig, amíg az alhálózatban létrejön a második felügyelt példány.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Erőforrások áttekintése és a teljes kiszolgálónév lekérése

Az üzembe helyezés sikeres befejezése után tekintse át a létrehozott erőforrásokat, és kérje le a kiszolgáló teljes nevét, hogy a későbbiekben felhasználhassa gyorskonfigurálások alkalmával.

1. Nyissa meg a felügyelt példányhoz tartozó erőforráscsoportot, és tekintse meg az erőforrásokat, amelyeket az imént hozott létre a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) gyorskonfigurálással.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)Nyissa meg a felügyelt példány erőforrását az Azure Portalon.

2. Kattintson a felügyelt példányra.
3. Az **Áttekintés** fülön keresse meg a **Gazdagép** tulajdonságot, és másolja be a felügyelt példány teljes állomáscímét.


   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/host-name.png)

   A név ehhez hasonló: **your_machine_name.neu15011648751ff.database.windows.net**.

## <a name="next-steps"></a>További lépések

- További információkért a felügyelt példányokhoz való csatlakozásról lásd:
  - Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
  - Rövid útmutató arról, hogyan csatlakozhat felügyelt példányokhoz Azure-beli virtuális gépről: [Azure-beli virtuálisgép-kapcsolat konfigurálása](sql-database-managed-instance-configure-vm.md).
  - Rövid útmutató arról, hogyan csatlakozhat felügyelt példányokhoz helyi ügyfélszámítógépről pont-hely típusú kapcsolattal: [Pont-hely típusú kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Ha vissza kíván állítani egy meglévő SQL-adatbázist egy felügyelt példányra, használhatja az [Azure Database Migration Services (DMS) migrálásához](../dms/tutorial-sql-server-to-managed-instance.md) szolgáltatást, amellyel a visszaállítás egy adatbázis biztonságimásolat-fájljából történik, vagy a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md), amellyel egy adatbázis biztonságimásolat-fájljából végezhet visszaállítást.
- A felügyelt példány adatbázisa teljesítményének beépített intelligenciával való speciális monitorozásáról további információkat az [Azure SQL Database Azure SQL Analytics használatával való monitorozásáról](../log-analytics/log-analytics-azure-sql.md) szóló cikkben talál
