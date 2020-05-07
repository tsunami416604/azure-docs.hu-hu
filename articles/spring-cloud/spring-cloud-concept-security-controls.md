---
title: Koncepció – az Azure Spring Cloud Service biztonsági vezérlői
description: Az Azure Spring Cloud Service-be beépített biztonsági vezérlők használata.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594984"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Az Azure Spring Cloud Service biztonsági vezérlői
A biztonsági vezérlők az Azure Spring Cloud Service-be vannak építve.

A biztonsági ellenőrzés olyan Azure-szolgáltatás minősége vagy funkciója, amely hozzájárul a szolgáltatás a biztonsági rések megelőzésére, észlelésére és reagálására.  Az egyes vezérlők esetében az *Igen* vagy a *nem* értékkel kell jelezni, hogy a szolgáltatás jelenleg van-e érvényben.  Az *N/a-* t olyan vezérlőhöz használjuk, amely nem alkalmazható a szolgáltatásra. 

**Adatvédelmi biztonsági vezérlők**

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A felhasználó által feltöltött forrás és összetevők, a konfigurációs kiszolgáló beállításai, az Alkalmazásbeállítások és az állandó tárterületen tárolt adatok tárolása az Azure Storage-ban történik, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A konfigurációs kiszolgáló gyorsítótára, a feltöltött forrásból származó futtatókörnyezeti bináris fájlok, valamint az alkalmazás élettartama alatt az alkalmazás naplófájljai az Azure által felügyelt lemezre kerülnek, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A felhasználó által feltöltött forrásból létrehozott tároló-lemezképeket a rendszer a Azure Container Registryba menti, ami automatikusan titkosítja a kép tartalmát a nyugalmi állapotban. | [Inaktív adatok Azure Storage-titkosítása](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Az Azure Managed Disks kiszolgálóoldali titkosítása](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Tároló képtárolója Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Titkosítás átmeneti állapotában | Igen | A felhasználói alkalmazás nyilvános végpontja alapértelmezés szerint HTTPS protokollt használ a bejövő forgalomhoz. |  |
| Titkosított API-hívások | Igen | Az Azure Spring Cloud Service-t konfiguráló felügyeleti hívások a HTTPS-en keresztül Azure Resource Manager hívásokon keresztül történnek. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

