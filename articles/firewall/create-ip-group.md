---
title: IP-csoportok létrehozása a Azure Firewallban
description: Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85602533"
---
# <a name="create-ip-groups"></a>IP-csoportok létrehozása

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