---
title: Egy- és több-bérlős alkalmazások az Azure AD-ben
titleSuffix: Microsoft identity platform
description: Ismerje meg a funkciók és a különbségek egy-bérlős és több-bérlős alkalmazások az Azure AD-ben.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: ec59383f9ca2b71ec9f4b6df3ab2e24c6b52473b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881365"
---
# <a name="tenancy-in-azure-active-directory"></a>Bérlő az Azure Active Directoryban

Az Azure Active Directory (Azure AD) bérlőnek nevezett csoportokba rendezi az objektumokat, például a felhasználókat és az *alkalmazásokat.* A bérlők lehetővé teszik a rendszergazda számára, hogy házirendeket állítson be a szervezeten belüli felhasználókra és a szervezet tulajdonában lévő alkalmazásokra, hogy megfeleljenek a biztonsági és üzemeltetési szabályzataiknak. 

## <a name="who-can-sign-in-to-your-app"></a>Ki jelentkezhet be az alkalmazásba?

Amikor az alkalmazások fejlesztéséről van szó, a fejlesztők beállíthatják, hogy az alkalmazásuk egy- vagy több-bérlős legyen az [Azure Portalon](https://portal.azure.com)történő alkalmazásregisztráció során.
* Az egybérlős alkalmazások csak abban a bérlőben érhetők el, ahol regisztráltak, más néven az otthoni bérlőben.
* A több-bérlős alkalmazások az otthoni és más bérlők felhasználói számára is elérhetők.

Az Azure Portalon beállíthatja, hogy az alkalmazás egy-bérlős vagy több-bérlős a közönség beállítása az alábbiak szerint.

| Célközönség | Egy/többbérlős | Ki jelentkezhet be? | 
|----------|--------| ---------|
| Csak ebben a címtárban lévő fiókok | Egybérlős alkalmazás | A címtárban lévő összes felhasználói és vendégfiók használhatja az alkalmazást vagy az API-t.<br>*Akkor használja ezt a beállítást, ha a célközönség a szervezeten belül van.* |
| Fiókok bármely Azure AD-címtárban | Több-bérlős | A Microsoft munkahelyi vagy iskolai fiókjával rendelkező összes felhasználó és vendég használhatja az alkalmazást vagy api-t. Ide tartoznak az Office 365-öt használó iskolák és vállalkozások is.<br>*Akkor használja ezt a lehetőséget, ha a célközönség üzleti vagy oktatási ügyfelek.* |
| Fiókok bármely Azure AD-címtárban és személyes Microsoft-fiókban (például Skype, Xbox, Outlook.com) | Több-bérlős | Minden munkahelyi, iskolai vagy személyes Microsoft-fiókkal rendelkező felhasználó használhatja az alkalmazást vagy api-t. Ide tartoznak az Office 365-öt használó iskolák és vállalkozások, valamint az Xbox és a Skype szolgáltatásokba való bejelentkezéshez használt személyes fiókok.<br>*Ezzel a beállítással a Microsoft-fiókok legszélesebb körét célozhatja meg.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Gyakorlati tanácsok a több-bérlős alkalmazásokhoz

Nagyszerű több-bérlős alkalmazások létrehozása kihívást jelenthet a különböző házirendek száma miatt, amelyeket az informatikai rendszergazdák a bérlőkben állíthatnak be. Ha úgy dönt, hogy több-bérlős alkalmazást hoz létre, kövesse az alábbi gyakorlati tanácsokat:

* Tesztelje az alkalmazást egy olyan bérlőben, amely konfigurálta [a feltételes hozzáférési házirendeket.](../azuread-dev/conditional-access-dev-guide.md)
* Kövesse a minimális felhasználói hozzáférés elvét annak érdekében, hogy az alkalmazás csak olyan engedélyeket kér, amelyekre ténylegesen szüksége van. Kerülje a rendszergazdai jóváhagyást igénylő engedélyek kérését, mivel ez megakadályozhatja, hogy a felhasználók bizonyos szervezetekben egyáltalán bevásárolják az alkalmazást. 
* Adja meg a megfelelő neveket és leírásokat az alkalmazás részeként elérhetővé tett engedélyekhez. Ez segít a felhasználóknak és a rendszergazdáknak, hogy tudják, mibe egyeznek bele, amikor megpróbálják használni az alkalmazás API-jait. További információt az engedélyekútmutató ajánlott eljárásokra vonatkozó szakaszában [talál.](v2-permissions-and-consent.md)

## <a name="next-steps"></a>További lépések

* [Hogyan lehet átalakítani egy alkalmazást több-bérlőssé](howto-convert-app-to-be-multi-tenant.md)
