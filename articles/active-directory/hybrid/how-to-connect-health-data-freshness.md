---
title: Az Azure AD Connect Health - az állapotfigyelő szolgáltatás adatai nem dátum riasztás legfeljebb |} A Microsoft Docs
description: Ez a dokumentum ismerteti az "Állapotfigyelő szolgáltatás adatai nem naprakészek" riasztás és hibaelhárítás annak okát.
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
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315097"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek riasztás

## <a name="overview"></a>Áttekintés
<li>Az Azure AD Connect Health adatok friss riasztást állít elő, ha azt nem kapott összes adatponttal a kiszolgáló két órán keresztül. A riasztás címét van **az állapotfigyelő szolgáltatás adatai nem naprakészek**. </li>
<li>A **figyelmeztetés** ügyfélállapot-riasztás akkor aktiválódik, ha Connect Health nem kap kiszolgálóról két órán keresztül küldött adatok csak részlegesen elemek. Figyelmeztetés állapot nem indítja el az e-mail értesítések a bérlői rendszergazdával. </li>
<li>A **hiba** ügyfélállapot-riasztás akkor aktiválódik, ha a Connect Health nem kapja meg a kiszolgáló két órán keresztül küldött adatok elemeket. Hiba állapota riasztás eseményindítók e-mail-értesítéseket a bérlői rendszergazdával. </li>

>[!IMPORTANT] 
> Ez a riasztás a következő Connect Health [adatmegőrzési szabályzat](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 
* Ügyeljen arra, hogy haladnak át, és megfelelnek a [követelmények szakaszt](how-to-connect-health-agent-install.md#requirements).
* Használat [teszt kapcsolódási eszköz](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kapcsolati hibák felderítésére.


## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
