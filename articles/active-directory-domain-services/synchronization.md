---
title: A szinkronizálás működése a Azure AD Domain Servicesban | Microsoft Docs
description: Ismerje meg, hogyan működik a szinkronizálási folyamat az Azure AD-bérlő vagy a helyszíni Active Directory tartományi szolgáltatások-környezet objektumainak és hitelesítő adatainak Azure Active Directory Domain Services felügyelt tartományba való bekapcsolásával.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 7e0e904b182a57a51b5d76f0acebc13bce5902b2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378524"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Az objektumok és a hitelesítő adatok szinkronizálása egy Azure AD Domain Services felügyelt tartományban

Azure Active Directory Domain Services (AD DS) felügyelt tartományban lévő objektumok és hitelesítő adatok a tartományon belül helyileg hozhatók létre, vagy egy Azure Active Directory (Azure AD) bérlőről is szinkronizálhatók. Amikor először telepíti az Azure AD DS-t, az automatikus egyirányú szinkronizálás be van állítva, és elindult az objektumok replikálása az Azure AD-ből. Ez az egyirányú szinkronizálás továbbra is fut a háttérben, hogy az Azure AD DS felügyelt tartomány naprakész állapotú legyen az Azure AD-val végzett bármilyen változással. Nem történik szinkronizálás az Azure AD DS vissza az Azure AD-be.

Hibrid környezetben a helyszíni AD DS tartományból származó objektumok és hitelesítő adatok szinkronizálhatók az Azure AD-vel Azure AD Connect használatával. Miután az objektumok sikeresen szinkronizálva lettek az Azure AD-be, az automatikus háttér-szinkronizálás ezután az Azure AD DS felügyelt tartományt használó alkalmazások számára elérhetővé teszi az objektumokat és a hitelesítő adatokat.

Az alábbi ábra azt szemlélteti, hogyan működik a szinkronizálás az Azure AD DS, az Azure AD és egy opcionális helyszíni AD DS-környezet között:

