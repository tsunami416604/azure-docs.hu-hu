---
title: A szinkronizálás működése az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Ismerje meg, hogyan működik a szinkronizálási folyamat az Objektumok és hitelesítő adatok egy Azure AD-bérlői vagy helyszíni Active Directory tartományi szolgáltatások környezetben egy Azure Active Directory tartományi szolgáltatások felügyelt tartományban.
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
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639912"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Az objektumok és hitelesítő adatok szinkronizálása az Azure AD tartományi szolgáltatások által kezelt tartományban

Objektumok és hitelesítő adatok egy Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományban lehet létrehozni helyileg a tartományon belül, vagy szinkronizálható egy Azure Active Directory (Azure AD) bérlő. Az Azure AD DS első üzembe helyezésekor egy automatikus egyirányú szinkronizálás van konfigurálva, és elindul az objektumok replikálása az Azure AD-ből. Ez az egyirányú szinkronizálás továbbra is fut a háttérben, hogy az Azure AD DS felügyelt tartomány naprakész az Azure AD-ből származó módosításokkal. Nincs szinkronizálás történik az Azure AD DS-ből vissza az Azure AD-hez.

Hibrid környezetben a helyszíni AD DS-tartományból származó objektumok és hitelesítő adatok szinkronizálhatók az Azure AD-vel az Azure AD Connect használatával. Miután ezeket az objektumokat sikeresen szinkronizálta az Azure AD-vel, az automatikus háttérszinkronizálás ezután elérhetővé teszi ezeket az objektumokat és hitelesítő adatokat az Azure AD DS felügyelt tartományhasználatával használó alkalmazások számára.

Az alábbi ábra bemutatja, hogyan működik a szinkronizálás az Azure AD DS, az Azure AD és egy opcionális helyszíni AD DS-környezet között:

