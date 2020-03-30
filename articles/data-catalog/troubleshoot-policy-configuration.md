---
title: Az Azure Data Catalog hibaelhárítása
description: Ez a cikk ismerteti az Azure Data Catalog-erőforrásokkal kapcsolatos gyakori hibaelhárítási aggályokat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879556"
---
# <a name="troubleshooting-azure-data-catalog"></a>Az Azure Data Catalog hibaelhárítása

Ez a cikk ismerteti az Azure Data Catalog-erőforrásokkal kapcsolatos gyakori hibaelhárítási aggályokat. 

## <a name="functionality-limitations"></a>A funkcionalitás korlátai

Az Azure Data Catalog használatakor a következő funkciók korlátozottak:

- **A Vendég szerepkör** típusú fiókok nem támogatottak. Az Azure Data Catalog felhasználóiként nem adhat hozzá vendégfiókokat, és a vendégfelhasználók nem használhatják a portált a alkalmazásban. [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)

- Az Azure Data Catalog-erőforrások létrehozása az Azure Resource Manager-sablonok vagy az Azure PowerShell-parancsok használatával nem támogatott.

- Az Azure Data Catalog erőforrás nem helyezhető át az Azure-bérlők között.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-szabályzat konfigurálása

Előfordulhat, hogy ugyan be tud lépni az Azure Data Catalog portálra, de amikor megpróbál bejelentkezni az adatforrás-regisztrációs eszközbe, hibaüzenet jelenik meg, amely meggátolja a belépést. Ez a hiba akkor fordulhat elő, ha a vállalati hálózaton vagy a vállalati hálózaton kívülről csatlakozik.

A regisztrációs eszköz *űrlapos hitelesítés* segítségével veti össze a felhasználói bejelentkezéseket az Azure Active Directory adatbázisával. Ahhoz, hogy a bejelentkezés sikeres legyen, az Azure Active Directory rendszergazdájának engedélyeznie kell az űrlapos hitelesítést a *globális hitelesítési szabályzatban*.

Ahogy az az alábbi képen is látható, a globális hitelesítési szabályzat segítségével különböző hitelesítést engedélyezhet az intranetes és az extranetes kapcsolatokhoz. Bejelentkezési hibák akkor fordulhatnak elő, ha az űrlaphitelesítés nincs engedélyezve ahhoz a hálózathoz, amelyről csatlakozik.

 ![Az Azure Active Directory globális hitelesítési szabályzata](./media/troubleshoot-policy-configuration/global-auth-policy.png)

További információkért lásd a [Hitelesítési házirendek konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)) című cikket.

## <a name="next-steps"></a>További lépések

* [Azure Data Catalog létrehozása](data-catalog-get-started.md)
