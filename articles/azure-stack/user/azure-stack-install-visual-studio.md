---
title: "Telepítse a Visual Studio, és csatlakozzon az Azure-verem |} Microsoft Docs"
description: "Ismerje meg, a Visual Studio telepítése, és kapcsolódjon az Azure-verem szükséges lépések"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: f85584b02f6b2f7436f711e794bb8fcad00ced4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Telepítse a Visual Studio, és csatlakozzon az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Visual Studio használata és telepítése Azure Resource Manager [sablonok](azure-stack-arm-templates.md) Azure-készletben. Segítségével a jelen cikkben ismertetett lépéseket telepítse a Visual Studio eszközből [Azure verem szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha keresztül kapcsolódó [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). A lépések a Visual Studio 2015 Community Edition új telepítés végrehajtásához. Tudjon meg többet az [párhuzamos](https://msdn.microsoft.com/library/ms246609.aspx) más Visual Studio verziói között.

## <a name="install-visual-studio"></a>A Visual Studio telepítése
1. Töltse le és futtassa a [Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Keresse meg **Visual Studio Community 2015 Microsoft Azure SDK - 2.9.6**, kattintson a **Hozzáadás**, és **telepítése**.

    ![Képernyőfelvétel a WebPI telepítés lépései](./media/azure-stack-install-visual-studio/image1.png) 

3. Távolítsa el a **Microsoft Azure PowerShell** az Azure SDK részeként telepített.

    ![Képernyőkép a Azure PowerShell telepítése és törlése programok felület](./media/azure-stack-install-visual-studio/image2.png) 

4. [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)

5. Újraindítja az operációs rendszert a telepítés befejezése után.

## <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

1. Indítsa el a Visual Studio.

2. Az a **nézet** menü **Cloud Explorer**.

3. Jelölje ki az új ablaktáblán **fiók hozzáadása** , és jelentkezzen be Azure Active Directory hitelesítő adataival.  
    ![Képernyőkép az Cloud Explorer egyszer bejelentkezve, és kapcsolódik az Azure-verem](./media/azure-stack-install-visual-studio/image6.png)

Miután bejelentkezett, [sablonok telepítése](azure-stack-deploy-template-visual-studio.md) vagy tallózással keresse meg a rendelkezésre álló típusok és az erőforráscsoportok saját sablonok létrehozása.  

## <a name="next-steps"></a>Következő lépések

 - [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)
