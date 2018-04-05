---
title: Egy egyéni Piactéri elemet közzététele az Azure-vermet (felhő üzemeltetője) |} Microsoft Docs
description: Azure verem kezelőként útmutató egy egyéni Piactéri elemet közzététele az Azure-készletben.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 37587cf5f99ce105413382cbd5a0de9cacc76b7d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="the-azure-stack-marketplace-overview"></a>Az Azure verem piactér – áttekintés

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A piactér gyűjteményei, szolgáltatások, alkalmazások és erőforrások testreszabott Azure vermét, például a hálózatok, virtuális gépek, tárolási és így tovább. Felhasználók itt új erőforrások létrehozása és telepítése az új alkalmazások származnak. Úgy is, mint egy vásárlásra szolgáló katalógust, ahol a felhasználók keresse meg és válassza ki a használni kívánt elemeket. A Piactéri elemet használatához felhasználók elő kell fizetnie az ajánlat, amely a cikk hozzáférést biztosít.

Azure verem kezelőként hozzáadni kívánt elemek mellett dönt a piactér (közzététel). Beállíthatja például adatbázisok, alkalmazásszolgáltatások és így tovább is. Így a felhasználók számára látható. Közzéteheti az Ön által létrehozott egyéni elemet. Egy növekvő szereplő elemeket is közzéteheti [Azure piactéren elemek listáját](azure-stack-marketplace-azure-items.md). Ha közzéteszi a Piactéri elemet, a felhasználók láthatják az öt percen belül.

A piactér megnyitásához kattintson **új**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace-elemek
Egy Azure verem piactér elem egy szolgáltatás, alkalmazás vagy erőforrás, a felhasználók által töltse le és használja. Minden Azure verem Piactéri elemek minden felhasználó számára láthatók.

Minden piactér elemnek:

* Az Azure Resource Manager sablon erőforrás üzembe helyezéséhez
* Metaadatok, például karakterláncok, ikonok és egyéb marketing biztosíték
* A cikk a portálon megjelenítendő adatok formázása

Minden elem a piactér közzétett használja az Azure Katalóguscsomagban (azpkg) formátumban. Központi telepítés, illetve runtime jelöléssel erőforrások hozzáadása (például a kódot, a szoftver- vagy virtuálisgép-rendszerképek zip-fájlok) Azure verem külön-külön, ne a Piactéri elemet részeként. 

1803 kezdve képek alakulnak ritka fájlok az Azure-ból letöltése során, vagy az egyéni lemezképek feltöltésekor. Ez a folyamat egyre több idő lemezkép való hozzáadásakor, de helyet takarít meg, és felgyorsítja a képek telepítését. Új lemezképek csak érvényes átalakítás. Meglévő lemezképet, nem módosulnak. 

## <a name="next-steps"></a>További lépések
[Hozzon létre, és tegye közzé a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md)

