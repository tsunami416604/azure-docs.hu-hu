---
title: Erőforrások és adatok az Azure Active Directory B2C fenyegetések kezelése
description: Ismerje meg a-szolgáltatásmegtagadási támadások és a jelszó támadások, az Azure Active Directory B2C észlelési és kockázatenyhítő módszereket.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798229"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Erőforrások és adatok az Azure Active Directory B2C fenyegetések kezelése

Az Azure Active Directory (Azure AD) B2C rendelkezik a beépített funkciók, amelyek segítségével az erőforrások és az adatok a fenyegetésekkel szemben. Ezek a kártevők-szolgáltatásmegtagadási támadások és a jelszó támadások közé tartozik. Szolgáltatásmegtagadási támadások előfordulhat, hogy elérhetetlenné erőforrások importálni kívánt felhasználók számára. Jelszó támadások vezethet a jogosulatlan hozzáférés az erőforrásokhoz.

## <a name="denial-of-service-attacks"></a>Szolgáltatásmegtagadási támadások

Az Azure AD B2C lelt a szinkronizálás a mi cookie-k használatával SZIN árvíz támadásokkal szemben. Az Azure AD B2C is védi-szolgáltatásmegtagadási támadások ellen, korlátok díjszabás, valamint a kapcsolatok számára.

## <a name="password-attacks"></a>Jelszó támadások

Ésszerűen összetettnek lenniük, hogy a felhasználók által van szükség. Az Azure AD B2C jelszó támadások kockázatcsökkentési módszerek rendelkezik. Megoldás magában foglalja a jelszó találgatásos támadások és a jelszó támadásoknak. Azure AD B2C különböző jelek segítségével elemzi az kérelmek sértetlenségének. Az Azure AD B2C intelligensen különbséget tenni a támadók és a botnetek importálni kívánt felhasználók tervezték.

Az Azure AD B2C a fiókok zárolásához kifinomultabb stratégiát alkalmaz. A fiókok az IP-címét a kérés és a megadott jelszavak vannak zárolva van a rendszer. A Zárolás időtartama annál is függ, hogy-e a támadás valószínűségét növeli. Miután jelszó sikertelenül próbálta a 10 alkalommal (az alapértelmezett kísérlet küszöbérték), egy egy perces fiókzárolási történik. A következő alkalommal a bejelentkezés nem sikerül, a számla után nem oldják a zárolást, (miután a fiók rendelkezik lett automatikusan feloldani a szolgáltatás a zárolás lejárta után), egy másik perces fiókzárolási történik, és továbbra is fennáll, az összes sikertelen bejelentkezést. Írja be ugyanazt a jelszót ismételten több sikertelen bejelentkezések nem számít.

Az első 10 fiókzárolási hosszú egyperces lesznek. A következő 10 fiókzárolási időszakok kiadva, hosszabb, és növelje a időtartam után minden 10 fiókzárolási időszakokra. A fiókzárolási számláló alaphelyzetbe állítása sikeres bejelentkezés után nulla, ha a fiók nincs zárolva. Fiókzárolás időszakokat is utolsó öt órát.

## <a name="manage-password-protection-settings"></a>Jelszó védelmi beállításainak kezelése

Jelszó védelmi beállításokat, beleértve a Fiókzárolás küszöbe kezelése:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár és előfizetés** szűrés a portál jobb felső menüjében, majd válassza ki az Azure AD B2C-bérlőben.
1. Válassza ki **Azure Active Directory** a bal oldali menüben (vagy válassza ki **minden szolgáltatás** a portál bal felső szakaszában, majd keresse meg és válassza *Azure Active Directory*).
1. Alatt **biztonsági**válassza **hitelesítési módszerek**, majd **jelszavas védelem**.
1. Adja meg a kívánt jelszót védelmi beállításokat, majd válassza ki **mentése**.

    ![Azure portal jelszó védelmi lapján az Azure AD-beállításokat](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*A fiókzárolási küszöbértéket 5 beállítást **jelszavas védelem** beállítások*.

## <a name="view-locked-out-accounts"></a>Zárolt fiókok nézet

Zárolt fiókok adatait beszerzéséhez ellenőrizheti az Active Directory [bejelentkezési tevékenységek jelentésének](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). A **állapot**válassza **hiba**. Sikertelen bejelentkezési kísérlet az egy **bejelentkezési hiba kódja** , `50053` jelzi a zárolt fiók:

![A rész az Azure AD bejelentkezési fiókok megjelenítése](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

A bejelentkezési tevékenységek jelentésének megtekintése az Azure Active Directory kapcsolatos további információkért lásd: [bejelentkezési tevékenységre vonatkozó jelentések hibakódjai](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
