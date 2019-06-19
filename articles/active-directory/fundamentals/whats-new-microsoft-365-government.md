---
title: Újdonságok a Microsoft 365 Government – Azure Active Directory az Azure Active Directory |} A Microsoft Docs
description: Ismerje meg néhány módosítást az Azure Active Directory (Azure AD) a felhő hatással lehet, hogy a Microsoft 365 Government példányban.
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
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258894"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>A Microsoft 365 Government az Azure Active Directory újdonságai

Végeztünk néhány módosítást az Azure Active Directory (Azure AD) alkalmazható a következő Servicest használó ügyfeleink a Microsoft 365 Government cloud példányban:

- Microsoft Azure Government

- A Microsoft 365 Government – GCC magas

- Microsoft 365 Government – DoD

Ez a cikk a Microsoft 365 Government – GCC ügyfelek nem vonatkozik.

## <a name="changes-to-the-initial-domain-name"></a>A kezdeti tartomány neve

Során a szervezet a kezdeti regisztráció a Microsoft 365 Government online szolgáltatás, felkérték a tartománynevet a szervezet, `<your-domain-name>.onmicrosoft.com`. Ha már rendelkezik egy tartomány nevét az .com utótaggal, semmi nem változik.

Azonban szolgáltatást, a Microsoft 365 Government szolgáltatásba, ha a rendszer egy tartomány nevét az kiválasztása a `.us` utótag. Így lesz `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Ez a változás nem vonatkozik a felhőszolgáltatók (CSP) által felügyelt összes ügyfél.

## <a name="changes-to-portal-access"></a>Portálhozzáférési módosításai

Frissítettük a portál végpontok a Microsoft Azure Government, Microsoft 365 Government – GCC magas, és a Microsoft 365 Government – védelmi Minisztérium, ahogyan az a [leképezés táblázatban](#endpoint-mapping).

Korábban ügyfelek sikerült jelentkezzen be a globális Azure (portal.azure.com) és az Office 365-höz (portal.office.com) Portalon. Ez a frissítés ügyfelek most jelentkezzen be az adott Microsoft Azure Government, a Microsoft 365 Government – GCC magas, és a Microsoft 365 Government – védelmi Minisztérium portálok használata.

## <a name="endpoint-mapping"></a>Végpont-leképezés

Az alábbi táblázat a végpontok minden ügyfelünk esetében:

| Name (Név) | végpont részletei |
|------|------------------|
| Portálok |A Microsoft Azure Government: https://portal.azure.us<p>A Microsoft 365 Government – GCC magas: https://portal.office365.us<p>A Microsoft 365 Government – védelmi Minisztérium: https://portal.apps.mil |
| Az Azure Active Directory-szolgáltató végpont | https://login.microsoftonline.us |
| Azure Active Directory – Graph API | https://graph.windows.net |
| A Microsoft Graph API-t a Microsoft 365 Government – GCC magas | https://graph.microsoft.us |
| A Microsoft Graph API-t a Microsoft 365 Government – védelmi Minisztérium | https://dod-graph.microsoft.us |
| Az Azure Government szolgáltatások végpontok | További információkért lásd: [Azure Government fejlesztői útmutató](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| A Microsoft 365 Government - GCC magas végpontok | További információkért lásd: [Office 365 Egyesült Államok Kormányzati GCC magas végpontok](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| A Microsoft 365 Government – védelmi Minisztérium | További információkért lásd: [Office 365 Egyesült Államok Kormányzati védelmi Minisztériuma – végpontok](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>További lépések

További információval a következő cikkek szolgálnak:

- [Mi az az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Az Azure Government AAD-szolgáltató végpont frissítése](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [A Microsoft Graph-végpontok az Egyesült Államok kormányának felhője](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Az Office 365 Egyesült Államokbeli kormányzati GCC magas és a védelmi Minisztérium](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)