---
title: IP-csoportok létrehozása a Azure Firewallban
description: Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444536"
---
# <a name="create-ip-groups-preview"></a>IP-csoportok létrehozása (előzetes verzió)

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését. Rendelkezhetnek egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartományok.

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása**lehetőséget.
2. A keresés szövegmezőbe írja be az **IP-csoportok** kifejezést, majd válassza az **IP-csoportok**lehetőséget.
3. Kattintson a **Létrehozás** gombra.
4. Válassza ki előfizetését.
5. Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.
6. Adjon egyedi nevet az IP-csoportnak, majd válasszon ki egy régiót.

6. Válassza a **Tovább: IP-címek**lehetőséget.
7. Írjon be egy IP-címet, több IP-címet vagy IP-címtartományt.

   Az IP-címeket kétféleképpen adhatja meg:
   - Manuálisan is megadhatja őket
   - Importálhatja őket egy fájlból.

   Fájlból való importáláshoz válassza az **Importálás fájlból**lehetőséget. Húzza a fájlt a mezőbe, vagy válassza a **Fájlok tallózása**lehetőséget. Ha szükséges, megtekintheti és szerkesztheti a feltöltött IP-címeit.

   Ha IP-címet ad meg, a portál ellenőrzi, hogy az átfedések, ismétlődések és formázási problémák ellenőrzéséhez szükségesek-e.

5. Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet.
6. Kattintson a **Létrehozás** gombra.


## <a name="next-steps"></a>További lépések

- [További információ az IP-csoportokról](ip-groups.md)