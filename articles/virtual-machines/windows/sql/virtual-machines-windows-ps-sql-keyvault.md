---
title: "Kulcstároló integrálható az SQL Server rendszerhez a Windows-alapú virtuális gépek (erőforrás-kezelő) Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan használható az Azure Key Vault SQL Server titkosítási konfigurálásának automatizálásához. Ez a témakör ismerteti, hogyan használható az Azure Key Vault-integráció az erőforrás-kezelőt hozott létre az SQL Server virtuális gépekkel."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: jroth
ms.openlocfilehash: 32b9564fa5c9ca6864ade343fda309b2c3edf123
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>SQL Server az Azure Key Vault-integráció konfigurálása az Azure virtuális gépeken (erőforrás-kezelő)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Áttekintés
Nincsenek több SQL Server titkosítási szolgáltatások, például a [átlátható adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [oszlop a blokkszintű titkosítás (törlése)](https://msdn.microsoft.com/library/ms173744.aspx), és [a biztonsági másolat titkosításához](https://msdn.microsoft.com/library/dn449489.aspx). Titkosítási űrlapok kell kezelése és a titkosításhoz használt kriptográfiai kulcsok tárolásához. Az Azure Key Vault (AKV) szolgáltatás szolgáltatást javítására készült, a biztonsági és kezelésére, ezek a kulcsok egy magas rendelkezésre állású és biztonságos helyen. A [SQL Server-összekötő](http://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy ezek a kulcsok Azure Key Vault a használni kívánt SQL Server.

Ha Ön a helyszíni SQL Server rendszert futtató gépek esetében nincs vannak [lépések követésével hozzáférni a helyszíni SQL Server-számítógépen az Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx). Az SQL Server Azure virtuális gépeken, időt takaríthat meg használatával, de a *Azure Key Vault-integráció* szolgáltatás.

Ha ez a funkció engedélyezve van, automatikusan telepíti az SQL Server-összekötő, konfigurálja az Azure Key Vault eléréséhez EKM-szolgáltatóval, és lehetővé teszi a tároló eléréséhez a hitelesítő adatot hoz létre. A korábban említett helyszíni ismertetettek nézett, láthatja, hogy ez a szolgáltatás automatizálja a 2. és 3. Még mindig manuálisan kell egyedül, ha a kulcstartó és a kulcsokat. Ott a teljes telepítése az SQL virtuális gép automatikus. Ez a funkció a telepítés befejezése után hajthat végre T-SQL utasítást, hogy az adatbázis vagy a biztonsági mentések titkosításához, a szokásos módon megkezdéséhez.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Engedélyezése és AKV-integráció konfigurálása
Kiépítés során AKV-integráció engedélyezéséhez, vagy konfigurálja úgy a meglévő virtuális gépekhez.

### <a name="new-vms"></a>Új virtuális gépek
Ha egy új SQL Server virtuális gép a Resource Manager kiépíteni, az Azure-portálon biztosít egy lépést, amely az Azure Key Vault-integráció engedélyezése. Az Azure Key Vault szolgáltatás csak az Enterprise, Developer és SQL Server kiadásai próbaverzióját érhető el.

![SQL Azure Key Vault-integráció](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Kiépítés részletes útmutatást lásd: [egy SQL Server rendszerű virtuális gép az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek
Meglévő SQL Server virtuális gépek válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfigurációs** szakasza a **beállítások** panelen.

![Meglévő virtuális gépek SQL AKV-integráció](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Az a **SQL Server-konfigurációs** panelen kattintson a **szerkesztése** gomb az automatikus Key Vault integration szakaszban.

![Meglévő virtuális gépek SQL AKV-integráció konfigurálása](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfigurációs** panelt, és menti a módosításokat.

> [!NOTE]
> Beállíthatja úgy is AKV-integráció sablon használatával. További információkért lásd: [Azure gyors üzembe helyezés sablon az Azure Key Vault-integráció](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

