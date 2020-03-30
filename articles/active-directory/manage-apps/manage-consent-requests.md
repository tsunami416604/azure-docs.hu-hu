---
title: Az alkalmazások hozvaló hozzájárulásának kezelése és a hozzájárulási kérelmek kiértékelése – Azure AD
description: Ismerje meg, hogyan kezelheti a hozzájárulási kérelmeket, ha a felhasználó hozzájárulása le van tiltva vagy korlátozott, és hogyan értékelheti ki az alkalmazás bérlői szintű rendszergazdai hozzájárulásiránti kérelmet.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367853"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Az alkalmazásokhoz való hozzájárulás kezelése és a hozzájárulási kérelmek értékelése

A Microsoft [azt javasolja,](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) hogy tiltsa le a végfelhasználói beleegyezését az alkalmazásokhoz. Ez központosítja a döntéshozatali folyamatot a szervezet biztonsági és identitás-rendszergazdai csapatával.

Miután a végfelhasználói hozzájárulás le van tiltva vagy korlátozott, számos fontos szempontot kell figyelembe venni, hogy a szervezet továbbra is biztonságos maradjon, miközben továbbra is lehetővé teszi az üzleti legkritikusabb alkalmazások használható. Ezek a lépések alapvető fontosságúak a szervezet támogatási csoportjára és a rendszergazdákra gyakorolt hatás minimalizálása érdekében, miközben megakadályozzák a nem felügyelt fiókok használatát a külső alkalmazásokban.

