---
title: Nyilvános környezetek konfigurálása és használata az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja és használhatja a nyilvános környezetek (Azure Resource Manager-sablonok egy Git-tárházban) az Azure DevTest Labs.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721693"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Nyilvános környezetek konfigurálása és használata az Azure DevTest Labsben
Az Azure DevTest Labs rendelkezik egy [nyilvános tárház az Azure Resource Manager-sablonok,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) amely segítségével környezetek létrehozása anélkül, hogy egy külső GitHub-forráshoz egyedül. Ez a tárház olyan gyakran használt sablonokat tartalmaz, mint az Azure Web Apps, a Service Fabric Cluster és a fejlesztői SharePoint Farm környezet. Ez a funkció hasonló a nyilvános tárház a műtermékek, amely tartalmazza az egyes létrehozott tesztkörnyezetben. A környezeti tárház lehetővé teszi, hogy gyorsan elkezdje az előzetesen megírt környezeti sablonok minimális bemeneti paraméterekkel, hogy zökkenőmentes engetagóni a PaaS-erőforrások laborokon belüli zökkenőmentes élményét biztosítsa. 

## <a name="configuring-public-environments"></a>Nyilvános környezetek konfigurálása
A labor tulajdonosaként engedélyezheti a nyilvános környezeti tárház a labor létrehozása során. Ha engedélyezni szeretné a nyilvános környezeteket a laborban, válassza **a Be** lehetőséget a **Nyilvános környezetek** mezőben egy tesztkörnyezet létrehozása közben. 

![Nyilvános környezet engedélyezése új laborszámára](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


A meglévő laborok esetében a nyilvános környezeti tárház nincs engedélyezve. Manuálisan engedélyezze a sablonok használatát a tárházban. Az Erőforrás-kezelő sablonokkal létrehozott laborok esetében a tárház alapértelmezés szerint is le van tiltva.

Engedélyezheti/letilthatja a nyilvános környezeteket a laborban, és csak bizonyos környezeteket tehet elérhetővé a tesztkörnyezet felhasználói számára az alábbi lépésekkel: 

1. Válassza a **konfiguráció és a labor házirendek** lehetőséget. 
2. A **VIRTUÁLISGÉP-BÁZISOK** csoportban válassza a **Nyilvános környezetek**lehetőséget.
3. Ha engedélyezni szeretné a tesztkörnyezet nyilvános környezetét, válassza az **Igen**lehetőséget. Egyéb esetben a **Nem** választógombot jelölje be. 
4. Ha engedélyezte a nyilvános környezeteket, a tárház összes környezete alapértelmezés szerint engedélyezve van. Kiválaszthat egy környezetet, hogy az ne legyen elérhető a tesztkörnyezet felhasználói számára. 

![Nyilvános környezetek lap](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Környezeti sablonok használata tesztkörnyezet-felhasználóként
Laborfelhasználóként létrehozhat egy új környezetet a környezeti sablonok engedélyezett listájából, ha egyszerűen kiválasztja a **+Hozzáadás** lehetőséget a laborlap eszköztárából. A bázisok listája tartalmazza a nyilvános környezetek sablonok által engedélyezett a labor rendszergazdája a lista tetején.

![Nyilvános környezeti sablonok](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>További lépések
Ez a tárház egy nyílt forráskódú tárház, amely hozzájárulhat a gyakran használt és hasznos Erőforrás-kezelő sablonok hozzáadásához. A hozzájáruláshoz egyszerűen küldjön be egy lekéréses kérelmet a tárház ellen.  
