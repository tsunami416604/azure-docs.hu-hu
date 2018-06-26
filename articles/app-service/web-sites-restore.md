---
title: Alkalmazás visszaállítása az Azure-ban
description: 'Útmutató: az alkalmazás visszaállítása biztonsági másolatból.'
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: d4b84904db703d1e8e78240cb971250672ef9615
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753286"
---
# <a name="restore-an-app-in-azure"></a>Alkalmazás visszaállítása az Azure-ban
A cikkből megtudhatja, hogyan lehet visszaállítani az alkalmazásban [Azure App Service](../app-service/app-service-web-overview.md) korábban biztonsági (lásd: [készítsen biztonsági másolatot az alkalmazás az Azure-ban](web-sites-backup.md)). Az alkalmazást és annak csatolt adatbázisait igény szerint visszaállíthatja egy korábbi állapotba, vagy az alkalmazás eredeti biztonsági másolatai alapján létrehozhat egy új alkalmazást. Az Azure App Service a következő adatbázisok biztonsági mentését és helyreállítását támogatja:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL alkalmazásbeli](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Futó alkalmazások számára elérhető visszaállítása biztonsági másolatból rendszer **szabványos** és **prémium** réteg. Az alkalmazás vertikális felskálázásával kapcsolatos információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md). **Prémium szintű** réteg lehetővé teszi, hogy a napi biztonsági mentését, mint nagyobb számú **szabványos** réteg.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Egy alkalmazás meglévő biztonsági másolaton visszaállítása
1. Az a **beállítások** lap az alkalmazás az Azure portálon, kattintson a **biztonsági mentések** megjelenítéséhez a **biztonsági mentések** lap. Kattintson a **visszaállítása**.
   
    ![Válassza ki a visszaállítási most][ChooseRestoreNow]
2. Az a **visszaállítása** lapon, először válassza ki a biztonsági mentési forrás.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    A **alkalmazás biztonsági mentési** beállítást választja, megjelenik az összes meglévő biztonsági mentését az aktuális alkalmazás, és egyszerűen kiválaszthatja az egyik.
    A **tárolási** beállítás lehetővé teszi bármely biztonsági mentési ZIP-fájl válasszon a meglévő Azure Storage-fiók és tároló az előfizetésben.
    Ha próbál biztonsági másolatát egy másik alkalmazás használja a **tárolási** lehetőséget.
3. Ezt követően adja meg az alkalmazás visszaállításához **visszaállítási célhelyének ellenőrzése**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Ha úgy dönt, **felülírása**, az összes meglévő adatok az aktuális alkalmazás törlése, és írja felül. Kattintás előtt **OK**, győződjön meg arról, hogy pontosan mit kíván tenni.
   > 
   > 
   
    Kiválaszthatja **meglévő App** az alkalmazás biztonsági másolatának visszaállítása egy másik alkalmazásnak ugyanabban az erőforráscsoportban. Mielőtt ezt a beállítást használja, kell már létrehozott egy másik alkalmazás a tükrözési adatbázis konfigurációja egy alkalmazás biztonsági mentési definiálva az erőforráscsoportban. Létrehozhat egy **új** app visszaállítani a tartalmat.

4. Kattintson az **OK** gombra.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Töltse le, illetve törölhet egy biztonsági mentés a storage-fiók
1. A fő **Tallózás** az Azure portálon, válassza a lap **tárfiókok**. A meglévő tárfiókok listája jelenik meg.
2. Válassza ki a kívánt letöltheti és törölheti a biztonsági másolatot tartalmazó tárfiókot. A tárfiók a lap jelenik meg.
3. A storage-fiók oldalán válassza ki a kívánt tároló
   
    ![Nézet tárolók][ViewContainers]
4. Válassza ki a biztonságimásolat-fájl letöltése vagy törölni szeretné.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Kattintson a **letöltése** vagy **törlése** attól függően, hogy mit kíván tenni.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>A figyelő a visszaállítási művelet
A sikeres vagy sikertelen volt-e az alkalmazás-visszaállítási művelet részleteinek megtekintéséhez lépjen a **tevékenységnapló** oldal az Azure portálon.  
 

Megtekintéséhez görgessen le a kívánt visszaállítási műveletet, és kattintással jelölje ki azt.

A Részletek lap megjeleníti a rendelkezésre álló információkat a visszaállítási művelethez kapcsolódó.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Automatizálható biztonságimásolat-felügyeleti parancsfájlok, használja a [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/overview).

Minták lásd:

- [Azure CLI-minták](app-service-cli-samples.md)
- [Az Azure PowerShell-példák](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
