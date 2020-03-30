---
title: Helyszíni Azure AD jelszóvédelem engedélyezése
description: Megtudhatja, hogy miként engedélyezheti az Azure AD jelszavas védelmet egy helyszíni Active Directory tartományi szolgáltatások környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263815"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Helyszíni Azure Active Directory jelszóvédelem engedélyezése

A felhasználók gyakran hoznak létre olyan jelszavakat, amelyek gyakori helyi szavakat használnak, például iskolát, sportcsapatot vagy híres személyt. Ezek a jelszavak könnyen kitalálható, és gyenge a szótáralapú támadások ellen. Erős jelszavak kényszerítése a szervezetben, az Azure Active Directory (Azure AD) jelszavas védelem globális és egyéni tiltott jelszó listát biztosít. A jelszómódosítási kérelem sikertelen, ha a tiltott jelszavak listájában egyezés van.

A helyszíni Active Directory tartományi szolgáltatások (AD DS) környezet védelme érdekében telepítheti és konfigurálhatja az Azure AD password protection-t a helyszíni tartományvezérlővel való együttműködésre. Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD password protection a helyszíni környezetben.

Az Azure AD Password Protection helyszíni környezetben való működéséről a [Windows Server Active Directory azure AD password protection kényszerítése](concept-password-ban-bad-on-premises.md)című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD password protection a helyszíni környezetben. A cikk befejezése előtt [telepítse és regisztrálja az Azure AD Password Protection proxyszolgáltatást és a tartományvezérlő-ügynököket](howto-password-ban-bad-on-premises-deploy.md) a helyszíni AD DS-környezetben.

## <a name="enable-on-premises-password-protection"></a>Helyszíni jelszavas védelem engedélyezése

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és keresse meg az **Azure Active Directory** > **biztonsági** > **hitelesítési módszerek** > **jelszavas védelme**.
1. A Windows **Server Active Directory jelszavas védelmének engedélyezése** beállítását *válassza Igen*beállításra.

    Ha ez a beállítás *nem,* az összes üzembe helyezett Azure AD password protection dc-ügynökök egy nyugalmi módba lép, ahol az összes jelszót fogadja el, ahogy van. Nem végeznek érvényesítési tevékenységeket, és a rendszer nem hoz létre naplózási eseményeket.

1. Javasoljuk, hogy kezdetben állítsa be a **mód** *naplózás .* Miután elégedett a funkcióval és a szervezet felhasználóira gyakorolt hatással, átválthat a **Mód** *kényszerített módra.* További információt a [működési módokról](#modes-of-operation)szóló következő szakaszban talál.
1. Ha készen áll, válassza a **Mentés gombot.**

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Működési módok

Ha engedélyezi a helyszíni Azure AD password protection, *használhatja* a vizsgálati mód vagy *kényszerítési* mód. Azt javasoljuk, hogy a kezdeti üzembe helyezés és tesztelés mindig indítsa el a vizsgálati módban. Az eseménynapló bejegyzéseit ezután figyelni kell annak előrejelzéséhez, hogy a meglévő működési folyamatok zavarása megtörténik-e, ha a *Kényszerítési* mód engedélyezve van.

### <a name="audit-mode"></a>Vizsgálati mód

*A vizsgálati* mód a szoftver "mi lenne, ha" módban történő futtatásának egyik módja. Minden Egyes Azure AD Password Protection DC-ügynök szolgáltatás kiértékeli a bejövő jelszót az aktuálisan aktív szabályzat szerint.

Ha az aktuális házirend úgy van beállítva, hogy vizsgálati módban legyen, a "hibás" jelszavak eseménynapló-üzeneteket eredményeznek, de feldolgozzák és frissítik őket. Ez a viselkedés az egyetlen különbség a naplózási és kényszerítési mód között. Minden más művelet ugyanúgy fut.

### <a name="enforced-mode"></a>Kényszerített mód

*A kényszerített* mód a végső konfiguráció. Mint ha a vizsgálati módban, minden Azure AD Password Protection DC-ügynök szolgáltatás kiértékeli a bejövő jelszavakat az aktuálisan aktív szabályzat szerint. Ha a kényszerített mód azonban engedélyezve van, a házirend szerint nem biztonságosnak ítélt jelszót a rendszer elutasítja.

Ha egy jelszót az Azure AD Password Protection DC-ügynök kényszerített módban elutasít, a végfelhasználó hasonló hibát lát, mintha látná, ha a jelszavát a hagyományos helyszíni jelszó-összetettség i. Előfordulhat például, hogy egy felhasználó a következő hagyományos hibaüzenetet látja a Windows bejelentkezési képernyőjén, vagy módosítja a jelszót:

*"Nem lehet frissíteni a jelszót. Az új jelszóhoz megadott érték nem felel meg a tartomány hosszára, összetettségére vagy előzményre vonatkozó követelményeinek."*

Ez az üzenet csak egy példa számos lehetséges kimenetelre. Az adott hibaüzenet a nem biztonságos jelszó beállítását megkísérlő szoftvertől vagy forgatókönyvtől függően változhat.

Előfordulhat, hogy az érintett végfelhasználóknak együtt kell működniük az informatikai személyzettel az új követelmények megértéséhez és a biztonságos jelszavak kiválasztásához.

> [!NOTE]
> Az Azure AD password protection nincs szabályosabban az ügyfélgép által megjelenített adott hibaüzenet, ha egy gyenge jelszó elutasítása.

## <a name="next-steps"></a>További lépések

A szervezet tiltott jelszólistájának testreszabásához olvassa [el az Azure AD Password Protection egyéni tiltott jelszavak listájának konfigurálása című témakört.](tutorial-configure-custom-password-protection.md)

A helyszíni események figyelése: [Monitoring on-prem Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
