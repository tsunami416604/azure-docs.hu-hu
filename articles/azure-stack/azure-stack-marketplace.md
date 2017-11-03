---
title: "Egy egyéni Piactéri elemet közzététele az Azure-vermet (felhő üzemeltetője) |} Microsoft Docs"
description: "Azure verem kezelőként útmutató egy egyéni Piactéri elemet közzététele az Azure-készletben."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="the-azure-stack-marketplace-overview"></a>Az Azure verem piactér – áttekintés

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A piactér gyűjteményei, szolgáltatások, alkalmazások és erőforrások testreszabott Azure vermét, például a hálózatok, virtuális gépek, tárolási és így tovább. Felhasználók itt új erőforrások létrehozása és telepítése az új alkalmazások származnak. Úgy is, mint egy vásárlásra szolgáló katalógust, ahol a felhasználók keresse meg és válassza ki a használni kívánt elemeket. A Piactéri elemet használatához felhasználók elő kell fizetnie az ajánlat, amely a cikk hozzáférést biztosít.

Azure verem kezelőként hozzáadni kívánt elemek mellett dönt a piactér (közzététel). Beállíthatja például adatbázisok, alkalmazásszolgáltatások és így tovább is. Így a felhasználók számára látható. Közzéteheti az Ön által létrehozott egyéni elemet. Egy növekvő szereplő elemeket is közzéteheti [Azure piactéren elemek listáját](azure-stack-marketplace-azure-items.md). Ha közzéteszi a Piactéri elemet, a felhasználók láthatják az öt percen belül.

A piactér megnyitásához kattintson **új**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Piactéri elemek
Egy Azure verem piactér elem egy szolgáltatás, alkalmazás vagy erőforrás, a felhasználók által töltse le és használja. Minden Azure verem Piactéri elemek minden felhasználó számára láthatók.

Minden piactér elemnek:

* Az Azure Resource Manager sablon erőforrás üzembe helyezéséhez
* Metaadatok, például karakterláncok, ikonok és egyéb marketing biztosíték
* A cikk a portálon megjelenítendő adatok formázása

Minden elem a piactér közzétett használja az Azure Katalóguscsomagban (azpkg) formátumban. Központi telepítés, illetve runtime jelöléssel erőforrások hozzáadása (például a kódot, a szoftver- vagy virtuálisgép-rendszerképek zip-fájlok) Azure verem külön-külön, ne a Piactéri elemet részeként. 

## <a name="next-steps"></a>Következő lépések
[Hozzon létre, és tegye közzé a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md)

