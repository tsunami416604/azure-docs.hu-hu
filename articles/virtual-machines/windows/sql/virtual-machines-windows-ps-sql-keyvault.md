---
title: A Key Vault integrálása az SQL Server szolgáltatással Windows rendszerű virtuális gépeken az Azure-ban (Resource Manager) | Microsoft dokumentumok
description: Megtudhatja, hogyan automatizálhatja az SQL Server titkosításkonfigurációját az Azure Key Vault használatával. Ez a témakör bemutatja, hogyan használhatja az Azure Key Vault-integrációt az Erőforrás-kezelővel létrehozott SQL Server virtuális gépekkel.
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
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102152"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Áttekintés
Az SQL Server titkosítási szolgáltatásai, például [az átlátszó adattitkosítás (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [az oszlopszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)és [a biztonsági másolat titkosítása](https://msdn.microsoft.com/library/dn449489.aspx)több. A titkosítás ezen formái megkövetelik a titkosításhoz használt titkosítási kulcsok kezelését és tárolását. Az Azure Key Vault (AKV) szolgáltatás célja, hogy javítsa a biztonságot és a kulcsok felügyeletét egy biztonságos és magas rendelkezésre állású helyen. Az [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy az SQL Server használja ezeket a kulcsokat az Azure Key Vaultból.

Ha az SQL Servert helyszíni gépekkel futtatja, a [helyszíni SQL Server-gépről az Azure Key Vault eléréséhez számos lépést követhet.](https://msdn.microsoft.com/library/dn198405.aspx) Az Azure-beli virtuális gépeken lévő SQL Server számára azonban időt takaríthat meg az *Azure Key Vault-integráció funkció* használatával.

Ha ez a funkció engedélyezve van, automatikusan telepíti az SQL Server-összekötőt, konfigurálja az EKM-szolgáltatót az Azure Key Vault eléréséhez, és létrehozza a hitelesítő adatokat, amelyek lehetővé teszik a tároló elérését. Ha a korábban említett helyszíni dokumentációban ismertetett lépéseket is megtekintette, láthatja, hogy ez a funkció automatizálja a 2. Az egyetlen dolog, amit továbbra is manuálisan kell tennie, hogy létrehozza a key vault ot és a kulcsokat. Innen az SQL virtuális gép teljes beállítása automatikus. Miután ez a szolgáltatás befejezte ezt a beállítást, a T-SQL utasítások végrehajtásával megkezdheti az adatbázisok vagy biztonsági mentések titkosítását a szokásos módon.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Az EKM-szolgáltató 1.0.4.0-s verziója az SQL Server virtuális gépre az [SQL IaaS bővítményen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)keresztül van telepítve. Az SQL IaaS extension frissítése nem frissíti a szolgáltató verzióját. Kérjük, vegye fontolóra az EKM-szolgáltató verziójának manuális frissítését, ha szükséges (például SQL felügyelt példányba való áttelepítéskor).


## <a name="enabling-and-configuring-akv-integration"></a>AKV-integráció engedélyezése és konfigurálása
Engedélyezheti az AKV-integrációt a meglévő virtuális gépek kiépítése során, vagy konfigurálhatja azt.

### <a name="new-vms"></a>Új virtuális gépek
Ha egy új SQL Server virtuális gépet épít ki az Erőforrás-kezelővel, az Azure Portal lehetővé teszi az Azure Key Vault-integrációt. Az Azure Key Vault funkció csak az SQL Server Enterprise, Developer és Evaluation Editions számára érhető el.

![SQL Azure Key Vault-integráció](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

A kiépítés részletes forgatókönyve: [SQL Server virtuális gép kiépítése az Azure Portalon.](virtual-machines-windows-portal-sql-server-provision.md)

### <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetén nyissa meg az [SQL virtuális gépek erőforrását,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) és válassza a **Biztonság** lehetőséget a **Beállítások csoportban.** Válassza **az Engedélyezés** lehetőséget az Azure Key Vault-integráció engedélyezéséhez. 

![SQL AKV-integráció meglévő virtuális gépekhez](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Ha végzett, a módosítások mentéséhez kattintson az **Alkalmaz** gombra a **Biztonság** lap alján.

> [!NOTE]
> Az itt létrehozott hitelesítő adatok nevét később egy SQL-bejelentkezéshez rendeli hozzá a rendszer. Ez lehetővé teszi, hogy az SQL-bejelentkezési hozzáférést a key vault. 


> [!NOTE]
> Az AKV-integrációt sablon használatával is konfigurálhatja. További információ: [Azure quickstart sablon az Azure Key Vault-integrációhoz.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
