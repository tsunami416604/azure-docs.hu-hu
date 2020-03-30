---
title: Az Azure AD-adatok csatlakoztatása az Azure Sentinelhez | Microsoft dokumentumok
description: Megtudhatja, hogyan kapcsolhatja össze az Azure Active Directory-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588620"
---
# <a name="connect-data-from-azure-active-directory"></a>Adatok csatlakoztatása az Azure Active Directoryból



Az Azure Sentinel lehetővé teszi, hogy adatokat gyűjtsön az [Azure Active Directoryból,](../active-directory/fundamentals/active-directory-whatis.md) és továbbítsa azokat az Azure Sentinelbe. Választhat, hogy a [bejelentkezési naplókat](../active-directory/reports-monitoring/concept-sign-ins.md) és [a naplókat streameli.](../active-directory/reports-monitoring/concept-audit-logs.md)

## <a name="prerequisites"></a>Előfeltételek

- Ha az Active Directoryból szeretne bejelentkezési adatokat exportálni, rendelkeznie kell egy Azure AD P1 vagy P2 licenccel.

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó azon a bérlőn, amelyről a naplókat streamelni szeretné.

- A kapcsolat állapotának megtekintéséhez engedéllyel kell rendelkeznie az Azure AD diagnosztikai naplók eléréséhez. 


## <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure Active Directory** csempére.

1. Az Azure Sentinelbe streamelni kívánt naplók mellett kattintson a **Csatlakozás**gombra.

1. Kiválaszthatja, hogy szeretné-e, hogy az Azure AD-ből érkező riasztások automatikusan incidenseket hozzanak létre az Azure Sentinelben. Az **Incidensek létrehozása csoportban** válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **Analytics,** majd **az Aktív szabályok**csoportban szerkesztheti.

1. Ha a megfelelő sémát szeretné használni a Log Analytics az Azure AD-riasztások, keresse meg a **SigninLogs** és **AuditLogs.**




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure AD-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
