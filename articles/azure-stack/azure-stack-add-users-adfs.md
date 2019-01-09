---
title: Felhasználók hozzáadása az Azure Stack AD FS |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá felhasználókat az Azure Stack az AD FS-telepítések
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120812"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Azure Stack-felhasználók hozzáadása az AD FS-ben
Használhatja a **Active Directory – felhasználók és számítógépek** beépülő modul segítségével adhat hozzá további felhasználókat az Azure Stack-környezet az identitás-szolgáltatóként az AD FS kihasználva.

## <a name="add-windows-server-active-directory-users"></a>A Windows Server Active Directory-felhasználók hozzáadása
> [!TIP]
> Ebben a példában az alapértelmezett azurestack.local ASDK active Directoryt használja. 

1.  Jelentkezzen be a számítógépre egy olyan fiókkal, hogy hozzáférést biztosíthat a Windows felügyeleti eszközök, és nyisson meg egy új Microsoft Management Console (MMC).
2.  Kattintson a **fájl > hozzáadása vagy eltávolítása a beépülő modul**.
3.  Válassza ki **az Active Directory – felhasználók és számítógépek** > **AzureStack.local** > **felhasználók**.
4.  Kattintson a **művelet** > **új** > **felhasználói**.
5.  Az új objektum – felhasználó ablakban adja meg, és a egy jelszó megerősítése
6.  Kattintson a **tovább** véglegesítése az értékeket, és kattintson a Befejezés gombra a felhasználó létrehozásához.


## <a name="next-steps"></a>További lépések
[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)