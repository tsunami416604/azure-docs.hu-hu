---
title: Telepítse a Visual Studio, és csatlakozzon az Azure-verem |} Microsoft Docs
description: Ismerje meg, a Visual Studio telepítése, és kapcsolódjon az Azure-verem szükséges lépések
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Telepítse a Visual Studio, és csatlakozzon az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Visual Studio használata és telepítése Azure Resource Manager [sablonok](azure-stack-arm-templates.md) Azure-készletben. Segítségével a jelen cikkben ismertetett lépéseket telepítse a Visual Studio eszközből [Azure verem szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha keresztül kapcsolódó [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Ezeket a lépéseket, a cikkben egy új Visual Studio 2015 Community Edition-példányhoz van. Tudjon meg többet az [párhuzamos](https://msdn.microsoft.com/library/ms246609.aspx) más Visual Studio verzióival.

## <a name="install-visual-studio"></a>A Visual Studio telepítése

1. Töltse le és futtassa a [Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx).
2. Keresse meg **Visual Studio Community 2015 Microsoft Azure SDK - 2.9.6**, jelölje be **Hozzáadás**, majd válassza ki **telepítése**.

    ![Képernyőfelvétel-készítés WebPI telepítési lépéseket](./media/azure-stack-install-visual-studio/image1.png)

3. Távolítsa el a **Microsoft Azure PowerShell** az Azure SDK részeként telepített.

    ![Azure PowerShell telepítése és törlése programok felület képernyőfelvétel-készítés](./media/azure-stack-install-visual-studio/image2.png)

4. [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)

5. Újraindítja az operációs rendszert a telepítés befejezése után.

## <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

1. Indítsa el a Visual Studio.

2. Az a **nézet** menü **Cloud Explorer**.

3. Jelölje ki az új ablaktáblán **fiók hozzáadása** , és jelentkezzen be Azure Active Directory hitelesítő adataival.
    ![Miután bejelentkezik a, és csatlakozik Azure verem Cloud Explorer képernyőfelvétel-készítés](./media/azure-stack-install-visual-studio/image6.png)

Miután bejelentkezett, [sablonok telepítése](azure-stack-deploy-template-visual-studio.md) vagy tallózással keresse meg a rendelkezésre álló típusok és az erőforráscsoportok saját sablonok létrehozása.

## <a name="next-steps"></a>További lépések

* [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)
