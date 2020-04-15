---
title: A FarmBeats vészhelyreállítása
description: Ez a cikk azt ismerteti, hogy az adathelyreállítás hogyan védi az adatok elvesztését.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313072"
---
# <a name="disaster-recovery-for-farmbeats"></a>A FarmBeats vészhelyreállítása

Az adat-helyreállítás megvédi önt az adatok elvesztésétől egy olyan esemény esetén, mint az Azure-régió összeomlása. Ilyen esetben elindíthatja a feladatátvételt, és helyreállíthatja a FarmBeats központi telepítésében tárolt adatokat.

Az adatok helyreállítása nem alapértelmezett szolgáltatás az Azure FarmBeats-ben. Ezt a funkciót manuálisan konfigurálhatja a FarmBeats által az Azure-ban párosított régióban tárolt adatok tárolására használt szükséges Azure-erőforrások konfigurálásával. Aktív – passzív megközelítés használata a helyreállítás engedélyezéséhez.

Az alábbi szakaszok arról nyújtanak tájékoztatást, hogyan konfigurálhatja az adatok helyreállítását az Azure FarmBeats szolgáltatásban:

- [Adatredundancia engedélyezése](#enable-data-redundancy)
- [Szolgáltatás visszaállítása online biztonsági mentésből](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Adatredundancia engedélyezése

A FarmBeats három Azure-beli szolgáltatásban tárolja az adatokat, amelyek az **Azure storage,** **a Cosmos DB** és a Time Series **Insights.** A következő lépésekkel engedélyezheti az adatredundanciát ezeknél a szolgáltatásoknál egy párosított Azure-régióhoz:

1.  **Azure Storage** – Kövesse ezt az iránymutatást, hogy a FarmBbeats üzembe helyezésében minden egyes tárfiókhoz adatredundanciát engedélyezhet.
2.  **Azure Cosmos DB** – Kövesse ezt az iránymutatást a Cosmos DB-fiók, a FarmBeats üzembe helyezéséhez az adatredundancia engedélyezéséhez.
3.  **Azure Time Series Insights (TSI)** – az TSI jelenleg nem kínál adatredundanciát. A Time Series Insights-adatok helyreállításához nyissa meg az érzékelő/időjárás-partnert, és ismét lenyomja az adatokat a FarmBeats üzembe helyezésére.

## <a name="restore-service-from-online-backup"></a>Szolgáltatás visszaállítása online biztonsági mentésből

Kezdeményezheti a feladatátvételt, és helyreállíthatja a tárolt adatokat, amelyek mindegyike a fent említett adattárak a FarmBeats központi telepítés. Miután helyreállította az Azure Storage és a Cosmos DB adatait, hozzon létre egy másik FarmBeats-telepítést az Azure párosított régióban, majd konfigurálja az új központi telepítést a visszaállított adattárakból (azaz az Azure Storage és a Cosmos DB) származó adatok használatára az alábbi lépések végrehajtásával:

1. [A Cosmos DB konfigurálása](#configure-cosmos-db)
2. [Tárfiók konfigurálása](#configure-storage-account)


### <a name="configure-cosmos-db"></a>A Cosmos DB konfigurálása

Másolja a visszaállított Cosmos DB hozzáférési kulcsát, és frissítse az új FarmBeats Datahub key vaultot.


  ![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Másolja a visszaállított Cosmos DB URL-címét, és frissítse azt az új FarmBeats Datahub App Service Configuration alkalmazásban. Most már törölheti cosmos DB-fiók az új FarmBeats központi telepítés.

  ![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Tárfiók konfigurálása

Másolja a visszaállított tárfiók hozzáférési kulcsát, és frissítse azt az új FarmBeats Datahub key vaultban.

![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Győződjön meg arról, hogy az új FarmBeats Batch virtuálisgép konfigurációs fájlban frissíti a tárfiók nevét.

![Vészhelyreállítás](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Hasonlóképpen, ha engedélyezte az adat-helyreállítást a gyorssegéd tárfiókjához, kövesse a 2.
