---
title: Az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server Key Vault integrálása (Resource Manager) | Microsoft Docs
description: Megtudhatja, hogyan automatizálhatja SQL Server titkosítás konfigurációját Azure Key Vault használatával. Ez a témakör azt ismerteti, hogyan használható Azure Key Vault integráció a Resource Managerrel létrehozott SQL Server virtuális gépekkel.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6d37c82775afd3118eb1b07530aab8890481dad
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049105"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Resource Manager](azure-key-vault-integration-configure.md)
> * [Klasszikus](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Áttekintés
Több SQL Server titkosítási funkció, például [transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), az [oszlop szintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)és a [biztonsági másolatok titkosítása](https://msdn.microsoft.com/library/dn449489.aspx). Ezek a titkosítási formák a titkosításhoz használt titkosítási kulcsok felügyeletét és tárolását igénylik. A Azure Key Vault (AKV) szolgáltatás úgy lett kialakítva, hogy javítsa a kulcsok biztonságát és felügyeletét egy biztonságos és magasan elérhető helyen. A [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy a SQL Server ezeket a kulcsokat a Azure Key Vault használatával használhassa.

Ha a SQL Servert a helyszíni gépekkel futtatja, akkor a helyszíni [SQL Server számítógépéről elérhető lépéseket követve elérheti a Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx). Az Azure-beli virtuális gépek SQL Server azonban időt takaríthat meg a *Azure Key Vault integrációs* funkciójával.

Ha ez a szolgáltatás engedélyezve van, a automatikusan telepíti a SQL Server Connector, konfigurálja a EKM-szolgáltatót a Azure Key Vault eléréséhez, és létrehozza a hitelesítő adatokat, amely lehetővé teszi a tár elérését. Ha a korábban említett helyszíni dokumentáció lépéseit kereste, láthatja, hogy ez a funkció automatizálja a 2. és a 3. lépést. A Key Vault és a kulcsok létrehozásához az egyetlen dolog, amit manuálisan kell elvégezni. Innentől kezdve az SQL-alapú virtuális gép teljes telepítése automatizálható. Miután a funkció befejezte a telepítést, a T-SQL-utasítások végrehajtásával megkezdheti az adatbázisok vagy biztonsági másolatok titkosítását, ahogy azt a szokásos módon tenné.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > A EKM-szolgáltató verziójának 1.0.4.0 az [SQL IaaS-bővítményen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)keresztül települ a SQL Server VM. Az SQL IaaS bővítmény frissítése nem frissíti a szolgáltató verzióját. Ha szükséges (például egy SQL felügyelt példányra való áttelepítéskor), vegye figyelembe, hogy szükség esetén manuálisan frissíti a EKM-szolgáltató verzióját.


## <a name="enabling-and-configuring-akv-integration"></a>A AKV-integráció engedélyezése és konfigurálása
Engedélyezheti a AKV-integrációt a kiépítés során, vagy konfigurálhatja azt a meglévő virtuális gépekhez.

### <a name="new-vms"></a>Új virtuális gépek
Ha új SQL Server virtuális gépet épít ki a Resource Managerrel, a Azure Portal lehetővé teszi Azure Key Vault integrálását. A Azure Key Vault funkció csak a SQL Server vállalati, fejlesztői és próbaverziós kiadásaihoz érhető el.

![SQL Azure Key Vault-integráció](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

A kiépítés részletes ismertetését lásd: [SQL Server virtuális gép kiépítése a Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépekhez nyissa meg az [SQL Virtual Machines-erőforrást](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) , és válassza a **Biztonság** lehetőséget a **Beállítások**területen. Azure Key Vault integráció engedélyezéséhez válassza az **Engedélyezés** lehetőséget. 

![SQL AKV-integráció meglévő virtuális gépekhez](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Ha elkészült, kattintson a **Biztonság** lap alján található **alkalmaz** gombra a módosítások mentéséhez.

> [!NOTE]
> Az itt létrehozott hitelesítő adatok neve később egy SQL-bejelentkezésre lesz leképezve. Ez lehetővé teszi, hogy az SQL-bejelentkezés hozzáférjen a kulcstartóhoz. 


> [!NOTE]
> A AKV-integrációt sablon használatával is konfigurálhatja. További információ: Azure rövid útmutató [sablon Azure Key Vault integrációhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
