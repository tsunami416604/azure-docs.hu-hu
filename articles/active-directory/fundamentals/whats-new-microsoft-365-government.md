---
title: Az Azure AD újdonságai a Microsoft 365 Government szolgáltatásban | Microsoft Docs
description: Ismerje meg az Azure Active Directory (Azure AD) néhány módosítását a Microsoft 365 Government felhőpéldányában, amely hatással lehet Önre.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425544"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Az Azure Active Directory újdonságai a Microsoft 365 Government szolgáltatásban

Néhány módosítást eszközöltünk az Azure Active Directoryban (Azure AD) a Microsoft 365 Government felhőpéldányban, amely a következő szolgáltatásokat használó ügyfelekre vonatkozik:

- Microsoft Azure Government

- Microsoft 365 kormány – GCC High

- Microsoft 365 kormány – DoD

Ez a cikk nem vonatkozik a Microsoft 365 Government – GCC-ügyfelekre.

## <a name="changes-to-the-initial-domain-name"></a>A kezdeti tartománynév módosítása

A szervezet microsoft 365-ös kormányzati online szolgáltatásra való első regisztrációja során a rendszer `<your-domain-name>.onmicrosoft.com`arra kérte, hogy válassza ki a szervezet tartománynevét. Ha már van tartományneve a .com utótaggal, semmi sem fog változni.

Ha azonban új Microsoft 365 Kormányzati szolgáltatásra regisztrál, a rendszer megkéri, hogy `.us` az utótag használatával válasszon tartománynevet. Szóval, ez `<your-domain-name>.onmicrosoft.us`lesz .

>[!Note]
>Ez a módosítás nem vonatkozik a felhőszolgáltatók (CsP-k) által kezelt ügyfelekre.

## <a name="changes-to-portal-access"></a>A portálhozzáférés módosításai

Frissítettük a microsoft Azure Government, a Microsoft 365 Government – GCC High és a Microsoft 365 Government – DoD portálvégpontjait, ahogy az az [Endpoint mapping táblában](#endpoint-mapping)látható.

Korábban az ügyfelek bejelentkezhett a világméretű Azure (portal.azure.com) és Office 365 (portal.office.com) portálokon. Ezzel a frissítéssel az ügyfeleknek most be kell jelentkezniük az adott Microsoft Azure Government, Microsoft 365 Government – GCC High és Microsoft 365 Government - DoD portálok használatával.

## <a name="endpoint-mapping"></a>Végpontleképezés

Az alábbi táblázat az összes vevő végpontjait mutatja be:

| Név | Végpont részletei |
|------|------------------|
| Portálok |Microsoft Azure Government:https://portal.azure.us<p>Microsoft 365 kormány – GCC High:https://portal.office365.us<p>Microsoft 365-kormány – DoD:https://portal.apps.mil |
| Az Azure Active Directory-szolgáltató végpontja | https://login.microsoftonline.us |
| Microsoft Graph API microsoft 365 kormányhoz – GCC High | https://graph.microsoft.us |
| Microsoft Graph API for Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Az Azure Government szolgáltatási végpontjai | További részletek az [Azure Government fejlesztői útmutatójában](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government – GCC Fővégpontok | További részletek az [Office 365 Egyesült Államok Kormányzati GCC –magas szintű végpontjaiban](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 kormány - DoD | További információt az [Office 365 Egyesült Államok kormányzati dod-végpontjaiban talál.](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>További lépések

További információval a következő cikkek szolgálnak:

- [Mi az az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Az Azure Government AAD-hatóság végpontjának frissítése](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph végpontok az Egyesült Államok kormányzati felhőjében](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 Amerikai Egyesült Államok Kormányzati GCC Magas és DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
