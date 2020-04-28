---
title: A fiók zárolásának hibáinak megoldása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a felhasználói fiókokat a Azure Active Directory Domain Servicesban kizárni kívánt gyakori problémákat.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743450"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartománnyal kapcsolatos fiókzárolási problémák elhárítása

A rosszindulatúan ismétlődő bejelentkezési kísérletek megelőzése érdekében az Azure AD DS egy meghatározott küszöbérték után zárolja a fiókokat. Ez a fiókzárolás a bejelentkezési támadás incidense nélkül is történhet véletlenül. Ha például a felhasználó többször is helytelen jelszót ad meg, vagy a szolgáltatás egy régi jelszót próbál használni, a rendszer kizárja a fiókot.

Ez a hibaelhárítási cikk azt ismerteti, hogy miért történik a fiókok zárolása, és hogyan konfigurálhatja a viselkedést, és hogyan tekintheti át a biztonsági naplózást a zárolási események hibaelhárítása érdekében.

## <a name="what-is-an-account-lockout"></a>Mi az a fiókzárolás?

Az Azure AD DSban lévő felhasználói fiók ki van zárva, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesül. Ez a fiókzárolási viselkedés úgy lett kialakítva, hogy megvédje a nem kötelezően kikényszerített, automatikus digitális támadásra utaló kísérleteket.

**Alapértelmezés szerint, ha 2 percen belül 5 hibás jelszóval próbálkozik, a fiók 30 percig ki lesz zárva.**

Az alapértelmezett fiókzárolási küszöbértékek a részletes jelszóházirendek használatával konfigurálhatók. Ha a követelmények meghatározott készletével rendelkezik, felülbírálhatja ezeket az alapértelmezett fiókzárolási küszöbértékeket. Azonban nem ajánlott a küszöbértékek növelését végrehajtani, hogy csökkentse a fiók zárolását. Először hárítsa el a fiókzárolási viselkedés forrását.

### <a name="fine-grained-password-policy"></a>Részletes jelszóházirendek

A részletes jelszóházirendek (Fgpp-EK) segítségével meghatározott korlátozásokat alkalmazhat a jelszó-és fiókzárolási házirendek számára a tartomány különböző felhasználói számára. A FGPP csak az Azure AD DS felügyelt tartományon belüli felhasználókat érinti. A Felhőbeli felhasználókat és a tartományi felhasználókat az Azure AD-ből szinkronizálták az Azure AD DS felügyelt tartományba, az Azure AD DSon belül csak a jelszóházirend érinti. Az Azure AD-ben vagy egy helyszíni címtárban nem érinti a fiókjaikat.

A szabályzatokat az Azure AD DS felügyelt tartományán keresztül osztják el, és az elvégzett módosítások a következő felhasználói bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már kizárt felhasználói fiók zárolását.

A részletes jelszóházirendek, valamint a közvetlenül az Azure AD DSban létrehozott felhasználók közötti különbségekről, valamint az Azure AD-ből való szinkronizálásról további információt a [jelszó-és fiókzárolási házirendek konfigurálása][configure-fgpp]című témakörben talál.

## <a name="common-account-lockout-reasons"></a>Gyakori fiókzárolási okok

A fiókok zárolásának leggyakoribb okai a kártékony szándékok vagy tényezők nélkül, a következő esetekben:

* **A felhasználó zárolta magát.**
    * A legutóbbi jelszó módosítása után a felhasználó továbbra is egy korábbi jelszót használ? Az 5 sikertelen kísérletet 2 percen belül az alapértelmezett fiókzárolási házirend miatt a felhasználó véletlenül újrapróbálkozhat egy régi jelszóval.
* **Van olyan alkalmazás vagy szolgáltatás, amely régi jelszóval rendelkezik.**
    * Ha egy fiókot alkalmazások vagy szolgáltatások használnak, akkor előfordulhat, hogy az erőforrások többször is megpróbálnak bejelentkezni egy régi jelszóval. Ez a viselkedés a fiók zárolását okozza.
    * Próbálja meg csökkenteni a fiókok használatát több különböző alkalmazás vagy szolgáltatás között, és jegyezze fel a hitelesítő adatokat. Ha a fiók jelszava megváltozik, frissítse a kapcsolódó alkalmazásokat vagy szolgáltatásokat.
* **A jelszó egy másik környezetben módosult, és az új jelszó még nincs szinkronizálva.**
    * Ha a fiók jelszava az Azure-AD DSon kívül módosul, például egy helyszíni AD DS környezetben, eltarthat néhány percig, amíg a jelszó megváltozása szinkronizálva lesz az Azure AD-ben és az Azure-AD DS.
    * Olyan felhasználó, aki az Azure AD DS használatával próbál bejelentkezni egy erőforrásba, mielőtt a jelszó-szinkronizálási folyamat befejeződik, a fiókját ki kell zárni.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Fiókok zárolásának hibakeresése biztonsági naplózással

Az [Azure AD DS biztonsági naplózásának engedélyezésével][security-audit-events]elháríthatja a fiókzárolási események bekövetkezésekor felmerülő problémákat. A naplózási események csak a szolgáltatás engedélyezésének időpontjában vannak rögzítve. Ideális esetben a biztonsági naplózást is engedélyeznie kell, *mielőtt* probléma merül fel a hibakeresés során. Ha egy felhasználói fióknak többször is van zárolási problémája, akkor a következő esetekben engedélyezheti a biztonsági naplózást.

A biztonsági naplózás engedélyezése után az alábbi példák bemutatják, hogyan tekintheti át a fiókzárolási *eseményeket*, a *4740*kódot.

Az összes fiókzárolási esemény megtekintése az elmúlt hét napban:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

A *driley*nevű fiók utolsó hét napjához tartozó összes fiókzárolási esemény megtekintése.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Az összes fiókzárolási esemény megtekintése a 2019. június 26. között, 9:00 és 2019 éjfél, a dátum és idő szerint növekvő sorrendbe rendezve:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>További lépések

A fiókzárolási küszöbértékek beállítására szolgáló részletes jelszóházirendek részletes ismertetését lásd: [jelszó-és fiókzárolási házirendek konfigurálása][configure-fgpp].

Ha továbbra sem sikerül csatlakoztatni a virtuális gépet az Azure AD DS felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
