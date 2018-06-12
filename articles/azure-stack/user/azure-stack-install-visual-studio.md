---
title: Telepítse a Visual Studio, és csatlakozzon az Azure-verem |} Microsoft Docs
description: Ismerje meg, a Visual Studio telepítése, és kapcsolódjon az Azure-verem szükséges lépések
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295030"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Telepítse a Visual Studio, és csatlakozzon az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Visual Studio használatával és Azure Resource Manager üzembe helyezését [sablonok](azure-stack-arm-templates.md) Azure verem. A cikkben ismertetett végigvezetik Önt a Visual Studio telepíti a [Azure verem](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy külső számítógépen, ha azt tervezi, hogy Azure verem keresztül a [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>A Visual Studio telepítése

1. Töltse le és futtassa a [Webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Nyissa meg a **Microsoft Webplatform-telepítő**.

3. Keresse meg **a Visual Studio Community 2015 Microsoft Azure SDK - 2.9.6**. Kattintson a **hozzáadása**, és **telepítése**.

4. Távolítsa el a **Microsoft Azure PowerShell** az Azure SDK részeként telepített.

    ![Képernyőfelvétel a WebPI telepítés lépései](./media/azure-stack-install-visual-studio/image1.png) 

5. [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)

6. Újraindítja az operációs rendszert a telepítés befejezése után.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Azure veremnek az Azure AD Connect

1. Indítsa el a Visual Studio.

2. Az a **nézet** menü **Cloud Explorer**.

3. Jelölje ki az új ablaktáblán **fiók hozzáadása** , és jelentkezzen be Azure Active Directory (Azure AD) hitelesítő adataival.  

    ![Képernyőkép az Cloud Explorer egyszer bejelentkezve, és kapcsolódik az Azure-verem](./media/azure-stack-install-visual-studio/image2.png)

Miután bejelentkezett, [sablonok telepítése](azure-stack-deploy-template-visual-studio.md) vagy tallózással keresse meg a rendelkezésre álló típusok és az erőforráscsoportok saját sablonok létrehozása.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Csatlakozás Azure verem az AD FS-sel

1. Indítsa el a Visual Studio.

2. A **eszközök**, jelölje be **beállítások**.

3. Bontsa ki a **környezet** a a **navigációs ablaktábla** válassza **fiókok**.

4. Válassza ki **Hozzáadás**, és írja be a felhasználó Azure Resource Manager-végpontot.  
  Az URL-cím pedig az Azure verem szoftverfejlesztői készlet: `https://management.local.azurestack/external`.  
  Az Azure-verem integrált rendszerek az URL-cím van: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Válassza a **Hozzáadás** lehetőséget.  

    A Visual Studio meghívja az Azure erőforrás-kezelője, és felderíti a végpontok, többek között a hitelesítési végpontot az Azure Directory összevont szolgáltatások (AD FS).

    ![Képernyőkép az Cloud Explorer egyszer bejelentkezve, és kapcsolódik az Azure-verem](./media/azure-stack-install-visual-studio/image6.png)

6. Válassza ki **Cloud Explorer** a a **nézet** menü.
7. Válassza ki **fiók hozzáadása** , és jelentkezzen be az AD FS hitelesítő adataival.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer lekérdezi az elérhető előfizetésekkel. Is kiválaszthat egyet az elérhető kezeléséhez.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Keresse meg a meglévő erőforrásokat, az erőforráscsoportok, vagy a sablonok telepítése.

## <a name="next-steps"></a>További lépések

 - Tudjon meg többet az [párhuzamos](https://msdn.microsoft.com/library/ms246609.aspx) más Visual Studio verzióival.
 - [Az Azure-verem sablonok fejlesztése](azure-stack-develop-templates.md)