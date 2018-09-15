---
title: Egyéni Piactéri elem közzététele az Azure Stackben (felhőüzemeltető) |} A Microsoft Docs
description: Az Azure Stack operátorait szerint megtudhatja, hogyan egyéni Piactéri elem közzététele az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 12310c088777d65bef211747806f942433857e40
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632348"
---
# <a name="the-azure-stack-marketplace-overview"></a>Az Azure Stack piactéren – áttekintés

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A piactér olyan szolgáltatások, alkalmazások és testre szabott Azure stack-erőforrások gyűjteménye. Erőforrások közé tartoznak a hálózatok, virtuális gépek, tárolási és így tovább. Felhasználók itt új erőforrásokat hozhat létre és helyezzen üzembe új alkalmazásokat jár. Tekintsen rá úgy mint a vásárlási katalógus, ahol a felhasználók tallózással keresse meg és válassza ki a használni kívánt elemeket. Piactéri elem használandó felhasználók kell előfizetés egy ajánlatra, amely hozzáférést biztosít számukra az elemet.

Az Azure Stack operátorait szerint dönt, hogy a hozzáadni kívánt elemek (közzététel) a piactéren. Közzéteheti, például adatbázisok, alkalmazásszolgáltatások, és így tovább. Közzététel őket a felhasználók számára láthatóvá teszi. Teheti közzé egyéni elemeket. Is közzéteheti az egyre növekvő elemek [Azure Marketplace-elemek listáját](azure-stack-marketplace-azure-items.md). A piactéren tesz közzé egy elemet, ha a felhasználók láthatják őket öt percen belül.

> [!Caution]  
> Minden gyűjtemény elem összetevők képek és a json-fájlokat más néven őket az Azure Stack piactéren elérhető elvégzése után hitelesítés nélkül érhetők el. Egyéni Piactéri elemek közzétételekor további szempontokért lásd: [létrehozása és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md).

Nyissa meg a Marketplace-en, hogy a felügyeleti konzolban válassza **+ erőforrás létrehozása**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace-elemek
Az Azure Stack piactéren elem egy szolgáltatás, alkalmazás vagy erőforrás, amely a felhasználók letöltheti és használhatja. Az összes Azure Stack piactéren elemek láthatók minden felhasználó számára, beleértve a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nincs szükség egy előfizetési nézet, de azok nem működik a felhasználók számára.

Minden Marketplace-en elemnek:

* Az Azure Resource Manager-sablonok, az erőforrás-kiépítés
* Metaadatok, mint például a karakterláncokat, az ikonok és az egyéb marketinganyag
* A cikk a portálon megjelenítendő adatok formázása

A piactéren közzétett minden eleme az Azure Gallery-csomag (.azpkg) formátumot használja. Üzembe helyezés vagy a futásidejű erőforrások hozzáadása (például a kód, a szoftver vagy a virtuális gépek lemezképeit tartalmazó zip-fájlok) az Azure Stackhez külön-külön, a Piactéri elem nem részeként. 

Az 1803 és az újabb Azure Stack alakítja lemezképek ritka fájlokat, amikor letöltik az Azure-ban, vagy ha egyéni lemezképeket tölthetnek fel. Ez a folyamat hozzáadja az idő kép hozzáadásakor viszont helyet takarít, és lerövidíti az üzembe helyezés ilyen rendszerképpel. Új képek csak érvényes átalakítás.  Meglévő lemezképek nem változnak. 

## <a name="next-steps"></a>További lépések
[Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
[Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)

