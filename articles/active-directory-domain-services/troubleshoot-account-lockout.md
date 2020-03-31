---
title: Fiókzárolás hibaelhárítása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el az Azure Active Directory tartományi szolgáltatásokban a felhasználói fiókokat zároló gyakori problémákelhárítását.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 2e274aa353f6c3e485ae10a6a67ee2940eb88b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246319"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által kezelt tartománnyal kapcsolatos fiókzárolási problémák elhárítása

Az ismételt rosszindulatú bejelentkezési kísérletek elkerülése érdekében az Azure AD DS zárolja a fiókokat egy meghatározott küszöbérték után. Ez a fiókzárolás véletlenül is előfordulhat bejelentkezési támadási incidens nélkül. Ha például egy felhasználó ismételten rossz jelszót ad meg, vagy egy szolgáltatás egy régi jelszót próbál használni, a fiók zárolva lesz.

Ez a hibaelhárítási cikk ismerteti, hogy miért történik a fiókzárolások, és hogyan konfigurálhatja a viselkedést, és hogyan tekintheti át a biztonsági naplózásokat a zárolási események elhárításához.

## <a name="what-is-an-account-lockout"></a>Mi az a fiókzárolás?

Az Azure AD DS-ben egy felhasználói fiók zárolva van, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesült. Ez a fiókzárolási viselkedés úgy van kialakítva, hogy megvédje Önt az ismétlődő találgatásos bejelentkezési kísérletektől, amelyek automatikus digitális támadásra utalhatnak.

**Alapértelmezés szerint, ha 2 percen belül 5 rossz jelszókísérlet van, a fiók 30 percre zárolva van.**

Az alapértelmezett fiókzárolási küszöbértékek részletes jelszóházirenddel vannak konfigurálva. Ha meghatározott követelményekkel rendelkezik, felülbírálhatja ezeket az alapértelmezett fiókzárolási küszöbértékeket. Azonban nem ajánlott, hogy növelje a küszöbértéket, hogy megpróbálja csökkenteni a szám fiók zárolások. Először hárítsa el a fiókzárolási viselkedés forrását.

### <a name="fine-grained-password-policy"></a>Részletes jelszóházirend

A részletes jelszóházirendek (FGPP-k) lehetővé teszik, hogy a tartomány különböző felhasználóira speciális korlátozásokat alkalmazzon a jelszó- és fiókzárolási házirendekre vonatkozóan. Az FGPP csak az Azure AD DS-ben létrehozott felhasználókat érinti. Az Azure AD DS által felügyelt tartományba szinkronizált felhőfelhasználókat és tartományi felhasználókat az Azure AD-ből nem érintik a jelszóházirendek.

A szabályzatok az Azure AD DS felügyelt tartományban lévő csoporttársításon keresztül kerülnek elosztásra, és a módosítások a következő felhasználó bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már zárolt felhasználói fiókot.

A részletes jelszóházirendekkel kapcsolatos további információkért olvassa el [a Jelszó- és fiókzárolási házirendek konfigurálása című témakört.][configure-fgpp]

## <a name="common-account-lockout-reasons"></a>Gyakori fiókzárolási okok

A fiók rosszindulatú szándék és tényezők nélküli zárlatának leggyakoribb okai a következő forgatókönyvek:

* **A felhasználó kizárta magát.**
    * A jelszó legutóbbi módosítása után a felhasználó továbbra is használt egy korábbi jelszót? Az 5 sikertelen kísérlet alapértelmezett zárolási házirendje 2 perc alatt az okozhatja, hogy a felhasználó véletlenül újra próbálkozik egy régi jelszóval.
* **Van egy alkalmazás vagy szolgáltatás, amely egy régi jelszót.**
    * Ha egy fiókot alkalmazások vagy szolgáltatások használnak, ezek az erőforrások ismételten megpróbálhatnak bejelentkezni egy régi jelszóval. Ez a viselkedés okozza a fiók zárolt.
    * Próbálja meg minimalizálni a fiókhasználatot több különböző alkalmazásban vagy szolgáltatásban, és rögzítse, hogy hol használja a hitelesítő adatokat. Ha a fiók jelszavát módosítja, ennek megfelelően frissítse a kapcsolódó alkalmazásokat vagy szolgáltatásokat.
* **A jelszó megváltozott egy másik környezetben, és az új jelszó még nem szinkronizálva.**
    * Ha egy fiók jelszavát az Azure AD DS-en kívül módosítják, például egy helyszíni AD DS-környezetben, eltarthat néhány percig, amíg a jelszómódosítása szinkronizálódik az Azure AD-n keresztül és az Azure AD DS-ben.
    * Egy felhasználó, amely megpróbál bejelentkezni egy erőforrásba az Azure AD DS-en keresztül, mielőtt a jelszó-szinkronizálási folyamat befejeződött, a fiók zárolva van.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Fiókzárolások biztonsági naplózással – problémamegoldás

Ha hibaelhárítást szeretne, ha fiókzárolási események történnek, és honnan származnak, [engedélyezze az Azure AD DS biztonsági naplózását.][security-audit-events] A naplózási események rögzítése csak a funkció engedélyezésétől kezdve lesz rögzítve. Ideális esetben engedélyeznie kell a biztonsági naplózást, *mielőtt* fiókzárolási probléma merülne fel a hibaelhárításhoz. Ha egy felhasználói fiók többször is zárolási problémákkal küzd, engedélyezheti a biztonsági naplózást a következő helyzet bekövetkezésekor.

Miután engedélyezte a biztonsági naplózást, a következő mintalekérdezések bemutatják, hogyan tekintheti át *a fiókzárolási eseményeket*, *4740-es*kód.

Az elmúlt hét nap összes fiókzárolási eseményének megtekintése:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

A *driley*nevű fiók összes fiókzárolási eseményének megtekintése az elmúlt hét napban.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

2019. június 26. 2019. július 1-je éjfélkor, a dátum és az idő szerint rendezve:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>További lépések

A fiókzárolási küszöbértékek módosításához szükséges részletes jelszóházirendekkel kapcsolatos további információkért olvassa el [a Jelszó- és fiókzárolási házirendek konfigurálása című témakört.][configure-fgpp]

Ha továbbra is problémái vannak a virtuális gépnek az Azure AD DS felügyelt tartományhoz való csatlakozásával, [kérjen segítséget, és nyisson meg egy támogatási jegyet az Azure Active Directoryhoz.][azure-ad-support]

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
