---
title: Azure AD-bérlő beszerzése | Microsoft Docs
description: Annak ismertetése, hogyan szerezhet be egy Azure Active Directory-bérlőt az alkalmazások regisztrálásához és fordításához.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 7ed0103b8ad8f23cd45a3a9ee94e01ce69e1e45b
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287264"
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure AD-bérlő beszerzése

Az Azure Active Directory (Azure AD) alatt a [bérlők](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) a szervezetek képviselői. Ez az Azure AD szolgáltatás egy dedikált példánya, amelyet a vállalatok megkapnak és a tulajdonukban áll, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak egy Microsoft-felhőszolgáltatásra, például az Azure, a Microsoft Intune vagy az Office 365 szolgáltatásra. Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől. 

A bérlők a vállalatnál tárolják a felhasználóikat és azok információit – a jelszavaikat, a felhasználói profiljuk adatait, az engedélyeiket stb. Csoportokat, alkalmazásokat és a szervezethez és annak biztonságához kapcsolódó egyéb információkat is tartalmazzák.

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek az alkalmazásába, regisztrálnia kell az alkalmazást a saját bérlőjében. Az Azure AD-bérlők létrehozása és az alkalmazások közzététele azokon **teljesen ingyenes** (bár választhat, hogy fizet-e a prémium szintű szolgáltatásokért a bérlőben). Sok fejlesztő több bérlőt és alkalmazást is létrehoz kísérleti, fejlesztési, előkészítési és tesztelési célokból.

## <a name="use-an-existing-azure-ad-tenant"></a>Meglévő Azure AD-bérlő használata

Sok fejlesztő az Azure AD-bérlőkhöz kapcsolódó szolgáltatások vagy előfizetések (például Office 365 vagy Azure-előfizetések) révén már rendelkezik bérlőkkel. Ha ellenőrizni szeretné, hogy már rendelkezik-e bérlővel, jelentkezzen be az [Azure Portalra](https://portal.azure.com) az alkalmazásfelügyelethez használni kívánt fiókkal, és tekintse meg a jobb felső sarokban látható fiókinformációkat. Ha van bérlője, automatikusan bejelentkezik rá, és közvetlenül a fióknév alatt láthatja a bérlő nevét. Ha a fiókja neve fölé helyezi a kurzort az Azure Portal jobb felső részén, megjelenik a neve, e-mail-címe, címtára és bérlőazonosítója (egy GUID), valamint tartománya. Ha a fiók több bérlővel van társítva, a fiók nevének kiválasztásával megnyithat egy menüt, ahol válthat a bérlők között. Minden bérlő saját bérlőazonosítóval rendelkezik.

> [!TIP]
> Ha szüksége van a bérlőazonosítóra, ez több módon is megtalálható. A bérlőazonosító lekérhető, ha a fiókneve fölé helyezi a mutatót, vagy ha az **Azure Active Directory > Tulajdonságok > Címtár-azonosító** lehetőséget választja az Azure Portalon.

Ha nincs társítva meglévő bérlő a fiókkal, egy GUID-t lát a fiók neve alatt, és nem tud műveleteket végezni, például alkalmazásokat regisztrálni, amíg [létre nem hoz egy új bérlőt](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Ha még nem rendelkezik Azure AD-bérlővel, vagy ha újat szeretne létrehozni, ezt az [Azure Portalon](https://portal.azure.com) a [címtár létrehozása élménnyel](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) teheti meg. A folyamat körülbelül egy percet vesz igénybe, és a végén a rendszer megkéri, hogy navigáljon az újonnan létrehozott bérlőhöz.
