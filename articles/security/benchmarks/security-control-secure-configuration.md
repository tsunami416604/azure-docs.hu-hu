---
title: Azure Security Control – biztonságos konfiguráció
description: Biztonsági vezérlés biztonságos konfigurációja
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934301"
---
# <a name="security-control-secure-configuration"></a>Biztonsági ellenőrzés: biztonságos konfiguráció

Az Azure-erőforrások biztonsági konfigurációjának létrehozása, implementálása és aktívan kezelése (nyomon követése, bejelentése, javítása), hogy a támadók ne tudják kiaknázni a sebezhető szolgáltatásokat és beállításokat.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.1 | 5.1 | Ügyfél |

Az összes Azure-erőforrás biztonsági konfigurációjának megőrzése Azure Policy vagy Azure Security Center használatával.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.2 | 5.1 | Ügyfél |

Azure Security Center ajánlás használatával &quot;a Virtual Machines&quot; biztonsági konfigurációjában található biztonsági réseket, hogy az összes számítási erőforráson fenntartsa a biztonsági konfigurációkat.

Azure Security Center javaslatok figyelése:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center javaslatok szervizelése:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.3 | 5,2 | Ügyfél |

Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.4 | 5,2 | Közös |

Az operációs rendszer lemezképeit a Microsoft felügyeli és tartja karban.

A szervezete által megkövetelt biztonsági beállítások azonban Azure Resource Manager sablonokkal és/vagy a kívánt állapot-konfigurációval is alkalmazhatók.

Azure-beli virtuális gép létrehozása Azure Resource Manager sablonból:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Az Azure Virtual Machines kívánt állapot-konfigurációjának megismerése:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.5 | 5.3 | Ügyfél |

Ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Az Azure Repos dokumentációja:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.6 | 5.3 | Ügyfél |

Ha egyéni lemezképeket használ, akkor a RBAC használatával győződjön meg arról, hogy csak a jogosult felhasználók férhetnek hozzá a lemezképekhez. A Container images esetében tárolja azokat Azure Container Registry és használja ki a RBAC annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

A RBAC megismerése az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

A Container Registry RBAC ismertetése:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

A RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.7 | 5.4 | Ügyfél |

Azure Policy használata a rendszerkonfigurációk riasztására, naplózására és betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.8 | 5.4 | Ügyfél |

Használja az Azure számítási bővítményeit, például a PowerShell kívánt állapot-konfigurációját a Windows-számítási vagy a Linux Chef-bővítményhez Linuxra.

Virtuálisgép-bővítmények telepítése az Azure-ban:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.9 | 5,5 | Ügyfél |

Az Azure-erőforrások alapkonfigurációs vizsgálatának elvégzéséhez Azure Security Center használata

Javaslatok szervizelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.1 | 5,5 | Ügyfél |

A Azure Security Center használatával elvégezheti a tárolók operációsrendszer-és Docker-beállításainak alapkonfigurációját.

A Azure Security Center-tárolóra vonatkozó javaslatok ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése 

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7.11 | 13,1 | Ügyfél |

A Felhőbeli alkalmazások titkos felügyeletének egyszerűbbé és biztonságossá tételéhez használja a Managed Service Identityt a Azure Key Vaultvel együtt.

Integráció az Azure felügyelt identitásokkal:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Felügyelt identitással rendelkező Key Vault hitelesítés biztosítása:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7,12 | 4.1 | Ügyfél |

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 7,13 | 13,3 | Ügyfél |

Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze. 

A hitelesítőadat-olvasó beállítása:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági vezérlőt: [kártevők elleni védelem](security-control-malware-defense.md)