![Szinkronizálás – áttekintés egy Azure AD tartományi szolgáltatások által kezelt tartományhoz](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Szinkronizálás az Azure AD-ről az Azure AD DS-re

A felhasználói fiókok, a csoporttagságok és a hitelesítő adatok kihmesszei az Azure AD-től az Azure AD DS-ig egy módon szinkronizálódnak. Ez a szinkronizálási folyamat automatikus. Nem kell konfigurálnia, figyelnie vagy kezelnie ezt a szinkronizálási folyamatot. A kezdeti szinkronizálás eltarthat néhány órával, hogy néhány napig, az Azure AD könyvtárban lévő objektumok száma. A kezdeti szinkronizálás befejezése után az Azure AD-ben végrehajtott módosítások, például a jelszó vagy az attribútum módosításai, majd automatikusan szinkronizálja az Azure AD DS-t.

Amikor egy felhasználó jön létre az Azure AD-ben, azok nem szinkronizálva az Azure AD DS, amíg nem módosítják a jelszót az Azure AD-ben. Ez a jelszómódosítási folyamat a Kerberos- és NTLM-hitelesítés jelszókivonatait hozza létre és tárolja az Azure AD-ben. A jelszókimondott a felhasználó sikeres hitelesítéséhez az Azure AD DS sikeres hitelesítéséhez van szükség.

A szinkronizálási folyamat egy irányú / egyirányú a tervezés. Az Azure AD DS-ből az Azure AD AD-re visszairányuló módosítások szinkronizálása nem fordított. Az Azure AD DS felügyelt tartomány nagyrészt csak olvasható, kivéve az egyéni, létrehozható egyéni független kiszolgálókat. Az Azure AD DS felügyelt tartományában nem módosíthatja a felhasználói attribútumokat, a felhasználói jelszavakat vagy a csoporttagságokat.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Attribútumszinkronizálás és -hozzárendelés az Azure AD DS szolgáltatáshoz

Az alábbi táblázat néhány gyakori attribútumot és az Azure AD DS-sel való szinkronizálásuk módját sorolja fel.

| Attribútum az Azure AD DS-ben | Forrás | Megjegyzések |
|:--- |:--- |:--- |
| UPN | A felhasználó *UPN-attribútuma* az Azure AD-bérlőben | Az Azure AD-bérlő upn attribútumszinkronizálva van az Azure AD DS-hez. Az Azure AD DS felügyelt tartományába való bejelentkezés legmegbízhatóbb módja az upn használata. |
| SAMAccountName | A felhasználó *mailNickname* attribútuma az Azure AD-bérlőben vagy automatikusan létrehozva | A *SAMAccountName* attribútum származik a *mailNickname* attribútum az Azure AD-bérlő. Ha több felhasználói fiók rendelkezik ugyanazzal a *mailNickname* attribútummal, a *SAMAccountName* automatikusan létrejön. Ha a felhasználó *felhasználó felhasználóbeceneve* vagy *UPN* előtagja 20 karakternél hosszabb, a *SAMAccountName* automatikusan létrejön, hogy megfeleljen a *SAMAccountName* attribútumok 20 karakteres korlátjának. |
| Jelszavak | A felhasználó jelszava az Azure AD-bérlőből | Az NTLM- vagy Kerberos-hitelesítéshez szükséges örökölt jelszókivét az Azure AD-bérlőből szinkronizálódik. Ha az Azure AD-bérlő van konfigurálva a hibrid szinkronizálás az Azure AD Connect használatával, ezek a jelszó-kihágások származik a helyszíni AD DS-környezetben. |
| Elsődleges felhasználó/csoport biztonsági azonosítója | Automatikusan létrehozva | A felhasználói/csoportfiókok elsődleges SID automatikusan létrejön az Azure AD DS.The primary SID for user/group accounts is autogenerated in Azure AD DS. Ez az attribútum nem egyezik meg az objektum elsődleges felhasználói/csoport biztonsági azonosítójával egy helyszíni AD DS-környezetben. Ez az eltérés oka, hogy az Azure AD DS felügyelt tartomány a helyszíni AD DS-tartománytól eltérő SID-névtérrel rendelkezik. |
| SID-előzmények felhasználók és csoportok számára | Helyszíni elsődleges felhasználó és csoport biztonsági azonosítója | Az Azure AD DS-ben a felhasználók és csoportok *SidHistory* attribútuma úgy van beállítva, hogy megfeleljen a megfelelő elsődleges felhasználó- vagy csoportbiztonsági azonosítónak egy helyszíni AD DS-környezetben. Ez a funkció megkönnyíti a helyszíni alkalmazások azure AD DS-be való áthelyezését, mivel nem kell újra ACL-erőforrásokat újraengedélyeznie. |

> [!TIP]
> **Bejelentkezés a felügyelt tartományba az upn formátumban** A *SAMAccountName* attribútum, `AADDSCONTOSO\driley`például a , előfordulhat, hogy automatikusan létre jön néhány felhasználói fiókok egy Azure AD DS felügyelt tartományban. A felhasználók automatikusan generált *SAMAccountName-je* eltérhet az UPN-előtagtól, így nem mindig megbízható bejelentkezési mód.
>
> Ha például több felhasználó rendelkezik ugyanazzal a *mailNickname* attribútummal, vagy ha a felhasználók túlságosan hosszú UPN előtagokkal rendelkeznek, akkor a felhasználók *SAMAccountName azonosítója* automatikusan generálható. Az UPN formátum, `driley@aaddscontoso.com`például a használatával megbízhatóan jelentkezzen be egy Azure AD DS felügyelt tartományba.

### <a name="attribute-mapping-for-user-accounts"></a>Attribútumleképezés felhasználói fiókokhoz

Az alábbi táblázat bemutatja, hogy az Azure AD-ben a felhasználói objektumok adott attribútumai hogyan szinkronizálódnak az Azure AD DS megfelelő attribútumaival.

| Felhasználói attribútum az Azure AD-ben | Felhasználói attribútum az Azure AD DS-ben |
|:--- |:--- |
| accountEnabled |userAccountControl (beállítja vagy törli a ACCOUNT_DISABLED bitet) |
| city |l |
| ország |Co |
| Részleg |Részleg |
| displayName |displayName |
| faxphoneTelefonszám |faxphoneTelefonszám |
| givenName |givenName |
| jobTitle |cím |
| Levelezés |Levelezés |
| mailBece |msDS-AzureADMailBeé |
| mailBece |SAMAccountName (néha automatikusan létrehozva) |
| mobil |mobil |
| tárgyiobjektum |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory (előfordult) |
| passwordPolicies |userAccountControl (beállítja vagy törli a DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postai kód |postai kód |
| preferredLanguage (elsődleges nyelv) |preferredLanguage (elsődleges nyelv) |
| state |st |
| utcacím |utcacím |
| surname |sn |
| telefonszáma |telefonszáma |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attribútumleképezés csoportokhoz

Az alábbi táblázat bemutatja, hogy az Azure AD-ben a csoportobjektumok adott attribútumai hogyan szinkronizálódnak az Azure AD DS megfelelő attribútumaival.

| Csoportattribútum az Azure AD-ben | Csoportattribútum az Azure AD DS-ben |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (néha automatikusan létrehozva) |
| Levelezés |Levelezés |
| mailBece |msDS-AzureADMailBeé |
| tárgyiobjektum |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory (előfordult) |
| securityEnabled (biztonságengedélyezve) |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Szinkronizálás a helyszíni AD DS-ről az Azure AD-re és az Azure AD DS-re

Az Azure AD Connect segítségével szinkronizálhatja a felhasználói fiókokat, a csoporttagságokat és a hitelesítő adatok at egy helyszíni Activeangeles-beli DS-környezetből az Azure AD-be. A felhasználói fiókok attribútumai, például az upn és a helyszíni biztonsági azonosító (SID) szinkronizálása történik. Az Azure AD DS használatával történő bejelentkezéshez az NTLM- és Kerberos-hitelesítéshez szükséges örökölt jelszókivétis szinkronizálódik az Azure AD-vel.

> [!IMPORTANT]
> Az Azure AD Connect csak telepíteni és konfigurálni a helyszíni AD DS-környezetekkel való szinkronizáláshoz. Nem támogatott az Azure AD Connect telepítése egy Azure AD DS által felügyelt tartományban az objektumok Azure AD-vel való szinkronizálásához.

Ha konfigurálja a visszaírást, az Azure AD-ből származó módosítások szinkronizálódnak a helyszíni Active Angeles-i Active Angeles-i környezettel. Ha például egy felhasználó módosítja a jelszavát az Azure AD önkiszolgáló jelszókezelés használatával, a jelszó frissül újra a helyszíni AD DS-környezetben.

> [!NOTE]
> Mindig az Azure AD Connect legújabb verzióját használja annak érdekében, hogy minden ismert hiba javítással rendelkezzen.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Szinkronizálás többerdős helyszíni környezetből

Számos szervezet rendelkezik egy meglehetősen összetett helyszíni AD DS-környezettel, amely több erdőt tartalmaz. Az Azure AD Connect támogatja a felhasználók, csoportok és hitelesítő adatok többerdős környezetekből az Azure AD-ből történő szinkronizálását.

Az Azure AD sokkal egyszerűbb és egyensíkú névtérrel rendelkezik. Annak érdekében, hogy a felhasználók megbízhatóan hozzáférhessenek az Azure AD által védett alkalmazásokhoz, oldja fel az upn-ütközéseket a különböző erdők felhasználói fiókjai között. Az Azure AD DS által felügyelt tartományok az Azure AD-hez hasonló, egysima szervezeti egységstruktúrát használnak. Minden felhasználói fiók és csoport az *AADDC-felhasználók* tárolóban tárolódik, annak ellenére, hogy különböző helyszíni tartományokból vagy erdőkből vannak szinkronizálva, még akkor is, ha a helyszíni hierarchikus szervezeti egység struktúrát konfigurált. Az Azure AD DS felügyelt tartomány összeolvasztja a hierarchikus szervezeti egység struktúrák.

A korábban részletezett, nincs szinkronizálás az Azure AD DS-ből vissza az Azure AD-hez. Létrehozhat [egy egyéni szervezeti egységet (OU)](create-ou.md) az Azure AD DS-ben, majd felhasználókat, csoportokat vagy szolgáltatásfiókokat ezeken az egyéni szervezeti egységeken belül. Az egyéni számos o-kban létrehozott objektumok egyike sem lesz szinkronizálva az Azure AD-vel. Ezek az objektumok csak az Azure AD DS felügyelt tartományon belül érhetők el, és nem láthatók az Azure AD PowerShell-parancsmagok, a Microsoft Graph API vagy az Azure AD felügyeleti felhasználói felület használatával.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Mi nincs szinkronizálva az Azure AD DS-sel?

A következő objektumok vagy attribútumok nincsenek szinkronizálva a helyszíni Active AD DS-környezetből az Azure AD vagy az Azure AD DS szolgáltatással:

* **Kizárt attribútumok:** Dönthet úgy, hogy bizonyos attribútumokat kizár az Azure AD-vel való szinkronizálásból egy helyszíni AD DS-környezetből az Azure AD Connect használatával. Ezek a kizárt attribútumok nem érhetők el az Azure AD DS-ben.
* **Csoportházirendek:** A helyszíni Activeád-ds-környezetben konfigurált csoportházirendek nincsenek szinkronizálva az Azure AD DS szolgáltatással.
* **Sysvol mappa:** A *Sysvol* mappa tartalmát egy helyszíni ActiveÁd DS-környezetben nincs szinkronizálva az Azure AD DS.
* **Számítógépes objektumok:** A helyszíni Activeád-ds-környezethez csatlakozott számítógépek számítógép-objektumai nincsenek szinkronizálva az Azure AD DS szolgáltatással. Ezek a számítógépek nem rendelkeznek megbízhatósági kapcsolattal az Azure AD DS felügyelt tartománnyal, és csak a helyszíni AD DS-környezethez tartoznak. Az Azure AD DS-ben csak a felügyelt tartományhoz explicit tartományhoz csatlakozott számítógépek számítógép-objektumai jelennek meg.
* **SidHistory attribútumok felhasználók és csoportok számára:** A helyszíni Activeád-szolgáltatáskörnyezetelsődleges felhasználói és elsődleges csoport-biztonsági azonosítói szinkronizálva vannak az Azure AD DS szolgáltatással. Azonban a meglévő *SidHistory* attribútumok a felhasználók és csoportok nem szinkronizálva a helyszíni ActiveÁd-ds-környezetben az Azure AD DS.
* **Szervezeti egységek (OU) struktúrái:** A helyszíni Activeád-ds-környezetben definiált szervezeti egységek nem szinkronizálódnak az Azure AD DS szolgáltatással. Az Azure AD DS-ben két beépített ous található – az egyik a felhasználók, a másik a számítógépek számára. Az Azure AD DS felügyelt tartomány egy sima szervezeti egység struktúrával rendelkezik. Dönthet úgy, hogy [egyéni szervezeti egységet hoz létre a felügyelt tartományban.](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>A jelszókivonat-szinkronizálással és a biztonsági szempontokkal kapcsolatos szempontok

Ha engedélyezi az Azure AD DS-t, az NTLM + Kerberos-hitelesítés örökölt jelszókivonatai szükségesek. Az Azure AD nem tárolja a tiszta szöveges jelszavakat, így ezek a kiválaszok nem hozhatnak létre automatikusan a meglévő felhasználói fiókokhoz. A létrehozást és tárolást követően az NTLM- és Kerberos-kompatibilis jelszókivéteket mindig titkosított módon tárolják az Azure AD-ben.

A titkosítási kulcsok egyediek az egyes Azure AD-bérlők számára. Ezek a kibúniak vannak titkosítva, így csak az Azure AD DS rendelkezik a visszafejtési kulcsok elérésével. Az Azure AD-ben nincs más szolgáltatás vagy összetevő, amely hozzáfér a visszafejtési kulcsokhoz.

A régi jelszókivéteket ezután szinkronizálja az Azure AD-ből egy Azure AD DS felügyelt tartomány tartományvezérlőire. Az Azure AD DS-ben ezeka felügyelt tartományvezérlők lemezei in-edítve vannak titkosítva. Ezek a jelszókivétek ezeken a tartományvezérlőkön vannak tárolva és biztosítva, hasonlóan a jelszavak helyszíni AD DS-környezetben történő tárolásához és biztonságoslásához.

Csak felhőalapú Azure AD-környezetekben a [felhasználóknak alaphelyzetbe kell állítaniuk/módosítaniuk kell a jelszavukat](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) a szükséges jelszókiállítások létrehozásához és tárolásához az Azure AD-ben. Az Azure AD-ben az Azure AD-ben az Azure AD tartományi szolgáltatások engedélyezése után létrehozott felhőalapú felhasználói fiókok esetében a jelszókivétaz NTLM és Kerberos-kompatibilis formátumokban jön létre és tárolódik. Minden felhőalapú felhasználói fióknak módosítania kell a jelszavát, mielőtt szinkronizálni adna az Azure AD DS-hez.

A helyszíni Active AD DS-környezetből az Azure AD Connect használatával szinkronizált hibrid felhasználói fiókok esetében be kell [állítania az Azure AD Connect szolgáltatást az NTLM és a Kerberos-kompatibilis formátumok jelszókivéinek szinkronizálásához.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>További lépések

A jelszó-szinkronizálás sajátosságairól a [Jelszókivonat-szinkronizálás működése az Azure AD Connecttel című témakörben talál](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)további információt.

Az Azure AD DS első lépéseihez [hozzon létre egy felügyelt tartományt.](tutorial-create-instance.md)
