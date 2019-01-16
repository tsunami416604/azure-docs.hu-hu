---
title: A Key Vault integrálható az SQL Server a Windows virtuális gépek az Azure-ban (Resource Manager) |} A Microsoft Docs
description: Megtudhatja, hogyan automatizálhatja a konfigurációját az Azure Key Vault használata az SQL Server titkosítását. Ez a témakör azt ismerteti, hogyan használható az Azure Key Vault-integráció az SQL Server rendszerű virtuális gépek a Resource Managerrel létrehozott.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1d977631b9975f717d60afff6f1b303fdd4039ff
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54328817"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Az SQL Server az Azure Key Vault-integráció konfigurálása Azure-beli virtuális gépeken (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasszikus](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Áttekintés
Nincsenek több SQL Server titkosítási funkciók, például [transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [oszlop a blokkszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), és [biztonsági mentés titkosítása](https://msdn.microsoft.com/library/dn449489.aspx). Ezek az űrlapok titkosítási kezelését és a titkosításhoz használt kriptográfiai kulcsok tárolásához szükséges. Az Azure Key Vault (AKV) szolgáltatás célja a biztonsági és a egy biztonságos és magas rendelkezésre állású helyen ezek a kulcsok kezelését. A [SQL Server-összekötő](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy ezek a kulcsok Azure Key vault használata az SQL Server.

A helyszíni gépek az SQL Server futtatja, van-e [lépések a helyszíni SQL Server-gép az Azure Key Vault elérése](https://msdn.microsoft.com/library/dn198405.aspx). Az SQL Server Azure virtuális gépeken, időt takaríthat használatával, de a *Azure Key Vault-integráció* funkció.

Ez a funkció engedélyezve van, amikor szolgáltatás automatikusan telepíti az SQL Server-összekötő, konfigurálja az EKM-szolgáltató Azure Key Vault elérése érdekében, és létrehozza a hitelesítő adatokat, ezáltal lehetővé teszi a hozzáférést a tárolóhoz. A korábban említett helyszíni dokumentáció lépéseit nézett, láthatja, hogy ez a funkció automatizálja a 2. és 3. Továbbra is kell manuálisan egyedül, ha a key vaulttal és a kulcsokat. Itt az SQL virtuális gép teljes beállítása automatizált. Ez a funkció a telepítés befejezését követően megkezdheti az adatbázisok vagy a biztonsági mentések titkosításához, ahogy azt szokásosan tenné a T-SQL utasításokkal hajthat végre.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Engedélyezése és konfigurálása az AKV-integráció
Üzembe helyezés során az AKV-integráció engedélyezése, vagy konfigurálja a meglévő virtuális gépekhez.

### <a name="new-vms"></a>Új virtuális gépek
Ha egy új SQL Server virtuális gép és a Resource Manager szeretne kiépíteni, az Azure Portalon biztosítja az Azure Key Vault-integráció engedélyezése. Az Azure Key Vault szolgáltatás csak az Enterprise, Developer és az SQL Server kiadások értékelésére érhető el.

![SQL Azure Key Vault-integráció](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

További üzembe helyezésének részletes útmutató: [az Azure Portalon az SQL Server virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek
Meglévő SQL Server virtuális gépek esetében válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfiguráció** szakaszában a **beállítások** panelen.

![Meglévő virtuális gépek SQL AKV-integráció](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Az a **SQL Server-konfiguráció** panelen kattintson a **szerkesztése** a automatikus Key Vault-integráció szakaszban gombot.

![SQL AKV-integráció konfigurálása a meglévő virtuális gépek](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfiguráció** panelen a módosítások mentéséhez.

> [!NOTE]
> Az itt létrehozott hitelesítő adat neve lesz rendelve egy SQL-bejelentkezési később. Ez lehetővé teszi az SQL-bejelentkezési hozzáférjen a kulcstartóhoz. 
>
>

> [!NOTE]
> Beállíthatja az AKV-integráció-sablon használatával. További információkért lásd: [Azure Key Vault-integráció az Azure gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