![Azure AD Domain Services felügyelt tartomány szinkronizálásának áttekintése](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Szinkronizálás az Azure AD-ből az Azure-ba AD DS

A felhasználói fiókokat, a csoporttagságok és a hitelesítő adatok kivonatait az Azure AD és az Azure AD DS között szinkronizálja a rendszer. Ez a szinkronizálási folyamat automatikus. Nem kell konfigurálnia, figyelnie vagy kezelnie ezt a szinkronizálási folyamatot. A kezdeti szinkronizálás néhány órát is igénybe vehet, az Azure AD-címtárban lévő objektumok számától függően. A kezdeti szinkronizálás befejezését követően az Azure AD-ben végrehajtott módosítások, például a jelszó vagy az attribútumok változásai automatikusan szinkronizálva lesznek az Azure AD DS.

A szinkronizálási folyamat egy módszer/egyirányú kialakítás. Az Azure AD DS változásainak visszavonása az Azure AD-be nem vonható vissza. Egy Azure AD DS felügyelt tartomány nagyrészt írásvédett, kivéve a létrehozható egyéni szervezeti egységeket. A felhasználói attribútumok, a felhasználói jelszavak vagy a csoporttagság nem módosítható egy Azure AD DS felügyelt tartományon belül.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Attribútumok szinkronizálása és leképezése az Azure AD DS

A következő táblázat néhány gyakori attribútumot sorol fel, valamint azt, hogy az Azure AD DS hogyan legyenek szinkronizálva.

| Attribútum az Azure AD DS | Forrás | Megjegyzések |
|:--- |:--- |:--- |
| EGYSZERŰ FELHASZNÁLÓNÉV | A felhasználó *UPN* -attribútuma az Azure ad-bérlőben | Az Azure AD-bérlő UPN-attribútuma szinkronizálva van az Azure AD DS. Az Azure AD DS felügyelt tartományba való bejelentkezés legmegbízhatóbb módja az egyszerű felhasználónév használata. |
| SAMAccountName | A felhasználó *mailNickname* attribútuma az Azure ad-bérlőben vagy az automatikusan generált | Az *sAMAccountName* attribútum forrása az Azure ad-bérlő *mailNickname* attribútuma. Ha több felhasználói fiók ugyanazzal a *mailNickname* attribútummal rendelkezik, a *sAMAccountName* automatikusan létrejön. Ha a felhasználó *mailNickname* vagy *UPN* -előtagja 20 karakternél hosszabb, a *sAMAccountName* automatikusan létrejön, hogy megfeleljen a *sAMAccountName* attribútumainak 20 karakteres korlátjának. |
| Jelszavak | Az Azure AD-bérlő felhasználói jelszava | Az NTLM-vagy Kerberos-hitelesítéshez szükséges örökölt jelszó-kivonatok az Azure AD-bérlőről lesznek szinkronizálva. Ha az Azure AD-bérlő a Azure AD Connect használatával történő hibrid szinkronizálásra van konfigurálva, ezek a jelszó-kivonatok a helyszíni AD DS-környezetből származnak. |
| Elsődleges felhasználó/csoport SID | Automatikusan létrehozott | A felhasználói/csoportfiókok elsődleges biztonsági azonosítója automatikusan létrejön az Azure AD DSban. Ez az attribútum nem egyezik meg az objektum elsődleges felhasználói vagy csoportjának SID-azonosítójával egy helyszíni AD DS környezetben. Ez az eltérés azért van így, mert az Azure AD DS felügyelt tartománya eltérő SID-névtérrel rendelkezik, mint a helyszíni AD DS tartomány. |
| A felhasználók és csoportok SID-előzményei | Helyszíni elsődleges felhasználó és csoport biztonsági azonosítója | Az Azure AD DS felhasználók és csoportok *SIDHistory* attribútuma úgy van beállítva, hogy megfeleljen a megfelelő elsődleges felhasználó vagy csoport biztonsági azonosítójának egy helyszíni AD DS környezetben. Ez a funkció segít a helyszíni alkalmazások Azure AD DSba való átállásának megkönnyítésében, mivel nem kell újrakonfigurálnia az erőforrásokat. |

> [!TIP]
> **Bejelentkezés a felügyelt tartományba UPN-formátum használatával** Előfordulhat, hogy a *sAMAccountName* attribútum, például a `AADDSCONTOSO\driley`automatikusan létrejön egy Azure AD DS felügyelt tartomány egyes felhasználói fiókjaihoz. A felhasználók automatikusan létrehozott *sAMAccountName* eltérőek lehetnek az UPN-előtagtól, ezért nem mindig megbízható a bejelentkezés.
>
> Ha például több felhasználó ugyanazzal a *mailNickname* attribútummal rendelkezik, vagy a felhasználók túl hosszú UPN-előtagokkal rendelkeznek, előfordulhat, hogy a felhasználók *sAMAccountName* automatikusan létrejön. Az egyszerű felhasználónév formátuma (például `driley@aaddscontoso.com`) használatával megbízhatóan bejelentkezhet egy Azure AD DS felügyelt tartományba.

### <a name="attribute-mapping-for-user-accounts"></a>Attribútum-hozzárendelés felhasználói fiókokhoz

Az alábbi táblázat azt szemlélteti, hogy az Azure AD-beli felhasználói objektumok egyes attribútumai hogyan legyenek szinkronizálva az Azure AD DS megfelelő attribútumaival.

| Felhasználói attribútum az Azure AD-ben | Felhasználói attribútum az Azure AD DSban |
|:--- |:--- |
| accountEnabled |userAccountControl (beállítja vagy törli a ACCOUNT_DISABLED bit) |
| city |l |
| ország |CO |
| Szervezeti egység |Szervezeti egység |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Beosztás |Cím |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (esetenként automatikusan létrehozott) |
| mobil |mobil |
| oid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (beállítja vagy törli a DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Irányítószám |Irányítószám |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Vezetéknév |sorozatszám |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attribútumok hozzárendelése csoportokhoz

Az alábbi táblázat azt szemlélteti, hogy az Azure AD-beli csoport objektumainak konkrét attribútumai hogyan legyenek szinkronizálva az Azure AD DS megfelelő attribútumaival.

| Group attribútum az Azure AD-ben | Group attribútum az Azure AD DSban |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (esetenként automatikusan létrehozott) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| oid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Helyszíni AD DS szinkronizálása az Azure AD-be és az Azure-ba AD DS

Azure AD Connect használatával szinkronizálhatók a felhasználói fiókok, csoporttagság és hitelesítő adatok kivonatai egy helyszíni AD DS környezetből az Azure AD-be. A felhasználói fiókok, például az UPN és a helyszíni biztonsági azonosító (SID) attribútumai szinkronizálva vannak. Az Azure AD DS használatával való bejelentkezéshez az NTLM-és Kerberos-hitelesítéshez szükséges örökölt jelszó-kivonatok is szinkronizálva lesznek az Azure AD-vel.

> [!IMPORTANT]
> A Azure AD Connect csak a helyszíni AD DS környezetekkel való szinkronizálásra kell telepíteni és konfigurálni. Nem támogatott Azure AD Connect telepítése Azure AD DS felügyelt tartományba az objektumok Azure AD-be való visszaszinkronizálásához.

Ha a visszaírást konfigurálja, az Azure AD változásai a helyszíni AD DS környezetbe lesznek szinkronizálva. Ha például egy felhasználó az Azure AD önkiszolgáló jelszavas felügyelet használatával módosítja a jelszavát, a rendszer visszafrissíti a jelszót a helyszíni AD DS környezetben.

> [!NOTE]
> Mindig a Azure AD Connect legújabb verzióját használja annak biztosítására, hogy az összes ismert hibával rendelkezik.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Szinkronizálás több erdőből álló helyszíni környezetből

Számos szervezet rendelkezik egy viszonylag összetett helyszíni AD DS környezettel, amely több erdőt is tartalmaz. Azure AD Connect támogatja a felhasználók, csoportok és hitelesítőadat-kivonatok többerdős környezetből az Azure AD-be történő szinkronizálását.

Az Azure AD sokkal egyszerűbb és lapos névteret tartalmaz. Ahhoz, hogy a felhasználók megbízhatóan hozzáférhessenek az Azure AD által védett alkalmazásokhoz, hárítsa el az UPN-ütközéseket a különböző erdőkben lévő felhasználói fiókok között. Az Azure AD DS felügyelt tartományok az Azure AD-hez hasonló, lapos szervezeti struktúrát használnak. Az összes felhasználói fiókot és csoportot a rendszer a *AADDC-felhasználók* tárolóban tárolja, annak ellenére, hogy a különböző helyszíni tartományokból vagy erdőkből szinkronizálva van, még akkor is, ha a helyszíni hierarchikus szervezeti egységet konfigurálta. Az Azure AD DS felügyelt tartomány bármely hierarchikus OU-struktúrát lelapul.

Ahogy korábban már említettük, az Azure-ból nem AD DS vissza az Azure AD-be. [Létrehozhat egyéni szervezeti egységet (OU-t)](create-ou.md) az Azure AD DS, majd a felhasználók, csoportok vagy szolgáltatásfiókok számára az egyéni szervezeti egységeken belül. Az egyéni szervezeti egységekben létrehozott objektumok egyike sincs szinkronizálva az Azure AD-vel. Ezek az objektumok csak az Azure AD DS felügyelt tartományon belül érhetők el, és nem láthatók az Azure AD PowerShell-parancsmagokkal, Microsoft Graph API-val vagy az Azure AD felügyeleti felhasználói felületének használatával.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Mi nincs szinkronizálva az Azure-AD DS

A következő objektumok vagy attribútumok nem szinkronizálhatók a helyszíni AD DS környezetből az Azure AD-be vagy az Azure AD DSba:

* **Kizárt attribútumok:** Dönthet úgy, hogy kizárja bizonyos attribútumok szinkronizálását az Azure AD-be egy helyszíni AD DS környezetből Azure AD Connect használatával. Ezek a kizárt attribútumok nem érhetők el az Azure AD DSban.
* **Csoportházirendek:** A helyszíni AD DS környezetben konfigurált csoportházirendek nem szinkronizálhatók az Azure AD DS.
* **SYSVOL mappa:** A helyi AD DS környezet *SYSVOL* mappájának tartalma nem szinkronizálódik az Azure-AD DS.
* **Számítógép-objektumok:** A helyszíni AD DS környezethez csatlakoztatott számítógépek számítógép-objektumai nem szinkronizálhatók az Azure AD DSval. Ezek a számítógépek nem rendelkeznek megbízhatósági kapcsolattal az Azure AD DS felügyelt tartományhoz, és csak a helyszíni AD DS-környezethez tartoznak. Az Azure AD DSban csak a felügyelt tartományhoz explicit módon tartományhoz csatlakoztatott számítógépek számítógép-objektumai jelennek meg.
* **Felhasználók és csoportok SIDHistory attribútumai:** A helyszíni AD DS környezet elsődleges felhasználóinak és elsődleges csoportjának biztonsági azonosítóit az Azure AD DS szinkronizálja. A felhasználók és csoportok meglévő *SIDHistory* -attribútumai azonban nem szinkronizálhatók a helyszíni AD DS környezetből az Azure ad DSba.
* **Szervezeti egységek (OU) struktúrái:** A helyszíni AD DS környezetekben definiált szervezeti egységek nem szinkronizálhatók az Azure AD DSval. Az AD DS Azure-ban két beépített szervezeti egység található: az egyik a felhasználók számára, a másik pedig a számítógépek számára. Az Azure AD DS felügyelt tartományhoz tartozik egy egyszerű szervezeti egység szerkezete. Létrehozhat [egy egyéni szervezeti egységet a felügyelt tartományában](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Jelszó-kivonatolási szinkronizálás és biztonsági megfontolások

Az Azure AD DS engedélyezésekor az NTLM + Kerberos hitelesítéshez örökölt jelszó-kivonatok szükségesek. Az Azure AD nem tárolja a tiszta szöveges jelszavakat, így ezek a kivonatok nem hozhatók létre automatikusan a meglévő felhasználói fiókokhoz. A generált és tárolt, NTLM és Kerberos-kompatibilis jelszavak kivonatait az Azure AD-ben mindig titkosított módon tárolja a rendszer.

A titkosítási kulcsok minden egyes Azure AD-bérlő esetében egyediek. Ezek a kivonatok titkosítva vannak, így csak az Azure AD DS fér hozzá a visszafejtési kulcsokhoz. Nincs más szolgáltatás vagy összetevő az Azure AD-ben a visszafejtési kulcsok eléréséhez.

A rendszer ezután szinkronizálja az örökölt jelszavakat az Azure AD-ből egy Azure AD DS felügyelt tartomány tartományvezérlőjére. Az Azure AD DS felügyelt tartományvezérlők lemezei inaktív állapotban vannak titkosítva. Ezeket a jelszó-kivonatokat a rendszer a jelszavak tárolási és biztonságos tárolásához és védelméhez hasonlóan tárolja és védi a helyszíni AD DS környezetben.

Kizárólag felhőalapú Azure AD-környezetek esetén a [felhasználóknak alaphelyzetbe kell állítaniuk vagy módosítaniuk a jelszavukat](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , hogy az Azure ad-ben a szükséges jelszó-kivonatok generálása és tárolása megtörténjen. Az Azure AD Domain Services engedélyezése után az Azure AD-ben létrehozott bármely felhőalapú felhasználói fiókhoz a rendszer a jelszó-kivonatokat az NTLM és a Kerberos-kompatibilis formátumban hozza létre és tárolja. Ezeknek az új fiókoknak nem kell alaphelyzetbe állítania vagy módosítaniuk a jelszavukat a régi jelszó-kivonatok létrehozásához.

A helyszíni AD DS környezetből Azure AD Connect használatával szinkronizált hibrid felhasználói fiókok esetében [konfigurálnia kell Azure ad Connect a jelszó-kivonatok szinkronizálását az NTLM és a Kerberos-kompatibilis formátumokban](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Következő lépések

A jelszó-szinkronizálási adatokkal kapcsolatos további információkért lásd: a [jelszó-kivonatolási szinkronizálás működése a Azure ad Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Az Azure AD DSának megkezdéséhez [hozzon létre egy felügyelt tartományt](tutorial-create-instance.md).
