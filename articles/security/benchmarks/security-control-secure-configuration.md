---
title: Azure Security Control – Biztonságos konfiguráció
description: Biztonsági vezérlő biztonságos konfigurációja
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934301"
---
# <a name="security-control-secure-configuration"></a>Biztonsági ellenőrzés: Biztonságos konfiguráció

Létrehozza, megvalósítja és aktívan kezelheti (nyomon követheti, jelentést tesz, helyesbítheti) az Azure-erőforrások biztonsági konfigurációját annak érdekében, hogy megakadályozza a támadók at a sebezhető szolgáltatások és beállítások kihasználásában.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.1 | 5.1 | Ügyfél |

Az Azure Policy vagy az Azure Security Center használatával az összes Azure-erőforrás biztonsági konfigurációját karbantarthatja.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.2 | 5.1 | Ügyfél |

Használja az Azure &quot;Security Center ajánlása biztonsági rések elhárítása biztonsági konfigurációk a virtuális gépek&quot; karbantartása biztonsági konfigurációk minden számítási erőforrások.

Az Azure Security Center-javaslatok figyelése:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Az Azure Security Center rekretijavaslatei:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.3 | 5.2 | Ügyfél |

Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] a biztonságos beállítások kényszerítése az Azure-erőforrások között.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak megismerése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.4 | 5.2 | Megosztott |

Az operációs rendszer alaplemezképeit a Microsoft kezeli és karbantartja.

Azonban az Azure Resource Manager-sablonok és/vagy a kívánt állapotkonfiguráció használatával alkalmazhatja a szervezet által megkövetelt biztonsági beállításokat.

Azure-beli virtuális gép létrehozása Azure Resource Manager-sablonból:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Az Azure virtuális gépek kívánt állapotkonfigurációjának ismertetése:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7,5 | 5.3 | Ügyfél |

Ha egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps-ban:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.6 | 5.3 | Ügyfél |

Ha egyéni lemezképeket használ, az RBAC segítségével győződjön meg arról, hogy csak a jogosult felhasználók férhetnek hozzá a lemezképekhez. Tárolólemezképek tárolása az Azure Container Registry és az RBAC használatával biztosíthatja, hogy csak a jogosult felhasználók férhetnek hozzá a rendszerképekhez.

Az RBAC ismertetése az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Az RBAC ismertetése a tárolóbeállításjegyzékhez:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Az RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.7 | 5.4 | Ügyfél |

Az Azure Policy használatával riasztást, naplózást és kényszerítést a rendszerkonfigurációk. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.8 | 5.4 | Ügyfél |

Azure számítási bővítmények, például a PowerShell kívánt állapotkonfigurációja windows os vagy Linux Chef-bővítmény Linux hoz.

A virtuálisgép-bővítmények telepítése az Azure-ban:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.9 | 5.5 | Ügyfél |

Az Azure Security Center használata az Azure-erőforrások alapszintű vizsgálataihoz

Javaslatok kijavítása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.1 | 5.5 | Ügyfél |

Az Azure Security Center használatával alapszintű vizsgálatokat végezhet az operációs rendszer és a Docker-beállítások tárolókhoz.

Ismerje meg az Azure Security Center tárolóra vonatkozó javaslatait:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése 

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.11 | 13.1 | Ügyfél |

A felügyelt szolgáltatásidentitás és az Azure Key Vault együttes használatával egyszerűsítheti és biztonságossá teszi a felhőalapú alkalmazások titkos felügyeletét.

Integrálás az Azure felügyelt identitásaival:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.12 | 4.1 | Ügyfél |

Felügyelt identitások használatával biztosítható az Azure-szolgáltatások automatikusan felügyelt identitásaz Azure AD-ben. Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot.

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.13 | 13.3 | Ügyfél |

A Hitelesítő adatok olvasójának implementációja a kódon belüli hitelesítő adatok azonosítására szolgál. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 

A Hitelesítő adatok képolvasójának beállítása:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>További lépések

Lásd a következő biztonsági ellenőrzés: [Malware Defense](security-control-malware-defense.md)
