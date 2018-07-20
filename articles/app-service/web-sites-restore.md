---
title: Alkalmazás visszaállítása az Azure-ban
description: Útmutató az alkalmazás visszaállítása biztonsági másolatból.
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
ms.openlocfilehash: 69e0e8282ee0b8503fe11a57b8ba6037247822dd
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160501"
---
# <a name="restore-an-app-in-azure"></a>Alkalmazás visszaállítása az Azure-ban
Ez a cikk bemutatja, hogyan állíthatja vissza egy alkalmazás a [Azure App Service](../app-service/app-service-web-overview.md) , amely korábban már készített biztonsági másolatot (lásd: [biztonsági mentése az Azure-ban az alkalmazás](web-sites-backup.md)). Az alkalmazást és annak csatolt adatbázisait igény szerint visszaállíthatja egy korábbi állapotba, vagy az alkalmazás eredeti biztonsági másolatai alapján létrehozhat egy új alkalmazást. Az Azure App Service támogatja a következő adatbázisok biztonsági mentése és visszaállítása:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [– Alkalmazáson belüli MySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Futó alkalmazások számára elérhető biztonsági másolatokból való visszaállítással **Standard** és **prémium** szint. Az alkalmazás vertikális felskálázásával kapcsolatos további információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md). **Prémium szintű** szint lehetővé teszi, hogy a napi biztonsági mentések hajtható végre, mint a nagyobb számú **Standard** szint.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Alkalmazás visszaállítása egy meglévő biztonsági mentésből
1. A a **beállítások** oldalon az alkalmazás az Azure Portalon, kattintson a **biztonsági mentések** megjelenítéséhez a **biztonsági mentések** lap. Kattintson a **visszaállítása**.
   
    ![Válassza ki a visszaállítási most][ChooseRestoreNow]
2. Az a **visszaállítása** lapon, először válassza ki a biztonsági mentési forrását.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    A **alkalmazás biztonsági másolatának** beállítást választja, megjelenik az összes a meglévő biztonsági másolatok az aktuális alkalmazás, és egyszerűen kiválaszthatja az egyik.
    A **tárolási** beállítás lehetővé teszi bármilyen biztonsági másolat ZIP-fájlt választhat a meglévő Azure Storage-fiók és tároló az előfizetésében.
    Ha szeretne egy másik alkalmazás biztonsági másolatának visszaállítása, használja a **tárolási** lehetőséget.
3. Ezután adja meg az alkalmazás visszaállításához való **visszaállítási célhelyének**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Ha úgy dönt, **felülírás**, az összes meglévő adatok az aktuális alkalmazás törölve lesz, és felül. Gombra való kattintás előtt **OK**, győződjön meg arról, hogy pontosan mit szeretne tenni.
   > 
   > 
   
   > [!WARNING]
   > Az App Service-ben írja az adatokat az adatbázisban, amíg vissza, ha azt eredményezheti jelenségeket, például az elsődleges kulcs és az adatvesztést megsértése. Az App Service-ben az adatbázis visszaállítása előtt először állítsa le. javasolt.
   > 
   > 
   
    Választhat **meglévő alkalmazás** az alkalmazás biztonsági másolatának visszaállítása egy másik alkalmazásnak ugyanabban az erőforráscsoportban. Ezt a beállítást használja, mielőtt kell már létrehozott egy másik alkalmazás tükrözési adatbázis konfigurációja egy meghatározott az alkalmazás biztonsági másolatának az erőforráscsoportban. Ezenkívül létrehozhat egy **új** alkalmazást a tartalom visszaállításához.

4. Kattintson az **OK** gombra.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Töltse le, vagy egy biztonsági másolat törlése egy storage-fiókból
1. A fő **Tallózás** az Azure Portalon, válassza az oldal **tárfiókok**. A meglévő tárfiókok listája jelenik meg.
2. Válassza ki a letölteni vagy törölni szeretné a biztonsági másolatot tartalmazó tárfiókot. A tárfiók az oldal jelenik meg.
3. A storage-fiók oldalon válassza a kívánt tároló
   
    ![Tárolók megtekintése][ViewContainers]
4. Válassza ki a biztonságimásolat-fájl letöltése vagy törölni szeretné.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Kattintson a **letöltése** vagy **törlése** függően, hogy mit szeretne tenni.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>A figyelő a visszaállítási művelet
A sikeres vagy sikertelen az alkalmazás-visszaállítási művelet részleteinek megtekintéséhez lépjen a **tevékenységnapló** oldal az Azure Portalon.  
 

Megtekintéséhez görgessen le a kívánt visszaállítási műveletet, és kattintással jelölje ki azt.

A Részletek lap megjeleníti a rendelkezésre álló információkat a visszaállítási művelethez kapcsolódó.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Automatizálhat biztonsági másolatokat kezelő szkriptekkel, a a [Azure CLI-vel](/cli/azure/install-azure-cli) vagy [Azure PowerShell-lel](/powershell/azure/overview).

Minták lásd:

- [Azure CLI-minták](app-service-cli-samples.md)
- [Azure PowerShell-minták](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
