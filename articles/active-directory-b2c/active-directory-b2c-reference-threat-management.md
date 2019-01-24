---
title: Erőforrások és adatok az Azure Active Directory B2C fenyegetések kezelése |} A Microsoft Docs
description: Ismerje meg a-szolgáltatásmegtagadási támadások és a jelszó támadások, az Azure Active Directory B2C észlelési és kockázatenyhítő módszereket.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 66932aa4ea070c5f8ca83524a424e3b73b724c73
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845722"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Erőforrások és adatok az Azure Active Directory B2C fenyegetések kezelése

Az Azure Active Directory (Azure AD) B2C rendelkezik a beépített funkciók, amelyek segítségével az erőforrások és az adatok a fenyegetésekkel szemben. Ezek a kártevők-szolgáltatásmegtagadási támadások és a jelszó támadások közé tartozik. Szolgáltatásmegtagadási támadások előfordulhat, hogy elérhetetlenné erőforrások importálni kívánt felhasználók számára. Jelszó támadások vezethet a jogosulatlan hozzáférés az erőforrásokhoz. 

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

Az Azure AD B2C lelt a szinkronizálás a mi cookie-k használatával SZIN árvíz támadásokkal szemben. Az Azure AD B2C is védi-szolgáltatásmegtagadási támadások ellen, korlátok díjszabás, valamint a kapcsolatok számára.

## <a name="password-attacks"></a>Jelszó támadások

Ésszerűen összetettnek lenniük, hogy a felhasználók által van szükség. Az Azure AD B2C jelszó támadások kockázatcsökkentési módszerek rendelkezik. Megoldás magában foglalja a jelszó találgatásos támadások és a jelszó támadásoknak. Azure AD B2C különböző jelek segítségével elemzi az kérelmek sértetlenségének. Az Azure AD B2C intelligensen különbséget tenni a támadók és a botnetek importálni kívánt felhasználók tervezték. 

Az Azure AD B2C a fiókok zárolásához kifinomultabb stratégiát alkalmaz. A fiókok az IP-címét a kérés és a megadott jelszavak vannak zárolva van a rendszer. A Zárolás időtartama annál is függ, hogy-e a támadás valószínűségét növeli. Egy jelszót a rendszer megpróbálkozik 10 alkalommal sikertelen feldolgozásokat is beleértve, miután egy egy perces fiókzárolási következik be. Bejelentkezés nem sikerül, miután a fiókot nem oldják a zárolást, amikor legközelebb egy másik perces fiókzárolási akkor fordul elő, és továbbra is fennáll, az összes sikertelen bejelentkezést. Írja be ugyanazt a jelszót ismételten több sikertelen bejelentkezések nem számít. 

Az első 10 fiókzárolási hosszú egyperces lesznek. A következő 10 fiókzárolási időszakok kiadva, hosszabb, és növelje a időtartam után minden 10 fiókzárolási időszakokra. A fiókzárolási számláló alaphelyzetbe állítása sikeres bejelentkezés után nulla, ha a fiók nincs zárolva. Fiókzárolás időszakokat is utolsó öt órát. 

Jelenleg nem:

- A kevesebb mint 10 sikertelen bejelentkezés kizárás aktiválása
- Zárolt fiókok listájának lekéréséhez
- A zárolás, a házirend konfigurálása

További információért látogasson el a [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
