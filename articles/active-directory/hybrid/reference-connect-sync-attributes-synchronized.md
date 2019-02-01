---
title: Az Azure AD Connect által szinkronizált attribútumok |} A Microsoft Docs
description: Azure Active Directoryval szinkronizált attribútumok listája.
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
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 38ae2ebccdb5164a582ebe78f7c85175747d085f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493354"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Az Azure AD Connect szinkronizálása: Az Azure Active Directoryval szinkronizált attribútumok
Ez a témakör az Azure AD Connect sync szinkronizált attribútumok listája.  
Az attribútumok vannak csoportosítva, a kapcsolódó Azure AD-alkalmazás.

## <a name="attributes-to-synchronize"></a>Attribútumok szinkronizálása
Egy gyakori kérdés *szinkronizálni a minimális attribútumok listáját mi*. Az alapértelmezett és ajánlott módszer az, hogy az alapértelmezett attribútumok, így a teljes globális Címlista (globális címlista) lehet létrehozni a felhőben, valamint hogy minden funkció az Office 365 számítási feladattal. Bizonyos esetekben egyes attribútum, amely a szervezet nem szeretné a felhőbe történő szinkronizálása mivel ezek az attribútumok tartalmaznak bizalmas vagy személyazonosításra alkalmas adatok (személyes azonosításra alkalmas adatokat) adatok, például ebben a példában:  
![hibás attribútumok](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Ebben az esetben indítsa el az ebben a témakörben az attribútumlistát, és azonosíthatja azokat az attribútumokat, amelyek bizalmas vagy PII adatokat tartalmaz, és nem lehet szinkronizálni. Törölje a jelölést során telepítési használatával ezek az attribútumok [az Azure AD alkalmazás- és attribútumszűrés](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Amikor attribútumok jelölésének, körültekintően járjon el, és csak törölje a ezek az attribútumok szinkronizálása nem feltétlenül lehetséges. Unselecting más jellemzőket funkciók negatív hatással lehet.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Attribútum neve | Felhasználó | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X |Meghatározza, hogy a fiók engedélyezve van. |
| CN |X | |
| displayName |X | |
| objectSID |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| pwdLastSet |X |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
|samAccountName|X| |
| sourceAnchor |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| usageLocation |X |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |

## <a name="exchange-online"></a>Exchange Online
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| Segéd |X |X | | |
| altRecipient |X | | |Az Azure AD Connect: 1.1.552.0 build igényel vagy után. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Vállalati |X |X | | |
| Országhívószám |X |X | | |
| Szervezeti egység |X |X | | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| információ |X |X |X |Ez az attribútum jelenleg nem használja fel a csoportok. |
| Monogram |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| kezelő |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Az Azure AD Connect 1.1.524.0 verzió érhető el |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ez az attribútum jelenleg nem használja fel az Exchange online-hoz. |
| msExchExtensionCustomAttribute2 |X |X |X |Ez az attribútum jelenleg nem használja fel az Exchange online-hoz. |
| msExchExtensionCustomAttribute3 |X |X |X |Ez az attribútum jelenleg nem használja fel az Exchange online-hoz. |
| msExchExtensionCustomAttribute4 |X |X |X |Ez az attribútum jelenleg nem használja fel az Exchange online-hoz. |
| msExchExtensionCustomAttribute5 |X |X |X |Ez az attribútum jelenleg nem használja fel az Exchange online-hoz. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
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
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Stránkování |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Irányítószám |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszó-szinkronizálás és az összevonási használják. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |GroupType származik |
| sorozatszám |X |X | | |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Vállalati |X |X | | |
| Országhívószám |X |X | | |
| Szervezeti egység |X |X | | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| információ |X |X |X | |
| Monogramja |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| levelezés |X |X |X | |
| mailnickname |X |X |X | |
| managedBy | | |X | |
| kezelő |X |X | | |
| tag | | |X | |
| middleName |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Stránkování |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Irányítószám |X |X | | |
| postOfficeBox |X |X | |Ez az attribútum jelenleg nem használja fel a SharePoint online-hoz. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |GroupType származik |
| sorozatszám |X |X | | |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online-ba (ezt követően Skype for Business néven)
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Vállalati |X |X | | |
| Szervezeti egység |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| levelezés |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| kezelő |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Irányítószám |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
| securityEnabled | | |X |GroupType származik |
| sorozatszám |X |X | | |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| cím |X |X | | |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| CN |X | |X |Köznapi név vagy aliasnév. Leggyakrabban a előtag [mail] érték. |
| displayName |X |X |X |Egy karakterlánc, amely gyakran a valódi neve (Utónév utolsó neve) jelenik meg a nevét jelöli. |
| levelezés |X |X |X |teljes e-mail-cím. |
| tag | | |X | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| proxyAddresses |X |X |X |gépi tulajdonság. Az Azure AD által használt. A felhasználó az összes másodlagos e-mail-címét tartalmazza. |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. |
| securityEnabled | | |X |Származtatott groupType. |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |Az UPN-je a felhasználó bejelentkezési azonosítója. Leggyakrabban a ugyanaz, mint a [mail] érték. |

## <a name="intune"></a>Intune
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| c |X |X | | |
| CN |X | |X | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| levelezés |X |X |X | |
| mailnickname |X |X |X | |
| tag | | |X | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
| securityEnabled | | |X |GroupType származik |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Vállalati |X |X | | |
| Országhívószám |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| kezelő |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| objectSID |X | |X |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| physicalDeliveryOfficeName |X |X | | |
| Irányítószám |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
| securityEnabled | | |X |GroupType származik |
| sorozatszám |X |X | | |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| cím |X |X | | |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |

## <a name="3rd-party-applications"></a>3. fél alkalmazások
Ez a csoport olyan készlete, az általános számítási feladat vagy alkalmazás számára szükséges minimális attribútumok szintként használt attribútumoknál. A munkaterhelés nem szerepel egy másik szakaszt vagy egy nem Microsoft-alkalmazáshoz is használható. Explicit módon használható a következő:

* A Yammer (csak a felhasználó használja fel)
* [Hibrid vállalatközi (B2B) szervezetek közötti együttműködés forgatókönyvek erőforrásokhoz, mint a SharePoint által kínált](https://go.microsoft.com/fwlink/?LinkId=747036)

Ez a csoport olyan készlete, az attribútumokat, amelyek is használható, ha az Azure AD-címtár nem támogatja az Office 365, Dynamics, vagy az Intune-ban. Rendelkezik egy kis készletét alapvető attribútum.

| Attribútum neve | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy a fiók engedélyezve van. |
| CN |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| levelezés |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| tag | | |X | |
| objectSID |X | | |gépi tulajdonság. AD-felhasználói azonosító az Azure közötti szinkronizálás fenntartásához használt AD és az AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |gépi tulajdonság. Ismerje meg, mikor már kiadott jogkivonatokat érvénytelenítéséhez használja. Jelszókivonat szinkronizálása, átmenő hitelesítésének és összevonási is használják. |
| sorozatszám |X |X | | |
| sourceAnchor |X |X |X |gépi tulajdonság. Nem módosítható azonosító karbantartása ADDS és az Azure AD közötti kapcsolat. |
| usageLocation |X | | |gépi tulajdonság. A felhasználó országától. A licenc-hozzárendelés használja. |
| userPrincipalName |X | | |A bejelentkezési Azonosítót a felhasználó UPN-je. Leggyakrabban a ugyanaz, mint a [mail] érték. |

## <a name="windows-10"></a>Windows 10
A Windows 10-tartományhoz computer(device) néhány az Azure AD-attribútumot szinkronizálja. További információ az alkalmazási: [csatlakoztatása az Azure AD-tartományhoz csatlakozott eszközökkel, a Windows 10-es élmény](../active-directory-azureadjoin-devices-group-policy.md). Ezek az attribútumok mindig szinkronizálása, és a Windows 10-es nem jelenik meg-alkalmazásként is törli. Windows 10 rendszerű tartományhoz csatlakoztatott számítógép kellene a feltöltve attribútum userCertificate által azonosított.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Szoftveresen kötött érték a tartományhoz csatlakoztatott számítógépeket. |
| displayName |X | |
| ms-DS-CreatorSID |X |RegisteredOwnerReference néven is ismert. |
| objectGUID |X |DeviceID néven is ismert. |
| objectSID |X |OnPremisesSecurityIdentifier néven is ismert. |
| operatingSystem |X |DeviceOSType néven is ismert. |
| operatingSystemVersion |X |DeviceOSVersion néven is ismert. |
| userCertificate |X | |

Ezeknek az attribútumoknak **felhasználói** mellett kiválasztott a többi alkalmazást is.  

| Attribútum neve | Felhasználó | Megjegyzés |
| --- |:---:| --- |
| domainFQDN |X |Tartománynév is nevezik. Ha például a contoso.com. |
| domainNetBios |X |NetBiosName néven is ismert. Például CONTOSO. |
| msDS-KeyCredentialLink |X |Miután megtörtént a felhasználó regisztrálása a Windows Hello for Business. | 

## <a name="exchange-hybrid-writeback"></a>Az Exchange hibrid visszaírás
Ezek az attribútumok a rendszer visszaírja Azure AD-ből a helyszíni Active Directoryban való engedélyezéséhez kiválasztásakor **Exchange hibrid**. Az Exchange verziójától függően kevesebb attribútumok szinkronizálása.

| Attribútum neve (Csatlakozás a felhasználói felület) |Attribútum neve (amely helyszíni AD) | Felhasználó | Kapcsolattartó | Csoport | Megjegyzés |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Származtatott cloudAnchor az Azure ad-ben. Ez az attribútum az Exchange 2016 és Windows Server 2016 AD jelent meg. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Online archívum: Archív tárolási szint mail használatát teszi lehetővé. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Szűrés: Visszaírja a helyszíni szűrése és online védelmét, és a blokkolt küldő adatokat ügyfelekről. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Szűrés: Visszaírja a helyszíni szűrése és online védelmét, és a blokkolt küldő adatokat ügyfelekről. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Szűrés: Visszaírja a helyszíni szűrése és online védelmét, és a blokkolt küldő adatokat ügyfelekről. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Engedélyezze az egyesített üzenetküldési (UM) – Online Hangposta: A Microsoft Lync Server által használt integrációs annak jelzéséhez, Lync Server helyszíni, hogy a felhasználó rendelkezik-e az online szolgáltatások hangposta. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Pereskedés céllal zároltak közé: Lehetővé teszi, hogy a cloud services annak meghatározására, hogy mely felhasználók alatt Pereskedés tárolásához. |
| proxyAddresses| proxyAddresses |X |X |X |Csak a cím az Exchange online-hoz kerül beillesztésre oda x500. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Lehetővé teszi az Exchange Online-postaládával, a felhasználók számára a helyszíni Exchange-postaládájába SendOnBehalfTo engedélyeket. Az Azure AD Connect: 1.1.552.0 build igényel vagy után. |

## <a name="exchange-mail-public-folder"></a>Exchange-levelezés nyilvános mappa
Ezek az attribútumok az Azure AD-választja ahhoz, hogy a helyszíni Active Directoryból szinkronizált **Exchange-levelezés nyilvános mappa**.

| Attribútum neve | PublicFolder | Megjegyzés |
| --- | :---:| --- |
| displayName | X |  |
| levelezés | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Eszközvisszaíró
Eszközobjektumok jönnek létre az Active Directoryban. Ezek az objektumok az Azure ad-hez csatlakoztatott eszközök lehetnek vagy tartományhoz csatlakoztatott Windows 10 rendszerű számítógépeken.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| DN |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Csak a Windows Server 2016 AD séma |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Megjegyzések
* A másik azonosító használata esetén a helyszíni attribútum userPrincipalName szinkronizálva van az Azure AD-attribútum onPremisesUserPrincipalName. A másik azonosító attribútum, például e-mail, az Azure AD-attribútum userPrincipalName szinkronizálva van.
* Az az objektumtípus a fenti listák **felhasználói** is vonatkozik az objektumtípus **iNetOrgPerson**.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
