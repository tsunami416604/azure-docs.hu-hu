---
title: IP-csoportok létrehozása az Azure tűzfalon
description: Az IP-csoportok lehetővé teszik az Azure Firewall-szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444536"
---
# <a name="create-ip-groups-preview"></a>IP-csoportok létrehozása (előzetes verzió)

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IP-csoportok lehetővé teszik az Azure Firewall-szabályok IP-címeinek csoportosítását és kezelését. Rendelkezhetnek egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartománysal.

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. Írja be **az IP-csoportok kifejezést** a keresőmezőbe, majd válassza az **IP-csoportok**lehetőséget.
3. Kattintson a **Létrehozás** gombra.
4. Válassza ki előfizetését.
5. Jelöljön ki egy erőforráscsoportot, vagy hozzon létre egy újat.
6. Írjon be egy egyedi nevet az IP-csoportnak, majd jelöljön ki egy régiót.

6. Válassza a **Tovább: IP-címek**lehetőséget.
7. Írjon be egy IP-címet, több IP-címet vagy IP-címtartományt.

   Az IP-címek kétféleképpen írják be:
   - Manuálisan is megadhatja őket
   - Importálhatja őket egy fájlból

   Ha fájlból szeretne importálni, válassza **az Importálás fájlból**lehetőséget. A fájlt áthúzhatja a dobozba, vagy a **Tallózás a fájlok után**lehetőséget választva. Szükség esetén áttekintheti és szerkesztheti a feltöltött IP-címeket.

   Ip-cím beírásakor a portál ellenőrzi az egymást átfedő, ismétlődési és formázási problémákat.

5. Ha végzett, válassza **a Véleményezés + Létrehozás lehetőséget.**
6. Kattintson a **Létrehozás** gombra.


## <a name="next-steps"></a>További lépések

- [További információ az IP-csoportokról](ip-groups.md)