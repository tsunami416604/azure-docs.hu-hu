---
title: Az Azure AD Connect Health - Állapotfigyelő szolgáltatás nincs dátum riasztás legfeljebb |} Microsoft Docs
description: Ez a dokumentum ismerteti az "Állapotfigyelő szolgáltatás adatai nem naprakészek" riasztás és hibaelhárítás érdekében annak okait.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29715011"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Állapotfigyelő szolgáltatás adatokat egy nem naprakész riasztás

## <a name="overview"></a>Áttekintés
<li>Az Azure AD Connect Health riasztást adatok friss riasztás, ha azt nem az adatpontok a kiszolgálóról fogadott két órán keresztül. A riasztási cím **Állapotfigyelő szolgáltatás adatokat nem folyamatosan naprakész adatokat tartalmazzon**. </li>
<li>A **figyelmeztetés** riasztás akkor következik be, ha Connect Health nem kapja meg részleges adatelemek kiszolgáló két órán keresztül küldi. Figyelmeztető riasztás nem indítja el az e-mail értesítéseket, hogy a bérlői rendszergazda segítségét. </li>
<li>A **hiba** riasztás akkor következik be, ha Connect Health nem kapja meg a kiszolgáló két órán keresztül küldött adatok elemeket. Hiba az állapot riasztási eseményindítók e-mail értesítések a bérlői rendszergazdának. </li>

>[!IMPORTANT] 
> Ez a riasztás a következő Connect Health [adatmegőrzési házirend](active-directory-aadconnect-health-gdpr.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 
* Ügyeljen arra, hogy ismerteti, és megfelelnek a [követelményeket ismertető részben](active-directory-aadconnect-health-agent-install.md#requirements).
* Használjon [teszt kapcsolódási eszköz](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kapcsolódási problémák felderítéséhez.


## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health – gyakori kérdések](active-directory-aadconnect-health-faq.md)
