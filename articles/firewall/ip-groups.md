---
title: IP-csoportok a Azure Firewall
description: Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444484"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP-csoportok (előzetes verzió) Azure Firewall

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IP-csoportok lehetővé teszik, hogy az alábbi módokon csoportosítsa és kezelje az Azure Firewall szabályok IP-címeit:

- Forrásként szolgáló DNAT-szabályok
- Forrásként vagy célként megadott címek a hálózati szabályokban
- Az alkalmazási szabályokban szereplő forráscím


Egy IP-csoport rendelkezhet egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartományból.

Az IP-csoportok újra felhasználhatók Azure Firewall DNAT, hálózati és alkalmazási szabályokban több tűzfalon az Azure-ban. A csoportok nevének egyedinek kell lennie. Az IP-csoportokat a Azure Portalban, az Azure CLI-ben vagy a REST API is konfigurálhatja. Az első lépésekhez egy minta sablont biztosítunk.

## <a name="sample-format"></a>Minta formátuma

Az alábbi IPv4-címformátum példák érvényesek az IP-csoportokban való használatra:

- Egyetlen címe: 10.0.0.0
- CIDR jelölése: 10.1.0.0/32
- Címtartomány: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

Az IP-csoportokat a Azure Portal, az Azure CLI vagy a REST API használatával lehet létrehozni. További információkért lásd: [IP-csoport létrehozása (előzetes verzió)](create-ip-group.md).

## <a name="browse-ip-groups"></a>IP-csoportok tallózása
1. A Azure Portal keresési sávban írja be az **IP-csoportok** kifejezést, és jelölje ki. Megtekintheti az IP-csoportok listáját, vagy kiválaszthatja a **Hozzáadás** lehetőséget egy új IP-csoport létrehozásához.
2. Válasszon ki egy IP-csoportot az Áttekintés lap megnyitásához. Szerkesztheti, hozzáadhatja vagy törölheti az IP-címeket vagy IP-csoportokat.

   ![IP-csoportok – áttekintés](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP-csoport kezelése

Megtekintheti az IP-csoport összes IP-címét és a hozzá társított szabályokat vagy erőforrásokat. Egy IP-csoport törléséhez először el kell távolítania az IP-csoportot az azt használó erőforrásból.

1. Az IP-címek megtekintéséhez vagy szerkesztéséhez válassza az **IP-címek** lehetőséget a bal oldali ablaktábla **Beállítások** területén.
2. Egyetlen vagy több IP-cím hozzáadásához válassza az **IP-címek hozzáadása**lehetőséget. Ekkor megnyílik a feltöltés **húzása vagy tallózása** oldal, vagy manuálisan is megadhatja a címeket.
3.  Válassza ki a jobbra lévő három pontot ( **..** .) az IP-címek szerkesztéséhez vagy törléséhez. Több IP-cím szerkesztéséhez vagy törléséhez jelölje be a jelölőnégyzeteket, majd kattintson a felül található **Szerkesztés** vagy **Törlés** lehetőségre.
4. Végül a CSV-fájlformátumban is exportálhatja a fájlt.

> [!NOTE]
> Ha egy IP-csoportban lévő összes IP-címet törli, miközben egy szabályban még használatban van, a rendszer kihagyja a szabályt.


## <a name="use-an-ip-group"></a>IP-csoport használata

Mostantól kiválaszthatja az **IP-csoport** lehetőséget az IP-cím (ek) **forrás** **-vagy DNAT** , amikor Azure Firewall-t, alkalmazást vagy hálózati szabályt hoz létre.

> [!NOTE]
> Az IP-csoportok nem támogatottak a tűzfal házirendjében. Jelenleg csak a hagyományos tűzfalszabályok támogatottak.

![IP-csoportok a tűzfalban](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az IP-csoportok jelenleg a következő régiókban érhetők el:

- USA nyugati régiója
- USA nyugati régiója, 2.
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója
- USA északi középső régiója
- USA nyugati középső régiója
- USA déli középső régiója
- Közép-Kanada
- Észak-Európa
- Nyugat-Európa
- Közép-Franciaország
- Az Egyesült Királyság déli régiója
- Kelet-Ausztrália
- Ausztrália középső régiója
- Délkelet-Ausztrália

## <a name="related-azure-powershell-cmdlets"></a>Kapcsolódó Azure PowerShell-parancsmagok

Az IP-csoportok létrehozásához és kezeléséhez a következő Azure PowerShell parancsmagokat használhatja:

- [Új – AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Új – AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [Új – AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Új – AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).