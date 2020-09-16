---
title: Azure-beli privát hivatkozás Azure Data Factory
description: Ismerje meg, hogyan működik az Azure Private link a Azure Data Factoryban.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 48ab83db3dcbcf5c99b640ccab205ed1f0ee7ca1
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604376"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure-beli privát hivatkozás Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Az Azure Private link használatával az Azure-ban egy privát végponton keresztül kapcsolódhat különböző platformokhoz (a szolgáltatásként) üzemelő példányokhoz. A privát végpont egy magánhálózati IP-cím egy adott virtuális hálózaton és alhálózaton belül. A privát kapcsolati funkciót támogató, a következő helyen található, a Private link [dokumentációját](https://docs.microsoft.com/azure/private-link/)tartalmazó, 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Biztonságos kommunikáció az ügyfél-hálózatok és a Azure Data Factory között 
Beállíthat egy Azure-beli virtuális hálózatot a hálózat logikai ábrázolásához a felhőben. Ez a következő előnyöket nyújtja:
* Segít az Azure-erőforrások védelmében a nyilvános hálózatokban lévő támadások elleni védelemben.
* Hagyja, hogy a hálózatok és Data Factory biztonságosan kommunikáljanak egymással. 

Helyszíni hálózatot is csatlakoztathat a virtuális hálózathoz egy Internet Protocol biztonsági (IPsec) VPN (helyek közötti) kapcsolat vagy egy Azure ExpressRoute (privát társ) kapcsolat beállításával. 

Telepítheti a saját üzemeltetésű integrációs modult egy helyszíni gépre vagy a virtuális hálózatban lévő virtuális gépre is. Ezzel a következőket teheti:
* Futtasson másolási tevékenységeket egy felhőalapú adattár és egy magánhálózaton lévő adattár között.
* Egy helyszíni hálózaton vagy egy Azure-beli virtuális hálózaton végezheti el a tevékenységek átformálását a számítási erőforrásokkal szemben. 

A Azure Data Factory és az ügyfél virtuális hálózata között számos kommunikációs csatorna szükséges, ahogy az a következő táblázatban látható:

| Tartomány | Port | Leírás |
| ---------- | -------- | --------------- |
| `pe-adf.azure.com` | 443 | Data Factory létrehozásához és figyeléséhez szükséges vezérlő síkja. |
| `*.{region}.datafactory.azure.net` | 443 | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. |
| `*.servicebus.windows.net` | 443 | A saját üzemeltetésű integrációs modul szükséges az interaktív létrehozáshoz. |
| `download.microsoft.com` | 443 | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. |

A Azure Data Factory saját hivatkozásának támogatásával a következőket teheti:
* Hozzon létre egy privát végpontot a virtuális hálózaton.
* Engedélyezze a magánhálózati kapcsolatokat egy adott adatelőállító-példánnyal. 

A Azure Data Factory szolgáltatással folytatott kommunikáció a privát kapcsolaton keresztül érhető el, és segít a biztonságos privát kapcsolat biztosításában. Nem kell konfigurálnia az előző tartományt és portot egy virtuális hálózaton vagy a vállalati tűzfalon, hogy biztonságosabban biztosítsa erőforrásai védelmét.  

![Azure Data Factory architektúrához tartozó privát hivatkozás ábrája.](./media/data-factory-private-link/private-link-architecture.png)

A Private link Service minden korábbi kommunikációs csatornán való engedélyezése a következő funkciókat kínálja:
- **Támogatott**:
   - A virtuális hálózatban megadhatja és figyelheti az adatokat előállítót, még akkor is, ha letiltja az összes kimenő kommunikációt.
   - A saját üzemeltetésű integrációs modul és a Azure Data Factory szolgáltatás közötti kommunikáció biztonságos, magánhálózati környezetben is elvégezhető. A saját üzemeltetésű integrációs modul és a Azure Data Factory szolgáltatás közötti forgalom privát kapcsolaton keresztül halad. 
- **Jelenleg nem támogatott**:
   - A saját üzemeltetésű integrációs modult használó interaktív szerzői műveletek, például a kapcsolat tesztelése, a mappák listájának és a táblák listájának lekérése, a séma beolvasása és az előnézeti adatmegjelenítés.
   - Ha engedélyezi az automatikus frissítést, a saját üzemeltetésű integrációs modul új verziója automatikusan letölthető a Microsoft letöltőközpontból.

   > [!NOTE]
   > A jelenleg nem támogatott funkciók esetében továbbra is konfigurálnia kell a korábban említett tartományt és portot a virtuális hálózaton vagy a vállalati tűzfalon. 

> [!WARNING]
> Társított szolgáltatás létrehozásakor győződjön meg arról, hogy a hitelesítő adatai egy Azure Key vaultban vannak tárolva. Ellenkező esetben a hitelesítő adatok nem fognak működni, ha engedélyezi a Azure Data Factoryban található privát hivatkozást.

## <a name="set-up-private-link-for-azure-data-factory"></a>Privát hivatkozás beállítása Azure Data Factoryhoz
Saját végpontokat [a Azure Portal, a](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)PowerShell vagy az Azure CLI használatával hozhat létre.

Az Azure-beli adatgyárat a Azure Portalban is megtekintheti, és létrehozhat egy privát végpontot, ahogy az itt látható:

![Képernyőkép a "Private Endpoint connections" panelről privát végpont létrehozásához.](./media/data-factory-private-link/create-private-endpoint.png)


Ha le szeretné tiltani az Azure-beli adatgyárhoz való nyilvános hozzáférést, és csak privát kapcsolaton keresztül engedélyezi a hozzáférést, tiltsa le a hálózati hozzáférést a Azure Portal Azure Data Factoryhoz az itt látható módon:

![A "hálózati hozzáférés" panel képernyőképe privát végpont létrehozásához.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> A nyilvános hálózati hozzáférés letiltása csak a saját üzemeltetésű integrációs modulra érvényes, nem Azure Integration Runtime és SQL Server Integration Services (SSIS) Integration Runtime.

> [!NOTE]
> A nyilvános hálózati hozzáférés letiltása után továbbra is elérheti a Azure Data Factory portált nyilvános hálózaton keresztül.

## <a name="next-steps"></a>További lépések

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületén](quickstart-create-data-factory-portal.md)
- [Az Azure Data Factory bemutatása](introduction.md)
- [Visual authoring in Azure Data Factory](author-visually.md)

