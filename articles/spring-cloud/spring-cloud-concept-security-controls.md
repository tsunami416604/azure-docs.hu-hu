---
title: Koncepció – az Azure Spring Cloud Service biztonsági vezérlői
description: Az Azure Spring Cloud Service-be beépített biztonsági vezérlők használata.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 8d002fae52fec1fafb2ad8e63bd8e3b779a1537c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984823"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Az Azure Spring Cloud Service biztonsági vezérlői
A biztonsági vezérlők az Azure Spring Cloud Service-be vannak építve.

A biztonsági ellenőrzés olyan Azure-szolgáltatás minősége vagy funkciója, amely hozzájárul a szolgáltatás a biztonsági rések megelőzésére, észlelésére és reagálására.  Az egyes vezérlők esetében az *Igen* vagy a *nem* értékkel kell jelezni, hogy a szolgáltatás jelenleg van-e érvényben.  Az *N/a-* t olyan vezérlőhöz használjuk, amely nem alkalmazható a szolgáltatásra. 

**Adatvédelmi biztonsági vezérlők**

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | A felhasználó által feltöltött forrás és összetevők, a konfigurációs kiszolgáló beállításai, az Alkalmazásbeállítások és az állandó tárterületen tárolt adatok tárolása az Azure Storage-ban történik, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A konfigurációs kiszolgáló gyorsítótára, a feltöltött forrásból származó futtatókörnyezeti bináris fájlok, valamint az alkalmazás élettartama alatt az alkalmazás naplófájljai az Azure által felügyelt lemezre kerülnek, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban.<br><br>A felhasználó által feltöltött forrásból létrehozott tároló-lemezképeket a rendszer a Azure Container Registryba menti, ami automatikusan titkosítja a kép tartalmát a nyugalmi állapotban. | [Inaktív adatok Azure Storage-titkosítása](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Az Azure Managed Disks kiszolgálóoldali titkosítása](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Tároló képtárolója Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Titkosítás átmeneti állapotában | Yes | A felhasználói alkalmazás nyilvános végpontja alapértelmezés szerint HTTPS protokollt használ a bejövő forgalomhoz. |  |
| Titkosított API-hívások | Yes | Az Azure Spring Cloud Service-t konfiguráló felügyeleti hívások a HTTPS-en keresztül Azure Resource Manager hívásokon keresztül történnek. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Hálózati hozzáférés biztonsági vezérlői**

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|:-------------|:-------|:-------------------------------|:----------------------|
| Szolgáltatás címkéje | Yes | A **AzureSpringCloud** szolgáltatás címkével [hálózati biztonsági csoportokon](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) vagy [Azure Firewallon](https://docs.microsoft.com/azure/firewall/service-tags)adhat meg kimenő hálózati hozzáférés-vezérlést, hogy lehetővé váljon az Azure Spring Cloud-alkalmazások forgalmának engedélyezése.<br><br>*Megjegyzés:* Jelenleg csak a 2020/07/07 után létrehozott új Azure Spring Cloud Service-példány támogatja a **AzureSpringCloud** szolgáltatás címkéjét. | [Szolgáltatáscímkék](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |
