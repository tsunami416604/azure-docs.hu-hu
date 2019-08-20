---
title: 'Azure Active Directory Domain Services: Szinkronizálás a felügyelt tartományokban | Microsoft Docs'
description: Azure Active Directory Domain Services felügyelt tartomány szinkronizálásának ismertetése
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617104"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Szinkronizálás Azure AD Domain Services felügyelt tartományban
Az alábbi ábra azt szemlélteti, hogyan működik a szinkronizálás Azure AD Domain Services felügyelt tartományokban.

![Szinkronizálás Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Szinkronizálás a helyszíni címtárból az Azure AD-bérlőbe
Azure AD Connect szinkronizálás a felhasználói fiókok, csoporttagság és a hitelesítő adatok kivonatának Azure AD-bérlőbe való szinkronizálására szolgál. A felhasználói fiókok, például az UPN és a helyszíni SID (biztonsági azonosító) attribútumai szinkronizálva vannak. Ha Azure AD Domain Services használ, az NTLM-és Kerberos-hitelesítéshez szükséges örökölt hitelesítőadat-kivonatok is szinkronizálva lesznek az Azure AD-Bérlővel.

Ha a visszaírást konfigurálja, az Azure AD-címtárban történt módosítások a helyszíni Active Directoryra lesznek szinkronizálva. Ha például az Azure AD önkiszolgáló jelszavas felügyelet használatával módosítja a jelszavát, a rendszer frissíti a módosított jelszót a helyszíni AD-tartományban.

> [!NOTE]
> Mindig a Azure AD Connect legújabb verzióját használja annak biztosítására, hogy az összes ismert hibával rendelkezik.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Szinkronizálás az Azure AD-bérlőről a felügyelt tartományba
A felhasználói fiókokat, a csoporttagságok és a hitelesítő adatok kivonatait az Azure AD-bérlőről a Azure AD Domain Services felügyelt tartományba szinkronizálja a rendszer. Ez a szinkronizálási folyamat automatikus. Ezt a szinkronizálási folyamatot nem kell konfigurálnia, figyelnie vagy kezelnie. A kezdeti szinkronizálás néhány órával több napig is eltarthat, az Azure AD-címtárban lévő objektumok számától függően. A kezdeti szinkronizálás befejeződése után körülbelül 20-30 percet vesz igénybe az Azure AD-ben végzett módosítások frissítése a felügyelt tartományban. Ez a szinkronizálási időköz a jelszó módosításait vagy az Azure AD-ben végrehajtott attribútumok módosításait érinti.

A szinkronizálási folyamat szintén egyirányú/egyirányú a természetben. A felügyelt tartomány nagyrészt írásvédett, kivéve a létrehozott egyéni szervezeti egységeket. Ezért a felügyelt tartományon belül nem végezheti el a felhasználói attribútumok, a felhasználói jelszavak vagy a csoporttagság módosítását. Ennek eredményeképpen a felügyelt tartomány változásai nem vonhatók vissza az Azure AD-bérlőre.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Szinkronizálás több erdőből álló helyszíni környezetből
Számos szervezet rendelkezik egy meglehetősen összetett helyszíni identitás-infrastruktúrával, amely több fiókból álló erdőből áll. Azure AD Connect támogatja a felhasználók, csoportok és hitelesítőadat-kivonatok többerdős környezetből az Azure AD-bérlőbe történő szinkronizálását.

Ezzel szemben az Azure AD-bérlő sokkal egyszerűbb és egyszerű névtér. Ahhoz, hogy a felhasználók megbízhatóan hozzáférhessenek az Azure AD által védett alkalmazásokhoz, hárítsa el az UPN-ütközéseket a különböző erdőkben lévő felhasználói fiókok között. A Azure AD Domain Services felügyelt tartománya az Azure AD-bérlőhöz való közvetlen hasonlóságot visel. A felügyelt tartományhoz egy egyszerű SZERVEZETIEGYSÉG-struktúra jelenik meg. A rendszer az összes felhasználói fiókot és csoportot a "AADDC Users" tárolóban tárolja, annak ellenére, hogy a különböző helyszíni tartományokból vagy erdőkből szinkronizálva van. Lehetséges, hogy a helyszíni hierarchikus szervezeti egységet konfigurálta. A felügyelt tartománynak még egy egyszerű, strukturált szervezeti struktúrája van.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Kizárások – a felügyelt tartományhoz nem szinkronizált
A következő objektumok vagy attribútumok nincsenek szinkronizálva az Azure AD-Bérlővel vagy a felügyelt tartományával:

* **Kizárt attribútumok:** Dönthet úgy, hogy bizonyos attribútumok kizárásával szinkronizálja az Azure AD-bérlőt a helyszíni tartományból Azure AD Connect használatával. Ezek a kizárt attribútumok nem érhetők el a felügyelt tartományban.
* **Csoportházirendek:** A helyszíni tartományban konfigurált csoportházirendek nincsenek szinkronizálva a felügyelt tartományba.
* **SYSVOL-megosztás:** Hasonlóképpen, a rendszer nem szinkronizálja a helyi tartomány SYSVOL-megosztásának tartalmát a felügyelt tartományával.
* **Számítógép-objektumok:** A helyszíni tartományhoz csatlakozó számítógépek számítógép-objektumai nem szinkronizálhatók a felügyelt tartományba. Ezek a számítógépek nem rendelkeznek megbízhatósági kapcsolattal a felügyelt tartományhoz, és csak a helyszíni tartományhoz tartoznak. A felügyelt tartományokban csak számítógép-objektumokat talál a felügyelt tartományhoz explicit módon tartományhoz csatlakoztatott számítógépekhez.
* **Felhasználók és csoportok SidHistory attribútumai:** A helyszíni tartomány elsődleges felhasználói és elsődleges biztonsági azonosítóit szinkronizálja a rendszer a felügyelt tartományba. A felhasználók és csoportok meglévő SidHistory-attribútumai azonban nem szinkronizálhatók a helyszíni tartományból a felügyelt tartományba.
* **Szervezeti egységek (OU) struktúrái:** A helyszíni tartományban definiált szervezeti egységek nem szinkronizálhatók a felügyelt tartományba. A felügyelt tartományban két beépített szervezeti egység található. Alapértelmezés szerint a felügyelt tartományhoz tartozik egy egyszerű szervezeti egység szerkezete. Dönthet azonban úgy, hogy [Egyéni szervezeti egységet hoz létre a felügyelt tartományában](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Adott attribútumok szinkronizálása a felügyelt tartományba
A következő táblázat néhány gyakori attribútumot sorol fel, és leírja, hogyan szinkronizálhatók a felügyelt tartománya.

| A felügyelt tartomány attribútuma | Source | Megjegyzések |
|:--- |:--- |:--- |
| EGYSZERŰ FELHASZNÁLÓNÉV |A felhasználó UPN-attribútuma az Azure AD-bérlőben |Az Azure AD-bérlő UPN-attribútuma szinkronizálva van a felügyelt tartományával. Ezért a felügyelt tartományba való bejelentkezés legmegbízhatóbb módja az egyszerű felhasználónév használata. |
| SAMAccountName |A felhasználó mailNickname attribútuma az Azure AD-bérlőben vagy automatikusan létrehozva |Az SAMAccountName attribútum forrása az Azure AD-bérlő mailNickname attribútuma. Ha több felhasználói fiók ugyanazzal a mailNickname attribútummal rendelkezik, a SAMAccountName automatikusan létrejön. Ha a felhasználó mailNickname vagy UPN-előtagja 20 karakternél hosszabb, a SAMAccountName automatikusan létrejön, hogy kielégítse a SAMAccountName attribútumainak 20 karakteres korlátját. |
| Jelszavak |Az Azure AD-bérlő felhasználói jelszava |Az NTLM-vagy Kerberos-hitelesítéshez szükséges hitelesítőadat-kivonatokat (más néven kiegészítő hitelesítő adatokat) szinkronizálja az Azure AD-bérlőből. Ha az Azure AD-bérlő egy szinkronizált bérlő, akkor ezek a hitelesítő adatok a helyszíni tartományból származnak. |
| Elsődleges felhasználó/csoport SID |Automatikusan generált |A felügyelt tartományban automatikusan létrejön a felhasználói/csoportfiókok elsődleges biztonsági azonosítója. Ez az attribútum nem egyezik meg a helyszíni AD-tartományban lévő objektum elsődleges felhasználói/csoportjának SID-azonosítójával. Ez az eltérés azért van így, mert a felügyelt tartomány eltérő SID-névtérrel rendelkezik, mint a helyszíni tartomány. |
| A felhasználók és csoportok SID-előzményei |Helyszíni elsődleges felhasználó és csoport biztonsági azonosítója |A felügyelt tartományba tartozó felhasználók és csoportok SidHistory attribútuma úgy van beállítva, hogy megfeleljen a helyi tartományban lévő megfelelő elsődleges felhasználó vagy csoport SID-nek. Ez a szolgáltatás megkönnyíti a helyszíni alkalmazások átemelését a felügyelt tartományba, mivel nem kell újraacl erőforrásokat létrehoznia. |

> [!NOTE]
> **Jelentkezzen be a felügyelt tartományba az UPN formátum használatával:** Előfordulhat, hogy a felügyelt tartomány egyes felhasználói fiókjaihoz a SAMAccountName attribútum automatikusan létrejön. Ha több felhasználó ugyanazzal a mailNickname attribútummal rendelkezik, vagy a felhasználók túl hosszú UPN-előtagokkal rendelkeznek, előfordulhat, hogy a felhasználók SAMAccountName automatikusan létrejön. Ezért a SAMAccountName formátuma (például "CONTOSO\dee") nem mindig megbízható módszer a tartományba való bejelentkezésre. A felhasználók automatikusan létrehozott SAMAccountName eltérőek lehetnek az UPN-előtagtól. Használja az UPN formátumot (például "dee@contoso.com"), hogy megbízhatóan jelentkezzen be a felügyelt tartományba.

### <a name="attribute-mapping-for-user-accounts"></a>Attribútum-hozzárendelés felhasználói fiókokhoz
Az alábbi táblázat azt szemlélteti, hogy az Azure AD-bérlőben lévő felhasználói objektumok adott attribútumai hogyan legyenek szinkronizálva a felügyelt tartomány megfelelő attribútumaival.

| Felhasználói attribútum az Azure AD-bérlőben | A felügyelt tartomány felhasználói attribútuma |
|:--- |:--- |
| accountEnabled |userAccountControl (beállítja vagy törli a ACCOUNT_DISABLED-bitet) |
| city |l |
| ország |CO |
| Szervezeti egység |Szervezeti egység |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Beosztás |cím |
| levelezés |levelezés |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (esetenként automatikusan generált) |
| mobil |mobil |
| oid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (beállítja vagy törli a DONT_EXPIRE_PASSWORD-bitet) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Irányítószám |Irányítószám |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Vezetéknév |sorozatszám |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attribútumok hozzárendelése csoportokhoz
Az alábbi táblázat azt szemlélteti, hogy az Azure AD-bérlőben lévő csoportok objektumainak konkrét attribútumai hogyan legyenek szinkronizálva a felügyelt tartomány megfelelő attribútumaival.

| A Group attribútum az Azure AD-bérlőben | A felügyelt tartomány Group attribútuma |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (esetenként automatikusan generált) |
| levelezés |levelezés |
| mailNickname |msDS-AzureADMailNickname |
| oid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Jelszó-kivonatolási szinkronizálás és biztonsági megfontolások
A Azure AD Domain Services engedélyezésekor az Azure AD-címtár jelszó-kivonatokat hoz létre és tárol az NTLM-& Kerberos-kompatibilis formátumokban. 

A meglévő felhőalapú felhasználói fiókok esetében, mivel az Azure AD soha nem tárolja a tiszta szöveges jelszavukat, ezek a kivonatok nem hozhatók létre automatikusan. Ezért a Microsoft megköveteli a felhasználóktól, hogy a jelszavas kivonatok létrehozásához és az Azure AD-ben való tárolásához a jelszavakat alaphelyzetbe [állítsa vagy módosítsa](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) . Az Azure AD Domain Services engedélyezése után az Azure AD-ben létrehozott bármely felhőalapú felhasználói fiókhoz a rendszer a jelszó-kivonatokat az NTLM és a Kerberos-kompatibilis formátumban hozza létre és tárolja. 

A helyszíni AD-ből szinkronizált felhasználói fiókok Azure AD Connect Sync használatával a [jelszó-kivonatok NTLM-és Kerberos-kompatibilis formátumokban](active-directory-ds-getting-started-password-sync-synced-tenant.md)való szinkronizálásához konfigurálnia kell a Azure ad Connect.

Az NTLM-és Kerberos-kompatibilis jelszó-kivonatok mindig titkosított módon tárolódnak az Azure AD-ben. Ezek a kivonatok titkosítva vannak, így csak Azure AD Domain Services férhet hozzá a visszafejtési kulcsokhoz. Nincs más szolgáltatás vagy összetevő az Azure AD-ben a visszafejtési kulcsok eléréséhez. A titkosítási kulcsok egyediek az Azure AD-bérlők esetében. Azure AD Domain Services szinkronizálja a jelszó-kivonatokat a felügyelt tartomány tartományvezérlőinekba. Ezeket a jelszavakat a rendszer a jelszavak tárolásának és védelmének a Windows Server AD-tartományvezérlőkön való védelméhez hasonlóan tárolja és védi. A felügyelt tartományvezérlők lemezei inaktív állapotban vannak titkosítva.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>A felügyelt tartományból az Azure AD-Bérlővel nem szinkronizált objektumok
A jelen cikk előző részében leírtaknak megfelelően a felügyelt tartományból nem lehet szinkronizálni az Azure AD-bérlőbe. Dönthet úgy, hogy [Egyéni szervezeti egységet (OU-t) hoz létre](create-ou.md) a felügyelt tartományában. Emellett létrehozhat más szervezeti egységeket, felhasználókat, csoportokat és szolgáltatásfiókot is ezeken az egyéni szervezeti egységeken belül. Az egyéni szervezeti egységeken belül létrehozott objektumok egyike sincs szinkronizálva az Azure AD-Bérlővel. Ezek az objektumok csak a felügyelt tartományon belül használhatók. Ezért ezek az objektumok nem láthatók az Azure ad PowerShell-parancsmagok, az Azure AD Graph API vagy az Azure AD felügyeleti felhasználói felület használatával.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Központi telepítési forgatókönyvek – Azure AD Domain Services](scenarios.md)
* [Hálózatkezelési megfontolások Azure AD Domain Services](network-considerations.md)
* [Ismerkedés a Azure AD Domain Services](tutorial-create-instance.md)
