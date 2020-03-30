---
title: Azure Security Control – készlet- és eszközkezelés
description: Biztonság-ellenőrzési leltár és eszközkezelés
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930064"
---
# <a name="security-control-inventory-and-asset-management"></a>Biztonságellenőrzés: Készlet- és eszközkezelés

A készlet- és eszközkezelési javaslatok az összes Azure-erőforrás aktív kezelésével (leltározással, nyomon követéssel és javítással) kapcsolatos problémák kezelésére összpontosítanak, hogy csak az engedélyezett erőforrások kaphassanak hozzáférést, és a nem felügyelt erőforrások azonosították és eltávolították.

## <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Ügyfél |

Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portokat és protokollokat stb.) az előfizetés(ek)en belül).  Biztosítsa a megfelelő (olvasási) engedélyeket a bérlőben, és számba vesse az összes Azure-előfizetést, valamint az előfizetéseken belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph, erősen ajánlott az Azure Resource Manager erőforrásainak létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.2 | 1.5 | Ügyfél |

Címkék alkalmazása az Azure-erőforrásokra, amelyek metaadatokat adnak logikailag rendszertani rendszerbe rendszerezésükhöz.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.3 | 1.6 | Ügyfél |

Adott esetben címkézéssel, felügyeleti csoportokkal és külön előfizetésekkel rendszerezheti és nyomon követheti az eszközöket. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.4 | 2.1 | Ügyfél |

Definiálja a jóváhagyott Azure-erőforrásokat és a számítási erőforrások jóváhagyott szoftverét.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.5 | 2.3, 2.4 | Ügyfél |

Az Azure Policy használatával korlátozhatja az előfizetés(ek)ben létrehozható erőforrások típusát.

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetéseikben. &nbsp;Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.6 | 2.3/2.4 | Ügyfél |

Az Azure virtuálisgép-készlet használatával automatizálhatja a virtuális gépeken lévő összes szoftverre vonatkozó információgyűjtést. A szoftvernév, a verzió, a publisher és a frissítés iódje elérhető az Azure Portalon. A telepítési dátum és egyéb információk eléréséhez engedélyezze a vendégszintű diagnosztikát, és vigye a Windows eseménynaplókat a Log Analytics-munkaterületre.

Az Azure virtuálisgép-leltár engedélyezése:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.7 | 2.5 | Ügyfél |

Használja az Azure Security Center fájlintegritás-figyelése (Change Tracking) és a virtuális gép Inventory segítségével azonosítani az összes telepített szoftver a virtuális gépeken. Saját eljárást valósíthat meg a jogosulatlan szoftverek eltávolítására. A nem jóváhagyott szoftverek azonosítására külső gyártótól származó megoldást is használhat.

A fájlintegritás figyelésének használata:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Az Azure változáskövetésének megismerése:

https://docs.microsoft.com/azure/automation/change-tracking

Az Azure virtuálisgépek készletének engedélyezése:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.8 | 2,6 | Ügyfél |

Az Azure Security Center Adaptive Application Controls használatával biztosíthatja, hogy csak az engedélyezett szoftverek hajtsanak végre, és az összes jogosulatlan szoftver le van tiltva az Azure virtuális gépeken.

Az Azure Security Center Adaptive Application Controls használata:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.9 | 2,6 | Ügyfél |

Az Azure-szabályzat használatával korlátozhatja, hogy milyen szolgáltatásokat tud kiépíteni a környezetében.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.1 | 2.7 | Ügyfél |

Az Azure Security Center Adaptive Application Controls használatával megadhatja, hogy a szabály mely fájltípusokra vonatkozzon, illetve melyekre.

Harmadik féltől származó megoldás megvalósítása, ha ez nem felel meg a követelménynek.

Az Azure Security Center Adaptive Application Controls használata:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resource Managerrel

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.11 | 2,8 | Ügyfél |

Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azure-erőforrás-kezelővel való interakcióját a &quot;Blokkhozzáférés&quot; konfigurálásával a &quot;Microsoft Azure Management&quot; alkalmazáshoz.

A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.12 | 2,8 | Ügyfél |

Az operációs rendszer-specifikus konfigurációk vagy harmadik féltől származó erőforrások használatával korlátozhatja a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre az Azure számítási erőforrásain belül.

Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 6.13 | 2.9 | Ügyfél |

Az üzleti műveletekhez szükséges, de a szervezet számára nagyobb kockázatot merülő szoftvert el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően kell biztosítani az Azure tűzfal vagy a hálózati biztonsági csoport használatával.

Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hogyan hozzunk létre egy NSG egy biztonsági config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>További lépések

Lásd a következő biztonsági vezérlőt: [Biztonságos konfiguráció](security-control-secure-configuration.md)
