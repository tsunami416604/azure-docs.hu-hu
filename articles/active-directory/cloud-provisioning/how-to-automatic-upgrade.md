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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
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

## <a name="uninstall-the-agent"></a>Az ügynök eltávolítása
Az ügynök eltávolításához lépjen az **Eltávolítás vagy a program módosítása** elemre, és távolítsa el a következőket:

- **Microsoft Azure AD összekapcsolási ügynök frissítése**
- **Microsoft Azure AD kiépítési ügynök összekötése**
- **Microsoft Azure AD létesítési ügynök csomagjának összekötése**

![Ügynök eltávolítása](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>További lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)

