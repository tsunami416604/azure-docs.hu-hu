---
title: Fájlok megnyitása és mentése az Azure-ban telepített SSIS-csomagokkal
description: Megtudhatja, hogy miként nyithatja meg és mentheti a fájlokat a helyszínen és az Azure-ban, amikor helyi fájlrendszereket használó SSIS-csomagokat emel fel és helyez át az Azure-beli SSIS-be
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760198"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Fájlok megnyitása és mentése a helyszínen és az Azure-ban az Azure-ban telepített SSIS-csomagokkal

Ez a cikk azt ismerteti, hogy miként nyithatja meg és mentheti a fájlokat a helyszínen és az Azure-ban, amikor helyi fájlrendszereket használó SSIS-csomagokat emel és helyez át az Azure-beli SSIS-be.

## <a name="save-temporary-files"></a>Ideiglenes fájlok mentése

Ha egyetlen csomag végrehajtása során kell tárolnia és feldolgoznia az ideiglenes`.`fájlokat,`%TEMP%`a csomagok használhatják az Azure-SSIS-integrációs futásidejű csomópontok aktuális munkakönyvtárát ( ) vagy ideiglenes mappáját ( )..

## <a name="use-on-premises-file-shares"></a>Helyszíni fájlmegosztások használata

Ha továbbra is használni szeretné **a helyszíni fájlmegosztásokat,** amikor helyi fájlrendszereket használó csomagokat emel fel és helyez át az Azure-beli SSIS-be, tegye a következőket:

1. Fájlok átvitele a helyi fájlrendszerekből helyszíni fájlmegosztásokra.

2. Csatlakozzon a helyszíni fájlmegosztásokhoz egy Azure virtuális hálózathoz.

3. Csatlakozzon az Azure-SSIS IR-hez ugyanahhoz a virtuális hálózathoz. További információ: [Csatlakozás az Azure-SSIS-integrációs futásidőhez virtuális hálózathoz.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

4. Csatlakoztassa az Azure-SSIS ir-t a helyszíni fájlmegosztásokhoz ugyanazon a virtuális hálózaton belül a Windows-hitelesítést használó hozzáférési hitelesítő adatok beállításával. További információt az [Adat- és fájlmegosztások windowsos hitelesítéssel című témakörben talál.](ssis-azure-connect-with-windows-auth.md)

5. Frissítse a csomagok helyi fájlelérési útjait a helyszíni fájlmegosztásokra mutató UNC elérési utakra. Például `C:\abc.txt` frissítsen `\\<on-prem-server-name>\<share-name>\abc.txt`a rendszerre.

## <a name="use-azure-file-shares"></a>Azure-fájlmegosztások használata

Ha az **Azure Files szolgáltatást** szeretné használni, amikor helyi fájlrendszereket használó csomagokat emel fel és helyez át az Azure-beli SSIS-be, tegye a következőket:

1. Fájlok átvitele a helyi fájlrendszerekből az Azure Files szolgáltatásba. További információ: [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Csatlakoztassa az Azure-SSIS IR-t az Azure Files-hoz a Windows-hitelesítést használó hozzáférési hitelesítő adatok beállításával. További információt az [Adat- és fájlmegosztások windowsos hitelesítéssel című témakörben talál.](ssis-azure-connect-with-windows-auth.md)

3. Frissítse a csomagokban lévő helyi fájlelérési utakat az Azure Files-ra mutató UNC-elérési utakra. Például `C:\abc.txt` frissítsen `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`a rendszerre.

## <a name="next-steps"></a>További lépések

- Telepítse a csomagokat. További információ: [SSIS-projekt telepítése az Azure-ban az SSMS segítségével.](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- Futtassa a csomagokat. További információ: [SSIS-csomagok futtatása az Azure-ban SSMS-sel.](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- Ütemezze be a csomagokat. További információ: [SSIS-csomagok ütemezése az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
