---
title: 'Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás |} A Microsoft Docs'
description: Ismerteti az Azure AD Connect szinkronizálása működik, és hogyan szabhatja testre.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 247b7bb5b5dbce94c8f8339a6f06c8ae5dab75d9
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919366"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás
Az Azure Active Directory Connect szinkronizálási szolgáltatások (Azure AD Connect szinkronizálási) Azure AD Connect egyik fő összetevője. Ez gondoskodik a azonosító adatok a helyszíni környezet és az Azure AD között szinkronizálja kapcsolatos műveletek. Az Azure AD Connect szinkronizálása a DirSync, Azure AD Sync és az Azure Active Directory-összekötő konfigurálva a Forefront Identity Manager utódja.

Ez a témakör a szóló **Azure AD Connect szinkronizálási** (más néven **szinkronizálási motor**), és felsorolja az összes többi kapcsolódó témakörökre mutató hivatkozásokat. Az Azure AD Connect hivatkozások: [a helyszíni identitások integrálása az Azure Active Directory](active-directory-aadconnect.md).

A helyszíni két összetevőből áll: a szinkronizálási szolgáltatás **Azure AD Connect szinkronizálási** összetevő és az Azure AD-ben Szolgáltatásoldali nevű **az Azure AD Connect szinkronizálási szolgáltatás**.

## <a name="azure-ad-connect-sync-topics"></a>Az Azure AD Connect szinkronizálási kapcsolatos témakörök
| Témakör | Azt ismerteti, és mikor olvasása |
| --- | --- |
| **Az Azure AD Connect-szinkronizálás – alapok** | |
| [Az architektúra ismertetése](active-directory-aadconnectsync-understanding-architecture.md) |Azoknak, akik nem ismeri a szinkronizálási motoron, és ismerje meg az architektúra és a használt feltételeket szeretne. |
| [Műszaki fogalmak](active-directory-aadconnectsync-technical-concepts.md) |Egy rövid verzió, röviden és az architektúra a témakör a használt kifejezések magyarázatát tartalmazza. |
| [Azure AD Connect-topológiák](active-directory-aadconnect-topologies.md) |Ismerteti a különböző topológiák és forgatókönyvek a szinkronizálási motor támogatja. |
| **Egyéni konfiguráció** | |
| [A telepítővarázsló újra futtatni](active-directory-aadconnectsync-installation-wizard.md) |Azt ismerteti, milyen beállításokat kell érhető el, ha újra futtatja az Azure AD Connect telepítővarázsló. |
| [Deklaratív kiépítés ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |A deklaratív kiépítés nevű konfigurációs modellt ismerteti. |
| [Deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Kifejezés nyelve deklaratív kiépítés szintaxisának ismerteti. |
| [Az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md) |Az out-of-box szabályok és az alapértelmezett konfiguráció ismertetése. Azt is leírja, hogy a szabályok együttműködni a beépített forgatókönyvek esetén működik. |
| [Understanding felhasználók és a kapcsolattartók](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Az előző témakörben tárgyaló, és ismerteti a felhasználók és a kapcsolattartók konfigurációjának működése együtt, különösen egy Többerdős környezetben. |
| [Hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md) |Bemutatja, hogyan, hogy az egyik gyakran alkalmazott konfiguráció attribútumfolyamok módosítsa. |
| [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Támogatás korlátai és a módosítások elvégzése az out-of-box-konfigurációhoz. |
| [A szűrés konfigurálása](active-directory-aadconnectsync-configure-filtering.md) |A különböző lehetőségeket a mely objektumok éppen szinkronizált Azure ad-hez, és részletes ezek a beállítások konfigurálása, hogy hogyan korlátozza ismerteti. |
| **Funkciók és alkalmazási helyzetek** | |
| [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |Ismerteti a *véletlen törlések megakadályozása* szolgáltatásról és konfigurálja azt. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |A beépített ütemezési, amely importálása, szinkronizálását, és az adatok exportálása ismerteti. |
| [Jelszókivonat-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Ismerteti a jelszó-szinkronizálás használata, megvalósításához és működtetéséhez és hibaelhárításához. |
| [Eszközvisszaírás](active-directory-aadconnect-feature-device-writeback.md) |Az Azure AD Connectben eszközvisszaírás működését ismerteti. |
| [Címtárbővítmények](active-directory-aadconnectsync-feature-directory-extensions.md) |Ismerteti, hogyan lehet a saját egyéni attribútumokkal az Azure AD-séma kiterjesztése. |
| [Az Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Ismerteti, hogyan lehet a felhasználó Office 365 erőforrásokat helyezze a felhasználó ugyanabban a régióban. |
| **Sync Service** | |
| [Az Azure AD Connect szinkronizálási szolgáltatás funkciók](active-directory-aadconnectsyncservice-features.md) |A szinkronizálási szolgáltatás oldalon és a szinkronizálási beállítások módosítása az Azure ad-ben írja le. |
| [Ismétlődő attribútumok rugalmassága](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Ismerteti, hogyan engedélyezheti és használja **userPrincipalName** és **proxyAddresses** ismétlődő értékek tűrés. |
| **Műveletek és a felhasználói felület** | |
| [Szinkronizálási szolgáltatáskezelő](active-directory-aadconnectsync-service-manager-ui.md) |Ismerteti a Synchronization Service Manager felhasználói felületén, beleértve a [műveletek](active-directory-aadconnectsync-service-manager-ui-operations.md), [összekötők](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverzumtervező](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), és [keresés a Metaverzumban](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) lapokon. |
| [Üzemeltetési feladatok és szempontok](active-directory-aadconnectsync-operations.md) |Fontos információk, például a vész-helyreállítási ismerteti. |
| **kézikönyv...** | |
| [Az Azure AD-fiók alaphelyzetbe állítása](active-directory-aadconnectsync-howto-azureadaccount.md) |Tudnivalók az Azure AD segítségével kapcsolódhat az Azure AD Connect szinkronizálási szolgáltatásfiók hitelesítő adatainak alaphelyzetbe állítása. |
| **További információk és hivatkozások** | |
| [Portok](active-directory-aadconnect-ports.md) |Meg kell nyitnia a szinkronizálási motor és a helyszíni címtárak és az Azure AD között milyen portokat sorolja fel. |
| [Az Azure Active Directoryval szinkronizált attribútumok](active-directory-aadconnectsync-attributes-synchronized.md) |Felsorolja az összes attribútum szinkronizálása között a helyszíni AD és az Azure ad-ben. |
| [Függvényreferencia](active-directory-aadconnectsync-functions-reference.md) |Deklaratív kiépítés elérhető összes funkciók listája. |

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
