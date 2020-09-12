---
title: Azure Security Control – biztonságos konfiguráció
description: Azure Security Control – biztonságos konfiguráció
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400092"
---
# <a name="security-control-secure-configuration"></a>Biztonsági ellenőrzés: biztonságos konfiguráció

Az Azure-erőforrások biztonsági konfigurációjának létrehozása, implementálása és aktívan kezelése (nyomon követése, bejelentése, javítása), hogy a támadók ne tudják kiaknázni a sebezhető szolgáltatásokat és beállításokat.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.1 | 5,1 | Ügyfél |

Használjon Azure Policy aliasokat egyéni szabályzatok létrehozásához az Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Biztonsági javaslatok – útmutató](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.2 | 5,1 | Ügyfél |

A biztonsági konfigurációk minden számítási erőforráson való fenntartásához használjon Azure Security Center javaslatokat.  Emellett egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat a szervezete számára szükséges operációs rendszer biztonsági konfigurációjának létrehozásához.

- [Azure Security Center javaslatok figyelése](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Biztonsági javaslatok – útmutató](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure Automation állapot konfigurációjának áttekintése](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.3 | 5,2 | Ügyfél |

Az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.  Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager sablonok áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,4 | 5,2 | Megosztott |

Az Azure számítási erőforrásain a sebezhetőségi felmérések elvégzéséhez Azure Security Center ajánlásokat követve.  Emellett Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot konfigurációját is használhatja a szervezete számára szükséges operációs rendszer biztonsági beállításainak fenntartásához.   A Microsoft virtuálisgép-sablonjai a Azure Automation kívánt állapot-konfigurációval együtt segíthetnek a biztonsági követelmények teljesítésében és fenntartásában. 

Azt is vegye figyelembe, hogy a Microsoft által közzétett Azure Marketplace virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban. 

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Azure-beli virtuális gép létrehozása Azure Resource Manager sablonból](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure Automation állapot konfigurációjának áttekintése](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Windows rendszerű virtuális gép létrehozása a Azure Portalban](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [A virtuálisgép-sablon letöltésével kapcsolatos információk](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,5 | 5.3 | Ügyfél |

Az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.6 | 5.3 | Ügyfél |

Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. Megosztott képkatalógus használatával a képeket megoszthatja a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal.  A tárolói lemezképek esetében tárolja azokat Azure Container Registryban, és használja ki az Azure RBAC annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.  

- [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Az Azure-RBAC megismerése Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Az Azure RBAC konfigurálása](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Megosztott képgyűjtemény – áttekintés](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.7 | 5.4 | Ügyfél |

Az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet.  Emellett Azure Automation használatával is telepítheti a konfigurációs módosításokat.

- [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,8 | 5.4 | Ügyfél |

Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják. 

- [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.9 | 5,5 | Ügyfél |

Az Azure-erőforrásokra vonatkozó alapvizsgálatok elvégzéséhez használja a Azure Security Center.  Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.10 | 5,5 | Ügyfél |

A Azure Security Center használatával elvégezheti a tárolók operációsrendszer-és Docker-beállításainak alapkonfigurációját.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7.11 | 13,1 | Ügyfél |

A Felhőbeli alkalmazások titkos felügyeletének egyszerűbbé és biztonságossá tételéhez használja a Managed Service Identityt a Azure Key Vaultvel együtt.

- [Integráció az Azure felügyelt identitásokkal](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Key Vault létrehozása](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Hitelesítés Key Vault](../../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,12 | 4.1 | Ügyfél |

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások konfigurálása](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 7,13 | 18,1, 18,7 | Ügyfél |

Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági vezérlőt:  [kártevők elleni védelem](security-control-malware-defense.md)
