---
title: Azure Security Control – Biztonságos konfiguráció
description: Az Azure Security Control biztonságos konfigurációja
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bfa16f59155e420209ab6370056a7c612b5327e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759040"
---
# <a name="security-control-secure-configuration"></a>Biztonsági ellenőrzés: Biztonságos konfiguráció

Létrehozza, megvalósítja és aktívan kezelheti (nyomon követheti, jelentést tesz, helyesbítheti) az Azure-erőforrások biztonsági konfigurációját annak érdekében, hogy megakadályozza a támadók at a sebezhető szolgáltatások és beállítások kihasználásában.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.1 | 5.1 | Ügyfél |

Azure Policy aliasok használatával egyéni szabályzatok létrehozásához az Azure-erőforrások naplózásához vagy kényszerítéséhez. Beépített Azure-szabályzat-definíciókat is használhat.

Emellett az Azure Resource Manager képes a sablon exportálására javascript-objektumnotítás (JSON), amelyet felül kell vizsgálni annak érdekében, hogy a konfigurációk megfelelnek / meghaladják a szervezet biztonsági követelményeit.

Az Azure Security Center ajánlásait is használhatja az Azure-erőforrások biztonságos konfigurációs alapkonfigurációjaként.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Egy- és többerőforrásos exportálás sablonba az Azure Portalon](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Biztonsági ajánlások – útmutató](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.2 | 5.1 | Ügyfél |

Az Azure Security Center-javaslatok használatával az összes számítási erőforrás biztonsági konfigurációját karbantarthatja.  Emellett egyéni operációsrendszer-lemezképek vagy az Azure Automation State-konfiguráció használatával is létrehozhatja a szervezet által igényelt operációs rendszer biztonsági konfigurációját.

- [Az Azure Security Center-javaslatok figyelése](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Biztonsági ajánlások – útmutató](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure Automation állapotkonfiguráció – áttekintés](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Töltsön fel egy virtuális merevlemezt, és használja új Windows-virtuális gépek létrehozásához az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.3 | 5.2 | Ügyfél |

Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] a biztonságos beállítások kényszerítése az Azure-erőforrások között.  Emellett azure Resource Manager-sablonok használatával karbantarthatja a szervezet által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Az Azure-szabályzat hatásainak ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Az Azure Resource Manager-sablonok áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.4 | 5.2 | Megosztott |

Kövesse az Azure Security Center ajánlásait az Azure számítási erőforrásain végzett biztonsági résértékelésekelvégzéséről.  Emellett használhatja az Azure Resource Manager-sablonokat, az egyéni operációsrendszer-lemezképeket vagy az Azure Automation State-konfigurációt a szervezet által igényelt operációs rendszer biztonsági konfigurációjának fenntartásához.   A Microsoft virtuálisgép-sablonok és az Azure Automation kívánt állapotkonfigurációja együttesen segíthetnek a biztonsági követelmények teljesítésében és fenntartásában. 

Vegye figyelembe azt is, hogy a Microsoft által közzétett Azure Marketplace virtuálisgép-lemezképeket a Microsoft kezeli és karbantartja. 

- [Az Azure Security Center biztonsági résértékelési javaslatainak megvalósítása](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Azure-alapú virtuális gép létrehozása Azure Resource Manager-sablonból](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure Automation állapotkonfiguráció – áttekintés](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Windows virtuális gép létrehozása az Azure Portalon](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [A virtuális gép sablonjának letöltésével kapcsolatos tudnivalók](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7,5 | 5.3 | Ügyfél |

Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például az egyéni Azure-szabályzatokat, az Azure Resource Manager-sablonokat és a kívánt állapotkonfigurációs parancsfájlokat. Az Azure DevOps által kezelt erőforrások eléréséhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directoryban (Azure AD) definiált csoportoknak, ha integrálva vannak az Azure DevOps-szal, vagy az Active Directoryt, ha integrálva vannak a TFS-sel.

- [Kód tárolása az Azure DevOps-ban](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps-ban](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.6 | 5.3 | Ügyfél |

Ha egyéni lemezképeket használ, használjon szerepköralapú hozzáférés-vezérlést (RBAC) annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. A megosztott képtár használatával megoszthatja a képeket a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal.  Tárolólemezképek tárolása az Azure Container Registry és az RBAC használatával biztosíthatja, hogy csak a jogosult felhasználók férhetnek hozzá a rendszerképekhez.  

- [Az RBAC ismertetése az Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Az RBAC ismertetése a tárolóbeállításjegyzékhez](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Az RBAC konfigurálása az Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Megosztott képtár – áttekintés](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelő eszközök üzembe helyezése az Azure-erőforrásokhoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.7 | 5.4 | Ügyfél |

Az Azure-erőforrások hoz szabványos biztonsági konfigurációkat definiálhat és valósíthat meg az Azure Policy használatával. Azure Policy aliasok használatával egyéni szabályzatok létrehozásához az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvényesítéséhez. Az adott erőforrásokhoz kapcsolódó beépített házirend-definíciókat is használhat.  Emellett használhatja az Azure Automation konfigurációs módosítások üzembe helyezéséhez.

- [Az Azure-szabályzat konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Az Aliases használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Konfigurációkezelő eszközök telepítése operációs rendszerekhez

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.8 | 5.4 | Ügyfél |

Az Azure Automation State Configuration egy konfigurációkezelő szolgáltatás a kívánt állapotkonfigurációs (DSC) csomópontokhoz bármely felhőbeli vagy helyszíni adatközpontban. Könnyedén bedeszkázhatja a gépeket, deklaratív konfigurációkat rendelhet hozzájuk, és megtekintheti az egyes gépek megfelelőségét mutató jelentéseket a megadott állapothoz. 

- [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezeléshez](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.9 | 5.5 | Ügyfél |

Az Azure Security Center használatával alapszintű vizsgálatokat végezhet az Azure-erőforrásokhoz.  Emellett az Azure Policy használatával riasztást és naplózást az Azure-erőforrás-konfigurációk.

- [Javaslatok kijavítása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.10 | 5.5 | Ügyfél |

Az Azure Security Center használatával alapszintű vizsgálatokat végezhet az operációs rendszer és a Docker-beállítások tárolókhoz.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.11 | 13.1 | Ügyfél |

A felügyelt szolgáltatásidentitás és az Azure Key Vault együttes használatával egyszerűsítheti és biztonságossá teszi a felhőalapú alkalmazások titkos felügyeletét.

- [Integrálás az Azure felügyelt identitásaival](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Key Vault létrehozása](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [A Kulcstartó hitelesítése felügyelt identitással](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.12 | 4.1 | Ügyfél |

Felügyelt identitások használatával biztosítható az Azure-szolgáltatások automatikusan felügyelt identitásaz Azure AD-ben. Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot.

- [Felügyelt identitások konfigurálása](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 7.13 | 18.1, 18.7 | Ügyfél |

A Hitelesítő adatok olvasójának implementációja a kódon belüli hitelesítő adatok azonosítására szolgál. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 

- [A Hitelesítő adatok képolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>További lépések

- Lásd a következő biztonsági ellenőrzés: [Malware Defense](security-control-malware-defense.md)