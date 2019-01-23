---
title: 'Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás |} A Microsoft Docs'
description: Ismerteti az Azure AD Connect szinkronizálása működik, és hogyan szabhatja testre.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
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
ms.openlocfilehash: 74973371b3d893c1e0c94024a97c77b59fa570fe
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461943"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás
Az Azure Active Directory Connect szinkronizálási szolgáltatások (Azure AD Connect szinkronizálási) Azure AD Connect egyik fő összetevője. Ez gondoskodik a azonosító adatok a helyszíni környezet és az Azure AD között szinkronizálja kapcsolatos műveletek. Az Azure AD Connect szinkronizálása a DirSync, Azure AD Sync és az Azure Active Directory-összekötő konfigurálva a Forefront Identity Manager utódja.

Ez a témakör a szóló **Azure AD Connect szinkronizálási** (más néven **szinkronizálási motor**), és felsorolja az összes többi kapcsolódó témakörökre mutató hivatkozásokat. Az Azure AD Connect hivatkozások: [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).

A helyszíni két összetevőből áll: a szinkronizálási szolgáltatás **Azure AD Connect szinkronizálási** összetevő és az Azure AD-ben Szolgáltatásoldali nevű **az Azure AD Connect szinkronizálási szolgáltatás**.

## <a name="azure-ad-connect-sync-topics"></a>Az Azure AD Connect szinkronizálási kapcsolatos témakörök
| Témakör | Azt ismerteti, és mikor olvasása |
| --- | --- |
| **Az Azure AD Connect-szinkronizálás – alapok** | |
| [Az architektúra ismertetése](concept-azure-ad-connect-sync-architecture.md) |Azoknak, akik nem ismeri a szinkronizálási motoron, és ismerje meg az architektúra és a használt feltételeket szeretne. |
| [Műszaki fogalmak](how-to-connect-sync-technical-concepts.md) |Egy rövid verzió, röviden és az architektúra a témakör a használt kifejezések magyarázatát tartalmazza. |
| [Azure AD Connect-topológiák](plan-connect-topologies.md) |Ismerteti a különböző topológiák és forgatókönyvek a szinkronizálási motor támogatja. |
| **Egyéni konfiguráció** | |
| [A telepítővarázsló újra futtatni](how-to-connect-installation-wizard.md) |Azt ismerteti, milyen beállításokat kell érhető el, ha újra futtatja az Azure AD Connect telepítővarázsló. |
| [Deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md) |A deklaratív kiépítés nevű konfigurációs modellt ismerteti. |
| [Deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Kifejezés nyelve deklaratív kiépítés szintaxisának ismerteti. |
| [Az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md) |Az out-of-box szabályok és az alapértelmezett konfiguráció ismertetése. Azt is leírja, hogy a szabályok együttműködni a beépített forgatókönyvek esetén működik. |
| [Understanding felhasználók és a kapcsolattartók](concept-azure-ad-connect-sync-user-and-contacts.md) |Az előző témakörben tárgyaló, és ismerteti a felhasználók és a kapcsolattartók konfigurációjának működése együtt, különösen egy Többerdős környezetben. |
| [Hogyan lehet módosítani az alapértelmezett konfiguráció](how-to-connect-sync-change-the-configuration.md) |Bemutatja, hogyan, hogy az egyik gyakran alkalmazott konfiguráció attribútumfolyamok módosítsa. |
| [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](how-to-connect-sync-best-practices-changing-default-configuration.md) |Támogatás korlátai és a módosítások elvégzése az out-of-box-konfigurációhoz. |
| [A szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) |A különböző lehetőségeket a mely objektumok éppen szinkronizált Azure ad-hez, és részletes ezek a beállítások konfigurálása, hogy hogyan korlátozza ismerteti. |
| **Funkciók és alkalmazási helyzetek** | |
| [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Ismerteti a *véletlen törlések megakadályozása* szolgáltatásról és konfigurálja azt. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |A beépített ütemezési, amely importálása, szinkronizálását, és az adatok exportálása ismerteti. |
| [Jelszókivonat-szinkronizálás megvalósítása](how-to-connect-password-hash-synchronization.md) |Ismerteti a jelszó-szinkronizálás használata, megvalósításához és működtetéséhez és hibaelhárításához. |
| [Eszközvisszaírás](how-to-connect-device-writeback.md) |Az Azure AD Connectben eszközvisszaírás működését ismerteti. |
| [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) |Ismerteti, hogyan lehet a saját egyéni attribútumokkal az Azure AD-séma kiterjesztése. |
| [Az Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Ismerteti, hogyan lehet a felhasználó Office 365 erőforrásokat helyezze a felhasználó ugyanabban a régióban. |
| **Sync Service** | |
| [Az Azure AD Connect szinkronizálási szolgáltatás funkciók](how-to-connect-syncservice-features.md) |A szinkronizálási szolgáltatás oldalon és a szinkronizálási beállítások módosítása az Azure ad-ben írja le. |
| [Ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Ismerteti, hogyan engedélyezheti és használja **userPrincipalName** és **proxyAddresses** ismétlődő értékek tűrés. |
| **Műveletek és a felhasználói felület** | |
| [Szinkronizálási szolgáltatáskezelő](how-to-connect-sync-service-manager-ui.md) |Ismerteti a Synchronization Service Manager felhasználói felületén, beleértve a [műveletek](how-to-connect-sync-service-manager-ui-operations.md), [összekötők](how-to-connect-sync-service-manager-ui-connectors.md), [Metaverzumtervező](how-to-connect-sync-service-manager-ui-mvdesigner.md), és [keresés a Metaverzumban](how-to-connect-sync-service-manager-ui-mvsearch.md) lapokon. |
| [Üzemeltetési feladatok és szempontok](how-to-connect-sync-operations.md) |Fontos információk, például a vész-helyreállítási ismerteti. |
| **kézikönyv...** | |
| [Az Azure AD-fiók alaphelyzetbe állítása](how-to-connect-azureadaccount.md) |Tudnivalók az Azure AD segítségével kapcsolódhat az Azure AD Connect szinkronizálási szolgáltatásfiók hitelesítő adatainak alaphelyzetbe állítása. |
| **További információk és hivatkozások** | |
| [Portok](reference-connect-ports.md) |Meg kell nyitnia a szinkronizálási motor és a helyszíni címtárak és az Azure AD között milyen portokat sorolja fel. |
| [Az Azure Active Directoryval szinkronizált attribútumok](reference-connect-sync-attributes-synchronized.md) |Felsorolja az összes attribútum szinkronizálása között a helyszíni AD és az Azure ad-ben. |
| [Függvényreferencia](reference-connect-sync-functions-reference.md) |Deklaratív kiépítés elérhető összes funkciók listája. |

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
