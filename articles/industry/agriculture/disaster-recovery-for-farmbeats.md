---
title: Vész-helyreállítási FarmBeats
description: Ez a cikk azt ismerteti, hogy az adatok helyreállítása Hogyan védi az adatok elvesztését.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683899"
---
# <a name="disaster-recovery-for-farmbeats"></a>Vész-helyreállítási FarmBeats

Az adatok helyreállítása megvédi az adatok elvesztését olyan eseményeken, mint az Azure-régió összeomlása. Ilyen esetben elindíthatja a feladatátvételt, és helyreállíthatja a FarmBeats üzemelő példányában tárolt adatokat.

Az Adathelyreállítás nem az Azure FarmBeats alapértelmezett funkciója. Ezt a funkciót manuálisan is konfigurálhatja a FarmBeats által az Azure párosított régióban tárolt adattároláshoz használt szükséges Azure-erőforrások konfigurálásával. A helyreállítás engedélyezéséhez használja az aktív – passzív módszert.

A következő szakaszokból megtudhatja, hogyan konfigurálhatja az adatok helyreállítását az Azure FarmBeats:

- [Az adatredundancia engedélyezése](#enable-data-redundancy)
- [Szolgáltatás visszaállítása online biztonsági mentésből](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Az adatredundancia engedélyezése

A FarmBeats három Azure-beli szolgáltatásban tárolja az Azure **Storage**-t, **Cosmos db** és **Time Series Insightsokat**. A következő lépésekkel engedélyezheti a szolgáltatások adatredundanciát egy párosított Azure-régióra:

1.  **Azure Storage** – ezt az útmutatást követve engedélyezheti az adatredundanciát a FarmBeats-telepítés minden egyes Storage-fiókjához.
2.  **Azure Cosmos db** – az alábbi útmutatást követve engedélyezheti az adatredundanciát a FarmBeats-telepítés Cosmos db-fiókjához.
3.  **Azure Time Series Insights (ÁME)** – az ÁME jelenleg nem nyújt adatredundanciát. Time Series Insights adatok helyreállításához nyissa meg az érzékelő/időjárási partnert, és küldje el újra az adatokat a FarmBeats üzembe helyezéséhez.

## <a name="restore-service-from-online-backup"></a>Szolgáltatás visszaállítása online biztonsági mentésből

Elindíthatja a feladatátvételt, és helyreállíthatja a tárolt adatokat, amelyek esetében a fentiekben felsorolt adattárak mindegyike a FarmBeats-telepítéshez szükséges. Miután visszaállította az Azure Storage és a Cosmos DB adatait, hozzon létre egy másik FarmBeats-telepítést az Azure párosított régiójában, majd konfigurálja az új központi telepítést úgy, hogy a visszaállított adattárakból (például az Azure Storage és a Cosmos DB) származó adatok használatára az alábbi lépéseket használja:

1. [A Cosmos DB konfigurálása](#configure-cosmos-db)
2. [Storage-fiók konfigurálása](#configure-storage-account)


### <a name="configure-cosmos-db"></a>A Cosmos DB konfigurálása

Másolja a visszaállított Cosmos DB hozzáférési kulcsát, és frissítse az új FarmBeats-Datahub Key Vault.


  ![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Másolja a visszaállított Cosmos DB URL-címét, és frissítse azt az új FarmBeats-Datahub App Service-konfigurációban. Mostantól törölhet Cosmos DB fiókot az új FarmBeats-telepítésben.

  ![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Storage-fiók konfigurálása

Másolja a visszaállított Storage-fiók hozzáférési kulcsát, és frissítse azt az új FarmBeats Datahub Key Vault.

![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Győződjön meg arról, hogy a Storage-fiók nevét frissíti az új FarmBeats batch virtuálisgép-konfigurációs fájlban.

![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Hasonlóképpen, ha engedélyezte az adathelyreállítást a Gyorssegéd-tároló fiókjához, kövesse a 2. lépést a Gyorssegéd-fiók elérési kulcsának és nevének frissítéséhez az új FarmBeats-példányban.
