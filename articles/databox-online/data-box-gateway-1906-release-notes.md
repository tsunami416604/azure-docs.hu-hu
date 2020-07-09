---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 kibocsátási megjegyzések | Microsoft Docs
description: Az 1906-es kiadást futtató Azure Data Box Gateway és Azure Data Box Edge kritikus nyitott problémáit és megoldásait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 9f4633a3fcafcb2b3151270715036e7940eb1c91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82561844"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge és Azure Data Box Gateway 1906 kibocsátási megjegyzései

A következő kibocsátási megjegyzések azonosítják a kritikus nyitott problémákat, valamint a Azure Data Box Edge és Azure Data Box Gateway 1906-es kiadásával kapcsolatos megoldott problémákat. 

A kibocsátási megjegyzések folyamatosan frissülnek, és olyan kritikus fontosságú problémák észlelhetők, amelyek megkerülő megoldást igényelnek. A Data Box Edge/Data Box Gateway üzembe helyezése előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

Ez a kiadás megfelel a szoftver verzióinak:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Az 1906-es frissítés Data Box Edge csak a szoftver általánosan elérhető (GA) vagy 1905-os verzióját futtató eszközökre alkalmazható.

## <a name="whats-new"></a>Újdonságok

- **Hibajavítás a helyreállítási kulcs kezelése munkafolyamatban** – a korábbi kiadásban hiba történt, ami miatt a helyreállítási kulcs nem lett alkalmazva. Ez a hiba ebben a kiadásban van kijavítva. Javasoljuk, hogy ezt a frissítést a helyreállítási kulcs lehetővé teszi, hogy helyreállítsa az eszközön lévő adatokat, abban az esetben, ha az eszköz nem indul el. További információ: [a helyreállítási kulcs mentése Data Box Edge vagy Data Box Gateway telepítésekor](azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- A **Field programozható Gate array (FPGA) naplózási funkcióinak fejlesztései** – a 1905-es verzióval kezdődően a FPGA kapcsolatos naplózási és riasztási fejlesztések történtek. Ez továbbra is szükséges frissítés a Data Box Edge esetén, ha az Edge számítási funkciót használja a FPGA. További információkért lásd: [adatok átalakítása az Edge számítási](azure-stack-edge-deploy-configure-compute-advanced.md)szolgáltatással a Data Box Edge.

## <a name="known-issues-in-ga-release"></a>Ismert problémák a GA kiadásban

Ebben a kiadásban nem jelennek meg új problémák a kiadásban. Az összes megjelent kiadási probléma az előző kiadásokból lett végrehajtva. Ha meg szeretné tekinteni az ismert problémák listáját, lépjen [a ga kiadás ismert problémáira](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)
- [Az Azure Data Box Edge üzembe helyezésének előkészítése](azure-stack-edge-deploy-prep.md)
