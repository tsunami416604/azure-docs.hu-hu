---
title: Adja hozzá az egyéni tartomány - vagy Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directory használata egyéni tartományok hozzáadásával kapcsolatos útmutatást.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376859"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Az Azure Active Directory portál használatával egyéni tartománynév hozzáadása

Minden új Azure AD-bérlőhöz tartozik egy kezdeti tartománynév, *\<tartománynév >. onmicrosoft. com*. A kezdeti tartománynév nem módosítható vagy törölhető, de felveheti a szervezet nevét. Az egyéni tartománynevek hozzáadásával a felhasználók számára ismerős felhasználónevek (például *alain\@contoso.com*) hozhatók létre.

## <a name="before-you-begin"></a>Előkészületek

Egyéni tartománynév hozzáadása előtt hozza létre a tartománynevet egy tartományregisztráló használatával. Egy akkreditált tartományregisztráló esetében tekintse meg a következőt: [ICANN-akkreditált regisztrátorok](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>A címtár létrehozása az Azure ad-ben

A tartománynév kap, miután az első Azure AD-címtár is létrehozhat. Jelentkezzen be a címtár Azure Portalba egy olyan fiókkal, amely az előfizetés **tulajdonosi** szerepkörét használja.

Az új címtár létrehozásához kövesse az [új bérlő létrehozása a szervezet számára](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)című témakör lépéseit.

>[!IMPORTANT]
>A személy, aki a bérlőt hoz létre a program automatikusan az adott bérlő globális rendszergazdája. A globális rendszergazdai a bérlőhöz további rendszergazdákat is hozzáadhat.

Az előfizetési szerepkörökkel kapcsolatos további információkért tekintse meg az [Azure RBAC szerepköreivel](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)foglalkozó témakört.

>[!TIP]
> Ha azt tervezi, hogy a helyszíni Windows Server AD-t az Azure AD-vel szeretné összevonása, akkor be kell jelölnie **ezt a tartományt a helyi Active Directory való egyszeri bejelentkezéshez** , amikor a Azure ad Connect eszközt futtatja a címtárak szinkronizálásához.
>
> Emellett a helyszíni címtárral való összevonáshoz kiválasztott tartománynevet is regisztrálnia kell az **Azure AD-tartomány** lépésnél a varázslóban. Ha szeretné megtekinteni, hogy a telepítő hogyan néz ki, tekintse meg [az összevonás számára kijelölt Azure ad-tartomány ellenőrzése](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)című témakört. Ha nem rendelkezik a Azure AD Connect eszközzel, [letöltheti itt](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Az egyéni tartománynév hozzáadása az Azure ad-ben

Miután létrehozta a címtárban, az egyéni tartománynevet is hozzáadhat.

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg és válassza ki a *Azure Active Directory* bármely oldalon. Ezután válassza az **Egyéni tartománynevek** > **egyéni tartomány hozzáadása**elemet.

    ![Custom Domain Names (egyéni tartománynevek) lap, amelyen az egyéni tartomány hozzáadása látható](media/add-custom-domain/add-custom-domain.png)

1. Az **Egyéni tartománynév**mezőben adja meg a szervezet új nevét, ebben a példában a *contoso.com*. Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget.

    ![Egyéni tartománynevek lap az egyéni tartomány hozzáadása oldalon](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >A megfelelő működéshez a *. com*, a *.net*vagy bármely más legfelső szintű bővítményt kell megadni.

    A nem ellenőrzött tartomány hozzá van adva. Megjelenik a **contoso.com** lap, amely a DNS-adatokat jeleníti meg. Mentse ezt az információt. Később szüksége lesz rá egy TXT-rekord létrehozásához a DNS konfigurálásához.

    ![DNS-bejegyzés adatokkal contoso lap](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>A DNS-adatokat hozzáadni a tartományregisztráló

Az egyéni tartománynév hozzáadása az Azure ad-hez, után térjen vissza a tartományregisztrálónál, és adja hozzá az Azure AD DNS-információkat a másolt TXT-fájlból. A TXT-rekordnak a tartományhoz való létrehozása ellenőrzi a tartománynév tulajdonjogát.

Térjen vissza a tartományregisztráló, és hozzon létre egy új TXT-rekordot a tartományhoz a másolt DNS-adatok alapján. Állítsa az élettartamot (TTL) 3600 másodpercre (60 perc), majd mentse a rekordot.

>[!IMPORTANT]
>A kívánt számú tartománynevek regisztrálhat. Azonban minden egyes tartományhoz lekérdezi a saját txt típusú rekordot az Azure ad-ből. Ügyeljen arra, hogy a rendszer a TXT-fájl adatait adja meg a tartományregisztrálónál. Ha hiba esetén helytelen vagy ismétlődő információt ad meg, meg kell várnia, amíg a TTL időtúllépés (60 perc), mielőtt újra próbálkozik.

## <a name="verify-your-custom-domain-name"></a>Az egyéni tartománynév ellenőrzése

Az Egyéni tartománynév regisztrálását követően győződjön meg arról, hogy az érvényes az Azure AD-ben. A tartományregisztráló és az Azure AD közötti propagálás lehet azonnali, vagy eltarthat néhány napig a tartományregisztrálótól függően.

Az egyéni tartomány nevének ellenőrzéséhez kövesse az alábbi lépéseket:

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg és válassza ki a *Azure Active Directory* bármely lapon, majd válassza az **Egyéni tartománynevek**lehetőséget.

1. Az **Egyéni tartománynevek**területen válassza ki az egyéni tartománynevet. Ebben a példában válassza a **contoso.com**lehetőséget.

    ![A Fabrikam – egyéni tartomány nevét oldala, amelyen kiemelve contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. A **contoso.com** lapon válassza az **ellenőrzés** lehetőséget, hogy ellenőrizze, hogy az egyéni tartomány megfelelően van-e regisztrálva, és hogy érvényes-e az Azure ad-hez.

    ![A DNS-bejegyzés adatainak és az ellenőrzés gombra a Contoso lap](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Miután ellenőrizte az egyéni tartománynevet, törölheti az ellenőrző TXT-vagy MX-fájlt.

## <a name="common-verification-issues"></a>Gyakori hitelesítési hibák

Ha az Azure AD nem képes egyéni tartománynév ellenőrzésére, próbálkozzon a következőkkel:

- **Várjon legalább egy órát, és próbálkozzon újra**. A DNS-rekordokat propagálni kell, mielőtt az Azure AD ellenőrizni tudja a tartományt. A folyamat egy óráig vagy tovább is tarthat.

- **Győződjön meg arról, hogy a DNS-rekord helyes.** Térjen vissza a tartománynév-regisztráló webhelyhez. Győződjön meg arról, hogy a bejegyzés létezik, és hogy az megfelel az Azure AD által biztosított DNS-bejegyzési információknak.

  Ha nem tudja frissíteni a rekordot a regisztrátor webhelyén, ossza meg a bejegyzést olyan személlyel, aki jogosult a bejegyzés hozzáadására, és ellenőrizze, hogy helyes-e.

- **Győződjön meg arról, hogy a tartománynév még nincs használatban egy másik címtárban.** A tartománynév csak egy címtárban ellenőrizhető. Ha a tartománynevet egy másik címtárban ellenőrzi, az nem ellenőrizhető az új könyvtárban is. Duplikáció a probléma elhárításához törölnie kell az a tartomány nevét a régi könyvtárból. További információ a tartománynevek törléséről: [Egyéni tartománynevek kezelése](../users-groups-roles/domains-manage.md).

- **Győződjön meg arról, hogy nem rendelkezik nem felügyelt Power BI Bérlővel.** Ha a felhasználók önkiszolgáló regisztráció a Power bi-ban aktiválták és létrehozott egy nem felügyelt bérlőt a szervezetén belül, meg kell átveszi belső vagy külső rendszergazdaként PowerShell használatával. További információkért tekintse meg a [nem felügyelt címtár az Azure Active Directoryban rendszergazdaként történő átvételét](../users-groups-roles/domains-admin-takeover.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

- Egy másik globális rendszergazda hozzáadása a címtárhoz. További információ: [szerepkörök és rendszergazdák társítása](active-directory-users-assign-role-azure-portal.md).

- Felhasználók hozzáadása a tartományhoz. További információ: [felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md).

- Kezelheti a tartomány nevét adatait az Azure ad-ben. További információ: [Egyéni tartománynevek kezelése](../users-groups-roles/domains-manage.md).

- Ha a Windows Server azon helyszíni verzióit használja, amelyeket a Azure Active Directory mellett szeretne használni, tekintse [meg a helyszíni címtárak integrálása Azure Active Directory](../connect/active-directory-aadconnect.md)használatával című témakört.
