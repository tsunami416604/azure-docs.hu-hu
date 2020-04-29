---
title: 'Azure AD Connect Cloud kiépítési ügynök: automatikus frissítés | Microsoft Docs'
description: Ez a cikk a Azure AD Connect Cloud kiépítési ügynök beépített automatikus frissítési funkcióját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190313"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud kiépítési ügynök: automatikus frissítés

Győződjön meg arról, hogy a Azure Active Directory (Azure AD) a felhőalapú üzembe helyezési ügynök telepítése mindig naprakész, és az automatikus frissítési funkció egyszerű.

Az ügynök itt van telepítve: "program files\Azure AD-kapcsolat létesítése Agent\AADConnectProvisioningAgent.exe"

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, majd válassza a tulajdonságok, majd a részletek elemet.

![Ügynök fájljának verziója](media/how-to-automatic-upgrade/agent1.png)

Az ügynök-Updater itt van telepítve: "program files\Azure AD-létesítési ügynök Updater\AzureADConnectAgentUpdater.exe"

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, majd válassza a tulajdonságok, majd a részletek elemet.

![Ügynök frissítési verziója](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Ügynök eltávolítása
Az ügynök eltávolításához lépjen az **Eltávolítás vagy a program módosítása** elemre, és távolítsa el a következőket:

- **Microsoft Azure AD összekapcsolási ügynök frissítése**
- **Microsoft Azure AD kiépítési ügynök összekötése**
- **Microsoft Azure AD létesítési ügynök csomagjának összekötése**

![Ügynök eltávolítása](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

