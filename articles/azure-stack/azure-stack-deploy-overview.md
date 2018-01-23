---
title: "Értékelje ki a Azure verem szoftverfejlesztői készlet |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti az Azure verem szoftverfejlesztői készlet tesztelési célokra."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Gyors üzembe helyezés: Az Azure verem szoftverfejlesztői készlet kiértékelése

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

A [Azure verem szoftverfejlesztői készlet](azure-stack-poc.md) egy tesztelési és fejlesztési környezet, amely értékeli, és bemutatja a verem Azure-szolgáltatások és szolgáltatások telepítése. Töltse le innen működik, és lesz szüksége a környezet hardver készít, és néhány (Ez órát fog igénybe venni több) parancsfájlok futtatása. Ezután bejelentkezhet a kezelése az Azure-vermet, és tesztelje a ajánlatok rendszergazdai és felhasználói portálon. 

1. [**Tervezze meg a hardveres, szoftveres és hálózati**](azure-stack-deploy.md). A szoftverfejlesztői készlet (development kit fogadó) futtató számítógépen meg kell felelnie hardveres, szoftveres és hálózati követelményeknek. Azure Active Directory vagy Active Directory összevonási szolgáltatások használata között is ki kell választania. Ne feledje, hogy a telepítési folyamat zökkenőmentesen, a a telepítés elindítása előtt az Előfeltételek ahhoz, hogy. 

2. [**Töltse le, és bontsa ki a központi telepítési csomag**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). A központi telepítési csomag letöltheti a development kit gazdagépre vagy egy másik számítógépre. A kibontott tároló 60 GB szabad lemezterület, tarthat, ezért egy másik számítógép segítségével csökkentheti a development kit gazdagép hardverkövetelményeinek.

3. [**Készítse elő a development kit gazdagép** ](azure-stack-run-powershell-script.md) a telepítő használatával. Ez a lépés után a development kit gazdagép fog indulni, a Cloudbuilder.vhdx (egy virtuális merevlemez-meghajtóról, amely egy rendszerindításra alkalmas operációs rendszer és az Azure-verem tartalmazza a fájlok telepítéséhez).

4. [**A csomag telepítése** ](azure-stack-run-powershell-script.md) a development kit gazdagépen.

5. Ha az Azure Alkalmazásveremben üzembe az Azure Active Directory használ, meg kell [Azure verem regisztrálni Azure](azure-stack-register.md) , hogy [töltse le az Azure piactéren elemek](azure-stack-download-azure-marketplace-item.md) Azure verem.

A lépések elvégzése után kell a csomag környezet a rendszergazdai és felhasználói portálon. A következő is [csatlakozzon, és jelentkezzen be a](azure-stack-connect-azure-stack.md) a portálra. El is indíthatja erőforrás-szolgáltatók telepítéséhez, létrehozása [kínál](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions), és feltöltése az Azure-verem [piactér](azure-stack-marketplace.md).
