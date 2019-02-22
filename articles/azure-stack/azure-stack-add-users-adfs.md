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
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648506"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Azure Stack-felhasználók hozzáadása az AD FS-ben
Használhatja a **Active Directory – felhasználók és számítógépek** beépülő modul segítségével adhat hozzá további felhasználókat az Azure Stack-környezet az identitás-szolgáltatóként az AD FS kihasználva.

## <a name="add-windows-server-active-directory-users"></a>A Windows Server Active Directory-felhasználók hozzáadása
> [!TIP]
> Ebben a példában az alapértelmezett azurestack.local ASDK active Directoryt használja. 

1. Jelentkezzen be a számítógépre egy olyan fiókkal, hogy hozzáférést biztosíthat a Windows felügyeleti eszközök, és nyisson meg egy új Microsoft Management Console (MMC).
2. Válassza ki **fájl > hozzáadása vagy eltávolítása a beépülő modul**.
3. Válassza ki **az Active Directory – felhasználók és számítógépek** > **AzureStack.local** > **felhasználók**.
4. Válassza ki **művelet** > **új** > **felhasználói**.
5. Az új objektum – felhasználó, adja meg a felhasználó részletes adatokkal. Kattintson a **Tovább** gombra.
6. Adja meg és erősítsen meg egy jelszót.
7. Válassza ki **tovább** értékek véglegesítéséhez. Válassza ki **Befejezés** a felhasználó létrehozásához.


## <a name="next-steps"></a>További lépések
[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)