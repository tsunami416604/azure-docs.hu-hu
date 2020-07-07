---
title: Azure Security Control – leltár-és eszközkezelés
description: Az Azure Security Control leltározása és az eszközök kezelése
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408361"
---
# <a name="security-control-inventory-and-asset-management"></a>Biztonság-ellenőrzés: leltár-és eszközkezelés

A leltár-és vagyonkezelési javaslatok az összes Azure-erőforrás aktív kezelésével (leltár, nyomon követés és helyes) kapcsolatos problémák kezelésére összpontosítanak, így csak a jogosult erőforrások férhetnek hozzá, és a jogosulatlan és nem felügyelt erőforrások azonosíthatók és eltávolíthatók.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Ügyfél |

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portokat és protokollokat stb.) az előfizetése (ke) n belül.  Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.2 | 1.5 | Ügyfél |

Címkéket alkalmazhat az Azure-erőforrásokhoz, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.3 | 1.6 | Ügyfél |

A címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.4 | 2.1 | Ügyfél |

A szervezeti igényeknek megfelelően hozzon létre egy leltárt a jóváhagyott Azure-erőforrásokról és a számítási erőforrásokhoz jóváhagyott szoftverekről.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.5 | 2,3, 2,4 | Ügyfél |

A Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.6 | 2,3, 2,4 | Ügyfél |

Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines összes szoftverével kapcsolatos információk gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez engedélyezze a vendég szintű diagnosztikát, és a Windows-eseménynaplókat egy Log Analytics-munkaterületre vigye.

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.7 | 2.5 | Ügyfél |

A Virtual Machines-ra telepített összes szoftver azonosításához használja a Azure Security Center fájl integritásának figyelése (Change Tracking) és a virtuális gépek leltárát. A jogosulatlan szoftverek eltávolításához saját eljárást is alkalmazhat. Harmadik féltől származó megoldást is használhat a nem jóváhagyott szoftverek azonosítására.

- [A fájl integritás-figyelésének használata](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Az Azure Change Tracking ismertetése](https://docs.microsoft.com/azure/automation/change-tracking)

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.8 | 2,6 | Ügyfél |

Azure Security Center Adaptive Application Controls használatával biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines-on való végrehajtás során.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6.9 | 2,6 | Ügyfél |

A Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetben.

- [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6,10 | 2.7 | Ügyfél |

Azure Security Center adaptív alkalmazás-vezérlőelemekkel megadhatja, hogy mely fájltípusok esetében lehet egy szabály, vagy nem alkalmazható.

Harmadik féltől származó megoldás implementálása, ha ez nem felel meg a követelménynek.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6,11 | 2.9 | Ügyfél |

Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra, ha a "Microsoft Azure felügyelet" alkalmazáshoz konfigurálja a "hozzáférés tiltása" lehetőséget.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6,12 | 2.9 | Ügyfél |

A parancsfájlok típusától függően használhat operációs rendszer-specifikus konfigurációkat vagy harmadik féltől származó erőforrásokat, hogy korlátozza a felhasználók számára az Azure számítási erőforrásokon belüli parancsfájlok futtatását.  Emellett kihasználhatja Azure Security Center adaptív alkalmazás vezérlőelemeit is, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Azure Security Center adaptív alkalmazás-vezérlők használata](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 6,13 | 2.9 | Ügyfél |

Az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy hálózati biztonsági csoporttal.

- [Virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [NSG létrehozása biztonsági konfigurációval](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [biztonságos konfiguráció](security-control-secure-configuration.md)