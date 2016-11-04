---
title: Azure AD-bérlő beszerzése | Microsoft Docs
description: Annak ismertetése, hogyan szerezhet be egy Azure Active Directory-bérlőt az alkalmazások regisztrálásához és fordításához.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: terrylan
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/28/2015
ms.author: dastrock

---
# Azure AD-bérlő beszerzése
Az Azure Active Directory (Azure AD) alatt a [bérlők](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) a szervezetek képviselői.  Ez az Azure AD szolgáltatás egy dedikált példánya, amelyet a szervezetek megkapnak és a tulajdonukban áll, amikor regisztrálnak egy Microsoft-felhőszolgáltatásra, például az Azure, a Microsoft Intune vagy az Office 365 szolgáltatásra.  Mindegyik Azure AD-bérlő önálló, és elkülönül a többi Azure AD-bérlőtől.  

A bérlők a vállalatnál tárolják a felhasználóikat és azok információit – a jelszavaikat, a felhasználói profiljuk adatait, az engedélyeiket stb.  Csoportokat, alkalmazásokat és a szervezethez és annak biztonságához kapcsolódó egyéb információkat is tartalmazzák.

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek az alkalmazásába, regisztrálnia kell az alkalmazást a saját bérlőjében.  Az Azure AD-bérlőkben az alkalmazások közzététele **teljesen ingyenes**.  Valójában a legtöbb fejlesztő több bérlőt és alkalmazást is létrehoz kísérleti, fejlesztési, előkészítési és tesztelési célokból.  Az alkalmazásra regisztráló és azt használó szervezetek licenceket vásárolhatnak, ha ki szeretnék használni a speciális címtárfunkciókat.

Szóval, hogyan szerezhet be Azure AD-bérlőt?  Az eljárás kissé eltérő, ha:

* [Meglévő Office 365-előfizetéssel rendelkezik](#use-an-existing-office-365-subscription)
* [Meglévő Azure-előfizetése társítva van egy Microsoft-fiókkal](#use-an-msa-azure-subscription)
* [Meglévő Azure-előfizetése társítva van egy szervezeti fiókkal](#use-an-organizational-azure-subscription)
* [A fentiek közül egyikkel sem rendelkezik, és teljesen új előfizetéssel kezd](#start-from-scratch)

## Meglévő Office 365-előfizetést használ
Ha van meglévő Office 365-előfizetése, akkor már Azure AD-bérlővel is rendelkezik! Az [Azure Portalra](https://portal.azure.com) bejelentkezhet O365-fiókjával, és el is kezdheti használni az Azure AD-t.

## MSA Azure-előfizetést használ
Ha korábban regisztrált egy Azure-előfizetésre egyéni Microsoft-fiókkal, már van bérlője.  Amikor bejelentkezik az [Azure Portalra](https://portal.azure.com), a rendszer automatikusan belépteti az alapértelmezett bérlői fiókba. Szabadon használhatja ezt a bérlőt, ahogyan szeretné – de érdemes lehet egy szervezeti rendszergazdai fiókot létrehozni.

Ehhez kövesse az alábbi lépéseket.  Vagy lehet, hogy új bérlőt szeretne létrehozni, és egy hasonló folyamattal rendszergazdát szeretne létrehozni a bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az egyéni fiókjával
2. Keresse meg a portál „Azure Active Directory” szakaszát (a bal oldali navigációs sávban található a **További szolgáltatások** menüpont alatt)
3. A rendszer automatikusan belépteti az „Alapértelmezett címtárba”, ha nem, akkor válthat a címtárak között a jobb felső sarokban található fióknevére kattintva.
4. A **Gyors feladatok** szakaszban válassza ki a **Felhasználó hozzáadása** lehetőséget.
5. A Felhasználó hozzáadása űrlapon adja meg a következő adatokat:
   
   * Név: (adjon meg egy megfelelő értéket)
   * Felhasználónév: (válasszon egy felhasználónevet ehhez a rendszergazdához)
   * Profil: (töltse ki az utónév, vezetéknév, beosztás és részleg mezőket megfelelő értékekkel)
   * Szerepkör: Globális rendszergazda
6. Amikor kitöltötte a Felhasználó hozzáadása űrlapot, és megkapta az ideiglenes jelszót az új rendszergazdai felhasználóhoz, jegyezze fel ezt a jelszót, mivel ezzel az új felhasználóval kell bejelentkeznie a jelszó módosításához. Közvetlenül el is küldheti a jelszót a felhasználónak egy másodlagos e-mail-címre.
7. Az új felhasználó létrehozásához kattintson a **Létrehozás** parancsra.
8. Az ideiglenes jelszó módosításához jelentkezzen be a [https://login.microsoftonline.com](https://login.microsoftonline.com) címre ezzel az új felhasználói fiókkal, és módosítsa a jelszót, ha a rendszer arra kéri.

## Szervezeti Azure-előfizetést használ
Ha korábban regisztrált egy Azure-előfizetésre a szervezeti fiókjával, már van bérlője.  Az [Azure Portalon](https://portal.azure.com) látnia kell egy bérlőt, ha a „További Szolgáltatások”, majd az „Azure Active Directory” területre navigál.  Szabadon használhatja ezt a bérlőt, ahogyan szeretné. 

## Kezdés a nulláról
Ha nem érti a fentieket, ne aggódjon.  Egyszerűen látogasson el a [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) oldalra, hogy egy új szervezettel regisztráljon az Azure-ba.  Amint befejezte a folyamatot, egy saját Azure AD-bérlővel fog rendelkezni a regisztrációkor választott tartománynévvel.  Az [Azure Portalon](https://portal.azure.com) megkeresheti a bérlőt, ha a bal oldali navigációs sávban az „Azure Active Directory” elemre lép.

Az Azure-ba történő regisztráció folyamatának részeként meg kell adnia a hitelkártyaadatait.  Nyugodtan folytathatja a folyamatot – nem kell fizetnie az alkalmazások Azure AD-ben történő közzétételéért vagy az új bérlők létrehozásáért.

<!--HONumber=Oct16_HO3-->


