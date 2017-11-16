---
title: "Az Azure Data Catalog előfeltételei |} Microsoft Docs"
description: "További tudnivalók az Azure Data Catalog használatába kell előfeltételek."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: 7d4ed0cc06e8b218b2b63c1d899701ba2eafd648
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-data-catalog-prerequisites"></a>Az Azure Data Catalog előfeltételei

Néhány dolgot gondoskodunk Azure Data Catalog beállítása előtt kell. Ne aggódjon, a folyamat nem tart sokáig.

## <a name="azure-subscription"></a>Azure-előfizetés
A Data Catalog beállítása, a tulajdonos vagy a társtulajdonos az Azure-előfizetéssel kell lennie.

Azure-előfizetések megkönnyítik felhőszolgáltatás erőforrások, például a Data Catalog elérésére. Előfizetések is segíthetnek, erőforrás-használat jelentett, számlázva és kifizette vezérlésére. Minden előfizetés van külön számlázási és a fizetési telepítő, így előfizetések és a csomagok, amelyek módosítják a részleg, a projekt, a regionális office és az stb. Egy előfizetés tartozik minden felhőalapú szolgáltatás, és kell rendelkeznie egy előfizetéshez, mielőtt a Data Catalog beállítása. További információkat a [fiókok, előfizetések és rendszergazdai szerepkörök kezeléséről](../active-directory/active-directory-assign-admin-roles-azure-portal.md) szóló cikkben talál.

## <a name="azure-active-directory"></a>Azure Active Directory
A Data Catalog beállításához be kell jelentkeznie be egy Azure Active Directory (Azure AD) felhasználói fiókkal.

Az Azure AD egyszerű módot kínál vállalkozásának az identitás és a hozzáférés kezelésére, mind a felhőben, mind a helyszínen. Felhasználók is használhat egy egyetlen munkahelyi vagy iskolai fiókkal egyszeri bejelentkezés az összes felhő, és a helyszíni webalkalmazást. A Data Catalog használ az Azure AD-bejelentkezés hitelesítéséhez. További tudnivalókért lásd: [Mi az Azure Active Directory?](../active-directory/active-directory-whatis.md).

> [!NOTE]
> Használatával a [Azure-portálon](http://portal.azure.com/), jelentkezhet be személyes Microsoft-fiókkal vagy egy Azure Active Directory munkahelyi vagy iskolai fiók. Állíthatja be a Data Catalog használatával vagy az Azure-portálon vagy a [Data Catalog-portál](http://www.azuredatacatalog.com), Azure Active Directory-fiókkal, nem egy személyes fiókkal kell bejelentkeznie.
>
>

## <a name="active-directory-policy-configuration"></a>Az Active Directory-házirend konfigurálása
Akkor léphetnek olyan helyzet, ahol bejelentkezhet a Data Catalog-portált, de ha megkísérli az jelentkezzen be az adatforrás-regisztráló eszköz, olyan hibaüzenetet, amely megakadályozza, hogy aláírási észlel. Ez a probléma a probléma akkor fordulhat elő, csak akkor, ha a vállalati hálózaton, vagy Ez akkor fordulhat elő, csak akkor, ha, amelyből csatlakozik a vállalati hálózaton kívülről.

Az adatforrás-regisztráló eszköz Active Directoryban a felhasználói fiók hitelesítő adatainak érvényesítéséhez használja a az űrlapos hitelesítés. Segítséget a bejelentkezés sikeres, az Active Directory-rendszergazda engedélyeznie kell az űrlapos hitelesítést a globális hitelesítési házirend.

A globális hitelesítési házirend a hitelesítési módszerek engedélyezhető külön-külön az intranetes és extranetes kapcsolatok, az alábbi képernyőfelvételen látható módon. Bejelentkezési hiba is felléphet, ha a hálózati kapcsolat, amelyből nincs engedélyezve az űrlapos hitelesítés.

 ![Az Active Directory globális hitelesítési házirend](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Következő lépések
További információkért lásd: [hitelesítési házirendek konfigurálása](https://technet.microsoft.com/library/dn486781.aspx).