## <a name="process-changes-and-education"></a>Folyamatváltozások és oktatás

 1. Fontolja meg a [rendszergazdai hozzájárulási munkafolyamat (előzetes verzió)](configure-admin-consent-workflow.md) engedélyezését, hogy a felhasználók közvetlenül a jóváhagyási képernyőről kérhessék a rendszergazda jóváhagyását.

 2. Győződjön meg arról, hogy minden rendszergazda tisztában van az engedélyekkel és a [hozzájárulási keretrendszerrel](../develop/consent-framework.md), a [hozzájárulási parancs működésével,](../develop/application-consent-experience.md) valamint a [bérlői rendszergazdai jóváhagyásra vonatkozó kérelem kiértékelésének módjával.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Tekintse át a szervezet meglévő folyamatait, hogy a felhasználók rendszergazdai jóváhagyást kérjenek egy alkalmazáshoz, és szükség esetén frissítéseket készítsenek. Ha a folyamatok megváltoznak:
    * Frissítse a vonatkozó dokumentációt, a figyelést, az automatizálást és így tovább.
    * A folyamatmódosítások at az összes érintett felhasználóval, fejlesztővel, támogatási csapattal és informatikai rendszergazdával kommunikálja.

## <a name="auditing-and-monitoring"></a>Naplózás és figyelés

1. [Audit alkalmazások és a szervezetben megadott engedélyeket](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) annak biztosítására, hogy indokolatlan vagy gyanús alkalmazások korábban nem kaptak hozzáférést az adatokhoz.

2. [Tekintse át az Illegális hozzájárulási támogatások észlelését és kiiktatását az Office 365-ben,](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) és olvassa el az OAuth beleegyezését kérő gyanús alkalmazások elleni további gyakorlati tanácsokat és biztosítékokat.

3. Ha a szervezet rendelkezik a megfelelő licenccel:

    * A Microsoft Cloud App Security további [OAuth alkalmazásnaplózási szolgáltatásainak](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)használata.
    * Az Azure Monitor munkafüzetek segítségével [figyelheti az engedélyeket és a hozzájárulással](../reports-monitoring/howto-use-azure-monitor-workbooks.md) kapcsolatos tevékenységeket. A *Consent Insights* munkafüzet az alkalmazások számát a sikertelen hozzájárulási kérelmek száma szerint látja. Ez hasznos lehet a rendszergazdák alkalmazásainak fontossági sorrendbe adása, hogy áttekinthesjék és eldöntsék, hogy rendszergazdai jóváhagyást adnak-e nekik.

### <a name="additional-considerations-for-reducing-friction"></a>További szempontok a súrlódás csökkentésére

A már használatban lévő megbízható, üzleti legkritikusabb alkalmazásokra gyakorolt hatás minimalizálása érdekében fontolja meg proaktív hozzájárulás megadása a rendszergazdai jóváhagyást a nagyszámú felhasználói hozzájárulással rendelkező alkalmazások számára:

1. Leltárt készíteni a szervezethez már nagy használatú alkalmazásokról, bejelentkezési naplók vagy hozzájárulási támogatási tevékenység alapján. A [PowerShell-parancsfájlok](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) segítségével gyorsan és egyszerűen felderíthető kedve alkalmazások számos felhasználói hozzájárulási támogatással.

2. Értékelje ki azokat a legnépszerűbb alkalmazásokat, amelyek még nem kaptak rendszergazdai jóváhagyást.

   > [!IMPORTANT]
   > Gondosan értékelje ki az alkalmazást, mielőtt bérlői szintű rendszergazdai jóváhagyást adna meg, még akkor is, ha a szervezet számos felhasználója már hozzájárult.

3. Minden egyes jóváhagyott alkalmazáshoz adja meg a bérlői szintű rendszergazdai jóváhagyást az alább dokumentált módszerek egyikével.

4. Minden egyes jóváhagyott alkalmazás esetében fontolja meg [a felhasználói hozzáférés korlátozását.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>A bérlői szintű rendszergazdai hozzájárulásra vonatkozó kérelem kiértékelése

Bérlői szintű rendszergazdai hozzájárulás megadása bizalmas művelet.  Az engedélyek a teljes szervezet nevében lesznek megadva, és tartalmazhatnak magas szintű jogosultsággal rendelkező műveletek kísérletére vonatkozó engedélyeket. Például a szerepkör-kezelés, az összes postaládához vagy webhelyhez való teljes hozzáférés és a teljes felhasználói megszemélyesítés.

A bérlői szintű rendszergazdai hozzájárulás megadása előtt meg kell győződnie arról, hogy megbízik az alkalmazásban és az alkalmazás közzétevőjében a megadott hozzáférési szinthez. Ha nem biztos abban, hogy tisztában van azzal, hogy ki vezérli az alkalmazást, és miért kéri az alkalmazások az engedélyeket, *ne adjon meg hozzájárulást.*

Az alábbi lista néhány olyan javaslatot tartalmaz, amelyeket figyelembe kell venni a rendszergazdai hozzájárulás megadására vonatkozó kérelem kiértékelésekor.

* **Ismerje meg az engedélyeket és a [hozzájárulási keretrendszert](../develop/consent-framework.md) a Microsoft identitásplatformján.**

* **Ismerje meg a [delegált engedélyek és az alkalmazásengedélyek közötti különbséget.](../develop/v2-permissions-and-consent.md#permission-types)**

   Az alkalmazásengedélyek lehetővé teszik, hogy az alkalmazás felhasználói beavatkozás nélkül hozzáférjen a teljes szervezet adataihoz. A delegált engedélyek lehetővé teszik, hogy az alkalmazás egy olyan felhasználó nevében járjon el, aki valamikor be volt jelentkezve az alkalmazásba.

* **Ismerje meg a kért engedélyeket.**

   Az alkalmazás által kért engedélyek a [jóváhagyási parancssorban](../develop/application-consent-experience.md)találhatók. Az engedélycím kibontásával megjelenik az engedély leírása. Az alkalmazásengedélyek leírása általában "bejelentkezett felhasználó nélkül" végződik. A delegált engedélyek leírása általában "a bejelentkezett felhasználó nevében" végződik. A Microsoft Graph API engedélyeit a [Microsoft Graph engedélyek hivatkozása] című dokumentum ismerteti, lásd a többi API dokumentációját, hogy megismerjék az általuk elérhetővé tett engedélyeket.

   Ha nem érti a kért engedélyt, *ne adjon hozzájárulást.*

* **Ismerje meg, hogy melyik alkalmazás kér engedélyeket, és ki tette közzé az alkalmazást.**

   Legyen óvatos a rosszindulatú alkalmazások próbál kinézni, mint más alkalmazások.

   Ha kételkedik egy alkalmazás vagy annak közzétevőjének legitimitásában, *ne adjon hozzájárulást.* Ehelyett kérjen további megerősítést (például közvetlenül az alkalmazás közzétevőjétől).

* **Győződjön meg arról, hogy a kért engedélyek igazodnak az alkalmazástól elvárt funkciókhoz.**

   Előfordulhat például, hogy egy SharePoint-webhelykezelést kínáló alkalmazásnak delegált hozzáférésre van szüksége az összes webhelycsoport olvasásához, de nem feltétlenül kell teljes körű hozzáférést biztosítani az összes postaládához, vagy teljes megszemélyesítési jogosultságot a címtárban.

   Ha azt gyanítja, hogy az alkalmazás a szükségesnél több engedélyt kér, *ne adjon meg hozzájárulást.* További részletekért forduljon az alkalmazás kiadójához.

## <a name="granting-consent-as-an-administrator"></a>Hozzájárulás megadása rendszergazdaként

### <a name="granting-tenant-wide-admin-consent"></a>Bérlői rendszergazdai hozzájárulás megadása

Lásd: Az egész [bérlői rendszergazdai hozzájárulás megadása egy alkalmazáshoz](grant-admin-consent.md) lépésről lépésre az Azure Portalon, az Azure AD PowerShell használatával vagy a hozzájárulási kérelemből adott bérlői szintű rendszergazdai hozzájárulás megadásához.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Hozzájárulás megadása egy adott felhasználó nevében

Ahelyett, hogy a teljes szervezet számára jóváhagyást adna, a rendszergazda a [Microsft Graph API-t](https://docs.microsoft.com/graph/use-the-api) is használhatja arra, hogy egyetlen felhasználó nevében delegált engedélyekhez adjon hozzájárulást. További információ: [Hozzáférés beszerezni egy felhasználó nevében](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>A felhasználói hozzáférés korlátozása az alkalmazásokhoz

A felhasználók hozzáférése az alkalmazásokhoz akkor is korlátozott lehet, ha a bérlőszintű rendszergazdai hozzájárulás meglett adva. A felhasználók [és csoportok hozzárendelésének módszereicímű](methods-for-assigning-users-and-groups.md)témakörben talál további információt arról, hogy miként követelheti meg a felhasználó hozzárendelését egy alkalmazáshoz.

További átfogó áttekintést, valamint további összetett forgatókönyvek kezelését, [lásd: Az Azure AD használata az alkalmazáshozzáférés-kezelés.](what-is-access-management.md)

## <a name="next-steps"></a>További lépések

[Öt lépés az identitás-infrastruktúra biztosításához](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[A rendszergazdai hozzájárulási munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[Annak konfigurálása, hogy a végfelhasználók hogyan járuljanak hozzá az alkalmazásokhoz](configure-user-consent.md)

[Engedélyek és hozzájárulás a Microsoft identitásplatformján](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)