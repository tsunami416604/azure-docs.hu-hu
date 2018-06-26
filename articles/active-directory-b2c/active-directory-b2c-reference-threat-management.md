---
title: A fenyegetés-kezelés az Azure Active Directory B2C |} Microsoft Docs
description: További információk a észlelés és javaslat technikák-szolgáltatásmegtagadási támadások és az Azure Active Directory B2C jelszó támadásokat.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0a439a2bb485829b863d6aa666938ab58c90c61f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750777"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Az Azure Active Directory B2C: A fenyegetés kezelése

Fenyegetés felügyeleti a rendszer és a hálózatok elleni támadások elleni védelem megtervezését foglalja magában. Szolgáltatásmegtagadást-tehetik az erőforrások nem érhető el a kívánt felhasználók számára. Jelszó támadások erőforrások jogosulatlan elérésére vezethet. Az Azure Active Directory B2C (Azure AD B2C-vel) rendelkezik beépített szolgáltatásokat tartalmaz, amely segítségével az adatok többféle módon ezek a fenyegetések elleni védelméhez.

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

Az Azure AD B2C által használt észlelés és javaslat módszerek, például SZIN cookie-k vagy sebesség és a kapcsolat korlátai az alapul szolgáló erőforrások-szolgáltatásmegtagadási támadások elleni védelméhez.

## <a name="password-attacks"></a>Jelszó támadások

Az Azure AD B2C is rendelkezik megoldás technikák jelszó támadásokat. Megoldás jelszó találgatásos támadások és a jelszó szótáras tartalmaz. Ésszerűen összetettnek lennie, hogy a felhasználók által beállított van szükség. Az Azure AD B2C különböző jelek segítségével elemzi a kérelmek integritását. Az Azure AD B2C intelligens módon megkülönböztetni azokat a támadók, és a botnetek kívánt felhasználók számára készült. Az Azure AD B2C biztosít a támadások valószínűségét a beírt jelszavak alapján fiókok zárolására kifinomult stratégiát.

További tudnivalókért keresse fel a [Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/default.aspx).
