---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 kiadási megjegyzések| Microsoft dokumentumok
description: A cikk az Azure Data Box Gateway és az Azure Data Box Edge 1906-os kiadást futtató fontos nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099485"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Az Azure Data Box Edge és az Azure Data Box Gateway 1906 kiadási megjegyzések

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és az Azure Data Box Edge és az Azure Data Box Gateway 1906-os kiadásának megoldott problémáit.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A Data Box Edge/Data Box Gateway telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

Ez a kiadás a szoftververzióknak felel meg:

- **Data Box átjáró 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Az 1906-os frissítés csak a Data Box Edge általános elérhetőségi (GA) vagy a szoftver 1905-ös verzióját futtató Data Box Edge-eszközökre alkalmazható.

## <a name="whats-new"></a>Újdonságok

- **Hibajavítás a helyreállítási kulcs kezelési munkafolyamatában** - A korábbi kiadásban volt egy hiba, amely miatt a helyreállítási kulcs nem lett alkalmazva. Ez a hiba ebben a kiadásban javítva. Javasoljuk, hogy alkalmazza ezt a frissítést, mivel a helyreállítási kulcs lehetővé teszi az eszközön lévő adatok helyreállítását abban az esetben, ha az eszköz nem indul el. További információ: A [helyreállítási kulcs mentése a Data Box Edge vagy a Data Box Gateway telepítésekor.](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)
- **Field Programmable Gate Array (FPGA) naplózási fejlesztések** - Az 1905-ös kiadás indításakor az FPGA-val kapcsolatos naplózási és riasztási fejlesztések történtek. Ez továbbra is a Data Box Edge szükséges frissítése, ha az Edge számítási funkciót használja az FPGA-val. További információ: Hogyan [alakíthatja át az adatokat az Edge-számítással a Data Box Edge-en.](data-box-edge-deploy-configure-compute-advanced.md)

## <a name="known-issues-in-ga-release"></a>Ismert problémák a GA kiadásban

Ehhez a kiadáshoz nem jelennek meg új problémák. Az összes kiadás megjegyezte, hogy a korábbi kiadásokból átkerültek. Az ismert problémák listájának megtekintéséhez olvassa el [az Ismert problémák a GA kiadásban című témakört.](data-box-gateway-release-notes.md#known-issues-in-ga-release)


## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)
- [Az Azure Data Box Edge üzembe helyezésének előkészítése](data-box-edge-deploy-prep.md)
