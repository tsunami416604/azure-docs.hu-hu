---
title: Az Azure Data Catalog hibaelhárítása
description: Ez a cikk ismerteti a gyakori hibaelhárítási problémák az Azure Data Catalog-erőforrásokhoz.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203506"
---
# <a name="troubleshooting-azure-data-catalog"></a>Az Azure Data Catalog hibaelhárítása

Ez a cikk ismerteti a gyakori hibaelhárítási problémák az Azure Data Catalog-erőforrásokhoz. 

## <a name="functionality-limitations"></a>Funkciók korlátozások

Az Azure Data Catalog használatakor az alábbi funkciókat korlátozva:

- Típusú fiókok **Vendég szerepkör** nem támogatottak. Az Azure Data Catalog felhasználói nem adható hozzá, a Vendég fiók, és www.azuredatacatalog.com, a vendég felhasználók nem használhatják a portálon.

- Az Azure Resource Manager-sablonok vagy az Azure PowerShell-parancsok az Azure Data Catalog-erőforrások létrehozása nem támogatott.

- Az Azure Data Catalog-erőforrást nem lehet áthelyezni az Azure-bérlők között.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-szabályzat konfigurálása

Előfordulhat, hogy ugyan be tud lépni az Azure Data Catalog portálra, de amikor megpróbál bejelentkezni az adatforrás-regisztrációs eszközbe, hibaüzenet jelenik meg, amely meggátolja a belépést. Ez a hiba akkor fordulhat elő, ha a vállalati hálózaton, vagy a vállalati hálózaton kívülről történő csatlakozáskor.

A regisztrációs eszköz *űrlapos hitelesítés* segítségével veti össze a felhasználói bejelentkezéseket az Azure Active Directory adatbázisával. Ahhoz, hogy a bejelentkezés sikeres legyen, az Azure Active Directory rendszergazdájának engedélyeznie kell az űrlapos hitelesítést a *globális hitelesítési szabályzatban*.

Ahogy az az alábbi képen is látható, a globális hitelesítési szabályzat segítségével különböző hitelesítést engedélyezhet az intranetes és az extranetes kapcsolatokhoz. Bejelentkezési hiba akkor fordulhat elő, ha nincs engedélyezve az űrlapos hitelesítés, amelyről csatlakozik a hálózathoz.

 ![Az Azure Active Directory globális hitelesítési szabályzata](./media/troubleshoot-policy-configuration/global-auth-policy.png)

További információkért lásd a [Hitelesítési házirendek konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)) című cikket.

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy Azure Data Catalog](data-catalog-get-started.md)
