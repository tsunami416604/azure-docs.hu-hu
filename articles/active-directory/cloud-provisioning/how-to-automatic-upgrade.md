---
title: 'Azure AD Connect Cloud kiépítési ügynök: automatikus frissítés | Microsoft Docs'
description: Ez a témakör a Azure AD Connect Cloud kiépítési ügynök beépített automatikus frissítési funkcióját ismerteti.
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794457"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud kiépítési ügynök: automatikus frissítés

Győződjön meg arról, hogy a Azure AD Connect Cloud kiépítési ügynök telepítése mindig naprakész, és soha nem volt ilyen egyszerű az **automatikus frissítési** funkció. Ez a funkció alapértelmezés szerint engedélyezve van, és nem tiltható le.

Az ügynök itt van telepítve: **"program FILES\AZURE ad-kapcsolat létesítése Agent\AADConnectProvisioningAgent.exe"**

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, és válassza a tulajdonságok, majd a részletek elemet.

![Ügynök fájljának verziója](media/how-to-automatic-upgrade/agent1.png)

Az ügynök-Updater itt van telepítve: **"program FILES\AZURE ad-létesítési ügynök Updater\AzureADConnectAgentUpdater.exe"**

A verzió ellenőrzéséhez kattintson a jobb gombbal a végrehajtható fájlra, és válassza a tulajdonságok, majd a részletek elemet.

![Ügynök frissítési verziója](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Az ügynök eltávolítása
Az ügynök eltávolításához navigáljon **egy program eltávolításához vagy módosításához** , és távolítsa el a következőket:

- Microsoft Azure AD összekapcsolási ügynök frissítése
- Microsoft Azure AD kiépítési ügynök összekötése
- Microsoft Azure AD létesítési ügynök csomagjának összekötése

![Ügynök eltávolítása](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)

