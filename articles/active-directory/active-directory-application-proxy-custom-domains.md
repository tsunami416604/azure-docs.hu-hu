---
title: Egyéni tartományok az Azure AD alkalmazásproxy |} Microsoft Docs
description: Az Azure AD alkalmazásproxy egyéni tartományok kezelése, úgy, hogy az alkalmazás URL-CÍMÉT a függetlenül ugyanaz a felhasználók elérhetik azt.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 91cb67bb7a8ebcf0b06561a4c647098c577a473a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Egyéni tartományok az Azure AD alkalmazásproxy használata

Amikor közzétesz egy alkalmazást az Azure Active Directory-proxyn keresztül történő, létre kell hoznia egy külső URL-címet a továbblépéshez, ha azok dolgozik-e távolról a felhasználók számára. Az URL-cím beolvasása az alapértelmezett tartomány *yourtenant.msappproxy.net*. Például, ha közzé egy alkalmazás nevű költségek a bérlő neve Contoso, majd a külső URL-cím lenne https://expenses-contoso.msappproxy.net. Ha saját tartománynevét használja, az alkalmazás az egyéni tartománynév konfigurálása 

Azt javasoljuk, hogy állít be egyéni tartományok az alkalmazások, amikor csak lehetséges. Az egyéni tartományok előnyei a következők:

- A felhasználók férhetnek hozzá a azonos URL-címet, az alkalmazás, hogy működnek-belül vagy kívül a hálózaton.
- Ha az alkalmazásokat a azonos belső és külső URL-címeket, majd egy alkalmazás egy másik mutató hivatkozások továbbra is működni a vállalati hálózaton kívül is. 
- Szabályozhatja a vállalati arculat, és az URL-címeket szeretne létrehozni. 


## <a name="configure-a-custom-domain"></a>Egyéni tartomány konfigurálása

### <a name="prerequisites"></a>Előfeltételek

Az egyéni tartománynév konfigurálása előtt győződjön meg arról, hogy rendelkezik-e előkészíteni a következő követelményeknek: 
- A [ellenőrzött tartományához felvétele az Azure Active Directory](active-directory-domains-add-azure-portal.md).
- Egy egyéni tanúsítványt a tartomány, a PFX-fájl formájában. 
- A helyszíni alkalmazások [proxyn keresztül történő közzétett](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Az egyéni tartomány konfigurálása

Ha készen áll a három követelményekről, kövesse az alábbi lépéseket az egyéni tartomány beállításához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** , és válassza ki a kezelni kívánt alkalmazást.
3. Válassza ki **alkalmazásproxy**. 
4. A külső URL-cím mezőben a legördülő lista segítségével válassza ki az egyéni tartomány. Ha nem látja a tartomány a listában, majd azt még nem nincs ellenőrizve még. 
5. Válassza ki **mentése**
5. A **tanúsítvány** letiltott mező aktívvá válik. Válassza ezt a mezőt. 

   ![Kattintson ide a tanúsítvány feltöltése](./media/active-directory-application-proxy-custom-domains/certificate.png)

   Ha már feltöltött tanúsítványt a tartomány, a tanúsítvány mezőjében a tanúsítvány adatait jeleníti meg. 

6. A PFX-tanúsítvány feltöltése, és írja be a jelszót a tanúsítványhoz. 
7. Válassza ki **mentése** menti a módosításokat. 
8. Adja hozzá a [DNS-rekord](../dns/dns-operations-recordsets-portal.md) , amely átirányítja a felhasználókat az új külső URL-címet a msappproxy.net tartományhoz. 

>[!TIP] 
>Csak akkor kell egyéni tartományonként egy tanúsítvány feltöltése. Tanúsítvány feltöltése után az egyéni tartomány kiválaszthatja, ha egy új alkalmazás közzététele, és nincs további konfigurációs kivételével a DNS-rekord. 

## <a name="manage-certificates"></a>Tanúsítványok kezelése

### <a name="certificate-format"></a>A tanúsítvány formátuma
A tanúsítvány-aláírás módszerek nincs korlátozva van. Elliptikus görbe alapú titkosítást (ECC), a tulajdonos alternatív nevére (SAN) és a közös tanúsítvány egyéb használatát támogatja. 

Helyettesítő tanúsítvány használható, amíg a helyettesítő karakter megegyezik a kívánt külső URL-cím. 

### <a name="changing-the-domain"></a>A tartomány módosítása
Minden ellenőrzött tartományok jelennek meg az alkalmazás külső URL-cím legördülő listából. Ha módosítani szeretné a tartományhoz, frissítse a ezt a mezőt az alkalmazáshoz. Ha a listában nem kíván [ellenőrzött tartományt adja hozzá azt](active-directory-domains-add-azure-portal.md). Ha egy tartományhoz, amely egy társított tanúsítványt még nem, kövesse a lépéseket 5-7 felvenni a tanúsítványt. Ezután ellenőrizze, hogy az új külső URL-címről átirányítja a DNS-rekordot frissítenie. 

### <a name="certificate-management"></a>Tanúsítványkezelés
Egyszerre több alkalmazás használhatja ugyanazt a tanúsítványt, kivéve, ha az alkalmazások osztoznak egy külső gazdagép. 

Megjelenik egy figyelmeztetés, ha a tanúsítvány lejár, felszólító töltse fel másik tanúsítványt a portálon keresztül. Ha a tanúsítvány visszavonása esetén a felhasználók biztonsági figyelmeztetés jelenhet meg az alkalmazáshoz való hozzáféréskor. A tanúsítványok visszavonási ellenőrzést nem végezzük.  Frissítse a tanúsítvány egy adott alkalmazáshoz, keresse meg az alkalmazás, és kövesse az 5 – 7. lépéseket az egyéni tartományok konfigurálása a közzétett alkalmazások egy új tanúsítvány feltöltése. Ha a régi tanúsítvány más alkalmazás nem használatban van, az automatikusan törlődik. 

Minden Tanúsítványkezelő jelenleg egyes lapjain keresztül ezért szüksége lesz a vonatkozó kérelmek kontextusában a tanúsítványok kezeléséhez. 

## <a name="next-steps"></a>További lépések
* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md) a közzétett alkalmazásokhoz az Azure AD-alapú hitelesítés.
* [A feltételes hozzáférés engedélyezése](application-proxy-enable-remote-access-sharepoint.md) a közzétett alkalmazásokhoz.
* [Egyéni tartománynév hozzáadása az Azure AD](active-directory-domains-add-azure-portal.md)


