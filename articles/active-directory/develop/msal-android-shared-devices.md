---
title: Megosztott eszköz mód Android-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: További információ arról, hogyan engedélyezheti a megosztott eszközmódot, hogy az Első vonalbeli dolgozók megoszthassák androidos eszközét
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: d9874e27c21906512c2f6c841767b4d6591dbeaf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550265"
---
# <a name="shared-device-mode-for-android-devices"></a>Megosztott eszköz mód Android-eszközökhöz

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Első vonalban dolgozók, mint a kiskereskedelmi partnerek, a hajózószemélyzet tagjai, és a helyszíni szolgáltatás munkavállalók gyakran használják a megosztott mobil eszköz munkáját. Ez akkor válik problémássá, amikor elkezdik megosztani a jelszavakat vagy pin-számokat, hogy hozzáférjenek az ügyfél- és üzleti adatokhoz a megosztott eszközön.

A megosztott eszköz mód lehetővé teszi az Android-eszköz konfigurálását, hogy az több alkalmazott számára is könnyen megosztható legyen. Az alkalmazottak gyorsan bejelentkezhetnek és hozzáférhetnek az ügyfelek adataihoz. Amikor befejezték a műszakot vagy a feladatot, kijelentkezhetnek az eszközből, és azonnal készen állnak a következő alkalmazott használatára.

A megosztott eszköz mód az eszköz Microsoft-identitással védett kezelését is biztosítja.

Megosztott eszközmódú alkalmazás létrehozásához a fejlesztők és a felhőalapú eszközök rendszergazdái együttműködnek:

- A fejlesztők egyfiókos alkalmazást írnak (a többfiókos alkalmazások `"shared_device_mode_supported": true` nem támogatottak megosztott eszköz módban), hozzáadják az alkalmazás konfigurációját, és kódot írnak például a megosztott eszközkijelentkezés kezeléséhez.
- Az eszközadminisztrátorok előkészítik az eszközt a megosztásra a hitelesítő alkalmazás telepítésével, és az eszköz megosztott módba állításával a hitelesítő alkalmazás használatával. Csak a [Felhőeszköz-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator-permissions) szerepkörben lévő felhasználók helyezhetnek el egy eszközt megosztott módba a [Hitelesítő alkalmazással.](../user-help/user-help-auth-app-overview.md) A szervezeti szerepkörök tagságát az Azure Portalon a következő módon konfigurálhatja: **Azure Active Directory** > **szerepkörök és rendszergazdák** > **felhőalapú eszközadminisztrátora.**

 Ez a cikk elsősorban arra összpontosít, hogy a fejlesztőknek mire kell gondolniuk.

## <a name="single-vs-multiple-account-applications"></a>Egy- és többfiókos alkalmazások

A Microsoft Authentication Library SDK (MSAL) használatával írt alkalmazások egyetlen fiókot vagy több fiókot kezelhetnek. További részletek: [egyfiókos vagy többfiókos mód.](single-multi-account.md) Az alkalmazás számára elérhető Microsoft identity platform funkciók attól függően változnak, hogy az alkalmazás egyfiókos vagy többfiókos módban fut-e.

**A megosztott eszközmódú alkalmazások csak egyfiókos módban működnek.**

> [!IMPORTANT]
> A csak többfiókos módot támogató alkalmazások nem futtathatók megosztott eszközön. Ha egy alkalmazott olyan alkalmazást tölt be, amely nem támogatja az egyfiókos módot, nem fog futni a megosztott eszközön.
>
> Az MSAL SDK kiadása előtt írt alkalmazások többfiókos módban futnak, és frissíteni kell őket, hogy támogassák az egyfiókos módot, mielőtt megosztott módú eszközön futtathatók lennének.

**Az egyfiókos és a többfiókos fiókok támogatása**

Az alkalmazás úgy is létrehozható, hogy mind a személyes, mind a megosztott eszközökön is támogassa a futtatást. Ha az alkalmazás jelenleg több fiókot is támogat, és támogatni szeretné a megosztott eszközmódot, adja hozzá az egyfiókos mód támogatását.

