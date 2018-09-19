---
title: Az egyéni tartomány hozzáadása az Azure Active Directoryhoz |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egy egyéni tartományt az Azure Active Directory portál használatával.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: lizross
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: e49e362528f5fcd00a13a9fc1b233e62a569fe5a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297946"
---
# <a name="how-to-add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Útmutató: az Azure Active Directory portál használatával egyéni tartománynév hozzáadása
Minden új Azure AD-bérlőt egy kezdeti tartománynevet tartalmaz *domainname*. onmicrosoft.com. Nem módosítható, és a kezdeti tartománynév törlése, de a szervezet neveket adhat hozzá a listában. Egyéni tartománynevek hozzáadása segítségével hozhat létre, amely számára is felismerhetők a felhasználók számára, például felhasználóneveket _alain@contoso.com_.

>[!Note]
>Meg kell ismételnie a teljes folyamatot, elejétől a végéig, az egyes egyéni tartománynév.

## <a name="add-a-custom-domain-name"></a>Egyéni tartománynév hozzáadása
Először hozzá kell adnia az egyéni tartománynév Azure AD-bérlőhöz.

### <a name="to-add-a-custom-domain-name"></a>Egyéni tartománynév hozzáadása
1. Jelentkezzen be a [Azure AD portálon](https://portal.azure.com/) a címtár egy globális rendszergazdai fiók használatával.

<<<<<<< HEAD
> [!TIP]
> Ha helyszíni Windows Server AD-jét szeretné összevonni az Azure AD-vel, az Azure AD Connect eszköz futtatásakor jelölje be a **Szeretném konfigurálni ezt a tartományt egyszeri bejelentkezéshez a helyi Active Directoryhoz** jelölőnégyzetet a címtárak szinkronizálásához. Emellett a helyszíni címtárral való összevonáshoz kiválasztott tartománynevet is regisztrálnia kell az **Azure AD-tartomány** lépésnél a varázslóban. Megtekintheti, hogyan néz ki ez a lépés a varázslóban [ezekben az útmutatásokban](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Ha még nem rendelkezik Azure AD Connect eszközzel, [innen letöltheti](http://go.microsoft.com/fwlink/?LinkId=615771).
=======
2. Válassza ki **Azure Active Directory**válassza **egyéni tartománynevek**, majd válassza ki **egyéni tartomány hozzáadása**.
>>>>>>> 73f447b1499ba1f189efb6832ad759f1230e55a2

    ![Fabrikam - Custom domain names blade, with Add custom domain option highlighted](media/add-custom-domain/add-custom-domain.png)

3. Írja be az új vállalati tartománynevet a **egyéni tartománynév** mezőbe (például _contoso.com_), majd válassza ki **tartomány hozzáadása**.

    >[!Important]
    >Szerepelnie kell .com, .net vagy egyéb legfelső szintű kiterjesztés ennek megfelelően működjön.

    ![A Fabrikam – egyéni tartomány nevét panelen Hozzáadás tartomány gomb kiemelésével](media/add-custom-domain/add-custom-domain-blade.png)

4. Másolja a DNS-bejegyzés adatait a a **Contoso** panelen.

    ![A DNS-bejegyzés adatok contoso panelről](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-domain-name-with-a-domain-name-registrar"></a>A tartománynév regisztrálójánál az tartománynév hozzáadása
Ezután a DNS-zónafájlban kell frissítenie az új egyéni tartományhoz. Használhat [DNS-zónák](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) az Azure-Office 365- vagy külső DNS-rekordok, vagy felveheti az új DNS-bejegyzés használatával egy másik DNS-regisztráló webhelyén (például [InterNIC](https://go.microsoft.com/fwlink/p/?LinkId=402770)).

### <a name="to-add-your-domain-name"></a>A tartománynév hozzáadása 
1. Jelentkezzen be a tartomány tartománynév, az egyéni tartományhoz. Nem rendelkezik a megfelelő engedélyekkel a bejegyzés frissítéséhez, ha valaki, ilyen engedélyekkel rendelkező szüksége.

2. A DNS-bejegyzést a regisztrálójánál frissítése után frissítenie kell a DNS-zónafájlban Azure AD által biztosított információkkal.

    >[!Note]
    >A DNS-bejegyzést a levélkezelési útválasztást vagy a webes üzemeltetés működése nem változik.

## <a name="verify-your-custom-domain-name"></a>Az egyéni tartománynév ellenőrzése
Miután regisztrálta az egyéni tartománynevet, is igénybe vehet pár másodpercet néhány óra múlva, mielőtt a DNS-információkat, ahol az Azure AD akkor láthatják őket, érvényes propagálása zajlik.

### <a name="to-verify-your-custom-domain-name"></a>Az egyéni tartománynév ellenőrzése
1. Jelentkezzen be a [Azure AD portálon](https://portal.azure.com/) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**, majd válassza ki **egyéni tartománynevek**.

3. Az a **Fabrikam – egyéni tartománynevek** panelen válassza ki az egyéni tartománynév **Contoso**.

    ![A Fabrikam – egyéni tartomány nevek panelen, a contoso kiemelésével](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

4. Az a **Contoso** panelen válassza ki **ellenőrizze** , hogy az egyéni tartomány megfelelően regisztrálva van, és érvényes Azure AD-hez.

    ![A DNS-bejegyzés adatainak és az ellenőrzés gombra a Contoso panel](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

### <a name="common-verification-issues"></a>Gyakori hitelesítési hibák
Ha az Azure AD nem képes egyéni tartománynév ellenőrzésére, próbálkozzon a következőkkel:
- **Várjon legalább egy órát, és próbálkozzon újra**. DNS-rekordokat kell propagálása előtt az Azure AD ellenőrizni tudja a tartományt, és ez a folyamat egy vagy több óráig tarthat.

- **Győződjön meg arról, hogy a DNS-rekord helyességéről.** Lépjen vissza a tartomány nevét regisztráló helyre, és ellenőrizze, hogy a bejegyzés van-e, és, hogy megegyezik-e az Azure AD által támogatott DSN tétel adatait.

    Ha nem frissíti a rekordot a regisztrálójánál helyen, egy olyan személlyel, amely rendelkezik a megfelelő engedélyekkel a bejegyzést, és győződjön meg arról, pontosan meg kell osztania a bejegyzést.

- **Ellenőrizze, hogy egy másik címtárban már nem a tartomány nevét.** A tartománynév csak egy címtárban, ami azt jelenti, hogy a tartománynév jelenleg ellenőrizve van egy másik címtárban, ha azt nem is lehet ellenőrizni az új címtárban ellenőrizhető. Duplikáció a probléma elhárításához törölnie kell az a tartomány nevét a régi könyvtárból. További információ a tartománynevek törléséről: [egyéni tartománynevek kezelése](../users-groups-roles/domains-manage.md).    

## <a name="next-steps"></a>További lépések
- Felhasználók hozzáadása a tartományhoz, lásd: [egyéni tartománynevek kezelése](../users-groups-roles/domains-manage.md).

- Ha rendelkezik a helyszíni verzióját, amelyet szeretne használhatja az Azure Active Directory mellett, a Windows Server [a helyszíni címtárak integrálása az Azure Active Directory](../connect/active-directory-aadconnect.md).