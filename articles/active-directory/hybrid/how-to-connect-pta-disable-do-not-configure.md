---
title: 'A PTA letiltása a következő használatakor: Azure AD Connect "nem konfigurálható" | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan tilthatja le a PTA Azure AD Connect "ne konfigurálja" funkciót.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5db99f5e8ed2ea5844acba5500bc94d8fb0db2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358344"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>A PTA letiltása a következő használatakor: Azure AD Connect "ne konfigurálja"

Ha áteresztő hitelesítést használ a Azure AD Connecthoz, és úgy állította be, hogy "nincs konfigurálva", akkor letilthatja. Ha a PTA-et letiltja, az alábbi parancsmagokkal végezhető el. 

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek:
- Minden olyan Windows-gép, amelyen telepítve van az PTA ESP-ügynök. 
- Az ügynöknek a (z) 1.5.1742.0 vagy újabb verzióban kell lennie. 
- Azure globális rendszergazdai fiók, amely a következő PowerShell-parancsmagok futtatására van engedélyezve: PTA.

>[!NOTE]
> Ha az ügynök régebbi, akkor előfordulhat, hogy nem rendelkezik a művelet végrehajtásához szükséges parancsmagokkal. Új ügynököt az Azure Portalon telepítheti bármely Windows-gépen, és rendszergazdai hitelesítő adatokat adhat meg. (Az ügynök telepítése nincs hatással a Felhőbeli PTA PTA állapotra)

> [!IMPORTANT]
> Ha a Azure Government-felhőt használja, akkor a következő értékkel kell átadnia a ENVIRONMENTNAME paramétert. 
>
>| Környezet neve | Felhőbeli |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>A PTA letiltása
Egy PowerShell-munkamenetből a következő paranccsal tilthatja le a PTA elemet:
1. PS C:\Program Files\Microsoft Azure AD Connect hitelesítési ügynök> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` vagy `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` vagy `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Ha nem rendelkezik hozzáféréssel az ügynökhöz

Ha nem rendelkezik ügynök-géppel, akkor a következő paranccsal telepítheti az ügynököt.

1. Töltse le a legújabb Auth Agent ügynököt a portal.azure.com webhelyről.
2. Telepítse a szolgáltatást: `.\AADConnectAuthAgentSetup.exe` vagy `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>További lépések

- [Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)
