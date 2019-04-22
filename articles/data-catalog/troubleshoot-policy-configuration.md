---
title: Az Azure Active Directory-szabályzat konfigurálása az Azure Data Catalog
description: Előfordulhat olyan helyzet állhat elő, ha tud bejelentkezni az Azure Data Catalog portál, de amikor megpróbál bejelentkezni az adatforrás-regisztráló eszköz, hibaüzenetet tapasztal.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: 558f8845f5469bf157188e20f1ec65a07ff8355f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59363026"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-szabályzat konfigurálása

Előfordulhat, hogy ugyan be tud lépni az Azure Data Catalog portálra, de amikor megpróbál bejelentkezni az adatforrás-regisztrációs eszközbe, hibaüzenet jelenik meg, amely meggátolja a belépést. Ez a hiba akkor fordulhat elő, ha a vállalati hálózaton, vagy a vállalati hálózaton kívülről történő csatlakozáskor.

## <a name="registration-tool"></a>Frissítésregisztráló eszköz

A regisztrációs eszköz *űrlapos hitelesítés* segítségével veti össze a felhasználói bejelentkezéseket az Azure Active Directory adatbázisával. Ahhoz, hogy a bejelentkezés sikeres legyen, az Azure Active Directory rendszergazdájának engedélyeznie kell az űrlapos hitelesítést a *globális hitelesítési szabályzatban*.

Ahogy az az alábbi képen is látható, a globális hitelesítési szabályzat segítségével különböző hitelesítést engedélyezhet az intranetes és az extranetes kapcsolatokhoz. Bejelentkezési hiba akkor fordulhat elő, ha nincs engedélyezve az űrlapos hitelesítés, amelyről csatlakozik a hálózathoz.

 ![Az Azure Active Directory globális hitelesítési szabályzata](./media/troubleshoot-policy-configuration/global-auth-policy.png)

További információkért lásd a [Hitelesítési házirendek konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)) című cikket.

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Azure Data Catalog](data-catalog-get-started.md)