---
title: IP-csoportok az Azure tűzfalon
description: Az IP-csoportok lehetővé teszik az Azure Firewall-szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444484"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP-csoportok (előzetes verzió) az Azure tűzfalon

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IP-csoportok lehetővé teszik az Azure Firewall-szabályok IP-címeinek csoportosítását és kezelését a következő módokon:

- Forráscímként a DNAT-szabályokban
- Forrás- vagy célcímként a hálózati szabályokban
- Forráscímként az alkalmazásszabályokban


Egy IP-csoport rendelkezhet egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartománysal.

Az IP-csoportok újra felhasználhatók az Azure Firewall DNST, hálózati és alkalmazásszabályok több tűzfal a régiók és az Azure-előfizetések között. A csoportneveknek egyedinek kell lenniük. Az Azure Portalon, az Azure CLI-ben vagy a REST API-ban konfigurálhat egy IP-csoportot. A kezdéshez mintasablon áll rendelkezésre.

## <a name="sample-format"></a>Mintaformátum

Az alábbi IPv4-címformátum-példák érvényesek az IP-csoportokban:

- Egycím: 10.0.0.0
- CIDR jelölés: 10.1.0.0/32
- Cím tartomány: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

Az IP-csoport az Azure Portalon, az Azure CLI-n vagy a REST API-n keresztül hozható létre. További információ: [Ip-csoport létrehozása (előzetes verzió)](create-ip-group.md).

## <a name="browse-ip-groups"></a>IP-csoportok tallózása
1. Az Azure Portal keresősávjában írja be az **IP-csoportok kifejezést,** és jelölje ki. Megtekintheti az IP-csoportok listáját, vagy a **Hozzáadás gombra** választva új IP-csoportot hozhat létre.
2. Az áttekintő lap megnyitásához jelöljön ki egy IP-csoportot. Ip-címek vagy IP-csoportok szerkeszthetők, adhatnak hozzá vagy törölhetők.

   ![IP-csoportok – áttekintés](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP-csoport kezelése

Az IP-csoport összes IP-címét, valamint a hozzá kapcsolódó szabályokat vagy erőforrásokat láthatja. IP-csoport törléséhez először el kell választania az IP-csoportot az azt használó erőforrástól.

1. Az IP-címek megtekintéséhez vagy szerkesztéséhez válassza az **IP-címek** lehetőséget a bal oldali ablaktábla **Beállítások** területén.
2. Egy vagy több IP-cím hozzáadásához jelölje be az **IP-címek hozzáadása jelölőnégyzetet.** Ekkor megnyílik a **Feltöltés Húzás vagy tallózás lapja,** vagy manuálisan is megadhatja a címet.
3.  Az IP-címek szerkesztéséhez vagy törléséhez jobbra lévő három pont (**...**) kiválasztása. Több IP-cím szerkesztéséhez vagy törléséhez jelölje be a jelölőnégyzeteket, és felül válassza a **Szerkesztés** vagy a **Törlés** lehetőséget.
4. Végül, exportálhatja a fájlt a CSV fájlformátumban.

> [!NOTE]
> Ha egy IP-csoport összes IP-címét törli, miközben az még használatban van egy szabályban, a rendszer kihagyja azt.


## <a name="use-an-ip-group"></a>IP-csoport használata

Az Azure Firewall DNST, alkalmazás vagy hálózati szabályok létrehozásakor most már kiválaszthatja az **IP-csoportot** **forrástípusként** vagy **céltípusként** az IP-cím(ek)hez.

> [!NOTE]
> Az IP-csoportok nem támogatottak a tűzfalházirendben. Jelenleg csak a hagyományos tűzfalszabályok támogatottak.

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

Az alábbi Azure PowerShell-parancsmagok használhatók AZ IP-csoportok létrehozásához és kezeléséhez:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Eltávolítás-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Új-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Új-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [telepítheti és konfigurálhatja az Azure-tűzfalat.](tutorial-firewall-deploy-portal.md)