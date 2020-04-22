---
title: A PTA letiltása az Azure AD Connect "Ne konfiguráljon" használataesetén | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan tiltsa le a PTA az Azure AD Connect "nem konfigurálni" szolgáltatás.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726799"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>A PTA letiltása az Azure AD Connect "Ne konfiguráljon" használatakor

Ha átmenő hitelesítést használ az Azure AD Connect tel, és "Ne konfiguráljon" beállítást, letilthatja azt. A PTA letiltása a következő parancsmagokkal végezhető el. 

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek:
- Minden olyan Windows-gép, amelyen telepítve van a PTA ügynök. 
- Az ügynöknek az 1.5.1742.0-s vagy újabb verzión kell lennie. 
- Egy Azure globális rendszergazdai fiók a PowerShell-parancsmagok futtatásához a PTA letiltásához.

>[!NOTE]
> Ha az ügynök régebbi, akkor lehet, hogy nem rendelkezik a művelet végrehajtásához szükséges parancsmagokkal. Az Azure Portalon beszerezhet egy új ügynököt, és telepítheti azt bármely Windows-gépen, és rendszergazdai hitelesítő adatokat adhat meg. (Az ügynök telepítése nincs hatással a PTA állapotára a felhőben)

> [!IMPORTANT]
> Ha az Azure Government-felhőt használja, akkor az ENVIRONMENTNAME paramétert a következő értékkel kell átadnia. 
>
>| Környezet neve | Felhő |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>A PTA letiltása
A PowerShell-munkameneten belül az alábbi módon tiltsa le a PTA-t:
1. PS C:\Program Files\Microsoft Azure AD Connect hitelesítési ügynök>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` vagy `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` vagy `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Ha nincs hozzáférése egy ügynökhöz

Ha nem rendelkezik ügynökgéppel, a következő paranccsal telepíthet egy ügynököt.

1. Töltse le a legújabb Auth Agent portal.azure.com.
2. Telepítse a `.\AADConnectAuthAgentSetup.exe` funkciót: vagy`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>További lépések

- [Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)