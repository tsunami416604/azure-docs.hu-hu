---
title: Azure Security Control – leltár-és eszközkezelés
description: A biztonsági vezérlők leltározása és az eszközök kezelése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 95c1834e997caae13e650df5b588df898a1e361d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564178"
---
# <a name="security-control-inventory-and-asset-management"></a>Biztonság-ellenőrzés: leltár-és eszközkezelés

A leltár-és vagyonkezelési javaslatok az összes Azure-erőforrás aktív kezelésével (leltár, nyomon követés és helyes) kapcsolatos problémák kezelésére összpontosítanak, így csak a jogosult erőforrások férhetnek hozzá, a jogosulatlan és nem felügyelt erőforrások pedig azonosítva és eltávolítva.

## <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Ügyfél |

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portokat és protokollokat stb.) az előfizetése (ke) n belül.  Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

Lekérdezések létrehozása az Azure Resource Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC ismertetése:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.2 | 1.5 | Ügyfél |

Címkéket alkalmazhat az Azure-erőforrásokhoz, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.3 | 1.6 | Ügyfél |

A címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.4 | 2.1 | Ügyfél |

A jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.5 | 2,3, 2,4 | Ügyfél |

A Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. &nbsp;ellenőrizze, hogy a környezetben lévő összes Azure-erőforrás jóvá van-e hagyva.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.6 | 2.3/2.4 | Ügyfél |

Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines összes szoftverével kapcsolatos információk gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez engedélyezze a vendég szintű diagnosztikát, és a Windows-eseménynaplókat egy Log Analytics-munkaterületre vigye.

Az Azure-beli virtuális gépek leltározásának engedélyezése:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.7 | 2,5 | Ügyfél |

A Virtual Machines-ra telepített összes szoftver azonosításához használja a Azure Security Center fájl integritásának figyelése (Change Tracking) és a virtuális gépek leltárát. A jogosulatlan szoftverek eltávolításához saját eljárást is alkalmazhat. Harmadik féltől származó megoldást is használhat a nem jóváhagyott szoftverek azonosítására.

A fájlok integritásának figyelése:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Az Azure Change Tracking ismertetése:

https://docs.microsoft.com/azure/automation/change-tracking

Az Azure-beli virtuális gépek leltározásának engedélyezése:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.8 | 2,6 | Ügyfél |

Azure Security Center Adaptive Application Controls használatával biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és az összes jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines-on való végrehajtás során.

Azure Security Center adaptív alkalmazás-vezérlők használata:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.9 | 2,6 | Ügyfél |

A Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetben.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6.1 | 2,7 | Ügyfél |

Azure Security Center adaptív alkalmazás-vezérlőelemekkel megadhatja, hogy mely fájltípusok esetében lehet egy szabály, vagy nem alkalmazható.

Harmadik féltől származó megoldás implementálása, ha ez nem felel meg a követelménynek.

Azure Security Center adaptív alkalmazás-vezérlők használata:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: korlátozza a felhasználók azon képességét, hogy parancsfájlok használatával Azure Resource Manager kommunikálni

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6,11 | 2,8 | Ügyfél |

Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra &quot;a &quot;Microsoft Azure felügyeleti&quot; alkalmazáshoz való hozzáférés-blokkoló&quot; konfigurálásával.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6,12 | 2,8 | Ügyfél |

Az operációs rendszer-specifikus konfigurációk vagy harmadik féltől származó erőforrások használatával korlátozhatja, hogy a felhasználók képesek legyenek parancsfájlokat végrehajtani az Azure számítási erőforrásokon belül.

Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben: https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 6,13 | 2.9 | Ügyfél |

Az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy hálózati biztonsági csoporttal.

Virtuális hálózat létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG létrehozása biztonsági konfigurációval:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági vezérlőt: [biztonságos konfiguráció](security-control-secure-configuration.md)
