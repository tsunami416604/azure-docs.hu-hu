---
title: Konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: d93818cd875c4050b1b35f21ce580933776c5bc5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234943"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs rendelkezik egy [nyilvános tárházban az Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , amelyek segítségével való csatlakozáshoz külső GitHub forrás saját magának nélkül hozhat létre környezeteket. Ez a tárház például az Azure Web Apps, a Service Fabric-fürt és a fejlesztési környezet SharePoint-Farm gyakran használt sablont tartalmaz. Ez a funkció az összetevők a nyilvános tárház, amely tartalmaz minden Ön által létrehozott laborban hasonlít. A környezet tárház használatának gyors megkezdése minimális bemeneti paraméterei, zökkenőmentes első lépések felhasználói élményt biztosíthat a PaaS-erőforrásokhoz labs belül az előre létrehozott környezeti sablonokkal teszi lehetővé. 

## <a name="configuring-public-environments"></a>A nyilvános környezetben konfigurálása
Labortulajdonosként a nyilvános környezetben tárház engedélyezheti a tesztkörnyezethez a labor létrehozása során. Ahhoz, hogy a nyilvános környezetben a tesztkörnyezethez, válassza ki a **a** számára a **nyilvános környezetben** mezőt a labor létrehozása során. 

![Új labor nyilvános környezet engedélyezése](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


A nyilvános környezetben tárházban meglévő laborral kapcsolatos nincs engedélyezve. Manuálisan engedélyezze, hogy a sablonok használatához a tárházban. A Resource Manager-sablonok használatával létrehozott labs az adattár is alapértelmezés szerint le van tiltva.

Engedélyezi/letiltja a nyilvános környezetben a tesztkörnyezethez, szabadon is elérhetővé csak az adott környezetben labor számára az alábbi lépések segítségével: 

1. Válassza ki **Konfigurace a zásady** a tesztkörnyezethez. 
2. Az a **VIRTUÁLISGÉP-BÁZISOK** szakaszban jelölje be **nyilvános környezetben**.
3. Ahhoz, hogy a labor a nyilvános környezetben, válasszon **Igen**. Ellenkező esetben válassza **nem**. 
4. Ha engedélyezte a nyilvános környezetben, az alapértelmezés engedélyezve vannak a tárházban minden környezetben. Válassza ki a környezet nem elérhető legyen a labor számára megszüntetéséhez. 

![A nyilvános környezetben lap](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Lab felhasználói környezet sablonok használata
Labor felhasználóként, létrehozhat egy új környezet környezeti sablonokkal engedélyezett listájából egyszerűen kiválasztásával **+ Hozzáadás** a labor oldalon az eszköz segítségével. Az adatbázisok listája tartalmazza a nyilvános környezetben sablonokat, a lista tetején a labor-rendszergazda által engedélyezett.

![Nyilvános környezeti sablonokkal](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>További lépések
Ez a tárház egy nyílt forráskódú adattár, amelyben gyakran használt és hasznos saját Resource Manager-sablonok hozzáadása közreműködhet. Közreműködés, egyszerűen ellen a tárház pull-kérést küldhet.  
