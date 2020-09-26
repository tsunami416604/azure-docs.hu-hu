---
title: Azure Security teljesítményteszt v2 – Asset Management
description: Azure Security teljesítményteszt v2 – eszközkezelés
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318024"
---
# <a name="security-control-v2-asset-management"></a>Biztonsági vezérlő v2: Asset Management

Az eszközkezelés kiterjed az Azure-erőforrások biztonságának és irányításának biztosítására szolgáló vezérlőkre. Ide tartoznak a biztonsági személyzet engedélyeivel kapcsolatos javaslatok, az eszközök leltározásának biztonsági hozzáférése, valamint a szolgáltatások és erőforrások jóváhagyásának kezelése (leltár, nyomon követés és helyes).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Győződjön meg arról, hogy a biztonsági csapatoknak biztonsági olvasói engedélyekkel kell rendelkezniük az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával felügyeljék 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. Azt is mondta, hogy a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: Előfordulhat, hogy további engedélyek szükségesek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../../governance/management-groups/overview.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Győződjön meg arról, hogy a biztonsági csapatok hozzáférhetnek az Azure-beli adategységek folyamatosan frissített leltározásához. A biztonsági csapatoknak gyakran erre a leltárra van szükségük ahhoz, hogy kiértékeljék a szervezetnek a felmerülő kockázatokat, valamint a folyamatos biztonsági tökéletesítéseket. 

A Azure Security Center Inventory szolgáltatás és az Azure Resource Graph az előfizetések összes erőforrását lekérdezheti és felderítheti, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.  

Az eszközöket logikailag rendezheti a szervezet besorolása alapján a címkék használatával, valamint az Azure-ban található egyéb metaadatokkal (név, leírás és kategória).  

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM-7, CM-8 |

A Azure Policy használatával naplózhatja és korlátozhatja, hogy mely szolgáltatások felhasználói tudnak kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat.  A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: az eszközök életciklus-kezelésének biztonsága

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Hozzon létre vagy frissítsen olyan biztonsági házirendeket, amelyek az eszköz életciklus-kezelési folyamataiban potenciálisan nagy hatású módosításokat végeznek. A módosítások közé tartoznak a következők: az identitás-szolgáltatók és a hozzáférés, az adatok érzékenysége, a hálózati konfiguráció és a rendszergazdai jogosultságok kiosztása.

Ha már nincs rájuk szükség, távolítsa el az Azure-erőforrásokat.

- [Azure-erőforráscsoport és-erőforrás törlése](../../azure-resource-manager/management/delete-resource-group.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Az Azure AD feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: csak jóváhagyott alkalmazások használata számítási erőforrásokban

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Győződjön meg arról, hogy csak a hitelesített szoftverek futnak, és az összes jogosulatlan szoftver le van tiltva az Azure Virtual Machines futtatásához.

Az alkalmazások engedélyezési listájának észleléséhez és létrehozásához használjon Azure Security Center (ASC) adaptív alkalmazás-vezérlőket. Az ASC adaptív alkalmazás-vezérlők használatával biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és minden jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

A Windows-és Linux-alapú virtuális gépekről származó leltározási adatok gyűjtésének automatizálásához használja Azure Automation Change Tracking és a leltárt. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A szoftver telepítési dátumának és egyéb információinak beszerzéséhez engedélyezze a vendég szintű diagnosztikát, és irányítsa a Windows-eseménynaplókat Log Analytics munkaterületre.

A parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozható, hogy a felhasználók képesek legyenek parancsfájlokat végrehajtani az Azure számítási erőforrásokban. 

Harmadik féltől származó megoldást is használhat a nem jóváhagyott szoftverek felderítésére és azonosítására.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../../security-center/security-center-adaptive-application.md)

- [A Azure Automation Change Tracking és a leltár ismertetése](../../automation/change-tracking.md)

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

