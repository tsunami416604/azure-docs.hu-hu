---
title: Alkalmazás visszaállítása biztonsági másolatból
description: További információ az alkalmazás biztonsági mentésből történő visszaállításáról. Bizonyos csatolt adatbázisok egyetlen műveletben állíthatók vissza az alkalmazással együtt.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 1295080d0eec7a4e88029cdadd85863f5f40d034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689249"
---
# <a name="restore-an-app-in-azure"></a>Alkalmazás visszaállítása az Azure-ban
Ez a cikk bemutatja, hogyan állíthatja vissza az [Azure App Service](../app-service/overview.md) olyan alkalmazását, amelyről korábban biztonsági másolatot tett (lásd: Az alkalmazás biztonsági [szolgálata az Azure-ban).](manage-backup.md) Visszaállíthatja az alkalmazást a csatolt adatbázisokkal egy korábbi állapotba, vagy létrehozhat egy új alkalmazást az eredeti alkalmazás biztonsági mentései alapján. Az Azure App Service a következő adatbázisokat támogatja a biztonsági mentéshez és visszaállításhoz:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL alkalmazáson belüli](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

A biztonsági mentések visszaállítása a **standard** és **prémium** szinten futó alkalmazások számára érhető el. Az alkalmazás bővítéséről az [Alkalmazás felskálázása az Azure-ban](manage-scale-up.md)című témakörben talál további információt. **A prémium** szint lehetővé teszi a napi biztonsági mentések nagyobb számát, mint **a standard** szintű.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Alkalmazás visszaállítása meglévő biztonsági másolatból
1. Az Azure Portalon az alkalmazás **Beállítások** lapján kattintson a **Biztonsági mentések** elemre a **Biztonsági mentések** lap megjelenítéséhez. Ezután kattintson **a Visszaállítás gombra.**
   
    ![Válassza a visszaállítás most lehetőséget][ChooseRestoreNow]
2. A **Visszaállítás** lapon először jelölje ki a biztonsági másolat forrását.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Az **Alkalmazás biztonsági mentése** lehetőség megmutatja az aktuális alkalmazás összes meglévő biztonsági mentését, és könnyedén kiválaszthat egyet.
    A **Storage** beállítás lehetővé teszi, hogy bármely biztonsági mentési ZIP-fájlt kiválasszon bármely meglévő Azure Storage-fiókból és tárolóból az előfizetésben.
    Ha egy másik alkalmazás biztonsági másolatát próbálja visszaállítani, használja a **Tárolás** lehetőséget.
3. Ezután adja meg az alkalmazás-visszaállítás célját a **Visszaállítás cél ban.**
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Ha a Felülírás lehetőséget **választja,** az aktuális alkalmazás összes meglévő adata törlődik, és felülíródik. Mielőtt az **OK**gombra kattintana, győződjön meg arról, hogy pontosan ezt szeretné tenni.
   > 
   > 
   
   > [!WARNING]
   > Ha az App Service adatokat ír az adatbázisba a visszaállítás közben, az olyan tüneteket okozhat, mint például a PRIMARY KEY megsértése és az adatvesztés. Javasoljuk, hogy az adatbázis visszaállítása előtt állítsa le az App Service-t.
   > 
   > 
   
    A **Meglévő alkalmazás** kiválasztásával visszaállíthatja az alkalmazás biztonsági mentését egy másik alkalmazásra ugyanabban az erőforráscsoportban. A beállítás használata előtt már létre kell hoznia egy másik alkalmazást az erőforráscsoportban, amely az alkalmazás biztonsági mentésében meghatározott adatbázis-konfigurációt tükrözve. **Új** alkalmazást is létrehozhat a tartalom visszaállításához.

4. Kattintson az **OK** gombra.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Biztonsági másolat letöltése vagy törlése tárfiókból
1. Az Azure Portal fő **Tallózás** lapján válassza a **Storage-fiókok**lehetőséget. Megjelenik a meglévő tárfiókok listája.
2. Válassza ki a letölteni vagy törölni kívánt biztonsági másolatot tartalmazó tárfiókot. Megjelenik a tárfiók lapja.
3. A tárfiók lapján válassza ki a kívánt tárolót
   
    ![Tárolók megtekintése][ViewContainers]
4. Jelölje ki a letölteni vagy törölni kívánt biztonságimásolat-fájlt.
   
    ![ViewContainers tárolók](./media/web-sites-restore/03ViewFiles.png)
5. Kattintson **a Letöltés** vagy **a Törlés** gombra attól függően, hogy mit szeretne tenni.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Visszaállítási művelet figyelése
Az alkalmazás-visszaállítási művelet sikeres vagy sikertelen működésével kapcsolatos részletek megtekintéséhez keresse meg a **Tevékenységnapló** lapot az Azure Portalon.  
 

Görgessen le a kívánt visszaállítási művelet megkereséséhez, és kattintással jelölje ki.

A részletek lap a visszaállítási művelettel kapcsolatos rendelkezésre álló információkat jeleníti meg.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/overview)használatával parancsfájlokkal automatizálhatja a biztonsági mentés kezelését.

A mintákat lásd:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell-minták](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
