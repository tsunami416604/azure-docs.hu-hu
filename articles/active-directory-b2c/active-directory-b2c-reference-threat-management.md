---
title: A fenyegetés-kezelés az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg a-szolgáltatásmegtagadási támadások és a jelszó támadások, az Azure Active Directory B2C észlelési és kockázatenyhítő módszereket.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1801fe9695aa15850d600300b957df2c7d7cd9ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054712"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Az Azure Active Directory B2C: Fenyegetések kezelése

Fenyegetések kezelése a rendszer és a hálózat elleni támadások elleni védelem megtervezését foglalja magában. Szolgáltatásmegtagadási támadások előfordulhat, hogy elérhetetlenné erőforrások importálni kívánt felhasználók számára. Jelszó támadások vezethet a jogosulatlan hozzáférés az erőforrásokhoz. Az Azure Active Directory B2C (Azure AD B2C-vel) rendelkezik a beépített funkciók, amelyek segítségével az adatok többféle módon ezek a kártevők elleni védelme.

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

Az Azure AD B2C-t használ észlelési és kockázatenyhítő módszereket, mint SZIN cookie-k és arányát és a kapcsolat korlátai mögöttes erőforrások-szolgáltatásmegtagadási támadások elleni védelme érdekében.

## <a name="password-attacks"></a>Jelszó támadások

Az Azure AD B2C-vel is rendelkezik kockázatcsökkentési módszerek jelszó támadásokkal szemben. Megoldás magában foglalja a jelszó találgatásos támadások és a jelszó támadásoknak. Ésszerűen összetettnek lenniük, hogy a felhasználók által van szükség. Azure AD B2C különböző jelek segítségével elemzi az kérelmek sértetlenségének. Az Azure AD B2C intelligensen különbséget tenni a támadók és a botnetek importálni kívánt felhasználók tervezték. Az Azure AD B2C biztosít a támadás valószínűségét megadott jelszavak alapján a fiókok zárolásához kifinomult stratégia.

További információért látogasson el a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
