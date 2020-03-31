---
title: 'Azure AD Connect felhőszolgáltató ügynök: Automatikus frissítés | Microsoft dokumentumok'
description: Ez a cikk ismerteti a beépített automatikus frissítési funkció az Azure AD Connect felhőalapú kiépítési ügynök.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190313"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect felhőszolgáltató: Automatikus frissítés

Az automatikus frissítési funkcióval egyszerűen győződjön meg arról, hogy az Azure Active Directory (Azure AD) Connect felhőbeli kiépítési ügynöktelepítése mindig naprakész.

Az ügynök itt van telepítve: "Program files\Azure AD Connect provisioning Agent\AADConnectProvisioningAgent.exe"

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, és válassza ki a tulajdonságokat, majd a részleteket.

![Ügynökfájl verziója](media/how-to-automatic-upgrade/agent1.png)

Az ügynökfrissítő itt van telepítve: "Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, és válassza ki a tulajdonságokat, majd a részleteket.

![Ügynökfrissítő verziója](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Ügynök eltávolítása
Az ügynök eltávolításához nyissa meg az **Eltávolítandó vagy módosított programot,** és távolítsa el az alábbiakat:

- **Microsoft Azure AD Connect ügynökfrissítő**
- **Microsoft Azure AD Connect kiépítési ügynök**
- **Microsoft Azure AD Connect kiépítési ügynök csomag**

![Ügynök eltávolítása](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