Előfordulhat, hogy azt is szeretné, hogy az alkalmazás a futó eszköz típusától függően módosítsa a viselkedését. Itt `ISingleAccountPublicClientApplication.isSharedDevice()` határozhatja meg, hogy mikor fusson egyfiókos módban.

Két különböző felület van, amelyek az alkalmazás eszköztípusát jelölik. Amikor alkalmazáspéldányt kér az MSAL alkalmazásgyárából, a rendszer automatikusan megküldi a megfelelő alkalmazásobjektumot.

A következő objektummodell bemutatja, hogy milyen típusú objektumot kaphat, és mit jelent egy megosztott eszköz környezetében:

![nyilvános ügyfélalkalmazás öröklési modellje](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Az objektum betöltésekén meg kell vizsgálnia a típusellenőrzést, és a megfelelő felületre kell vetnie. `PublicClientApplication` A következő kód több fiók módot vagy egyfiókos módot keres, és megfelelően elveti az alkalmazásobjektumot:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

A következő különbségek attól függően érvényesek, hogy az alkalmazás megosztott vagy személyes eszközön fut-e:

|  | Megosztott módú eszköz  | Személyes eszköz |
|---------|---------|---------|
| **Fiókok**     | Egy fiókos | Több fiók |
| **Bejelentkezés** | Globális | Globális |
| **Kijelentkezés** | Globális | Minden alkalmazás szabályozhatja, hogy a kijelentkezés helyi-e az alkalmazás vagy az alkalmazáscsalád számára. |
| **Támogatott fióktípusok** | Csak munkahelyi fiókok | Támogatott személyes és munkahelyi fiókok  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Miért érdemes csak egyfiókos módot támogatni?

Ha olyan alkalmazást ír, amelyet csak megosztott eszköz használatával használnak az első vonalbeli dolgozók számára, azt javasoljuk, hogy az alkalmazást úgy írja meg, hogy csak az egyfiókos módot támogassa. Ez magában foglalja a legtöbb olyan alkalmazást, amely feladatközpontú, például az orvosi feljegyzésekre, a számlaalkalmazásokra és a legtöbb üzletági alkalmazásra. Csak az egyfiókos mód támogatása egyszerűsíti a fejlesztést, mert nem kell megvalósítania a többfiókos alkalmazások részét képezi további funkciókat.

## <a name="what-happens-when-the-device-mode-changes"></a>Mi történik, ha az eszköz mód megváltozik?

Ha az alkalmazás többfiókos módban fut, és a rendszergazda az eszközt megosztott eszköz módba helyezi, az eszközön lévő összes fiók törlődik az alkalmazásból, és az alkalmazás egyfiókos módba vált.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Megosztott eszközkijelentkezés és az alkalmazás teljes életciklusa

Amikor egy felhasználó kijelentkezik, lépéseket kell tennie a felhasználó adatainak és adatainak védelme érdekében. Ha például orvosi feljegyzések alkalmazást készít, győződjön meg arról, hogy amikor a felhasználó kijelentkezik, a betegrekordok törlődnek. Az alkalmazás nak elő kell készülnie erre, és minden alkalommal ellenőriznie kell, amikor belép az előtérbe.

Ha az alkalmazás az MSAL segítségével jelentkezik ki a felhasználóegy megosztott módban futó eszközön futó alkalmazásban, a bejelentkezett fiók és a gyorsítótárazott tokenek mind az alkalmazásból, mind az eszközből törlődnek.

Az alábbi ábra az alkalmazás teljes életciklusát és az alkalmazás futása közben előforduló gyakori eseményeket mutatja be. Az ábra a tevékenység elindításának, a fiók bavaló bejelentkezésének és kijelentkezésének időpontjától, valamint atevékenység szüneteltetésének, folytatásának és leállításának időpontjától terjed ki.

![Megosztott eszközalkalmazás életciklusa](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>További lépések

Próbálja ki a [Megosztott eszköz mód használata az Android-alkalmazás oktatóanyagában,](tutorial-v2-shared-device-mode.md) amely bemutatja, hogyan futtathat egy első vonali munkavégző alkalmazást megosztott módú Android-eszközön.
