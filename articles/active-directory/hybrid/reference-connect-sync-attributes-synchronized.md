---
title: Az Azure AD Connect által szinkronizált attribútumok | Microsoft dokumentumok
description: Az Azure Active Directoryval szinkronizált attribútumok listája.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab51030ad39e1360cabc7d63390af7c1654d2891
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082118"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect szinkronizálási szolgáltatás: Az Azure Active Directoryval szinkronizált attribútumok
Ez a témakör felsorolja az Azure AD Connect szinkronizálása által szinkronizált attribútumokat.  
Az attribútumok vannak csoportosítva a kapcsolódó Azure AD-alkalmazás.

## <a name="attributes-to-synchronize"></a>Szinkronizálandó attribútumok
Gyakori kérdés, hogy *mi a szinkronizálandó minimális attribútumok listája.* Az alapértelmezett és ajánlott módszer az alapértelmezett attribútumok megtartása, hogy teljes GAL (globális címlista) épülhessen fel a felhőben, és az Office 365-beli számítási feladatok összes szolgáltatása leváltható legyen. Bizonyos esetekben vannak olyan attribútumok, amelyeket a szervezet nem szeretne szinkronizálni a felhővel, mivel ezek az attribútumok bizalmas vagy személyazonosításra alkalmas adatokat tartalmaznak, mint például ebben a példában:  
![rossz attribútumok](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Ebben az esetben kezdje a témakör attribútumainak listájával, és azonosítsa azokat az attribútumokat, amelyek bizalmas vagy személyazonosításra alkalmas adatokat tartalmaznak, és nem szinkronizálhatók. Ezután törölje az attribútumok jelölését az [Azure AD alkalmazás és az attribútumszűrés](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)használatával történő telepítés során.

> [!WARNING]
> Az attribútumok kijelölésének megszüntetésekor óvatosnak kell lennie, és csak azokat a tulajdonságokat törölje, amelyeket nem lehet szinkronizálni. Más attribútumok kijelölésének megszüntetése negatív hatással lehet a funkciókra.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Attribútum neve | Felhasználó | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| Cn |X | |
| displayName |X | |
| objectSID |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| pwdLastSet |X |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
|samAccountName|X| |
| sourceAnchor |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation (használathelye) |X |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |

## <a name="exchange-online"></a>Exchange Online
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| Asszisztens |X |X | | |
| altRecipient |X | | |Az Azure AD Connect 1.1.552.0-s vagy azt követő buildet igényel. |
| authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| cég |X |X | | |
| országKód |X |X | | |
| Részleg |X |X | | |
| leírás | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| faxszám |X |X | | |
| givenName |X |X | | |
| otthoni telefon |X |X | | |
| Info |X |X |X |Ez az attribútum jelenleg nem használható fel csoportokhoz. |
| Monogram |X |X | | |
| l |X |X | | |
| örököltExchangeDN |X |X |X | |
| mailBece |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-FonetikusDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Elérhető az Azure AD Connect 1.1.524.0-s verziójában |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxJelzők |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ezt az attribútumot jelenleg nem használja az Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ezt az attribútumot jelenleg nem használja az Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ezt az attribútumot jelenleg nem használja az Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ezt az attribútumot jelenleg nem használja az Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ezt az attribútumot jelenleg nem használja az Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldTulajdonos |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationJelzők |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintFordítások |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsSzervezeti | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| oOFReplyToOriginator | | |X | |
| egyébFacsimileTelefon |X |X | | |
| egyébHomePhone |X |X | | |
| egyébTelefon |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postai kód |X |X | | |
| proxyAddresses |X |X |X | |
| nyilvános küldöttek |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszószinkronizálás és az összevonás egyaránt használja. |
| reportToOriginator (jelentés: kezdeményező) | | |X | |
| reportToowner (Toowner) | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| utcacím |X |X | | |
| Targetaddress |X |X | | |
| telefonAsszisztens |X |X | | |
| telefonszáma |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| unauthOrig között |X |X |X | |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| cég |X |X | | |
| országKód |X |X | | |
| Részleg |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| faxszám |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| otthoni telefon |X |X | | |
| Info |X |X |X | |
| Kezdőbetűi |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| Levelezés |X |X |X | |
| mailbecenév |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| middleName (középső név) |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| oOFReplyToOriginator | | |X | |
| egyébFacsimileTelefon |X |X | | |
| egyébHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| egyébMobil |X |X | | |
| otherPager |X |X | | |
| egyébTelefon |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postai kód |X |X | | |
| postOfficeBox |X |X | |Ezt az attribútumot jelenleg nem használja fel a SharePoint Online. |
| preferredLanguage (elsődleges nyelv) |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
| reportToOriginator (jelentés: kezdeményező) | | |X | |
| reportToowner (Toowner) | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| utcacím |X |X | | |
| Targetaddress |X |X | | |
| telefonAsszisztens |X |X | | |
| telefonszáma |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| unauthOrig között |X |X |X | |
| url |X |X | | |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója
. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Teams és Skype Vállalati online verzió
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| cég |X |X | | |
| Részleg |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| faxszám |X |X |X | |
| givenName |X |X | | |
| otthoni telefon |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| Levelezés |X |X |X | |
| mailBece |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress cím |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| egyébTelefon |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postai kód |X |X | | |
| preferredLanguage (elsődleges nyelv) |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| utcacím |X |X | | |
| telefonszáma |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| Cn |X | |X |Köznapi név vagy alias. Leggyakrabban a [mail] érték előtagja. |
| displayName |X |X |X |A nevet gyakran rövid névként (vezetéknév) jelképező karakterlánc. |
| Levelezés |X |X |X |teljes e-mail címet. |
| tag | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X |mechanikai tulajdonság. Az Azure AD használja. A felhasználó összes másodlagos e-mail címét tartalmazza. |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Ez az upn a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |

## <a name="intune"></a>Intune
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| c |X |X | | |
| Cn |X | |X | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| Levelezés |X |X |X | |
| mailbecenév |X |X |X | |
| tag | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| cég |X |X | | |
| országKód |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| faxszám |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| physicalDeliveryOfficeName |X |X | | |
| postai kód |X |X | | |
| preferredLanguage (elsődleges nyelv) |X | | | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| utcacím |X |X | | |
| telefonszáma |X |X | | |
| cím |X |X | | |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |

## <a name="3rd-party-applications"></a>Harmadik féltől származó jelentkezések
Ez a csoport egy általános számítási feladathoz vagy alkalmazáshoz szükséges minimális attribútumként használt attribútumok készlete. Használható egy másik szakaszban nem felsorolt számítási feladatokhoz vagy egy nem Microsoft-alkalmazáshoz. Kifejezetten a következőkre használják:

* Yammer (csak a felhasználó taszatba kerül)
* [Hibrid vállalkozások közötti (B2B) több szervezeti együttműködési forgatókönyvek által kínált erőforrások, mint a SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Ez a csoport olyan attribútumok csoportja, amelyek akkor használhatók, ha az Azure AD könyvtárat nem használják az Office 365, a Dynamics vagy az Intune támogatására. Ez egy kis sor alapvető attribútumok. Vegye figyelembe, hogy az egyszeri bejelentkezés vagy egyes külső alkalmazások ba való kiépítés az itt leírt attribútumokon kívül az attribútumok szinkronizálásának konfigurálását is igényli. Az alkalmazáskövetelményeit az egyes [alkalmazások SaaS-alkalmazásoktatóanyagismerteti.](../saas-apps/tutorial-list.md)

| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Azt határozza meg, hogy engedélyezve van-e egy fiók. |
| Cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| Levelezés |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| tag | | |X | |
| objectSID |X | | |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Használt, hogy tudja, mikor érvényteleníti a már kiadott jogkivonatokat. A jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás is használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Nem módosítható azonosító az ADDS és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation (használathelye) |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. Leggyakrabban ugyanaz, mint a [mail] érték. |

## <a name="windows-10"></a>Windows 10
A Windows 10-hez tartományhoz csatlakozott számítógép(eszköz) szinkronizálja az Azure AD egyes attribútumait. A forgatókönyvekről további információt a [Tartományhoz kapcsolódó eszközök csatlakoztatása az Azure AD for Windows 10-hez című témakörben talál.](../active-directory-azureadjoin-devices-group-policy.md) Ezek az attribútumok mindig szinkronizálódnak, és a Windows 10 nem jelenik meg olyan alkalmazásként, amelyet kilehet választani. A Windows 10 tartományhoz csatlakozó számítógépet a userCertificate attribútum feltöltésével azonosítják.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Kódolt érték a tartományhoz csatlakozó számítógépekhez. |
| displayName |X | |
| ms-DS-CreatorSID |X |Más néven registeredOwnerReference. |
| objectGUID azonosító |X |Más néven deviceID. |
| objectSID |X |Más néven onPremisesSecurityIdentifier. |
| operációs rendszer |X |Más néven deviceOSType. |
| operatingSystemVersion |X |Más néven deviceOSVersion. |
| userCertificate |X | |

Ezek az attribútumok a **felhasználó** számára a többi kiválasztott alkalmazáson kívül találhatók.  

| Attribútum neve | Felhasználó | Megjegyzés |
| --- |:---:| --- |
| tartományFQDN |X |Más néven dnsDomainName. Például adja meg a contoso.com nevet. |
| tartományNetBios |X |Más néven netBiosName. Például CONTOSO. |
| msDS-KeyCredentialLink |X |Miután a felhasználó regisztrált a Windows Hello for Business szolgáltatásban. | 

## <a name="exchange-hybrid-writeback"></a>Az Exchange hibrid visszaírása
Ezek az attribútumok az Azure AD-ből visszakerülnek a helyszíni Active Directoryba, amikor az **Exchange hibrid**engedélyezését választja. Az Exchange-verziótól függően kevesebb attribútum szinkronizálható.

| Attribútum neve (helyszíni AD) | Attribútum neve (Connect UI) | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Az Azure AD felhőalapú anchorjából származtatva. Ez az attribútum az Exchange 2016 és a Windows Server 2016 AD újdonsága. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Online archívum: Lehetővé teszi az ügyfelek számára, hogy archiválják a leveleket. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Szűrés: Visszaírja a helyszíni szűrést és az online biztonságos és letiltott küldő adatokat az ügyfelektől. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Szűrés: Visszaírja a helyszíni szűrést és az online biztonságos és letiltott küldő adatokat az ügyfelektől. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Szűrés: Visszaírja a helyszíni szűrést és az online biztonságos és letiltott küldő adatokat az ügyfelektől. |
| msExchUCVoiceMailBeállítások| ms-Exch-UCVoiceMailBeállítások |X | | |Egyesített üzenetküldés engedélyezése – Online hangposta: A Microsoft Lync Server integrációja segítségével jelzi a Lync Server helyszíni kiszolgálójának, hogy a felhasználó hangpostával rendelkezik az online szolgáltatásokban. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Peres eljárás miatti tartás: Lehetővé teszi, hogy a felhőszolgáltatások meghatározzák, hogy mely felhasználók tartoznak a peres eljárás miatti tartás alá. |
| proxyAddresses| proxyAddresses |X |X |X |Csak az Exchange Online x500-as címe kerül beszúrva. |
| nyilvános küldöttek| ms-Exch-Public-küldöttek  |X | | |Lehetővé teszi, hogy egy Exchange Online-postaláda SendOnBehalfTo jogokat kapjon a helyszíni Exchange-postaládával rendelkező felhasználók számára. Az Azure AD Connect 1.1.552.0-s vagy azt követő buildet igényel. |

## <a name="exchange-mail-public-folder"></a>Exchange Mail nyilvános mappa
Ezek az attribútumok szinkronizálódnak a helyszíni Active Directoryból az Azure AD-be, amikor az **Exchange Mail nyilvános mappájának**engedélyezését választja.

| Attribútum neve | Nyilvános mappa | Megjegyzés |
| --- | :---:| --- |
| displayName | X |  |
| Levelezés | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID azonosító | X |  |
| proxyAddresses | X |  |
| Targetaddress | X |  |

## <a name="device-writeback"></a>Eszközvisszaíró
Az eszközobjektumok az Active Directoryban jönnek létre. Ezek az objektumok lehetnek az Azure AD-hez vagy a tartományhoz csatlakozó Windows 10-es számítógépekhez csatlakozott eszközök.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| megkülönböztető név |X | |
| msDS-CloudAnchor |X | |
| msDS-eszközazonosító |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDazonosítók |X | |
| msDS-KeyCredentialLink |X |Csak Windows Server 2016 AD sémával |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-Felügyelt |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Megjegyzések
* Alternatív azonosító használata esetén a helyszíni attribútum userPrincipalName szinkronizálva van az Azure AD attribútumonPremisesUserPrincipalName. Az Alternatív azonosító attribútum, például a mail, szinkronizálva van az Azure AD attribútum userPrincipalName.
* A fenti listákban a **Felhasználó** objektumtípus az **iNetOrgPerson**objektumtípusra is vonatkozik.

## <a name="next-steps"></a>További lépések
További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
