---
title: Egyéni tartomány hozzáadása – Azure Active Directory | Microsoft dokumentumok
description: Útmutató az egyéni tartomány azure Active Directory használatával történő hozzáadásához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262151"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Egyéni tartománynév hozzáadása az Azure Active Directory portálon

Minden új Azure AD-bérlő höz tartozik egy kezdeti tartománynév, * \<>.onmicrosoft.com tartománynév.* A kezdeti tartománynév nem módosítható vagy törölhető, de hozzáadhatja a szervezet nevét. Az egyéni tartománynevek hozzáadásával olyan felhasználóneveket hozhat létre, amelyek ismerősek a felhasználók számára, például *az\@alain contoso.com.*

## <a name="before-you-begin"></a>Előkészületek

Egyéni tartománynév hozzáadása előtt hozza létre a tartománynevet egy tartományregisztrálóval. Akkreditált tartományregisztrátorról az [ICANN-akkreditált regisztrátorok](https://www.icann.org/registrar-reports/accredited-list.html)oldalon lehet.

## <a name="create-your-directory-in-azure-ad"></a>A címtár létrehozása az Azure AD-ben

Miután bekapta a tartománynevet, létrehozhatja az első Azure AD-címtár. Jelentkezzen be az Azure Portalon a címtárban, egy fiók használatával a **tulajdonos** szerepkör az előfizetéshez.

Hozza létre az új könyvtárat az Új bérlő létrehozása a szervezet számára című lépés lépéseit [követve.](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)

>[!IMPORTANT]
>A bérlőt létrehozó személy automatikusan az adott bérlő globális rendszergazdája. A globális rendszergazda további rendszergazdákat adhat a bérlőhöz.

Az előfizetési szerepkörökről további információt az [Azure RBAC-szerepkörök című témakörben talál.](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)

>[!TIP]
> Ha azt tervezi, hogy a helyszíni Windows Server AD-t az Azure AD-vel összekapcsolja, akkor az Azure AD Connect eszköz futtatásakor az I plan to configure if **this domain for single sign-on with my local Active Directory** t, amikor futtatja a helyi AD Connect eszközt a könyvtárak szinkronizálásához.
>
> Emellett a helyszíni címtárral való összevonáshoz kiválasztott tartománynevet is regisztrálnia kell az **Azure AD-tartomány** lépésnél a varázslóban. A beállítás megjelenésének megtekintéséről az [Összevonásra kiválasztott Azure AD-tartomány ellenőrzése című](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)témakörben látható. Ha nem rendelkezik az Azure AD Connect eszközzel, [letöltheti itt.](https://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Egyéni tartománynév hozzáadása az Azure AD-hez

A címtár létrehozása után hozzáadhatja az egyéni tartománynevet.

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg, és válassza ki az *Azure Active Directory* bármely oldalon. Ezután válassza **az Egyéni tartománynevek** > **Egyéni tartomány hozzáadása lehetőséget.**

    ![Egyéni tartománynevek lap, az Egyéni tartomány hozzáadása mezőben](media/add-custom-domain/add-custom-domain.png)

1. Az **Egyéni tartománynév**mezőbe írja be a szervezet új nevét ebben a példában *a contoso.com.* Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget.

    ![Egyéni tartománynevek lap, egyéni tartomány hozzáadása lap](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >A megfelelő működéshez meg kell adnia a *.com*, *.net*vagy bármely más legfelső szintű bővítményt.

    A nem ellenőrzött tartomány hozzáadódik. Megjelenik **a contoso.com** lap, amely a DNS-adatokat tartalmazza. Mentse ezt az információt. A DNS konfigurálásához később létre kell hoznia egy TXT rekordot.

    ![Contoso lap dns-bejegyzési adatokkal](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>A DNS-adatok hozzáadása a tartományregisztrálóhoz

Miután hozzáadja az egyéni tartománynevet az Azure AD-hez, vissza kell térnie a tartományregisztrálóhoz, és hozzá kell adnia az Azure AD DNS-adatait a másolt TXT-fájlból. Ha létrehozza ezt a TXT rekordot a tartományához, az ellenőrzi a tartománynév tulajdonjogát.

Lépjen vissza a tartományregisztrálóhoz, és hozzon létre egy új TXT rekordot a tartományhoz a másolt DNS-adatok alapján. Állítsa az élő időt (TTL) 3600 másodpercre (60 perc), majd mentse a rekordot.

>[!IMPORTANT]
>Annyi tartománynevet regisztrálhat, amennyit csak akar. Azonban minden tartomány lealevelét az Azure AD-től. Legyen óvatos, amikor megadja a TXT fájl adatait a tartományregisztrálónál. Ha véletlenül rossz vagy ismétlődő adatokat ad meg, meg kell várnia, amíg a TTL időtúllépésre kerül (60 perc), mielőtt újra próbálkozna.

## <a name="verify-your-custom-domain-name"></a>Az egyéni tartománynév igazolása

Az egyéni tartománynév regisztrálása után győződjön meg arról, hogy érvényes az Azure AD-ben. A tartományregisztráló tól az Azure AD-be történő propagálás azonnali lehet, vagy a tartományregisztrálótól függően néhány napig is eltarthat.

Az egyéni tartománynév ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

1. Keresse meg és válassza ki az *Azure Active Directoryt* bármelyik lapon, majd válassza **az Egyéni tartománynevek**lehetőséget.

1. Az **Egyéni tartománynevek**párbeszédpanelen jelölje ki az egyéni tartománynevet. Ebben a példában válassza **a contoso.com**lehetőséget.

    ![Fabrikam - Egyéni tartománynevek lap, kiemelve a contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. A **contoso.com** lapon válassza **az Ellenőrzés lehetőséget,** hogy az egyéni tartomány megfelelően regisztrálva legyen, és érvényes-e az Azure AD-re.

    ![Contoso lap a DNS-bejegyzés adataival és az Ellenőrzés gombbal](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Miután ellenőrizte az egyéni tartománynevet, törölheti az ellenőrző TXT- vagy MX-fájlt.

## <a name="common-verification-issues"></a>Gyakori ellenőrzési problémák

Ha az Azure AD nem tudja ellenőrizni az egyéni tartománynevet, próbálkozzon a következő javaslatokkal:

- **Várjon legalább egy órát, majd próbálkozzon újra.** A DNS-rekordokat propagálni kell, mielőtt az Azure AD ellenőrizni tudja a tartományt. A folyamat egy óráig vagy tovább is tarthat.

- **Ellenőrizze, hogy helyes-e a DNS-rekord.** Lépjen vissza a tartománynév-regisztráló webhelyére. Győződjön meg arról, hogy a bejegyzés ott van, és hogy megegyezik az Azure AD által biztosított DNS-bejegyzési adatokkal.

  Ha nem tudja frissíteni a rekordot a regisztrátorwebhelyen, ossza meg a bejegyzést valakivel, akinek engedélye van a bejegyzés hozzáadására, és ellenőrizze, hogy helyes-e.

- **Győződjön meg arról, hogy a tartománynév nincs még használatban egy másik könyvtárban.** A tartománynév csak egy könyvtárban ellenőrizhető. Ha a tartománynév jelenleg egy másik könyvtárban van ellenőrizve, akkor az nem ellenőrizhető az új könyvtárban sem. Az ismétlődési probléma megoldásához törölnie kell a tartománynevet a régi könyvtárból. A tartománynevek törléséről az [Egyéni tartománynevek kezelése című](../users-groups-roles/domains-manage.md)témakörben talál további információt.

- **Győződjön meg arról, hogy nincsenek nem felügyelt Power BI-bérlők.** Ha a felhasználók önkiszolgáló regisztrációval aktiválták a Power BI-t, és nem felügyelt bérlőt hoztak létre a szervezet számára, a PowerShell használatával belső vagy külső rendszergazdaként kell átvennie a felügyeletet. További információkért tekintse meg a [nem felügyelt címtár az Azure Active Directoryban rendszergazdaként történő átvételét](../users-groups-roles/domains-admin-takeover.md) ismertető cikket.

## <a name="next-steps"></a>További lépések

- Vegyen fel egy másik globális rendszergazdát a könyvtárba. További információt a [Szerepkörök és rendszergazdák hozzárendelése](active-directory-users-assign-role-azure-portal.md)című témakörben talál.

- Felhasználók hozzáadása a tartományhoz. További információt a Felhasználók hozzáadása és törlése című témakörben [talál.](add-users-azure-active-directory.md)

- A tartománynév-adatok kezelése az Azure AD-ben. További információt az [Egyéni tartománynevek kezelése](../users-groups-roles/domains-manage.md)című témakörben talál.

- Ha a Windows Server helyszíni verzióit az Azure Active Directory mellett szeretné használni, [olvassa el a Helyszíni könyvtárak integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)című témakört.
