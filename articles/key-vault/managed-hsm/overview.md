---
title: Az Azure Managed HSM áttekintése – Azure Managed HSM | Microsoft Docs
description: Az Azure Managed HSM egy felhőalapú szolgáltatás, amely védi a titkosítási kulcsokat a felhőalapú alkalmazásokhoz.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: e98b21d5afebdbd2654d032493b92eb5e6e7fdc6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91320557"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Mi az Azure Key Vault Managed HSM (előzetes verzió)?

Azure Key Vault felügyelt HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét az **FIPS 140-2 3. szintű** hitelesített HSM használatával.  

## <a name="why-use-managed-hsm"></a>Miért érdemes felügyelt HSM-et használni?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Teljes körűen felügyelt, magasan elérhető, egyetlen bérlős HSM szolgáltatásként

- **Teljes körűen felügyelt**: a HSM-kiépítés, a konfiguráció, a javítás és a karbantartás a szolgáltatással történik. 
- **Magasan elérhető és rugalmas zónák** (ahol a rendelkezésre állási zónák támogatottak): az egyes HSM-FÜRTÖK több HSM-partícióból állnak, amelyek legalább két rendelkezésre állási zónára terjednek ki. Ha a hardver meghibásodik, a HSM-fürthöz tartozó partíciók automatikusan át lesznek telepítve a kifogástalan állapotú csomópontokra.
- **Egybérlős**: minden felügyelt HSM-példány egyetlen ügyfélhez van hozzárendelve, és több HSM-partícióból álló fürtből áll. Mindegyik HSM-fürt egy külön ügyfél-specifikus biztonsági tartományt használ, amely kriptográfiailag elkülöníti az egyes ügyfelek HSM-fürtjét.


### <a name="access-control-enhanced-data-protection--compliance"></a>Hozzáférés-vezérlés, fokozott adatvédelem & megfelelőség

- **Központosított kulcskezelő**: egy helyen kezelheti a cégen belüli kritikus és nagy értékű kulcsokat. A minimális jogosultságokkal rendelkező hozzáférés elve alapján minden egyes kulcshoz meg kell határozni a hozzáférést.
- **Elkülönített hozzáférés-vezérlés**: a felügyelt HSM "helyi RBAC" hozzáférés-vezérlési modell lehetővé teszi, hogy a kijelölt HSM-fürt rendszergazdái teljes körűen szabályozzák a HSM, hogy még a felügyeleti csoport, az előfizetés vagy az erőforráscsoport-rendszergazda nem bírálhatja felül
- **Fips 140-2 3. szint ellenőrzött HSM**: az adatvédelmet és a megfelelőségi követelmények teljesítését a FIPS ((Federal Information Protection Standard) 140-2 3. szint ellenőrzött HSM. A felügyelt HSM a Marvell LiquidSecurity HSM-adaptereket használják.
- **Monitorozás és naplózás**: teljes mértékben integrált az Azure monitorral. Az összes tevékenység teljes naplóinak beolvasása Azure Monitoron keresztül. Az Azure Log Analytics használata az elemzésekhez és a riasztásokhoz.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Az Azure és a Microsoft Pásti/SaaS szolgáltatásokkal integrálva 

- Az Azure-szolgáltatásokban, például az Azure [Storage](../../storage/common/encryption-customer-managed-keys.md)-ban, az [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)-ben és a [Azure Information Protection](/azure/information-protection/byok-price-restrictions)-ben a [BYOK](hsm-protected-keys-byok.md)-kulcsok használatával (vagy importálással) titkosíthatja adatait.

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Ugyanazokat az API-és felügyeleti felületeket használja, mint a Key Vault

- Egyszerűen migrálhatja meglévő alkalmazásait, amelyek tárolót használnak (több-bérlős) a felügyelt HSM használatához.
- Ugyanazokat az alkalmazás-fejlesztési és üzembe helyezési mintákat használhatja az összes alkalmazáshoz, a használatban lévő kulcskezelő megoldástól függetlenül: több-bérlős tárolók vagy egyetlen bérlő által felügyelt HSM

### <a name="import-keys-from-your-on-premise-hsms"></a>Kulcsok importálása a helyszíni HSM

- HSM-védelemmel ellátott kulcsok generálása a helyszíni HSM-ben és biztonságos importálása a felügyelt HSM-be

## <a name="next-steps"></a>További lépések
- Útmutató [: felügyelt HSM kiépítése és aktiválása az Azure CLI használatával](quick-create-cli.md) a felügyelt HSM létrehozásához és aktiválásához
- [Ajánlott eljárások a Azure Key Vault Managed HSM használatával](best-practices.md)