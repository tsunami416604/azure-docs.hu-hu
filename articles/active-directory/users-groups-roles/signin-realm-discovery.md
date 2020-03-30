---
title: Felhasználónév-alapú névkeresés bejelentkezés közben – Azure Active Directory | Microsoft dokumentumok
description: Hogyan tükrözi a képernyőn megjelenő üzenetküldés a felhasználónév-alapú képet az Azure Active Directoryba való bejelentkezés során?
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024252"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Az Azure Active Directory bejelentkezési lapjainak kezdőlapos felderítése

Módosítjuk az Azure Active Directory (Azure AD) bejelentkezési viselkedését, hogy helyet adjon az új hitelesítési módszereknek, és javítsa a használhatóságot. A bejelentkezés során az Azure AD határozza meg, ahol a felhasználónak szüksége van a hitelesítésre. Az Azure AD intelligens döntéseket hoz a bejelentkezési lapon megadott felhasználónév szervezet- és felhasználói beállításainak olvasásával. Ez egy lépés a jelszónélküli jövő felé, amely további hitelesítő adatokat tesz lehetővé, például a FIDO 2.0.This a step to a password-free future that enables additional credentials like FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Otthoni birodalom felderítési viselkedése

Történelmileg az otthoni tartomány felderítését a bejelentkezéskor megadott tartomány vagy néhány örökölt alkalmazás Home Realm Discovery-házirendje szabályozta. Felderítési viselkedésünkben például egy Azure Active Directory-felhasználó elgépelheti a felhasználónevét, de továbbra is megérkezik a szervezet hitelesítő adatok gyűjteményének képernyőjére. Ez akkor fordul elő, ha a felhasználó helyesen adja meg a szervezet "contoso.com" tartománynevét. Ez a viselkedés nem teszi lehetővé, hogy a részletesség az egyes felhasználók felhasználói élményének testreszabásához.

A hitelesítő adatok szélesebb körének támogatása és a használhatóság növelése érdekében az Azure Active Directory felhasználónév-keresési viselkedése a bejelentkezési folyamat során frissül. Az új viselkedés intelligens döntéseket hoz a bérlői és felhasználói szintű beállítások olvasásával a bejelentkezési lapon megadott felhasználónév alapján. Ennek lehetővé teszi, hogy az Azure Active Directory ellenőrzi, hogy a bejelentkezési lapon megadott felhasználónév létezik-e a megadott tartományban, vagy átirányítja a felhasználót a hitelesítő adataimegadására.

Ennek a munkának további előnye a továbbfejlesztett hibaüzenet.A további előnye ennek a munkának a továbbfejlesztett hibaüzenet.A additional benefit of this work is improved error messaging. Íme néhány példa a továbbfejlesztett hibaüzenet-küldés, amikor bejelentkezik egy alkalmazás, amely csak az Azure Active Directory-felhasználók számára.

- A felhasználónév elgépelt, vagy a felhasználónév még nincs szinkronizálva az Azure AD-vel:
  
    ![a felhasználónév elgépelt vagy nem található](./media/signin-realm-discovery/typo-username.png)
  
- A tartománynév elgépelt:
  
    ![a tartománynév elgépelt vagy nem található](./media/signin-realm-discovery/typo-domain.png)
  
- A felhasználó egy ismert fogyasztói tartománnyal próbál bejelentkezni:
  
    ![bejelentkezés ismert fogyasztói tartománnyal](./media/signin-realm-discovery/consumer-domain.png)
  
- A jelszó elgépelt, de a felhasználónév pontos:  
  
    ![a jelszó elgépelt jó felhasználónévvel](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ez a szolgáltatás hatással lehet a régi tartományszintű home realm discovery-re támaszkodó összevont tartományokra az összevonás kényszerítéséhez. Az összevont tartományi támogatás hozzáadásának időpontjáról a [Microsoft 365-szolgáltatások bejelentkezésekor](https://azure.microsoft.com/updates/signin-hrd/)található Kezdőlap tartomány felderítése című témakörben található. Eközben egyes szervezetek betanították az alkalmazottakat, hogy olyan felhasználónévvel jelentkezzenek be, amely nem létezik az Azure Active Directoryban, de tartalmazza a megfelelő tartománynevet, mivel a tartománynevek a felhasználókat jelenleg a szervezet tartományvégpontjára irányítja. Az új bejelentkezési viselkedés ezt nem teszi lehetővé. A felhasználó értesítést kap a felhasználónév javítására, és nem jelentkezhet be olyan felhasználónévvel, amely nem létezik az Azure Active Directoryban.
>
> Ha Ön vagy a szervezet a régi viselkedéstől függő gyakorlatokkal rendelkezik, fontos, hogy a szervezeti rendszergazdák frissítsék az alkalmazotti bejelentkezési és hitelesítési dokumentációt, és betanítsák az alkalmazottakat az Azure Active Directory-felhasználónevük bejelentkezéséhez.
  
Ha aggályai vannak az új viselkedéssel kapcsolatban, hagyja meg megjegyzéseit a cikk **Visszajelzés** szakaszában.  

## <a name="next-steps"></a>További lépések

[A bejelentkezési márkajelzés testreszabása](../fundamentals/add-custom-domain.md)
