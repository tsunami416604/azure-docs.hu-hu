---
title: "Bevezetés az Azure MFA üzemeltetésébe a felhőben |Microsoft Docs"
description: "Ez a Microsoft Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA használatát a felhőben."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 1cce449a87571fdabd0dbf76f764f442b2990ffe
ms.contentlocale: hu-hu
ms.lasthandoff: 08/11/2017

---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Azure Multi-Factor Authentication a felhőben – első lépések
A cikk végigkalauzolja az Azure Multi-Factor Authentication a felhőben való használatának kezdeti lépésein.

> [!NOTE]
> A következő dokumentáció arról nyújt tájékoztatást, hogyan engedélyezhet felhasználókat a **klasszikus Azure portállal**. Az Azure Multi-Factor Authentication az O365-felhasználók számára való beállításával kapcsolatos információk: [Többtényezős hitelesítés beállítása az Office 365-höz.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA a felhőben](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Előfeltétel
[Regisztráció Azure-előfizetésre](https://azure.microsoft.com/pricing/free-trial/) – Ha még nincs Azure-előfizetése, regisztrálnia kell rá. Ha csak most kezdi és az Azure MFA-t használja, használhat próbaelőfizetést

## <a name="enable-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése
Ha a felhasználói rendelkeznek az Azure Multi-Factor Authenticationt magában foglaló licenccel, semmit nem kell tennie az Azure MFA bekapcsolásához. Felhasználói alapon, egyenként kezdeményezheti a kétlépéses ellenőrzés igénylését. Az Azure MFA használatát lehetővé tévő licencek:
- Azure Multi-Factor Authentication
- Prémium szintű Azure Active Directory
- Enterprise Mobility + Security

Ha nem rendelkezik ilyen licencekkel, vagy nincs elég licence az összes felhasználó lefedéséhez, az sem gond. Csak egy plusz lépésre van szüksége, hogy [létrehozzon egy többtényezős hitelesítési szolgáltatót](multi-factor-authentication-get-started-auth-provider.md) a könyvtárában.

## <a name="turn-on-two-step-verification-for-users"></a>A kétlépéses ellenőrzés bekapcsolása a felhasználók számára

Az Azure MFA használatának megkezdéséhez használja a [kétlépéses ellenőrzés felhasználók vagy csoportok számára történő megkövetelését](multi-factor-authentication-get-started-user-states.md) ismertető cikkben felsorolt eljárások valamelyikét. Választhat, hogy kétlépéses ellenőrzést követel meg az összes bejelentkezéshez, vagy olyan feltételes hozzáférési szabályzatokat hoz létre, amelyek csak olyan esetekben követelnek meg kétlépéses ellenőrzést, amelyekben annak szükségét érzi.

## <a name="next-steps"></a>Következő lépések
Most, hogy beállította az Azure Multi-Factor Authenticationt a felhőben, konfigurálhatja és beállíthatja az üzemelő példányt. A részletekről lásd: [Az Azure Multi-Factor Authentication konfigurálása](multi-factor-authentication-whats-next.md).


