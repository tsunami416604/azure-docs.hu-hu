---
title: Azure-beli privát hivatkozás Azure Data Factory
description: További információ az Azure Private linkről Azure Data Factory.
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
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596022"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure-beli privát hivatkozás Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. A privát kapcsolati funkciót támogató Pásti-szolgáltatások listáját a [Private link dokumentációs oldalán találja](https://docs.microsoft.com/azure/private-link/). A privát végpont egy magánhálózati IP-cím egy adott virtuális hálózaton és alhálózaton belül.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Biztonságos kommunikáció az ügyfél-hálózat és a Azure Data Factory szolgáltatás között
Ahhoz, hogy az Azure-erőforrások védve legyenek a nyilvános hálózatban lévő támadásoktól, vagy biztonságos módon kommunikáljanak egymással, beállíthatja, hogy az Azure-Virtual Network a felhőben a hálózat logikai ábrázolása legyen. Helyszíni hálózatot is összekapcsolhat a virtuális hálózattal az IPSec VPN (helyek közötti) vagy a ExpressRoute (privát társ) beállításával. A saját üzemeltetésű Integration Runtime telepíthető egy helyszíni gépen vagy virtuális gépen a Virtual Network a másolási tevékenységek futtatására egy felhőalapú adattár és egy magánhálózat adattára között, vagy átalakíthatja a tevékenységeket egy helyszíni hálózaton vagy egy Azure-beli virtuális hálózaton lévő számítási erőforrásokkal szemben. 

Több kommunikációs csatorna szükséges a Data Factory és az ügyfél virtuális hálózata között.


| **Tartomány** | **Port** | **Leírás** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Vezérlő síkja Data Factory létrehozásához és figyeléséhez szükséges. |
| `*.{region}.datafactory.azure.net` | 443 | A saját üzemeltetésű Integration Runtime a Data Factory szolgáltatáshoz való kapcsolódáshoz szükséges. |
| `*.servicebus.windows.net` | 443 | Az interaktív létrehozáshoz a saját üzemeltetésű Integration Runtime szükséges. |
| `download.microsoft.com` | 443 | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. |


A Azure Data Factory Azure Private-hivatkozásának támogatásával létrehozhat egy privát végpontot (PE) a virtuális hálózatban, és engedélyezheti a magánhálózati kapcsolatot adott Azure Data Factoryhoz. A Azure Data Factory szolgáltatással folytatott kommunikáció az Azure privát hivatkozásán keresztül érhető el, amely biztonságos privát kapcsolatot biztosít. Nem kell konfigurálnia a tartományt és a portot a virtuális hálózaton vagy a vállalati tűzfalon, amely biztonságosabban biztosítja az erőforrások védelmét.  

![Azure Data Factory privát kapcsolat architektúrája](./media/data-factory-private-link/private-link-architecture.png)

Az alábbi előnyökkel jár a Private link Service engedélyezése a fent ábrázolt kommunikációs csatornákon:
- Támogatott Megteheti a virtuális hálózatban lévő Azure Data Factory készítését és figyelését, még akkor is, ha letiltja az összes kimenő kommunikációt.
- Támogatott A saját üzemeltetésű Integration Runtime és Azure Data Factory szolgáltatás közötti kommunikáció a magánhálózati környezetekben biztonságosan végezhető el. A saját üzemeltetésű Integration Runtime és Azure Data Factory szolgáltatás közötti forgalom privát kapcsolaton keresztül halad. 
- (Jelenleg nem támogatott) A saját üzemeltetésű Integration Runtime használatával történő interaktív szerzői műveletek privát hivatkozáson keresztül érhetők el, például a kapcsolat tesztelése, a mappák listájának és a táblázatok listájának lekérése, a séma beolvasása és az előnézet
- (Jelenleg nem támogatott) Ha engedélyezi az automatikus frissítést, a saját üzemeltetésű Integration Runtime új verziója automatikusan letölthető a letöltőközpontból.

> [!NOTE]
> A jelenleg nem támogatott funkciók esetében továbbra is konfigurálnia kell a tartományt és a portot a virtuális hálózaton vagy a vállalati tűzfalon. 

> [!WARNING]
> A társított szolgáltatás létrehozásakor ellenőrizze, hogy a hitelesítő adatokat Azure Key Vault tárolja-e a rendszer. Ellenkező esetben nem működik, ha engedélyezi a Azure Data Factory a Private link Service-t.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Privát hivatkozás beállítása Azure Data Factoryhoz
A magánhálózati végpontokat a Azure Portal, a PowerShell vagy az Azure CLI használatával lehet létrehozni:

[Portál](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


A Azure Data Factory is megnyithatja Azure Portal és létrehozhat privát végpontot (PE):

![Privát végpont létrehozása](./media/data-factory-private-link/create-private-endpoint.png)


Ha le szeretné tiltani a Azure Data Factoryhoz való nyilvános hozzáférést, és csak privát kapcsolaton keresztül engedélyezi a hozzáférést, letilthatja Azure Data Factory hálózati hozzáférését Azure Portal:

![Privát végpont létrehozása](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> A nyilvános hálózati hozzáférés letiltása csak a saját üzemeltetésű Integration Runtimera vonatkozik, nem Azure Integration Runtime és SSIS Integration Runtime.

> [!NOTE]
> A nyilvános hálózati hozzáférés letiltása után a felhasználók továbbra is hozzáférhetnek Azure Data Factory portál nyilvános hálózaton keresztül.

## <a name="next-steps"></a>Következő lépések

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületén](quickstart-create-data-factory-portal.md)

- [Az Azure Data Factory bemutatása](introduction.md)

- [Visual authoring in Azure Data Factory](author-visually.md)

