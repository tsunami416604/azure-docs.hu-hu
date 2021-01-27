---
title: Az Azure Spring Cloud hibaelhárítása a Virtual Network szolgáltatásban
description: Hibaelhárítási útmutató az Azure Spring Cloud Virtual Network szolgáltatáshoz.
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c2b0488663233546411b3a1b5ac1b1eb6c0b899c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887197"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>Az Azure Spring Cloud hibaelhárítása a Virtual Networks szolgáltatásban

Ez a dokumentum segítséget nyújt az Azure Spring Cloud virtuális hálózatokon való használatakor felmerülő különféle problémák megoldásában.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Probléma merült fel egy Azure Spring Cloud Service-példány létrehozásával

Az Azure Spring Cloud-példány létrehozásához elegendő engedély szükséges ahhoz, hogy a példányt a virtuális hálózathoz lehessen telepíteni.  A Spring Cloud Service-példánynak saját maga is biztosítania kell [Az Azure Spring Cloud Service engedélyt a virtuális hálózat számára](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network).

Ha a Azure Portal használatával állítja be az Azure Spring Cloud Service-példányt, akkor a Azure Portal érvényesíti az engedélyeket.

Az Azure Spring Cloud Service-példány az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)használatával történő beállításához ellenőrizze a következőket:

- Az előfizetés aktív.
- A helyet az Azure Spring Cloud támogatja.
- A példányhoz tartozó erőforráscsoport már létre van hozva.
- Az erőforrás neve megfelel az elnevezési szabálynak. Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek 2 és 32 karakter közöttinek kell lennie.

Ha az Azure Spring Cloud Service-példányt a Resource Manager-sablonnal szeretné beállítani, tekintse át [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)című témakört.

### <a name="common-creation-issues"></a>Gyakori létrehozási problémák

| Hibaüzenet | A hiba kijavítása |
|------|------|
| Az Azure Spring Cloud által létrehozott erőforrásokat a szabályzat nem engedélyezte. | A hálózati erőforrások akkor jönnek létre, amikor üzembe helyezi az Azure Spring Cloudt a saját virtuális hálózatán. Ellenőrizze, hogy van-e [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) definiálva a létrehozás blokkolásához. Nem sikerült létrehozni az erőforrásokat a következő hibaüzenetben:. |
| A megadott alhálózatok útválasztási táblákhoz vannak társítva. Kérjük, szüntesse meg őket. | Jelenleg nem támogatott az Azure Spring Cloud üzembe helyezése a meglévő útválasztási táblázatokhoz társított alhálózatban, majd az újbóli próbálkozás. |
| A szükséges forgalom nem engedélyezett. | A szükséges adatforgalom engedélyezéséhez tekintse meg az [ügyfelek felelősségeit az Azure Spring Cloud VNET való futtatásához](spring-cloud-vnet-customer-responsibilities.md) . |

## <a name="my-application-cant-be-registered"></a>Az alkalmazásom nem regisztrálható

Ez a probléma akkor fordul elő, ha a virtuális hálózat egyéni DNS-beállításokkal van konfigurálva. Ebben az esetben az Azure Spring Cloud által használt magánhálózati DNS-zóna nem hatékony. Adja hozzá a Azure DNS IP-168.63.129.16 a felsőbb rétegbeli DNS-kiszolgálóként az egyéni DNS-kiszolgálón.

## <a name="other-issues"></a>Egyéb problémák

[Gyakori Azure Spring Cloud-problémák elhárítása](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-troubleshoot).